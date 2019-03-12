---
title: Kérelem- és válaszműveletek használata |} A Microsoft Docs
description: A kérés- és az eseményindító és művelet az Azure logic app – áttekintés
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
ms.openlocfilehash: 2479db2abcb578eb380655346582392770606b39
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552180"
---
# <a name="get-started-with-the-request-and-response-components"></a>A kérések és válaszok összetevői használatának első lépései
A logikai alkalmazás a kérések és válaszok összetevőivel reagálhat a valós idejű események.

Megteheti például a következőt:

* Válaszol egy HTTP-kérelem adatokkal keresztül egy logikai alkalmazást egy helyszíni adatbázisból.
* Egy logikai alkalmazást egy külső webhook-esemény indítása.
* Hívja meg a logikai alkalmazás egy másik logikai alkalmazásban a kérések és válaszok művelettel.

Használatának megkezdéséhez a kérelem- és válaszműveletek egy logikai alkalmazásban, lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Használja a HTTP-kérelem típusú trigger
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. [További tudnivalók a triggerek](connectors-overview.md).

Íme egy parancssorozat-példa HTTP-kérést, a Logic App Designerben beállítása.

1. A trigger hozzáadása **kérelem – Ha egy HTTP-kérelem érkezett** a logikai alkalmazásban. Opcionálisan megadhat egy JSON-sémájában (egy hasonló eszköz használatával [JSONSchema.net](https://jsonschema.net)) a kérés törzse számára. Ez lehetővé teszi a HTTP-kérelem a Tulajdonságok jogkivonatokat hoz létre a tervezőben.
2. Egy másik művelet hozzáadása, hogy a logikai alkalmazás mentheti.
3. Után a logikai alkalmazás mentése, a kérelem kártya a HTTP-kérelem URL-CÍMÉT is kérhet.
4. Egy HTTP POST (például egy eszközzel [Postman](https://www.getpostman.com/)) az URL-címre elindítja a logikai alkalmazás.

> [!NOTE]
> Ha nem ad meg egy válaszművelet egy `202 ACCEPTED` azonnali válasz a hívónak. A válaszművelet segítségével testre szabhatja a választ.
> 
> 

![Válasz eseményindító](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>A HTTP-válasz művelet használata
A HTTP-válasz művelet csak akkor érvényes, ha egy munkafolyamatot, amely akkor aktiválódik, egy HTTP-kérelem használatát. Ha nem ad meg egy válaszművelet egy `202 ACCEPTED` azonnali válasz a hívónak.  Hozzáadhat egy válaszművelet bármely lépése a munkafolyamaton belül. A logikai alkalmazás csak fenntartja a bejövő kérelem egy percet a válaszra.  Egy perc, ha nincs válasz érkezett a munkafolyamat (és a egy válaszművelet megtalálható a definíció) után egy `504 GATEWAY TIMEOUT` küld vissza a hívónak.

A következő HTTP-válasz művelet hozzáadása:

1. Válassza ki a **új lépés** gombra.
2. Válasszon **művelet hozzáadása**.
3. A művelet be a keresőmezőbe írja be **válasz** a válaszművelet listázásához.
   
    ![Válassza ki a válaszművelet](./media/connectors-native-reqres/using-action-1.png)
4. Adja hozzá a HTTP-válaszüzenet szükséges paramétereket.
   
    ![Végezze el a válaszművelet](./media/connectors-native-reqres/using-action-2.png)
5. Kattintson az eszköztár menteni a bal felső sarkában, és a logikai alkalmazás mentése és közzététele (aktiválása).

## <a name="request-trigger"></a>Kérelem típusú trigger
Az alábbiakban az eseményindító, amely támogatja ezt az összekötőt. Nincs egyetlen kérelem-eseményindítóval.

| Eseményindító | Leírás |
| --- | --- |
| Kérés |Akkor történik, amikor HTTP-kérés fogadásakor. |

## <a name="response-action"></a>Válaszművelet
Az alábbiakban a művelet, amely támogatja ezt az összekötőt. Nincs egyetlen válaszművelet, amely csak akkor használható, ha a kérelem-eseményindítóval kíséri.

| Műveletek | Leírás |
| --- | --- |
| Válasz |Választ küld a korrelált HTTP-kérelem |

### <a name="trigger-and-action-details"></a>Az eseményindító és művelet részletei
Az alábbi táblázatok ismertetik az adatbeviteli mezők az eseményindító és művelet, és a megfelelő kimeneti részleteit.

#### <a name="request-trigger"></a>Kérelem típusú trigger
Az alábbiakban látható egy beviteli mező, az eseményindító a bejövő HTTP-kérelemből.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| JSON-sémája |séma |A HTTP-kérés törzse JSON-sémája |

<br>

**Kimenet részletei**

A kérelem kimenet részletei az alábbiakban.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Fejlécek |objektum |Kérelemfejlécek |
| Törzs |objektum |Támogatásikérelem-objektum |

#### <a name="response-action"></a>Válaszművelet
Az alábbiakban a HTTP-válasz művelet beviteli mezőket. A * azt jelenti, hogy egy kötelező mező.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| Állapot kód * |statusCode |A HTTP-állapotkód: |
| Fejlécek |A fejlécek |Bármely közé tartozik a válaszfejlécek JSON-objektum |
| Törzs |törzs |A válasz törzse |

## <a name="next-steps"></a>További lépések
Most, próbálja ki a platformot és [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb elérhető összekötők a logic apps megtekintésével megismerheti a [API-k listája](apis-list.md).

