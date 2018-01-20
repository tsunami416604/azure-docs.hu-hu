---
title: "Azure Logic Apps Webhook-összekötője |} Microsoft Docs"
description: "Például a szűrő tömb műveletek végrehajtásához ennél a logic Apps alkalmazásokból webhookműveletek és eseményindítók használata"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: ec407d72f90cf3616214d27c378dee0ff42f4c6f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-webhook-connector"></a>A webhook összekötő az első lépései

A webhook művelet és az eseményindító indítsa el, szüneteltetése és folytatása adatfolyamok ezen feladatok végrehajtásával:

* Az indítás egy [Azure Event Hubs](https://github.com/logicappsio/EventHubAPI) amikor egy elem érkezik.
* Várjon, amíg a munkafolyamat folytatása előtt a jóváhagyás

További információ [egyéni API-k, amelyek támogatják a webhook létrehozása](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>A webhook eseményindító használata

A [ *eseményindító* ](connectors-overview.md) olyan esemény, amely a logic app munkamenet indítása. A webhook eseményindító eseményalapú, és nem függ a lekérdezési új elemek. Például a [kérelem eseményindító](connectors-native-reqres.md), a logikai alkalmazást, amely egy esemény történik, azonnali következik be. A webhook eseményindító regisztrál egy *visszahívási URL-cím* szolgáltatás és a szükséges URL-címet az érvényesítést a logikai alkalmazást.

Íme egy példa bemutatja, hogyan állíthat be egy HTTP-eseményindítóval a Logic App tervezőben. A lépések azt feltételezik, hogy már telepítették, vagy az alábbi API-k elérésére a [webhook szolgáltatásra, és a logic apps mintát leiratkozhat](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). Az előfizetés kezdeményezték a logikai alkalmazás egy új webhook menti, vagy átváltott le van tiltva, engedélyezze a. A lemondás kezdeményezték a logic app webhook eseményindító eltávolítva és mentésekor vagy átváltott a engedélyezett.

**A webhook eseményindító hozzáadása**

1. Adja hozzá a **HTTP Webhook** logikai alkalmazás első lépéseként eseményindító.
2. Adja meg a paraméterek, a webhook előfizetés és hívások leiratkozhat.

   Ezt a lépést, azonos mintát követi a [HTTP-művelet](connectors-native-http.md) formátumban.

     ![HTTP-eseményindítóval](./media/connectors-native-webhook/using-trigger.png)

3. Legalább egy műveletet hozzá.
4. Kattintson a **mentése** közzététele a logikai alkalmazást. Ebben a lépésben az előfizetés végpont meghívja a visszahívási URL-címet, a logikai alkalmazás indításához szükséges.
5. Amikor a szolgáltatás lehetővé teszi egy `HTTP POST` visszahívási URL-címet, a logikai alkalmazás következik be, és a kérelem átadott adatokat tartalmaz.

## <a name="use-the-webhook-action"></a>A webhook művelettel

Egy [ *művelet* ](connectors-overview.md) művelet végzi el a logikai alkalmazás definiált munkafolyamat. A webhook művelet regisztrálja a *visszahívási URL-cím* egy szolgáltatás, és megvárja, amíg az URL-cím neve folytatása előtt. A ["E-mail küldése a jóváhagyási"](connectors-create-api-office365-outlook.md) csatlakozó, amely ebben a mintában a következő példát. Ebben a mintában kiterjesztheti a webhook művelet keresztül semmilyen szolgáltatásba. 

Íme egy példa bemutatja, hogyan állíthat be a webhook művelet a Logic App tervezőben. Ezek a lépések feltételezik, hogy már telepítették, vagy az alábbi API-k elérésére a [webhook szolgáltatásra, és a logic apps használt minta leiratkozhat](../logic-apps/logic-apps-create-api-app.md#webhook-actions). Az előfizetés kezdeményezték a webhook művelet végrehajtásakor a logikai alkalmazás. A lemondás kezdeményezték futtató megszakadt a válaszra való várakozás során, vagy előtt a logic app túllépi az időkorlátot.

**A webhook művelet hozzáadása**

1. Válasszon **új lépés** > **művelet hozzáadása**.

2. A keresési mezőbe, írja be az "webhook" található a **HTTP Webhook** művelet.

    ![Válassza ki a lekérdezési művelet](./media/connectors-native-webhook/using-action-1.png)

3. Adja meg a paraméterek, a webhook előfizetés és leiratkozhat hívások

   Ezt a lépést, azonos mintát követi a [HTTP-művelet](connectors-native-http.md) formátumban.

     ![Teljes lekérdezési művelet](./media/connectors-native-webhook/using-action-2.png)
   
   Futásidőben a logikai alkalmazást az előfizetés végpont meghívja a lépés elérése után.

4. Kattintson a **mentése** közzététele a logikai alkalmazást.

## <a name="technical-details"></a>Technikai részletek

Az alábbiakban további részleteket az eseményindítók és műveletek adott webhook támogatja.

## <a name="webhook-triggers"></a>Webhook triggers

| Műveletek | Leírás |
| --- | --- |
| HTTP Webhook |Az előfizetés olyan szolgáltatás, amely meghívhatja az érvényesítést a logikai alkalmazás igény szerint az URL-cím visszahívási URL-CÍMÉT. |

### <a name="trigger-details"></a>Eseményindító részletei

#### <a name="http-webhook"></a>HTTP Webhook

Az előfizetés olyan szolgáltatás, amely meghívhatja az érvényesítést a logikai alkalmazás igény szerint az URL-cím visszahívási URL-CÍMÉT.
Egy * azt jelenti, hogy a mezőt kötelező kitölteni.

| Megjelenítendő név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Előfizetés metódus * |metódus |Előfizetés a kérelemhez használandó HTTP-metódus |
| Előfizetés URI * |uri azonosító |Előfizetés a kérelemhez használandó HTTP URI- |
| Leiratkozhat metódus * |metódus |HTTP-metódus lemondási kérelmet használandó |
| Leiratkozhat URI * |uri azonosító |HTTP URI-t használ a lemondási kérelmet |
| Fizessen elő a szervezet |törzs |Az előfizetés HTTP-kérelem törzsében |
| Fejlécek előfizetés |fejlécek |HTTP-kérelmek fejléceinek előfizetés |
| Hitelesítési előfizetés |hitelesítés |HTTP-hitelesítés használata az előfizetés. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletek |
| Törzs leiratkozhat |törzs |HTTP kérelem törzse lemondás |
| Fejlécek leiratkozhat |fejlécek |HTTP-kérelmek fejléceinek lemondás |
| Hitelesítési leiratkozhat |hitelesítés |HTTP-hitelesítés lemondás esetén használandó. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletek |

**Kimeneti részletei**

Webhook kérelem

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |objektum |Webhook kérelemfejléc |
| Törzs |objektum |Webhook kérelem objektum |
| Állapotkód |int |Webhook kérésállapotkódot |

## <a name="webhook-actions"></a>Webhookműveletek

| Műveletek | Leírás |
| --- | --- |
| HTTP Webhook |Az előfizetés olyan szolgáltatás, amely hívhatják meg folytatni a munkafolyamat-lépés igény szerint az URL-cím visszahívási URL-CÍMÉT. |

### <a name="action-details"></a>Művelet részletei

#### <a name="http-webhook"></a>HTTP Webhook

Az előfizetés olyan szolgáltatás, amely hívhatják meg folytatni a munkafolyamat-lépés igény szerint az URL-cím visszahívási URL-CÍMÉT.
Egy * azt jelenti, hogy a mezőt kötelező kitölteni.

| Megjelenítendő név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Előfizetés metódus * |metódus |Előfizetés a kérelemhez használandó HTTP-metódus |
| Előfizetés URI * |uri azonosító |Előfizetés a kérelemhez használandó HTTP URI- |
| Leiratkozhat metódus * |metódus |HTTP-metódus lemondási kérelmet használandó |
| Leiratkozhat URI * |uri azonosító |HTTP URI-t használ a lemondási kérelmet |
| Fizessen elő a szervezet |törzs |Az előfizetés HTTP-kérelem törzsében |
| Fejlécek előfizetés |fejlécek |HTTP-kérelmek fejléceinek előfizetés |
| Hitelesítési előfizetés |hitelesítés |HTTP-hitelesítés használata az előfizetés. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletek |
| Törzs leiratkozhat |törzs |HTTP kérelem törzse lemondás |
| Fejlécek leiratkozhat |fejlécek |HTTP-kérelmek fejléceinek lemondás |
| Hitelesítési leiratkozhat |hitelesítés |HTTP-hitelesítés lemondás esetén használandó. [Tekintse meg a HTTP-összekötő](connectors-native-http.md#authentication) részletek |

**Kimeneti részletei**

Webhook kérelem

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |objektum |Webhook kérelemfejléc |
| Törzs |objektum |Webhook kérelem objektum |
| Állapotkód |int |Webhook kérésállapotkódot |

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Más összekötők keresése](apis-list.md)