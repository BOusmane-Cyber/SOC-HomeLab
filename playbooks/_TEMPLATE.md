# Playbook — [Nom de l'incident]

> Procédure de réponse suivie par l'analyste face à cette alerte. Structure inspirée du cycle NIST : Détection → Analyse → Confinement → Éradication → Récupération → Leçons.

## Déclencheur

Quelle alerte déclenche ce playbook ? (référence la détection associée)

## 1. Détection & triage

- D'où vient l'alerte ? Quel est le niveau de sévérité ?
- Premières questions : quel hôte ? quel compte ? quelle IP source ?

## 2. Analyse / investigation

- [ ] Vérifier le contexte (heure, géoloc IP, compte concerné)
- [ ] Vrai positif ou faux positif ?
- [ ] L'attaque a-t-elle réussi ? (chercher un succès après les échecs)
- [ ] Y a-t-il eu des actions post-compromission ?

## 3. Confinement

Actions pour limiter l'impact (isoler l'hôte, bloquer l'IP, désactiver le compte…).

## 4. Éradication & récupération

Supprimer la cause, restaurer un état sain, réinitialiser les identifiants.

## 5. Leçons apprises

Qu'est-ce qui a marché ? Quelle règle/seuil ajuster ? Quelle détection ajouter ?
