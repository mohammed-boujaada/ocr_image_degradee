
# Reconnaissance optique de caractères (OCR) sur une image fortement dégradée avec OpenCV et PyTesseract

## 1. Présentation du projet

Ce projet s'inscrit dans le domaine de la vision par ordinateur appliquée à l'extraction de texte. Il vise à reconnaître un mot isolé à partir d'une image fortement dégradée et bruitée (`exam_ultra_hard.png`), en combinant des techniques de prétraitement d'images avec OpenCV et le moteur de reconnaissance optique de caractères (*Optical Character Recognition — OCR*) Tesseract, accessible en Python via la bibliothèque PyTesseract.

Le pipeline applique successivement un filtrage médian, une binarisation automatique par la méthode d'Otsu, des opérations morphologiques, un agrandissement et l'ajout d'une bordure, avant de soumettre l'image au moteur OCR configuré pour un vocabulaire alphanumérique restreint.

L'objectif est de mettre en œuvre une approche expérimentale de restauration d'image appliquée à l'OCR, où la qualité du prétraitement conditionne directement la précision de la reconnaissance.

## 2. Objectifs

Les principaux objectifs du projet sont les suivants :

- Charger une image dégradée et la convertir en niveaux de gris.
- Réduire le bruit impulsionnel par filtrage médian tout en préservant les contours des caractères.
- Binariser l'image automatiquement à l'aide de la méthode d'Otsu, puis inverser sa polarité.
- Renforcer les caractères par des opérations morphologiques (dilatation, ouverture).
- Agrandir l'image et ajouter une bordure pour faciliter la lecture par Tesseract.
- Restreindre le vocabulaire OCR aux majuscules et aux chiffres, et adapter le mode de segmentation.
- Extraire le texte détecté et visualiser les résultats dans le notebook.

## 3. Fonctionnalités principales

### 3.1. Prétraitement de l'image

- **Conversion en niveaux de gris** (`cv2.cvtColor`, `COLOR_BGR2GRAY`) : réduit la représentation de l'image à un seul canal afin de simplifier les traitements ultérieurs.
- **Filtrage médian** (`cv2.medianBlur`, noyau de 21 × 21) : élimine le bruit impulsionnel de type « sel et poivre » tout en préservant les contours des caractères.
- **Seuillage d'Otsu** (`cv2.threshold`, `THRESH_BINARY + THRESH_OTSU`) : binarisation automatique sans seuil manuel.
- **Inversion** (`cv2.bitwise_not`) : obtention de caractères blancs sur fond noir, polarité exploité par les opérations morphologiques suivantes.
- **Dilatation** (`cv2.dilate`, noyau rectangulaire 5 × 5, deux itérations) : épaissit les traits des caractères blancs pour renforcer leur visibilité.
- **Ouverture morphologique** (`cv2.morphologyEx`, `MORPH_OPEN`) : supprime les petites composantes blanches parasites et les artefacts introduits par le traitement.

### 3.2. Mise à l'échelle et ajout de bordure

