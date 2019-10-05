---
title: A lekérdezési művelet hozzáadása a Logic Appsben | Microsoft Docs
description: A lekérdezési művelet áttekintése, például a Filter Array művelet végrehajtása.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 10332b95e0d385d7155003efcc52b4bae3969313
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973751"
---
# <a name="get-started-with-the-query-action"></a>Ismerkedés a lekérdezési művelettel
A lekérdezési művelettel a következő műveleteket végezheti el a munkafolyamatok végrehajtásához a kötegek és tömbök használatával:

* Hozzon létre egy feladatot az adatbázis összes magas prioritású rekordjához.
* Az e-mailekhez tartozó összes PDF-melléklet mentése egy Azure-blobba.

A logikai alkalmazásokban a lekérdezési művelet használatának megkezdéséhez lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>A lekérdezési művelet használata
A művelet olyan művelet, amelyet a logikai alkalmazásban definiált munkafolyamat hajt végre. 
[További információ a műveletekről](../connectors/apis-list.md).  

A lekérdezési műveletnek jelenleg egyetlen művelete van, amelyet a Designer tömbnek nevezünk. Ez lehetővé teszi egy tömb lekérdezését, és szűrt eredmények készletének visszaadása.

A következőképpen adhat hozzá egy logikai alkalmazásban:

1. Kattintson az **új lépés** gombra.
2. Válassza **a művelet hozzáadása**lehetőséget.
3. A művelet keresés mezőjébe írja be a **szűrő** kifejezést a **tömb szűrése** művelet kilistázásához.
   
    ![Lekérdezési művelet kiválasztása](./media/connectors-native-query/using-action-1.png)
4. Válassza ki a szűrni kívánt tömböt. (A következő képernyőképen a Twitter-keresés eredményeinek tömbje látható.)
5. Hozzon létre egy feltételt az egyes elemek kiértékeléséhez. (A következő képernyőfelvétel a több mint 100 követővel rendelkező felhasználók tweeteit szűri.)
   
    ![A lekérdezési művelet végrehajtása](./media/connectors-native-query/using-action-2.png)
   
    A művelet egy új tömböt fog kiadni, amely csak a szűrési követelményeknek megfelelő eredményeket tartalmaz.
6. Kattintson a menteni kívánt eszköztár bal felső sarkára, és a logikai alkalmazás is mentés és közzététel (aktiválás).

@no__t – 0 Ha HTTP-végpontot hív meg, és JSON-választ kap, akkor a _JSON elemzése művelettel_ elemezheti a JSON-választ. Ennek a lépésnek a megkezdése nélkül a _tömb szűrése_ csak a törzset fogja látni, és nem ÉRTI a JSON-adattartalom szerkezetét.

## <a name="query-action"></a>Lekérdezési művelet
Az összekötő által támogatott művelet részleteit itt találja. Az összekötőnek van egy lehetséges művelete.

| Action | Leírás |
| --- | --- |
| Tömb szűrése |Kiértékel egy feltételt egy tömb minden eleméhez, és visszaadja az eredményeket |

## <a name="action-details"></a>Művelet részletei
A lekérdezési művelethez egyetlen lehetséges művelet tartozik. A következő táblázatok ismertetik a művelet kötelező és opcionális beviteli mezőit, valamint a művelettel társított kimeneti adatokat.

### <a name="filter-array"></a>Tömb szűrése
A következő beviteli mezők szerepelnek a művelethez, ami egy HTTP kimenő kérelmet tesz elérhetővé.
A * azt jelenti, hogy a mező kitöltése kötelező.

| Display name | Tulajdonság neve | Leírás |
| --- | --- | --- |
| A |from |A szűrni kívánt tömb |
| Feltétel |ahol |Az egyes elemek kiértékelésének feltétele |

<br>

### <a name="output-details"></a>Kimenet részletei
A HTTP-válasz kimeneti adatai a következők:

| Tulajdonság neve | Adattípus | Leírás |
| --- | --- | --- |
| Szűrt tömb |array |Egy tömb, amely minden szűrt eredményhez tartalmaz egy objektumot. |

## <a name="next-steps"></a>További lépések
Most próbálja ki a platformot, és [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az API-k [listájának](apis-list.md)megtekintésével megtekintheti a logikai alkalmazásokban elérhető többi összekötőt.

