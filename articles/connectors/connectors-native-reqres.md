---
title: Kérelem és válasz műveletek használata |} Microsoft Docs
description: A kérelem-válasz eseményindítója és tevékenysége fel az Azure logic App áttekintése
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 58210db585befd7ce915d4579d4d0303eb15bff3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27960561"
---
# <a name="get-started-with-the-request-and-response-components"></a>Első lépések a kérelem-válasz összetevői
A logikai alkalmazás kérelem-válasz összetevővel reagálhat valós idejű eseményekre.

Megteheti például a következőt:

* Válaszol a HTTP-kérelem adatokkal keresztül logikai alkalmazás a helyi adatbázisból.
* Indítás, a logikai alkalmazást egy külső webhook eseményből.
* Hívja meg a logikai alkalmazás belül egy másik logikai alkalmazás a kérelem-válasz művelettel.

Első lépések egy logikai alkalmazás a kérelem-válasz műveletek használatával, lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Használja a HTTP-kérelem eseményindító
Egy eseményindító egy eseményt, amely segítségével indítsa el a munkafolyamatot, amely a logikai alkalmazás van definiálva. [További tudnivalók az eseményindítók](connectors-overview.md).

Íme egy parancssorozat-példa bemutatja, hogyan állítson be egy HTTP-kérelem a Logic App tervezőben.

1. Adja hozzá az eseményindító **kérelem - amikor egy HTTP-kérelem érkezik** a a Logic Apps alkalmazást. Is megadhat egy JSON-séma (például egy olyan eszközzel [JSONSchema.net](http://jsonschema.net)) vonatkozó kérés törzsében. Ez lehetővé teszi, hogy a Tervező jogkivonatokat a tulajdonságok a HTTP-kérelmet létrehozni.
2. Egy másik művelet hozzáadása, hogy a logikai alkalmazást is mentheti.
3. A logikai alkalmazást a mentés után letölthető a HTTP-kérelem URL-CÍMÉT a kérelem kártya.
4. Egy HTTP POST (használhatja, hogy egy eszköz, például [Postman](https://www.getpostman.com/)) URL-címre akkor váltja ki a logikai alkalmazást.

> [!NOTE]
> Ha nem adja meg egy válasz művelet egy `202 ACCEPTED` választ a rendszer azonnal visszaérkezik a hívóhoz. A válasz művelet segítségével testre szabhatja a választ.
> 
> 

![Válasz eseményindító](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>A HTTP-válasz művelettel
A HTTP-válasz művelet csak akkor érvényes, ha egy munkafolyamatban, amely egy HTTP-kérés használhatja. Ha nem adja meg egy válasz művelet egy `202 ACCEPTED` választ a rendszer azonnal visszaérkezik a hívóhoz.  Hozzáadhat egy válasz művelet bármely lépése a munkafolyamaton belül. A logikai alkalmazást csak fenntartja a bejövő kérelem választ egy percig.  Ha nincs válasz érkezett a munkafolyamat (és a definíciója van a válasz művelet), egy perc után egy `504 GATEWAY TIMEOUT` van visszaérkezik a hívóhoz.

Megtudhatja, hogyan HTTP-válasz művelet hozzáadása:

1. Válassza ki a **új lépés** gombra.
2. Válasszon **művelet hozzáadása**.
3. A művelet a keresőmezőbe írja be **válasz** a válasz művelet listázásához.
   
    ![Válassza ki a válasz](./media/connectors-native-reqres/using-action-1.png)
4. Adja hozzá a HTTP-válaszüzenetnek szükséges paramétereket.
   
    ![A válasz a művelet](./media/connectors-native-reqres/using-action-2.png)
5. Kattintson az eszköztáron menteni a bal felső sarkában, és a Logic Apps alkalmazást mentése és közzététele (aktiválása).

## <a name="request-trigger"></a>Kérelem eseményindító
Az alábbiak az eseményindító, amely támogatja ezt az összekötőt. Nincs egyetlen kérelem eseményindítót.

| Eseményindító | Leírás |
| --- | --- |
| Kérés |Akkor következik be, amikor egy HTTP-kérelem érkezik |

## <a name="response-action"></a>Válasz művelet
Az alábbiak a művelet, amely támogatja ezt az összekötőt. Nincs olyan egyetlen válasz művelet, amely csak akkor használható, amikor egy kérelem eseményindító kíséri.

| Műveletek | Leírás |
| --- | --- |
| Válasz |A korrelált HTTP-kérelem választ ad vissza |

### <a name="trigger-and-action-details"></a>Eseményindítója és tevékenysége részletei
Az alábbi táblázatban láthatók a beviteli mezők az eseményindító és a művelet, és a megfelelő kimeneti részleteit.

#### <a name="request-trigger"></a>Kérelem eseményindító
A bejövő HTTP-kérelem az eseményindító egy beviteli mezőt a következő:

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| JSON-séma |Séma |A JSON-séma, a HTTP-kérelem törzse |

<br>

**Kimeneti részletei**

Az alábbiakban a kérelem részletes kimenet.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |objektum |Kérelemfejlécek |
| Törzs |objektum |Kérelem objektum |

#### <a name="response-action"></a>Válasz művelet
Az alábbiakban a beviteli mezők a HTTP-válasz művelethez. A * azt jelenti, hogy mezőt kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Állapot kód * |állapotkód |A HTTP-állapotkód: |
| Fejlécek |fejlécek |Bármely válaszfejlécek felvenni, egy JSON-objektum |
| Törzs |törzs |A választörzs |

## <a name="next-steps"></a>További lépések
Most, próbálja ki a platformot és [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők logic Apps alkalmazások felmérésével felfedezheti a [API-k lista](apis-list.md).

