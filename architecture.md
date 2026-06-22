# Architecture du lab

## Schéma réseau

> Remplace ce bloc par un vrai schéma (draw.io / Excalidraw) exporté dans `assets/`.

```
                    Réseau host-only (ex : 192.168.56.0/24)
                              isolé d'Internet
   ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
   │     KALI-01      │   │      WIN-01      │   │     SIEM-01      │
   │   Attaquant      │──▶│  Victime + AD    │──▶│  Wazuh Manager   │
   │  192.168.56.10   │   │  192.168.56.20   │   │  192.168.56.30   │
   └──────────────────┘   └──────────────────┘   └──────────────────┘
        Kali Linux          Sysmon + agent          collecte & alertes
                             Wazuh installés
```

## Détail des machines

### SIEM-01 — Le cerveau
- **OS** : Ubuntu Server 22.04
- **Rôle** : Wazuh Manager + Indexer + Dashboard
- **Ressources** : 4 Go RAM min (8 Go recommandé), 2 vCPU
- **Notes** : reçoit les logs des agents, applique les règles, affiche les alertes

### WIN-01 — La victime
- **OS** : Windows 10 ou Windows Server 2019/2022
- **Rôle** : poste/serveur cible, contrôleur de domaine Active Directory (optionnel)
- **Logiciels** : agent Wazuh, **Sysmon** (config SwiftOnSecurity ou Olaf Hartong)
- **Notes** : génère les Event Logs et les télémétries Sysmon

### KALI-01 — L'attaquant
- **OS** : Kali Linux
- **Rôle** : lance les attaques (manuelles ou via Atomic Red Team déporté)
- **Outils** : nmap, hydra, Metasploit, Mimikatz, etc.

## Choix techniques (à justifier en entretien)

- **Réseau host-only** : isole le lab d'Internet → on peut manipuler du malware/des outils offensifs sans risque.
- **Sysmon** : la télémétrie Windows native est pauvre ; Sysmon enrichit énormément (création de process, connexions réseau, hash, etc.).
- **Wazuh** : gratuit, complet, proche d'un vrai SIEM d'entreprise, large communauté.

## Prérequis matériels

| Config | RAM hôte | Faisable ? |
|--------|----------|-----------|
| Minimale | 8 Go | Oui, en lançant les VM une par une |
| Confortable | 16 Go | Oui, les 3 VM en simultané |
| Idéale | 32 Go | Lab étendu (plusieurs victimes, AD complet) |
