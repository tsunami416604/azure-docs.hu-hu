---
title: Csatlakozás webszolgáltatás-szolgáltatáshoz (WFS) | Microsoft Azure Maps
description: Ismerje meg, hogyan csatlakozhat egy WFS-szolgáltatáshoz, majd lekérdezheti a WFS-szolgáltatást az Azure Maps webes SDK-val és a térbeli I/O-modullal.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334205"
---
# <a name="connect-to-a-wfs-service"></a>Csatlakozás WFS-szolgáltatáshoz

A Web Feature Service (WFS) egy olyan térbeli adatok lekérdezésére szolgáló webszolgáltatás, amely az Open Geospatial Consortium (OGC) által meghatározott szabványosított API-val rendelkezik. A `WfsClient` térbeli I/O-modul osztálya lehetővé teszi a fejlesztők számára, hogy egy WFS-szolgáltatáshoz csatlakozzanak, és adatokat kérdezzenek le a szolgáltatásból.

Az osztály a `WfsClient` következő funkciókat támogatja:

- Támogatott verziók: `1.0.0`, `1.1.0`, és`2.0.0`
- Támogatott szűrőoperátorok: bináris összehasonlítások, logika, `bbox`matematika, érték és .
- A kérelmek `HTTP GET` et csak a kérésekkel teszid.
- Támogatott műveletek:

    | | |
    | :-- | :-- |
    | GetCapabilities (GetCapabilities) | Érvényes WFS-műveletekkel és paraméterekkel rendelkező metaadat-dokumentumot hoz létre |
    | GetFeature funkció | Adatforrásból származó szolgáltatások egy részét adja vissza. |
    | Leírástípusa | A támogatott szolgáltatástípusokat adja vissza. |

## <a name="using-the-wfs-client"></a>A WFS-ügyfél használata

A `atlas.io.ogc.WfsClient` térbeli IO-modul osztálya megkönnyíti a WFS-szolgáltatás lekérdezését és a válaszok GeoJSON-objektumokká alakítását. Ez a GeoJSON objektum ezután más leképezési célokra is használható.

A következő kód lekérdezi a WFS-szolgáltatást, és megjeleníti a visszaadott funkciókat a térképen.

<br/>

<iframe height='700' scrolling='no' title='Egyszerű WFS példa' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Simple WFS példát</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Támogatott szűrők

A WFS-szabvány specifikációja ogc szűrőket használ. Az alábbi szűrőket a WFS-ügyfél támogatja, feltételezve, hogy a hívott szolgáltatás is támogatja ezeket a szűrőket. Egyéni szűrőkarakterláncok adhatók `CustomFilter` át az osztálynak.

**Logikai operátorok**

- `And`
- `Or`
- `Not`

**Értékoperátorok**

- `GmlObjectId`
- `ResourceId`

**Matematikai operátorok**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Összehasonlító operátorok**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

A következő kód bemutatja a különböző szűrők használatát a WFS-ügyféllel.

<br/>

<iframe height='500' scrolling='no' title= 'Példák wfs-szűrőre' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS szűrő példák</a> at Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS szolgáltatáskezelő

A következő kód a WFS-ügyfél segítségével vizsgálja wfs-szolgáltatások. Jelöljön ki egy tulajdonságtípus-réteget a szolgáltatáson belül, és tekintse meg a kapcsolódó jelmagyarázatot.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS szolgáltatáskezelő' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS szolgáltatáskezelőt</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

A nem CORS-kompatibilis végpontokon üzemeltetett WFS-szolgáltatások eléréséhez a `proxyService` CORS-kompatibilis proxyszolgáltatás átadható a WFS-ügyfél beállításának az alábbiak szerint. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [WfsClient között](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Az alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
