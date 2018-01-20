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
ms.openlocfilehash: 6cde5b8ba8d770a07199816286b666e952394de1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Bevezetés az a késleltetés és a késleltetés-ig műveletek
A késleltetés használatával és a "késleltetés-amíg" műveletek, befejezheti a munkafolyamatokban.

Megteheti például a következőt:

* Várjon, amíg a hét napja állapotfrissítés keresztül e-mailek küldése.
* A munkafolyamat elhalasztani, amíg egy HTTP-hívás befejezését, mielőtt folytatja a futtatását, és az eredmény beolvasása idő tartozik.

Első lépések egy logikai alkalmazás késleltetés műveletével, lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>A késleltetés műveletek használata
Egy művelet során, amely a logikai alkalmazás definiált munkafolyamat végzi. [További információ a műveletek](connectors-overview.md).

Íme egy parancssorozat-példa a logikai alkalmazás egy késleltetés lépés használata:

1. A felvett egy eseményindítót, kattintson a **új lépés** művelet hozzáadása.
2. Keresse meg **késleltetés** a késleltetés műveletek elindítani. Ebben a példában, azt fogja kiválasztani **késleltetés**.
   
    ![Késleltetés műveletek](./media/connectors-native-delay/using-action-1.png)
3. A művelet tulajdonságait, a késés konfigurálásához hajtsa végre.
   
    ![Késleltetési konfiguráció](./media/connectors-native-delay/using-action-2.png)
4. Kattintson a **mentése** közzététele, és aktiválja a logikai alkalmazást.

## <a name="action-details"></a>Művelet részletei
Az ismétlődési eseményindító tulajdonságai a következők konfigurálható.

### <a name="delay-action"></a>Késleltetés művelet
Ez a művelet késlelteti a Futtatás egy meghatározott időtartam alatt.
A * azt jelenti, hogy mezőt kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Száma * |darab |A késleltetési idő egységek száma |
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

## <a name="next-steps"></a>További lépések
Most, próbálja ki a platformot és [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők logic Apps alkalmazások felmérésével felfedezheti a [API-k lista](apis-list.md).

