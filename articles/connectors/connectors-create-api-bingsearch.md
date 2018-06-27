---
title: Bing keresési - Azure Logic Apps csatlakozni |} Microsoft Docs
description: Bing keresési REST API-k és az Azure Logic Apps hírek keresése
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 8ac67f9df0e5baccc668c2aeb70f65d96e574df5
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021285"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Bing keresési és az Azure Logic Apps hírek keresése 

Ez a cikk bemutatja, hogyan híreket, videókat és más elemek keresztül Bing keresés a Bing keresési összekötő logikai alkalmazás belül található. Ily módon hozhat létre, amely a feladatok automatizálása a logic apps, és a feldolgozási munkafolyamatok a keresési eredményekben, és ezen elemekre más műveletek elérhetővé teszi. 

Például keresési feltétel alapján hírek elemeinek megkeresése, és utáni elemeket, a Twitter-üzeneteket a a Twitter hírcsatorna Twitter rendelkezik.

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. Ha most ismerkedik a logic apps, tekintse át a [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [gyors üzembe helyezés: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Összekötő-specifikus műszaki információkért lásd: a <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">Bing keresési összekötő hivatkozás</a>.

## <a name="prerequisites"></a>Előfeltételek

* A [kognitív Services-fiók](../cognitive-services/cognitive-services-apis-create-account.md)

* A [Bing keresési API-kulcs](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), amely biztosítja a hozzáférést a logikai alkalmazás a Bing keresési API-k 

* A logikai alkalmazást, ahová az Eseményközpont eléréséhez. A Logic Apps alkalmazást az Bing keresési eseményindító elindításához szükséges egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Bing keresési eseményindító hozzáadása

Az Azure Logic Apps, minden logikai alkalmazást be kell kezdődnie egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor indul, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító következik be, a Logic Apps motor hoz létre egy logic app-példány, az alkalmazás a munkafolyamat futásának indításakor.

1. Az Azure portálon vagy a Visual Studio hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic App Tervező. A példa az Azure-portálon.

2. A keresési mezőbe írja be a "Bing keresési" szűrőként. Az eseményindítók listájából válassza ki a kívánt eseményindító. 

   Ez a példa az eseményindító: **Bing keresési - új hírek cikk**

   ![Bing keresési eseményindító keresése](./media/connectors-create-api-bing-search/add-trigger.png)

3. Ha a kapcsolat részletes számítógép [most a Bing keresési kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a szükséges adatokat az eseményindító.

   Például adja meg a feltételeket az egyező hírcikkeket visszatérése Bing keresési.

   | Tulajdonság | Szükséges | Érték | Leírás | 
   |----------|----------|-------|-------------| 
   | Keresési lekérdezés | Igen | <*Keresés – szavakat*> | Adja meg a használni kívánt keresési kulcsszavakat. |
   | Piac | Igen | <*Területi beállítás*> | A keresési nyelvterületi beállításokat. Az alapértelmezett érték az "en-US", de választhat egy másik értéket. | 
   | Biztonságos keresés | Igen | <*keresési szintű*> | A szűrő szintje felnőtt tartalom kivételével. Az alapértelmezett érték "Közepes", de egy további szintű választja. | 
   | Darabszám | Nem | <*eredmények száma*> | A megadott számú eredményt adja vissza. Az alapértelmezett érték 20, de egy másik értéket is megadhat. A visszaadott eredményeken tényleges száma kisebb, mint a megadott szám lehet. | 
   | Eltolás | Nem | <*Kihagyás-érték*> | Az eredmények visszatérése előtt kihagyását eredmények száma | 
   ||||| 

   Példa:

   ![Eseményindító beállítása](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Válassza ki az időköz és gyakoriságát, hogy milyen gyakran szeretné az eseményindító eredmények kereséséhez.

5. Amikor elkészült, a designer eszköztáron válassza **mentése**. 

6. Most már folytathatja hozzáadása egy vagy több műveletet szeretne végezni az eseményindító eredményekkel feladatokhoz a Logic Apps alkalmazást.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Bing keresési művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamatban, amely egy eseményindító vagy egy másik művelet a következő lépés. Ebben a példában a logikai alkalmazást, amely visszaadja a megadott feltételnek megfelelő hírcikkeket Bing keresési eseményindító kezdődik. 

1. Az Azure portálon vagy a Visual Studio nyissa meg a Logic Apps alkalmazást Logic App tervezőben. A példa az Azure-portálon.

2. Az eseményindító vagy a művelet alatt válassza ki a **új lépés** > **művelet hozzáadása**.

   Ez a példa az eseményindító: **Bing keresési - új hírek cikk**

   ![Művelet hozzáadása](./media/connectors-create-api-bing-search/add-action.png)

   Meglévő lépései közötti művelet hozzáadása az egérmutatót a kapcsolódó nyílra. 
   Válassza ki a plusz jelre (**+**), amely akkor jelenik meg, és válassza a **művelet hozzáadása**.

3. A keresési mezőbe írja be a "Bing keresési" szűrőként.
Válassza ki az elvégzendő műveletek listában.

   A példában ez a művelet: **Bing keresési - lista hírek lekérdezés**

   ![Bing keresési művelet található](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Ha a kapcsolat részletes számítógép [most a Bing keresési kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a művelet a szükséges információkat. 

   Ennél a példánál adja meg a feltétel egy részét az eseményindító eredményeket ad vissza.

   | Tulajdonság | Szükséges | Érték | Leírás | 
   |----------|----------|-------|-------------| 
   | Keresési lekérdezés | Igen | <*keresési kifejezés*> | Adja meg az eseményindító eredmények lekérdezése kifejezés. Válassza ki a mezőket a dinamikus tartalom listába, vagy hozzon létre egy kifejezés Kifejezésszerkesztő. |
   | Piac | Igen | <*Területi beállítás*> | A keresési nyelvterületi beállításokat. Az alapértelmezett érték az "en-US", de választhat egy másik értéket. | 
   | Biztonságos keresés | Igen | <*keresési szintű*> | A szűrő szintje felnőtt tartalom kivételével. Az alapértelmezett érték "Közepes", de egy további szintű választja. | 
   | Darabszám | Nem | <*eredmények száma*> | A megadott számú eredményt adja vissza. Az alapértelmezett érték 20, de egy másik értéket is megadhat. A visszaadott eredményeken tényleges száma kisebb, mint a megadott szám lehet. | 
   | Eltolás | Nem | <*Kihagyás-érték*> | Az eredmények visszatérése előtt kihagyását eredmények száma | 
   ||||| 

   Tegyük fel például azt szeretné, hogy az eredmények, amelynek kategórianeve "technológia" szót tartalmaz. 
   
   1. Kattintson a **keresési lekérdezés** mezőben, így a dinamikus tartalom listába jelenik meg. 
   Válassza ki a listából, **kifejezés** így Kifejezésszerkesztő jelenik meg. 

      ![Bing keresési eseményindító](./media/connectors-create-api-bing-search/bing-search-action.png)

      Most elindíthatja a kifejezés létrehozásához.

   2. A funkciók listájából válassza ki a **contains()** függvénynek, amely a kifejezés mezőjében jelenik majd meg. Kattintson a **dinamikus tartalom** , hogy a mezőlista ismét megjelenik, de győződjön meg arról, hogy a kurzor zárójelben marad.

      ![A függvény](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. A listán, válassza ki **kategória**, amely egy paraméter alakítja. 
   Az első paraméter, és a vessző után adja hozzá a vesszőt, hozzáadni: `'tech'` 

      ![Jelölje ki a mezőt.](./media/connectors-create-api-bing-search/expression-select-field.png)
   
   4. Amikor elkészült, válassza ki a **OK**.

      A kifejezés most megjelenik a **keresési lekérdezés** mezőben a következő formátumban:

      ![Befejezett kifejezés](./media/connectors-create-api-bing-search/resolved-expression.png)

      A kód nézetre a kifejezés a következő formátumban jelenik meg:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Amikor elkészült, a designer eszköztáron válassza **mentése**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Bing keresési kapcsolódni

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Amikor a rendszer kéri a kapcsolati adatokat, meg az alábbi adatokat:

   | Tulajdonság | Szükséges | Érték | Leírás | 
   |----------|----------|-------|-------------| 
   | Kapcsolat neve | Igen | <*kapcsolat-neve*> | A neve a kapcsolat létrehozása |
   | API-verzió | Igen | <*API-verzió*> | Alapértelmezés szerint a Bing keresési API-verzió az aktuális verzióra van beállítva. Egy korábbi szükség szerint is választhat. | 
   | API-kulcs | Igen | <*API-kulcs*> | A Bing keresési API-kulcs korábbi portáltól. Ha még nem rendelkezik egy kulcs, a [most API-kulcs](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Példa:

   ![Kapcsolat létrehozása](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Ha elkészült, kattintson a **Létrehozás** gombra.

## <a name="connector-reference"></a>Összekötő-referencia

Technikai részletek, például eseményindítók műveletek vagy -korlátok, az összekötő a Swagger-fájl szerint, lásd: a [összekötő referencialapja](/connectors/bingsearch/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Tudnivalók más [Logic Apps-összekötők](../connectors/apis-list.md)
