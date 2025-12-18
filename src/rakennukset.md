---
theme: dashboard
title: Rakennuskannan kehitys
toc: false
---

# Rakennuskannan kehitys

```js
const year = view(Inputs.range([1900, 2025], {step: 5, value: 1900}));
```

```js
import {rakennukset} from "/data/rakennukset.json.js";

const div = display(document.createElement("div"));
div.style = "height: 600px;";

/*function onLocationFound(e) {
    var radius = e.accuracy;
    L.circle(e.latlng, radius).addTo(map).bindPopup("Olet jossain täällä").openPopup();
}*/

const map = L.map(div, {
    minZoom: 14,
    maxZoom: 17,
    maxBounds: [[62.588, 29.73081], [62.625, 29.77592]],
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
    return html`<div class="popup">
    <h2>${feature.properties.nimi}</h2>
    <figure>
    <img src="${feature.properties.url}">
    <figcaption> &copy; ${feature.properties.oikeudet} </figcaption>
    </figure>
    <p>${feature.properties.teksti}</p>
    </div>`;
}

L.geoJSON(rakennukset, {
    style: function (feature) {
        switch (feature.properties.tyyppi) {
            case 'julkinen': return {color: "#ff0000"};
            case 'yksityinen': return {color: "#0000ff"};
            case 'puisto': return {color: "#00ff00"};
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
```

<style>

.popup {
  display: flex;
  flex-direction: column;
  align-items: left;
  text-wrap: balance;
  max-height: 90%;
  max-width: 100%;
  min-width: 200px;
}

.popup h2 {
    text-align: left;
    font-family: var(--sans-serif);
}

.popup p {
    font-size: 14px;
    margin-top: 2px;
    text-align: justify;
    font-family: var(--serif);
}

.popup img {
    max-height: 100%;
    min-height: 150px;
    max-width: 100%;
    min-width: 150px;
    aspect-ratio: auto;
}

.popup figure {
    margin-top: 1px;
    margin-bottom: 1px;
}

.popup figcaption {
    font-size: 12px;
    font-style: italic;
}

</style>
