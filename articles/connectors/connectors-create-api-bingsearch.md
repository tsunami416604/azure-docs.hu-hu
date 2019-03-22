---
title: A Bing Search – Azure Logic Apps csatlakoztatása
description: Keresse meg a hírkeresési Bing Search REST API-k és az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313549"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Keresse meg a hírkeresési Bing Search és az Azure Logic Apps

Ez a cikk bemutatja, hogyan találhatja híreket, videókat és más elemek keresztül a Bing Search belül egy logikai alkalmazást, a Bing Search-összekötővel. Ezzel a módszerrel hozhat létre, amely a feladatok automatizálása a logic apps és a feldolgozási munkafolyamatok keresési eredmények, és ezekhez az elemekhez más műveletek is elérhetővé. 

Például keresse meg a keresési feltétel alapján híreket, és azok az elemek közzététele, ahogy a tweetet a Twitter-hírcsatorna Twitter rendelkezik.

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Összekötő-specifikus technikai tudnivalókért tekintse meg a <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">összekötő-referencia a Bing Search</a>.

## <a name="prerequisites"></a>Előfeltételek

* A [Cognitive Services-fiók](../cognitive-services/cognitive-services-apis-create-account.md)

* A [Bing keresési API-kulcs](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), amely biztosítja a hozzáférést a logikai alkalmazás a Bing Search APIs

* A logikai alkalmazás, ahol szeretné elérni az Eseményközpont. A Bing Search triggert a logikai alkalmazás indításához szükséges egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>A Bing keresés eseményindító hozzáadása

Az Azure Logic Appsben, mindegyik logikai alkalmazásnak kell kezdődnie, egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor aktiválódik, ha egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt, és megkezdi az alkalmazás munkafolyamatában.

1. Az Azure Portalon vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logikaialkalmazás-Tervező. Ebben a példában az Azure Portalt használja.

2. A Keresés mezőbe írja be a "Bing search" szűrőként. Az eseményindítók listában jelölje ki a kívánt eseményindító.

   Ebben a példában ez az eseményindító: **Bing Search – új cikk nyitható meg**

   ![Keresse meg a Bing Search triggert](./media/connectors-create-api-bing-search/add-trigger.png)

