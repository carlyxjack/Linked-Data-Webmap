# Linked-Data-Webmap

Deze repository is voor de mensen die graag een webmap applicatie willen bouwen waarbij Linked Data wordt bevraagd. 

Waarschuwing: Men is vrij om de code te (her)gebruiken, maar bij maken van de webmap applicatie is geen rekening gehouden met aspecten, zoals beveiliging. Verder zou iemand de webmap applicatie gebruiksvriendelijker of meer esthetisch kunnen maken. Aangezien ik pas een beginner ben op het gebied van JavaScript, HTML en CSS, kan het zich voordoen dat de code die ik heb gebruikt/geschreven niet geheel volgens standaard is. Het is dus geadviseerd om de code waarschijnlijk nog aan te passen/ laten nakijken door iemand
met meer ervaring op het gebied van webdevelopment en SPARQL voor daadwerkelijk gebruik.

# Handleiding Webmap applicatie

Hierbij zal ik kort toelichten hoe ik de webmap-applicatie heb opgebouwd. 
Begin zoals gewoonlijk met het opbouwen van een HTML file in een code editor, zoals in het code-block hieronder.
Ik heb de volgende titel gegeven: Webmap-applicatie, maar je mag uiteraard een andere naam voor de titel geven.


```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no">

<title>Webmap-applicatie</title>
</head>

<body>

</body>

</html>
``` 

Aangezien je de 'ArcGIS API for JavaScript' wilt gebruiken om de webmap applicatie op te bouwen, kun je gemakkelijk de CDN versie van de API daarvoor gebruiken. De meest geüpdate versie van de API is te vinden op de volgende website. https://developers.arcgis.com/javascript/latest/guide/get-api/

Ik gebruik voor mijn webmap-applicatie de 4.10 versie, maar deze kan momenteel uiteraard geüpdated zijn.



```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no">

<title>Webmap-applicatie</title>
<link rel="stylesheet" href="https://js.arcgis.com/4.10/esri/css/main.css">
<script src="https://js.arcgis.com/4.10/"></script>

</head>

<body>

</body>

</html>
``` 

Vervolgens gebruik ik de volgende functies om een webmap applicatie op te bouwen binnen een **<script> </script>**. Meer informatie over deze functies is te vinden via de volgende link: https://developers.arcgis.com/javascript/latest/api-reference/ . Het daadwerkelijke gebruik van de ArcGIS functies zal ik naderhand toelichten. Eerst zal ik toelichten hoe ik deels de user interface van de webmap-applicatie heb opgebouwd.

```html
    <script>
        require([
            "esri/views/SceneView",
            "esri/WebScene",
            "esri/widgets/Search",
            "esri/layers/GraphicsLayer",
            "esri/Graphic",
            "esri/tasks/support/Query",
            "esri/tasks/QueryTask",
            "esri/geometry/Extent",
            "dojo/i18n!esri/nls/common",
            "esri/widgets/LayerList",
            "esri/geometry/Polygon",
            "esri/request",
            "dojo/dom",
            "dojo/on",
            "dojo/domReady!"

        ], function (SceneView, WebScene, Search, GraphicsLayer, Graphic, Query, QueryTask, Extent, i18n, LayerList, Polygon, esriRequest, dom, on, domReady) {
        
```

# Globale informatie over de data: BRT Linked Data van het Kadaster

Voor mijn webmap-applicate bevraag ik Top10NL/BRT Linked Data van het Kadaster. Normaliter kun je gewoon via de volgende SPARQL endpoint van het Kadaster Linked Data bevragen: https://data.pdok.nl/sparql.

**Een SPARQL endpoint is een webservice die een SPARQL query kan ontvangen en probeert de desbetreffende informatie uit een graph te halen en vervolgens iets doet met de gevonden resultaten.** 

Echter dit vereist dat een gebruiker zelf een SPARQL query zou moeten kunnen schrijven, daarom om te voorkomen dat een gebruiker zelf de SPARQL query moet opstellen, is de webmap-applicatie gemaakt.

Van de BRT Linked data zal ik voornamelijk focussen op gebouwen. 
In mijn webmap-applicatie worden drie verschillende SPARQL queries bevraagd:

  1. Informatie over het geselecteerd BRT-gebouw zelf
  2. CBS-informatie op wijk/buurt niveau waarbinnen het geselecteerde gebouw valt
  3. Informatie ove andere gebouwen (bepaald door de gebruiker) binnen een gegeven afstand van het geselecteerde gebouw
  

Binnen de **body** sectie creëer ik eerst een **div** (**"viewDiv"**) waarbinnen de webmap applicatie en de zijbalk ingevoerd zullen worden. Informatie over de styling van de elementen is te vinden in het CSS bestand en zal niet erg op toegespitst worden.
Vervolgens binnen de div wordt een **andere div** (**"sidebar"**) gecreëerd wat de zijbalk zal vertegenwoordigen.

