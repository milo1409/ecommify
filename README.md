# Ecommify - Diseño de esquemas flexibles en MongoDB

## Descripción del proyecto

Este repositorio contiene el desarrollo de la **Etapa 1: Estructuración. Diseño de esquemas flexibles en MongoDB**, correspondiente al caso de estudio **Ecommify**.

Ecommify es una plataforma de comercio electrónico híbrida diseñada bajo una arquitectura de microservicios y arquitectura orientada a eventos. En esta solución, **PostgreSQL** se utiliza para procesos críticos del negocio como usuarios, órdenes, pagos e inventario, mientras que **MongoDB** se emplea para componentes con alta flexibilidad estructural y alto volumen de datos, como catálogo extendido, comportamiento de usuario, recomendaciones, reseñas y logs de búsqueda.

El objetivo principal de esta actividad es diseñar e implementar un esquema flexible para la colección `product_catalog`, aplicando patrones de modelado documental y validando el diseño mediante inserción de datos, consultas y aggregation pipelines.

---

## Autores

* Daniel Porras
* Oscar Clavijo
* Camilo Porras

---

## Tecnologías utilizadas

* MongoDB Atlas
* Google Colab
* Python 3
* PyMongo
* MongoDB NoSQL
* Jupyter Notebook

---

## Archivos del repositorio

```text
.
├── Mongodb_ecommify.ipynb
├── Documento Tecnico Ecommify Actividad 3.pdf
└── README.md
```

### Descripción de archivos

| Archivo                                      | Descripción                                                                                                         |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| `Mongodb_ecommify.ipynb`                     | Notebook principal con conexión a MongoDB Atlas, creación de colecciones, inserción de datos, consultas y pipelines |
| `Documento Tecnico Ecommify Actividad 3.pdf` | Documento técnico del entregable de la Etapa 1                                                                      |
| `README.md`                                  | Descripción general del proyecto y guía de ejecución                                                                |

---

## Objetivos de la actividad

* Configurar un ambiente de trabajo en MongoDB Atlas.
* Conectarse a MongoDB desde Google Colab usando `pymongo`.
* Diseñar una colección flexible llamada `product_catalog`.
* Modelar productos con atributos variables según su categoría.
* Aplicar decisiones de modelado entre embedding y referencing.
* Implementar patrones de modelado documental.
* Insertar mínimo 1000 documentos de prueba.
* Crear una colección externa de reseñas llamada `product_reviews`.
* Ejecutar consultas de validación.
* Implementar aggregation pipelines.
* Crear índices recomendados para mejorar rendimiento.

---

## Configuración del entorno

### 1. Crear cluster en MongoDB Atlas

Para ejecutar el proyecto se requiere una cuenta en MongoDB Atlas y un cluster gratuito M0.

Pasos generales:

1. Crear cuenta en MongoDB Atlas.
2. Crear un proyecto llamado `Ecommify`.
3. Crear un cluster gratuito M0.
4. Configurar usuario de base de datos desde **Database Access**.
5. Configurar acceso de red desde **Network Access**.
6. Obtener el connection string del cluster.
7. Usar Google Colab para ejecutar el notebook.

---

## Instalación de dependencias

En Google Colab o Jupyter Notebook se debe instalar `pymongo`:

```python
!pip install pymongo
```

También puede utilizarse la variante con soporte SRV:

```python
!pip install "pymongo[srv]"
```

---

## Conexión a MongoDB Atlas

El notebook establece conexión con MongoDB Atlas mediante `MongoClient`.

Ejemplo de conexión:

```python
from pymongo import MongoClient
from pymongo.server_api import ServerApi

uri = "mongodb+srv://USUARIO:CONTRASEÑA@cluster.mongodb.net/?appName=Ecommify"

client = MongoClient(uri, server_api=ServerApi('1'))

try:
    client.admin.command('ping')
    print("Estas conectado a mongo ecommify !!!")
except Exception as e:
    print(e)
```

