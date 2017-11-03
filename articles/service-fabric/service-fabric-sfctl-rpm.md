---
title: Az Azure Service Fabric CLI - sfctl rpm |} Microsoft Docs
description: A Service Fabric CLI sfctl rpm parancsok ismerteti.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: f032af4714ad458fa6ad6fb0741f689d44f4098b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-rpm"></a>sfctl rpm
Lekérdezi és parancsainak elküldését a repair-kezelő szolgáltatás.

## <a name="commands"></a>Parancsok
|Parancs|Leírás|
| --- | --- |
|    hagyja jóvá kényszerítése| A megadott javítási feladat jóváhagyása kényszeríti.|
|    törlése       | Törli a befejezett javítása feladatot.|
|    lista         | Megfelelő a megadott szűrők javítási tevékenységek listájának lekérése.|

## <a name="sfctl-rpm-delete"></a>sfctl rpm törlése
Törli a befejezett javítása feladatot.

Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni. 

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|    – a feladat-azonosító [szükséges]| A befejezett javítási feladat törlendő azonosítója.|
|    --verziója           | A javítási feladat aktuális verziószáma. Ha nullától eltérő, majd az a kérelem csak sikeres lesz, ha ez az érték megegyezik a javítási feladat a tényleges jelenlegi verziójával. Ha nulla, majd verzió végrehajtani.|

### <a name="global-arguments"></a>Globális argumentumok
|Argumentum|Leírás|
| --- | --- |
|    --hibakeresése             | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
|    – Súgó -h           | Ez egy súgóüzenet és kilépési megjelenítése.|
|    – a kimeneti -o         | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.
|    --lekérdezés             | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
|    – részletes           | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|


## <a name="sfctl-rpm-list"></a>sfctl rpm listája
Megfelelő a megadott szűrők javítási tevékenységek listájának lekérése.

Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni. 

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|    --végrehajtó-szűrő| A javítási végrehajtó, amelynek az igényelt feladatok szerepelnie kell a lista neve.|
|    --Állapot-szűrő   | Az eredménylista egy, a következő értékek megadásával, mely feladat állapota bitenkénti – vagy szerepelnie kell. -1 - létrehozott - 2 - igényelt - 4 - előkészítése - 8 - jóváhagyott - 16 - végrehajtó - 32 - visszaállítási - 64 - befejeződött.|
|    – a feladat-azonosító-szűrő | A javítási feladat Azonosítójának előtagja megfeleltethetők.|

### <a name="global-arguments"></a>Globális argumentumok
|Argumentum|Leírás|
| --- | --- |
|    --hibakeresése          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
|    – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
|    – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett| JSON-ná.|
|    --lekérdezés          | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
|    – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>Következő lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).