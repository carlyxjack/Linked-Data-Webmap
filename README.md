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

# Informatie over het opbouwen van de User Interface

Voor mijn webmap-applicate bevraag ik Top10NL/BRT Linked Data van het Kadaster. Normaliter kun je gewoon via de volgende SPARQL endpoint van het Kadaster Linked Data bevragen: https://data.pdok.nl/sparql.

**Een SPARQL endpoint is een webservice die een SPARQL query kan ontvangen en probeert de desbetreffende informatie uit een graph te halen en vervolgens iets doet met de gevonden resultaten.** 

Echter dit vereist dat een gebruiker zelf een SPARQL query zou moeten kunnen schrijven, daarom om te voorkomen dat een gebruiker zelf de SPARQL query moet opstellen, is de webmap-applicatie gemaakt.

Van de BRT Linked data zal ik voornamelijk focussen op gebouwen. 
In mijn webmap-applicatie worden drie verschillende SPARQL queries bevraagd:

  1. Informatie over het geselecteerd BRT-gebouw zelf
  2. CBS-informatie op wijk/buurt niveau waarbinnen het geselecteerde gebouw valt
  3. Informatie ove andere gebouwen (bepaald door de gebruiker) binnen een gegeven afstand van het geselecteerde gebouw
  

Binnen de **body** sectie creëer ik eerst een **div** ("viewDiv") waarbinnen de webmap applicatie en de zijbalk ingevoerd zullen worden. Informatie over de styling van de elementen is te vinden in het CSS bestand en zal niet erg op toegespitst worden.
Vervolgens binnen de div wordt een **andere div** ("sidebar") gecreëerd wat de zijbalk zal vertegenwoordigen.

Het eerste gedeelte van de body sectie ziet er als volgt uit en betreft de layout voor de informatie over het geselecteerde gebouw zelf.

```html
<body>

    <div id="viewDiv"> 
        <div id="sidebar">
        
            <button class="closebtn" onclick="document.getElementById('sidebar').style.display='none'"><i
                    class="fas fa-times"></i> \\ button voor het sluiten van de zijbalk
            </button>
            
            <h3>Informatie over het gebouw</h3>


            <label id="namebuilding"> </label>
            <ul id="entries"> \\hier zal de informatie komen van de resultaten van de SPARQL query
            </ul>
```





