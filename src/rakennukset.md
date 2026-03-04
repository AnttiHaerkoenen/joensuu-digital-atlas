---
style: /css/map-page.css
title: Rakennuskannan kehitys
toc: false
---

## Rakennuskannan kehitys 1930&ndash;2000

```js
import {rakennukset} from "/data/rakennukset.json.js";
import {ilmakuvat} from "/data/ilmakuvat.json.js";

const kuvat = new Map();
for (let y in ilmakuvat) {kuvat.set(y, html`<img class="largeImage" src="${ilmakuvat[y].url}" alt="Kuvaa ei saatavilla">`)}
```

<div id="grid" class="grid grid-cols-3" style="grid-auto-rows: auto;">

<div id="mapPanel" class="card grid-rowspan-4 grid-colspan-2">

## Interaktiivinen kartta

```js
const mapDiv = display(document.createElement("div", {"is": "mapDiv"}));
mapDiv.style = "height: 700px;";

// function onLocationFound(e) {
//     var radius = e.accuracy;
//     L.circle(e.latlng, radius).addTo(map).bindPopup("Olet jossain täällä").openPopup();
// }

const map = L.map(mapDiv, {
    minZoom: 14,
    maxZoom: 17,
    maxBounds: [[62.588, 29.73081], [62.635, 29.77592]],
    allowFullScreen: true,
}).setView([62.599977, 29.756126], 14);

// map.locate({
//     watch: false,
//     setView: true,
//     maxZoom: 15,
//   })
//   .on('locationfound', onLocationFound);

L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
  attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
})
.addTo(map);

function buildingPopUp(feature) {
    if (feature.properties.url != null) {
        return html`
        <div id="buildingPopUp" class="popup">
        <h3>${feature.properties.nimi}</h3>
        <figure>
        <img src="${feature.properties.url}">
        <figcaption> &copy; ${feature.properties.oikeudet}<figcaption>
        </figure>
        <p>${feature.properties.teksti}</p>
        <footer>
        <details><summary>Lähteet</summary>${feature.properties.sources}</details>
        </footer>
        </div>`;
        }
    else {
        return html`
        <div id="buildingPopUp" class="popup">
        <h3>${feature.properties.nimi}</h3>
        <p>${feature.properties.teksti}</p>
        <footer>
        <details><summary>Lähteet</summary>${feature.properties.sources}</details>
        </footer>
        </div>`;
        }
    };

// Historiallisen kartan lisäys

var imageUrl = 'https://expo.oscapps.jyu.fi/files/original/2617b1db085b60d176a0a4c2e81afbc40aa6fbdf.jpeg';
var errorOverlayUrl = 'https://cdn-icons-png.flaticon.com/512/110/110686.png';

var overlayMap = L.imageOverlay(
    oldMaps[0].imageUrl, 
    oldMaps[0].latLngBounds, {
    opacity: 1,
    errorOverlayUrl: errorOverlayUrl,
    alt: oldMaps[0].attributionText,
    interactive: true
    });

overlayMap.setUrl(selectMap.imageUrl);
overlayMap.setBounds(selectMap.latLngBounds);
overlayMap.setOpacity((100 - overlayOpacity) / 100);

if (overlayMapsOn) {overlayMap.addTo(map);}

// Rakennukset

var buildings = L.geoJSON(rakennukset,
{
    style: function (feature) {
        switch (feature.properties.tyyppi) {
            case 'julkinen': return {color: "#e20e0e"};
            case 'yksityinen': return {color: "#1c1cc5"};
            case 'puisto': return {color: "#7fd85c"};
        };
    },
    onEachFeature: function (feature, layer) {
        layer.bindPopup(buildingPopUp(feature), {
            keepInView: true,
        })
    },
    filter: function (feature, layer) {
        return feature.properties.start <= year && year <= feature.properties.end;
    }
}).addTo(map);

```

</div>

<div id="yearPicker" class="card grid-rowspan-1">

## Vuosikymmen

<!-- <label for="yearInput">Rakennukset vuodelta</label><br> -->
<input id="yearInput" type="number" value=1930 min=1930 max=2000 step=10 size="5em" required="True">

</div>

<div id="picturePanel" class="card grid-rowspan-2">
    <h2>${year}-luku</h2>
    <figure>
        ${kuvat.get(String(year)) || "Kuvaa ei löydetty"}
        <figurecaption>
            <br>${ilmakuvat.hasOwnProperty(year) ? ilmakuvat[year].caption : ""}
        </figurecaption>
    </figure>
</div>

<div id="mapOptions" class="card grid-rowspan-1">

```js
const oldMaps = [
    {name: "1847", 
    imageUrl: "https://expo.oscapps.jyu.fi/files/original/2617b1db085b60d176a0a4c2e81afbc40aa6fbdf.jpeg", 
    attributionText: "Gyldén, C. W. 1847",
    latLngBounds: L.latLngBounds([[62.588, 29.73081], [62.610, 29.77592]]),
    },
    {name: "1928",
    imageUrl: "https://raw.githubusercontent.com/AnttiHaerkoenen/joensuu-digital-atlas/refs/heads/main/src/img/maps/1928.jpg",
    attributionText: "Joensuun kaupungin asemakaava. KA Kaupunkikartat, Joensuu Ieb* 3",
    latLngBounds: L.latLngBounds([[62.57884, 29.72547], [62.61433, 29.80331]]),
    },
    {name: "1948", 
    imageUrl: "https://timomeriluoto.kapsi.fi/KARTAT/Kaupunkikartat/Joensuu%20asemakaava%201948.jpg", 
    attributionText: "MML / Timo Meriluoto",
    latLngBounds: L.latLngBounds([[62.588, 29.7307], [62.610, 29.776]]),
    },
];
const overlayMapsOn = view(Inputs.toggle({label: "Vanhat kartat"}));
```

```js
const selectMap = view(Inputs.select(
    oldMaps, {
        label: "Valitse kartta",
        width: "5em",
        format: (t) => t.name,
        value: oldMaps.find((t) => t.name === "1847"),
        disabled: !overlayMapsOn,
        }
    ));

const overlayOpacity = view(Inputs.range(
    [0, 100], {
        label: "Läpinäkyvyys, %",
        value: 0, 
        step: 5, 
        width: "15em",
        disabled: !overlayMapsOn,
        }
    ));

```

</div>

```js
const year = Generators.input(yearInput);
```

</div>
