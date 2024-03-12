# 5R - Premio Cervera

<img src="./imgs/Red-5R-Red-Cervera-300x109.png" alt="Logo5R"/>

&nbsp;
&nbsp;

Este concurso se encuentra enmarcado dentro de las actividades del proyecto “5R – Red Cervera de tecnologías robóticas en fabricación inteligente”, financiado por el Ministerio de Ciencia e Innovación a través del Centro para el Desarrollo Tecnológico Industrial (CDTI), número de contrato CER-20211007, en el marco del programa “Centros Tecnológicos de Excelencia Cervera”. La red se encuentra formada por los siguientes centros: TEKNIKER, EURECAT, AIMEN, CARTIF y CATEC.

El proyecto 5R se basa en el desarrollo de tecnologías robóticas, así como la creación de una Fábrica Piloto por cada uno de los centros participantes. 5R tiene la misión de establecer una red colaborativa, con las tecnologías, herramientas e infraestructuras necesarias para actuar como elemento tractor del desarrollo e introducción de nuevas tecnologías robóticas en el tejido industrial de fabricación español.

Gracias al lanzamiento de este reto se pretende dar a conocer la red formada por los distintos centros tecnológicos españoles, así como las actividades de estos y el acercamiento a las universidades.


## Descripción

El concurso consiste en resolver un problema de segmentación de instancias. La segmentación de instancias de una escena es un proceso avanzado de visión por ordenador que va más allá de la simple detección de objetos o la segmentación semántica. Mientras que la detección de objetos se centra en identificar la ubicación y la categoría de los objetos de una imagen, y la segmentación semántica trata de comprender y etiquetar cada píxel de la imagen como parte de una categoría de objetos, la segmentación instantánea combina estos conceptos.

Por lo tanto, el reto será desarrollar un algoritmo que pueda identificar y segmentar cada objeto individual dentro de una imagen, es decir, que reconozca y separe múltiples instancias del mismo tipo de objeto. Al mismo tiempo, debe ser capaz de generar una máscara de píxeles que cubra exactamente la instancia del objeto, separándola no solo del fondo, sino también de otras instancias.


## Evaluación

Los participantes se enfrentan a varios retos, entre los que destacan:

- Oclusión y solapamiento: instancias de objetos que se solapan u ocultan entre sí, lo que complica su identificación individual y segmentación. Será necesario detectar objetos que sean visibles en más del 50% de su superficie.
- Eficacia y rendimiento: conseguir una alta precisión de segmentación manteniendo un rendimiento computacional aceptable


## Intersection over Union (IoU) Overview

Intersection over Union (IoU), also known as Jaccard Index, is a widely used metric for evaluating the performance of object detection and segmentation algorithms. It is a measure of the overlap between a predicted bounding box or mask and its corresponding ground truth counterpart.

### Formula:

$$ IoU = \frac{|A \cap B|}{|A \cup B|} $$

where: 
- A is the predicted mask 
- B is the ground truth mask

The IoU score ranges from 0 to 1, where 0 indicates no overlap and 1 indicates perfect overlap. A higher IoU score indicates a better match between the predicted mask and the ground truth mask.

### Benefits of IoU:

Simplicity: IoU is a straightforward metric to understand and calculate.
Robustness: IoU is relatively insensitive to minor variations in the segmentations, which is important for real-world applications.


## Metric Description

The overall segmentation performance is assessed by computing the average IoU score across all images. The IoU score for each image is computed by averaging the IoU score for each object mask within that image.

The metric code can be found in the provided Jupyter notebook (for now here: IoU Metric Notebook)

### Formula

Specifically, for image i, the IoU score is calculated for each individual object mask j within that image. The average of these scores represents the image's final IoU score IoU_i. This is expressed mathematically as:

$$ \text{IoU}{i} = \frac{1}{M_{i}}\sum_{j=1}^{M_{i}}\text{IoU}_{ij} $$

where: 
- M_i is the total number of object masks in image i 
- IoU_ij is the Intersection over Union score for the j-th mask in image i

$$ \text{Final Score} = \frac{1}{N}\sum_{i=1}^{N}\text{IoU}_{i} $$

