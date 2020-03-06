---
title: Kapcsolódás a web Feature Service (WFS) szolgáltatáshoz | Microsoft Azure térképek
description: Ismerje meg, hogyan csatlakozhat egy WFS szolgáltatáshoz, majd hogyan kérdezheti le a WFS szolgáltatást a Azure Maps web SDK és a térbeli IO modul használatával.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402751"
---
# <a name="connect-to-a-wfs-service"></a>Kapcsolódás WFS szolgáltatáshoz

A webszolgáltatások (WFS) egy olyan webszolgáltatás, amely szabványosított API-val rendelkező térbeli adatainak lekérdezésére szolgál, amelyet a Nyílt térinformatikai konzorcium (OGC) határoz meg. A térbeli IO modul `WfsClient` osztálya lehetővé teszi a fejlesztők számára a WFS-szolgáltatásokhoz való kapcsolódást és a szolgáltatás adatainak lekérdezését.

A `WfsClient` osztály a következő funkciókat támogatja:

- Támogatott verziók: `1.0.0`, `1.1.0`és `2.0.0`
- Támogatott szűrési operátorok: bináris összehasonlítás, logika, matematikai, érték és `bbox`.
- A kérelmek csak `HTTP GET` használatával készültek.
- Támogatott műveletek:

    | | |
    | :-- | :-- |
    | GetCapabilities | Metaadat-dokumentum létrehozása érvényes WFS-műveletekkel és-paraméterekkel |
    | GetFeature | Egy adatforrás funkcióinak kiválasztását adja vissza. |
    | DescribeFeatureType | A támogatott szolgáltatások típusait adja vissza. |

## <a name="using-the-wfs-client"></a>A WFS-ügyfél használata

A térbeli IO-modul `atlas.io.ogc.WfsClient` osztálya megkönnyíti a WFS-szolgáltatás lekérdezését és a válaszok GeoJSON objektummá alakítását. Ezt a GeoJSON-objektumot ezután más leképezési célokra is használhatja.

A következő kód lekérdez egy WFS szolgáltatást, és megjeleníti a visszaadott funkciókat a térképen.

<br/>

<iframe height='700' scrolling='no' title='Egyszerű WFS példa' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>egyszerű WFS példáját</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Támogatott szűrők

A WFS standard specifikációja OGC-szűrőket használ. Az alábbi szűrőket a WFS-ügyfél támogatja, feltéve, hogy a meghívott szolgáltatás is támogatja ezeket a szűrőket. Az egyéni szűrő sztringek átadhatók a `CustomFilter` osztályba.

**Logikai operátorok**

- `And`
- `Or`
- `Not`

**Érték operátorok**

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

A következő kód a különböző szűrők használatát mutatja be a WFS-ügyféllel.

<br/>

<iframe height='500' scrolling='no' title= 'Példák a WFS szűrésére' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS-szűrési példákat</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS Service Explorer

A következő kód a WFS-ügyfelet használja a WFS szolgáltatások megismerésére. Válasszon egy tulajdonság típusú réteget a szolgáltatásban, és tekintse meg a társított jelmagyarázatot.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS Service Explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS Service explorert</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A proxy szolgáltatással olyan tartományokban tárolt erőforrásokat is betöltheti, amelyek nincsenek engedélyezve a CORs. Először meg kell határoznia egy változót a proxy szolgáltatás URL-címének tárolásához és a WFS-ügyfél `proxyService` beállításának beállításához. Ha egy proxy szolgáltatást szeretne megjeleníteni a felhasználó számára, adjon hozzá egy felhasználói adatbevitelt a felhasználói FELÜLEThez. Töltse be a szolgáltatás URL-címét a bevitelre való kattintáskor. A következő kódrészletek azt mutatják be, hogyan használható a proxy szolgáltatás.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

Az alábbi HTML-kódrészlet megfelel a fenti JavaScript-kódnak:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
