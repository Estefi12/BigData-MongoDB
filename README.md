# BigData-MongoDB
## Diseño de base de datos en MongoDB
El proyecto utiliza MongoDB, una base de datos NoSQL orientada a documentos, ideal para trabajar con el conjunto de datos de Accidentes Viales (tipo Logs de Eventos). La base de datos se llama AnalisisVialBD y contiene una única colección principal: accidentes.

## Implementación de Consultas: CRUD y Filtros
La interacción con la base de datos se realiza a través de las operaciones CRUD y consultas complejas.
### Consultas Básicas (CRUD)
La Inserción (Create) se demostró mediante db.accidentes.insertOne(), asegurando que cada nuevo registro (como un accidente reciente) se conforme al esquema diseñado.

 <img width="793" height="469" alt="image" src="https://github.com/user-attachments/assets/955146bb-4125-4f45-b265-857b57e01e09" />

La Selección (Read) básica utiliza db.accidentes.find(), donde se puede filtrar fácilmente por campos anidados como "ubicacion.barrio".

 <img width="791" height="639" alt="image" src="https://github.com/user-attachments/assets/d30916db-663b-4bb9-8f53-2ee2355184d3" />

Para la Actualización (Update), se utilizó db.accidentes.updateOne() junto al operador $set; este operador es vital porque solo modifica los campos especificados (ej. involucrados.vehiculos), dejando el resto del documento intacto.
<img width="921" height="555" alt="image" src="https://github.com/user-attachments/assets/48f8a80c-ff06-47eb-ae6a-60a0fbcffca7" />

 
Finalmente, la Eliminación (Delete) se realiza con db.accidentes.deleteOne(), utilizando un criterio (como codigoAccidente) para remover un registro específico de manera segura.
<img width="921" height="226" alt="image" src="https://github.com/user-attachments/assets/aed3622a-5324-4be3-bcb4-11bf6242447e" />

 
## Consultas con Filtros y Operadores

Para demostrar la capacidad del lenguaje de consulta de MongoDB, se implementaron filtros avanzados. El uso de Operadores de Comparación como $gt (mayor que) permite encontrar eventos de alta gravedad, como accidentes con más de un herido ("involucrados.heridos": { $gt: 1 }). Se aplicaron Operadores Lógicos como $or para realizar búsquedas complejas, encontrando registros que cumplen una de dos condiciones (ej. "tipo de accidente es ATROPELLO O el barrio es 'X'"). Adicionalmente, se usó el operador $in para filtrar documentos cuyos campos coinciden con cualquiera de los valores contenidos en una lista predefinida de barrios.

### Consultas de Agregación y Análisis de Resultados

Las consultas de agregación se realizan a través del Pipeline de MongoDB (db.collection.aggregate()), que es la herramienta clave para el análisis estadístico en Big Data.

* Top 5 de Barrios con Mayor Número de Accidentes

Explicación del Código: Esta consulta utiliza el pipeline con tres etapas. Primero, $group agrupa los documentos por el campo ubicacion.barrio y usa el operador $sum: 1 para contar la frecuencia de accidentes en cada grupo. Luego, $sort ordena estos totales de forma descendente, y finalmente, $limit: 5 restringe la salida a las zonas más críticas.
Análisis de Resultados: El resultado de esta agregación es fundamental para la toma de decisiones geoespaciales. Permite a las autoridades identificar de forma rápida los puntos calientes (hotspots) de accidentalidad. Si un valor como "SIN INFORMACIÓN DEL BARRIO" aparece en el top, esto también sirve como una alerta sobre la necesidad de mejorar la calidad de la captura de datos en campo.


* Distribución de Gravedad de Accidentes
  
Explicación del Código: La etapa $group aquí es más compleja. Agrupa los eventos por su nivel de gravedad (resultado.tipo) y utiliza el operador $sum de dos maneras: $sum: 1 para contar el total de eventos, y $sum: "$involucrados.heridos" para sumar el número total de víctimas heridas, proporcionando estadísticas de resumen.

Análisis de Resultados: Este análisis cuantifica el impacto real de los eventos. Permite distinguir si la mayoría de los accidentes son de baja gravedad ("solo daños materiales") o si contribuyen significativamente al número total de heridos o muertes. Esta información es crucial para evaluar la letalidad de los diferentes tipos de accidentes y para dimensionar la capacidad necesaria de los servicios de emergencia y salud.

### Optimización y Validación (Cierre del Diseño)

Como paso final, se implementaron Índices para asegurar la eficiencia del sistema, lo cual es esencial en un entorno de Big Data con grandes volúmenes de datos. Los índices se crearon en campos usados frecuentemente para filtrar y agrupar (como ubicacion.barrio), acelerando las consultas de agregación. 
Además, se aplicó la Validación de Esquema usando el comando db.runCommand({ collMod: ...}). Aunque MongoDB es schema-less, la validación ($jsonSchema) asegura que los nuevos documentos cumplan con las reglas de negocio (ej. el campo heridos debe ser de tipo int y ser mayor o igual a 0), garantizando la integridad y consistencia de los datos.
 
 
 
