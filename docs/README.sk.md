# Sparkplug Metriky - NGSI-LD Dátové Modely

Komplexný systém dátových modelov, schém a validácií pre Sparkplug 3.0 metriky s podporou NGSI-LD.

## 📋 Obsah

1. [Úvod](#úvod)
2. [Základné Koncepty](#základné-koncepty)
3. [Štruktúra Projektu](#štruktúra-projektu)
4. [Dátové Modely](#dátové-modely)
5. [Validácie](#validácie)
6. [Príklady Použitia](#príklady-použitia)
7. [Integrácia s NGSI-LD brokermi](#integrácia)

---

## 🎯 Úvod

Tento projekt poskytuje **sémantickú vrstvu** pre Sparkplug 3.0 metriky pomocou:
- **JSON-LD** - prepojené dáta (linked data) s kontextom
- **NGSI-LD** - ETSI štandard pre Context Information Management
- **QUDT** - ontológia pre jednotky a veličiny
- **Schema.org** - základné vlastnosti
- **SHACL** - validácia RDF grafov

### Čo je JSON-LD?

**JSON-LD** (JSON for Linking Data) je formát, ktorý pridáva **význam** (semantics) k JSON dátam. Umožňuje:
- Prepojenie dát s ontológiami
- Jednoznačnú interpretáciu dát (čo znamená "temperature"?)
- Interoperabilitu medzi systémami
- Transformáciu na RDF pre sémantické dotazy

**Príklad:**
```json
{
  "@context": {
    "temperature": "http://schema.org/temperature",
    "unit": "http://qudt.org/schema/qudt/unit"
  },
  "temperature": 23.5,
  "unit": "http://qudt.org/vocab/unit/DEG_C"
}
```

### Čo je NGSI-LD?

**NGSI-LD** je ETSI štandard (GS CIM 009) pre manažment kontextových informácií. Kombinuje:
- JSON-LD pre sémantiku
- Štandardizovaný API model
- Temporálne dotazy (časové rady)
- Geografické dotazy

**Základná štruktúra entity:**
```json
{
  "id": "urn:ngsi-ld:Entity:123",
  "type": "EntityType",
  "property": {
    "type": "Property",
    "value": "hodnota"
  },
  "relationship": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:Other:456"
  }
}
```

---

## 🧩 Základné Koncepty

### 1. Hierarchia Sparkplug v NGSI-LD

```
SparkplugNode (Edge Node)
├── Group ID: "Factory1"
├── Node ID: "EdgeGateway01"
└── hasDevice →
    ├── SparkplugDevice (PLC01)
    │   ├── Device ID: "PLC01"
    │   └── hasMetric →
    │       ├── SparkplugMetric (Temperature)
    │       ├── SparkplugMetric (Pressure)
    │       └── SparkplugMetric (Counter)
    └── SparkplugDevice (PLC02)
        └── hasMetric → ...
```

### 2. Typy vzťahov

**Property (Vlastnosť)** - obsahuje hodnotu:
```json
"temperature": {
  "type": "Property",
  "value": 23.5,
  "observedAt": "2025-10-20T14:30:00Z"
}
```

**Relationship (Vzťah)** - referencia na inú entitu:
```json
"belongsToDevice": {
  "type": "Relationship",
  "object": "urn:ngsi-ld:SparkplugDevice:PLC01"
}
```

### 3. Mapovanie na ontológie

| Sparkplug pojem | NGSI-LD vlastnosť | Mapovaná ontológia |
|----------------|-------------------|-------------------|
| Metric Name | name | schema:name |
| Metric Value | value | qudt:numericValue |
| Unit | unitCode | qudt:unit |
| Quantity | quantityKind | qudt:hasQuantityKind |
| Datatype | dataType | dvcs:sparkplugDataType |

---

## 📁 Štruktúra Projektu

```
metric/
├── catalog/                          # Dátový katalóg (DCAT)
│   ├── data-catalog.jsonld          # Hlavný katalóg
│   ├── datasets.jsonld              # Definície datasetov
│   └── distributions.jsonld         # Distribúcie (schémy)
│
├── schemas/                          # Schémy a modely
│   ├── ngsi-ld/                     # NGSI-LD dátové modely
│   │   ├── SparkplugMetric.jsonld
│   │   ├── SparkplugDevice.jsonld
│   │   └── SparkplugNode.jsonld
│   │
│   ├── json-schema/                 # JSON Schema validácie
│   │   ├── sparkplug-metric.schema.json
│   │   ├── sparkplug-device.schema.json
│   │   └── sparkplug-node.schema.json
│   │
│   └── shacl/                       # SHACL shapes pre RDF
│       └── sparkplug-metric-shapes.ttl
│
├── examples/                         # Praktické príklady
│   ├── 01-simple-metric.jsonld
│   ├── 02-pressure-metric.jsonld
│   ├── 03-device-with-metrics.jsonld
│   └── 04-complete-hierarchy.jsonld
│
├── docs/                            # Dokumentácia
│   └── README.sk.md
│
├── metric-context.jsonld            # JSON-LD kontext
└── ontology.jsonld                  # Ontológia DVCS
```

---

## 📊 Dátové Modely

### SparkplugMetric

Reprezentuje jednu telemetrickú metriku podľa Sparkplug 3.0.

**Povinné vlastnosti:**
- `id` - URI identifikátor (napr. `urn:ngsi-ld:SparkplugMetric:Temperature:Sensor01`)
- `type` - vždy `"SparkplugMetric"`
- `name` - názov metriky (napr. "Temperature", "Pressure")
- `value` - numerická hodnota
- `dataType` - Sparkplug dátový typ (Float, Double, Int32, ...)

**Voliteľné vlastnosti:**
- `unitCode` - QUDT jednotka (napr. `http://qudt.org/vocab/unit/DEG_C`)
- `quantityKind` - druh veličiny (Temperature, Pressure, ...)
- `timestamp` - časová značka merania
- `quality` - kvalita dát (good, bad, uncertain)
- `isHistorical` - príznak historických dát
- `alias` - Sparkplug číselný alias
- `description` - popis

**Vzťahy:**
- `belongsToDevice` → SparkplugDevice

**Príklad:**
```json
{
  "id": "urn:ngsi-ld:SparkplugMetric:Temperature:Sensor01",
  "type": "SparkplugMetric",
  "name": {
    "type": "Property",
    "value": "Temperature"
  },
  "value": {
    "type": "Property",
    "value": 23.5,
    "observedAt": "2025-10-20T14:30:00Z"
  },
  "dataType": {
    "type": "Property",
    "value": "Float"
  },
  "unitCode": {
    "type": "Property",
    "value": "http://qudt.org/vocab/unit/DEG_C"
  }
}
```

### SparkplugDevice

Reprezentuje Sparkplug zariadenie (Device).

**Povinné vlastnosti:**
- `id` - URI identifikátor
- `type` - vždy `"SparkplugDevice"`
- `name` - názov zariadenia
- `deviceId` - Sparkplug Device ID

**Voliteľné vlastnosti:**
- `description` - popis zariadenia
- `status` - stav (online, offline, error)

**Vzťahy:**
- `hasMetric` → [SparkplugMetric] - zoznam metrík
- `belongsToNode` → SparkplugNode

### SparkplugNode

Reprezentuje Sparkplug Edge Node (uzol).

**Povinné vlastnosti:**
- `id` - URI identifikátor
- `type` - vždy `"SparkplugNode"`
- `name` - názov uzla
- `nodeId` - Sparkplug Node ID
- `groupId` - Sparkplug Group ID

**Voliteľné vlastnosti:**
- `description` - popis uzla
- `status` - stav (online, offline, error)

**Vzťahy:**
- `hasDevice` → [SparkplugDevice] - zoznam zariadení

---

## ✅ Validácie

Projekt poskytuje tri vrstvy validácie:

### 1. JSON Schema Validácia

Použitie: Validácia JSON štruktúry pred odoslaním do NGSI-LD brokera.

**Nástroje:**
- [ajv](https://ajv.js.org/) - JavaScript validátor
- [jsonschema](https://python-jsonschema.readthedocs.io/) - Python validátor

**Príklad (JavaScript):**
```javascript
const Ajv = require('ajv');
const schema = require('./schemas/json-schema/sparkplug-metric.schema.json');
const data = require('./examples/01-simple-metric.jsonld');

const ajv = new Ajv();
const validate = ajv.compile(schema);
const valid = validate(data);

if (!valid) {
  console.error(validate.errors);
}
```

**Príklad (Python):**
```python
import json
from jsonschema import validate

with open('schemas/json-schema/sparkplug-metric.schema.json') as f:
    schema = json.load(f)

with open('examples/01-simple-metric.jsonld') as f:
    data = json.load(f)

validate(instance=data, schema=schema)
```

### 2. SHACL Validácia

Použitie: Validácia RDF grafov po konverzii JSON-LD → RDF.

**Nástroje:**
- [pySHACL](https://github.com/RDFLib/pySHACL) - Python validátor
- [Apache Jena SHACL](https://jena.apache.org/documentation/shacl/)

**Príklad (Python):**
```python
from pyshacl import validate
from rdflib import Graph

# Načítaj dáta
data_graph = Graph()
data_graph.parse('examples/01-simple-metric.jsonld', format='json-ld')

# Načítaj SHACL shapes
shacl_graph = Graph()
shacl_graph.parse('schemas/shacl/sparkplug-metric-shapes.ttl', format='turtle')

# Validuj
conforms, results_graph, results_text = validate(
    data_graph,
    shacl_graph=shacl_graph,
    inference='rdfs',
    abort_on_first=False
)

print(f"Validné: {conforms}")
if not conforms:
    print(results_text)
```

### 3. NGSI-LD API Validácia

NGSI-LD brokery (Orion-LD, Scorpio, Stellio) automaticky validujú:
- Správny formát URI identifikátorov
- Existenciu povinných atribútov (id, type)
- Správnu štruktúru Properties a Relationships

---

## 🔧 Príklady Použitia

### Jednoduchá metrika

Súbor: `examples/01-simple-metric.jsonld`

Základná teplota bez vzťahov:
```json
{
  "@context": [
    "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context.jsonld",
    "https://gitlab.presov.sk/vocab/dvcs/metric/-/raw/main/metric-context.jsonld"
  ],
  "id": "urn:ngsi-ld:SparkplugMetric:Temperature:Sensor01",
  "type": "SparkplugMetric",
  "name": {"type": "Property", "value": "Temperature"},
  "value": {"type": "Property", "value": 23.5},
  "dataType": {"type": "Property", "value": "Float"},
  "unitCode": {"type": "Property", "value": "http://qudt.org/vocab/unit/DEG_C"}
}
```

### Metrika s vzťahom

Súbor: `examples/02-pressure-metric.jsonld`

Tlak patriaci k zariadeniu:
```json
{
  "id": "urn:ngsi-ld:SparkplugMetric:Pressure:Sensor02",
  "type": "SparkplugMetric",
  "name": {"type": "Property", "value": "Pressure"},
  "value": {"type": "Property", "value": 101325},
  "belongsToDevice": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:SparkplugDevice:WeatherStation01"
  }
}
```

### Kompletná hierarchia

Súbor: `examples/04-complete-hierarchy.jsonld`

Node → Devices → Metrics:
- 1 Edge Node (EdgeGateway01)
- 2 Devices (PLC01, PLC02)
- 4 Metrics (teploty, počítadlá, rýchlosť)

**Vizualizácia:**
```
EdgeGateway01 (Group: Factory1)
├── PLC01 (Siemens S7-1200)
│   ├── CPU_Temperature: 52.3 °C
│   └── Production_Counter: 15234 ks
└── PLC02 (ABB AC500)
    ├── CPU_Temperature: 48.7 °C
    └── Conveyor_Speed: 1.5 m/s
```

---

## 🔗 Integrácia

### NGSI-LD Brokery

Podporované brokery:
- **Orion-LD** (FIWARE)
- **Scorpio** (FIWARE)
- **Stellio** (EGM)

### Vytvorenie entity v Orion-LD

```bash
curl -X POST 'http://localhost:1026/ngsi-ld/v1/entities' \
  -H 'Content-Type: application/ld+json' \
  -d @examples/01-simple-metric.jsonld
```

### Query na metriky podľa zariadenia

```bash
curl -X GET 'http://localhost:1026/ngsi-ld/v1/entities' \
  -H 'Accept: application/ld+json' \
  -H 'Link: <https://gitlab.presov.sk/vocab/dvcs/metric/-/raw/main/metric-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"' \
  -G -d 'type=SparkplugMetric' \
     -d 'q=belongsToDevice=="urn:ngsi-ld:SparkplugDevice:PLC01"'
```

### Temporálny dotaz (časový rad)

```bash
curl -X GET 'http://localhost:1026/ngsi-ld/v1/temporal/entities' \
  -H 'Accept: application/ld+json' \
  -G -d 'type=SparkplugMetric' \
     -d 'attrs=value' \
     -d 'timerel=between' \
     -d 'timeAt=2025-10-20T00:00:00Z' \
     -d 'endTimeAt=2025-10-20T23:59:59Z'
```

### SPARQL dotazy na RDF

Po konverzii JSON-LD → RDF:

```sparql
PREFIX dvcs: <https://gitlab.presov.sk/vocab/dvcs/-/raw/main/context.jsonld#>
PREFIX qudt: <http://qudt.org/schema/qudt/>
PREFIX schema: <https://schema.org/>

SELECT ?metric ?name ?value ?unit
WHERE {
  ?metric a dvcs:SparkplugMetric ;
          schema:name ?name ;
          qudt:numericValue ?value ;
          qudt:unit ?unit .

  ?metric dvcs:belongsToDevice ?device .
  ?device schema:identifier "PLC01" .
}
```

---

## 📚 Ďalšie Zdroje

### Špecifikácie
- [Sparkplug 3.0](https://sparkplug.eclipse.org/specification/version/3.0/)
- [NGSI-LD (ETSI GS CIM 009)](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_CIM009v010801p.pdf)
- [JSON-LD 1.1](https://www.w3.org/TR/json-ld11/)
- [SHACL](https://www.w3.org/TR/shacl/)

### Ontológie
- [QUDT](http://qudt.org/) - jednotky a veličiny
- [Schema.org](https://schema.org/) - základné vlastnosti
- [DCAT](https://www.w3.org/TR/vocab-dcat-3/) - Data Catalog Vocabulary

### Nástroje
- [JSON-LD Playground](https://json-ld.org/playground/)
- [SHACL Playground](https://shacl.org/playground/)
- [Orion-LD](https://github.com/FIWARE/context.Orion-LD)

---

## 📄 Licencia

CC BY 4.0 - Creative Commons Attribution 4.0 International

---

**Kontakt:** DVCS - Prešov
**Verzia:** 1.0.0
**Dátum:** 2025-10-20
