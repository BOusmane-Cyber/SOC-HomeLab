# Playbook — Brute Force RDP (T1110)

> Exemple complet. Associé à la détection [`T1110-bruteforce-rdp.md`](../detections/T1110-bruteforce-rdp.md).

## Déclencheur

Alerte SIEM « Brute Force RDP » : plus de 10 Event ID 4625 (LogonType 10) depuis une même IP en 5 minutes.

## 1. Détection & triage

- Sévérité initiale : **Moyenne**.
- Relever : hôte ciblé, compte visé, IP source, horodatage.
- Question immédiate : l'IP source est-elle interne attendue, ou inconnue/externe ?

## 2. Analyse / investigation

- [ ] Géolocaliser / contextualiser l'IP source (interne ? VPN ? inconnue ?)
- [ ] **Chercher un Event ID 4624 (succès)** depuis la même IP juste après la rafale
      → si présent : **le brute force a réussi**, passer en sévérité **Haute**
- [ ] Si succès : examiner l'activité du compte après connexion (process créés via Sysmon, mouvements latéraux, exfiltration)
- [ ] Vérifier si le compte ciblé est privilégié (admin du domaine ?)

## 3. Confinement

- Bloquer l'IP source au niveau pare-feu.
- Si compromission confirmée : isoler `WIN-01` du réseau.
- Désactiver / forcer la réinitialisation du compte ciblé.

## 4. Éradication & récupération

- Réinitialiser le mot de passe (politique robuste) et révoquer les sessions actives.
- Vérifier l'absence de persistance laissée par l'attaquant (tâches planifiées, comptes ajoutés, clés de registre Run).
- Restaurer l'hôte si nécessaire, puis remettre en service.

## 5. Leçons apprises

- Restreindre RDP : ne pas l'exposer, exiger un VPN, activer le verrouillage de compte (account lockout policy).
- Activer le MFA sur les accès distants.
- Ajuster le seuil de détection si des faux positifs sont apparus.
- Ajouter une détection corrélée « brute force **réussi** » (4625 en rafale suivi d'un 4624).
