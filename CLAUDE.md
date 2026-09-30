# Projet partogramme

Application web « Partogramme » : suivi du travail en salle de naissance.
Site statique (HTML/CSS/JavaScript, sans étape de build), données sur Supabase.
Maquette de référence : `maquette.png`.

## Pages (onglets)

- **Suivi du travail** : choix d'une patiente et de sa grossesse, indicateurs
  (dilatation, durée du travail, présentation, dernier examen), courbe de
  dilatation (Chart.js), tableau des examens avec ajout/modification/suppression.
- **Patientes** : liste, création, modification (pas de suppression dans l'interface).
- **Grossesses** : par patiente, avec équipe référente (sage-femme, obstétricien,
  anesthésiste, pédiatre).
- **Enfants** : par grossesse (prénom, sexe, naissance, poids, taille).
- **Praticiens** : liste, création, modification.

Bouton « Exporter en Excel » (ExcelJS) actif seulement sur la page Suivi.

## Fichiers

- `index.html` : les 5 pages (une `div.page` par onglet) et les formulaires.
- `config.js` : URL du projet Supabase et clé publishable (rien d'autre — jamais
  de clé secrète ici, même collée par erreur avec les autres clés).
- `app.js` : client Supabase, navigation entre onglets, page Patientes.
- `examens.js`, `grossesses.js`, `enfants.js`, `praticiens.js` : une page chacun,
  même schéma (charger, formulaire d'ajout/édition, enregistrer).
- `export.js` : export Excel du suivi.
- Les bibliothèques (supabase-js, Chart.js, ExcelJS, police Inter) viennent du
  CDN jsdelivr, chargées en bas de `index.html`.

## Base Supabase

Projet : `bqmgxpphovghgyztxgdu`. Schéma `public`, 6 tables :

- `patiente` (id_patiente) : nom, nom_marital, prenom, gs_rh, date_naissance.
- `grossesse` (id_grossesse) : id_patiente, debut, parite, hiv, toxo,
  duree_travail, duree_expulsion + 4 clés vers `praticien`
  (id_sage_femme, id_obstetricien, id_anesthesiste, id_pediatre).
- `examen` (id_examen) : id_grossesse, date_heure, dilatation (0–10),
  presentation_hauteur (-5 à 5), id_orientation, remarque.
- `enfant` (id_enfant) : id_grossesse, prenom, sexe, date_heure_naissance,
  poids_naissance_kg, taille_cm.
- `praticien` (id_praticien) : nom, prenom, specialite.
- `orientation` (id_orientation) : nom_orientation, fichier_pictogramme.
  Table de référence en lecture seule (aucune politique d'écriture).

Types énumérés : `groupe_sanguin` (A+…AB-, inconnu), `specialite`
(Sage-femme, Obstétricien, Anesthésiste, Pédiatre), `sexe` (Garçon, Fille).

RLS : toutes les tables sont protégées, avec des politiques ouvertes en
lecture/ajout/modification pour la clé publishable (données fictives du TP).
Suppressions ouvertes seulement sur `examen`. Si une action renvoie « la base a
refusé l'opération », vérifier qu'une politique RLS correspondante existe.

## À savoir

- Dates affichées au format jour/mois/année (JJ/MM/AAAA) — préférence de
  l'utilisateur ; ne jamais afficher AAAA-MM-JJ dans l'interface.
- Données fictives du TP, 4 patientes, quelques examens.
- Site en ligne : https://sps-g02-parto.professeurpetitchat.com/ (mise en ligne
  par `git push`, workflow `.github/workflows/deploy.yml` — ne pas modifier).
- Avant de dire que c'est fini : vérifier dans le navigateur (MCP Playwright)
  que les pages fonctionnent sans erreur dans la console. En local, servir le
  dossier avec un petit serveur HTTP (le protocole `file://` ne marche pas pour
  l'accès à Supabase).
- Python n'est pas installé sur ce PC ; pour un serveur local, utiliser
  `npx.cmd http-server` (le script `npx.ps1` est bloqué par Windows).
