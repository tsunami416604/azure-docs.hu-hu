---
title: "Tábla API Azure Cosmos DB globális telepítési útmutató |} Microsoft Docs"
description: "Megtudhatja, hogyan beállítani az Azure Cosmos DB globális terjesztési tábla API használatával."
services: cosmos-db
keywords: "globális terjesztési, tábla"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: e9cb4b5b886cec46c0483287460c720855867f38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Hogyan lehet beállítani az Azure Cosmos DB globális terjesztési tábla API használatával

Ebben a cikkben megmutatjuk, hogyan használható az Azure portál Azure Cosmos DB globális terjesztési telepítési és a tábla API (előzetes verzió) segítségével csatlakozzon.

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * Globális terjesztési használatával konfigurálja a [tábla API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>A preferált régió tábla API használatával csatlakozik

Kihasználása érdekében [globális terjesztési](distribute-data-globally.md), ügyfélalkalmazások is adja meg a dokumentum műveletek végrehajtásához használandó régiók rendezett beállítások listáját. Ezt megteheti úgy, hogy a `TablePreferredLocations` konfigurációs érték az előzetes Azure Storage szolgáltatás SDK az alkalmazás Config. Az Azure Cosmos DB-fiók konfigurációja, az aktuális területi rendelkezésre állás és a megadott beállításokat szabályozó lista alapján, a legoptimálisabb végpont választja ki az Azure Storage szolgáltatás SDK írási és olvasási műveletek.

A `TablePreferredLocations` olvasása előnyben részesített (többhelyű) helyek vesszővel tagolt listáját kell tartalmaznia. Minden ügyfél példány e régiók részhalmazát megadhat kis késleltetésű olvasása csatlakozási kísérleteinek kívánt sorrendjét. A régiók névvel kell ellátni használatával a [megjelenített neveket](https://msdn.microsoft.com/library/azure/gg441293.aspx), például `West US`.

Az összes olvasási kapnak az első rendelkezésre álló terület a `TablePreferredLocations` listája. A kérés nem teljesíthető, ha az ügyfél lefelé a listában, a következő régióban sikertelen, és így tovább.

Az SDK-t csak megpróbálja beolvasni a régió van megadva a `TablePreferredLocations`. Igen, például ha az adatbázis-fiókot a három régióban, de az ügyfél csak meghatározza a nem írási területek két `TablePreferredLocations`, akkor nincs olvasási szolgáltató kívül az írási régió, feladatátvétel esetén is.

Az SDK automatikusan elküld minden írási műveleteket ad ki az aktuális írási régió.

Ha a `TablePreferredLocations` tulajdonság nincs beállítva, az összes kérelem szolgáltató aktuális írási régióban.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

Ez azt, hogy ez az oktatóanyag befejezése. Megismerheti a globális replikált fiókja konzisztencia kezeléséhez olvasásával [Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md). És hogyan globális adatbázis-replikációval kapcsolatos további információk az Azure Cosmos Adatbázisba működik, a következő témakörben: [adatok globálisan Azure Cosmos DB terjesztése](distribute-data-globally.md).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * A DocumentDB API-k használatával globális terjesztési konfigurálása

Most már folytathatja a következő oktatóanyag megtudhatja, hogyan fejleszthet, helyileg emulátorral Azure Cosmos DB helyi.

> [!div class="nextstepaction"]
> [Helyileg emulátorral fejlesztése](local-emulator.md)