---
title: Késleltetés hozzáadása a logic appsben |} A Microsoft Docs
description: Áttekintés a késleltetés és a késleltetés-műveletek és az Azure logic app használatával.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 15e581454b60319ab734f2fa5faf0d90e0a7c8bf
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893724"
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Ismerkedés a késleltetés és a késleltetés-ig műveletek
A késleltetés használatával és a "késleltetés-mindaddig, amíg" műveletek, munkafolyamat-forgatókönyvek is elvégezheti.

Megteheti például a következőt:

* Várjon, amíg az állapot frissítéséhez küldése e-mailben egy hét napja.
* Késleltetés a munkafolyamat, amíg egy HTTP-hívás befejezését, mielőtt folytatása, és az eredmény beolvasása idő tartozik.

Használatának megkezdéséhez a késleltetési műveletet egy logikai alkalmazásban, lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>A késleltetés műveletek használata

Egy műveletet, amely a logikai alkalmazásban definiált munkafolyamat által végzett művelet. 
[További információért azokról a műveletekről](../connectors/apis-list.md).

Íme egy parancssorozat-példa egy késleltetés lépés használata a Logic Apps-alkalmazás:

1. A felvett egy eseményindítót, kattintson a **új lépés** művelet hozzáadása.
2. Keresse meg **késleltetés** viszi, megjelenik a késleltetési műveletet. Ebben a példában választjuk **késleltetés**.
   
    ![Késleltetés műveletek](./media/connectors-native-delay/using-action-1.png)
3. Hajtsa végre a művelet tulajdonságainak konfigurálása a késés.
   
    ![Késleltetési konfiguráció](./media/connectors-native-delay/using-action-2.png)
4. Kattintson a **mentése** tehet közzé, és aktiválja a logikai alkalmazást.

## <a name="action-details"></a>Művelet részletei
Az ismétlődési eseményindító funkcióját beállíthatja a következő tulajdonságokkal rendelkezik.

### <a name="delay-action"></a>Késleltetési műveletet
Ez a művelet késlelteti a Futtatás egy adott időtartam alatt.
A * azt jelenti, hogy egy kötelező mező.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Száma * |count |Késleltetési idő egységek száma |
| Unit* |egység |Az időegység: `Second`, `Minute`, `Hour`, vagy `Day` |

<br>

### <a name="delay-until-action"></a>Késleltetés – amíg a művelet
Ez a művelet késlelteti a futtatást, amíg egy megadott dátum/idő.
A * azt jelenti, hogy egy kötelező mező.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Év * |időbélyeg |Az év, a késleltetés eddig (GMT) |
| Havonta * |időbélyeg |Az elmúlt hónapban késleltetés eddig (GMT) |
| Nap * |időbélyeg |A nap késleltetés eddig (GMT) |

<br>

## <a name="next-steps"></a>További lépések
Most, próbálja ki a platformot és [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb elérhető összekötők a logic apps megtekintésével megismerheti a [API-k listája](apis-list.md).

