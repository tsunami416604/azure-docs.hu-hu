---
title: HTTP-szolgáltatások a Durable Functions-Azure Functions
description: Ismerje meg a Azure Functions Durable Functions bővítményének integrált HTTP-funkcióit.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: b909918ff4f9abc1dd64d4c7e5ccb35954b233f7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935793"
---
# <a name="http-features"></a>HTTP-funkciók

A Durable Functions számos funkcióval rendelkezik, amelyekkel könnyedén beépítheti a tartós és az entitásokat a HTTP-munkafolyamatokban. Ez a cikk részletesen ismerteti ezeket a funkciókat.

## <a name="exposing-http-apis"></a>HTTP API-k kimutatása

A bevezetések és az entitások HTTP-kérelmek használatával hívhatók és kezelhetők. A Durable Functions bővítmény elérhetővé teszi a beépített HTTP API-kat, és API-kat biztosít a munkafolyamatokkal és az entitásokkal való interakcióhoz a HTTP által aktivált függvények között.

### <a name="built-in-http-apis"></a>Beépített HTTP API-k

A Durable Functions bővítmény automatikusan hozzáadja a HTTP API-kat a Azure Functions gazdagéphez. Ezek az API-k lehetővé teszik az összehangolás és az entitások kezelését a kódok írása nélkül.

A következő beépített HTTP API-k támogatottak.

