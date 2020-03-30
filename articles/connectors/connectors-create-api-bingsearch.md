---
title: Csatlakozás a Bing Kereséshez
description: Az Azure Logic Apps használatával automatizálhatja azokat a feladatokat és munkafolyamatokat, amelyek találatokat találnak a Bing Search szolgáltatásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: e547ae59f7b3260f46756825bca2bef1c10bcc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665887"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Eredmények keresése a Bing Search szolgáltatásban az Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan találhat híreket, videókat és egyéb elemeket a Bing Keresés szolgáltatáson keresztül egy logikai alkalmazásból a Bing Keresés összekötővel. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a feladatokat és a munkafolyamatokat a keresési eredmények feldolgozásához, és ezeket az elemeket más műveletek számára is elérhetővé teszik. 

Megkeresheti például a keresett ségi feltételek alapján található híreket, és a Twitter tweetként közzéteheti azokat a Twitter-hírcsatornában.

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
Az összekötő-specifikus műszaki információkat a [Bing Search összekötő hivatkozási útmutatójában talál.](https://docs.microsoft.com/connectors/bingsearch/)

## <a name="prerequisites"></a>Előfeltételek

* [Cognitive Services-fiók](../cognitive-services/cognitive-services-apis-create-account.md)

* Bing [Search API-kulcs,](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)amely hozzáférést biztosít a logikai alkalmazásból a Bing Search API-khoz

* A logikai alkalmazás, ahol szeretné elérni az Event Hub. A logikai alkalmazás Bing Search-eseményindítóval való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Bing-keresési eseményindító hozzáadása

Az Azure Logic Apps minden logikai alkalmazás kell kezdeni egy [eseményindító,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely egy adott esemény bekövetkezésekor, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító aktiválódik, a Logic Apps motor létrehoz egy logikai alkalmazáspéldányt, és megkezdi az alkalmazás munkafolyamatának futtatását.

1. Az Azure Portalon vagy a Visual Studio,hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic App Designer. Ez a példa az Azure Portalt használja.

2. A keresőmezőbe írja be szűrőként a "Bing keresés" kifejezést. Az eseményindítók listájában válassza ki a kívánt eseményindítót.

   Ez a példa ezt az eseményindítót használja: **Bing Keresés – Új hírek**

   ![Bing keresés eseményindítójának megkeresése](./media/connectors-create-api-bing-search/add-trigger.png)

3. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre most a Bing Keresési kapcsolatot.](#create-connection)
Vagy ha a kapcsolat már létezik, adja meg az eseményindítóhoz szükséges információkat.

   Ebben a példában adja meg a Bing Search egyező hírcikkeinek visszaadását.

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | Keresési lekérdezés | Igen | <*keresőszavak*> | Adja meg a használni kívánt keresési kulcsszavakat. |
   | Piac | Igen | <*Locale*> | A keresési területi beállítás. Az alapértelmezett érték az "en-US", de választhat egy másik értéket. |
   | Biztonságos keresés | Igen | <*keresési szint*> | A felnőtt tartalom kizárásának szűrőszintje. Az alapértelmezett érték a "Mérsékelt", de egy másik szintet választ. |
   | Darabszám | Nem | <*eredmények száma*> | Adja vissza a megadott számú eredményt. Az alapértelmezett érték 20, de megadhat egy másik értéket. A visszaadott eredmények tényleges száma kisebb lehet, mint a megadott szám. |
   | Eltolás | Nem | <*ugrás-érték*> | Az eredmények visszaadása előtt kihagyandó eredmények száma |
   |||||

   Példa:

   ![Eseményindító beállítása](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Válassza ki azt az időközt és gyakoriságot, hogy milyen gyakran szeretné az eseményindítónak az eredményeket keresni.

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

6. Most folytassa egy vagy több művelet hozzáadásával a logikai alkalmazáshoz az eseményindító eredményekkel végrehajtani kívánt feladatokhoz.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Bing keresési művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy lépés a munkafolyamatban, amely követi az eseményindító vagy egy másik művelet. Ebben a példában a logikai alkalmazás egy Bing Search eseményindítóval kezdődik, amely a megadott feltételeknek megfelelő híreket ad vissza.

1. Az Azure Portalon vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer. Ez a példa az Azure Portalt használja.

2. Az eseményindító vagy művelet alatt válassza az **Új lépés** > **Művelet hozzáadása**lehetőséget.

   Ez a példa ezt az eseményindítót használja:

   **Bing Keresés - Az új hírek**

   ![Művelet hozzáadása](./media/connectors-create-api-bing-search/add-action.png)

   Ha műveletet szeretne hozzáadni a meglévő lépések közé, vigye az egeret a csatlakozó nyíl fölé. 
   Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

3. A keresőmezőbe írja be szűrőként a "Bing keresés" kifejezést.
A műveletek listájában jelölje ki a kívánt műveletet.

   Ez a példa a következő műveletet használja:

   **Bingkeresés – Hírek listázása lekérdezés szerint**

   ![Bing keresési művelet keresése](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre most a Bing Keresési kapcsolatot.](#create-connection) Vagy ha a kapcsolat már létezik, adja meg a művelethez szükséges információkat.

   Ebben a példában adja meg az eseményindító eredményei egy részhalmazának visszaadásának kritériumait.

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | Keresési lekérdezés | Igen | <*keresőkifejezés*> | Adjon meg egy kifejezést az eseményindító eredményének lekérdezésére. Választhat a dinamikus tartalomlista mezőiből, vagy létrehozhat egy kifejezést a kifejezésszerkesztővel. |
   | Piac | Igen | <*Locale*> | A keresési területi beállítás. Az alapértelmezett érték az "en-US", de választhat egy másik értéket. |
   | Biztonságos keresés | Igen | <*keresési szint*> | A felnőtt tartalom kizárásának szűrőszintje. Az alapértelmezett érték a "Mérsékelt", de egy másik szintet választ. |
   | Darabszám | Nem | <*eredmények száma*> | Adja vissza a megadott számú eredményt. Az alapértelmezett érték 20, de megadhat egy másik értéket. A visszaadott eredmények tényleges száma kisebb lehet, mint a megadott szám. |
   | Eltolás | Nem | <*ugrás-érték*> | Az eredmények visszaadása előtt kihagyandó eredmények száma |
   |||||

   Tegyük fel például, hogy azokat az eredményeket szeretné, amelyek kategórianeve tartalmazza a "tech" szót.

   1. Kattintson a **Keresési lekérdezés** mezőbe, hogy megjelenjen a dinamikus tartalomlista. 
   Ebből a listából válassza a **Kifejezés** lehetőséget, hogy megjelenjen a kifejezésszerkesztő. 

      ![Bing keresés eseményindító](./media/connectors-create-api-bing-search/bing-search-action.png)

      Most már elkezdheti létrehozni a kifejezést.

   2. A függvények listájában jelölje ki a **contains()** függvényt, amely ezután megjelenik a kifejezésmezőben. Kattintson a **Dinamikus tartalom** elemre, hogy a mezőlista újra megjelenjen, de győződjön meg arról, hogy a kurzor a zárójelek között marad.

      ![Függvény kiválasztása](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. A mezőlistában válassza a **Kategória**lehetőséget, amely paraméterré alakul át. 
   Adjon hozzá vesszőt az első paraméter után, majd a vessző után adja hozzá ezt a szót:`'tech'` 

      ![Mező kijelölése](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Amikor elkészült, válassza az **OK** lehetőséget.

      A kifejezés most antól a Következő formátumban jelenik meg a **Keresési lekérdezés** mezőben:

      ![Kész kifejezés](./media/connectors-create-api-bing-search/resolved-expression.png)

      Kódnézetben ez a kifejezés a következő formátumban jelenik meg:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Csatlakozás a Bing Kereséshez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Amikor a rendszer csatlakozási adatokat kér, adja meg az alábbi adatokat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | Kapcsolat neve | Igen | <*kapcsolat neve*> | A kapcsolathoz létrehozandó név |
   | API-verzió | Igen | <*API-verzió*> | Alapértelmezés szerint a Bing Search API verziója az aktuális verzióra van beállítva. Szükség esetén kiválaszthat egy korábbi verziót is. |
   | API-kulcs | Igen | <*API-kulcs*> | A korábban kapott Bing Search API-kulcs. Ha nem rendelkezik kulccsal, most kapja meg az [API-kulcsot.](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api) |  
   |||||  

   Példa:

   ![Kapcsolat létrehozása](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő Swagger-fájljában leírt technikai részleteket, például eseményindítókat, műveleteket és korlátokat az [összekötő referenciaoldalán](/connectors/bingsearch/)találja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