3. Ha a kapcsolat részleteivel, kér [most már a Bing keresési kapcsolat létrehozására](#create-connection).
Vagy, ha a kapcsolat már létezik, adja meg a szükséges információkat az eseményindító.

   Ebben a példában adja meg feltételeknek megfelelő híreket visszatérésre Bing Search.

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | Keresési lekérdezés | Igen | <*Keresés – szavakat*> | Adja meg a használni kívánt keresési kulcsszavakat. |
   | Piac | Igen | <*Területi beállítás*> | A keresési nyelv. Az alapértelmezett érték "en-US", de választhat egy másik értéket. |
   | Biztonságos keresés | Igen | <*Keresés-szint*> | A szűrő kizárása, felnőtt tartalom szintje. Az alapértelmezett érték a "Közepes", de egy másik szintet választ. |
   | Darabszám | Nem | <*eredmények száma*> | A megadott számú eredményt adja vissza. Az alapértelmezett érték 20, de egy másik értéket is megadhat. A visszaadott eredmények tényleges száma kisebb, mint a megadott szám lehet. |
   | Eltolás | Nem | <*skip-value*> | Mielőtt az eredményt adnak vissza a kihagyandó találatok száma |
   |||||

   Példa:

   ![Eseményindító beállítása](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Válassza ki a időközét és gyakoriságát, hogy milyen gyakran kívánja az eseményindító eredmények kereséséhez.

5. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

6. Most már folytathatja, egy vagy több művelet hozzáadása a logikai alkalmazáshoz, a feladatok a trigger eredményekkel végrehajtására vonatkozó szándékát.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>A Bing Search-művelet hozzáadása

Az Azure Logic Apps- [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamat egy eseményindító vagy egy másik műveletet a következő lépés. Ebben a példában a logikai alkalmazás elindul, a Bing Search eseményindítóval, amely visszaadja a megadott feltételeknek megfelelő híreket.

1. Az Azure Portalon vagy a Visual Studióban nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben. Ebben a példában az Azure Portalt használja.

2. A trigger vagy művelet alatt válassza **új lépés** > **művelet hozzáadása**.

   Ebben a példában ez az eseményindító:

   **Bing Search – új cikk nyitható meg**

   ![Művelet felvétele](./media/connectors-create-api-bing-search/add-action.png)

   Meglévő lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó nyílra. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza **művelet hozzáadása**.

3. A Keresés mezőbe írja be a "Bing search" szűrőként.
Jelölje ki az elvégzendő műveletek listájában.

   Ebben a példában ez a művelet:

   **A Bing Search - lekérdezés által lista hírek**

   ![Keresse meg a Bing Search-művelet](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Ha a kapcsolat részleteivel, kér [most már a Bing keresési kapcsolat létrehozására](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a művelet a szükséges információkat.

   Ebben a példában adja meg a feltételeknek egy részét az eseményindító eredmények visszaadása.

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | Keresési lekérdezés | Igen | <*search-expression*> | Adja meg a kiértékelt kifejezés az eseményindító eredmények lekérdezése. Válassza ki a mezőket a dinamikus tartalmak listájában, vagy hozzon létre egy kifejezést a Kifejezésszerkesztőt. |
   | Piac | Igen | <*Területi beállítás*> | A keresési nyelv. Az alapértelmezett érték "en-US", de választhat egy másik értéket. |
   | Biztonságos keresés | Igen | <*Keresés-szint*> | A szűrő kizárása, felnőtt tartalom szintje. Az alapértelmezett érték a "Közepes", de egy másik szintet választ. |
   | Darabszám | Nem | <*eredmények száma*> | A megadott számú eredményt adja vissza. Az alapértelmezett érték 20, de egy másik értéket is megadhat. A visszaadott eredmények tényleges száma kisebb, mint a megadott szám lehet. |
   | Eltolás | Nem | <*skip-value*> | Mielőtt az eredményt adnak vissza a kihagyandó találatok száma |
   |||||

   Tegyük fel, hogy azt szeretné, amelynek kategória neve tartalmazza a "műszaki" szót eredmények.

   1. Kattintson a **keresési lekérdezés** mezőbe, így a dinamikus tartalmak listája jelenik meg. 
   Válassza ki a listából **kifejezés** így a Kifejezésszerkesztő jelenik meg. 

      ![A Bing Search triggert](./media/connectors-create-api-bing-search/bing-search-action.png)

      Most már megkezdheti a kifejezés létrehozását.

   2. A functions listából válassza ki a **contains()** függvény, amely a kifejezés mező jelenik majd meg. Kattintson a **dinamikus tartalom** , hogy a mezőlistában ismét megjelenik, de győződjön meg arról, hogy az egérmutatót a zárójeleken belül marad.

      ![Válassza ki a függvényt](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. A mezők listájában jelölje ki **kategória**, amely alakítja át a paramétert. 
   Az első paraméterként, és a vessző után egy vessző hozzáadásával, adja hozzá ezt a szót: `'tech'` 

      ![Jelöljön ki egy mezőt](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Ha elkészült, válassza ki a **OK**.

      A kifejezés most már megjelenik a **keresési lekérdezés** mezőben a következő formátumban:

      ![Befejezett kifejezés](./media/connectors-create-api-bing-search/resolved-expression.png)

      Kódnézet Ez a kifejezés a következő formátumban jelenik meg:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>A Bing Search csatlakozni

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Amikor a kapcsolati adatokat kér, adja meg ezeket az adatokat:

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | Kapcsolat neve | Igen | <*kapcsolat neve*> | A neve, a kapcsolat létrehozása |
   | API-verzió | Igen | <*API-version*> | Alapértelmezés szerint a keresési Bing-API-verzió az aktuális verzióra van beállítva. Kiválaszthatja egy korábbi verziója szükséges. |
   | API-kulcs | Igen | <*API-kulcs*> | A keresési Bing-API-kulcs, amely a korábban kapott. Ha nem rendelkezik egy kulcs, a [most az API-kulcs](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Példa:

   ![Kapcsolat létrehozása](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Ha elkészült, kattintson a **Létrehozás** gombra.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részletekért, például a triggereket, műveletek és -korlátok, leírtak szerint az összekötő OpenAPI (korábbi nevén Swagger) fájl, tekintse meg a [összekötő referenciájának oldalát](/connectors/bingsearch/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
