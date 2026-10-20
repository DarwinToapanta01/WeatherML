# WeatherML

WeatherML es una aplicación web sencilla para entrenar un modelo que clasifica registros meteorológicos y predice el valor de una variable objetivo. Por defecto, esa columna se llama `rain`, aunque la idea central es que el usuario cargue sus propios datos en formato CSV.

El proyecto está pensado como una práctica de arquitectura de software. El procesamiento se divide en filtros pequeños, cada uno con una responsabilidad concreta: cargar los datos, comprobar que puedan utilizarse, prepararlos, entrenar el modelo y evaluar sus resultados.

## Cómo funciona

Desde la página principal se carga un archivo CSV. El servidor procesa el archivo y separa el 80 % de los registros para entrenamiento y el 20 % para evaluación. La división usa una semilla fija y mantiene la proporción de clases para que los resultados sean reproducibles y la prueba conserve una distribución similar a la del conjunto original.

Antes del entrenamiento, el programa elimina filas duplicadas y registros que no tienen un valor en la columna objetivo. Para las variables numéricas completa los datos faltantes con la mediana; para las categóricas, usa el valor más frecuente y convierte sus categorías a columnas numéricas. Luego entrena un clasificador Random Forest.

La aplicación presenta la exactitud, precisión, recall, F1 y la matriz de confusión, junto con la distribución de las clases. También permite introducir los valores de un registro para obtener una predicción individual. El modelo entrenado, sus nombres de variables y los resultados se guardan en `models/weather_model.joblib`.

## Estructura del proyecto

- `app.py`: configura Flask y define las rutas para cargar la página, entrenar el modelo y realizar predicciones.
- `filtros/carga_dataset.py`: lee el archivo CSV con pandas.
- `filtros/validar_dataset.py`: comprueba que el conjunto no esté vacío, que exista la variable objetivo y que haya al menos dos clases.
- `filtros/preparar_datos.py`: separa las variables predictoras de la variable objetivo y configura el tratamiento de datos numéricos y categóricos.
- `filtros/entrenar_modelo.py`: crea y entrena el pipeline con Random Forest.
- `filtros/evaluar_modelo.py`: calcula las métricas y la matriz de confusión usando los datos reservados para prueba.
- `templates/index.html`: contiene la interfaz para cargar datos, consultar métricas y pedir predicciones.
- `public/css/styles.css`: reúne los estilos de la aplicación.
- `models/weather_model.joblib`: ubicación donde se guarda el modelo después de entrenarlo.
- `requirements.txt`: lista las bibliotecas de Python que necesita la aplicación.

## Requisitos del CSV

El archivo debe ser un CSV legible por pandas y contener una columna objetivo llamada `rain`. Esa columna debe tener al menos dos clases distintas y registros válidos. Las demás columnas se usan como características predictoras; pueden ser numéricas o categóricas. Conviene que los nombres de las columnas y los tipos de datos sean consistentes con los valores que se utilizarán después para pedir predicciones.

## Instalación y ejecución

Se recomienda usar Python 3. Para instalar las dependencias y arrancar la aplicación desde la carpeta del proyecto:

```bash
python -m venv venv
```

En Windows, activa el entorno con:

```powershell
venv\Scripts\Activate.ps1
```

En macOS o Linux, utiliza:

```bash
source venv/bin/activate
```

Después instala las dependencias y ejecuta el servidor:

```bash
pip install -r requirements.txt
python app.py
```

Abre `http://127.0.0.1:5001` en el navegador. Al cargar un CSV válido, WeatherML entrena y guarda el modelo antes de mostrar los resultados.

## Dependencias

El archivo `requirements.txt` incluye Flask para el servidor web, pandas para leer y preparar los datos, scikit-learn para el preprocesamiento y el modelo, y joblib para guardar y cargar el modelo entrenado.
