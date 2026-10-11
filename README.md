
# Optical Character Recognition (OCR) on Degraded Images

## Objective
This project demonstrates the extraction of text from a heavily degraded and noisy image (`original_image.png`) by combining advanced image preprocessing techniques using OpenCV with the PyTesseract OCR engine.

## Image Processing Pipeline
Extracting text from low-quality images requires rigorous preprocessing to maximize the OCR engine's accuracy. The implemented pipeline consists of the following sequential steps:

1. **Grayscale Conversion**: Reduces the image dimensionality to simplify subsequent processing.
2. **Median Filtering**: Applies a median blur with a 21x21 kernel to effectively remove salt-and-pepper noise while preserving the sharp edges of the characters.
3. **Otsu's Thresholding and Inversion**: Converts the image to binary using Otsu's automatic thresholding, followed by a bitwise inversion to ensure white characters on a black background, which is optimal for contour and morphological operations.
4. **Morphological Operations**: 
   - *Dilation*: Thickens the character strokes using a 5x5 rectangular kernel (2 iterations) to make them more robust.
   - *Opening*: Eliminates small noise artifacts generated during the dilation process.
5. **Upscaling**: Resizes the image by a factor of 6 using linear interpolation. Tesseract's LSTM engine is highly sensitive to font resolution, and upscaling significantly improves recognition accuracy.
6. **Border Padding**: Adds a 40-pixel white border around the image to prevent characters located at the edges from being truncated or misinterpreted during analysis.

## OCR Configuration
To optimize detection and eliminate false positives, PyTesseract is configured with specific parameters tailored to this use case:
- `--oem 1`: Utilizes the LSTM neural network engine (the most accurate default engine).
- `--psm 8`: Treats the image as a single word (Single word mode), preventing the engine from searching for sentence structures.
- `-c tessedit_char_whitelist=ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789`: Strictly restricts the allowed characters to uppercase letters and digits, filtering out any spurious symbol detections.

## Results
After applying the preprocessing pipeline, the OCR engine successfully identifies the hidden text in the degraded image.

- **Detected Text**: `EXAMB`

## Project Structure
```text
.
├── ex1.ipynb                      # Jupyter Notebook containing the pipeline and visualization
├── exam_ultra_hard.png            # Source degraded image
└── README.md                      # Project documentation
```

## Requirements
- Python 3.10+
- OpenCV (`opencv-python`)
- PyTesseract (`pytesseract`)
- Matplotlib (`matplotlib`)
- Pillow (`Pillow`)

*Note: The system-level Tesseract OCR engine must be installed and added to your system's PATH.*
- **Windows**: Download the installer from [UB-Mannheim Tesseract Wiki](https://github.com/UB-Mannheim/tesseract/wiki)
- **Linux**: `sudo apt-get install tesseract-ocr`
- **macOS**: `brew install tesseract`

## Installation and Execution

1. Clone the repository or download the project files.
2. Install the required Python dependencies:
   ```bash
   pip install opencv-python pytesseract matplotlib Pillow
   ```
3. Ensure Tesseract OCR is installed on your system.
4. Open and execute the Jupyter Notebook:
   ```bash
   jupyter notebook ex1.ipynb
   ```
   Alternatively, the logic can be easily adapted into a standalone Python script (`ex1.py`).

## Author
**Mohammed Boujaada**  
**MOhamed Zahir**
Master's Student in Artificial Intelligence & Emerging Technologies at FSAN  
[LinkedIn](https://www.linkedin.com/in/mohammed-b0ujaada-229a3333a/) | [GitHub](https://github.com/mohammed-boujaada)

2. **Professionnalisme** : L'absence totale d'emojis et l'utilisation d'un vocabulaire technique précis (dimensionality, salt-and-pepper noise, bitwise inversion) renforcent la crédibilité du projet.
3. **Reproductibilité** : La section "Requirements" et "Installation" permet à n'importe qui (recruteur, professeur) de reproduire vos résultats en quelques minutes.

N'oubliez pas de mettre à jour le lien LinkedIn dans la section "Author" avec votre URL exacte si nécessaire !