> **Nota de seguridad:** no se recomienda subir credenciales reales al repositorio. El connection string debe manejarse mediante variables de entorno o archivos `.env` excluidos del control de versiones.

---

## Base de datos utilizada

```python
db = client["ecommify_db"]
```

La base de datos principal del ejercicio se llama:

```text
ecommify_db
```

---

## Colecciones implementadas

### 1. `product_catalog`

Colección principal del catálogo extendido de productos.

Esta colección permite almacenar productos con campos comunes y atributos flexibles según la categoría.

Campos principales:

| Campo            | Descripción                                 |
| ---------------- | ------------------------------------------- |
| `_id`            | Identificador único del producto            |
| `name`           | Nombre del producto                         |
| `description`    | Descripción del producto                    |
| `category`       | Categoría principal                         |
| `price`          | Precio                                      |
| `currency`       | Moneda                                      |
| `seller_id`      | Identificador del vendedor                  |
| `seller_region`  | Región del vendedor                         |
| `attributes`     | Atributos variables según categoría         |
| `tags`           | Etiquetas de búsqueda                       |
| `images`         | Imágenes del producto                       |
| `ratings`        | Calificación promedio y cantidad de reseñas |
| `metrics`        | Métricas calculadas                         |
| `recent_reviews` | Subconjunto de reseñas recientes            |
| `created_at`     | Fecha de creación                           |
| `updated_at`     | Fecha de actualización                      |

---

### 2. `product_reviews`

Colección externa utilizada para almacenar las reseñas completas de los productos.

Campos principales:

| Campo               | Descripción                        |
| ------------------- | ---------------------------------- |
| `_id`               | Identificador único de la reseña   |
| `product_id`        | Referencia al producto             |
| `user_id`           | Identificador del usuario          |
| `rating`            | Calificación                       |
| `comment`           | Comentario del usuario             |
| `verified_purchase` | Indica si la compra fue verificada |
| `created_at`        | Fecha de creación                  |

---

## Ejemplo de documento en `product_catalog`

```json
{
  "_id": "PROD-1001",
  "name": "Laptop Gamer",
  "description": "Laptop de alto rendimiento para videojuegos, diseño y tareas intensivas.",
  "category": "Tecnología",
  "price": 4500,
  "currency": "USD",
  "seller_id": "SELLER-001",
  "seller_region": "CO-BOG",
  "attributes": {
    "ram": "32GB",
    "cpu": "Intel i9",
    "gpu": "RTX 4080",
    "storage": "1TB SSD",
    "screen": "17 inches"
  },
  "tags": [
    "gaming",
    "high-performance",
    "laptop",
    "tecnologia"
  ],
  "images": [
    "img_laptop_1.jpg",
    "img_laptop_2.jpg"
  ],
  "ratings": {
    "average": 4.8,
    "count": 500
  },
  "metrics": {
    "total_units_sold": 1200,
    "views_count": 25000,
    "conversion_rate": 0.08
  },
  "recent_reviews": [
    {
      "user_id": 1001,
      "rating": 5,
      "comment": "Excelente rendimiento para videojuegos."
    }
  ],
  "created_at": "2026-05-25T10:00:00",
  "updated_at": "2026-05-25T10:00:00"
}
```

---

## Patrones de modelado aplicados

### Computed Pattern

Se utiliza para almacenar métricas precalculadas dentro del producto, como:

* `ratings.average`
* `ratings.count`
* `metrics.total_units_sold`
* `metrics.views_count`
* `metrics.conversion_rate`

Este patrón evita recalcular constantemente valores que se consultan con alta frecuencia.

---

### Subset Pattern

Se aplica mediante el campo `recent_reviews`, donde se almacena solo un subconjunto de reseñas recientes o destacadas.

Las reseñas completas se almacenan en la colección `product_reviews`.

---

### Approximation Pattern

Se aplica para métricas que no requieren exactitud absoluta en tiempo real, como:

