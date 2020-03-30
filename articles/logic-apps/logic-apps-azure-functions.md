---
title: Azure-függvények hozzáadása és hívása az Azure Logic Apps alkalmazásból
description: Egyéni kód hívása és futtatása az Azure-függvényekben automatizált feladatokból és munkafolyamatokból az Azure Logic Apps-ben
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284121"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Azure-függvények hívása az Azure Logic Apps alkalmazásból

Ha olyan kódot szeretne futtatni, amely egy adott feladatot hajt végre a logikai alkalmazásokban, létrehozhatja saját függvényét az [Azure Functions](../azure-functions/functions-overview.md)használatával. Ez a szolgáltatás segít node.js, C#és F# függvények létrehozásában, így nem kell teljes alkalmazást vagy infrastruktúrát létrehoznia a kód futtatásához. [A logikai alkalmazásokat az Azure-függvényeken belül is hívhatja.](#call-logic-app) Az Azure Functions kiszolgáló nélküli számítástechnikát biztosít a felhőben, és hasznos az alábbi példákhoz hasonló feladatok elvégzéséhez:

* Bővítse ki a logikai alkalmazás viselkedését a Node.js vagy a C#függvényekkel.
* Számításokat végezhet a logikai alkalmazás munkafolyamatában.
* Speciális formázási vagy számítási mezők alkalmazása a logikai alkalmazásokban.

Ha kódrészleteket szeretne futtatni Azure-függvények létrehozása nélkül, olvassa el, hogyan [adhat hozzá és futtathat szövegközi kódot.](../logic-apps/logic-apps-add-run-inline-code.md)

