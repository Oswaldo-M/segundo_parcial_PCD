# Reto Semana 6: Validador de Códigos con Expresiones Regulares

**Programación para Ciencia de Datos**  
Instituto Politécnico Nacional | Semestre Febrero-Julio 2026

---

## Descripción

Sistema automatizado para validar códigos de una empresa de logística. El programa recibe códigos desde la entrada estándar (stdin), detecta su tipo usando expresiones regulares y determina si son válidos según las reglas de cada formato. La salida se genera en formato CSV.

---

## Requisitos

- Python 3
- Sin dependencias externas (solo biblioteca estándar)

---

## Estructura del proyecto

```
reto-semana-06/
├── README.md
├── main.py
├── .gitignore
└── tests/
    ├── codigos.txt
    └── salida_esperada.txt
```

---

## Uso

### Ejecutar con un archivo de entrada

```bash
# Linux / Mac
python3 main.py < tests/codigos.txt

# Windows (PowerShell)
Get-Content tests/codigos.txt | python main.py

# Windows (CMD)
type tests/codigos.txt | python main.py
```

### Guardar la salida en un archivo CSV

```bash
python3 main.py < tests/codigos.txt > resultados.csv
```

### Comparar con la salida esperada

```bash
python3 main.py < tests/codigos.txt > tests/mi_salida.txt
diff tests/mi_salida.txt tests/salida_esperada.txt
```

Si `diff` no muestra nada, la salida es correcta.

---

## Formatos de códigos soportados

### Producto
```
Formato:  ABC-1234-MX
           ───  ────  ──
            │    │    └── País: 2 letras MAYÚSCULAS
            │    └─────── Número: exactamente 4 dígitos
            └──────────── Categoría: exactamente 3 letras MAYÚSCULAS
```

| Válido | Inválido | Razón |
|--------|----------|-------|
| `TEC-0001-MX` | `tec-0001-MX` | Categoría en minúsculas |
| `ALI-9999-US` | `TEC-001-MX` | Solo 3 dígitos |
| `ROB-1234-CA` | `TECH-0001-MX` | 4 letras en categoría |

---

### Envío
```
Formato:  ENV-YYYY-MM-DD-NNNNNN
           ───  ────  ──  ──  ──────
            │    │    │   │    └── Secuencial: 6 dígitos
            │    │    │   └─────── Día: 01-31
            │    │    └──────────── Mes: 01-12
            │    └───────────────── Año: 2020-2030
            └────────────────────── Prefijo fijo: ENV
```

| Válido | Inválido | Razón |
|--------|----------|-------|
| `ENV-2024-03-15-001234` | `ENV-2019-03-15-001234` | Año fuera de rango |
| `ENV-2025-12-01-999999` | `ENV-2024-13-15-001234` | Mes 13 no existe |
| | `ENV-2024-03-32-001234` | Día 32 no existe |

---

### Empleado
```
Formato:  EMP-XXX-NNNN
           ───  ───  ────
            │    │    └── Número: 4 dígitos, no puede empezar con 0
            │    └─────── Departamento: VEN, ADM, TEC, LOG, RHH
            └──────────── Prefijo fijo: EMP
```

| Válido | Inválido | Razón |
|--------|----------|-------|
| `EMP-VEN-1234` | `EMP-VEN-0123` | Número empieza con 0 |
| `EMP-TEC-9999` | `EMP-XXX-1234` | Departamento no válido |
| `EMP-ADM-1000` | `EMP-VEN-123` | Solo 3 dígitos |

---

### Factura
```
Formato:  FAC-S-NNNNNN
           ───  ─  ──────
            │   │    └── Número: 6 dígitos
            │   └─────── Serie: A, B, C, D o E (MAYÚSCULA)
            └──────────── Prefijo fijo: FAC
```

| Válido | Inválido | Razón |
|--------|----------|-------|
| `FAC-A-123456` | `FAC-F-123456` | Serie F no existe |
| `FAC-E-000001` | `FAC-a-123456` | Serie en minúscula |
| `FAC-B-999999` | `FAC-A-12345` | Solo 5 dígitos |

---

## Formato de salida

El programa genera un CSV con tres columnas:

```
codigo,tipo,valido
TEC-0001-MX,producto,VALIDO
tec-0001-MX,producto,INVALIDO
ENV-2024-03-15-001234,envio,VALIDO
EMP-VEN-0123,empleado,INVALIDO
FAC-A-123456,factura,VALIDO
XXX-1234,desconocido,INVALIDO
```

### Reglas de detección de tipo

| Prefijo / Estructura | Tipo detectado |
|----------------------|---------------|
| 3 letras + `-` + 4 dígitos + `-` + 2 letras | `producto` |
| `ENV-` + fecha + secuencial | `envio` |
| `EMP-` + 3 letras + `-` + 4 dígitos | `empleado` |
| `FAC-` + 1 letra + `-` + 6 dígitos | `factura` |
| Cualquier otro | `desconocido` |

> Un código puede ser del tipo correcto pero **INVALIDO**. Por ejemplo, `tec-0001-MX` se detecta como `producto` pero es inválido porque la categoría debe ser mayúscula.

---

## Ejemplo completo

**Entrada (`tests/codigos.txt`):**
```
TEC-0001-MX
tec-0001-MX
ENV-2024-03-15-001234
ENV-2019-03-15-001234
EMP-VEN-1234
EMP-VEN-0123
FAC-A-123456
FAC-F-123456
XXX-1234
```

**Salida:**
```
codigo,tipo,valido
TEC-0001-MX,producto,VALIDO
tec-0001-MX,producto,INVALIDO
ENV-2024-03-15-001234,envio,VALIDO
ENV-2019-03-15-001234,envio,INVALIDO
EMP-VEN-1234,empleado,VALIDO
EMP-VEN-0123,empleado,INVALIDO
FAC-A-123456,factura,VALIDO
FAC-F-123456,factura,INVALIDO
XXX-1234,desconocido,INVALIDO
```

---

## Autor

Oswaldo Jafet Morales Flores 2026
