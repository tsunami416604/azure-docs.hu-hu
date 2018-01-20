---
title: "A lekérdezési művelet hozzáadása a logic apps |} Microsoft Docs"
description: "A lekérdezés művelet, például a szűrő tömb műveletek végrehajtásához áttekintése."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 05dd4ae3c4ee439d66401a3f5595f9104051f8ee
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-query-action"></a>Ismerkedjen meg a lekérdezési művelet
A lekérdezési művelet használatával is dolgozhat kötegek és tömbök munkafolyamatokat elvégzéséhez:

* Hozzon létre egy feladatot az összes magasabb prioritású rekord adatbázisból.
* Az e-mailekhez, az Azure-blobot összes PDF melléklet mentése.

Első lépések egy logikai alkalmazás lekérdezés műveletével, lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>A lekérdezés művelettel
Egy művelet során, amely a logikai alkalmazás definiált munkafolyamat végzi. [További információ a műveletek](connectors-overview.md).  

A lekérdezés jelenleg műveletnek egy művelet, a szűrő tömb, a tervezőben elérhetővé neve. Ez lehetővé teszi egy tömb lekérdezése, és a szűrt eredmények készlettel tért vissza.

Itt látható, hogyan adhat hozzá azt a logikai alkalmazást:

1. Válassza ki a **új lépés** gombra.
2. Válasszon **művelet hozzáadása**.
3. A művelet a keresőmezőbe írja be **szűrő** listájára a **szűrő tömb** művelet.
   
    ![Válassza ki a lekérdezés](./media/connectors-native-query/using-action-1.png)
4. Válassza ki a tömb szűréséhez. (Az alábbi képernyőfelvételen látható Twitter keresési eredmények tömbje.)
5. Minden egyes kiértékelése feltétel létrehozása. (Az alábbi képernyőfelvételen szűrők a felhasználók, akik több mint 100 followers Twitter-üzeneteket.)
   
    ![A lekérdezés a művelet](./media/connectors-native-query/using-action-2.png)
   
    A művelet egy új tömb csak, amely megfelel a szűrő eredményeit tartalmazó fog kimeneti.
6. Kattintson az eszköztáron menteni a bal felső sarkában, és a Logic Apps alkalmazást mentése és közzététele (aktiválása).

\*Ha Ön egy HTTP-végpont meghívása, és egy JSON-válasz fogadásakor, a _elemzése JSON_ művelettel a JSON-válasz elemzése. Ebben a lépésben anélkül _szűrő tömb_ a rendszer csak a Választörzs megtekintéséhez, majd a JSON-adattartalmat nem értelmezésekor.

## <a name="query-action"></a>Lekérdezési művelet
Az alábbiak a művelet, amely támogatja ezt az összekötőt. Az összekötő egy lehetséges művelettel rendelkezik.

| Műveletek | Leírás |
| --- | --- |
| Tömb szűrése |Minden elem tömbben feltételt ad meg, és az eredményt ad vissza. |

## <a name="action-details"></a>Művelet részletei
A lekérdezési művelet egy lehetséges műveletet tartalmaz. A következő táblázatok ismertetik, hogy a művelet és a megfelelő kimeneti részletek művelettel társított szükséges és választható bemeneti mező.

### <a name="filter-array"></a>Tömb szűrése
A művelet, amely a kimenő HTTP-kérelem a beviteli mezők a következők:
A * azt jelenti, hogy mezőt kötelező kitölteni.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| A * |forrás: |A tömb szűrése |
| Az állapot * |Ha |A feltétel kiértékelése minden elemhez |

<br>

### <a name="output-details"></a>Kimenet részletei
A HTTP-válasz kimeneti részleteit az alábbiakban.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Szűrt tömb |tömb |Olyan tömb, amely minden szűrt eredmény objektumot tartalmaz |

## <a name="next-steps"></a>További lépések
Most, próbálja ki a platformot és [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők logic Apps alkalmazások felmérésével felfedezheti a [API-k lista](apis-list.md).

