---
theme: dashboard
title: Rakennuskannan kehitys
toc: false
---

# Rakennuskannan kehitys

```js
import {rakennukset} from "/data/rakennukset.json.js";
import {ilmakuvat} from "/data/ilmakuvat.json.js";

const kuvat = new Map();
for (let y in ilmakuvat) {
    kuvat.set(y, html`<img src="${ilmakuvat[y].url}">`);
}
```

<div id="grid1" class="grid grid-cols-2">
<div id="sidepanel" class="card">

<label for="yearInput">Vuosi</label>
<input id="yearInput" type="number" value=1900 min=1900 max=2025 step=10 size="4">
<div id="picturePanel">
    <figure>
        ${kuvat.get(String(year))}
        <figurecaption>
            ${ilmakuvat[year].caption}
        </figurecaption>
    </figure>
</div>

```js
const year = Generators.input(yearInput);
```

</div>

<div id="mapPanel" class="card">

```js
const mapDiv = display(document.createElement("div"));
mapDiv.style = "height: 700px;";

/*function onLocationFound(e) {
    var radius = e.accuracy;
    L.circle(e.latlng, radius).addTo(map).bindPopup("Olet jossain täällä").openPopup();
}*/

const map = L.map(mapDiv, {
    minZoom: 14,
    maxZoom: 17,
    maxBounds: [[62.588, 29.73081], [62.635, 29.77592]],
})
  .setView([62.599977, 29.756126], 14);
  /*.locate({
    watch: false,
    setView: true,
    maxZoom: 15,
  })
  .on('locationfound', onLocationFound);*/

L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
  attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
})
.addTo(map);

function buildingPopUp(feature) {
    if (feature.properties.url != null) {
        return html`<div id="buildingPopUp" class="popup"><h2>${feature.properties.nimi}</h2><figure><img src="${feature.properties.url}"><figcaption> &copy; ${feature.properties.oikeudet}<figcaption></figure><p>${feature.properties.teksti}</p><footer>${feature.properties.kaupunginosa}</footer></div>`;
        }
    else {
        return html`<div id="buildingPopUp" class="popup"><h2>${feature.properties.nimi}</h2><p>${feature.properties.teksti}</p><footer>${feature.properties.kaupunginosa}</footer></div>`;
    }
};

L.geoJSON(rakennukset, {
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
            maxWidth: 600,
            maxHeight: 600,
        })
    },
    filter: function (feature, layer) {
        return feature.properties.start <= year && year <= feature.properties.end;
    }
}).addTo(map);

var mapPanel = document.getElementById('mapPanel');
var parent = mapPanel.parentNode;
parent.insertBefore(mapPanel, parent.firstChild);
```
</div>
</div>

<style>

#observablehq-footer {
    margin-top: 4px;
}

.popup {
  display: flex;
  flex-direction: column;
  align-items: left;
  text-wrap: balance;
  max-height: 80%;
  max-width: 100%;
  min-width: 200px;
}

.popup h2 {
    font-size: 16px;
    text-align: left;
    font-family: var(--sans-serif);
    font-style: bold;
}

.popup p {
    font-size: 14px;
    margin-top: 0px;
    margin-bottom: 2px;
    text-align: justify;
    font-family: var(--serif);
}

.popup footer {
    font-size: 12px;
    margin-top: 4px;
    text-align: left;
    font-style: italic;
    font-family: var(--serif);
}

.popup img {
    max-height: 100%;
    min-height: 200px;
    max-width: 100%;
    min-width: 200px;
    aspect-ratio: auto;
}

.popup figure {
    margin-top: 1px;
    margin-bottom: 0px;
}

figure {
    margin-top: 6px;
    margin-bottom: 6px;
}

figurecaption {
    font-size: 14px;
    margin-bottom: 0px;
}

label {
    font-size: 20px;
    padding: 2px;
}

#yearInput {
    font-size: 18px;
    padding: 2px;
    width: 4em;
}

#largeImage {
    max-height: 100%;
    min-height: 300px;
    max-width: 100%;
    min-width: 300px;
    aspect-ratio: auto;
}

</style>