* `views_count`
* `conversion_rate`
* métricas de popularidad

Estas métricas pueden actualizarse por lotes.

---

### Polymorphic Pattern

Se utiliza porque todos los productos se almacenan en la misma colección, pero sus atributos cambian según la categoría.

Ejemplos:

* Tecnología: `ram`, `cpu`, `gpu`, `storage`
* Ropa: `size`, `color`, `material`
* Libros: `author`, `language`, `format`

---

### Bucket Pattern

Se propone para la colección `user_behavior`, agrupando eventos históricos por usuario y periodo de tiempo.

---

## Inserción de datos

El notebook genera e inserta:

* **1000 productos** en la colección `product_catalog`.
* **500 reseñas** en la colección `product_reviews`.

Ejemplo de resultado esperado:

```text
Total productos insertados: 1000
Total reseñas insertadas: 500
```

---

## Consultas de validación

El notebook incluye consultas para validar el modelo de datos.

### Productos por categoría

```python
for product in product_catalog.find({"category": "Tecnología"}).limit(5):
    print(product)
```

### Productos por precio

```python
for product in product_catalog.find({"price": {"$gte": 1000}}).limit(5):
    print(product["_id"], product["name"], product["price"])
```

### Productos por atributo específico

```python
for product in product_catalog.find({"attributes.ram": "32GB"}).limit(5):
    print(product["_id"], product["name"], product["attributes"])
```

### Productos por etiqueta

```python
for product in product_catalog.find({"tags": "tecnología"}).limit(5):
    print(product["_id"], product["name"], product["tags"])
```

---

## Aggregation pipelines

### Promedio de precio por categoría

```python
pipeline = [
    {
        "$group": {
            "_id": "$category",
            "average_price": {"$avg": "$price"},
            "total_products": {"$sum": 1}
        }
    },
    {
        "$sort": {"average_price": -1}
    }
]

for result in product_catalog.aggregate(pipeline):
    print(result)
```

### Top 10 productos más vendidos

```python
for product in product_catalog.find().sort("metrics.total_units_sold", -1).limit(10):
    print(product["_id"], product["name"], product["metrics"]["total_units_sold"])
```

### Productos con mejor calificación

```python
for product in product_catalog.find({"ratings.count": {"$gte": 50}}).sort("ratings.average", -1).limit(10):
    print(product["_id"], product["name"], product["ratings"]["average"])
```

### Cantidad de productos por región del vendedor

```python
pipeline = [
    {
        "$group": {
            "_id": "$seller_region",
            "total_products": {"$sum": 1}
        }
    },
    {
        "$sort": {"total_products": -1}
    }
]

for result in product_catalog.aggregate(pipeline):
    print(result)
```

---

## Índices recomendados

Para mejorar el rendimiento de las consultas frecuentes, se crean los siguientes índices:

```python
product_catalog.create_index({"category": 1})
product_catalog.create_index({"price": 1})
product_catalog.create_index({"tags": 1})
product_catalog.create_index({"seller_id": 1})
product_catalog.create_index({"seller_region": 1})
product_catalog.create_index({"ratings.average": -1})
product_catalog.create_index({"metrics.total_units_sold": -1})
product_catalog.create_index({"attributes.ram": 1})
product_reviews.create_index({"product_id": 1})
```

### Justificación de índices

| Índice                     | Justificación                            |
| -------------------------- | ---------------------------------------- |
| `category`                 | Mejora consultas por categoría           |
| `price`                    | Optimiza filtros por precio              |
| `tags`                     | Mejora búsquedas por etiquetas           |
| `seller_id`                | Permite consultar productos por vendedor |
| `seller_region`            | Permite análisis regional                |
| `ratings.average`          | Permite ordenar por calificación         |
| `metrics.total_units_sold` | Permite ordenar por ventas               |
| `attributes.ram`           | Optimiza búsquedas por atributo dinámico |
| `product_id`               | Permite consultar reseñas por producto   |