* [Új előkészítés indítása](durable-functions-http-api.md#start-orchestration)
* [Lekérdezés-előkészítési példány](durable-functions-http-api.md#get-instance-status)
* [Összehangoló példány leállítása](durable-functions-http-api.md#terminate-instance)
* [Külső esemény küldése egy előkészítési folyamatnak](durable-functions-http-api.md#raise-event)
* [Előkészítési előzmények törlése](durable-functions-http-api.md#purge-single-instance-history)
* [Műveleti esemény küldése egy entitásnak](durable-functions-http-api.md#signal-entity)
* [Entitás állapotának lekérdezése](durable-functions-http-api.md#query-entity)

A Durable Functions bővítmény által elérhető beépített HTTP API-k teljes leírását a [http API](durable-functions-http-api.md) -k című cikkben találja.

### <a name="http-api-url-discovery"></a>HTTP API URL-cím felderítése

A hangolási [ügyfél kötése](durable-functions-bindings.md#orchestration-client) olyan API-kat tesz elérhetővé, amelyekkel kényelmes http-válaszokra vonatkozó hasznos adatokat lehet előállítani. Létrehozhat például egy olyan választ, amely egy adott előkészítési példányhoz tartozó felügyeleti API-kra mutató hivatkozásokat tartalmaz. Az alábbi példa egy HTTP-trigger függvényt mutat be, amely bemutatja, hogyan használhatja ezt az API-t egy új előkészítési példányhoz:

#### <a name="precompiled-c"></a>ElőfordítottC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C#Parancsfájl

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

A fentiekben ismertetett HTTP-trigger függvények használatával egy Orchestrator-függvény indítása bármely HTTP-ügyféllel végezhető el. A következő cURL-parancs elindít egy nevű `DoWork`Orchestrator-függvényt.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Íme egy példa egy, a (z) azonosítóval rendelkező `abc123` előkészítési válaszra (az egyértelműség érdekében néhány részletet eltávolított):

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Az előző példában szereplő összes mezőmegfelelabeépítetthttpAPI-knak.`~Uri` Ezek az API-k használhatók a cél-előkészítési példány kezeléséhez.

> [!NOTE]
> A webhook URL-címeinek formátuma eltérő lehet attól függően, hogy a Azure Functions gazdagép melyik verzióját futtatja. A fenti példa a Azure Functions 2,0 gazdagépre mutat.

Az összes beépített HTTP API leírását a [http API](durable-functions-http-api.md) -dokumentációban találja.

### <a name="async-operation-tracking"></a>Aszinkron művelet követése

A korábban említett HTTP-válasz úgy lett kialakítva, hogy segítse a hosszú ideig futó HTTP aszinkron API-k megvalósítását Durable Functions. Ezt a mintát más néven a *lekérdezési fogyasztói mintának*nevezzük. Az ügyfél/kiszolgáló folyamat a következőképpen működik:

1. Az ügyfél HTTP-kérést bocsát ki egy hosszú ideig futó folyamat elindításához, például egy Orchestrator függvényt.
2. A cél http-trigger egy http 202 választ ad vissza `Location` , amely egy `statusQueryGetUri` fejlécet tartalmaz az értékkel.
3. Az ügyfél lekérdezi az URL- `Location` címet a fejlécben. Továbbra is megjelenik `Location` a http 202-válaszok fejléctel.
4. Ha a példány befejeződik (vagy sikertelen), a `Location` fejlécben található végpont a http 200 értéket adja vissza.

Ez a protokoll lehetővé teszi a hosszan futó folyamatok koordinálását olyan külső ügyfelekkel vagy szolgáltatásokkal, amelyek támogatják a http- `Location` végpontok lekérdezését és a fejlécet követve. A minta ügyfél-és kiszolgáló-implementációja is be van építve a Durable Functions HTTP API-khoz.

> [!NOTE]
> Alapértelmezés szerint a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) által biztosított összes HTTP-alapú művelet támogatja a normál aszinkron műveleti mintát. Ez a funkció lehetővé teszi a hosszan futó tartós funkciók beágyazását egy Logic Apps munkafolyamat részeként. Az aszinkron HTTP-minták támogatásáról Logic Apps további részleteket a [Azure Logic apps munkafolyamat-műveletek és eseményindítók dokumentációjában](../../logic-apps/logic-apps-workflow-actions-triggers.md)talál.

> [!NOTE]
> A feladatokkal való interakció bármely Function típusból elvégezhető, nem csak a HTTP által aktivált függvényekből.

A következő témakörben talál további információt arról, hogyan kezelheti a folyamatokat és entitásokat az ügyféloldali API-k használatával: a [példányok kezelése](durable-functions-instance-management.md) című cikk.

## <a name="consuming-http-apis"></a>HTTP API-k fogyasztása

A Orchestrator függvények nem jogosultak az I/O-műveletek közvetlen végrehajtására, ahogy azt a [Orchestrator-függvény kódjainak megkötései](durable-functions-code-constraints.md)című témakör írja le. Ehelyett a Orchestrator functions általában az I/O-műveleteket végrehajtó [tevékenységi funkciókat](durable-functions-types-features-overview.md#activity-functions) hívja meg.

A Durable Functions 2,0-től kezdve a megszervezések képesek natív módon felhasználni a HTTP API-kat a koordináló [trigger kötés](durable-functions-bindings.md#orchestration-trigger)használatával.

> [!NOTE]
> A HTTP-végpontok közvetlenül a Orchestrator függvényekből való meghívásának lehetősége még nem érhető el a JavaScriptben.

A következő mintakód egy C# Orchestrator függvényt mutat be, amely egy kimenő http- `CallHttpAsync` kérést használ a .NET API használatával:

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

A "HTTP hívása" művelettel a következő műveleteket végezheti el a Orchestrator függvényekben:

* HTTP API-k hívása közvetlenül a hangkezelő függvényektől (bizonyos korlátozásokkal később).
* Az ügyféloldali HTTP 202-állapot lekérdezési mintáinak automatikus támogatása.
* Az [Azure által felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) használatával a hitelesítő http-hívásokat más Azure-végpontokhoz is elvégezheti.

A HTTP-API-k közvetlenül a Orchestrator függvényekből való használatának lehetősége a gyakori forgatókönyvek bizonyos készletének kényelmét szolgálja. Az összes funkciót saját maga is megvalósíthatja a Activity functions használatával. Számos esetben a tevékenység funkciói nagyobb rugalmasságot biztosíthatnak.

### <a name="http-202-handling"></a>HTTP 202-kezelő

A "HTTP hívása" API automatikusan megvalósíthatja a *lekérdezési fogyasztói minta*ügyféloldali oldalát. Ha a hívott API egy `Location` fejléctel rendelkező http 202-választ ad vissza, akkor a Orchestrator függvény automatikusan lekérdezi az `Location` erőforrást, amíg nem érkezik vissza a nem 202 válasz. A nem 202 válasz a válasz a Orchestrator függvény kódjába kerül.

> [!NOTE]
> A Orchestrator függvények natív módon támogatják a kiszolgálóoldali *lekérdezési fogyasztói mintát*is, az [aszinkron műveletek nyomon követése](#async-operation-tracking)című témakörben leírtak szerint. Ez azt jelenti, hogy az egyik Function alkalmazásban az összehangolás egyszerűen koordinálhatja a Orchestrator függvényeket más függvények alkalmazásaiban. Ez hasonló a [beszerelési](durable-functions-sub-orchestrations.md) koncepcióhoz, de támogatja az alkalmazások közötti kommunikációt. Ez különösen hasznos a szolgáltatás-stílusú alkalmazások fejlesztéséhez.

### <a name="managed-identities"></a>Felügyelt identitások

A Durable Functions natív módon támogatja az Azure AD-jogkivonatokat elfogadó API-kat az engedélyezéshez. Ez a támogatás az [Azure által felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) használatával szerzi be ezeket a jogkivonatokat.

A következő kód egy olyan .NET Orchestrator-függvényre mutat példát, amely a Azure Resource Manager [Virtual Machines REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)használatával hitelesített hívásokat kezdeményez a virtuális gépek újraindításához.

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

Az előző példában a `tokenSource` paraméter úgy van konfigurálva, hogy beszerezze az Azure ad-jogkivonatokat a [Azure Resource Managerhoz](../../azure-resource-manager/resource-group-overview.md) (az `https://management.core.windows.net`erőforrás URI azonosítója alapján azonosítva). A példa feltételezi, hogy az aktuális Function alkalmazás vagy helyileg fut, vagy felügyelt identitással Azure Functions alkalmazásként lett telepítve. A rendszer feltételezi, `myRG`hogy a helyi identitás vagy a felügyelt identitás jogosult a virtuális gépek kezelésére a megadott erőforráscsoporthoz.

Futásidőben a konfigurált jogkivonat-forrás automatikusan egy OAuth 2,0 hozzáférési tokent ad vissza, és a kimenő kérelem `Authorization` fejlécéhez hozzáadja tulajdonosi jogkivonatként. Ez a modell javítja az engedélyezési fejlécek manuális hozzáadását a HTTP-kérelmekhez, mert:

* A rendszer automatikusan kezeli a jogkivonat-frissítést. Nem kell aggódnia a lejárt jogkivonatokkal kapcsolatban.
* A tokeneket a rendszer soha nem tárolja tartós előkészítési állapotban.
* Nem kell kódot írnia a jogkivonat-beszerzéssel kapcsolatos problémák kezelésére.

A teljes példa az előre [lefordított C# "RestartVMs" mintákban](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)található.

A felügyelt identitások nem korlátozódnak az Azure erőforrás-kezelésre. A felügyelt identitások olyan API-k elérésére használhatók, amelyek elfogadják az Azure AD tulajdonosi jogkivonatait, beleértve az első féltől származó Azure-szolgáltatásokat vagy harmadik féltől származó webalkalmazásokat is. A harmadik féltől származó webalkalmazás akár egy másik Function alkalmazás is lehet. Az Azure AD-hitelesítést támogató, első féltől származó Azure-szolgáltatások listáját az Azure [ad-hitelesítést támogató Azure-szolgáltatások](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)című témakörben tekintheti meg.

### <a name="limitations"></a>Korlátozások

A HTTP API-k meghívására szolgáló beépített támogatás egy kényelmi funkció, amely minden esetben nem megfelelő. A Orchestrator függvények által küldött HTTP-kérések és azok válaszai szerializálva vannak, és üzenetsor-üzenetekként is megmaradnak. Ez a várólista-kezelési viselkedés biztosítja a HTTP [-hívások megbízhatóságát és biztonságát a rendezési ismétlésekhez](durable-functions-orchestrations.md#reliability). Ez a várakozási mód azonban azt is jelenti, hogy:

* Minden HTTP-kérelemhez a natív HTTP-ügyfélhez képest további késleltetés szükséges.
* A várólista-üzenetbe nem illeszkedő nagyméretű kérelmek vagy válaszüzenetek jelentősen csökkenthetik a folyamat teljesítményét. A lehetséges teljesítmény romlása az üzenetek blob Storage-ba való kiszervezésének terhelése miatti.
* A folyamatos átviteli, a darabolási és a bináris adattartalom nem támogatott.
* A HTTP-ügyfél viselkedésének testre szabása korlátozott.

Ha a korlátozások bármelyike hatással lehet a használati esetre, érdemes inkább a tevékenység-és nyelvspecifikus HTTP-ügyféloldali kódtárakat használni a kimenő HTTP-hívások elvégzéséhez.

> [!NOTE]
> Ha Ön .net-fejlesztő, akkor lehet, hogy kíváncsi, hogy miért használja `DurableHttpRequest` `DurableHttpResponse` a szolgáltatás a beépített .net `HttpRequestMessage` -és `HttpResponseMessage`a-típusokat. Ez a tervezési lehetőség szándékos volt. Az elsődleges ok az, hogy az egyéni típusok segítségével biztosítható, hogy a felhasználók ne tegyenek helytelen feltételezéseket a belső HTTP-ügyfél által támogatott viselkedések tekintetében. A tartós-specifikus típusok révén egyszerűbbé válik az API-kialakítás, és könnyebben megoldhatók a speciális funkciók, például a [felügyelt identitások integrálása](#managed-identities) és a [lekérdezési fogyasztói minta](#http-202-handling).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A tartós entitások megismerése](durable-functions-entities.md)