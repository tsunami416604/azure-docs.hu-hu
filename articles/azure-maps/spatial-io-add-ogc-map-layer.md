---
title: Nyílt térinformatikai konzorcium (OGC) térképréteg hozzáadása | Microsoft Azure Maps
description: Megtudhatja, hogyan fedhet le egy OGC-térképréteget a térképen, és hogyan használhatja az OgcMapLayer osztály különböző beállításait.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334285"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Térképréteg hozzáadása az Open Geospatial Consortium (OGC) területről

Az `atlas.layer.OgcMapLayer` osztály átfedheti a Web Map Services (WMS) képeit és a WMTS-képeket a térképen. A WMS egy szabványos protokoll, amelyet az OGC fejlesztett ki a georeferencia-térképképek interneten történő kiszolgálására. A kép georeferencálása a kép földrajzi helyhez való társításának folyamata. WMTS is egy szabványos protokoll által kifejlesztett OGC. Előre renderelt és georeferenciaű térképcsempék kiszolgálására tervezték.

A következő szakaszok ismertetik az `OgcMapLayer` osztály által támogatott webtérkép-szolgáltatás szolgáltatásait.

**Webtérkép-szolgáltatás (WMS)**

- Támogatott verziók: `1.0.0`, `1.1.0`, `1.1.1`, és`1.3.0`
- A szolgáltatásnak `EPSG:3857` támogatnia kell a vetítési rendszert, vagy kezelnie kell az újravetítéseket.
- A GetFeatureInfo használatához `EPSG:4326` a szolgáltatásnak támogatnia kell vagy kezelnie kell az újravetítéseket. 
- Támogatott műveletek:

    | | |
    | :-- | :-- |
    | GetCapabilities (GetCapabilities) | A szolgáltatás metaadatainak beolvasása a támogatott képességekkel |
    | GetMap (GetMap) | Egy adott terület térképképének lekérése |
    | GetFeatureInfo | Lekéri `feature_info`, amely a szolgáltatás alapjául szolgáló adatokat tartalmaz |

**Webtérkép csempeszolgáltatás (WMTS)**

- Támogatott verziók:`1.0.0`
- Csempe kell négyzet, `TileWidth == TileHeight`úgy, hogy .
- CRS támogatott: `EPSG:3857` vagy`GoogleMapsCompatible` 
- A TileMatrix azonosítójának egész számnak kell lennie, amely megfelel a térképen lévő nagyítási szintnek. Az azure-térképen a nagyítási `"0"` `"22"`szint a és a között van. Tehát `"0"` támogatott, de `"00"` nem támogatott.
- Támogatott műveletek:

    | | |
    | :-- | :-- |
    | GetCapabilities (GetCapabilities) | A támogatott műveletek és szolgáltatások lekérése |
    | GetTile (GetTile) | Egy adott csempe képeinek lekérése |

## <a name="overlay-an-ogc-map-layer"></a>OGC-térképréteg átfedése

A `url` lehet a szolgáltatás alap URL-címe, vagy egy teljes URL-cím a lekérdezéssel a szolgáltatás képességeinek beolvasásához. A megadott adatoktól függően a WFS-ügyfél több szabványos URL-formátumot is megpróbálhat annak meghatározásához, hogy kezdetben hogyan férhet hozzá a szolgáltatáshoz.

A következő kód bemutatja, hogyan fedhet le egy OGC térképréteget a térképen.

<br/>

<iframe height='700' scrolling='no' title='Példa az OGC-térképrétegre' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC Map layer példát</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>OGC térképréteg beállításai

Az alábbi minta bemutatja a különböző OGC térképréteg-beállításokat. A kódtoll szerkesztéséhez kattintson a jobb felső sarokban található kódtoll gombra.

<br/>

<iframe height='700' scrolling='no' title='OGC térképréteg beállításai' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC térképréteg-beállításait</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Ogc webtérkép-szolgáltatás kezelője

A következő eszköz rétegként fedi le a Web Map Services (WMS) és a Web Map Tile Services (WMTS) képeit. Kiválaszthatja, hogy a szolgáltatás mely rétegei jelenjenek meg a térképen. Megtekintheti a társított jelmagyarázatokat is ezekhez a rétegekhez.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Ogc webtérkép-szolgáltatás kezelője' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Map Service explorer by Azure Maps ( ) alkalmazást a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

Megadhatja a proxyszolgáltatás használatához használt térképbeállításokat is. A proxyszolgáltatás lehetővé teszi a CORS-t nem engedélyező tartományokban üzemeltetett erőforrások betöltését.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [OgcMapLayer között](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOpciók](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Lásd a következő cikkeket, amelyek kódmintákat adhat hozzá a térképek:

> [!div class="nextstepaction"]
> [Csatlakozás WFS-szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Az alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
