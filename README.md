# 🛡️ SOC Home Lab — Détection & Réponse aux menaces

Laboratoire de détection complet montrant le cycle de vie d'une menace : **attaque → génération de logs → détection → réponse**. Chaque détection est mappée sur le framework **MITRE ATT&CK**.

> Projet personnel d'apprentissage de l'analyse SOC (Security Operations Center). L'objectif : maîtriser de bout en bout la chaîne SIEM, de la collecte de logs à la réponse à incident.

---

## 🎯 Objectifs du projet

- Monter une infrastructure de détection réaliste (SIEM, endpoint logging, AD)
- Simuler des attaques contrôlées mappées sur MITRE ATT&CK
- Développer des règles de détection (format Sigma) et les tuner
- Documenter des playbooks de réponse à incident
- (Bonus) Automatiser des actions de réponse façon SOAR

## 🧱 Architecture

Voir [`docs/architecture.md`](docs/architecture.md) pour le détail et le schéma.

| VM | Rôle | OS |
|----|------|----|
| `SIEM-01` | Collecte & analyse des logs | Wazuh / Ubuntu |
| `WIN-01` | Machine victime + Active Directory | Windows Server / 10 |
| `KALI-01` | Machine attaquante | Kali Linux |

Réseau isolé (host-only) — aucune exposition Internet.

## 🛠️ Stack technique

- **SIEM** : Wazuh (open-source)
- **Endpoint logging** : Sysmon (config SwiftOnSecurity) + Windows Event Logs
- **Émulation d'adversaire** : Atomic Red Team
- **Format de détection** : Sigma
- **Framework de référence** : MITRE ATT&CK
- *(optionnel)* **Gestion d'incidents** : TheHive — **SOAR** : Shuffle

## 📂 Structure du dépôt

```
soc-home-lab/
├── README.md                  ← tu es ici
├── docs/
│   └── architecture.md        ← schéma réseau + détail des VM
├── attacks/                   ← journal des attaques simulées
├── detections/                ← une fiche par détection (le cœur du projet)
│   ├── _TEMPLATE.md
│   └── T1110-bruteforce-rdp.md  ← exemple complet
├── playbooks/                 ← procédures de réponse à incident
│   ├── _TEMPLATE.md
│   └── bruteforce-rdp.md      ← exemple complet
└── assets/                    ← captures d'écran, schémas
```

## 📊 Couverture des détections

| ID ATT&CK | Technique | Tactique | Statut |
|-----------|-----------|----------|--------|
| T1110 | Brute Force (RDP) | Credential Access | ✅ Détecté |
| _à compléter_ | | | 🔲 |

> Mets ce tableau à jour à chaque nouvelle détection — c'est la première chose qu'un recruteur regarde.

## 🚀 Phases du projet

1. ✅ Infrastructure (VM + réseau isolé)
2. ✅ Centralisation des logs (Sysmon → SIEM)
3. ✅ Déploiement du SIEM (Wazuh)
4. 🔲 Émulation d'attaques (Atomic Red Team)
5. 🔲 Développement des règles de détection
6. 🔲 Réponse & automatisation

## 📝 Compétences démontrées

`SIEM` · `Wazuh` · `MITRE ATT&CK` · `Sysmon` · `Analyse de logs` · `Threat Detection` · `Sigma` · `Incident Response` · `Active Directory`
