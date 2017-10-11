---
title: "Adja hozzá a logic apps késleltetést |} Microsoft Docs"
description: "A késleltetés és a késleltetés áttekintése-műveletek, és hogyan érdemes azokat használni az Azure Logic Apps alkalmazást."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 5f4f7052d48b4ca4ed91212d970551141e78e852
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Bevezetés az a késleltetés és a késleltetés-ig műveletek
A késleltetés használatával és a "késleltetés-amíg" műveletek, befejezheti a munkafolyamatokban.

Megteheti például a következőt:

* Várjon, amíg a hét napja állapotfrissítés keresztül e-mailek küldése.
* A munkafolyamat elhalasztani, amíg egy HTTP-hívás befejezését, mielőtt folytatja a futtatását, és az eredmény beolvasása idő tartozik.

Első lépések egy logikai alkalmazás késleltetés műveletével, lásd: [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>A késleltetés műveletek használata
Egy művelet során, amely a logikai alkalmazás definiált munkafolyamat végzi. [További információ a műveletek](connectors-overview.md).

Íme egy parancssorozat-példa a logikai alkalmazás egy késleltetés lépés használata:

1. A felvett egy eseményindítót, kattintson a **új lépés** művelet hozzáadása.
2. Keresse meg **késleltetés** a késleltetés műveletek elindítani. Ebben a példában, azt fogja kiválasztani **késleltetés**.
   
    ![Késleltetés műveletek](./media/connectors-native-delay/using-action-1.png)
3. A művelet tulajdonságait, a késés konfigurálásához hajtsa végre.
   
    ![Késleltetési konfiguráció](./media/connectors-native-delay/using-action-2.png)
4. Kattintson a **mentése** közzététele, és aktiválja a logikai alkalmazást.

## <a name="action-details"></a>A művelet részletei
Az ismétlődési eseményindító tulajdonságai a következők konfigurálható.

### <a name="delay-action"></a>Késleltetés művelet
Ez a művelet késlelteti a Futtatás egy meghatározott időtartam alatt.
A * azt jelenti, hogy mezőt kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Száma * |Száma |A késleltetési idő egységek száma |
| Egység * |egység |Időegység: `Second`, `Minute`, `Hour`, vagy`Day` |

<br>

### <a name="delay-until-action"></a>Késleltetés-amíg művelet
Ez a művelet késlelteti a Futtatás csak a megadott dátum és idő.
A * azt jelenti, hogy mezőt kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Év * |időbélyeg |Az év (GMT) amíg késleltetése |
| Hónap * |időbélyeg |A hónap (GMT) amíg késleltetése |
| Nap * |időbélyeg |A nap (GMT) amíg késleltetése |

<br>

## <a name="next-steps"></a>Következő lépések
Most, próbálja ki a platformot és [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md). Az egyéb rendelkezésre álló összekötők logic Apps alkalmazások felmérésével felfedezheti a [API-k lista](apis-list.md).