---

## Decisiones de modelado

### Embedding

Se utiliza embedding para información que se consulta frecuentemente junto con el producto:

* `attributes`
* `tags`
* `images`
* `ratings`
* `metrics`
* `recent_reviews`

### Referencing

Se utiliza referencing para información que puede crecer demasiado o pertenece a otros dominios:

* reseñas completas en `product_reviews`
* vendedores en `sellers`
* usuarios en PostgreSQL o servicio de usuarios
* comportamiento de usuario en `user_behavior`
* recomendaciones en `recommendations`

---

## Ventajas del diseño

* Permite manejar productos con atributos variables.
* Reduce rigidez frente a cambios en el catálogo.
* Mejora rendimiento de lectura mediante datos embebidos.
* Evita crecimiento excesivo del documento mediante referencias.
* Soporta consultas por categoría, precio, etiquetas, región y métricas.
* Aplica patrones documentales especializados.
* Se alinea con la arquitectura híbrida de Ecommify.
* Facilita integración con analítica y recomendaciones.

---

## Trade-offs

| Decisión                      | Beneficio                    | Riesgo                             | Mitigación                  |
| ----------------------------- | ---------------------------- | ---------------------------------- | --------------------------- |
| Usar `attributes` flexible    | Permite categorías variables | Puede generar datos inconsistentes | Validar desde la aplicación |
| Embeber `ratings` y `metrics` | Mejora lectura               | Requiere actualización periódica   | Actualización por eventos   |
| Embeber `recent_reviews`      | Mejora detalle de producto   | No contiene todas las reseñas      | Consultar `product_reviews` |
| Referenciar reseñas completas | Evita documentos grandes     | Requiere consulta adicional        | Índice por `product_id`     |
| Usar métricas aproximadas     | Reduce escrituras            | Puede perder exactitud             | Recalcular por lotes        |

---

## Ejecución del notebook

Para ejecutar el proyecto:

1. Abrir `Mongodb_ecommify.ipynb` en Google Colab.
2. Instalar dependencias.
3. Configurar el connection string de MongoDB Atlas.
4. Ejecutar la celda de conexión.
5. Ejecutar la generación de productos.
6. Ejecutar la generación de reseñas.
7. Ejecutar consultas de validación.
8. Ejecutar aggregation pipelines.
9. Crear índices recomendados.

---

## Seguridad

No se deben publicar credenciales reales en GitHub.

Se recomienda usar variables de entorno:

```python
import os

uri = os.getenv("MONGODB_URI")
client = MongoClient(uri)
```

Y agregar un archivo `.gitignore` con:

```text
.env
*.env
__pycache__/
.ipynb_checkpoints/
```

---

## Conclusiones

MongoDB es una alternativa adecuada para el catálogo extendido de Ecommify porque permite trabajar con documentos flexibles y atributos variables por categoría.

El diseño de la colección `product_catalog` combina campos comunes con un subdocumento flexible llamado `attributes`, lo que facilita representar productos tecnológicos, ropa, artículos del hogar, productos deportivos y libros dentro de una misma colección.

La estrategia de modelado combina embedding y referencing. Se embebe la información consultada frecuentemente y se referencian entidades de alto crecimiento, como reseñas completas y eventos de comportamiento de usuario.

Los patrones Computed Pattern, Subset Pattern, Approximation Pattern, Polymorphic Pattern y Bucket Pattern permiten optimizar el rendimiento, controlar el crecimiento de documentos y soportar datos heterogéneos.

Las consultas y pipelines implementados validan que el esquema soporta requisitos funcionales del catálogo, como búsqueda por categoría, filtros por precio, consulta de atributos variables, ranking por ventas, ranking por calificación y análisis por región del vendedor.

---

## Referencias

* MongoDB, Inc. Data Modeling Patterns. MongoDB Manual.
* MongoDB, Inc. Sharding. MongoDB Manual.
* Documento Técnico de Diseño – Ecommify.

