# Apuntes para Examen – Modelo Relacional  
Basado en **TEMA 2. Parte 2. Modelo Relacional.pdf**

---

## **1. TERMINOLOGÍA RELACIONAL**

| Término relacional formal | Equivalente informal       |
|---------------------------|----------------------------|
| **Relación**              | Tabla                      |
| **Tupla**                 | Fila o registro            |
| **Cardinalidad**          | Número de filas            |
| **Atributo**              | Columna o campo            |
| **Grado**                 | Número de columnas         |
| **Clave primaria**        | Identificador único        |
| **Dominio**               | Conjunto de valores permitidos |

**Condiciones que debe cumplir una relación:**

1. **Un solo tipo de registro** por tabla → cada tipo será una tabla diferente.
2. **No existen tuplas repetidas** (el cuerpo es un conjunto matemático).
3. **El orden de las tuplas no está determinado** (por ser conjunto).
4. **No existen atributos repetidos** (la cabecera también es un conjunto).
5. **El orden de los atributos no está determinado** (por ser conjunto).

---

## **2. ESTRUCTURA Y NORMALIZACIÓN**

### **2.1. Definición de Base de Datos Relacional**
- Colección de **relaciones normalizadas** de diversos grados que varía con el tiempo.
- Los sistemas relacionales operan sobre **tablas completas**, mediante operaciones sobre ellas.

### **2.2. Normalización**
**Problemas sin normalizar:**
- **Redundancia** (datos repetidos).
- **Problemas de actualización** (modificaciones inconsistentes).
- **Problemas de inserción** (no se puede insertar sin ciertos datos).
- **Problemas de borrado** (se pierde información relacionada).

**Definición:** Técnica para agrupar la información en diferentes conjuntos facilitando la manipulación.

#### **Dependencias**
1. **Dependencia Funcional (DF)**  
   `X → Y` → cada valor de X tiene un único valor de Y asociado.
   - Ejemplo: `CodEmp → NomEmp`

2. **Dependencia Funcional Plena**  
   `(X1,X2) → Y` y ni X1 ni X2 por separado determinan Y.
   - Ejemplo: `(NumFac, CodArt) → Cantidad`

3. **Dependencia Transitiva**  
   `X → Y` y `Y → Z` → entonces `X → Z` (transitivamente).
   - Ejemplo: `CodCiudad → CodComunidad` y `CodComunidad → NomComAut`

---

## **FORMAS NORMALES**

### **Primera Forma Normal (1FN)**
- Todos los dominios contienen **valores atómicos** (no conjuntos).
- Cada intersección fila-columna contiene **un solo valor**.

### **Segunda Forma Normal (2FN)**
- Debe estar en 1FN.
- Si la clave primaria es compuesta, **todos los atributos no primarios deben tener dependencia funcional plena** con la clave primaria.
- Si la clave primaria no es compuesta → automáticamente en 2FN.

### **Tercera Forma Normal (3FN)**
- Debe estar en 2FN.
- **Ningún atributo no primario tiene dependencias transitivas** respecto a la clave.

**Objetivo final:** Todas las tablas en **3FN**.

---

## **3. REGLAS DE INTEGRIDAD RELACIONAL**

### **3.1. Reglas Específicas**
- Propias de cada base de datos.
- Ejemplos: cantidades múltiplos de 100, no negativas, formatos `Snnn`, `Pnnn`.

### **3.2. Reglas Generales**
#### **a) Regla de Integridad de la Entidad**
- **Ningún componente de la clave primaria puede aceptar nulos.**

#### **b) Regla de Integridad Referencial**
- **La base de datos no debe contener valores de clave ajena sin concordancia** con la clave primaria referenciada.

---

## **CLAVES PRIMARIAS Y AJENAS**

### **Claves Candidatas**
- Atributo(s) que identifican una tupla de forma unívoca.
- Puede haber varias.

### **Clave Primaria**
- Cualquier elección de una clave candidata.
- Las demás son **claves alternativas**.

