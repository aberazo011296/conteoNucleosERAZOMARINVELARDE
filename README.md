# Detección y Cuantificación de Núcleos Celulares en Imágenes H&E de Hígado de Ratón con StarDist 2D 

Este proyecto implementa un pipeline de machine learning para detectar, segmentar y cuantificar automáticamente núcleos celulares en imágenes histológicas H&E de hígado de ratón. Utilizamos el modelo StarDist 2D con ajuste fino (fine-tuning) sobre un dataset anotado manualmente en QuPath.

## Requisitos

- Python 3.x
- Google Colab con GPU T4
- Paquetes: StarDist, TensorFlow, scikit-image, csbdeep (ver requirements.txt)

## Flujo de Trabajo

1. **Anotación en QuPath**: Generamos el ground truth trazando manualmente los contornos nucleares en QuPath. Usamos un script en Groovy para exportar de forma sincronizada los pares de imágenes H&E y máscaras de instancias. El dataset consiste en 14 pares.

2. **Augmentation**: Expandimos el dataset de 14 a 224 pares aplicando transformaciones (rotaciones, flips, variación de brillo) para mejorar la robustez del modelo.

3. **Ajuste Fino de StarDist**: Realizamos transfer learning partiendo del modelo preentrenado `2D_versatile_he`. Configuración:
   - 60 épocas 
   - Learning rate = 3e-5
   - Batch size = 4
   - Patch size = 128x128

4. **Optimización**: Ajustamos los umbrales de probabilidad (0.5106) y non-maximum suppression (0.4000) para maximizar el F1-score sobre el dataset de validación.

5. **Inferencia**: Proporcionamos una celda autónoma que carga los pesos entrenados y realiza la detección sobre nuevas imágenes. Genera 3 paneles de visualización (H&E original, ground truth, predicción de StarDist) y exporta un archivo CSV con atributos morfométricos para cada núcleo detectado (área, diámetro equivalente, circularidad, excentricidad, coordenadas).

## Resultados

Sobre las 14 imágenes de validación, nuestro modelo alcanzó:
- F1-score = 0.877
- Precisión = 0.836 
- Recall = 0.922

Esto supera nuestro objetivo de F1 ≥ 0.75 y alcanza el umbral publicable de 0.85. En total se exportaron 1,715 detecciones nucleares con sus atributos morfométricos completos.

## Uso del Código

1. Clona este repositorio
2. Instala las dependencias (ver Requisitos)
3. Monta Google Drive en la sesión de Colab
4. Carga tus imágenes H&E y máscaras en las rutas especificadas
5. Ejecuta el notebook `ERAZO_MARIN_VELARDE_PROYECTOMIA.ipynb` celda por celda
6. Para inferencia en nuevas imágenes, usa la Celda 9 y apunta a la carpeta con tus imágenes
7. Revisa los resultados en el folder `resultados/` (imágenes anotadas y CSVs)

## Créditos

- Ana Belén Erazo Baroja
- Sebastián Andree Marín Arias  
- Ariel Osmar Velarde Zambrano

Universidad de Las Américas - Ingeniería y Ciencias Aplicadas - Maestría en Inteligencia Artificial Aplicada
