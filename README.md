# Reconnaissance optique de caractères (OCR) sur des images dégradées

## Objectif

Ce projet démontre l'extraction de texte à partir d'une image fortement dégradée et bruitée (`original_image.jpeg`), en combinant des techniques avancées de prétraitement d'images avec OpenCV et le moteur de reconnaissance optique de caractères (OCR) PyTesseract.

## Pipeline de traitement de l'image

L'extraction de texte à partir d'images de faible qualité nécessite un prétraitement rigoureux afin d'améliorer la précision du moteur OCR. Le pipeline mis en œuvre comprend les étapes suivantes :

1. **Conversion en niveaux de gris** : réduit la représentation de l'image à un seul canal afin de simplifier les étapes de traitement ultérieures.

2. **Filtrage médian** : applique un flou médian avec un noyau de taille `21 × 21` pour éliminer efficacement le bruit impulsionnel, notamment le bruit de type « sel et poivre », tout en préservant autant que possible les contours des caractères.

3. **Seuillage d'Otsu et inversion** : convertit l'image en une représentation binaire à l'aide de la méthode automatique de seuillage d'Otsu, puis inverse les valeurs des pixels afin d'obtenir des caractères blancs sur un fond noir. Cette représentation facilite certaines opérations morphologiques.

4. **Opérations morphologiques** :

   - **Dilatation** : épaissit les traits des caractères à l'aide d'un noyau rectangulaire de taille `5 × 5`, appliqué pendant deux itérations, afin de renforcer leur visibilité.
   - **Ouverture morphologique** : permet d'éliminer certaines petites composantes parasites et certains artefacts introduits lors du traitement.

5. **Agrandissement de l'image** : redimensionne l'image avec un facteur d'échelle de `6`, en utilisant l'interpolation linéaire. L'agrandissement peut améliorer la lisibilité des caractères pour le moteur OCR de Tesseract, notamment lorsque l'image initiale présente une résolution insuffisante.

6. **Ajout d'une bordure** : ajoute une marge de `40` pixels autour de l'image afin de limiter les erreurs de reconnaissance des caractères proches des bords. La couleur de cette bordure doit être adaptée à la polarité de l'image finale.

## Configuration du moteur OCR

Afin de limiter les fausses détections et d'adapter la reconnaissance au cas étudié, PyTesseract est configuré avec les paramètres suivants :

- `--oem 1` : utilise le moteur de reconnaissance basé sur les réseaux neuronaux LSTM de Tesseract.
- `--psm 8` : indique que l'image contient un seul mot, ce qui convient lorsque le texte recherché est constitué d'un mot isolé.
- `-c tessedit_char_whitelist=ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789` : limite les caractères autorisés aux lettres majuscules de A à Z et aux chiffres de 0 à 9, afin de réduire les détections de symboles indésirables.

## Résultats

Après l'application du pipeline de prétraitement, le moteur OCR a détecté le texte suivant dans l'image dégradée :

- **Texte détecté** : `EXAMB`

Ce résultat correspond à la sortie obtenue lors de l'exécution du pipeline sur l'image utilisée dans ce projet.

## Structure du projet

```text
.
├── ocr_pipeline.ipynb     # Notebook Jupyter contenant le pipeline et les visualisations
├── original_image.jpeg    # Image source dégradée
├── processed_image.png    # Image prétraitée
├── .gitattributes
├── .gitignore
└── README.md              # Documentation du projet
```

## Prérequis

- Python 3.10 ou version ultérieure
- OpenCV (`opencv-python`)
- PyTesseract (`pytesseract`)
- Matplotlib (`matplotlib`)
- Pillow (`Pillow`)

**Remarque :** le moteur Tesseract OCR doit également être installé au niveau du système et accessible depuis le `PATH`.

### Installation de Tesseract OCR

- **Windows** : télécharger l'installateur depuis le [dépôt UB-Mannheim Tesseract](https://github.com/UB-Mannheim/tesseract/wiki).
- **Linux** : exécuter la commande suivante :

  ```bash
  sudo apt-get install tesseract-ocr
  ```

- **macOS** : exécuter la commande suivante :

  ```bash
  brew install tesseract
  ```

## Installation et exécution

1. Cloner le dépôt Git ou télécharger les fichiers du projet.

2. Installer les dépendances Python nécessaires :

   ```bash
   pip install opencv-python pytesseract matplotlib Pillow
   ```

3. Vérifier que Tesseract OCR est installé et accessible depuis le système.

4. Ouvrir et exécuter le notebook Jupyter :

   ```bash
   jupyter notebook ocr_pipeline.ipynb
   ```

   Il est également possible d'adapter facilement le code du notebook en un script Python autonome nommé `ocr_pipeline.py`.

## Auteur

**Mohammed Boujaada**

Étudiant en Master Intelligence Artificielle et Technologies Émergentes à la Faculté Pluridisciplinaire de Nador (FSAN).

- [LinkedIn](https://www.linkedin.com/in/mohammed-b0ujaada-229a3333a/)
- [GitHub](https://github.com/mohammed-boujaada)
