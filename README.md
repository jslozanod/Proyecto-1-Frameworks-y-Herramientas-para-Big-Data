# Proyecto Corte 1 - Frameworks y herramientas para Big Data

Grupo 3.

Data stack con Minio (data lake), Nessie + Iceberg (table format/catálogo), Clickhouse (data warehouse) y dlt para la ingesta, todo corriendo en docker. La fuente es el parquet de yellow taxi de enero 2025 que dio el profe en el pdf.

## Los 4 pipelines (notebooks/)

1. **1_http_to_bucket.ipynb** - baja el parquet de la fuente http y lo sube al bucket `taxis` de Minio.
2. **2_parquet_to_iceberg.ipynb** - lee ese parquet y lo guarda como tabla Iceberg en `my-bucket`, registrada en el catálogo de Nessie (namespace `taxis`).
3. **3_minio_to_azure.ipynb** - lee la tabla Iceberg y la sube a Azure, en la carpeta `GRUPO_3`.
4. **4_minio_to_clickhouse.ipynb** - lee la tabla Iceberg y la inserta en Clickhouse. Al final da 3.475.226 registros, que es el número que pide el parcial.

Se corren en ese orden porque cada uno depende de lo que deja el anterior.

## Cómo levantarlo

1. Tener Docker Desktop corriendo.
2. Copiar `notebooks/.dlt/secrets.toml.example` a `notebooks/.dlt/secrets.toml` y poner la key de Azure real (la del pdf del profe, no la subimos al repo por seguridad).
3. Levantar todo:
   ```
   docker compose up -d
   ```
   Esto levanta Jupyter (localhost:8890), Minio (localhost:9001, user/pass admin/password), Nessie (localhost:19120) y Clickhouse (localhost:8123).
4. Entrar a Jupyter y correr los 4 notebooks de la carpeta `notebooks/` en orden.

Ninguna librería está quemada en los notebooks, todo se instala solo desde `requirements.txt` cuando se levanta el contenedor de Jupyter (revisar el `command` del servicio `jupyter` en el `docker-compose.yml`).

## Estructura

```
docker-compose.yml
requirements.txt
notebooks/
  .dlt/
    secrets.toml.example   -> plantilla, copiar a secrets.toml
  1_http_to_bucket.ipynb
  2_parquet_to_iceberg.ipynb
  3_minio_to_azure.ipynb
  4_minio_to_clickhouse.ipynb
entregables/
  evidencias_proyecto_corte1.docx   -> pantallazos y consultas que pide el parcial
```
