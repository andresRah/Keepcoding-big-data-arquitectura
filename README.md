# Keepcoding-big-data-arquitectura
Keepcoding Big Data Arquitectura

# Arquitectura DAaaS

## Definición
Definir la selección de componentes, la definición de procesos de ingeniería y el diseño de interfaces de usuario. Diseño y ejecución de Proofs-of-Concept (PoC) para demostrar la viabilidad del enfoque DAaaS.

## Componentes

### Adquisición de datos
- **Sensores**: Utilización del servicio de Pub/Sub de Google Cloud como puntos de recepción de mensajes bajo protocolo de comunicación liviano MQTT para la lectura de los datos de los sensores.
- **Procesamiento de datos**: Servicio Google Cloud Dataflow para procesamiento de streams de datos recibidos de los sensores, limpieza de los datos e ingesta de estos en Google Cloud Bigtable y Dataproc.
- **Almacenamiento de datos procesados**: Servicio Google Cloud Bigtable para el almacenamiento de los datos procesados provenientes de los sensores en una base de datos noSQL, dado que la integridad referencial no es un requisito obligatorio para el almacenamiento de los datos.
- **Carga manual de sistemas de información propios**: Integración de sistemas propios con la nube de Google a través de Google Cloud DataFlow para la clasificación y envío de la información a Google Cloud Dataproc.
- **Ejecución manual de Crawler**: Uso de Google Cloud Compute Engine para ejecutar un crawler que recopila información de precios de aguacates en páginas web de supermercados y tiendas, estimando el stock.

### Almacenamiento
- **Google Cloud Storage**: Almacenamiento del resultado de los procesos de adquisición de datos, incluyendo archivos generados por los procesos de adquisición de datos y un dataset de Kaggle con información de consumo de productos por eventos relevantes.

### Análisis con BigQuery
- **Google Cloud BigQuery**: Análisis y reportes de negocio posterior al procesamiento de datos, con el objetivo de quitar carga al sistema general.

### Notificación push
- **Google Cloud Pub/Sub y Google Cloud Compute Engine**: Procesamiento del resultado y formateo del mensaje que se enviará como notificación push a través de Google Cloud App Engine.

## Brainstorm
- Sistema de recomendación de vuelos.
- Servicios web de recomendación de planes de viaje.
- Servicio web para recomendar destinos de viajes con baja demanda.
- Detección de anomalías en salud, como el examen de cáncer de mama.
- Predicción de demanda en cervecería.
- Servicio de notificaciones push para predecir picos de demanda en granja de aguacates.

## Diseño del DAaaS
Definición de la estrategia del DAaaS, incluyendo la incorporación de datos, limpieza, transformación, datapedias, bibliotecas de herramientas analíticas, y otros.

### Idea Seleccionada
Servicio de notificaciones push para predecir picos de demanda en granja de aguacates, basado en la recopilación y análisis de datos en tiempo real de sensores en la granja, información histórica de producción de cosechas pasadas, y datos externos de tiendas en línea y eventos especiales.

## DAaaS Operating Model Design and Rollout
Personalización de modelos operativos DAaaS para adaptarse a los procesos, estructura organizacional, reglas y gobernanza de los clientes individuales. Incluye seguimiento de consumo y mecanismos de informe.

## Desarrollo de la plataforma DAaaS
Incluye un ejercicio básico de crawler escrito en Python dirigido a la página de Mercadona para la extracción de información relacionada con aguacates.

## Diagrama
Se adjunta un diagrama en formato PNG que ilustra el flujo de datos y las interacciones entre los componentes de la arquitectura DAaaS.
