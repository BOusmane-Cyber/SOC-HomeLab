# [T####] — Nom de la technique

> Copie ce fichier pour chaque nouvelle détection. Nomme-le `T####-nom-court.md`.

## 📌 Résumé

| Champ | Valeur |
|-------|--------|
| **Technique MITRE ATT&CK** | [T####](https://attack.mitre.org/techniques/T####/) |
| **Tactique** | _ex : Credential Access_ |
| **Plateforme** | _ex : Windows_ |
| **Source de log** | _ex : Windows Security Event Log / Sysmon_ |
| **Sévérité** | _Basse / Moyenne / Haute_ |
| **Statut** | 🔲 En cours / ✅ Détecté & validé |

## 1. L'attaque

Décris ce que tu as fait, depuis quelle machine, avec quel outil.

```bash
# Commande(s) utilisée(s)
```

## 2. Ce qu'on observe dans les logs

Quel(s) Event ID / champ(s) apparaissent ? Colle un extrait de log (anonymisé).

```
# Extrait de log pertinent
```

## 3. La règle de détection

Logique de détection, idéalement au format **Sigma**.

```yaml
title: ...
status: experimental
description: ...
logsource:
    product: windows
    service: security
detection:
    selection:
        EventID: ...
    condition: selection
level: medium
tags:
    - attack.t####
```

## 4. Validation

- [ ] L'alerte se déclenche bien quand je rejoue l'attaque
- [ ] Pas (ou peu) de faux positifs en fonctionnement normal
- [ ] Capture d'écran de l'alerte ajoutée dans `assets/`

## 5. Notes / pistes d'amélioration

_Seuils à ajuster, contournements possibles, etc._
