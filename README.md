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
Servicio de notificaciones push para predecir picos de demanda en granja de aguacates. La estrategia de DAaaS consistirá en recopilar y analizar datos en tiempo real de sensores en la granja, información histórica de producción de cosechas pasadas almacenados en sistemas propios, y datos externos de tiendas en línea así como de eventos especiales. El objetivo es hacer un pronóstico en las fluctuaciones de la demanda de aguacates, permitiendo optimizar producción, inventario y estrategias de precios.

# Arquitectura DAaaS

## Componentes

### Adquisición de Datos de Sensores
- **Pub/Sub de Google Cloud**: Recepción de mensajes bajo protocolo MQTT para la lectura de los datos de los sensores.
- **Google Cloud Dataflow**: Procesamiento de streams de datos recibidos de los sensores, limpieza e ingesta en Google Cloud Bigtable y Dataproc.
- **Google Cloud Bigtable**: Almacenamiento de los datos procesados en una base de datos noSQL.

### Adquisición de Datos mediante Carga Manual
- **Google Cloud DataFlow (ETL)**: Integración entre los sistemas de información propios (On-Premise) y la nube de Google.

### Ejecución Manual de Crawler
- **Google Cloud Compute Engine**: Máquina virtual para ejecutar el crawler dirigido a páginas web de almacenes de cadena y tiendas, con el objetivo de obtener datos de aguacates.

### Almacenamiento de Documentos en Google Cloud Storage
- Almacenamiento del resultado de los procesos de adquisición de datos, incluyendo archivos generados y un dataset de Kaggle con información de consumo de productos por eventos relevantes.

## Análisis con BigQuery
- **Google Cloud BigQuery**: Análisis y reportes de negocio posterior al procesamiento de datos.

## Notificación Push
- **Google Cloud Pub/Sub**: Punto final de salida con el proceso de forecasting.
- **Google Cloud Compute Engine**: Máquina virtual para procesar el resultado y formatear el mensaje de la notificación push.
- **Google Cloud App Engine**: Envío de la notificación push final a los usuarios.

## Inventario final de servicios en la nube
- Detalle de los servicios de Google Cloud utilizados como Pub/Sub, Dataflow, BigTable, Storage, BigQuery, y App Engine, especificando su función dentro de la arquitectura.

# DAaaS Operating Model Design and Rollout
Personalización de los modelos operativos DAaaS para adaptarse a los procesos y estructura organizacional del cliente. Incluye seguimiento de consumo y mecanismos de informe.

# Desarrollo de la Plataforma DAaaS
Inclusión de un ejercicio básico de crawler en Python dirigido a la página de Mercadona para extracción de información relacionada con aguacates.

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

## Diagrama
Se adjunta un diagrama en formato PNG que ilustra el flujo de datos y las interacciones entre los componentes de la arquitectura DAaaS.

![Architecture drawio](https://github.com/andresRah/Keepcoding-big-data-arquitectura/assets/10521199/baf9de0f-d081-43b9-8035-919e2ea589fc)