# Webmap-applicatie bouwen

Het eerste gedeelte van de **body** sectie ziet er als volgt uit en betreft de layout voor de informatie over het geselecteerde gebouw zelf.

```html
<body>

    <div id="viewDiv"> 
        <div id="sidebar">
        
            <button class="closebtn" onclick="document.getElementById('sidebar').style.display='none'"><i
                    class="fas fa-times"></i> //button voor het sluiten van de zijbalk
            </button>
            
            <h3>Informatie over het gebouw</h3>


            <label id="namebuilding"> </label>
            <ul id="entries"> //hier zal de informatie komen van de resultaten van de SPARQL query
            </ul>
```

In het code-blok hierboven kun je de volgende lijn zien: <ul id="entries> Dit is een 'unordered list' wat momenteel leeg staat, maar zal naderhand wanneer men dus op een gebouw klikt een vervolgens een SPARQL query naar een SPARQL endpoint stuurt en vervolgens de resultaten van de SPARQL in de entries tonen.

Ik zal vervolgens eerst laten zien hoe ik de basaal de webmap applicatie heb opgebouwd. Veel van de code is simpelweg ontleend van de sample code die beschikbaar is via de volgende link: https://developers.arcgis.com/javascript/latest/sample-code/

De code-blokken die volgen zitten eveneens in de **script** tag onder de require functies.

Het eerste wat ik doe is een instantie van een webscene maken. Deze webscene van Amsterdam heb ik met de ArcGIS Pro gemaakt (aangezien ik geen 3d van het BRT kon vinden). De WebScence bestaat uit een webservice van BRT data, waarbij ik de hoogte van alle gebouwen op een vaste hoogte hebt vastgesteld.
Vervolgens heb ik de WebScene geüpload naar ArcGIS Online en daarna gebruik ik het id van het portal item om de webscene op te halen in de webmap-applicatie.

```javascript
            var scene = new WebScene({
                portalItem: { // autocasts as new PortalItem()
                    id: "cc5f739f95354259818233640d31c7dc"
                }
            });


```
Vervolgens creëer je een SceneView, waarbij een 3d weergave wordt gegeven van de WebScene met behulp van WebGL.
De container betaat uit een DOM element die de SceneView zal weergeven, in dit geval betreft het de **#ViewDiv**.
Bij map geef je een instantie op van een object dat in de View weergeven zal worden, in dit geval betreft het de scene.
Bij **postition** heb ik de coordinaten van Amsterdam opgegeven. 

```javascript
            var view = new SceneView({
                container: "viewDiv",  //deze is in de body sectie aangemaakt
                map: scene,
                zoom: 8,  
                popupEnabled : false,
                camera: {
                    position: [4.89516, 52.370216], // coordinaten van Amsterdam
                    tilt: 81,
                    heading: 50

                },
                highlightOptions: {
                    color: [0, 255, 255],
                    fillOpacity: 0.6
                }
                // Sets center point of view using longitude,latitude
            });
```

Als extraatje heb ik een searchWidget gemaakt en eveneens gevoegd in de webmap-applicatie

```javascript
            var searchWidget = new Search({
                view: view
            });

            // Add the search widget to the top right corner of the view
            view.ui.add(searchWidget, {
                position: "bottom-left"
            });

```
# De eerste SPARQL query

Deels van de webmap-applicatie hebben we net opgebouwd. Echter we willen nu iets doen waarbij er iets gebeurt wanner men op één van de 
gebouwen klikt. 
Daarbij zijn de volgende coderegels in ieder geval daarbij nodig. Lees gerust de aanvullende commentaar voor de functies van de coderegels.

```javascript
          scene.when(function () {

                var sceneLayer = scene.layers.getItemAt(0); // hierbij haal je eerste laag van de WebScene in geval de BRT webservice
                var highlight = null;
                view.whenLayerView(sceneLayer) // Hierbij haal je de LayerView op van de laag (BRT webservice)
                    .then(function (sceneLayerView) {



                        view.on("click", function (event) { // Hierbij vindt de klik-event plaats
                            view.hitTest(event) // zorgt ervoor dat je de graphic(s) terugkrijgt van waar je op geklikt hebt
                                .then(function (response) {

                                    document.getElementById("sidebar").style.display = "inline";
                                    const graphic = response.results.filter(function (result) { \\ de graphic 
                                        
                                        var id = result.graphic.attributes.lokaalid; \\ het verkrijgen van het id van de graphic, in dit geval zijn we geïnteresseerd in de lokaalid
                                        // Vervolgens haal je de "namebuilding" HTML element op en bewaar je de lokaalid in de dataset attribute "numBuilding" om daar de lokaalid vervolgens in te bewaren. Dit id halen we op wanneer we de tweede en de derde SPARQL query gaan uitvoeren.
                                        
                                        var namebuilding = document.getElementById("namebuilding")
                                        namebuilding.textContent = id;
                                        namebuilding.dataset.numBuilding = id;
            
```
Na het klikken van een gebouw willen we eigenlijk een request sturen naar de volgende SPARQL endpoint : https://data.pdok.nl/sparql. Echter daarbij moeten we eerst een SPARQL query opstellen om die vervolgens mee te kunnen geven aan zo een request.

