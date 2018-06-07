---
title: Az Azure-leképezések térkép vezérlőelem használata |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Maps térkép vezérlőelem ügyféloldali Javascript-függvénytárat.
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4ba4b9a9f2357d283ddc03a4723cb08b48d40a9b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599211"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Az Azure-leképezések térkép vezérlőelem használata
A térkép vezérlőelem ügyféloldali Javascript-függvénytárat lehetővé teszi a webkiszolgáló vagy a mobilalkalmazás leképezések és beágyazott Azure leképezhető funkció megjelenítéséhez. 

## <a name="create-a-new-map-in-a-web-page"></a>Hozzon létre egy új leképezés egy weblapon

A térkép vezérlőelem ügyféloldali Javascript-könyvtár használatával térképre ágyazható be egy weblapon.

1. Hozzon létre egy új fájlt, és adjon neki nevet MapSearch.html.

2. Az Azure-leképezések stíluslap és parancsfájl forrás hivatkozásokat adni a `<head>` elem a fájl:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Ahhoz, hogy hatására a böngésző új leképezés, hozzáadni egy **#map** hivatkozik a `<style>` elemet.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Ahhoz, hogy a térkép vezérlőelem inicializálására, adjon meg egy új szakaszt a html törzs, és hozzon létre egy parancsfájlt. A saját Azure Maps fiókkulcs használja a parancsfájlban. Ha a fiók létrehozása, vagy keresse meg a, tekintse meg kell [Azure Maps fiókja és -kulcsok kezelése](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. A böngészőben nyissa meg a fájlt, és a megjelenített megjelenítése.

## <a name="next-steps"></a>További lépések

Ez a cikk egy alapszintű térkép létrehozása az Azure-leképezések kulccsal bemutatta. További kód hozzáadása a maps, tekintse meg a következő cikkeket: 

* [Térkép létrehozásához](map-create.md)
* [A PIN-kód hozzáadása](map-add-pin.md)