where: 
- N is the total number of images in the dataset 
- IoU_i is the average Intersection over Union score for image i


## Ground truth and predicted masks matching

Matching Ground Truth and Predicted Masks: For each ground truth mask, the predicted mask with the highest IoU is considered the corresponding predicted mask. This ensures that each ground truth mask is matched with the predicted mask that best represents its shape and location.

IoU Threshold: Only ground truth masks with an IoU score above a certain threshold (0.5 in this challenge) are considered for scoring. This helps to filter out predictions that are only weakly correlated with the ground truth masks. This ensures that the evaluation focuses on the more meaningful matches and prevents inflated scores due to overly conservative predictions.


## Masks Encoding to String

To reduce the size of the submission CSV file and simplify the evaluation process, the predicted masks are encoded using a special format. This encoding scheme involves converting the binary masks into run-length encoding (RLE) format, compressing the RLE strings, and then encoding them using base64 encoding.

The encoding functions and an example of CSV encoding are available in the provided Jupyter notebook (for now here: Cervera Encoding and Decoding Notebook)

RLE (Run-length encoding): RLE is a compact representation of binary masks that exploits the repetitive nature of pixels. It works by replacing consecutive runs of identical pixels with a single code that indicates the length of the run. This significantly reduces the size of the encoded mask representation.

zlib Compression: The compressed RLE strings are further compressed using zlib compression algorithm. This further reduces the size of the encoded masks, making it more efficient to store and transmit them.

Base64 Encoding: The final step is to encode the compressed RLE strings using base64 encoding. Base64 encoding ensures that the encoded masks can be stored and transmitted as a standard text string, making them compatible with various data formats and protocols.

The resulting encoded strings are included in the 'EncodedMasks' column of the submission CSV file.


## Decoding Masks from String

To evaluate the predicted masks, the encoded strings in the submission CSV file need to be decoded back into binary masks. This process involves the following steps:

Base64 Decoding: The encoded strings are first decoded from base64 format. This converts the encoded masks back into binary strings.

RLE Decompression: The decoded binary strings are then decompressed using zlib decompression algorithm. This restores the RLE representations of the masks.

RLE to Binary Mask Conversion: The RLE strings are converted into binary masks using the COCO API. This process reverses the encoding process to obtain the original binary masks.

The decoding functions and an example of CSV decoding are available in the provided Jupyter notebook (for now here: Cervera Encoding and Decoding Notebook)


## Submission Format

### CSV Formatting and Submission

The submission CSV file should have the following columns:

ID: The unique identifier for the image (also called scene)

Width: The width of the image

Height: The height of the image

EncodedMasks: The encoded binary masks for the image (space-separated)

It is important to ensure that the submission CSV file has the correct formatting and that all required columns are present. Any inconsistencies in the formatting or missing columns may result in evaluation errors.

### Rules

The submission CSV file should not be empty, and it should have the exact number of lines as the test CSV file. This ensures that the submission contains predictions for all images in the test set.

If one prediction is empty, just leave the column 'EncodedMasks' empty but populate the 'ID', 'Width', and 'Height' columns. This indicates that there is no prediction for that particular image.

Ensure that the 'EncodedMasks' column contains only valid encoded mask strings. Invalid or corrupted encoded masks will result in errors during the evaluation process.

For best results, use the provided code to encode and decode the masks. This code ensures that the encoding and decoding processes are consistent and accurate.

Example Submission CSV:
ID	Width	Height	EncodedMasks
1	1920	1080	encodedMask1, encodedMask2, encodedMask3, ...
2	1920	1080	encodedMask1
3	1920	1080	encoded_mask_1, encodedMask2
...	...	...	...

En el siguiente repositorio público se puede encontrar un ejemplo de Notebook para generar el archivo CSV:
- https://github.com/premio-cervera-5r/5r_premio_cervera



&nbsp;
&nbsp;

El proyecto 5R está financiado por el Ministerio de Ciencia e Innovación a través del Centro para el Desarrollo Tecnológico Industrial (CDTI), número de contrato CER-20211007, en el marco del programa “Centros Tecnológicos de Excelencia Cervera”

<img src="./imgs/CDTI-logo.png" alt="LogoCDTI"/>