---
title: Nyílt térinformatikai konzorcium (OGC) Térkép réteg hozzáadása | Microsoft Azure térképek
description: Megtudhatja, hogyan fedi le egy OGC-Térkép réteget a térképen, és hogyan használhatja a OgcMapLayer osztály különböző lehetőségeit.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c41ee293c853572ec9e1f9dd3edf001c805924d3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402774"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Térkép réteg hozzáadása a Nyílt térinformatikai konzorciumból (OGC)

A `atlas.layer.OgcMapLayer` osztály a webes leképezési szolgáltatások (WMS) képi és webes leképezési szolgáltatásait (WMTS) ábrázolja a térképen. A WMS egy szabványos protokoll, amelyet a OGC fejlesztett ki a georeferens-leképezési lemezképek interneten keresztüli kiszolgálásához. A rendszerkép georeferencing a képek földrajzi helyre való társításának folyamatai. A WMTS a OGC által fejlesztett szabványos protokoll is. A szolgáltatás az előre megjelenített és a georeferens Térkép csempék kiszolgálására szolgál.

A következő részekben a `OgcMapLayer` osztály által támogatott webes leképezési szolgáltatás funkcióit vázoljuk.

**Webes leképezési szolgáltatás (WMS)**

- Támogatott verziók: `1.0.0`, `1.1.0`, `1.1.1`és `1.3.0`
- A szolgáltatásnak támogatnia kell a `EPSG:3857` kivetítési rendszerét, vagy a szolgáltatásnak képesnek kell lennie az újravetítések kezelésére.
- A GetFeatureInfo használatához a szolgáltatásnak támogatnia kell a `EPSG:4326` vagy a kezelői újravetítéseket. 
- Támogatott műveletek:

    | | |
    | :-- | :-- |
    | GetCapabilities | A szolgáltatás metaadatainak beolvasása a támogatott funkciókkal |
    | GetMap | Egy adott régióhoz tartozó térképi rendszerkép lekérése |
    | GetFeatureInfo | A szolgáltatással kapcsolatos alapul szolgáló adatokat tartalmazó `feature_info`beolvasása. |

**Webes leképezési csempe szolgáltatás (WMTS)**

- Támogatott verziók: `1.0.0`
- A csempéknek szögleteseknek kell lenniük, például `TileWidth == TileHeight`.
- Támogatott CRS: `EPSG:3857` vagy `GoogleMapsCompatible` 
- A TileMatrix azonosítójának olyan egész számnak kell lennie, amely megfelel a Térkép nagyítási szintjének. Az Azure-térképeken a nagyítási szint `"0"` és `"22"`közötti érték. Így a `"0"` támogatott, de `"00"` nem támogatott.
- Támogatott műveletek:

    | | |
    | :-- | :-- |
    | GetCapabilities | A támogatott műveletek és funkciók beolvasása |
    | GetTile | Képek lekérése egy adott csempéhez |

## <a name="overlay-an-ogc-map-layer"></a>OGC-Térkép rétegének átfedése

A `url` lehet a szolgáltatás alap URL-címe vagy egy teljes URL-cím a szolgáltatás képességeinek beszerzéséhez. A megadott adatoktól függően előfordulhat, hogy a WFS-ügyfél számos szabványos URL-formátumot próbál megállapítani a szolgáltatás kezdeti eléréséhez.

A következő kód bemutatja, hogyan fedi le az OGC Térkép rétegét.

<br/>

<iframe height='700' scrolling='no' title='Példa OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC Térkép rétegét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>OGC Térkép rétegének beállításai

Az alábbi minta bemutatja a különböző OGC-Térkép réteg beállításait. A kód toll szerkesztéséhez kattintson a jobb felső sarokban található kód toll gombra.

<br/>

<iframe height='700' scrolling='no' title='OGC Térkép rétegének beállításai' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC web Map Service Explorer

Az alábbi eszköz a webes térképi szolgáltatásokból (WMS) és a web Map csempe-szolgáltatásokból (WMTS) származó képeket fedi fel rétegként. Megadhatja, hogy a szolgáltatás mely rétegeit jelenítse meg a térképen. Ezen rétegek társított jelmagyarázatait is megtekintheti.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC web Map Service Explorer' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC web Map Service explorert</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A Térkép beállításait a proxy szolgáltatás használatára is megadhatja. A proxy szolgáltatás lehetővé teszi az olyan tartományokban tárolt erőforrások betöltését, amelyeken nincs engedélyezve a CORs.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Tekintse meg az alábbi cikkeket, amelyek tartalmazzák a térképekhez felvehető mintakód-mintákat:

> [!div class="nextstepaction"]
> [Kapcsolódás WFS szolgáltatáshoz](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alapvető műveletek kihasználása](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)
