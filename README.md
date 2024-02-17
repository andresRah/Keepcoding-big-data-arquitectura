# Keepcoding-big-data-arquitectura
Keepcoding Big Data Arquitectura

# Brainstorm

- Sistema de recomendación de vuelos.
- Servicios web de recomendación de planes de viajes.
- Servicio web para recomendar destino de viajes con baja demanda.
- Salud: Detección de anomalías, examen de cáncer de mama.
- Predicción de demanda en cervecería.
- Servicio de notificaciones push para predecir picos de demanda en granja de aguacates.

# Diseño del DAaaS

## Estrategia DAaaS
Definir el catálogo de servicios que proporcionará la plataforma DAaaS que incluye incorporación de datos, limpieza de datos, transformación de datos, datapedias, bibliotecas de herramientas analíticas y otros.

## Idea Seleccionada

o	Servicio de notificaciones push para predecir picos de demanda en granja de aguacates.

La estrategia de DAaaS consistirá en recopilar y analizar datos en tiempo real de sensores en la granja, información histórica de producción de cosechas pasadas almacenados en sistemas propios, y datos externos de tiendas en línea así como de eventos especiales. El objetivo es hacer un pronóstico en las fluctuaciones de la demanda de aguacates, permitiendo optimizar producción, inventario y estrategias de precios.

# Arquitectura DAaaS

## Componentes

### 1) Adquisición de Datos de Sensores
- **Pub/Sub de Google Cloud**: Recepción de mensajes bajo protocolo MQTT para la lectura de los datos de los sensores.
- **Google Cloud Dataflow**: Procesamiento de streams de datos recibidos de los sensores, limpieza e ingesta en Google Cloud Bigtable y Dataproc.
- **Google Cloud Bigtable**: Almacenamiento de los datos procesados en una base de datos noSQL.

### 2) Adquisición de Datos mediante Carga Manual
- **Google Cloud DataFlow (ETL)**: Integración entre los sistemas de información propios (On-Premise) y la nube de Google.

### 3) Ejecución Manual de Crawler
- **Google Cloud Compute Engine**: Máquina virtual para ejecutar el crawler dirigido a páginas web de almacenes de cadena y tiendas, con el objetivo de obtener datos de aguacates (mercadona.es).

### 4) Almacenamiento de Documentos en Google Cloud Storage
- Almacenamiento del resultado de los procesos de adquisición de datos, incluyendo archivos generados y un dataset de Kaggle con información de consumo de productos por eventos relevantes.

## 5) Análisis con BigQuery
- **Google Cloud BigQuery**: Análisis y reportes de negocio posterior al procesamiento de datos.

## 6) Notificación Push
- **Google Cloud Pub/Sub**: Punto de salida conectado a Google DataProc - Proceso de forecasting.
- **Google Cloud Compute Engine**: Máquina virtual para procesar el resultado y formatear el mensaje de la notificación push además de determinar si se trata de un valor anómalo que genere la alerta.
- **Google Cloud App Engine**: Envío de la notificación push final a los usuarios.

## Inventario final de servicios en la nube
- Servicios de Google Cloud utilizados en la arquitectura: Pub/Sub, Dataflow, BigTable, Cloud Storage, BigQuery, y App Engine.

- 	**Google Cloud Pub/Sub Streaming** - Puntos para lectura de sensores.
-	**Google Cloud Dataflow** - Transaction streams.
-	**Cloud BigTable** - NoSQL Database.
-	**ETL Dataflow** - Ingesta e integración de datos de sistemas propios.
-	**Dataproc** - Análisis de la información proveniente de los procesos de adquisición de datos.
-	**Crawler** a páginas web de supermercados y tiendas en línea - Máquina virtual en Google Compute Engine (Página objetivo mercadona.es).
-	**Dataset de Kaggle**, información sobre eventos relevantes en el año (super Bowl, festividades, torneos, etc).
-	**Google Cloud Storage** - Almacenamiento de archivos resultantes de los procesos de adquisición de datos (Crawler, sistemas propios, sensores) y el dataset de kaggle.
-	**Google Cloud BigQuery** - Análisis de reportes y analiticas.
-	**Máquina virtual en Google Compute Engine**, formateo de mensaje push.
-	**Google Cloud Pub/Sub**, punto final de salida hacia la notificación push.
-	**Google App Engine** para notificaciones Push.

# DAaaS Operating Model Design and Rollout
Personalización de los modelos operativos DAaaS para adaptarse a los procesos y estructura organizacional del cliente. Incluye seguimiento de consumo y mecanismos de informe.

## **•	Transmisión de Datos de Sensores:** Los datos capturados por los sensores se transmitirán en tiempo real. No obstante, para optimizar el uso del ancho de banda y los recursos en la nube, se contempla la posibilidad de configurar envíos periódicos (diarios o semanales) que incluyan un promedio de las lecturas, basándose en su relevancia.

## **•	Integración de Sistemas Propios:** La integración de los sistemas operativos de la granja y el sistema contable con nuestra plataforma se llevará a cabo semanalmente mediante procesos ETL, garantizando una sincronización eficiente y actualizada de los datos.

## **•	Ejecución del Crawler:** El crawler se programará para ejecutarse diariamente, asegurando la recopilación continua de datos relevantes del mercado y tendencias de precios de aguacates.

## **•	Procesamiento de Datos y Generación de Pronósticos:** Al concluir el procesamiento de datos y la generación de pronósticos por parte de Google Cloud Dataproc, se activarán automáticamente tres tareas críticas:

### **1.    Almacenamiento de Datos en CSV:** Los datos procesados se guardarán en formato CSV en Google Cloud Storage tras cada ciclo de procesamiento, lo cual se realizará diariamente.

### **2.	Almacenamiento de Pronósticos en BigQuery:** Los pronósticos generados se almacenarán en Google BigQuery siguiendo la misma frecuencia diaria para facilitar el acceso y análisis.

### **3.	Envío de Notificaciones Push:** Una máquina virtual en Google Compute Engine evaluará los datos procesados en busca de anomalías o picos en la demanda de aguacates. Si se identifica una tendencia significativa, se enviará una notificación push a través de Google App Engine, sin una frecuencia predefinida, ya que dependerá de la detección de eventos críticos.

# Desarrollo de la Plataforma DAaaS
Se adjunta un desarrollo básico de crawler escrito en Python a la página de mercadona.es

```python
import requests
from bs4 import BeautifulSoup

def main():
    url = 'https://www.mercadona.es/es'
    response = requests.get(url)

    if response.status_code == 200:
        soup = BeautifulSoup(response.content, 'html.parser')
        products = soup.find_all('div', class_='product')
        
        avocado_products = [product.find('span', class_='title').text
                            for product in products
                            if 'aguacate' in product.find('span', class_='title').text.lower()]

        if avocado_products:
            print("Productos relacionados con aguacates en Mercadona:")
            for product in avocado_products:
                print("-", product)
        else:
            print("No se encontraron productos relacionados con aguacates en Mercadona.")
    else:
        print("Error al conectar con la página de Mercadona.")

if __name__ == "__main__":
    main()
```

```python
pip install requests
pip install beautifulsoup4
```

## Diagrama
Se adjunta un diagrama en formato PNG que ilustra el flujo de datos y las interacciones entre los componentes de la arquitectura DAaaS.

![Architecture drawio](https://github.com/andresRah/Keepcoding-big-data-arquitectura/assets/10521199/baf9de0f-d081-43b9-8035-919e2ea589fc)

