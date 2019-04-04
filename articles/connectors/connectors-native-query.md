---
title: A query művelet hozzáadása a logic appsben |} A Microsoft Docs
description: A lekérdezési műveletet hajt végre műveleteket, például a tömb szűrése áttekintése.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 2a82afe396039857e5b9ad6b8a6d0e710573037f
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893567"
---
# <a name="get-started-with-the-query-action"></a>A lekérdezés művelettel használatának első lépései
A query művelet használatával, használhatja a kötegek és tömbök munkafolyamatokat elvégzéséhez:

* Minden magas prioritású rögzíti a feladat létrehozása az adatbázisból.
* Az e-mailekből másolja egy Azure-blobból az összes PDF-mellékletek mentése.

A lekérdezés művelettel, a logikai alkalmazás a használatának megkezdéséhez lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>A lekérdezés művelettel
Egy műveletet, amely a logikai alkalmazásban definiált munkafolyamat által végzett művelet. 
[További információért azokról a műveletekről](../connectors/apis-list.md).  

A lekérdezés művelettel jelenleg egy műveletet, a tömb szűrése, a tervezőben érintkező nevű rendelkezik. Ez lehetővé teszi egy tömb lekérdezését, és a egy szűrt eredmények készletét adja vissza.

Itt látható, hogyan adhatja hozzá a logikai alkalmazás:

1. Válassza ki a **új lépés** gombra.
2. Válasszon **művelet hozzáadása**.
3. A művelet be a keresőmezőbe írja be **szűrő** listához a **tömb szűrése** művelet.
   
    ![Válassza ki a lekérdezés művelettel](./media/connectors-native-query/using-action-1.png)
4. Válassza ki a tömb szűrése. (A következő képernyőképen látható a tömbben, a Twitter keresési eredményeit.)
5. Hozzon létre az egyes elemek kiértékelendő feltétel. (Az alábbi képernyőfelvételen látható szűrők-tweetek streamelését több mint 100 követővel rendelkező felhasználók számára).
   
    ![A query művelet végrehajtása](./media/connectors-native-query/using-action-2.png)
   
    A művelet kimenete egy új, csak olyan eredményeket, amely megfelel az szűrőt tartalmazó tömb.
6. Kattintson az eszköztár menteni a bal felső sarkában, és a logikai alkalmazás mentése és közzététele (aktiválása).

\* Ha Ön egy HTTP-végpontot hív-e, és egy JSON-válasz fogadása, használja a _JSON elemzése_ művelet a JSON-válasz elemzése. Anélkül, hogy ebben a lépésben _tömb szűrése_ meg csak a szervezet és a JSON-adattartalom nem értelmezésekor.

## <a name="query-action"></a>Lekérdezési művelet
Az alábbiakban a művelet, amely támogatja ezt az összekötőt. Az összekötő egy lehetséges művelettel rendelkezik.

| Műveletek | Leírás |
| --- | --- |
| Tömb szűrése |Kiértékeli a feltételt a tömbben lévő egyes elemek, és az eredményeket adja vissza |

## <a name="action-details"></a>Művelet részletei
A lekérdezés művelettel egy lehetséges műveletet tartalmaz. Az alábbi táblázatok ismertetik a szükséges és választható adatbeviteli mezők a művelet és a megfelelő kimeneti részletek társított a művelettel.

### <a name="filter-array"></a>Tömb szűrése
Az alábbiakban a beviteli mezőket a művelet, ami lehetővé teszi a kimenő HTTP-kérést.
A * azt jelenti, hogy egy kötelező mező.

| Megjelenített név | Tulajdonság neve | Leírás |
| --- | --- | --- |
| A * |forrás: |A tömb szűrése |
| A feltétel * |ahol |Az egyes elemekhez tartozó kiértékelendő feltétel |

<br>

### <a name="output-details"></a>Kimenet részletei
Az alábbiakban a HTTP-válasz kimeneti adatait.

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Szűrt tömb |tömb |Egy tömb, amely az egyes szűrt eredmények objektumot tartalmaz |

## <a name="next-steps"></a>További lépések
Most, próbálja ki a platformot és [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb elérhető összekötők a logic apps megtekintésével megismerheti a [API-k listája](apis-list.md).

