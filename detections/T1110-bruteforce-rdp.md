# [T1110] — Brute Force (RDP)

> Exemple complet servant de modèle. Une détection bien documentée = une histoire que tu peux dérouler en entretien.

## 📌 Résumé

| Champ | Valeur |
|-------|--------|
| **Technique MITRE ATT&CK** | [T1110](https://attack.mitre.org/techniques/T1110/) |
| **Tactique** | Credential Access |
| **Plateforme** | Windows |
| **Source de log** | Windows Security Event Log |
| **Sévérité** | Moyenne |
| **Statut** | ✅ Détecté & validé |

## 1. L'attaque

Depuis `KALI-01`, tentative de connexion RDP en force sur `WIN-01` avec une liste de mots de passe.

```bash
# Depuis KALI-01 — hydra contre le service RDP de la victime
hydra -l administrateur -P /usr/share/wordlists/rockyou.txt \
      rdp://192.168.56.20
```

Comportement attendu : une rafale d'échecs d'authentification en quelques secondes.

## 2. Ce qu'on observe dans les logs

Sur `WIN-01`, chaque échec génère un **Event ID 4625** (An account failed to log on) dans le journal *Security*. Le champ `LogonType` vaut `10` (RemoteInteractive = RDP) et `IpAddress` pointe vers l'attaquant.

```
EventID:      4625
Account Name: administrateur
LogonType:    10
IpAddress:    192.168.56.10
Status:       0xC000006D  (mauvais identifiants)
```

Signal clé : un grand nombre de 4625 (même compte, même IP source) sur une courte fenêtre de temps.

## 3. La règle de détection

```yaml
title: Brute Force RDP - multiples échecs d'authentification
status: experimental
description: Détecte un nombre élevé d'échecs de connexion RDP (EventID 4625, LogonType 10) depuis une même source sur une courte période.
logsource:
    product: windows
    service: security
detection:
    selection:
        EventID: 4625
        LogonType: 10
    timeframe: 5m
    condition: selection | count() by IpAddress > 10
fields:
    - IpAddress
    - TargetUserName
level: medium
tags:
    - attack.credential_access
    - attack.t1110
```

> Dans Wazuh, ça se traduit par une règle custom qui compte les occurrences (`frequency` / `timeframe`) et lève une alerte au-delà du seuil.

## 4. Validation

- [x] L'alerte se déclenche dès ~10 échecs en 5 min
- [x] Aucun faux positif lors de connexions légitimes normales
- [x] Capture d'écran de l'alerte : `assets/t1110-alerte.png`

## 5. Notes / pistes d'amélioration

- Le seuil (10 / 5 min) est arbitraire — à ajuster selon le bruit de fond.
- Un attaquant lent (low & slow) passerait sous le radar : envisager une fenêtre plus longue en complément.
- Enrichissement possible : corréler avec un **4624** (succès) suivant la rafale → signe d'un brute force *réussi*, alerte à élever en sévérité Haute.
