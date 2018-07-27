---
title: Adja hozzá, és futtassa a Azure Logic Apps és az Azure Functions egyéni kódot |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá, és egyéni kódrészleteket futtat az Azure Logic Apps az Azure Functions használatával
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263190"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Adja hozzá, és egyéni kódrészleteket futtat az Azure Logic Apps az Azure Functions használatával

Ha szeretne létrehozni, és a Futtatás csak a megfelelő kód, amely egy konkrét problémával kapcsolatban, a logic Apps, hozhat létre a saját függvények használatával [Azure Functions](../azure-functions/functions-overview.md). Ez a szolgáltatás lehetővé teszi a létrehozására és futtatására nem kell bajlódnunk a teljes alkalmazás vagy a kód futtatásához az infrastruktúra létrehozása a logic Apps Node.js vagy a C#-készült egyéni kódrészleteket. Az Azure Functions biztosít a kiszolgáló nélküli számítási feladatokat a felhőben, és akkor hasznos, ha a feladatok, például a következőket:

* Kiterjesztheti a logikai alkalmazás viselkedése a Node.js vagy a C# által támogatott funkciók.
* Hajtsa végre a logikai alkalmazás munkafolyamatának számításokat.
* Speciális formázás alkalmazása, vagy a logic Apps mezők számítási.

