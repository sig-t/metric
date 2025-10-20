# Sparkplug NGSI-LD Data Models | Sparkplug NGSI-LD Dátové Modely

[![NGSI-LD](https://img.shields.io/badge/NGSI--LD-v1.8-blue)](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_CIM009v010801p.pdf)
[![Sparkplug](https://img.shields.io/badge/Sparkplug-3.0-green)](https://sparkplug.eclipse.org/)
[![JSON-LD](https://img.shields.io/badge/JSON--LD-1.1-orange)](https://www.w3.org/TR/json-ld11/)

Sémantické dátové modely pre Sparkplug 3.0 metriky s podporou NGSI-LD.

**Semantic data models for Sparkplug 3.0 metrics with NGSI-LD support.**

---

## 🇸🇰 Slovensky

Tento projekt poskytuje:
- ✅ **NGSI-LD dátové modely** pre Sparkplug metriky, zariadenia a uzly
- ✅ **JSON Schema validácie** pre kontrolu štruktúry
- ✅ **SHACL shapes** pre RDF validáciu
- ✅ **Katalóg dát (DCAT)** s metadátami
- ✅ **Príklady použitia** od jednoduchých po komplexné hierarchie
- ✅ **Mapovanie na štandardné ontológie** (QUDT, Schema.org)

### 📖 Dokumentácia

➡️ **[Kompletná dokumentácia po slovensky](docs/README.sk.md)**

### 🚀 Rýchly štart

1. **Prečítaj si základy JSON-LD a NGSI-LD** v [dokumentácii](docs/README.sk.md#základné-koncepty)
2. **Prezri si príklady** v priečinku `examples/`
3. **Použij schémy** na validáciu svojich dát
4. **Integruj s NGSI-LD brokerom** (Orion-LD, Scorpio, Stellio)

### 📁 Štruktúra

```
metric/
├── catalog/          # Dátový katalóg (DCAT)
├── schemas/          # NGSI-LD modely, JSON Schema, SHACL
├── examples/         # Praktické príklady
├── docs/             # Dokumentácia
└── *.jsonld          # Kontexty a ontológie
```

### 🧪 Príklad

```json
{
  "@context": "https://gitlab.presov.sk/vocab/dvcs/metric/-/raw/main/metric-context.jsonld",
  "id": "urn:ngsi-ld:SparkplugMetric:Temperature:Sensor01",
  "type": "SparkplugMetric",
  "name": {"type": "Property", "value": "Temperature"},
  "value": {"type": "Property", "value": 23.5},
  "dataType": {"type": "Property", "value": "Float"},
  "unitCode": {"type": "Property", "value": "http://qudt.org/vocab/unit/DEG_C"}
}
```

---

## 🇬🇧 English

This project provides:
- ✅ **NGSI-LD data models** for Sparkplug metrics, devices, and nodes
- ✅ **JSON Schema validations** for structure checking
- ✅ **SHACL shapes** for RDF validation
- ✅ **Data catalog (DCAT)** with metadata
- ✅ **Usage examples** from simple to complex hierarchies
- ✅ **Mapping to standard ontologies** (QUDT, Schema.org)

### 📖 Documentation

➡️ **[Complete documentation in Slovak](docs/README.sk.md)** (English version coming soon)

### 🚀 Quick Start

1. **Read JSON-LD and NGSI-LD basics** in the [documentation](docs/README.sk.md)
2. **Check examples** in `examples/` folder
3. **Use schemas** to validate your data
4. **Integrate with NGSI-LD broker** (Orion-LD, Scorpio, Stellio)

---

## 🔗 Links | Odkazy

- **Sparkplug Specification:** https://sparkplug.eclipse.org/specification/version/3.0/
- **NGSI-LD Specification:** https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_CIM009v010801p.pdf
- **QUDT Ontology:** http://qudt.org/
- **Schema.org:** https://schema.org/

---

## 📄 License | Licencia

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) - Creative Commons Attribution 4.0 International

---

**Maintained by | Spravuje:** DVCS - Prešov
**Version | Verzia:** 1.0.0
**Date | Dátum:** 2025-10-20
