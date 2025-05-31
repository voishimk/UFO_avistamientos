# Informe Big Data Evaluación N°2

* Integrantes: Valentina Guajardo, Giovanna Espinosa, Diego Palmeiro
* Seccion : BIY7131-001

---
 ## Introducción
> En el presente informe se documenta el proceso de carga, preparación, análisis y visualización de datos referente a los avistamientos de OVNI en  Estados Unidos, para ello utilizamos herramientas de Google Cloud Platform: **Cloud Storage**, **Cloud Dataprep**, **BigQuery** y **Looker Studio**.

> El dataset ufo_sighting, nos presenta datos históricos sobre avistamientos de ovnis, en los datos encontramos información como Fecha y hora, ciudad, estado, país, Forma del ovni, duración del avistamiento en segundos, descripcion, fecha de documentacion, latitud y longitud de la ubicacion del avistamiento. Este dataset posee 11 variables (columnas) y 80333 observaciones (filas).
---
## Desarrollo 
> Para el desarrollo de este informe, se utilizó las instancias de Google Cloud Skill Boosts, utilizando el laboratorio GSP823 para poder utilizar las herramientas de Google Cloud Platform.

### Carga de datos

##### Cloud Storage:
> Iniciada la instancia, nos dirigimos a **Cloud Storage** para la creación de un Bucket de nombre ´palmeiro-espinosa-guajardo´ el cual utilizaremos como un contenedor de almacenamiento, una vez creado el bucket cargaremos el dataset ´údo_sightligh.csv´ para  su posterior análisis. Además de habilitar las APIs de Cloudy BigQuery 

