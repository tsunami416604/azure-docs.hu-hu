---
title: "A Twitter-összekötő használata a logic apps |} Microsoft Docs"
description: "A REST API paraméterekkel Twitter-összekötő áttekintése"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: eb953ee7701d407b9b75a0699f53b9b64828a0e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twitter-connector"></a>A Twitter-összekötő az első lépései
A Twitter-összekötő segítségével:

* Tweeteket és get Twitter-üzenetek
* Hozzáférés ütemtervek, ismerősei és followers
* Hajtsa végre az egyéb műveletek és eseményindítók jelen cikkben ismertetett

Használandó [a csatlakozókat](apis-list.md), először hozzon létre egy logikai alkalmazást. Elkezdheti által [logikai alkalmazás létrehozása most](../logic-apps/quickstart-create-first-logic-app-workflow.md).  

## <a name="connect-to-twitter"></a>Csatlakozás a Twitterhez
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először hozzon létre egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) biztosít a logikai alkalmazás és egy másik szolgáltatás közötti kapcsolat.  

### <a name="create-a-connection-to-twitter"></a>Kapcsolatot létesíthet a Twitteren
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Twitter-eseményindítók
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Ebben a példában, használja a **amikor egy új tweetet visszaküldi** eseményindító #Seattle kereséséhez. És ha #Seattle megtalálható, frissítse a Dropbox fájlt a tweetet szöveget. Vállalati például sikerült keresse meg a vállalat nevét és a tweetet szöveget az SQL-adatbázis frissítése.

1. Adja meg *twitter* be a keresőmezőbe a logic apps designer válassza ki a **Twitter - amikor egy új tweetet visszaküldi** eseményindító   
   ![Twitter eseményindító kép 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Adja meg *#Seattle* a a **keresett szöveg** vezérlő  
   ![Twitter eseményindító kép 2](./media/connectors-create-api-twitter/trigger-2.png) 

A Logic Apps alkalmazást ezen a ponton úgy van konfigurálva, az eseményindító, amely akkor kezdődik, eseményindítók és műveletek a munkafolyamat futását. 

> [!NOTE]
> A logikai alkalmazás működéséhez legalább egy eseményindító és egy műveletet kell tartalmaznia. A következő szakaszban a lépések segítségével új művelet.

## <a name="add-a-condition"></a>Feltétel hozzáadása
Azt is csak a felhasználók több mint 50 felhasználóival Twitter-üzeneteket. Igen olyan feltétel, amely megerősíti, hogy followers száma szerepel a logikai alkalmazás.  

1. Válassza ki **+ új lépés** a műveletet hajtson végre egy új tweetet található #Seattle hozzáadása  
   ![Twitter művelet kép 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Válassza ki a **feltétel hozzáadása** hivatkozásra.  
   ![Twitter feltétel kép 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Ekkor megnyílik a **feltétel** vezérlő, ahol ellenőrizheti a feltételeket, mint *egyenlő*, *értéke kisebb, mint*, *nagyobb, mint*, *tartalmaz*stb.  
   ![Twitter feltétel kép 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Válassza ki a **adjon meg értéket** vezérlő. A vezérlő választhat egy vagy több tulajdonságaihoz bármely korábbi műveletek vagy eseményindítók. Ez a tulajdonság értékének a feltétel igaz vagy hamis értékeli.
   ![Twitter feltétel kép 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Válassza ki a **...**  bontsa ki a tulajdonságok listájának, így minden elérhető tulajdonságokat.        
   ![Twitter feltétel kép 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Válassza ki a **Followers száma** tulajdonság.    
   ![Az állapot kép 5 Twitter](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Figyelje meg, a Followers a count tulajdonság már a érték vezérlőben.    
   ![Twitter feltétel kép 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Válassza ki **nagyobb, mint** operátorok listájából.    
   ![Twitter feltétel kép 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Adjon meg 50 az operandus a *nagyobb, mint* operátor.  
   A feltétel jelenik meg. Mentés a munkahelyi használatával a **mentése** hivatkozásra kattintva a menüben.    
   ![Twitter feltétel kép 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Egy Twitter művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Most, hogy nincs egy eseményindítót, a Twitter-üzeneteket az eseményindító által megtalált tartalmát egy új tweetet visszaküldés művelet hozzáadása. Ez az útmutató a csak Twitter-üzeneteket a felhasználók a több mint 50 followers közzé tenni.  

A következő lépésben adja hozzá egy Twitter-műveletet, amely egy tweetet, egyes minden tweetet, a felhasználó több mint 50 followers feladott tulajdonságok használatával küldi.  

1. Válassza ki **művelet hozzáadása**. Ez a lépés a keresési vezérlőelemet kereshetnek, ahol a más műveletek és eseményindítók nyílik meg.  
   ![Twitter feltétel kép 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Adja meg *twitter* a keresőmezőbe válassza ki a **Twitter - egy tweetet utáni** művelet. Ebben a lépésben megnyitja a **egy tweetet utáni** szabályozása, ahol minden részletek megadása az elküldött tweetet.      
   ![A művelet kép 1-5 Twitter](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Válassza ki a **Tweetet szöveg** vezérlő. A korábbi műveletek és a logikai alkalmazást az eseményindítók összes kimenetének is látható. Válassza ki valamelyik ezek kimenetek, és használhatja őket az új tweetet tweetet szövegét részeként.     
   ![Twitter művelet kép 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Válassza ki **felhasználónév**   
5. Közvetlenül a felhasználói név után írja be a *szereplő ellenőrzőösszeg:* a tweetet szöveg vezérlőben.
6. Válassza ki *Tweetet szöveg*.       
   ![Twitter művelet kép 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Aktiválja a munkafolyamatot, mentse a munkáját, és küldjön egy tweetet, a #Seattle hashtaggel történő.


## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/twitterconnector/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)