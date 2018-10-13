---
title: Az Azure Maps térkép vezérlőelem használata |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Maps térkép vezérlőelem ügyféloldali Javascript-függvénytárat.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166029"
---
# <a name="use-the-azure-maps-map-control"></a>Az Azure Maps térkép vezérlőelem használata

A térkép vezérlőelem ügyféloldali Javascript-kódtár térképeket és beágyazott Azure Maps-funkciók jelennek meg a webes vagy mobilalkalmazás segítségével.

## <a name="create-a-new-map-in-a-web-page"></a>Egy weblapon egy új térkép létrehozása

A térkép vezérlőelem ügyféloldali Javascript-kódtár használatával egy térkép is beágyazható egy weblapon.

1. Hozzon létre egy új fájlt, és adja neki **MapSearch.html**.

2. Adja hozzá az Azure Maps stíluslap és parancsfájl forrás hivatkozásokat a `<head>` elem a fájl:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. Megjelenítése egy új térkép a böngészőben, adjon hozzá egy **#map** hivatkozzon a `<style>` elem:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. A térkép vezérlőelem inicializálása, új definiálására a html törzsében, és hozzon létre egy parancsfájlt. A parancsfájl a saját az Azure Maps-fiók kulcsot használni. Ha hozzon létre egy fiókot, vagy keresse meg a, tekintse meg kell [az Azure Maps-fiók és kulcsok kezelése](how-to-manage-account-keys.md). A **setLanguage** metódus adja meg a használni kívánt nyelvet, térképfeliratait és vezérlők. A támogatott nyelvek további információkért lásd: [támogatott nyelvek](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. A böngészőben nyissa meg a fájlt, és megtekintheti a megjelenített térképen.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre egy térképet, egy teljes példa:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

Ismerje meg, hogyan térkép stílus:

> [!div class="nextstepaction"]
> [Térkép stílus kiválasztása](choose-map-style.md)
