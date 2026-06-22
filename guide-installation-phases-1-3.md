# Guide d'installation — Phases 1 à 3

Montage du lab depuis zéro : machines virtuelles + réseau isolé, puis SIEM Wazuh, puis Sysmon sur la victime. Compte ~1 à 2 soirées.

> Commandes vérifiées pour **Wazuh 4.14**. En cas de doute, la doc officielle fait foi : https://documentation.wazuh.com/current/quickstart.html

---

## Phase 1 — Infrastructure (VM + réseau isolé)

### 1.1 Installer l'hyperviseur

Installe **VirtualBox** (gratuit, multiplateforme) : https://www.virtualbox.org/
*(Alternative : VMware Workstation, désormais gratuit pour un usage personnel.)*

### 1.2 Télécharger les ISO

| VM | ISO à récupérer |
|----|-----------------|
| `SIEM-01` | Ubuntu Server 22.04 LTS |
| `WIN-01` | Windows 10/11 (ISO d'évaluation Microsoft) ou Windows Server 2022 |
| `KALI-01` | Kali Linux (image VirtualBox pré-faite, la plus simple) |

> Astuce : Microsoft fournit des VM Windows d'évaluation gratuites (90 jours) prêtes à l'emploi — cherche « Windows evaluation VM ». Idéal pour un lab.

### 1.3 Créer le réseau isolé

C'est l'étape de sécurité **critique** : le lab ne doit pas toucher Internet.

Dans VirtualBox → **File > Tools > Network Manager > Host-only Networks** → crée un réseau (ex : `192.168.56.0/24`, serveur DHCP désactivé pour fixer les IP toi-même).

Pour chaque VM → **Settings > Network > Adapter 1 > Host-only Adapter**, et sélectionne ce réseau.

> Pendant l'installation des OS uniquement, tu peux temporairement passer un adaptateur en NAT pour les mises à jour, puis le remettre en host-only. Ne lance jamais d'outil offensif avec Internet actif.

### 1.4 Plan d'adressage (à reporter dans `docs/architecture.md`)

| VM | IP fixe |
|----|---------|
| `SIEM-01` | 192.168.56.30 |
| `WIN-01` | 192.168.56.20 |
| `KALI-01` | 192.168.56.10 |

### 1.5 Ressources conseillées par VM

| VM | RAM | vCPU | Disque |
|----|-----|------|--------|
| SIEM-01 | 4 Go (8 idéal) | 2 | 50 Go |
| WIN-01 | 4 Go | 2 | 50 Go |
| KALI-01 | 2 Go | 2 | 30 Go |

✅ **Checkpoint phase 1** : les 3 VM démarrent et se pinguent entre elles. Fais un *snapshot* VirtualBox de chaque VM propre — tu pourras revenir en arrière après une attaque.

---

## Phase 2 & 3 — SIEM Wazuh sur SIEM-01

On installe les 3 composants Wazuh (serveur + indexeur + dashboard) sur la même VM via l'assistant officiel.

### 3.1 Mettre à jour la VM Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
```

### 3.2 Lancer l'assistant d'installation Wazuh

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

L'option `-a` (all-in-one) installe tout sur cette machine. L'installation prend ~10 min.

### 3.3 Récupérer le mot de passe admin

À la fin, l'assistant affiche l'utilisateur `admin` et son mot de passe. Pour le réafficher plus tard :

```bash
sudo tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```

### 3.4 Accéder au dashboard

Depuis ton PC hôte, ouvre :

```
https://192.168.56.30
```

Accepte l'avertissement de certificat (normal, c'est auto-signé), puis connecte-toi avec `admin` / le mot de passe récupéré.

### 3.5 (Recommandé) Figer la version

Pour éviter qu'une mise à jour casse le lab :

```bash
sudo sed -i "s/^deb /#deb /" /etc/apt/sources.list.d/wazuh.list
sudo apt update
```

✅ **Checkpoint Wazuh** : tu vois le dashboard Wazuh dans ton navigateur.

---

## Phase 2 (suite) — Agent Wazuh + Sysmon sur WIN-01

### A. Déployer l'agent Wazuh

Dans le dashboard Wazuh → **Agents management > Summary > Deploy new agent** → choisis Windows, renseigne l'IP du serveur (`192.168.56.30`), et copie la commande PowerShell générée. Exécute-la **en administrateur** sur `WIN-01`.

L'agent doit apparaître « Active » dans le dashboard sous une minute.

### B. Installer Sysmon (la pièce maîtresse de la télémétrie)

1. Télécharge **Sysmon** (Microsoft Sysinternals).
2. Récupère une config de référence — celle de **SwiftOnSecurity** (`sysmonconfig-export.xml`) est le standard pour débuter.
3. Dans une invite **administrateur** :

```powershell
.\Sysmon64.exe -accepteula -i sysmonconfig-export.xml
```

### C. Dire à Wazuh d'ingérer les logs Sysmon

Édite la config de l'agent sur `WIN-01` : `C:\Program Files (x86)\ossec-agent\ossec.conf`, et ajoute dans `<ossec_config>` :

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Puis redémarre l'agent :

```powershell
Restart-Service -Name WazuhSvc
```

✅ **Checkpoint final** : dans le dashboard Wazuh → **Security events**, tu vois remonter des événements Sysmon (`Event ID 1` = création de process) quand tu lances des programmes sur `WIN-01`.

---

## 🎉 Et après ?

Ton lab est opérationnel. Tu peux maintenant attaquer la **Phase 4** : reproduis l'exemple `detections/T1110-bruteforce-rdp.md` de bout en bout. Pour ça :

1. Active le RDP sur `WIN-01`.
2. Depuis `KALI-01`, lance le brute force (`hydra`).
3. Vérifie que les Event ID 4625 remontent dans Wazuh.
4. Écris ta règle de détection et remplis la fiche.

Ta première détection maison = le moment où tout prend sens. Bon courage !
