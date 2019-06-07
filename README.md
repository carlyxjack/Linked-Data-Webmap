# Linked-Data-Webmap

Deze repository is voor de mensen die graag een webmap applicatie willen bouwen waarbij Linked Data wordt bevraagd. 

Waarschuwing: Men is vrij om de code te (her)gebruiken, maar bij maken van de webmap applicatie is geen rekening gehouden met aspecten, zoals beveiliging. Verder zou iemand de webmap applicatie gebruiksvriendelijker of meer esthetisch kunnen maken. Aangezien ik pas een beginner ben op het gebied van JavaScript, HTML en CSS, kan het zich voordoen dat de code die ik heb gebruikt/geschreven niet geheel volgens standaard is. Het is dus geadviseerd om de code waarschijnlijk nog aan te passen/ laten nakijken door iemand
met meer ervaring op het gebied van webdevelopment en SPARQL voor daadwerkelijk gebruik.

# Korte Handleiding Webmap applicatie

Hierbij zal ik kort toelichten hoe ik de webmap-applicatie heb opgebouwd. 
Begin zoals gewoonlijk met het opbouwen van een HTML file in een code editor, zoals in het code-block hieronder.
Ik heb de volgende titel gegeven: Webmap-applicatie, maar je mag uiteraard een andere naam voor de titel geven.


```<!DOCTYPE html>
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



```<!DOCTYPE html>
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

