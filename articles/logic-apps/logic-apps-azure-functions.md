---
title: Adja hozzá, és az Azure functions hívása az Azure Logic Apps
description: Adja hozzá, és a logic apps az Azure functions futtatása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 06/04/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 524b927ec0966199c51cdee93e920d7b847139ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495134"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Az Azure functions hívása az Azure Logic Apps

Ha szeretné futtatni, hogy egy adott feladat a logic Apps, a saját függvény használatával létrehozhat [Azure Functions](../azure-functions/functions-overview.md). Ez a szolgáltatás segítségével hozhat létre a Node.js, C#, és F# működik, így nem kell egy teljes alkalmazás vagy infrastruktúra kód futtatásához. Emellett [a logikai alkalmazások meghívása Azure functions belül](#call-logic-app). Az Azure Functions biztosít a kiszolgáló nélküli számítási feladatokat a felhőben, és akkor hasznos, ha feladatok, például a következőket:

* Terjessze ki a logikai alkalmazás viselkedése, Node.js vagy a C#-függvényekkel.
* Hajtsa végre a logikai alkalmazás munkafolyamatának számításokat.
* Speciális formázás alkalmazása, vagy a logic Apps mezők számítási.

A kódrészleteket futtat az Azure functions létrehozása nélkül, megtudhatja, hogyan [adja hozzá, és futtassa a beágyazott kódot](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> A Logic Apps és az Azure Functions közötti integráció jelenleg nem működik az tárolóhelyek engedélyezve van.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Azure-függvényalkalmazás, amely az Azure functions, valamint az Azure-függvény tárolója. Ha nem rendelkezik egy függvényalkalmazást, [a függvényalkalmazás létrehozásához először](../azure-functions/functions-create-first-azure-function.md). Ezután létrehozhat függvényalkalmazást vagy kívül esik a logikai alkalmazás az Azure Portalon vagy [a logikai alkalmazáson belül](#create-function-designer) a Logic App Designerben.

* A logic apps használatakor ugyanazok a követelmények vonatkoznak függvényalkalmazások és funkciók attól meglévő vagy új:

  * A függvényalkalmazás és a logikai alkalmazás az Azure-előfizetéshez kell használnia.

  * Új függvényalkalmazás a futtatókörnyezeti veremként kell használnia a .NET vagy a JavaScript. Amikor egy új függvény hozzá meglévő függvényalkalmazások, akkor ki lehet C# és a Javascripthez.

  * A függvény használja a **HTTP-eseményindító** sablont.

    A HTTP-eseményindító sablonját is fogadja el a tartalmat `application/json` írja be a logikai alkalmazás. Egy Azure-függvényt ad hozzá a logikai alkalmazást, a Logikaialkalmazás-Tervező jelenít meg az Azure-előfizetésen belül ebből a sablonból létrehozott egyéni függvényekhez.

  * A függvény nem használ egyéni útvonalakat, kivéve, ha meghatározta- [OpenAPI-definíció](../azure-functions/functions-openapi-definition.md) (korábban egy [Swagger-fájl](https://swagger.io/)).

  * Ha a függvény egy OpenAPI-definíció, a Logic Apps Designer lehetővé teszi egy gazdagabb élményt, amikor munkáját a függvény paraméterei. Mielőtt a logikai alkalmazás megtalálhatja és elérheti az OpenAPI-definíció rendelkező függvények [az alábbi lépéseket a függvényalkalmazás beállítása](#function-swagger).

* A logikai alkalmazás, ahol szeretné adja hozzá a függvényt, beleértve egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként

  Függvények futtatása műveleteket is hozzáadhat, mielőtt a logikai alkalmazás egy eseményindítóval kell elindítania. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>OpenAPI-leírások rendelkező függvények keresése

A függvény paraméterei a Logic Apps Designerben való munkánál sokoldalúbb felhasználói élményben [OpenAPI-definíció létrehozása](../azure-functions/functions-openapi-definition.md)nevén, egy [Swagger-fájl](https://swagger.io/), a függvény. Állítsa be a függvényalkalmazást, így a logikai alkalmazás megkeresheti és használhatja a functions, amelyek rendelkeznek a Swagger-leírásai, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy a függvényalkalmazás aktívan fut-e.

1. A függvényalkalmazásban, állítsa be [eltérő eredetű erőforrások megosztása (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) úgy, hogy az összes forrás engedélyezettek az alábbi lépéseket:

   1. Az a **Függvényalkalmazások** listájához, válassza ki a függvényalkalmazást. A jobb oldali panelen válassza ki a **platformfunkciók** > **CORS**.

      ![Válassza ki a függvényalkalmazást > "Platformfunkciók" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. A **CORS**, adja hozzá a csillag ( **`*`** ) helyettesítő karakter, de a lista minden más forrást eltávolítása, és válassza **mentése**.

      ![Állítsa be "CORS * helyettesítő karaktert a" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Hozzáférés tulajdonságértékek belső HTTP-kérelmekre

Webhook függvény is fogadja el a HTTP-kérések bemenetként, és ezeket a kérelmeket át más függvényeknek. Például bár a Logic Apps rendelkezik [funkciók, amelyek a dátum/idő értékek](../logic-apps/workflow-definition-language-functions-reference.md), az alapszintű példa JavaScript-függvény bemutatja, hogyan férhet hozzá egy tulajdonságot a kérelem objektumban, amely a függvénynek átadott és műveleteket végezhet a tulajdonság értéke. Belüli objektumok tulajdonságai el ebben a példában a [pont (.) operátort](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors):

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Ez a függvényen belül történik:

1. A függvény létrehoz egy `data` változót, és rendel a `body` belüli objektum a `request` objektum erre a változóra. A függvény a pont (.) operátort használja való hivatkozáshoz a `body` belüli objektum a `request` objektum:

   ```javascript
   var data = request.body;
   ```

1. A funkció most már elérheti a `date` keresztül tulajdonság a `data` változóhoz, majd, hogy a DateTime típusú érték tulajdonság DateString meghívásával írja be a konvertálás a `ToDateString()` függvény. A függvény is keresztül eredményt adja vissza a `body` tulajdonság frissítése az a függvény által visszaadott válasz:

   ```javascript
   body: data.date.ToDateString();
   ```

Most, hogy létrehozta az Azure-függvény, kövesse a lépéseket, hogy hogyan [funkciók hozzáadása a logic apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>A logic apps belül függvények létrehozása

Létrehozhat egy Azure-függvényt a Logikaialkalmazás-Tervező használatával a logikai alkalmazás belül kezdve, először szüksége van egy Azure-függvényalkalmazás, amely az Ön függvényeinek tárolója. Ha nem rendelkezik egy függvényalkalmazást, először hozza létre a függvényalkalmazást. Lásd: [az első függvény létrehozása az Azure Portalon](../azure-functions/functions-create-first-azure-function.md).

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben.

1. Hozhat létre, és adja hozzá a függvényt, végezze el a lépést, amely a forgatókönyvéhez vonatkozik:

   * Válassza ki a logikai alkalmazás munkafolyamat utolsó lépése, **új lépés**.

   * A logikai alkalmazás munkafolyamat meglévő lépéseivel, közötti vigye az egérmutatót a mutató nyílra, válassza a plusz (+) aláírására, és válassza ki **művelet hozzáadása**.

1. A keresőmezőbe írja be az "azure functions" szűrőként. A műveletek listából válassza a következő műveletet: **Válassza ki az Azure-függvény – az Azure Functions**

   ![Keresse meg az "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A függvény alkalmazások listájában jelölje ki a függvényalkalmazást. Miután megnyílik a műveletek listáját, válassza a következő műveletet: **Az Azure Functions – új függvény létrehozása**

   ![Válassza ki a függvényalkalmazást](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. A függvény definícióját szerkesztő adja meg a függvényt:

   1. Az a **függvénynév** adja meg a függvény nevét.

   1. Az a **kód** adjon hozzá a kódot a függvénysablon, beleértve a válasz és a kívánt tartalom a visszaadott a logikai alkalmazáshoz után a függvény a lejáratot követően újrainduljon.

      ![A függvény meghatározása](./media/logic-apps-azure-functions/function-definition.png)

      A sablon kódban a  *`context` objektum* hivatkozik az üzenetet, amely a logikai alkalmazás keresztül küld a **kérelem törzse** mezőt egy későbbi lépésben. Hozzáférés a `context` objektum tulajdonságait a függvényen belül a következő szintaxist használja:

      `context.body.<property-name>`

      Például, hogy hivatkozási a `content` belül a `context` objektumazonosító, a következő szintaxist használja: 

      `context.body.content`

      A sablonban lévő kód is tartalmaz egy `input` változó, amely tárolja az értéket a `data` paraméter, a függvény végezhetnek műveleteket az ezt az értéket. JavaScript-függvények, belül a `data` változó értéke is parancsikont `context.body`.

      > [!NOTE]
      > A `body` tulajdonság itt vonatkozik a `context` objektumra, és nem ugyanaz, mint a **törzs** művelet származó jogkivonat kimeneti, amely előfordulhat, hogy is átadhat a függvényt.

   1. Ha elkészült, kattintson a **Létrehozás** gombra.

1. Az a **kérelem törzse** adja meg a függvény bemeneti, amely egy JavaScript Object Notation (JSON) objektumot formátumban kell lenniük.

   A bemeneti a *context objektumot* vagy üzenet, amely a logikai alkalmazás küld a függvényt. Amikor rákattint az a **kérelem törzse** mező, a dinamikus tartalmak listája jelenik meg, ezért kiválaszthatja a kimenetek jogkivonatok az előző lépésekből. Ebben a példában adja meg, hogy a környezetben hasznos nevű tulajdonságot tartalmaz `content` , amely rendelkezik a **a** token meg az e-mail-trigger értéket:

   !["A kérelem törzse" Példa - környezet objektum adattartalom](./media/logic-apps-azure-functions/function-request-body-example.png)

   Itt a context objektumot is karakterláncként, a leadott nem, hogy az objektum tartalmat közvetlenül a JSON-adattartalom hozzáadása. Azonban a környezeti objektumon nem egy JSON-jogkivonatot, amelyet átad egy karakterlánc, egy JSON-objektum vagy egy JSON-tömböt, ha hibaüzenetet kap. Ha ebben a példában használt a **Received Time** token ehelyett is szavazattal a context objektumot karakterláncként idézőjelek hozzáadásával:  

   ![A CAST objektum karakterláncként](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Egyéb részleteket, például a módszert használja, a kérelemfejlécek vagy a lekérdezési paraméterek megadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt beállításokat.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>A meglévő funkciók hozzáadása a logic apps használatával

Meglévő Azure-függvények hívása a logic apps, az Azure functions, mint bármilyen más műveletet is hozzáadhat a Logic App Designerben.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben.

1. A lépés, ahol szeretné adja hozzá a függvényt, alatt válassza ki a **új lépés**, és válassza ki **művelet hozzáadása**.

1. A keresőmezőbe írja be az "azure functions" szűrőként. A műveletek listából válassza a következő műveletet: **Válassza ki az Azure-függvény – az Azure Functions**

   ![Keresse meg az "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A függvény alkalmazások listájában jelölje ki a függvényalkalmazást. Miután megjelenik a függvények listáját, válassza ki a függvényt.

   ![Válassza ki a függvényalkalmazást, és az Azure-függvény](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   A functions API-definíciók (Swagger-leírásai) és amelyek [állítsa be úgy a logikai alkalmazás megtalálhatja és elérheti az ezekhez a függvényekhez](#function-swagger), választhat **Swagger-műveletek**:

   ![Válassza ki a függvényalkalmazást, a "Swagger-műveletek" ", és az Azure-függvény](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Az a **kérelem törzse** adja meg a függvény bemeneti, amely egy JavaScript Object Notation (JSON) objektumot formátumban kell lenniük.

   A bemeneti a *context objektumot* vagy üzenet, amely a logikai alkalmazás küld a függvényt. Amikor rákattint az a **kérelem törzse** mező, a dinamikus tartalmak listája jelenik meg, így az előző lépésekből kimenetek jogkivonatainak kiválaszthatja. Ebben a példában adja meg, hogy a környezetben hasznos nevű tulajdonságot tartalmaz `content` , amely rendelkezik a **a** token meg az e-mail-trigger értéket:

   !["A kérelem törzse" Példa - környezet objektum adattartalom](./media/logic-apps-azure-functions/function-request-body-example.png)

   Itt a context objektumot is karakterláncként, a leadott nem, hogy az objektum tartalmat közvetlenül a JSON-adattartalom hozzáadása. Azonban a környezeti objektumon nem egy JSON-jogkivonatot, amelyet átad egy karakterlánc, egy JSON-objektum vagy egy JSON-tömböt, ha hibaüzenetet kap. Ha ebben a példában használt a **Received Time** token ehelyett is szavazattal a context objektumot karakterláncként idézőjelek hozzáadásával:

   ![A CAST objektum karakterláncként](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Egyéb részleteket, például a módszert használja, a kérelemfejlécek vagy a lekérdezési paraméterek megadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt beállításokat.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Logikai alkalmazások meghívása Azure functions a

Ha meg szeretné elindítani a logikai alkalmazás egy Azure-függvényt belül, a logikai alkalmazás egy eseményindítóval, amely egy hívható végpontot biztosít kell elindítania. Például elindíthatja az a logikai alkalmazás a **HTTP**, **kérelem**, **Azure-üzenetsorok**, vagy **Event Grid** eseményindító. A függvényen belül HTTP POST-kérelmet küldeni az aktiváló URL-címe, és a hasznos a logikai alkalmazás feldolgozni kívánt adatokat tartalmazza. További információkért lásd: [logikai alkalmazások hívása, eseményindító, vagy beágyazása](../logic-apps/logic-apps-http-endpoint.md).

## <a name="next-steps"></a>További lépések

* Ismerje meg [Logic Apps-összekötők](../connectors/apis-list.md)
