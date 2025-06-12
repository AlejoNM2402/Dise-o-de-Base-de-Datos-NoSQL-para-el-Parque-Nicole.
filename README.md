# 📊 Diseño de Base de Datos NoSQL para el Parque Nicole

## 👥 Integrantes
- Sebastian Mauricio Betancourt
- Alejandro Naranjo
- Daniel Trigos

---

## 🧠 Introducción

Este proyecto propone un modelo de base de datos para gestionar eficientemente la información de un parque de diversiones. A diferencia de un modelo relacional tradicional, el diseño aprovecha la flexibilidad de MongoDB para decidir entre **incrustar (embedding)** o **referenciar (referencing)** datos según las necesidades de consulta, rendimiento y escalabilidad.

---

## 🧩 Análisis de Entidades y Propuesta de Modelo de Datos

### 🎢 1. Colección: `atracciones`
**Atributos:**
- `_id`: ObjectId  
- `nombre`: String  
- `tipo`: String  
- `descripción`: String  
- `altura_minima_requerida_metros`: Number  
- `capacidad`: Number  
- `estado`: String  
- `tiempo_de_espera_promedio_minutos`: Number  

**Relaciones:**
- Zona Muchos a Uno → Referencia  
- Empleados asignados Uno a Muchos → Referencia  
- Mantenimiento Uno a Muchos → Referencia  

**Justificación:**  
Se usa referencia para empleados, zona y mantenimiento porque pueden cambiar frecuentemente, tienen mucha información y pueden compartirse entre entidades.

---

### 👷 2. Colección: `empleados`
**Atributos:**
- `_id`: ObjectId  
- `nombre`: String  
- `apellido`: String  
- `cargo`: String  
- `horario_trabajo`: Object  
- `atracciones_asignadas`: [ObjectId]  

**Relaciones:**
- Atracciones (Muchos a Muchos) → Referencia  
- Eventos (Muchos a Muchos) → Referencia  
- Mantenimiento (Muchos a Muchos) → Referencia  

**Justificación:**  
Referencia para mantener integridad y evitar duplicación. Un empleado puede trabajar en varias atracciones o eventos.

---

### 🎉 3. Colección: `eventos`
**Atributos:**
- `_id`: ObjectId  
- `nombre`: String  
- `descripcion`: String  
- `horario`: Object  
- `ubicacion`: ObjectId  
- `empleados_responsables`: [ObjectId]  

**Relaciones:**
- Ubicación (Uno a Uno) → Referencia  
- Empleados (Uno a Muchos) → Referencia  

**Justificación:**  
Uso de IDs para evitar redundancia y permitir cambios frecuentes en responsables.

---

### 🛠️ 4. Colección: `mantenimiento`
**Atributos:**
- `_id`: ObjectId  
- `fecha`: Date  
- `descripcion`: String  
- `atraccion`: ObjectId  
- `empleados_responsables`: [ObjectId]  
- `costo_us`: Number  

**Relaciones:**
- Atracción (Uno a Uno) → Referencia  
- Empleados (Muchos a Muchos) → Referencia  

**Justificación:**  
Registros históricos con varios empleados involucrados → se mantiene por referencia.

---

### 🎟️ 5. Colección: `tickets`
**Atributos:**
- `_id`: ObjectId  
- `tipo`: String  
- `precio_us`: Number  
- `fecha_compra`: Date  
- `fecha_validez`: Date  
- `visitante_asociado`: ObjectId  

**Relaciones:**
- Visitante (Muchos a Uno) → Referencia  

**Justificación:**  
Evita duplicación de información del visitante.

---

### 👨‍👩‍👧 6. Colección: `visitantes`
**Atributos:**
- `_id`: ObjectId  
- `nombre`: String  
- `apellido`: String  
- `fecha_nacimiento`: Date  
- `email`: String  
- `historial_visitas`: [Object]  
- `tickets`: [Object]  

**Relaciones:**
- Tickets (Uno a Muchos) → Incrustación  
- Historial de visitas (Uno a Muchos) → Incrustación con referencias  

**Justificación:**  
Incrustación por acoplamiento fuerte, referencias en historial para zonas y atracciones.

---

### 🗺️ 7. Colección: `zonas`
**Atributos:**
- `_id`: ObjectId  
- `nombre`: String  
- `descripcion`: String  
- `atracciones`: [ObjectId]  

**Relaciones:**
- Atracciones (Uno a Muchos) → Referencia  

**Justificación:**  
Posible reasignación de atracciones o uso compartido entre zonas → referencia.

---

## 🧾 Conclusiones y Desafíos

- ✅ **Decisión de Incrustación vs Referencia:**  
  Incrustación para datos estáticos y acoplados. Referencia para datos compartidos.

- 📈 **Escalabilidad:**  
  Priorización de referencias para entidades con crecimiento y  modificación frecuente.

- ⚠️ **Desafíos:**  
  Modelar historial de visitas fue complejo por combinar incrustación y referencias a múltiples zonas y atracciones.