### **Clave Ajena (Foránea)**
- Atributo(s) de una relación R2 cuyos valores deben concordar con la clave primaria de R1.
- **Puede aceptar nulos** (a diferencia de la primaria).
- Si es compuesta: todos sus componentes son nulos o todos no nulos.
- Puede ser **autorreferencial** (referenciar la misma tabla).

**Relaciones:**
- **Referencial:** contiene la clave ajena.
- **Objetivo:** contiene la clave primaria referenciada.

---

## **REGLAS PARA CLAVES AJENAS**
Para cada clave ajena se debe especificar:

1. **¿Puede aceptar nulos?** (depende del modelo del mundo real).
2. **Regla de Eliminación (DELETE):**
   - `RESTRICT` / `NO ACTION`: no permite eliminar si hay referencias.
   - `CASCADE`: elimina también las filas referenciadas.
   - `SET NULL`: asigna NULL a la clave ajena (si acepta nulos).
3. **Regla de Modificación (UPDATE):** mismas opciones que DELETE.

**Sintaxis SQL:**
```sql
FOREIGN KEY (atributos) REFERENCES tabla_objetivo(atributos)
ON DELETE efecto
ON UPDATE efecto
```

**Ejemplo:**
```sql
CREATE TABLE empleado (
  CodEmp INT NOT NULL,
  NomEmp VARCHAR(100) NOT NULL,
  CodDep INT,
  PRIMARY KEY (CodEmp),
  FOREIGN KEY (CodDep) REFERENCES departamento(CodDep)
    ON DELETE SET NULL
    ON UPDATE CASCADE
);
```

---

## **4. LENGUAJES RELACIONALES**

### **4.1. Introducción**
- **Álgebra relacional:** procedimental (cómo obtener datos).
- **Cálculo relacional:** declarativo (qué datos obtener).
- **SQL** es un híbrido de ambos.

### **4.2. Álgebra Relacional**
**Operaciones tradicionales de conjuntos** (tablas compatibles en grado y dominios):
- **UNIÓN (R ∪ S):** filas de R o S.
- **INTERSECCIÓN (R ∩ S):** filas comunes.
- **DIFERENCIA (R - S):** filas de R no en S.
- **PRODUCTO CARTESIANO (R × S):** todas las combinaciones.

**Operaciones relacionales básicas:**
- **SELECCIÓN (σ):** filas que cumplen condición.
- **PROYECCIÓN (π):** columnas seleccionadas.
- **CONCATENACIÓN (JOIN):** une tablas por atributo común.
- **DIVISIÓN:** tuplas de R que contienen todos los valores de S.

**Notación algebraica:**
- `SELECT tabla WHERE condición GIVING resultado`
- `PROJECT tabla OVER atributo/s GIVING resultado`
- `JOIN tabla1 AND tabla2 OVER atributo_común GIVING resultado`
- `DIV tabla_binaria IN tabla_unaria OVER atributo_común GIVING resultado`

### **4.3. Cálculo Relacional**
- Estructura: `<objetivo> WHERE <predicados>`
- **Variable de tupla:** `RANGE X IS tabla`
- **Cuantificadores:**
  - `EXIST X (condición)` → existe al menos una tupla.
  - `FORALL X (condición)` → para todas las tuplas.
- **Operadores lógicos:** `∧` (AND), `∨` (OR), `¬` (NOT)

**Ejemplo:**
```
RANGE XP IS P
(XP.P#, XP.Nombre) : (XP.Ciudad = 'Madrid')
```

---

## **5. RESUMEN PARA EL EXAMEN**
- **Terminología:** relación, tupla, atributo, clave, dominio, cardinalidad, grado.
- **Normalización:** 1FN, 2FN, 3FN, dependencias funcionales.
- **Integridad:** reglas específicas y generales (entidad y referencial).
- **Claves:** candidata, primaria, alternativa, ajena (reglas DELETE/UPDATE).
- **Álgebra relacional:** operaciones de conjuntos y relacionales.
- **Cálculo relacional:** estructura, cuantificadores, operadores lógicos.

**Recomendación:** practicar ejercicios de normalización hasta 3FN y consultas en álgebra relacional.