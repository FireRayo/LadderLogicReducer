# 🔧 Reductor Booleano → Ladder LDmicro

Aplicación web HTML5 (archivo único) para capturar, validar, minimizar y convertir tablas lógicas booleanas a lógica Ladder compatible con LDmicro.

---

## 🚀 Características

- Importación de tablas (Excel / ODT / copiar-pegar)
- Validación de consistencia lógica
- Minimización booleana avanzada
- Optimización específica para Ladder (PLC)
- Generación de:
  - Ladder ASCII
  - Vista visual estilo LDmicro
  - Archivo `.ld`
  - Archivo `.txt`
- Interfaz multilenguaje:
  - Español
  - English
  - Italiano
- Detección de errores:
  - filas duplicadas
  - solapamientos ambiguos

---

## 🧠 Funcionamiento

### 1. Entrada
Entradas: E1..En  
Salidas: S1..Sn  

Valores:
- 1 → TRUE  
- 0 → FALSE  
- X / vacío → Don't Care  

---

### 2. Validación

Detecta:
- duplicados contradictorios
- conflictos por cobertura (fila general vs específica)

---

### 3. Minimización

- absorción lógica
- eliminación de redundancias
- reducción de términos

---

### 4. Optimización Ladder

- factor común
- reducción de contactos
- generación de ramas paralelas

Ejemplo:

S1 = ¬E10 · (E2 + E4 + E1·¬E5)

---

### 5. Salida

- Ladder ASCII
- Vista visual
- Archivo `.ld`
- Archivo `.txt`

---

## 📊 Tipos de tablas

### ✔️ Válidas
- con Don't Care
- parciales (reglas)
- completas

### ❌ Inválidas
- duplicadas contradictorias
- solapamientos inconsistentes

---

## ⚠️ Manejo de errores

La app bloquea resultados si detecta ambigüedad lógica.

---

## 🧱 Arquitectura

- HTML5 + CSS + JS
- archivo único
- sin dependencias

---

## 📦 Uso

1. Abrir HTML
2. Ingresar tabla
3. Minimizar
4. Exportar

---

## 🌐 Idiomas

Español / English / Italiano

---

## 👤 Autor

Raymundo Ortiz

---

## 🧾 Versión

v16

---

## 🚀 Futuro

- prioridad de reglas
- importación directa Excel
- editor Ladder avanzado
