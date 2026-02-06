---
title: Rakennusdata
---

## rakennukset.json

- [GeoJSON-standardin](https://geojsonlint.com) mukainen tekstitiedosto

## Geometriatyypit

- "FeatureCollection"-kokoelmatyyppi
- "Polygon"
- "Point"

### coordinates

- lista koordinaattipareista muodossa [[x1, y1], [x2, y2], [x3, y3], ... jne.]
- pisteissä [x, y]
- koordinaatit desimaaliasteina (WGS84)

## properties

### kaupunginosa

- "I", "II", "III" tai "IV"

### tyyppi

- "yksityinen"
- "julkinen"
- "puisto"

### url

- kuvan pysyvä osoite
- null

### start

- rakennusvuosi, jos tiedossa
- muuten vuosi, jona viimeistään rakennettu
- String tai Int

### end

- purkuvuosi, jos tiedossa
- vuosi, jona viimeistään purettu
- String tai Int
- jos vielä pystyssä, null

### oikeudet

- kuvan oikeuksien haltija
