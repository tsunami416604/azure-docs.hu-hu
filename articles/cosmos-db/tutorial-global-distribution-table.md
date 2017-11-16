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
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 93e75429d66a30bfc4588a3070e32d58eec0df4b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Hogyan lehet beállítani az Azure Cosmos DB globális terjesztési tábla API használatával

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * Globális terjesztési használatával konfigurálja a [tábla API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>A preferált régió tábla API használatával csatlakozik

Kihasználása érdekében [globális terjesztési](distribute-data-globally.md), ügyfélalkalmazások is adja meg a dokumentum műveletek végrehajtásához használandó régiók rendezett beállítások listáját. Ezt megteheti úgy, hogy a `TablePreferredLocations` konfigurációs érték az az App.config fájlban, az Azure Cosmos DB tábla API SDK-ban. Az Azure Cosmos DB tábla API SDK válasszon fog kommunikálni a legjobb végpont a fiók konfigurációjának, aktuális területi rendelkezésre állás és a megadott beállítások listája alapján.

A `TablePreferredLocations` olvasása előnyben részesített (többhelyű) helyek vesszővel tagolt listáját kell tartalmaznia. Minden ügyfél példány e régiók részhalmazát megadhat kis késleltetésű olvasása csatlakozási kísérleteinek kívánt sorrendjét. A régiók névvel kell ellátni használatával a [megjelenített neveket](https://msdn.microsoft.com/library/azure/gg441293.aspx), például `West US`.

Az első rendelkezésre álló terület küldött összes írási a `TablePreferredLocations` listája. A kérés nem teljesíthető, ha az ügyfél lefelé a listában, a következő régióban sikertelen, és így tovább.

Az SDK próbál olvasni a megadott régiók `TablePreferredLocations`. Igen, például ha az adatbázis-fiókot a három régióban, de az ügyfél csak meghatározza a nem írási területek két `TablePreferredLocations`, akkor nincs olvasási szolgáltató kívül az írási régió, feladatátvétel esetén is.

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
> * Az Azure Cosmos DB tábla API-kkal globális terjesztési konfigurálása

