# Fiches sources

Une fiche par source, au format YAML, nommée d'après l'identifiant du registre de [PARAMETRES.md](../PARAMETRES.md) (`FR4.yaml` pour la source FR4). Une fiche dit ce que la source observe, quels paramètres du modèle elle concerne et ce qu'elle ne permet pas de conclure. Le statut de chaque paramètre est tenu dans `PARAMETRES.md`, qui agrège les fiches.

Fiches remplies au 25/10/2026 : FR2, FR3, FR4, FR7, FR15. Ce sont les sources françaises qui appuient ou contredisent le plus directement un paramètre v2. Les autres sources du registre attendent leur fiche.

## Règles

- Ne remplir une fiche qu'après lecture de la source. Indiquer les pages, paragraphes ou heures dans `passage`.
- Distinguer la période observée de la date de publication.
- Le champ `ne_permet_pas_de_conclure` est obligatoire : un lien seul ne valide pas un paramètre.
- Les conversions vers le modèle utilisent l'échelle implicite de `PARAMETRES.md` (case de 10 m, pas de 10 s, groupe de 130 personnes) et donnent leur calcul.
- Ne reprendre aucune donnée permettant d'identifier une personne : pas de nom de manifestant, de requérant ou de témoin. Les décisions de justice sont citées par juridiction, date et numéro.
- `licence` reprend la mention portée par la source, sinon « non relevée ». Citer une source n'en reproduit pas le contenu.

## Modèle

```yaml
id: ""
reference: ""                # auteur ou institution, titre, support
url: ""
type_source: ""              # juridiction | officiel | parlementaire | ONG | presse | témoignage | académique | prépublication
pays: ""
lieu_observe: ""
periode_observee: ""         # dates et heures de l'observation
date_publication: ""
statut_lecture: ""           # lu (pages lues) | résumé seulement | citation secondaire
mecanisme_decrit: ""
chiffres:
  - valeur: ""
    passage: ""              # page, paragraphe ou heure
    conversion_modele: ""    # en pas, cases ou groupes, avec le calcul
parametres_identifiables:
  - parametre: ""            # nom dans le code ou mécanisme du modèle
    file: ""                 # fichier du code, séparés par des virgules s'il y en a plusieurs
    apport: ""               # appuie | contredit | borne | mécanisme seulement
qualite: ""
incertitudes: ""
ne_permet_pas_de_conclure: ""
transposabilite_paris: ""
conflits_avec: []
licence: ""
donnees_personnelles: ""
fiche_redigee: ""            # date
```

Vérifier que les fiches se chargent (PyYAML, absent de `requirements.txt`) :

```bash
python -c "import sys, yaml; [yaml.safe_load(open(p)) for p in sys.argv[1:]]" docs/sources/*.yaml
```
