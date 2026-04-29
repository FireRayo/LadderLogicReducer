# 🔧 Boolean to Ladder Reducer (LDmicro)

Single-file HTML5 web application to capture, validate, minimize, and convert boolean logic tables into Ladder Logic compatible with LDmicro.

---

## 🚀 Features

- Table import (Excel / ODT / copy-paste)
- Logical consistency validation
- Advanced boolean minimization
- Optimization specifically for Ladder (PLC)
- Output generation:
  - Ladder ASCII
  - Visual Ladder view (LDmicro style)
  - `.ld` file
  - `.txt` file
- Multilanguage interface:
  - Spanish
  - English
  - Italian
- Error detection:
  - duplicated rows
  - ambiguous overlaps

---

## 🧠 How it works

### 1. Input
Inputs: E1..En  
Outputs: S1..Sn  

Allowed values:
- 1 → TRUE  
- 0 → FALSE  
- X / empty → Don't Care  

---

### 2. Validation

Detects:
- contradictory duplicates
- coverage conflicts (general vs specific row)

---

### 3. Minimization

- logical absorption
- redundancy elimination
- term reduction

---

### 4. Ladder Optimization

- common factor extraction
- contact reduction
- parallel branch generation

Example:

S1 = ¬E10 · (E2 + E4 + E1·¬E5)

---

### 5. Output

- Ladder ASCII
- Visual view
- `.ld` file
- `.txt` file

---

## 📊 Supported table types

### ✔️ Valid
- with Don't Care
- partial (rule-based)
- full truth tables

### ❌ Invalid
- contradictory duplicates
- inconsistent overlaps

---

## ⚠️ Error Handling

The app blocks output if logical ambiguity is detected.

---

## 🧱 Architecture

- HTML5 + CSS + JS
- single file
- no dependencies

---

## 📦 Usage

1. Open the HTML file
2. Enter or paste the table
3. Run minimization
4. Export results

---

## 🌐 Languages

Spanish / English / Italian

---

## 👤 Author

Raymundo Ortiz

---

## 🧾 Version

v16

---

## 🚀 Future

- rule priority mode
- direct Excel import
- advanced Ladder editor