>![image](https://github.com/user-attachments/assets/084de36d-3975-4b78-977a-1f9f3e8db00b)
>![image1](https://github.com/user-attachments/assets/44917231-68cf-4bd0-be9e-c83882cc3a0c)

##### Big Query
> Nos dirigimos a BigQuery para la creación del dataset ´óvni´ en el cual posteriormente almacenaremos los datos ya procesados.
> ![image2](https://github.com/user-attachments/assets/e4278c11-eb4e-48d7-a3c8-20f7670fc2c5)

### Preparación de los datos.
#### Cloud DataPrep

> Eligiendo en views all products desde el navegador de Cloud Platform, en la seccion Analitics con ello entramos a Alteryx Designer Cloud, para ingresar a la interfaz de Data Prep.
> Una vez ingresado en Data Prep, seleccionamos "Create a new Flow" para crear un flujo de datos, al cual llamaremos AvistamientosUFO y seleccionamos añadir dataset, el cual mediante la conexión de Cloud Storage, buscamos el csv previamente cargado en nuestro bucket, al cual seleccionamos e importamos y añadimos al Flujo.
> ![image](https://github.com/user-attachments/assets/ba6df312-996b-45ce-aa0a-b4541ae673d0)
> ![image](https://github.com/user-attachments/assets/8b1b9d8b-c8f8-40a7-a114-ebe1a419225b)
> ![image](https://github.com/user-attachments/assets/e1930de3-ff00-466e-adfe-6be8836eea1a)

>Para la preparación de los datos realizamos un Edit Recipe, lo cual nos permitira preparar los datos para su análisis. Al seleccionar el Edit recipe, se nos visualizara los datos de dataset, en el cual podemos encontrar todos los datos organizados en columnas y filas.

>  Los Recipe, nos permite fácilmente poder tratar los datos de forma simple datos como son los missing value, los mismatching data y los datos duplicados y conversion de los datos. Para tratar estos datos se realizo lo siguiente por columna.
 > * Date_time: Replace missing values with the last valid value.
 > * state/province: Replace mismatched values with Null, Replace missing values with the las valid value, convert text to lowercase.
 > * Country: Replace missing values with the last valid value, convert text to lowercase
 > * UFO_Shape: Replace missing values with "unknown", convert text to lowercase
 > * length_of_encounter_seconds: Delete rows where is missing.
 > * Description: Delete rows where is missing.
 > * Latitude: Delete rows where is missing.
 > * Remove duplicate rows.

![image](https://github.com/user-attachments/assets/860eca20-413e-4169-b056-54ae6138f343)

Finalmente nos encontramos con un dataset con el tratamiento de nulos, mismatched value, duplicacion de datos el cual se visualiza de la siguiente manera.

![image3](https://github.com/user-attachments/assets/27e9bd68-1c8a-4cf4-a98a-dc6e1b720db9)

Seguidamente ejecutamos las transformaciones realizadas y publicamos este dataset tratado en el dataset creado anteriormente en BigQuery. Se espera a su carga y publicación para seguir con el siguiente paso.
> ![image5](https://github.com/user-attachments/assets/81743d2e-f57d-48eb-a43d-6535dc8f8698)

#### Big Query
> Una vez ejecutado las transformaciones en Dataprep, nos dirigimos a BigQuery el cual contiene el dataset limpio y podemos hacer las consultas necesarias.
> ![image](https://github.com/user-attachments/assets/8094480a-2776-4ef9-8aa9-d91a8721c180)

> Realizamos las consultas SQL en Big Query para el análisis del dataset.

**Esta consulta nos muestra la cantidad de avistamientos de ovnis según su forma**
```sql
SELECT 
  UFO_shape,
  COUNT(*) AS Sightings
FROM 
  `qwiklabs-gcp-03.ovni.avistamientos`
WHERE 
  country = 'us'
GROUP BY 
  UFO_shape
ORDER BY 
  Sightings DESC
LIMIT 5;
```

**La consulta nos cuantifica la cantidad de avistamientos por año**
```sql

SELECT 
  EXTRACT(YEAR FROM DATETIME(DateTime)) AS Year,
  COUNT(*) AS Sightings
FROM `qwiklabs-gcp-03.ovni.avistamientos`
WHERE 
  country = 'us'
GROUP BY 
  Year
ORDER BY 
  Sightings DESC;
```
** La ultima consulta nos muestra la cantidad de avistamientos según el estado o provicia en el que fue visto**

```sql
 SELECT 
  `state¨Province` AS State,
  COUNT(*) AS Sightings
FROM 
 `qwiklabs-gcp-03.ovni.avistamientos`
WHERE 
  country = 'us'
GROUP BY 
  State
ORDER BY 
  Sightings DESC;
```
Se ejecutan las Consultas y observamos los resultados obtenidos en cada una.
> ![image](https://github.com/user-attachments/assets/516cd4ea-67a9-406f-9e7e-f22022de1264)

1ra consulta ( Forma del ovni)

> ![image](https://github.com/user-attachments/assets/ac267bd0-4e57-4ecf-95cd-76fd8dc16f1a)

2da consulta ( Avistamientos por año)
> ![image](https://github.com/user-attachments/assets/cb0ac99b-38e1-4a2e-ad4e-defa77758264)

3ra consulta ( Estado o provincia de avistamiento)
> ![image](https://github.com/user-attachments/assets/5aee840d-c669-4db9-afaa-927c1aeda133)

### Visualización de los datos.
#### Looker Studio

> Realizadas las consultas en BigQuery, visualizamos los resultados directamente en Looker Studio.

##### Cantidad de Avistamientos segun su Forma
![1](https://github.com/user-attachments/assets/43ecb5a4-1440-46d4-ad2d-c9364ec8cf6f)

> En este grafio podemos observar que un 37.7% de los avistamientos tienen una forma  Light un 18.1% con una forma triangular, un 17.1% de forma circular, un 14.3% con forma de Fireball y un 12,8% de los datos describen una forma desconocida.

##### Cantidad de Avistamientos segun el año

![2](https://github.com/user-attachments/assets/c5a6619c-87f5-4e2b-85cb-c2730b24694e)

> Este gráfico nos muestra la cantidad de avistamientos por años ordenados en forma descendente, siendo el año 2013 donde mas avistamientos se contabilizan con un 32159 observaciones seguido del año 2014 con un 13286 avistamientos, continuando con otros años  de los 2000's. 

##### Cantidad de Avistamientos segun el estado provincia de EEUU

![3](https://github.com/user-attachments/assets/1709b8e7-d105-498e-89d8-206b66a75c1c)

> Este gráfico nos muestra de manera geografica, los avistamientos en EEUU , siendo el estado de california el que mas observaciones registra con 10127 casos seguido de Washingtoncon 4429 casos, seguido de otros estados los cuales tienen menor cantidad de avistamientos, los cuales se distribuyen en las provincias de EEUU.
---

## Conclusión   

> A través de este trabajo pudimos aplicar de manera práctica distintas herramientas de Google Cloud Platform para trabajar con un dataset real relacionado a los avistamientos de OVNIs en Estados Unidos. Desde la carga de los datos en Cloud Storage, pasando por su preparación en Cloud Dataprep, el análisis en BigQuery y la visualización en Looker Studio, logramos completar un flujo de trabajo completo de Big Data.

> Durante el proceso, fue posible identificar y tratar datos nulos, inconsistencias y valores faltantes, dejando el dataset listo para su análisis. Con las consultas realizadas en BigQuery obtuvimos información interesante, como las formas más comunes de los OVNIs reportados, los años con más avistamientos y los estados donde se registraron más casos. Además, al visualizar estos resultados en Looker Studio, pudimos interpretarlos de manera clara y ordenada.

> Este proyecto nos permitió reforzar conocimientos sobre el uso de herramientas cloud para el análisis de datos a gran escala y nos demostró la importancia de una correcta limpieza y preparación de datos antes de cualquier análisis. Fue una experiencia completa que integró distintas etapas del ciclo de vida de los datos, desde su carga hasta su visualización final.
