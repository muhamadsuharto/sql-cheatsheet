<h1 align="center">📘 SQL Cheatsheet</h1>

<p align="center">
  Todo lo esencial de <strong>SQL</strong> en una sola hoja. <br/>
  Consultas, comandos y operaciones clave explicadas de forma clara, ordenada y con ejemplos prácticos. 
</p>

<div align="center">
  <img src="https://img.shields.io/badge/SQL-CheatSheet-blue.svg" alt="SQL Badge"/>
  <img src="https://img.shields.io/badge/Práctico-100%25-green.svg"/>
  <img src="https://img.shields.io/badge/Formato-Markdown-lightgrey.svg"/>
</div>

<br>

## 📚 Índice de contenido

#### **1. DDL _(Data Definition Language)_**

- [`CREATE`](#create), [`DROP`](#drop), [`ALTER`](#alter), [`TRUNCATE`](#truncate)

#### **2. DQL _(Data Query Language)_**

- [`SELECT`](#select)
  - [`TOP`/`LIMIT`](#top-limit) | [`AS`](#as)
  - **Filtros:** [`WHERE`](#where) | [`AND`/`OR`](#and-or) | [`NULL`](#null) | [`BETWEEN`](#between) | [`LIKE`](#like) | [`IN`](#in) | [`EXISTS`](#exists)
  - **Orden y agrupación:** [`ORDER BY`](#order-by) | [`DISTINCT`](#distinct) | [`GROUP BY`](#group-by) | [`HAVING`](#having)
  - **Agregaciones:** [`COUNT`|`SUM`|`AVG`|`MIN`|`MAX`](#count-sum-avg-min-max)
- **Joins:** [`INNER`](#join) | [`LEFT`](#left-join) | [`RIGHT`](#right-join) | [`FULL OUTER`](#full-join) | [`CROSS`](#cross-join) | [`SELF`](#self-join) | [`NATURAL`](#natural-join)
- **Operadores de conjunto:** [`UNION`/`UNION ALL`](#union) | [`INTERSECT`](#intersect) | [`EXCEPT`/`MINUS`](#except)
- **Subconsultas avanzadas:** [`ANY`/`ALL`](#any-all) | [`WITH`](#with)

#### **3. DML _(Data Manipulation Language)_**

- [`INSERT`](#insert) | [`UPDATE`](#update) | [`DELETE`](#delete)
- [`CALL`](#call) | [`LOCK`](#lock)

#### **4. DCL _(Data Control Language)_**

- [`GRANT`](#grant) | [`REVOKE`](#revoke)

#### **5. TCL _(Transaction Control Language)_**

- [`BEGIN TRANSACTION`/`START TRANSACTION`](#begin-start-transaction) | [`COMMIT`](#commit) | [`SAVEPOINT`](#savepoint) | [`ROLLBACK`](#rollback) | [`SET TRANSACTION`](#set)

<br>

## 1. DDL - `CREATE`**,**`DROP`**,**`ALTER`**,**`TRUNCATE`

Los comandos **DDL _(Data Definition Language)_** permiten **definir, modificar y eliminar estructuras** de bases de datos como esquemas, tablas y vistas.

<a name="create"></a>

### **CREATE** (crear tablas, esquemas...)

#### Crear un **esquema** o **base de datos**:

```sql
CREATE SCHEMA nombre_esquema;
-- o (MySQL):
CREATE DATABASE nombre_base_datos;
```

> [!NOTE] > `SCHEMA` ≠ `DATABASE` en algunos motores (como MySQL/PostgreSQL). En **PostgreSQL**, el esquema es un contenedor lógico para objetos de base de datos como tablas, vistas, secuencias, etc. En **MySQL**, el esquema es equivalente a una base de datos.

#### Crear una **tabla**:

```sql
CREATE TABLE tabla (
   col1 datatype,
   col2 datatype,
   col3 datatype,
   col4 datatype,
);
```

También se puede hacer:

```sql
CREATE TABLE IF NOT EXISTS tabla ( ... );
```

```sql
CREATE TABLE IF NO EXISTS books (
   id     SERIAL PRIMARY KEY,
   title  VARCHAR(100) NOT NULL,
   author VARCHAR(100) NOT NULL
);
```

#### Crear una **tabla** en un esquema (PostgreSQL):

```sql
CREATE TABLE nombre_esquema.tabla (
   col1 datatype,
   col2 datatype
);
```

#### Crear una **vista** (vista virtual)

```sql
CREATE VIEW nombre_vista AS
SELECT columna1, columna2 FROM tabla;
```

```sql
CREATE VIEW vista_ventas AS
SELECT fecha, SUM(total) AS total_diario
FROM ventas
GROUP BY fecha;
```

<a name="drop"></a>

### **DROP** - Eliminar objetos

```sql
DROP TABLE IF EXISTS tabla;
DROP VIEW IF EXISTS nombre_vista;
DROP SCHEMA IF EXISTS nombre_esquema CASCADE;
DROP DATABASE IF EXISTS nombre_base_datos;
```

<a name="alter"></a>

### **ALTER** - Modificar estructuras existentes

#### **`ADD`** - Añadir una nueva columna

```sql
ALTER TABLE tabla ADD nueva_columna tipo;
```

#### **`RENAME`** - Renombrar una columna

```sql
ALTER TABLE tabla RENAME COLUMN antigua_columna TO nueva_columna;
```

#### **`MODIFY`** _(MySQL)_ | `ALTER TYPE` _(PostgreSQL)_ - Cambiar el tipo de dato de la columna

```sql
-- En MySQL
ALTER TABLE tabla MODIFY columna tipo;

-- En PostgreSQL
ALTER TABLE tabla ALTER COLUMN columna TYPE nuevo_tipo;
```

#### **`DROP`** - Eliminar una columna existente

```sql
ALTER TABLE tabla DROP COLUMN columna;
```

<a name="truncate"></a>

### **TRUNCATE** - Vaciar una tabla (sin eliminar su estructura)

Borra todas las filas de una tabla existente.

```sql
TRUNCATE TABLE tabla;
TRUNCATE TABLE tabla RESTART IDENTITY;  -- PostgreSQL: reinicia IDs auto
```

<br>

## 2. DQL - `SELECT`

DQL (Data Query Language) se utiliza para **consultar datos** de una base de datos. La palabra clave principal es `SELECT`, acompañada de múltiples cláusulas opcionales que permiten **filtrar, agrupar, ordenar y combinar** resultados.

<a name="select"></a>

### **`SELECT`** - Consultar datos

```sql
SELECT * FROM tabla;
SELECT col1, col2 FROM tabla;
```

Seleccionar / Usar una vista (view):

```sql
SELECT * FROM vista_ventas WHERE fecha = '2024-01-01';
```

<a name="top-limit"></a>

### **`TOP` / `LIMIT`** - Limitar registros

Especificar el número de registros a devolver desde la parte superior de la tabla.

```sql
-- SQL Server
SELECT TOP 10 * FROM tabla;
SELECT TOP number columnas FROM tabla WHERE condicion;
SELECT TOP percent columnas FROM tabla WHERE condicion;

-- PostgreSQL / MySQL / SQLite
SELECT * FROM tabla LIMIT 10;
SELECT * FROM tabla LIMIT 10 OFFSET 5;
```

> [!NOTE]
> No todos los sistemas de bases de datos soportan `SELECT TOP`.
> El equivalente en MySQL es la cláusula`LIMIT`.

<a name="as"></a>

#### **`AS`** - Alias de columnas o tablas

Un _alias_ es el nombre temporal que toma una tabla o columna

```sql
SELECT columna AS alias FROM tabla;
SELECT columna FROM tabla AS alias;
```

```sql
SELECT col1 AS alias1, col2 AS alias2;
SELECT col1 AS alias1, col2 AS alias2 FROM tabla AS alias3;
SELECT t.col1, t.col2 FROM tabla AS t;
```

<a name="where"></a>

#### **`WHERE`** - Filtro condicional

```sql
SELECT * FROM tabla WHERE columna = valor;
SELECT col1, col2 FROM tabla WHERE condicion;
SELECT * FROM tabla WHERE NOT condicion;
SELECT * FROM tabla WHERE EXISTS (SELECT 1 FROM otra_tabla WHERE condicion);
```

<a name="and-or"></a>

#### **`AND & OR`** - Múltiples condiciones

```sql
SELECT * FROM tabla WHERE condicion1 AND condicion2;
SELECT * FROM tabla WHERE condicion1 AND condicion2 AND ...;
SELECT * FROM tabla WHERE condicion1 OR condicion2;
SELECT * FROM tabla WHERE condicion1 OR condicion2 OR ...;
SELECT * FROM tabla WHERE condicion1 AND (condicion2 OR condicion3);
```

```sql
SELECT * FROM tabla WHERE col1 = 'A' AND col2 > 10;
SELECT * FROM tabla WHERE col1 = 'B' OR col2 < 5;
SELECT * FROM tabla WHERE col1 = 'X' AND (col2 = 'Y' OR col3 = 'Z');
```

<a name="null"></a>

#### **`NULL`** - Valores nulos

```sql
SELECT * FROM tabla WHERE columna IS NULL;
SELECT * FROM tabla WHERE columna IS NOT NULL;
```

<a name="between"></a>

#### **`BETWEEN`** - Rango de valores

```sql
SELECT col1, col2 FROM tabla WHERE columna BETWEEN value1 AND value2;
SELECT * FROM tabla WHERE columna BETWEEN 10 AND 20;
SELECT * FROM ventas WHERE fecha BETWEEN '2024-01-01' AND '2024-12-31';
```

<a name="like"></a>

#### **`LIKE`** - Busqueda por patrón

```sql
SELECT * FROM tabla WHERE columna LIKE 'a%';      -- empieza por 'a'
SELECT * FROM tabla WHERE columna LIKE '%a';      -- termina por 'a'
SELECT * FROM tabla WHERE columna LIKE '%texto%'; -- contiene 'texto'
SELECT * FROM tabla WHERE columna LIKE '_r%';     -- segundo carácter es 'r'
SELECT * FROM tabla WHERE columna LIKE 'a%b';     -- empieza por 'a' y termina por 'b'
SELECT * FROM tabla WHERE columna LIKE 'a%b%c';   -- empieza por 'a', termina por 'c' y tiene 'b' en cualquier posición
```

- `%` (signo porcentaje) - carácter comodín que representa cero, uno o varios caracteres.
- `\_` (underscore) - carácter comodín que representa un único carácter.

<a name="in"></a>

#### **`IN`** - Lista de valores o subconsulta

_Esencialmente, el operador `IN` es una abreviatura de las condiciones `OR` múltiples._

```sql
SELECT columnas FROM tabla WHERE columna IN (value1, value2, …);
SELECT * FROM tabla WHERE columna IN ('A', 'B', 'C');
SELECT columnas FROM tabla WHERE columna IN (SELECT columna FROM otra_tabla);
```

<a name="exists"></a>

#### **`EXISTS`** - Subconsulta

```sql
SELECT * FROM tabla
WHERE EXISTS (
   SELECT 1
   FROM otra_tabla
   WHERE condicion
);
```

```sql
SELECT name
FROM countries
WHERE EXISTS (
   SELECT *
   FROM cities
   WHERE country_id = countries.id
)
```

<a name="order-by"></a>

#### **`ORDER BY`** - Orden de resultados (**ascendente** o **descendente**)

```sql
SELECT * FROM tabla ORDER BY column;
SELECT * FROM tabla ORDER BY column DESC;
SELECT * FROM tabla ORDER BY col1 ASC, col2 DESC;
```

<a name="distinct"></a>

#### **`DISTINCT`** - Eliminar duplicados

```sql
SELECT DISTINCT col1, col2 FROM tabla;
```

<a name="group-by"></a>

#### **`GROUP BY`** – Agrupar resultados

```sql
SELECT columna, COUNT(*) FROM tabla GROUP BY columna;
SELECT departamento, COUNT(*) FROM empleados GROUP BY departamento;
```

```sql
SELECT col1, COUNT(col2) FROM tabla WHERE condicion
GROUP BY col1 ORDER BY COUNT(col2) DESC;
```

<a name="having"></a>

#### **`HAVING`** - Filtro para grupos (con agregaciones)

```sql
SELECT columna, COUNT(*) FROM tabla GROUP BY columna HAVING COUNT(*) > 10;
SELECT COUNT(col1), col2 FROM tabla GROUP BY col2 HAVING COUNT(col1) > 5;
SELECT departamento, COUNT(*) FROM empleados GROUP BY departamento HAVING COUNT(*) > 10;
```

<a name="count-sum-avg-min-max"></a>

#### **`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`**

```sql
SELECT COUNT(*) FROM tabla;
SELECT COUNT(DISTINCT columna) FROM tabla;
SELECT SUM(total) FROM ventas;
SELECT AVG(salario) FROM empleados;
SELECT MIN(precio), MAX(precio) FROM productos;
```

### Joins

A continuación un set de **tablas de ejemplo** y cómo quedan los resultados tras cada tipo de `JOIN`.

**`empleados`**

| id  | nombre | dep_id | supervisor_id |
| --- | ------ | ------ | ------------- |
| 1   | Juan   | 1      | NULL          |
| 2   | María  | 2      | 1             |
| 3   | Pedro  | 3      | 1             |
| 4   | Ana    | 2      | 2             |

**`departamentos`**

| id  | departamento     |
| --- | ---------------- |
| 1   | Marketing        |
| 2   | Ventas           |
| 3   | Recursos Humanos |
| 4   | Finanzas         |

<a name="inner-join"></a>

#### **`INNER JOIN`** - Solo los registros que coinciden

```sql
SELECT columnas
FROM tabla1
INNER JOIN tabla2
   ON tabla1.columna = tabla2.columna;

SELECT tabla1.columna1, tabla2.columna2, tabla3.columna3
FROM ( ( tabla1
   INNER JOIN tabla2
      ON relacion
   )
INNER JOIN tabla3
   ON relacion
);
```

```sql
SELECT e.nombre, d.departamento
FROM empleados e
INNER JOIN departamentos d
  ON e.dep_id = d.id;
```

**Resultado:**

| nombre | departamento     |
| ------ | ---------------- |
| Juan   | Marketing        |
| María  | Ventas           |
| Pedro  | Recursos Humanos |
| Ana    | Ventas           |

<a name="left-join"></a>

#### `LEFT JOIN` - Todos los registros de la tabla de la izquierda

```sql
SELECT e.nombre, d.departamento
FROM empleados e
LEFT JOIN departamentos d
  ON e.dep_id = d.id;
```

**Resultado:**

| nombre | departamento     |
| ------ | ---------------- |
| Juan   | Marketing        |
| María  | Ventas           |
| Pedro  | Recursos Humanos |
| Ana    | Ventas           |

<a name="right-join"></a>

#### `RIGHT JOIN` - Todos los registros de la tabla de la derecha

```sql
SELECT columnas
FROM tabla1
RIGHT JOIN tabla2
   ON tabla1.columna = tabla2.columna;
```

```sql
SELECT e.nombre, d.departamento
FROM empleados e
RIGHT JOIN departamentos d
  ON e.dep_id = d.id;
```

**Resultado:**

| nombre | departamento     |
| ------ | ---------------- |
| Juan   | Marketing        |
| María  | Ventas           |
| Pedro  | Recursos Humanos |
| Ana    | Ventas           |
| NULL   | Finanzas         |

<a name="full-join"></a>

#### `FULL [OUTER] JOIN` - Todos los registros de ambas tablas

```sql
SELECT columnas
FROM tabla1
FULL OUTER JOIN tabla2
   ON tabla1.columna = tabla2.columna;
```

```sql
SELECT e.nombre, d.departamento
FROM empleados e
FULL OUTER JOIN departamentos d
  ON e.dep_id = d.id;
```

**Resultado:**

| nombre | departamento     |
| ------ | ---------------- |
| Juan   | Marketing        |
| María  | Ventas           |
| Pedro  | Recursos Humanos |
| Ana    | Ventas           |
| NULL   | Finanzas         |

<a name="cross-join"></a>

#### `CROSS JOIN` - Todas las combinaciones posibles de registros de ambas tablas

```sql
SELECT e.nombre, d.departamento
FROM empleados e
CROSS JOIN departamentos d;
```

**Resultado (4×4 = 16 filas):** Ejemplo de las primeras 4 filas:

| nombre | departamento     |
| ------ | ---------------- |
| Juan   | Marketing        |
| Juan   | Ventas           |
| Juan   | Recursos Humanos |
| Juan   | Finanzas         |

<a name="self-join"></a>

#### `SELF JOIN` (empleados y sus supervisores) - Unir una tabla consigo misma

```sql
SELECT e.nombre      AS empleado,
       s.nombre      AS supervisor
FROM empleados e
JOIN empleados s
  ON e.supervisor_id = s.id;
```

**Resultado:**

| empleado | supervisor |
| -------- | ---------- |
| María    | Juan       |
| Pedro    | Juan       |
| Ana      | María      |

<a name="natural-join"></a>

#### `NATURAL JOIN` _(según SGBD)_

> Une automáticamente por columnas de mismo nombre (`dep\_id = id`). Puede ser inseguro si hay nombres duplicados.

```sql
SELECT *
FROM empleados
NATURAL JOIN departamentos;
```

**Resultado equivalente a INNER JOIN:**

| id  | nombre | departamento     |
| --- | ------ | ---------------- |
| 1   | Juan   | Marketing        |
| 2   | María  | Ventas           |
| 3   | Pedro  | Recursos Humanos |
| 4   | Ana    | Ventas           |

<br>

### Operadores de conjunto (Set Operators)

<a name="union"></a>

#### **`UNION` | `UNION ALL`**

<img src="./src/sql-full-join.jpg" width="300">

Combina los resultados de dos o más consultas `SELECT` en un solo conjunto de resultados.

- Cada sentencia `SELECT` debe tener el mismo número de columnas.
- Las columnas deben tener el mismo tipo de datos.
- Las columnas en cada `SELECT` deben también estar en el mismo orden.

```sql
SELECT columnas FROM tabla1
UNION
SELECT columna FROM tabla2;
```

El operador `UNION` solo selecciona valores distintos, `UNION ALL` permitirá duplicados

```sql
SELECT columnas FROM tabla1
UNION ALL
SELECT columnas FROM tabla3;
```

_Ejemplo: "Obtener los nombres de ciclistas y nadadores españoles"._

```sql
SELECT name FROM cycling WHERE country = 'ES'
UNION
SELECT name FROM swimming WHERE country = 'ES';
```

<a name="insert"></a>

#### **`INTERSECT`**

<img src="./src/sql-inner-join.jpg" width="300">

Devuelve los registros que están presentes en ambas tablas. Suele utilizarse del mismo modo que `UNION`.

```sql
SELECT columnas FROM tabla1
INTERSECT
SELECT columna FROM tabla2;
```

_Ejemplo: "Obtener los nombres de ciclistas que también hacen triatlón en España"._

```sql
SELECT name FROM cycling WHERE country = 'ES'
INTERSECT
SELECT name FROM triathlon WHERE country = 'ES';
```

<a name="except"></a>

#### **`EXCEPT` / `MINUS`** _(según SGBD)_

<img src="./src/sql-left-subtrack-join.jpg" width="300">

Utilizado para devolver todos los registros de la primera sentencia `SELECT` que no se encuentran en la segunda sentencia `SELECT`. Generalmente se utiliza de la misma manera que **UNION**.

```sql
SELECT columnas FROM tabla1
EXCEPT
SELECT columna FROM tabla2;
```

_Ejemplo: "Obtener los nombres de ciclistas que no hacen triatlón en España"._

```sql
SELECT name FROM cycling WHERE country = 'ES'
EXCEPT
SELECT name FROM triathlon WHERE country = 'ES';
```

### Subconsultas avanzadas

<a name="any-all"></a>

#### **`ANY` / `ALL`** - Subconsultas con operadores de comparación

- `ANY` - Devuelve verdadero si algún valor de la subconsulta cumple la condicion.
- `ALL` - Devuelve verdadero si todos los valores de la subconsulta cumplen la condicion.

```sql
SELECT * FROM tabla WHERE columna operator ANY (SELECT * FROM tabla WHERE condicion);
SELECT * FROM tabla WHERE columna operator ALL (SELECT * FROM tabla WHERE condicion);
```

```sql
SELECT * FROM productos
WHERE precio > ANY (
   SELECT precio FROM productos WHERE categoria = 'A'
);

SELECT * FROM productos
WHERE precio > ALL (
   SELECT precio FROM productos WHERE categoria = 'B'
);
```

- `ANY` - Busca todos los productos cuyo precio sea mayor que al menos uno de los precios de la categoría A (`> mínimo` de los valores).
- `ALL` - Busca todos los productos cuyo precio sea mayor que todos los precios de la categoría B (`> máximo` de los valores).

<a name="with"></a>

#### **`WITH`** - CTE (Common Table Expression)

Subconsultas con alias temporales

```sql
WITH alias AS (
   SELECT * FROM tabla WHERE condicion
)
SELECT * FROM alias WHERE condicion;
```

```sql
WITH empleados_activos AS (
  SELECT * FROM empleados WHERE activo = TRUE
)
SELECT * FROM empleados_activos WHERE salario > 3000;
```

<br>

## 3. DML - `INSERT`, `UPDATE`, `DELETE`, `CALL`, `LOCK`

<a name="insert"></a>

### **`INSERT INTO`**

Insertar una fila en todas las columnas:

```sql
INSERT INTO tabla VALUES (value1, value2 …);
INSERT INTO empleados VALUES (2, 'Rocío', 1, NULL);
```

Insertar múltiples filas:

```sql
INSERT INTO tabla (col1, col2) VALUES
   (value1, value2),
   (value3, value4),
   (value5, value6);

INSERT INTO empleados (id, nombre, dep_id) VALUES
   (2, 'Rocío', 1),
   (3, 'Elena', 2),
   (4, 'Ángel', 3);
```

Insertar desde otra tabla:

```sql
INSERT INTO tabla (col1, col2)
SELECT col1, col2
FROM otra_tabla;

INSERT INTO empleados_ventas (id, nombre)
SELECT id, nombre
FROM empleados WHERE dep_id = 2;
```

<a name="update"></a>

### **`UPDATE`**

Actualizar columnas con condición:

```sql
UPDATE tabla
SET col1 = value1, col2 = value2
WHERE condicion;

UPDATE empleados
SET salario = salario * 1.1
WHERE dep_id = 1;
```

```sql
-- UPDATE con JOIN (PostgreSQL)
UPDATE empleados e
SET salario = s.nuevo_salario
FROM ajustes s
WHERE e.id = s.emp_id;
```

<a name="delete"></a>

### **`DELETE`**

Eliminar filas según condición:

```sql
DELETE FROM tabla WHERE condicion;
DELETE FROM empleados WHERE id = 7;
```

Eliminar todas las filas de una tabla (mantiene la estructura):

```sql
DELETE FROM empleados;
```

<a name="call"></a>

### **`CALL`** - _(Stored Procedures)_

Llamar a un procedimiento almacenado:

```sql
CALL procedimiento(param1, param2);
```

```sql
CALL sumar_sueldos(3);
```

<a name="lock"></a>

### **`LOCK`** - _(MySQL)_

Bloquear una tabla para lectura/escritura (evitar modificaciones):

```sql
LOCK TABLES tabla1 READ, tabla2 WRITE;
LOCK TABLES empleados WRITE;
UNLOCK TABLES;
```

> [!NOTE] > _No disponible en BigQuery (gestión de permisos a través de consola IAM y control a nivel dataset)._

<br>

## 4. DCL - `GRANT`, `REVOKE`

<a name="grant"></a>

#### **`GRANT`** - Asigna permisos a determinados usuarios

```sql
-- Conceder SELECT y INSERT a un usuario
GRANT SELECT, INSERT ON empleados TO juan;

-- Conceder todos los privilegios con opción de re-grant
GRANT ALL PRIVILEGES ON base_datos.* TO 'app_user'@'%'
  WITH GRANT OPTION;
```

> [!NOTE] > _No disponible en BigQuery (gestión de permisos a través de consola IAM y control a nivel dataset)._

<a name="revoke"></a>

#### **`REVOKE`** - Revoca permisos a determinados usuarios

```sql
-- Revocar permisos
REVOKE INSERT ON empleados FROM juan;
```

> [!NOTE] > _No disponible en BigQuery (gestión de permisos a través de consola IAM y control a nivel dataset)._

<br>

## 5. TCL – Control de Transacciones

TCL (Transaction Control Language) gestiona el control de las transacciones, asegurando la integridad de los datos. Incluye comandos para confirmar, deshacer y establecer puntos intermedios.

<a name="begin-start-transaction"></a>

#### `BEGIN TRANSACTION`/ `START TRANSACTION` - Iniciar una transacción

```sql
-- SQL Server / Oracle
BEGIN TRANSACTION;

-- MySQL / PostgreSQL (implícito tras cualquier DML)
START TRANSACTION;
```

<a name="commit"></a>

#### `COMMIT` - Confirmar cambios

```sql
COMMIT;
```

Guarda **permanentemente** todas las operaciones realizadas desde el inicio de la transacción.

<a name="rollback"></a>

#### `ROLLBACK` - Deshacer una transacción

```sql
ROLLBACK;
```

Cancela **toda** la transacción, dejando la base de datos en el estado previo.

<a name="savepoint"></a>

#### `SAVEPOINT` - Establecer un punto intermedio

```sql
SAVEPOINT nombre_punto;
```

**`ROLLBACK TO SAVEPOINT` - Deshacer hasta un punto**

```sql
SAVEPOINT nombre_punto;
-- .. operaciones intermedias ...
ROLLBACK TO SAVEPOINT nombre_punto;
```

Permite deshacer hasta un punto específico sin abordar toda la transacción.

**`RELEASE SAVEPOINT` - Eliminar un punto intermedio**

```sql
SAVEPOINT nombre_punto;
-- .. operaciones intermedias ...
ROLLBACK TO SAVEPOINT nombre_punto;
RELEASE SAVEPOINT nombre_punto;
```

<a name="set"></a>

#### `SET TRANSACTION` - Nivel de aislamiento y modo

```sql
-- Definir nivel de aislamiento (PostgreSQL, MySQL 8+)
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- Modo de solo lectura
SET TRANSACTION READ ONLY;
```

Controla concurrencia y visibilidad de datos entre transacciones.
