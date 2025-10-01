# fastapi_semana_7-katerin_moya-3147253
Estudiante: Katerin Michel Moya Ardila
Ficha: 3147253
Dominio asignado: Escuela de Música Armonía – Cursos

1. Resumen rápido

Este proyecto consiste en la implementación de una API con FastAPI aplicada al dominio de la Escuela de Música Armonía – Cursos.
El objetivo principal es optimizar la consulta de datos mediante el uso de Redis como sistema de caché, documentar automáticamente la API y aplicar buenas prácticas en la definición de rutas.

En términos simples: se creó una API donde al consultar la información de un curso por su ID, la primera vez responde como si fuera desde base de datos, y las siguientes veces responde desde caché, reduciendo así el tiempo de respuesta y mejorando la eficiencia.

2. Marco teórico breve

FastAPI: Framework moderno de Python para construir APIs rápidas y eficientes.

Redis: Motor de base de datos en memoria usado como sistema de caché.

Caché: Técnica de almacenamiento temporal de datos para evitar consultas repetitivas a la base de datos.

Swagger (docs): Interfaz automática de documentación que facilita probar los endpoints.

3. Desarrollo de las prácticas
3.1 Optimización de consultas

Se simula la consulta a una base de datos. La primera vez que se solicita un curso, la API responde con "source": "db".

3.2 Uso de caché (Redis)

Se configuró Redis como sistema de caché. Al consultar el mismo curso otra vez, la API responde con "source": "cache", demostrando que no volvió a la base de datos.

3.3 Buenas prácticas de rutas

Se definió un endpoint principal / y otro específico /curso/{id}, cumpliendo con estándares REST y facilitando la documentación.

3.4 Documentación automática

FastAPI genera automáticamente la documentación en la ruta /docs, donde se pueden probar los endpoints sin necesidad de herramientas externas.

4. Código fuente principal
from fastapi import FastAPI
import redis.asyncio as redis

app = FastAPI(title="Escuela de Música Armonía - Cursos")

redis_client = None

@app.on_event("startup")
async def startup_event():
    global redis_client
    redis_client = redis.from_url("redis://localhost", decode_responses=True)

@app.get("/")
async def root():
    return {"message": "API de Escuela de Música Armonía funcionando"}

@app.get("/curso/{curso_id}")
async def get_curso(curso_id: int):
    cache_key = f"curso:{curso_id}"

    if await redis_client.exists(cache_key):
        return {"source": "cache", "data": await redis_client.get(cache_key)}

    curso_data = f"Curso {curso_id}: Guitarra Avanzada"
    await redis_client.set(cache_key, curso_data, ex=60)

    return {"source": "db", "data": curso_data}

Servidor FastAPI corriendo en consola (uvicorn main:app --reload).

Ejecución en Swagger /docs.

Resultado de la primera consulta (source: db).

Resultado de la segunda consulta (source: cache).

6. Resumen final

La implementación de esta práctica permitió aplicar los conceptos de optimización de consultas, uso de caché, buenas prácticas de rutas y documentación automática con Swagger en un entorno real.

Con este proyecto, se evidenció cómo FastAPI junto con Redis ofrece una solución eficiente para la gestión de datos, mejorando la velocidad de respuesta y optimizando recursos.

El trabajo desarrollado cumple con los objetivos planteados en la ficha, mostrando tanto la parte técnica (código funcional) como la parte de evidencias y explicación escrita.
