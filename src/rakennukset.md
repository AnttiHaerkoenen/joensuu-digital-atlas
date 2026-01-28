---
style: /css/map-page.css
title: Rakennuskannan kehitys
toc: false
---

## Rakennuskannan kehitys 1900&ndash;2025

```js
import {rakennukset} from "/data/rakennukset.json.js";
import {ilmakuvat} from "/data/ilmakuvat.json.js";

const kuvat = new Map();
for (let y in ilmakuvat) {kuvat.set(y, html`<img class="largeImage" src="${ilmakuvat[y].url}" alt="Kuvaa ei saatavilla">`)}
```

<div id="grid1" class="grid grid-cols-2" style="grid-auto-rows: auto;">
<div id="grid2" class="grid grid-cols-1">
<div id="yearPicker" class="card">

<form>
<label for="yearInput">Vuosikymmen</label>
<input id="yearInput" type="number" value=1900 min=1900 max=2000 step=10 size="5em" required="True">
</form>

<!-- ```js
const yearInput = view(Inputs.range(
    [1900, 2000], {
        value: 1900,
        step: 10,
        width: "15em",
        label: "Vuosikymmen",
    }
))
``` -->

</div>
<div id="mapPicker" class="card">

```js
const overlayMapsOn = view(Inputs.toggle({label: "Vanhat kartat"}));
```

</div>
</div>

<div id="grid3" class="grid grid-cols-1">
<div id="overlayDiv" class="card">

```js
const overlayOpacity = view(Inputs.range(
    [0, 100], {
        value: 0, 
        step: 10, 
        width: "15em",
        label: "Läpinäkyvyys, %",
        disabled: !overlayMapsOn,
        }
    ));
```

</div>
<div id="mapPicker" class="card">

```js
const oldMaps = [
    {name: "1847", 
    imageUrl: "https://expo.oscapps.jyu.fi/files/original/2617b1db085b60d176a0a4c2e81afbc40aa6fbdf.jpeg", 
    attributionText: "Gyldén, C. W. 1847",
    latLngBounds: L.latLngBounds([[62.588, 29.73081], [62.610, 29.77592]]),
    },
    {name: "1948", 
    imageUrl: "https://timomeriluoto.kapsi.fi/KARTAT/Kaupunkikartat/Joensuu%20asemakaava%201948.jpg", 
    attributionText: "MML / Timo Meriluoto",
    latLngBounds: L.latLngBounds([[62.588, 29.7307], [62.610, 29.776]]),
    },
];

const selectMap = view(
  Inputs.select(oldMaps, {
    label: "Valitse kartta",
    width: "5em",
    format: (t) => t.name,
    value: oldMaps.find((t) => t.name === "1847"),
    disabled: !overlayMapsOn,
  })
)
```

</div>
</div>

<div id="mapPanel" class="card">

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
        return html`<div id="buildingPopUp" class="popup"><h3>${feature.properties.nimi}</h3>
        <figure><img src="${feature.properties.url}"><figcaption> &copy; ${feature.properties.oikeudet}<figcaption></figure>
        <p>${feature.properties.teksti}</p><footer>${feature.properties.kaupunginosa}</footer></div>`;
        }
    else {
        return html`<div id="buildingPopUp" class="popup"><h3>${feature.properties.nimi}</h3>
        <p>${feature.properties.teksti}</p><footer>${feature.properties.kaupunginosa}</footer></div>`;
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
<div id="sidepanel" class="card">

<div id="picturePanel">
    <h2>${year}-luku</h2>
    <figure>
        ${kuvat.get(String(year)) || "Kuvaa ei löydetty"}
        <figurecaption>
            ${ilmakuvat.hasOwnProperty(year) ? ilmakuvat[year].caption : ""}
        </figurecaption>
    </figure>
</div>

```js
const year = Generators.input(yearInput);
```

</div>
</div>
