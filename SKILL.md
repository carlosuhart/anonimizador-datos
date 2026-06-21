---
name: anonimizar
description: >
  Anonimiza datos personales en archivos CSV, XLSX, MD y DOCX aplicando la
  normativa de protección de datos de la jurisdicción indicada: RGPD (UE),
  Ley 21.719 (Chile), LGPD (Brasil), LFPDPPP (México), Ley 1581 (Colombia),
  Ley 25.326 (Argentina), UK GDPR, CCPA/CPRA (California).
  Solo invocar con /anonimizar — no activar de forma automática.
user-invocable: true
argument-hint: "[ley] [ruta-carpeta]"
license: MIT
metadata:
  author: PubliUp SEO
  version: "2.0.0"
  category: privacy
---

# Anonimizador multi-jurisdiccional

Detecta y reemplaza datos personales identificables (PII) en documentos con
etiquetas neutras, cumpliendo la normativa de la jurisdicción seleccionada.

## Flujo de uso

### 1. Identificar jurisdicción y archivos

Preguntar al usuario si no especificó:
- ¿Qué ley aplica? (ver tabla de jurisdicciones)
- ¿Dónde están los archivos a anonimizar? (copiar a `entrada/` o indicar ruta)
- ¿Es un export de Screaming Frog? El script lo detecta automáticamente y solo
  toca columnas de texto libre.

### 2. Preparar entorno

Verificar que `anonimizar.py` existe en el proyecto. Si no:
- Ruta de referencia: `D:\OneDrive\Escritorio\Yo\Papitas SEO\anonimizador_rgpd\anonimizar.py`
- Instalar dependencias: `pip install -r requirements.txt`
- Instalar modelo spaCy mínimo: `python -m spacy download es_core_news_lg`
- Para inglés (UK/CCPA): `python -m spacy download en_core_web_lg`
- Para portugués (LGPD): `python -m spacy download pt_core_news_lg`

### 3. Copiar archivos a la carpeta `entrada/`

```
anonimizador_rgpd/
  entrada/    ← poner aquí los archivos originales
  salida/     ← el script crea esta carpeta y deposita los resultados
  anonimizar.py
```

### 4. Ejecutar

```bash
# RGPD (por defecto — España / UE)
python anonimizar.py

# Una sola jurisdicción
python anonimizar.py --ley chile
python anonimizar.py --ley brasil
python anonimizar.py --ley mexico
python anonimizar.py --ley uk
python anonimizar.py --ley ccpa

# Varias jurisdicciones a la vez (recomendado para proyectos internacionales)
python anonimizar.py --ley rgpd chile
python anonimizar.py --ley rgpd brasil

# Todas las jurisdicciones simultáneamente
python anonimizar.py --ley todo

# Ver jurisdicciones disponibles
python anonimizar.py --lista-leyes
```

---

## Jurisdicciones soportadas

| Clave       | Marco legal                                   | Identificadores detectados         |
|-------------|-----------------------------------------------|------------------------------------|
| `rgpd`      | RGPD/GDPR 2016/679 (UE)                       | DNI, NIE, teléfonos ES             |
| `chile`     | Ley 21.719 + Ley 19.628 (Chile)               | RUT/RUN, teléfonos CL              |
| `brasil`    | LGPD Lei 13.709/2018 (Brasil)                 | CPF, CNPJ, teléfonos BR            |
| `mexico`    | LFPDPPP 2010/2025 (México)                    | CURP, RFC, teléfonos MX            |
| `colombia`  | Ley 1581/2012 + Decreto 1377/2013 (Colombia)  | NIT, CC con contexto, teléfonos CO |
| `argentina` | Ley 25.326 (Argentina)                        | DNI, CUIT/CUIL, teléfonos AR       |
| `uk`        | UK GDPR / DPA 2018 / DUAA 2025               | NINO, teléfonos UK                 |
| `ccpa`      | CCPA/CPRA (California, EE.UU.)                | SSN, DL californiano, teléfonos US |

Entidades detectadas en **todas** las jurisdicciones (universales):
- Nombres de persona (`<PERSONA>`)
- Correos electrónicos (`<EMAIL>`)
- Números de teléfono (`<TELEFONO>`)
- Códigos IBAN (`<IBAN>`)
- Tarjetas de crédito (`<TARJETA>`)
- Fechas (`<FECHA>`)
- Direcciones IP (`<IP>`)
- Ubicaciones próximas a nombres (`<UBICACION>`)

---

## Etiquetas de reemplazo

| Etiqueta       | Qué sustituye                  | Jurisdicción       |
|----------------|--------------------------------|--------------------|
| `<PERSONA>`    | Nombre de persona              | Todas              |
| `<EMAIL>`      | Correo electrónico             | Todas              |
| `<TELEFONO>`   | Número de teléfono             | Todas              |
| `<IBAN>`       | Código IBAN                    | Todas              |
| `<TARJETA>`    | Número de tarjeta de crédito   | Todas              |
| `<FECHA>`      | Fechas                         | Todas              |
| `<IP>`         | Dirección IP                   | Todas              |
| `<UBICACION>`  | Lugar próximo a un nombre      | Todas              |
| `<DNI-ES>`     | DNI o NIE español              | RGPD               |
| `<RUT-CL>`     | RUT/RUN chileno                | Chile              |
| `<CPF-BR>`     | CPF brasileño                  | Brasil             |
| `<CNPJ-BR>`    | CNPJ (persona jurídica BR)     | Brasil             |
| `<CURP-MX>`    | CURP mexicana                  | México             |
| `<RFC-MX>`     | RFC mexicano                   | México             |
| `<NIT-CO>`     | NIT colombiano                 | Colombia           |
| `<CUIT-AR>`    | CUIT/CUIL argentino            | Argentina          |
| `<DNI-AR>`     | DNI argentino                  | Argentina          |
| `<NINO-UK>`    | National Insurance Number (UK) | UK                 |
| `<SSN-US>`     | Social Security Number (EE.UU.)| CCPA               |
| `<DL-US>`      | Driver's License (California)  | CCPA               |

