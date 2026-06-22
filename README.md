# Journal des attaques simulées

> Tiens ce registre à jour : une ligne par attaque rejouée. Il sert de fil conducteur entre `attacks/`, `detections/` et `playbooks/`.

| Date | Attaque | Outil / méthode | Technique ATT&CK | Détection associée | Playbook |
|------|---------|-----------------|------------------|--------------------|----------|
| AAAA-MM-JJ | Brute force RDP | hydra | T1110 | `detections/T1110-bruteforce-rdp.md` | `playbooks/bruteforce-rdp.md` |
| | | | | | |

## Idées d'attaques à couvrir ensuite

- **T1059** — Exécution via PowerShell / cmd
- **T1547** — Persistance (clé de registre Run)
- **T1003** — Dump de credentials (Mimikatz / LSASS)
- **T1021** — Mouvement latéral (RDP / SMB)
- **T1053** — Tâche planifiée malveillante
- **T1486** — Simulation de ransomware (chiffrement de fichiers test)

> Astuce : **Atomic Red Team** fournit chacune de ces techniques clé en main, déjà mappées sur ATT&CK.
