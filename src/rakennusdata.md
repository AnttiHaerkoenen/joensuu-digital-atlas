---
title: Rakennusdata
---
# JSON-tiedosto
<h2></h2>

## rakennukset.json

- [GeoJSON-standardin](https://geojsonlint.com) mukainen tekstitiedosto

## Geometriatyypit

- "FeatureCollection"-kokoelmatyyppi
- "Polygon"
- paljon muitakin vektorityyppejä, mutta tässä aineistossa tarvitaan vain polygoneja

### coordinates

- lista koordinaattipareista muodossa [[x1, y1], [x2, y2], [x3, y3], ... jne.]
- pisteissä [x, y]
- koordinaatit desimaaliasteina (WGS84)

## properties

### osoite

- vain omaan käyttöön
- talon nimi, tontin nro, yleisesti käytetty nimi

### teksti

- rakennuksen kuvailuteksti, yksi kappale
- enintään 500 merkkiä
- nykyisen osoitteen voi mainita heti alussa

<div class="warning" label="Huomio">
Tarkistakaa, ettei tekstissä ole ylimääräisiä näkymättömiä merkkejä, kuten sarkaimia, välilyöntejä tai rivinvaihtoja. Jokaisella merkillä on väliä!
</div>

### tyyppi

- "yksityinen" <b>tai</b>
- "julkinen"

### url

- kuvan pysyvä osoite (kuvatiedoston nimi) <b>tai</b>
- null

<div class="tip" label="Vinkki">
Anna kuvatiedostoille selkeä ja kuvaava nimi. Turvallisinta on käyttää pelkkiä ASCII-merkkejä, mutta ei välilyöntiä tai muita erikoismerkkejä.
</div>

### start

- rakennusvuosi, jos tiedossa <b>tai</b>
- vuosi, jona viimeistään rakennettu
- String tai Int

### end

- purkuvuosi, jos tiedossa <b>tai</b>
- vuosi, jona viimeistään purettu
- String tai Int
- jos vielä pystyssä, 2026

<div class="warning" label="Huomio">
Jos saman rakennuksen tiedot pitää jakaa ajallisesti kahteen osaan, laita vanhemman osan kohtaan <b>end</b> sama vuosi kuin uuden osan kohtaan <b>start</b>.
</div>

### sources

- lähteet lyhyesti
- pitkä versio lähdeluetteloon [Lähteet](/sources)-välilehteen

<div class="tip" label="Vinkki">
Tässä voi käyttää samoja ohjeita kuin historian gradun alaviitteissä.
</div>

### oikeudet

- kuvan oikeuksien haltija

<div class="warning" label="Huomio">
Tarkista kahteen kertaan, että lähteet ja oikeudet on varmasti merkitty oikein.
</div>