> [!NOTE]
> Logic Apps és az Azure Functions közötti integráció jelenleg nem működik a slots engedélyezve van.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure-függvény alkalmazás, amely egy tároló az Azure-függvények, valamint az Azure-függvény. Ha nem rendelkezik függvényalkalmazással, [először hozza létre a függvényalkalmazást.](../azure-functions/functions-create-first-azure-function.md) Ezután létrehozhatja a függvényt a logikai alkalmazáson kívül az Azure Portalon, vagy [a logikai alkalmazáson belül](#create-function-designer) a Logic App Designerben.

* A logikai alkalmazásokkal végzett munka során ugyanazok a követelmények vonatkoznak a függvényalkalmazásokra és -függvényekre is, függetlenül attól, hogy azok már meglévők vagy újak-e:

  * A függvényalkalmazás és a logikai alkalmazás ugyanazt az Azure-előfizetést kell használnia.

  * Az új függvényalkalmazásoknak futásidejű veremként a .NET vagy a JavaScript használatát kell használniuk. Amikor új függvényt ad hozzá a meglévő függvényalkalmazásokhoz, kiválaszthatja a C# vagy a JavaScript lehetőséget.

  * A függvény a **HTTP-eseményindító sablont** használja.

    A HTTP-eseményindító sablon `application/json` képes fogadni a logikai alkalmazásból beírt tartalmat. Amikor hozzáad egy Azure-függvényt a logikai alkalmazásához, a Logic App Designer megjeleníti az Azure-előfizetésen belül ebből a sablonból létrehozott egyéni függvényeket.

  * A függvény csak akkor használ egyéni útvonalakat, ha megadott egy [OpenAPI-definíciót](../azure-functions/functions-openapi-definition.md) (korábbi nevén [Swagger-fájlt).](https://swagger.io/)

  * Ha rendelkezik a függvény OpenAPI-definíciójával, a Logic Apps Designer gazdagabb élményt nyújt a funkcióparaméterekkel végzett munka során. Mielőtt a logikai alkalmazás megkeresheti és elérhesse az OpenAPI-definíciókkal rendelkező függvényeket, [állítsa be a függvényalkalmazást az alábbi lépések végrehajtásával.](#function-swagger)

* Az a logikai alkalmazás, amelyhez hozzá szeretné adni a függvényt, beleértve az [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts) is a logikai alkalmazás első lépéseként

  A függvényeket futtató műveletek hozzáadása előtt a logikai alkalmazásnak egy eseményindítóval kell kezdődnie. Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>OpenAPI-leírással rendelkező függvények keresése

A Logic Apps Designer függvényparamétereinek használata során a függvényparaméterekkel való gazdagabb élmény [érdekében hozzon létre egy OpenAPI-definíciót](../azure-functions/functions-openapi-definition.md), amelyet korábban [Swagger-fájlnak](https://swagger.io/)is nevezünk a függvényhez. Ha úgy szeretné beállítani a függvényalkalmazást, hogy a logikai alkalmazás megtalálja és használja a Swagger-leírásokkal rendelkező függvényeket, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a függvényalkalmazás aktívan fut.

1. A függvényalkalmazásban állítsa be az [eredetközi erőforrás-megosztást (CORS),](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) hogy az alábbi lépések végrehajtásával minden eredete tegyék lehetővé:

   1. A **Függvényalkalmazások** listában válassza ki a függvényalkalmazást. A jobb oldali ablaktáblában válassza **a Platform CORS szolgáltatása** > **CORS**lehetőséget.

      ![Válassza ki a funkcióalkalmazást > "Platform funkciók" > a "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. A **CORS**csoportban adja**`*`** hozzá a csillag ( ) helyettesítő karaktert, de távolítsa el a lista összes többi forrását, és válassza a **Mentés gombot.**

      ![Állítsa a "CORS* beállítását a helyettesítő karakterre "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Tulajdonságértékek elérése HTTP-kérelmekben

A Webhook-függvények fogadhatják a HTTP-kérelmeket bemenetként, és továbbítják ezeket a kérelmeket más függvényeknek. Például, bár a Logic Apps olyan függvényekkel rendelkezik, [amelyek konvertálják a DateTime értékeket,](../logic-apps/workflow-definition-language-functions-reference.md)ez az alapvető Minta JavaScript-függvény bemutatja, hogyan érheti el a függvénynek átadott kérelemobjektumon belüli tulajdonságot, és műveleteket hajthat végre az adott tulajdonságértéken. Az objektumokon belüli tulajdonságok eléréséhez ez a példa a [pont (.) operátort](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)használja:

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Itt van, mi történik ebben a funkcióban:

1. A függvény `data` létrehoz egy változót, és hozzárendeli az `body` objektumon belüli objektumot a `request` változóhoz. A függvény a pont (.) operátorral hivatkozik az `body` `request` objektumon belüli objektumra:

   ```javascript
   var data = request.body;
   ```

1. A függvény most `date` már elérheti `data` a tulajdonságot a változón keresztül, és a `ToDateString()` tulajdonságértékét DateTime típusból DateString típusra konvertálhatja a függvény hívásával. A függvény a függvény `body` válaszában lévő tulajdonságon keresztül is adja vissza az eredményt:

   ```javascript
   body: data.date.ToDateString();
   ```

Most, hogy létrehozta az Azure-függvényt, kövesse a [függvények logikai alkalmazásokhoz való hozzáadásának lépéseit.](#add-function-logic-app)

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Függvények létrehozása logikai alkalmazásokon belül

Mielőtt létrehozhat egy Azure-függvényt a logikai alkalmazástervező használatával kezdve, először rendelkeznie kell egy Azure-függvényalkalmazással, amely a függvények tárolója. Ha nem rendelkezik függvényalkalmazással, először hozza létre a függvényalkalmazást. Lásd: [Az első függvény létrehozása az Azure Portalon.](../azure-functions/functions-create-first-azure-function.md)

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A függvény létrehozásához és hozzáadásához kövesse a forgatókönyvre vonatkozó lépést:

   * A logikai alkalmazás munkafolyamatának utolsó lépése alatt válassza az **Új lépés lehetőséget.**

   * A logikai alkalmazás munkafolyamatának meglévő lépései között vigye az egeret a nyíl fölé, jelölje ki a plusz (+) jelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként az "azure functions" kifejezést. A műveletek listájában válassza ki az **Azure-függvény kiválasztása** műveletet, például:

   ![Az "Azure-függvények" megkeresése](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A függvényalkalmazások listájából válassza ki a függvényalkalmazást. A műveletlista megnyitása után válassza a következő műveletet: **Új függvény létrehozása**

   ![A függvényalkalmazás kiválasztása](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. A függvénydefiníció-szerkesztőben adja meg a függvényt:

   1. A **Függvény neve** mezőben adja meg a függvény nevét.

   1. A **Kód** mezőben adja hozzá a kódot a függvénysablonhoz, beleértve a választ és a hasznos terhet, amelyet vissza szeretne adni a logikai alkalmazásnak a függvény futásának befejezése után. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   Példa:

   ![A függvény definiálása](./media/logic-apps-azure-functions/add-code-function-definition.png)

   A sablon kódjában az * `context` objektum* arra az üzenetre hivatkozik, amelyet a logikai alkalmazás egy későbbi lépésben küld a **Kérelem törzs** mezőjén keresztül. Az objektum `context` tulajdonságainak a függvényen belülről történő eléréséhez használja ezt a szintaxist:

   `context.body.<property-name>`

   Ha például az `content` objektumon `context` belüli tulajdonságra szeretne hivatkozni, használja ezt a szintaxist:

   `context.body.content`

   A sablonkód tartalmaz `input` egy változót is, `data` amely tárolja a paraméter értékét, így a függvény műveleteket hajthat végre az adott értéken. Belül JavaScript funkciók, a `data` változó `context.body`is egy parancsikont .

   > [!NOTE]
   > A `body` tulajdonság itt `context` vonatkozik az objektumra, és nem ugyanaz, mint a **body** token egy művelet kimenetén, amely et is átadhat a függvény.

1. A **Kérelem törzse** mezőben adja meg a függvény bemenetét, amelyet JavaScript-objektumjelölés (JSON) objektumként kell formázni.

   Ez a bemenet a *logikai alkalmazás* által a függvénynek küldött környezetobjektum vagy üzenet. Ha a **Törzs kérése** mezőre kattint, megjelenik a dinamikus tartalomlista, így az előző lépésekből kiválaszthat tokeneket a kimenetekhez. Ebben a példában adja meg, hogy `content` a környezeti hasznos tartalom tartalmaz egy tulajdonság nevű, amely rendelkezik a **From** token értéke az e-mail eseményindító.

   !["Request Body" példa - context object hasznos adat](./media/logic-apps-azure-functions/function-request-body-example.png)

   Itt a környezeti objektum nem adja le karakterláncként, így az objektum tartalma közvetlenül hozzáadódik a JSON hasznos adathoz. Ha azonban a környezeti objektum nem egy JSON-jogkivonat, amely átad egy karakterláncot, egy JSON-objektumot vagy egy JSON-tömböt, hibaüzenetet kap. Tehát ha ez a példa a **Beérkezett idő** jogkivonatot használta, a környezeti objektumot karakterláncként is leadhatja dupla idézőjelek hozzáadásával.

   ![Öntött objektum karakterláncként](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. További részletek megadásához, például a használandó módszerhez, a lekérdezési paraméterek hez vagy a lekérdezési paraméterekhez vagy hitelesítéshez nyissa meg az **Új paraméter hozzáadása** listát, és adja meg a kívánt beállításokat. A hitelesítéshez a beállítások a kiválasztott függvénytől függően eltérőek lehetnek. Lásd: [Hitelesítés engedélyezése az Azure-függvényekhez.](#enable-authentication-functions)

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Meglévő függvények hozzáadása a logikai alkalmazásokhoz

A meglévő Azure-függvények hívása a logikai alkalmazások, azure-függvények hozzáadása, mint bármely más művelet a Logic App Designer.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A funkció hozzáadásának lépése alatt válassza az **Új lépés**lehetőséget.

1. A **Művelet kiválasztása csoportkereső**mezőjébe írja be szűrőként az "azure functions" parancsot. A műveletek listájában válassza ki az **Azure-függvény kiválasztása** műveletet.

   ![Az "Azure-függvények" megkeresése](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A függvényalkalmazások listájából válassza ki a függvényalkalmazást. A függvénylista megjessze, és válassza ki a funkciót.

   ![Válassza ki a függvényalkalmazást és az Azure-függvényt](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Az API-definíciókkal (Swagger leírásokkal) rendelkező függvények esetében, amelyek úgy vannak [beállítva, hogy a logikai alkalmazás megtalálja és elérhesse ezeket a függvényeket,](#function-swagger)kiválaszthatja a **Swagger-műveletek et.**

   ![Válassza ki a függvényalkalmazást, a "Swagger-műveleteket", valamint az Azure-függvényt](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. A **Kérelem törzse** mezőben adja meg a függvény bemenetét, amelyet JavaScript-objektumjelölés (JSON) objektumként kell formázni.

   Ez a bemenet a *logikai alkalmazás* által a függvénynek küldött környezetobjektum vagy üzenet. Ha a **Törzs kérése** mezőre kattint, megjelenik a dinamikus tartalomlista, így az előző lépésekből kiválaszthat tokeneket a kimenetekhez. Ebben a példában adja meg, hogy `content` a környezeti hasznos tartalom tartalmaz egy tulajdonság nevű, amely rendelkezik a **From** token értéke az e-mail eseményindító.

   !["Request Body" példa - context object hasznos adat](./media/logic-apps-azure-functions/function-request-body-example.png)

   Itt a környezeti objektum nem adja le karakterláncként, így az objektum tartalma közvetlenül hozzáadódik a JSON hasznos adathoz. Ha azonban a környezeti objektum nem egy JSON-jogkivonat, amely átad egy karakterláncot, egy JSON-objektumot vagy egy JSON-tömböt, hibaüzenetet kap. Ha tehát ez a példa a **Beérkezett idő** jogkivonatot használta, akkor a környezeti objektumot karakterláncként is leadhatja dupla idézőjelek hozzáadásával:

   ![Öntött objektum karakterláncként](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. További részletek megadásához, például a használandó módszerhez, a kérelemfejlécek, a lekérdezési paraméterek vagy a hitelesítés megadásához nyissa meg az **Új paraméter hozzáadása** listát, és adja meg a kívánt beállításokat. A hitelesítéshez a beállítások a kiválasztott függvénytől függően eltérőek lehetnek. Lásd: [Hitelesítés engedélyezése az Azure-függvényekben.](#enable-authentication-functions)

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Logikai alkalmazások hívása az Azure-függvényekből

Ha egy Azure-függvényen belülről szeretne elindítani egy logikai alkalmazást, a logikai alkalmazásnak egy olyan eseményindítóval kell kezdődnie, amely egy hívható végpontot biztosít. Például elindíthatja a logikai alkalmazást a **HTTP,** **Request**, **Azure Queues**vagy **Event Grid** eseményindítóval. A függvényen belül küldjön egy HTTP POST-kérelmet az eseményindító URL-címére, és adja meg a logikai alkalmazás által feldolgozandó hasznos terhet. További információt a [Hívás, eseményindító vagy logikai alkalmazások beágyazása című témakörben talál.](../logic-apps/logic-apps-http-endpoint.md)

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Hitelesítés engedélyezése az Azure-függvényekhez

Más Azure Active Directory (Azure AD) bérlők erőforrásaihoz való hozzáférés hitelesítéséhez anélkül, hogy be kellene jelentkeznie, és hitelesítő adatokat vagy titkos kulcsokat kellene megadnia, a logikai alkalmazás egy [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) (korábbi nevén felügyelt szolgáltatásidentitást vagy MSI-t) használhat. Az Azure kezeli ezt az identitást az Ön számára, és segít a hitelesítő adatok biztonságossá tétele, mert nem kell megadnia vagy elforgatnia a titkos kulcsokat. További információ az [Azure AD-hitelesítéshez felügyelt identitásokat támogató Azure-szolgáltatásokról.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

Ha úgy állítja be a logikai alkalmazást, hogy a rendszer által hozzárendelt identitást vagy egy manuálisan létrehozott, felhasználó által hozzárendelt identitást használjon, a logikai alkalmazásban az Azure-függvények is használhatják ugyanazt az identitást a hitelesítéshez. Az Azure-függvények logikai alkalmazásokban való hitelesítési támogatásáról a [Hitelesítés hozzáadása kimenő hívásokhoz című](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)témakörben talál további információt.

A felügyelt identitás beállítása és használata a funkcióval, kövesse az alábbi lépéseket:

1. Engedélyezze a felügyelt identitást a logikai alkalmazásban, és állítsa be az identitás hozzáférését a célerőforráshoz. Lásd: [Hozzáférés hitelesítése az Azure-erőforrásokhoz felügyelt identitások használatával az Azure Logic Apps-ben.](../logic-apps/create-managed-service-identity.md)

1. Engedélyezze a hitelesítést az Azure függvény- és függvényalkalmazásban az alábbi lépésekkel:

   * [Névtelen hitelesítés beállítása a függvényben](#set-authentication-function-app)
   * [Az Azure AD-hitelesítés beállítása a függvényalkalmazásban](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Névtelen hitelesítés beállítása a függvényben

A logikai alkalmazás felügyelt identitásának az Azure-függvényben való használatához a függvény hitelesítési szintjét névtelenre állította be. Ellenkező esetben a logikai alkalmazás "BadRequest" hibát okoz.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a függvényalkalmazást. Ezek a lépések a "FabrikamFunctionApp" függvényalkalmazásként használják.

1. A függvényalkalmazás ablaktábláján válassza a **Platform funkciói lehetőséget.** A **Fejlesztői eszközök csoportban**válassza **a Speciális eszközök (Kudu) lehetőséget.**

   ![Nyissa meg a kudu speciális eszközeit](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. A Kudu webhely címsorának címsorába, a **Debug Console** menüben válassza a **CMD parancsot.**

   ![A debug konzol menüjében válassza a "CMD" lehetőséget](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Miután a következő oldal megjelenik, a mappalistából **válassza** > a**wwwroot** > *hely funkciót.* Ezek a lépések a "FabrikamAzureFunction" függvényt használják példaként.

   ![Válassza ki a "site" > "wwwroot" > a funkció](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Nyissa `function.json` meg a fájlt szerkesztésre.

   ![Kattintson a "function.json" fájl szerkesztési gombjára](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Az `bindings` objektumban ellenőrizze, `authLevel` hogy a tulajdonság létezik-e. Ha a tulajdonság létezik, `anonymous`állítsa a tulajdonság értékét . Ellenkező esetben adja hozzá ezt a tulajdonságot, és állítsa be az értéket.

   ![Add hozzá az "authLevel" tulajdonságot, és állítsd be a "névtelen" értéket](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Ha elkészült, mentse a beállításokat, majd folytassa a következő szakasszal.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Az Azure AD-hitelesítés beállítása a függvényalkalmazáshoz

A feladat megkezdése előtt keresse meg és tegye félre ezeket az értékeket későbbi használatra:

* A rendszeráltal kijelölt identitáshoz létrehozott objektumazonosító, amely a logikai alkalmazást jelöli

  * Az objektumazonosító létrehozásához [engedélyezze a logikai alkalmazás rendszeráltal hozzárendelt identitását.](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)

  * Ellenkező esetben az objektumazonosító megkereséséhez nyissa meg a logikai alkalmazást a Logic App Designerben. A logikai alkalmazás menüjében válassza a **Beállítások** **csoportban** > a**Hozzárendelt identitásrendszer lehetőséget.**

* A bérlő címtárazonosítója az Azure Active Directoryban (Azure AD)

  A bérlő könyvtárazonosítójának lekért, futtathatja a [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) Powershell parancsot. Vagy az Azure Portalon kövesse az alábbi lépéseket:

  1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a függvényalkalmazást.

  1. Keresse meg és válassza ki az Azure AD-bérlőt. Ezek a lépések a "Fabrikam" mint példa bérlő.

  1. A bérlő menüjének **Kezelése**területén válassza a **Tulajdonságok lehetőséget.**

  1. Másolja például a bérlő könyvtárazonosítóját, és mentse el az azonosítót későbbi használatra.

     ![Az Azure AD-bérlő címtárazonosítójának megkeresése és másolása](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Az elérni kívánt célerőforrás erőforrásazonosítója

  * Ezek az erőforrás-azonosítók, tekintse át az [Azure-szolgáltatások, amelyek támogatják az Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

  > [!IMPORTANT]
  > Ennek az erőforrás-azonosítónak pontosan meg kell egyeznie az Azure AD által elvárt értékkel, beleértve a szükséges követő perjeleket is.

  Ez az erőforrás-azonosító ugyanaz az érték is, amelyet később a **Célközönség** tulajdonságban használ, amikor [a rendszeráltal hozzárendelt identitás használatára állítja be a függvényműveletet.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

Most már készen áll az Azure AD-hitelesítés beállítására a függvényalkalmazáshoz.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a függvényalkalmazást.

1. A függvényalkalmazás ablaktábláján válassza a **Platform funkciói lehetőséget.** A **Hálózat csoportban**válassza a **Hitelesítés/engedélyezés**lehetőséget.

   ![Hitelesítési és engedélyezési beállítások megtekintése](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Módosítsa az **App Service-hitelesítés** beállítást **Be**beállításra. A **művelet, hogy ha a kérelem nem hitelesített** lista, válassza a Bejelentkezés az Azure Active **Directoryval**lehetőséget. A **Hitelesítésszolgáltatók csoportban**válassza az **Azure Active Directory**lehetőséget.

   ![A hitelesítés bekapcsolása az Azure AD-vel](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Az **Azure Active Directory beállításai** ablaktáblán hajtsa végre az alábbi lépéseket:

   1. Állítsa **a Felügyeleti módot** **Speciális**módra.

   1. Az **Ügyfélazonosító** tulajdonságban adja meg a logikai alkalmazás rendszeráltal hozzárendelt identitásának objektumazonosítóját.

   1. A **Kiállító URL-cím** tulajdonságában adja meg az `https://sts.windows.net/` URL-címet, és fűzz hozzá az Azure AD-bérlő címtárazonosítóját.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Az **Engedélyezett jogcímcsoportok** tulajdonságban adja meg az elérni kívánt célerőforrás erőforrásazonosítóját.

      Ez az erőforrás-azonosító megegyezik azzal az értékkel, amelyet később a **Célközönség** tulajdonságban használ, amikor [a rendszeráltal hozzárendelt identitás használatára állítja be a függvényműveletet.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

   Ezen a ponton a verzió hasonlít a példához:

   ![Az Azure Active Directory hitelesítési beállításai](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Amikor elkészült, válassza az **OK** lehetőséget.

1. Térjen vissza a Logic App Designerhez, és kövesse a [lépéseket a hozzáférés hitelesítéséhez a felügyelt identitással.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

## <a name="next-steps"></a>További lépések

* Tudnivalók a [Logic Apps-összekötőkről](../connectors/apis-list.md)
