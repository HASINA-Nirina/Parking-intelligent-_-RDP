# Parking Intelligent — Réseau de Petri (Projet complet)

Application web (Python + Flask) qui modélise, simule et analyse un
**Réseau de Petri** représentant un parking intelligent.

---

## 1. Structure du projet

parking_rdp/
├── app.py                 # Serveur Flask : toutes les routes web + API
├── petri.py                # Moteur du Réseau de Petri (le cœur mathématique)
├── requirements.txt         # Dépendances (Flask)
├── .gitignore
├── instance/                # Base SQLite (historique) créée automatiquement
├── templates/
│   ├── base.html
│   ├── index.html            # Accueil
│   ├── model.html             # Places / Transitions / Arcs / Scénarios
│   ├── simulation.html         # Diagramme RDP + tir des transitions
│   └── analysis.html           # Matrices Pre/Post/W, conflits, propriétés
└── static/
    ├── css/style.css
    └── js/
        ├── diagram.js         # Dessin SVG du réseau
        ├── simulation.js       # Logique de la page simulation
        └── analysis.js          # Logique de la page analyse


Le modèle implémenté (voir `petri.py` pour tous les détails et
commentaires) :

- **Places** : P1 Véhicule en entrée, P2 Accès validé, P3 Place libre,
  P4 Véhicule garé, P5 Sortie demandée, P6 Parking plein (signal),
  P7 Paiement validé.
- **Transitions** : T1 Contrôle entrée, T2 Affecter une place / garer,
  T3 Signaler parking plein, T4 Demander sortie, T5 Valider paiement,
  T6 Libérer la place.
- **Point important à dire à l'oral** : le cahier des charges signalait
  lui-même que la séparation entre T2 et T3 devait être ajustée pendant
  l'implémentation pour éviter une double consommation de jetons. La
  solution retenue ici : T2 fusionne "affecter" et "garer" (elle a besoin
  d'un jeton dans P2 **et** dans P3 — c'est une **synchronisation**), et T3
  devient la transition qui signale "parking plein" via un **arc
  inhibiteur** (elle n'est active que si P3 = 0). Cela crée aussi un
  **conflit structurel** naturel sur P1 (P1 alimente T1 et T3), qui sert
  très bien à démontrer la notion de conflit/concurrence du cours.

---

## 2. Installation et lancement en local

Ouvrez un terminal (PowerShell sur Windows, ou Terminal sur Mac/Linux)
dans le dossier `parking_rdp`.

### Étape 1 — Vérifier Python

python --version

Si la commande n'existe pas, essayez `python3 --version`. Il faut Python
3.9 ou plus récent.

### Étape 2 — Créer un environnement virtuel (recommandé, pas obligatoire)

python -m venv venv

Puis activez-le :
- Windows : `venv\Scripts\activate`
- Mac/Linux : `source venv/bin/activate`

### Étape 3 — Installer les dépendances avec pip

pip install -r requirements.txt


### Étape 4 — Lancer l'application

python app.py

Vous devez voir dans le terminal :

Running on http://127.0.0.1:5000


### Étape 5 — Ouvrir le navigateur
Allez sur **http://127.0.0.1:5000**. Naviguez entre les pages
**Accueil / Modèle / Simulation / Analyse** avec le menu en haut.

Pour arrêter le serveur : `CTRL + C` dans le terminal.

---

## 6. Ce qui est déjà fait vs les extensions possibles

**Déjà fait et fonctionnel** : modèle complet (7 places, 6 transitions),
franchissement, marquage, historique, matrices Pré/Post/W, détection de
conflits, détection de blocage, 5 scénarios prêts à l'emploi, export JSON,
persistance SQLite de l'historique, interface web complète en français.

**Non nécessaire pour la version 1** (mentionné dans le cahier des
charges comme amélioration future, à ne pas faire avant la soutenance) :
capteurs réels, barrière physique, reconnaissance de plaque, plusieurs
zones de parking, tarification, notifications.

---

## 7. En cas de problème

- **`ModuleNotFoundError: No module named 'flask'`** → vous n'avez pas
  fait `pip install -r requirements.txt`, ou vous n'êtes pas dans le bon
  environnement virtuel.
- **`Address already in use`** → un ancien serveur tourne encore ; fermez
  le terminal précédent ou changez de port avec `app.run(port=5050)`
  dans `app.py`.
- **La page est blanche / erreur 500** → regardez le message d'erreur
  affiché dans le terminal où tourne `python app.py`, il indique
  exactement la ligne du problème.