Emellett [a logikai alkalmazások meghívása Azure functions belül](#call-logic-app).

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk követéséhez az alábbiakban szükséges elemek:

* Ha nem rendelkezik Azure-előfizetésem, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>. 

* A logikai alkalmazás, ahol szeretné a függvény hozzáadása

  Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként 

  A függvények futtatását műveleteket is hozzáadhat, mielőtt a logikai alkalmazás egy eseményindítóval kell elindítania.

* Azure-függvényalkalmazás, amely egy tároló az Azure functions és az Azure-függvény. Ha a függvényalkalmazás nem rendelkezik, akkor kell [a függvényalkalmazás létrehozásához először](../azure-functions/functions-create-first-azure-function.md). Létrehozhatja a függvény vagy [külön kívül a logikai alkalmazás](#create-function-external), vagy [a logikai alkalmazáson belül](#create-function-designer) a Logic App Designerben.

  Új és meglévő Azure function appsszel és a functions rendelkezik ugyanazok a követelmények, a logic Apps használatához:

  * A függvényalkalmazás Azure-előfizetéshez, a logikai alkalmazás kell tartoznia.

  * A függvényt kell használnia a **általános webhook** függvénysablon a **JavaScript** vagy **C#**. Ez a sablon is fogadja el a tartalmat `application/json` írja be a logikai alkalmazás. Ezeket a sablonokat is segít a Logic App Designerben keresse meg és az egyéni függvények, amelyek ezeket a sablonokat az hoz létre, amikor a logic apps hozzá ezekhez a függvényekhez megjelenítése.

  * Ellenőrizze, hogy a függvénysablon **mód** tulajdonsága **Webhook** és a **webhooktípus** tulajdonsága **általános JSON**.

    1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.
    2. Az Azure fő menüjéből válassza **Függvényalkalmazások**. 
    3. Az a **Függvényalkalmazások** listában válassza ki a függvényalkalmazást, bontsa ki a függvényt, és válassza ki **integráció**. 
    4. Ellenőrizze a sablont **mód** tulajdonsága **Webhook** , és hogy a **webhooktípus** tulajdonsága **általános JSON**. 

  * Ha a függvény egy [API-definíció](../azure-functions/functions-openapi-definition.md)nevén, egy [Swagger-fájl](http://swagger.io/), a Logic Apps Designerben függvény paraméterei a munka sokoldalúbb felhasználói élményben kínál. 
  A logikai alkalmazás megtalálhatja és elérheti az funkciók, amelyek rendelkeznek a Swagger-leírásai, mielőtt [az alábbi lépéseket a függvényalkalmazás beállítása](#function-swagger).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Függvényeket hozhat létre kívül a logic apps

Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, az Azure függvényalkalmazást, amely az Azure-előfizetéshez, a logikai alkalmazást, és a majd hozza létre az Azure-függvény létrehozása. Ha most ismerkedik az Azure Functions, megtudhatja, hogyan [az első függvény létrehozása az Azure Portalon](../azure-functions/functions-create-first-azure-function.md), azonban vegye figyelembe ezeket, hozzáadhat és a logic apps, hívja meg az Azure functions létrehozásához szükséges követelményeket.

* Mindenképpen jelölje ki a **általános webhook** függvénysablon a **JavaScript** vagy **C#**.

  ![Általános webhook – JavaScript- vagy C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Miután létrehozta az Azure-függvény, ellenőrizze, hogy a sablon **mód** és **webhooktípus** tulajdonságai megfelelően vannak-e beállítva.

  1. Az a **Függvényalkalmazások** listában, válassza ki és bontsa ki a függvényt **integráció**. 

  2. Ellenőrizze, hogy a sablon **mód** tulajdonsága **Webhook** , és hogy a **webhooktípus** tulajdonsága **általános JSON**. 

     ![A függvény "Integrálás" sablontulajdonságok](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* Igény szerint ha Ön [API-definíció létrehozása](../azure-functions/functions-openapi-definition.md)nevén, egy [Swagger-fájl](http://swagger.io/), a függvény kap sokoldalúbb felhasználói élményben függvény paraméterei a Logic Apps Designerben való munka során. A függvényalkalmazás beállítása a logikai alkalmazás megtalálhatja és elérheti az funkciók, amelyek rendelkeznek a Swagger-leírások:

  * Győződjön meg arról, hogy a függvényalkalmazás aktívan fut-e.

  * A függvényalkalmazásban, állítsa be [eltérő eredetű erőforrások megosztása (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) így az összes forrás engedélyezettek:

    1. Kezdve a **Függvényalkalmazások** listájához, válassza ki a függvényalkalmazást > **platformfunkciók** > **CORS**.

       ![Válassza ki a függvényalkalmazást > "Platformfunkciók" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. A **CORS**, adja hozzá a `*` helyettesítő karakter, de a lista minden más forrást eltávolítása, és válassza **mentése**.

       ![Válassza ki a függvényalkalmazást > "Platformfunkciók" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Hozzáférés tulajdonságértékek belső HTTP-kérelmekre

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

2. A funkció most már elérheti a `date` keresztül tulajdonság a `data` változóhoz, majd, hogy a DateTime típusú érték tulajdonság DateString meghívásával írja be a konvertálás a `ToDateString()` függvény. A függvény is keresztül eredményt adja vissza a `body` tulajdonság frissítése az a függvény által visszaadott válasz: 

   ```javascript
   body: data.date.ToDateString();
   ```

Most, hogy létrehozta az Azure-függvény, kövesse a lépéseket, hogy hogyan [funkciók hozzáadása a logic apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>A logic apps belül függvények létrehozása

Létrehozhat egy Azure-függvényt a logikai alkalmazás a Logic App Designerben belül kezdve, először szüksége van egy Azure-függvényalkalmazás, amely az Ön függvényeinek tárolója. Ha nem rendelkezik egy függvényalkalmazást, először hozza létre a függvényalkalmazást. Lásd: [az első függvény létrehozása az Azure Portalon](../azure-functions/functions-create-first-azure-function.md). 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, nyissa meg a logikai alkalmazás a Logic App Designerben. 

2. Válassza ki a lépés, ahol szeretné létrehozni, és adja hozzá a függvényt, alatt **új lépés** > **művelet hozzáadása**. 

3. A keresőmezőbe írja be az "azure functions" szűrőként.
Válassza ezt a műveletet a műveletek listájának: **válassza ki az Azure-függvény – az Azure Functions** 

   ![Keresse meg az "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. A függvény alkalmazások listájában jelölje ki a függvényalkalmazást. A műveletek listájának megnyitása után válassza a következő műveletet: **Azure Functions – új függvény létrehozása**

   ![Válassza ki a függvényalkalmazást](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. A függvény definícióját szerkesztő adja meg a függvényt:

   1. Az a **függvénynév** adja meg a függvény nevét. 

   2. Az a **kód** adjon hozzá, a függvény kódját a sablonhoz, beleértve a válasz és a kívánt tartalom a logikai alkalmazáshoz adja vissza, miután a függvény a lejáratot követően újrainduljon. 
   A sablonban lévő kód a context objektumot mutatja be, az üzenet és a tartalmat, amelyet a logikai alkalmazás átad a függvényt, például:

      ![A függvény meghatározása](./media/logic-apps-azure-functions/function-definition.png)

      A függvényen belül a tulajdonságokat a context objektumot a következő szintaxis használatával lehet hivatkozni:

      ```text
      context.<token-name>.<property-name>
      ```
      Ebben a példában az itt látható a szintaxist használja:

      ```text
      context.body.content
      ```

   3. Ha elkészült, kattintson a **Létrehozás** gombra.

6. Az a **kérelem törzse** adja meg a környezet objektum, a függvény bemeneti, át kell formázni a JavaScript Object Notation (JSON). Amikor rákattint az a **kérelem törzse** mezőben a dinamikus tartalmak listájából a ezért kiválaszthatja a tokenek számára rendelkezésre álló tulajdonságok az előző lépésekből nyílik meg. 

   Ez a példa továbbítja az objektumot a **törzs** az e-mail-trigger tokent:  

   !["A kérelem törzse" Példa - környezet objektum adattartalom](./media/logic-apps-azure-functions/function-request-body-example.png)

   A context objektumot tartalma alapján, a Logic App Designerben állít elő, hogy szerkeszthető beágyazott függvényt sablon. 
   A Logic Apps változók alapján bemeneti context objektumot is létrehoz.

   Ebben a példában a context objektumot nem alakítható karakterláncként, így a tartalmat közvetlenül, a JSON-adattartalom hozzáadása. 
   Azonban ha az objektum nem egy JSON-jogkivonatot, amely egy karakterlánc, egy JSON-objektumot, vagy egy JSON-tömböt kell lennie, hibaüzenetet kap. 
   A context objektumot karakterláncként leadott, adja hozzá a idézőjelek közé, például:

   ![A CAST objektum karakterláncként](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Egyéb részleteket, például a módszert használja, a kérelemfejlécek vagy a lekérdezési paraméterek megadásához válassza **speciális beállítások megjelenítése**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>A meglévő funkciók hozzáadása a logic apps használatával

Meglévő Azure-függvények hívása a logic apps, az Azure functions, mint bármilyen más műveletet is hozzáadhat a Logic App Designerben. 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, nyissa meg a logikai alkalmazás a Logic App Designerben. 

2. A lépés, ahol szeretné adja hozzá a függvényt, alatt válassza ki a **új lépés** > **művelet hozzáadása**. 

3. A keresőmezőbe írja be az "azure functions" szűrőként.
Válassza ezt a műveletet a műveletek listájának: **válassza ki az Azure-függvény – az Azure Functions** 

   ![Keresse meg az "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. A függvény alkalmazások listájában jelölje ki a függvényalkalmazást. Miután megjelenik a függvények listáját, válassza ki a függvényt. 

   ![Válassza ki a függvényalkalmazást, és az Azure-függvény](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Az funkciók, amelyek API-definíciók (Swagger-leírásai) és, amelyek [állítsa be úgy a logikai alkalmazás megtalálhatja és elérheti az ezekhez a függvényekhez](#function-swagger), választhat **Swagger-műveletek**:

   ![Válassza ki a függvényalkalmazást, a "Swagger-műveletek" ", és az Azure-függvény](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. Az a **kérelem törzse** adja meg a környezet objektum, a függvény bemeneti, át kell formázni a JavaScript Object Notation (JSON). A környezeti objektumon ismerteti az üzenet és a tartalom, amely a logikai alkalmazás küld a függvényt. 

   Amikor rákattint az a **kérelem törzse** mezőben a dinamikus tartalmak listájából a ezért kiválaszthatja a tokenek számára rendelkezésre álló tulajdonságok az előző lépésekből nyílik meg. 
   Ez a példa továbbítja az objektumot a **törzs** az e-mail-trigger tokent:

   !["A kérelem törzse" Példa - környezet objektum adattartalom](./media/logic-apps-azure-functions/function-request-body-example.png)

   Ebben a példában a context objektumot nem alakítható karakterláncként, így a tartalmat közvetlenül, a JSON-adattartalom hozzáadása. 
   Azonban ha az objektum nem egy JSON-jogkivonatot, amely egy karakterlánc, egy JSON-objektumot, vagy egy JSON-tömböt kell lennie, hibaüzenetet kap. 
   A context objektumot karakterláncként leadott, adja hozzá a idézőjelek közé, például:

   ![A CAST objektum karakterláncként](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Egyéb részleteket, például a módszert használja, a kérelemfejlécek vagy a lekérdezési paraméterek megadásához válassza **speciális beállítások megjelenítése**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Logikai alkalmazások hívása funkciók

Aktiválhat egy logikai alkalmazást egy Azure-függvényt belül, a logikai alkalmazás rendelkeznie kell egy hívható végponton, vagy még pontosabban egy **kérelem** eseményindító. Majd, az a függvényen belül küldött HTTP POST-kérelmet az URL-címet, amely **kérelem** aktiválása és a hasznos a logikai alkalmazás feldolgozni kívánt adatokat tartalmazza. További információkért lásd: [logikai alkalmazások hívása, eseményindító, vagy beágyazása](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Ismerje meg [Logic Apps-összekötők](../connectors/apis-list.md)
