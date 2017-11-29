---
title: "Az Azure hely alapú Services – térkép vezérlő használata |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure hely alapú szolgáltatások térkép vezérlőelem ügyféloldali Javascript-függvénytárat."
services: location-based-services
keywords: "Nem adjon hozzá vagy kulcsszavak szerkesztése nélkül tanácsadás a Keresőmotor-optimalizálást végző szakemberrel."
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: how-to
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 9ddd11806accddb41c02c0c6681aac07bba25356
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Az Azure hely alapú Services – térkép vezérlő használata
A térkép vezérlőelem ügyféloldali Javascript-függvénytárat lehetővé teszi a webkiszolgáló vagy a mobilalkalmazás leképezések és beágyazott Azure hely alapú funkció megjelenítéséhez. 

## <a name="prerequisites"></a>Előfeltételek
Egy Azure-alapú helyszolgáltatás fiókja és -előfizetése kulcsot. Fiók létrehozása és egy előfizetés kulcs beolvasása kapcsolatos tudnivalókat lásd: [kezelése az Azure-alapú helyszolgáltatás fiókja és -kulcsok](how-to-manage-account-keys.md). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Hozzon létre új leképezés egy weblapon a térkép vezérlőelem API használatával
A térkép vezérlőelem ügyféloldali Javascript-könyvtár használatával térképre ágyazható be egy weblapon.

1. Hozzon létre egy új fájlt, és adjon neki nevet MapSearch.html.

2. Az Azure-alapú helyszolgáltatás stíluslap és parancsfájl forrás hivatkozásokat adni a `<head>` elem a fájl:

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
    
4. Ahhoz, hogy a térkép vezérlőelem inicializálására, adjon meg egy új szakaszt a html törzs, és hozzon létre egy parancsfájlt. A saját Azure-alapú helyszolgáltatás fiókjából előfizetés-kulcsot használ. 

    ```html
    <div id="map">
        <script>
            var subscriptionKey = "<_subscriptionKey_>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        <script>
    <div>
    ```
    
5. A böngészőben nyissa meg a fájlt, és a megjelenített megjelenítése.