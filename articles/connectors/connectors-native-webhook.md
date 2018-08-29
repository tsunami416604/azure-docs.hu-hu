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
ms.openlocfilehash: 7b1886321ca4afd4b4710bd9fddf16d2d5eb224b
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126587"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Hozzon létre eseményalapú munkafolyamatokat és műveleteket webhookok és az Azure Logic Apps használatával

A webhook művelet és eseményindító elindítása, szüneteltetése, és folytathatja a következő feladatok végrehajtására folyamatok:

* A trigger egy [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) érkezésekor egy elem
* Várjon, amíg egy jóváhagyási munkafolyamat folytatása előtt

Tudjon meg többet [egyéni API-k, amelyek támogatják a webhook létrehozása](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>A webhook eseményindítóhoz használata

A [ *eseményindító* ](connectors-overview.md) olyan esemény, amely elindítja a logikai alkalmazás munkafolyamatának. Webhook eseményindítóként eseményalapú, és nem függ a lekérdezés az új elemeket. Például a [kérelem típusú trigger](connectors-native-reqres.md), a logikai alkalmazás aktiválódik egy esemény azonnali. A webhook eseményindítóhoz regisztrál egy *visszahívási URL-Címének* egy szolgáltatást, és használja a logikai alkalmazás aktiválódik az URL-CÍMRE szükség.

A következő példa bemutatja, hogyan állítható be a HTTP-trigger, a Logic App Designerben. A lépések azt feltételezik, hogy már üzembe helyezte, vagy fér hozzá a következő API-k a [webhook előfizetés, és a logic apps minta mondja](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). Az előfizetés hívást kezdeményez, amikor egy logikai alkalmazást egy új webhook mentve, vagy kapcsolt Letiltottról engedélyezett értékre. Ha egy logic app webhooktrigger eltávolítva és mentett, vagy átváltani a leiratkozás hívást kezdeményez engedélyezett.

**A webhook-eseményindító hozzáadása**

1. Adja hozzá a **HTTP-Webhook** eseményindítót a logikai alkalmazás első lépéseként.
2. Adja meg a paramétereket, a webhook előfizetés, és mondja hívások.

   Ezt a lépést, ugyanezt a mintát követi a [HTTP-művelet](connectors-native-http.md) formátumban.

     ![HTTP-eseményindító](./media/connectors-native-webhook/using-trigger.png)

3. Adjon hozzá legalább egy műveletet.
4. Kattintson a **mentése** a logikai alkalmazás közzétételére. Ebben a lépésben az előfizetési végpont meghívja a visszahívási URL-címet, a logikai alkalmazás aktiválásához szükséges.
5. Minden alkalommal, amikor a szolgáltatás lehetővé teszi egy `HTTP POST` visszahívási URL-címre, a logikai alkalmazás akkor aktiválódik, és a kérés átadott adatokat tartalmazza.

## <a name="use-the-webhook-action"></a>A webhook művelettel

Egy [ *művelet* ](connectors-overview.md) egy művelet által elvégzett a logikai alkalmazásban definiált munkafolyamat. Egy webhook művelettel regisztrál egy *visszahívási URL-Címének* egy szolgáltatás, és megvárja, amíg az URL-cím neve folytatása előtt. A ["Jóváhagyási E-mail küldése"](connectors-create-api-office365-outlook.md) egy összekötőt, ez a minta a következő példát. Ez a minta kiterjesztheti az minden olyan szolgáltatáshoz, a webhook művelettel keresztül. 

A következő példa bemutatja, hogyan állítható be a Logic App Designerben webhook művelettel. Ezek a lépések feltételezik, hogy már üzembe helyezte, vagy fér hozzá a következő API-k a [webhook előfizetés, és a logic apps használt minta mondja](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Ha egy logikai alkalmazás a webhook művelet végrehajtása a feliratkozás hívást kezdeményez. A leiratkozási hívást kezdeményez egy Futtatás válaszra való várakozás közben meg lett szakítva, vagy az előtt a logikai alkalmazás túllépi az időkorlátot.

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
| Fizessen elő a fejlécek |fejlécek |HTTP-kérelmek fejléceinek előfizetés |
| Fizessen elő a hitelesítés |hitelesítés |Az előfizetés használandó HTTP-hitelesítést. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletekért |
| Előfizetés lemondása törzse |törzs |A leiratkozási HTTP-kérés törzse |
| Leiratkozás a fejlécek |fejlécek |HTTP-kérelmek fejléceinek unsubscribe |
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
| Fizessen elő a fejlécek |fejlécek |HTTP-kérelmek fejléceinek előfizetés |
| Fizessen elő a hitelesítés |hitelesítés |Az előfizetés használandó HTTP-hitelesítést. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletekért |
| Előfizetés lemondása törzse |törzs |A leiratkozási HTTP-kérés törzse |
| Leiratkozás a fejlécek |fejlécek |HTTP-kérelmek fejléceinek unsubscribe |
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