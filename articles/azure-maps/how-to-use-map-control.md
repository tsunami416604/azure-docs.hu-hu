---
title: Az Azure Maps térkép vezérlőelem használata |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Maps térkép vezérlőelem ügyféloldali Javascript-függvénytárat.
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 228d2d3331b510a0f07dbd3ca278715466d747af
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988891"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Az Azure Maps térkép vezérlőelem használata
A térkép vezérlőelem ügyféloldali Javascript-kódtár térképeket és beágyazott Azure Maps-funkciók jelennek meg a webes vagy mobilalkalmazás segítségével. 

## <a name="create-a-new-map-in-a-web-page"></a>Egy weblapon egy új térkép létrehozása

A térkép vezérlőelem ügyféloldali Javascript-kódtár használatával egy térkép is beágyazható egy weblapon.

1. Hozzon létre egy új fájlt, és nevezze el MapSearch.html.

2. Adja hozzá az Azure Maps stíluslap és parancsfájl forrás hivatkozásokat a `<head>` elem a fájl:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Annak érdekében, hogy a render egy új térkép a böngészőben, adjon hozzá egy **#map** hivatkozzon a `<style>` elemet.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. A térkép vezérlőelem inicializálásához a html törzsében új definiálására, és hozzon létre egy parancsfájlt. A parancsfájl a saját az Azure Maps-fiók kulcsot használni. Ha hozzon létre egy fiókot, vagy keresse meg a, tekintse meg kell [az Azure Maps-fiók és kulcsok kezelése](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. A böngészőben nyissa meg a fájlt, és megtekintheti a megjelenített térképen.

## <a name="next-steps"></a>További lépések

Ez a cikk láthatta, hogyan hozhat létre egy egyszerű térképet az Azure Maps-kulcs. A maps hozzá további kódot példák a következő cikkekben talál: 

* [Térkép létrehozása](map-create.md)
* [A PIN-kód hozzáadása](map-add-pin.md)
