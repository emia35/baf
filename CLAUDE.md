# Projet BAF — Boussole pour l'Analyse Fonctionnelle

## Contexte
Arthur Guillieux, psychologue clinicien (EMIA35 / Adapei 35, équipe mobile
d'intervention autisme, Bretagne). Outil clinique d'analyse fonctionnelle des
troubles du comportement chez des personnes autistes, destiné aux
professionnels du médico-social et aux familles. Hébergé sur GitHub Pages.
Contrainte absolue : **fichier HTML autonome unique, zéro dépendance externe au
runtime**.

## Objectif de ce chantier : FUSION
Fusionner deux outils HTML existants en un seul fichier « BAF » :
- l'outil de **Recueil des données** (fichier source `baf.html`, ancien « BAF »,
  ~4500 lignes) ;
- l'outil **Indice de confiance** (fichier source `icaf.html`, ancien « ICAF »,
  ~1250 lignes).

Le terme « ICAF » est abandonné. Désormais, dans tout le langage du projet :
**BAF = Recueil des données + Indice de confiance.**

### Nommage des fichiers
- `baf.html` et `icaf.html` = fichiers sources existants, à fusionner (lecture
  seule, conservés comme référence pendant le chantier).
- **Le fichier fusionné final doit s'appeler `index.html`**, impératif : l'outil
  est servi par GitHub Pages à l'adresse
  https://arthurguillieux.github.io/baf/, qui charge automatiquement
  `index.html`. Le lien diffusé aux équipes doit rester valide à l'issue de la
  fusion.

### Architecture cible
1. **Page d'accueil pédagogique** (nouvelle) : note d'intention sur l'analyse
   fonctionnelle. Message clé = une bonne analyse fonctionnelle repose sur trois
   piliers :
   - (1) un bon **recueil des données** → soutenu par l'outil ;
   - (2) du **raisonnement clinique humain** pour poser les hypothèses
     fonctionnelles → reste humain, aucun outil ne le fait à la place du
     clinicien (à dire explicitement) ;
   - (3) un **regard sur l'indice de confiance** du travail produit → soutenu
     par l'outil.

   Puis deux boutons : **« Recueil des données »** et **« Indice de confiance »**.
2. Derrière « Recueil des données » : tout le code de l'ancien outil de recueil.
3. Derrière « Indice de confiance » : tout le code de l'ancien indice de confiance.

### Choix d'architecture : FUSION RÉELLE (namespace unique) — VALIDÉ
Décision arbitrée par Arthur : **pas d'iframe**. Vraie fusion dans un seul
namespace, pour pouvoir partager l'état en mémoire entre les deux outils à terme
(l'outil est appelé à être de plus en plus connecté). La direction artistique de
référence est celle de l'outil de recueil actuel.

## Diagnostic des collisions (déjà établi — à exploiter, ne pas refaire)
- **Palette CSS quasi identique** entre les deux fichiers (mêmes variables
  `:root`, mêmes valeurs), à deux exceptions : `--radius` (16px contre 14px) et
  `--radius-sm` (10px contre 8px). → réconcilier sur une seule valeur.
- **Aucune collision de noms de fonctions JS** entre les deux fichiers.
- **Collision structurelle décisive** : les deux sont des SPA bâties sur le même
  système `section.view` / `section.view.show`, ET les deux définissent un
  `id="view-accueil"`. Les deux réutilisent aussi `.top`, `.main` (nav),
  `.card`, etc.
- **Stratégie de résolution** : préfixer classes / ID / variables globales de
  l'indice de confiance (ex. préfixe `idc-`) ; renommer les deux `view-accueil`
  existants et créer un nouvel accueil global distinct ; unifier la nav ;
  fusionner les deux blocs `:root`. Vérifier qu'aucune variable globale JS ne
  fuit entre les deux périmètres.

## Méthode de travail (impérative)
- **Contenu avant code** : valider la structure et le contenu AVEC Arthur avant
  de coder. En particulier, faire valider le **texte de la note d'intention de
  l'accueil** avant de l'intégrer. Ne rien coder sans validation préalable.
- **Développement par passes nommées** (A, B, C…), périmètre explicite annoncé
  pour chaque passe, **smoke test après chaque passe**.
- **Environnement réel : Windows, machine pro verrouillée, sans Node.js ni
  Python disponibles** (et sans possibilité d'installation). Le CLAUDE.md
  initial supposait un environnement Linux avec ces outils : ce n'est pas le
  cas ici.
- **Édition** : outils natifs (Edit pour les remplacements ciblés, `sed`/`grep`
  via Git Bash pour les renommages répétitifs sur des lignes précises). Pas de
  scripts Python heredoc.
- **Pas de smoke test automatisé (Node/jsdom indisponibles)**. Le test réel
  après chaque passe : Arthur ouvre `index.html` dans son navigateur et
  vérifie. Avant chaque passe, indiquer explicitement à Arthur quoi observer
  pour valider la passe.
- **Vigilance encodage** : le code contient de nombreuses apostrophes et
  accents français en UTF-8. Après toute édition (et systématiquement après la
  première édition d'une passe), relire un passage accentué pour vérifier
  qu'aucun caractère n'a été corrompu. En cas de corruption détectée : arrêt
  immédiat, signaler à Arthur avant de continuer.
- **Single-file** : tout reste dans un seul fichier HTML autonome.
- **Git** : un commit par passe, pour pouvoir revenir en arrière proprement.

## Style de communication attendu par Arthur
- Franchise totale, **zéro compliment**, pas de flagornerie.
- Dire « je ne sais pas » plutôt que d'approximer.
- Aller à l'essentiel.
- Signaler ambiguïtés, décisions sensibles et risques AVANT d'agir, puis laisser
  Arthur arbitrer.
- **Tout en français.**

## Première étape concrète
Avant tout code : proposer à Arthur le **texte de la note d'intention** de la
page d'accueil, le faire valider, puis seulement ensuite démarrer la fusion par
passes.
