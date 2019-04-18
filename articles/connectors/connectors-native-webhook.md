---
title: Hozzon létre munkafolyamatokat eseményalapú vagy a műveletek – Azure Logic Apps |} A Microsoft Docs
description: Automatizálja az eseményalapú munkafolyamatokat és műveleteket webhookok és Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895579"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Hozzon létre eseményalapú munkafolyamatokat és műveleteket webhookok és az Azure Logic Apps használatával

A webhook művelet és eseményindító elindítása, szüneteltetése, és folytathatja a következő feladatok végrehajtására folyamatok:

* A trigger egy [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) érkezésekor egy elem
* Várjon, amíg egy jóváhagyási munkafolyamat folytatása előtt

Tudjon meg többet [egyéni API-k, amelyek támogatják a webhook létrehozása](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>A webhook eseményindítóhoz használata

A [ *eseményindító* ](../connectors/apis-list.md) olyan esemény, amely elindítja a logikai alkalmazás munkafolyamatának. A webhook eseményindítóhoz eseményalapú, amely új elemeket a lekérdezése nem függ. Ha menti a logikai alkalmazás a webhook eseményindítóként, illetve ha módosítja a logikai alkalmazás le van tiltva, hogy engedélyezve van, a webhook eseményindítóhoz *feliratkozik* a megadott szolgáltatás vagy a végpont regisztrálásával egy *visszahívási URL-Címének* szolgáltatás vagy a végpont. Az eseményindító URL-CÍMRE használja fel a logikai alkalmazás futtatása szükséges. Például a [kérelem típusú trigger](connectors-native-reqres.md), a logikai alkalmazás akkor aktiválódik, ha a várt esemény történik, azonnal. Az eseményindító *lemondja az előfizetést* Ha eltávolítja az eseményindítót, a logikai alkalmazás mentése, illetve amikor megváltozik a logikai alkalmazást engedélyezett.

A következő példa bemutatja, hogyan állítható be a HTTP-trigger, a Logic App Designerben. A lépések azt feltételezik, hogy már üzembe helyezte, vagy fér hozzá a következő API-k a [webhook előfizetés, és a logic apps minta mondja](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). 

**A webhook-eseményindító hozzáadása**

1. Adja hozzá a **HTTP-Webhook** eseményindítót a logikai alkalmazás első lépéseként.
2. Adja meg a paramétereket, a webhook előfizetés, és mondja hívások.

   Ezt a lépést, ugyanezt a mintát követi a [HTTP-művelet](connectors-native-http.md) formátumban.

     ![HTTP-trigger](./media/connectors-native-webhook/using-trigger.png)

3. Adjon hozzá legalább egy műveletet.
4. Kattintson a **mentése** a logikai alkalmazás közzétételére. Ebben a lépésben az előfizetési végpont meghívja a visszahívási URL-címet, a logikai alkalmazás aktiválásához szükséges.
5. Minden alkalommal, amikor a szolgáltatás lehetővé teszi egy `HTTP POST` visszahívási URL-címre, a logikai alkalmazás akkor aktiválódik, és a kérés átadott adatokat tartalmazza.

## <a name="use-the-webhook-action"></a>A webhook művelettel

Egy [ *művelet* ](../connectors/apis-list.md) egy meghatározott műveletet, és futtassa a logikai alkalmazás munkafolyamat által. Mikor fusson a logikai alkalmazás a webhook művelettel, a művelet *feliratkozik* a megadott szolgáltatás vagy a végpont regisztrálja a *visszahívási URL-Címének* szolgáltatás vagy a végpont. A webhook művelettel, majd megvárja, amíg, amely a szolgáltatás hívást az URL-cím a logikai alkalmazás folytatja futtatása előtt. A logikai alkalmazás lemondja az előfizetést a szolgáltatás vagy a végpont ezekben az esetekben: 

* Ha a webhook művelet sikeres befejezését követően
* Ha a logikai alkalmazás futtatásának megszakítása válaszra való várakozás közben
* Mielőtt a logikai alkalmazás túllépi az időkorlátot

Ha például a [ **jóváhagyó e-mail küldése** ](connectors-create-api-office365-outlook.md) beavatkozásra egy webhook művelettel ezt a mintát a következő példát. Ez a minta kiterjesztheti az minden olyan szolgáltatáshoz, a webhook művelettel keresztül. 

A következő példa bemutatja, hogyan állítható be a Logic App Designerben webhook művelettel. Ezek a lépések feltételezik, hogy már üzembe helyezte, vagy fér hozzá a következő API-k a [webhook előfizetés, és a logic apps használt minta mondja](../logic-apps/logic-apps-create-api-app.md#webhook-actions). 

**Egy webhook művelet hozzáadása**

1. Válasszon **új lépés** > **művelet hozzáadása**.

2. A Keresés mezőbe írja be az "webhook" található a **HTTP-Webhook** művelet.

    ![Válassza ki a lekérdezési műveletet](./media/connectors-native-webhook/using-action-1.png)

3. Adja meg a paraméterek, a webhook előfizetés, és mondja hívások

   Ezt a lépést, ugyanezt a mintát követi a [HTTP-művelet](connectors-native-http.md) formátumban.

     ![Teljes lekérdezési művelet](./media/connectors-native-webhook/using-action-2.png)
   
   Futásidőben a logikai alkalmazás az előfizetési végpont meghívja ezt a lépést elérése után.

4. Kattintson a **mentése** a logikai alkalmazás közzétételére.

## <a name="technical-details"></a>Technikai részletek

További részletek eseményindítók és műveletek kapcsolatban, hogy a webhook támogat.

## <a name="webhook-triggers"></a>Webhook triggers

| Műveletek | Leírás |
| --- | --- |
| HTTP Webhook |Az előfizetés egy szolgáltatás, amely az URL-cím a logikai alkalmazás igény szerint üzenetszám meghívhatja egy visszahívási URL-címe. |

### <a name="trigger-details"></a>A trigger részletei

#### <a name="http-webhook"></a>HTTP Webhook

Az előfizetés egy szolgáltatás, amely az URL-cím a logikai alkalmazás igény szerint üzenetszám meghívhatja egy visszahívási URL-címe.
Egy * azt jelenti, hogy kötelező mező.

| Megjelenítendő név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Feliratkozás metódus * |method |Előfizetési kérés használandó HTTP-metódus |
| Feliratkozás URI * |uri azonosító |Előfizetési kérés használandó HTTP URI |
| Előfizetés lemondása metódus * |method |Leiratkozás kérelem használandó HTTP-metódus |
| Előfizetés lemondása URI * |uri azonosító |A HTTP URI unsubscribe kérelem használata |
| Fizessen elő a törzs |törzs |Az előfizetés HTTP-kérés törzse |
| Fizessen elő a fejlécek |A fejlécek |HTTP-kérelmek fejléceinek előfizetés |
| Fizessen elő a hitelesítés |hitelesítés |Az előfizetés használandó HTTP-hitelesítést. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletekért |
| Előfizetés lemondása törzse |törzs |A leiratkozási HTTP-kérés törzse |
| Leiratkozás a fejlécek |A fejlécek |HTTP-kérelmek fejléceinek unsubscribe |
| Előfizetés lemondása hitelesítés |hitelesítés |A leiratkozási használandó HTTP-hitelesítést. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletekért |

**Kimenet részletei**

Webhook-kérelmet

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |objektum |Webhook-kérelemfejlécek |
| Törzs |objektum |Webhook támogatásikérelem-objektum |
| Állapotkód |int |Webhook kérésállapotkódot |

## <a name="webhook-actions"></a>Webhook-műveletek

| Műveletek | Leírás |
| --- | --- |
| HTTP Webhook |Az előfizetés egy szolgáltatás, amely igény szerint egy munkafolyamat-lépés folytatásához az URL-cím segítségével meghívhatja a visszahívási URL-címe. |

### <a name="action-details"></a>Művelet részletei

#### <a name="http-webhook"></a>HTTP Webhook

Az előfizetés egy szolgáltatás, amely igény szerint egy munkafolyamat-lépés folytatásához az URL-cím segítségével meghívhatja a visszahívási URL-címe.
Egy * azt jelenti, hogy kötelező mező.

| Megjelenítendő név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Feliratkozás metódus * |method |Előfizetési kérés használandó HTTP-metódus |
| Feliratkozás URI * |uri azonosító |Előfizetési kérés használandó HTTP URI |
| Előfizetés lemondása metódus * |method |Leiratkozás kérelem használandó HTTP-metódus |
| Előfizetés lemondása URI * |uri azonosító |A HTTP URI unsubscribe kérelem használata |
| Fizessen elő a törzs |törzs |Az előfizetés HTTP-kérés törzse |
| Fizessen elő a fejlécek |A fejlécek |HTTP-kérelmek fejléceinek előfizetés |
| Fizessen elő a hitelesítés |hitelesítés |Az előfizetés használandó HTTP-hitelesítést. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletekért |
| Előfizetés lemondása törzse |törzs |A leiratkozási HTTP-kérés törzse |
| Leiratkozás a fejlécek |A fejlécek |HTTP-kérelmek fejléceinek unsubscribe |
| Előfizetés lemondása hitelesítés |hitelesítés |A leiratkozási használandó HTTP-hitelesítést. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletekért |

**Kimenet részletei**

Webhook-kérelmet

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |objektum |Webhook-kérelemfejlécek |
| Törzs |objektum |Webhook támogatásikérelem-objektum |
| Állapotkód |int |Webhook kérésállapotkódot |

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Más összekötők keresése](apis-list.md)