---

## Notas legales por jurisdicción

### Chile — Ley 21.719 (2024)
La Ley 21.719 fue publicada el 13 de diciembre de 2024 y entra en vigencia
el 1 de diciembre de 2026, sustituyendo íntegramente la Ley 19.628 de 1999.
Crea la APDP (Agencia de Protección de Datos Personales) como organismo autónomo
con potestad sancionadora de hasta 20.000 UTM (~1.400 millones CLP). Amplía datos
sensibles (biométricos, geolocalización continua, datos de menores) e introduce el
Delegado de Protección de Datos. El RUT es el identificador nacional único.

### Brasil — LGPD (Lei 13.709/2018)
En vigor desde agosto 2020. Exige base legal explícita para cada tratamiento,
aplica a datos de personas en Brasil independientemente de dónde se procesen.
El CPF identifica a personas físicas; el CNPJ a personas jurídicas. La ANPD
(Autoridade Nacional de Proteção de Dados) es el organismo de control.

### México — LFPDPPP 2010/2025
La ley original data de 2010. Una reforma constitucional de noviembre 2024
eliminó el INAI como autoridad de control. La ley reformada entró en vigor el
21 de marzo de 2025; la nueva autoridad es la SABG (Secretaría Anticorrupción
y Buen Gobierno). La reforma añade obligaciones sobre decisiones automatizadas
e inteligencia artificial. Aplica al sector privado. El sector público se rige
por la Ley General de Protección de Datos Personales en Posesión de Sujetos
Obligados (2017). El CURP identifica a personas físicas (18 chars); el RFC se
usa para obligaciones fiscales (persona física: 13 chars, moral: 12 chars).

### Colombia — Ley 1581/2012
Complementada por el Decreto 1377/2013. La Superintendencia de Industria y
Comercio (SIC) es la autoridad de control. El NIT identifica personas
jurídicas. La cédula de ciudadanía (CC) se detecta solo con contexto previo
para minimizar falsos positivos.

### Argentina — Ley 25.326 (2000)
La AAIP (Agencia de Acceso a la Información Pública) es el organismo de
control. Proyecto de reforma PIDIA en tramitación parlamentaria (junio 2026,
aún no aprobado). El CUIT/CUIL incluye el tipo de persona y dígito verificador.

### UK — UK GDPR / DPA 2018 / DUAA 2025
El marco legal UK consta de tres instrumentos: UK GDPR (en vigor 1 enero 2021),
Data Protection Act 2018, y Data (Use and Access) Act 2025 (DUAA), con Royal
Assent el 19/06/2025 y disposiciones principales en vigor desde el 5 de febrero
de 2026. La DUAA introduce divergencias materiales respecto al RGPD de la UE:
exención de cookies analíticas de consentimiento, recognised legitimate interests
sin test de balance, y mecanismo stop-the-clock en solicitudes de acceso.
Autoridad supervisora: ICO. La Comisión Europea renovó la decisión de adecuación
del UK hasta diciembre 2031. El NINO es el identificador de prestaciones y fiscal.

### CCPA / CPRA — California (EE.UU.)
La CCPA (2018) y su extensión CPRA (vigente desde enero 2023) aplican a
empresas con facturación > 25M USD anuales, o que traten datos de ≥ 100.000
consumidores californianos, o que deriven ≥ 50% de ingresos de la venta de datos.
El CPPA (California Privacy Protection Agency) es el organismo de aplicación.
Regulaciones adicionales aprobadas en septiembre 2025, en vigor desde el 1 de
enero de 2026: datos neurales añadidos como datos sensibles; datos de menores
de 16 años clasificados automáticamente como sensibles; obligación de honrar
señales GPC (Global Privacy Control) como opt-out válido.
No existe ley federal equivalente en EE.UU.

---

## Formatos soportados

| Extensión | Comportamiento                                                        |
|-----------|-----------------------------------------------------------------------|
| `.csv`    | Todas las celdas. Si detecta export Screaming Frog: solo texto libre. |
| `.xlsx`   | Todas las hojas, celda a celda.                                       |
| `.md`     | Línea a línea; preserva formato Markdown.                             |
| `.docx`   | Párrafo por run; preserva estilos (negrita, cursiva, etc.).           |

---

## Limitaciones conocidas

- **Precisión NER:** spaCy puede generar falsos positivos en nombres comunes
  (saludos, títulos). El script filtra los más frecuentes en español.
- **Cédula colombiana:** sin contexto previo (abreviatura CC o palabra "cédula")
  los 6-10 dígitos tienen tasa de falsos positivos alta; se activa solo con contexto.
- **DNI argentino sin puntos:** el patrón de 7-8 dígitos sueltos tiene score bajo
  (0,55) y puede capturar números no relacionados. Revisar salida si hay datos numéricos.
- **LGPD — CPF sin formato:** el patrón de 11 dígitos continuos tiene score bajo (0,60)
  por el mismo motivo. Se recomienda usar datos con puntos y guiones si es posible.
- **Textos cortos (<4 palabras):** la detección de idioma no es fiable; se usa el
  idioma por defecto (primero disponible según modelos instalados).