- **Agrandissement** (`cv2.resize`, facteur d'échelle 6 en x et y, `INTER_LINEAR`) : augmente la taille perçue des caractères, ce qui améliore la reconnaissance lorsque l'image initiale présente une résolution insuffisante.
- **Bordure constante** (`cv2.copyMakeBorder`, 40 pixels sur les quatre côtés, `BORDER_CONSTANT`, `value=255`) : isole le texte des bords de l'image afin de limiter les erreurs de reconnaissance des caractères situés en périphérie. La couleur de la bordure est définie par le paramètre `value` (ici blanc) et doit rester cohérente avec la polarité de l'image soumise à Tesseract.

### 3.3. Configuration du moteur OCR

PyTesseract est appelé avec la configuration suivante :

```text
--oem 1 --psm 8 -c tessedit_char_whitelist=ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789
```

Le détail des paramètres est présenté en section 6. Le texte retourné est nettoyé (suppression des espaces et retours à la ligne via `strip()`).

### 3.4. Visualisation des résultats

Le traitement produit les visualisations suivantes dans le notebook :

- L'image binaire prétraitée, avant agrandissement.
- L'image finale soumise à l'OCR, avec le texte détecté affiché en titre.
- Le texte détecté imprimé dans la console.

Sur l'image étudiée, le pipeline a produit la sortie brute suivante :

```text
Texte détecté : EXAMB
```

Ce résultat correspond à la sortie obtenue lors de l'exécution du pipeline sur l'image utilisée dans ce projet. Il convient de le confronter à la vérité terrain (voir section 10.2).

## 4. Architecture technique

Le pipeline de traitement est organisé en plusieurs étapes successives.

### Étape 1 : Acquisition de l'image

Lecture de l'image `exam_ultra_hard.png` via `cv2.imread`.

### Étape 2 : Conversion en niveaux de gris

Réduction de l'image à un seul canal (`COLOR_BGR2GRAY`).

### Étape 3 : Réduction du bruit

Filtrage médian avec un noyau de 21 × 21.

### Étape 4 : Binarisation et inversion

Seuillage automatique d'Otsu suivi d'une inversion (`bitwise_not`).

### Étape 5 : Opérations morphologiques

Dilatation (noyau rectangulaire 5 × 5, deux itérations) puis ouverture morphologique avec le même noyau.

### Étape 6 : Agrandissement et bordure

Redimensionnement avec un facteur 6 (interpolation linéaire), puis ajout d'une bordure constante blanche de 40 pixels sur les quatre côtés.

### Étape 7 : Inférence OCR et affichage

Appel de `pytesseract.image_to_string` avec la configuration définie, nettoyage de la sortie et visualisation via Matplotlib.

## 5. Technologies utilisées

| Technologie | Utilisation |
|---|---|
| Python | Langage principal du projet |
| Jupyter Notebook | Développement et exécution interactive du pipeline |
| OpenCV | Prétraitement (niveaux de gris, filtrage, seuillage, morphologie, redimensionnement, bordure) |
| NumPy | Représentation et manipulation des matrices d'images |
| PyTesseract (Tesseract OCR) | Reconnaissance optique de caractères |
| Matplotlib | Visualisation des étapes intermédiaires et du résultat |
| Pillow | Utilisé en interne par PyTesseract pour la conversion des images |

## 6. Paramètres de configuration Tesseract

| Paramètre | Valeur | Rôle |
|---|---|---|
| `--oem` | 1 | Sélectionne le moteur de reconnaissance basé sur les réseaux de neurones LSTM de Tesseract |
| `--psm` | 8 | Indique que l'image contient un seul mot |
| `-c tessedit_char_whitelist` | A–Z, 0–9 | Limite les caractères autorisés aux majuscules et aux chiffres, afin de réduire les détections de symboles indésirables |

Remarque : le code contient une configuration alternative testée puis écartée (`--psm 6` avec une whitelist limitée aux chiffres), qui n'a pas permis de détecter le chiffre « 8 » présent dans l'image. Ce point illustre la sensibilité du moteur OCR au mode de segmentation et au vocabulaire autorisé.

## 7. Prérequis

Pour exécuter le projet, les éléments suivants sont nécessaires :

- Python 3.8 ou une version ultérieure.
- pip pour l'installation des dépendances.
- Jupyter Notebook ou JupyterLab.
- Les bibliothèques Python : `opencv-python`, `pytesseract`, `numpy`, `matplotlib` (Pillow est installé comme dépendance de PyTesseract).
- Le moteur **Tesseract OCR installé au niveau du système** et accessible depuis le PATH.
- L'image `exam_ultra_hard.png` disponible à côté du notebook.

## 8. Installation et exécution

### 8.1. Récupération du projet

Cloner le dépôt Git :

```bash
git clone <URL_DU_DEPOT>
```

Accéder au dossier du projet :

```bash
cd ocr_pipeline
```

Remplacer `<URL_DU_DEPOT>` par l'adresse réelle du dépôt GitHub.

### 8.2. Installation des dépendances

Installer les dépendances Python :

```bash
pip install opencv-python pytesseract numpy matplotlib
```

Ou, si un fichier `requirements.txt` est présent :

```bash
pip install -r requirements.txt
```

### 8.3. Installation de Tesseract OCR

Le moteur Tesseract doit être installé séparément, au niveau du système :

- **Windows** : télécharger l'installateur depuis le dépôt [UB-Mannheim Tesseract](https://github.com/UB-Mannheim/tesseract/wiki), puis vérifier que le dossier d'installation est ajouté au PATH.
- **Linux** :

```bash
sudo apt-get install tesseract-ocr
```

- **macOS** :

```bash
brew install tesseract
```

Vérifier l'installation :

```bash
tesseract --version
```

### 8.4. Lancement du notebook

Démarrer Jupyter Notebook :

```bash
jupyter notebook
```

Ouvrir le fichier `ocr_pipeline.ipynb` et exécuter les cellules dans l'ordre prévu.

Avant l'exécution, vérifier les éléments suivants :

- La présence de l'image `exam_ultra_hard.png` au chemin attendu par le code.
- L'accessibilité de Tesseract depuis le PATH (sinon, définir `pytesseract.pytesseract.tesseract_cmd` dans le notebook).
- L'installation correcte des dépendances Python.

## 9. Structure du projet

La structure actuelle du dépôt est la suivante :

```text
ocr_pipeline/
├── ocr_pipeline.ipynb     # Notebook contenant le pipeline et les visualisations
├── exam_ultra_hard.png    # Image source dégradée utilisée par le code
├── README.md              # Documentation du projet
├── .gitattributes
└── .gitignore
```

Remarque : le pipeline n'exporte aucune image traitée (aucun appel à `cv2.imwrite`). Si un fichier `processed_image.png` est présent dans le dépôt, il s'agit d'un artefact exporté manuellement et non d'une sortie du code.

## 10. Limites et perspectives d'amélioration

### 10.1. Paramètres calibrés pour une image

Les valeurs du pipeline (noyau médian 21, noyau morphologique 5 × 5, deux itérations, facteur d'agrandissement 6, bordure de 40 pixels) sont fixes et ajustées pour l'image étudiée. Leur généralisation à d'autres images n'est pas garantie. Une automatisation du choix des paramètres (recherche par grille, sélection adaptative) constituerait une amélioration notable.

### 10.2. Confusions de caractères

Le whitelist réduit les faux caractères mais n'élimine pas les confusions visuelles typiques (« 8 »/« B », « 0 »/« O », « 1 »/« I »). Le commentaire présent dans le code suggère qu'un « 8 » de l'image est difficile à détecter, la sortie « EXAMB » pouvant correspondre à une telle confusion. L'exploitation des scores de confiance (`pytesseract.image_to_data`) et un vote entre plusieurs configurations PSM permettraient de fiabiliser le résultat.

### 10.3. Cas d'usage restreint

Le mode `--psm 8` suppose une image contenant un seul mot. Une extension du pipeline à des images multi-mots ou multi-lignes (modes `--psm 6` ou `--psm 3`) et à un traitement par lots d'images élargirait le périmètre du projet.

### 10.4. Absence d'évaluation quantitative

Aucune métrique de performance n'est calculée. Une comparaison systématique avec la vérité terrain (taux d'erreur de caractères — *Character Error Rate* — et de mots — *Word Error Rate*) sur un ensemble d'images dégradées permettrait d'évaluer objectivement le pipeline.

### 10.5. Polarité et bordure

La polarité après inversion et la couleur de la bordure sont fixées manuellement. Une détection automatique de la polarité (proportion de pixels noirs et blancs) rendrait le pipeline robuste à différents types d'images d'entrée.

### 10.6. Techniques de prétraitement avancées

D'autres pistes pourraient être explorées : débruitage non local (`cv2.fastNlMeansDenoising`), super-résolution, ou méthodes d'apprentissage profond (autoencodeurs de débruitage). Une comparaison avec des moteurs OCR alternatifs (EasyOCR, PaddleOCR) compléterait l'étude.

### 10.7. Robustesse du code

Le code ne vérifie pas le succès de la lecture de l'image (`cv2.imread` retourne silencieusement `None` en cas de fichier manquant) ni la disponibilité du binaire Tesseract. L'ajout de contrôles et de messages d'erreur explicites améliorerait l'expérience d'exécution.

## 11. Conclusion

Ce projet illustre l'intégration de techniques de prétraitement d'images et d'un moteur OCR dans un scénario de reconnaissance de texte sur image dégradée.

L'association du filtrage médian, de la binarisation d'Otsu, des opérations morphologiques et d'une configuration restreinte de Tesseract permet d'étudier comment la qualité du prétraitement influence la précision de la reconnaissance.

Cette architecture fournit une base expérimentale pour approfondir des sujets tels que l'évaluation quantitative de l'OCR, la gestion des confusions de caractères et la généralisation à des ensembles d'images plus variés.

## 12. Auteur

**Mohammed Boujaada**

Étudiant en Master Intelligence Artificielle et Technologies Émergentes  
Faculté des Sciences Appliquées de Nador (FSAN), Université Mohammed Premier, Maroc

- LinkedIn : https://www.linkedin.com/in/mohammed-boujaada-229a3333a/
- GitHub : https://github.com/mohammed-boujaada
