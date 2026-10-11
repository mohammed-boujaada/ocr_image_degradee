# Reconnaissance Optique de Caractères (OCR) sur Image Dégradée

## Objectif
Extraire avec précision du texte à partir d'une image fortement dégradée et bruitée en combinant des techniques avancées de prétraitement d'image avec OpenCV et le moteur de reconnaissance Tesseract.

## Pipeline de Traitement d'Image
L'extraction de texte sur une image de faible qualité nécessite un prétraitement rigoureux pour maximiser le taux de réussite du moteur OCR. Le pipeline mis en œuvre suit les étapes suivantes :

1. Conversion en niveaux de gris : Réduction de la dimensionalité des données pour simplifier le traitement.
2. Filtrage médian : Application d'un flou médian (noyau 21x21) pour supprimer efficacement le bruit "poivre et sel" tout en préservant les contours des caractères.
3. Seuillage d'Otsu et inversion : Conversion en image binaire. La méthode d'Otsu détermine automatiquement le seuil optimal, suivi d'une inversion pour obtenir des caractères clairs sur un fond sombre.
4. Opérations morphologiques : 
   - Dilatation : Épaississement des traits des caractères à l'aide d'un noyau rectangulaire (5x5) pour les rendre plus robustes.
   - Ouverture : Élimination des petits résidus de bruit isolés générés lors de la dilatation.
5. Mise à l'échelle (Upscaling) : Redimensionnement de l'image par un facteur de 6 (interpolation linéaire). Les moteurs OCR comme Tesseract sont très sensibles à la résolution des polices.
6. Ajout d'une bordure : Ajout d'une marge blanche de 40 pixels autour de l'image pour éviter que les caractères situés en bordure ne soient tronqués lors de l'analyse.

## Configuration du Moteur OCR
Pour optimiser la détection et éliminer les faux positifs, PyTesseract est configuré avec des paramètres spécifiques :
- `--oem 1` : Utilisation du moteur de réseaux de neurones LSTM (le plus performant).
- `--psm 8` : Mode de segmentation traité comme un seul mot (Single word).
- `-c tessedit_char_whitelist=ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789` : Restriction stricte des caractères autorisés aux lettres majuscules et aux chiffres.

## Résultat
Après l'application de ce pipeline de prétraitement, le moteur OCR parvient à identifier correctement le texte masqué dans l'image dégradée.

Texte détecté : EXAMB

## Structure du Projet
- `ex1.py` : Script Python contenant le pipeline de traitement et l'OCR.
- `original_image.png` : Image source dégradée d'origine.
- `processed_image.png` : Image finale après prétraitement (pour visualisation).
- `README.md` : Documentation du projet.
- `.gitignore` : Fichier de configuration pour exclure les fichiers temporaires du contrôle de version.

## Prérequis et Installation
1. Cloner le dépôt ou télécharger le dossier.
2. Installer les dépendances Python requises :
   ```bash
   pip install opencv-python pytesseract matplotlib