De eerste query is grotendeels ingevuld, zoals in het onderstaande code-blok te zien is, maar wordt deels aangevuld door het **lokaalid** dat we hebben gekregen wanneer we op een gebouw hebben geklikt. Informatie het schrijven van een SPARQL query wordt in deze handleiding niet toegespitst. Het kadaster heeft een tutorial beschikbaar om de basissyntax van SPARQL onder de knie te krijgen:
https://data.labs.pdok.nl/presentations/Kadaster-SPARQL-Tutorial.html#/


```javascript
                                        var query1 = "PREFIX top10nl: <http://brt.basisregistraties.overheid.nl/def/top10nl#>\n\
                                                    PREFIX brt: <http://brt.basisregistraties.overheid.nl/def/top10nl#>\n\
                                                    PREFIX def: <http://betalinkeddata.cbs.nl/def/83487NED#>\n\
                                                    PREFIX dimension: <http://betalinkeddata.cbs.nl/def/dimension#>\n\
                                                    PREFIX gemeente: <http://betalinkeddata.cbs.nl/regios/2016/id/gemeente-geografisch/>\n\
                                                    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>\n\
                                                    PREFIX geo: <http://www.opengis.net/ont/geosparql#>\n\
                                                    PREFIX geof: <http://www.opengis.net/def/function/geosparql/>\n\
                                                    PREFIX uom: <http://www.opengis.net/def/uom/OGC/1.0/>\n\
                                                    PREFIX cbs: <http://betalinkeddata.cbs.nl/def/cbs#>\n\
                                                    PREFIX cs: <http://purl.org/vocab/changeset/schema#>\n\
                                                    PREFIX cb: <http://cbasewrap.ontologycentral.com/vocab#>\n\
                                                    \n\
                                                    SELECT DISTINCT ?object ?name"


                                        query3 = " where  {\n\
                                                    VALUES ?s {<http://brt.basisregistraties.overheid.nl/top10nl/id/gebouw/"+ id + ">  }\n\
                                                ?s ?predicate ?object.\n\
                                                ?predicate rdfs:label ?name.\n\
                                                ?s geo:hasGeometry/geo:asWKT ?Gebouwgeo.}"


                                        query3 = query1.concat(query3);
```


Vervolgens gaan we een de parameters opgeven die nodig zijn om aan request zodadelijk mee te geven. 

```javascript
                                        var options = {
                                            body: query3,
                                            responseType: 'json',
                                            headers: {
                                                Accept: 'application/sparql-results+json',
                                                'Content-Type': 'application/sparql-query'
                                            }
                                        };
```

Uiteindelijk kunnen we de uiteindelijke request naar de SPARQL endpoint sturen

```javascript
     esriRequest("https://data.pdok.nl/sparql", options).then(function (response) {
                                            var dic = {} // Het maken van een lege dictionary waar de unieke waarden van de reponse van de SPARQL endpoint aan toegevoegd worden

                                            var entries = document.getElementById('entries'); // het ophalen van de HTML element entries
                                            entries.innerHTML = ""; // het leegmaken van de entries inhoud indien er nog inhoud van de vorige request erin nog verwerkt is.

                                            for (i = 0; i < response.data.results.bindings.length; i++) {
                                                var name = response.data.results.bindings[i].name.value;
                                                var value = response.data.results.bindings[i].object.value;
                                                dic[name] = value;




                                            }

                                            // Heb een tweede for-loop gebruikt om over de unieke waarden te loopen, geen idee of het gebruik van een tweede for-loop wel of niet efficiënt is.
                                            for (var key in dic) {

                                                var entry = document.createElement('li');
                                                entry.textContent = key + ": ";
                                                var value = dic[key];
                                                var start = value.startsWith("http");

                                                var link = document.createElement('a');
                                                link.textContent = value
                                                link.dataset.identifier = value
                                                var whiteline = document.createElement('p');

                                                if (start) {
                                                    link.style.textDecoration = "underline";

                                                    link.href = value;
                                                    link.target = "_blank";

                                                }
                                                else {
                                                    link.href = '#'
                                                    link.style.pointerEvents = "none";
                                                }






                                                entry.appendChild(link);
                                                entries.appendChild(entry);
                                                entries.appendChild(whiteline);




                                            }
                                            var other = document.getElementById('otherInfo');
                                            other.style.display = "";



                                        });



                                    });


                                });
                        });





                    });
            });
```
