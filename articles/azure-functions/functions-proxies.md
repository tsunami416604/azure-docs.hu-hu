---
title: Proxyk kal való kapcsolata az Azure Functionsben
description: Az Azure Functions proxyk használatának áttekintése
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849190"
---
# <a name="work-with-azure-functions-proxies"></a>Az Azure Functions proxyinak munkája

Ez a cikk bemutatja, hogyan konfigurálható és együtt működik az Azure Functions proxykkal. Ezzel a funkcióval megadhatja a függvényalkalmazás végpontjait, amelyeket egy másik erőforrás valósít meg. Ezekkel a proxykkal bonthatja a nagy API-k több függvényalkalmazások (mint egy mikroszolgáltatás-architektúra), miközben továbbra is egyetlen API-felület et az ügyfelek számára.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> A Standard Functions számlázása a proxy-végrehajtásokra vonatkozik. További információ: [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Proxy létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre proxyt a Függvények portálon.

1. Nyissa meg az [Azure Portalon,]majd nyissa meg a függvényalkalmazást.
2. A bal oldali ablaktáblában válassza az **Új proxy**lehetőséget.
3. Adja meg a proxy nevét.
4. Konfigurálja a függvényalkalmazásban elérhető végpontot az **útvonalsablon** és a **HTTP-metódusok**megadásával. Ezek a paraméterek a [HTTP-eseményindítók]szabályaiszerint viselkednek.
5. Állítsa be a **háttércím-címet** egy másik végpontra. Ez a végpont lehet egy függvény egy másik függvényalkalmazásban, vagy lehet bármely más API.This endpoint could be a function in another function app, or it could be any other API. Az értéknek nem kell statikusnak lennie, és hivatkozhat az eredeti ügyfélkérelem [alkalmazásbeállításaira] és [paramétereire.]
6. Kattintson **a Létrehozás gombra.**

A proxy most már létezik, mint egy új végpont a függvényalkalmazásban. Ügyfél szempontból egyenértékű egy HttpTrigger az Azure Functionsben. Kipróbálhatja az új proxyt a proxy URL-címének másolásával és a kedvenc HTTP-ügyféllel való tesztelésével.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Kérések és válaszok módosítása

Az Azure Functions proxyk segítségével módosíthatja a háttér-feladatokra érkező kérelmeket és válaszokat. Ezek az átalakítások a Felhasználási változók ban meghatározott [változókat használhatnak.]

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>A háttérkérelem módosítása

Alapértelmezés szerint a háttér-kérelem inicializálása az eredeti kérelem másolataként történik. A háttérURL beállítása mellett módosíthatja a HTTP-metódust, a fejléceket és a lekérdezési karakterlánc paramétereit is. A módosított értékek hivatkozhatnak az eredeti ügyfélkérelem [alkalmazásbeállításaira] és [paramétereire.]

A háttérkérelmek a portálon módosíthatók a proxy információs lap *kérelemfelülbírálási* szakaszának kibontásával. 

### <a name="modify-the-response"></a><a name="modify-response"></a>A válasz módosítása

Alapértelmezés szerint az ügyfélválasz inicializálása a háttérválasz másolataként történik. Módosíthatja a válasz állapotkódját, okkifejezését, fejlécét és törzsét. A módosított értékek hivatkozhatnak [az alkalmazás beállításaira], [az eredeti ügyfélkérelem paramétereire]és [a háttérválasz paramétereire].

A háttérkérelmek a portálon módosíthatók a proxy információs lap *válaszfelülbírálási* szakaszának kibontásával. 

## <a name="use-variables"></a><a name="using-variables"></a>Változók használata

A proxy konfigurációjának nem kell statikusnak lennie. Feltétele, hogy az eredeti ügyfélkérelem, a háttérválasz vagy az alkalmazás beállításainak változóit használja.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Helyi függvények hivatkozása
Segítségével `localhost` hivatkozhat egy függvényt ugyanazon a függvényalkalmazáson belül közvetlenül, oda-vissza proxy kérés nélkül.

`"backendurl": "https://localhost/api/httptriggerC#1"`egy helyi HTTP-funkcióra fog hivatkozni az útvonalon`/api/httptriggerC#1`

 
>[!Note]  
>Ha a függvény függvényt használ *függvény, rendszergazdai vagy sys* engedélyezési szintek, meg kell adnia a kódot és clientId, mint az eredeti függvény URL-címét. Ebben az esetben a hivatkozás `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` így nézne ki: Javasoljuk, hogy ezeket a kulcsokat [az alkalmazás beállításaiban] tárolja, és hivatkozzon a proxykban lévőkre. Ezzel elkerülhető, hogy a forráskódban titkokat tároljon. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Hivatkozási kérelem paraméterei

A kérelemparamétereket használhatja a háttérURL-tulajdonság bemeneteként vagy a kérések és válaszok módosításának részeként. Egyes paraméterek az alapproxy-konfigurációban megadott útvonalsablonból is kötve, míg mások a bejövő kérelem tulajdonságaiból származhatnak.

#### <a name="route-template-parameters"></a>Útvonalsablon paraméterei
Az útvonalsablonban használt paraméterek név szerint hivatkozhatók. A paraméterneveket kapcsos{}zárójelek ( ).

Ha például egy proxy rendelkezik útvonalsablonnal, például `/pets/{petId}`a háttérURL-cím a értékét is `{petId}`tartalmazhatja. `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` Ha az útvonalsablon egy helyettesítő karakterben `/api/{*restOfPath}`végződik, például az érték `{restOfPath}` a bejövő kérelem ben fennmaradó elérési útszegmensek karakterlánc-ábrázolása.

#### <a name="additional-request-parameters"></a>További kérelemparaméterek
Az útvonalsablon paraméterein kívül a következő értékek használhatók a konfigurációs értékekben:

* **{request.method}**: Az eredeti kérésben használt HTTP-módszer.
* **{request.headers.\< HeaderName\>}**: Az eredeti kérelemből olvasható fejléc. Cserélje * \<\> * le a HeaderName fejlécet az olvasni kívánt fejléc nevére. Ha a fejléc nem szerepel a kérelemben, az érték lesz az üres karakterlánc.
* **{request.querystring.\< ParameterName\>}**: Olyan lekérdezési karakterlánc-paraméter, amely az eredeti kérelemből olvasható. Cserélje * \<\> * le a ParameterName paraméternevet az olvasni kívánt paraméter nevére. Ha a paraméter nem szerepel a kérelemben, az érték lesz az üres karakterlánc.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Háttérválaszok referenciaparaméterei

A válaszparaméterek az ügyfélnek adott válasz módosításának részeként használhatók. A konfigurációs értékekben a következő értékek használhatók:

* **{backend.response.statusCode}**: A háttérválaszon visszaadott HTTP-állapotkód.
* **{backend.response.statusReason}**: A háttérválaszon visszaadott HTTP-ok kifejezés.
* **{backend.response.headers.\< HeaderName\>}**: A háttérválaszból olvasható fejléc. Cserélje * \<\> * le a HeaderName fejlécet az olvasni kívánt fejléc nevére. Ha a fejléc nem szerepel a válaszban, az érték lesz az üres karakterlánc.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Hivatkozási alkalmazás beállításai

A [függvényalkalmazáshoz definiált alkalmazásbeállításokra hivatkozhat,](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) ha a beállítás nevét százalékjelekkel (%).

Például egy háttér-URL-cím a *https://%ORDER_PROCESSING_HOST%/api/orders* következőlenne: "%ORDER_PROCESSING_HOST%" helyett a ORDER_PROCESSING_HOST érték.

> [!TIP] 
> Ha több központi telepítéssel vagy tesztkörnyezettel rendelkezik, használja az alkalmazásbeállításokat a háttér-állomásokhoz. Így, akkor győződjön meg arról, hogy mindig beszél a jobb back-end az adott környezetben.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Proxyk – problémamegoldásokkal kapcsolatos elhárítás

Ha hozzáadja `"debug":true` a jelzőt `proxies.json` a proxyhoz, engedélyezi a hibakeresési naplózást. Naplók `D:\home\LogFiles\Application\Proxies\DetailedTrace` tárolják, és elérhető a fejlett eszközök (kudu). A HTTP-válaszok tartalmazni `Proxy-Trace-Location` fognak egy fejlécet is, amelynek URL-címe van a naplófájl eléréséhez.

A proxykat az ügyféloldalról is `Proxy-Trace-Enabled` debugolhatja, ha fejléckészletet ad hozzá a hoz. `true` Ez a fájlrendszernyomatot is naplózza, és a válasz fejléceként visszaadja a nyomkövetési URL-címet.

### <a name="block-proxy-traces"></a>Proxy-nyomkövetések blokkolása

Biztonsági okokból előfordulhat, hogy nem szeretné, hogy bárki, aki felhívja a szolgáltatást, hogy nyomkövetést hozzon létre. A naplóhitelesítő adatok nélkül nem férhetnek hozzá a nyomkövetési tartalomhoz, de a nyomkövetés létrehozása erőforrásokat vesz fel, és felfedi, hogy a Függvényproxykat használja.

Tiltsa le a `"debug":false` nyomkövetéseket teljesen `proxies.json`úgy, hogy hozzáadja a proxyt a .

## <a name="advanced-configuration"></a>Speciális konfiguráció

A konfigurált proxyk egy *proxies.json* fájlban tárolódnak, amely egy függvényalkalmazás könyvtárának gyökerében található. Ezt a fájlt manuálisan szerkesztheti és telepítheti az alkalmazás részeként, ha a Functions által támogatott [telepítési módszerek](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) bármelyikét használja. 

> [!TIP] 
> Ha még nem állította be a telepítési módszerek egyikét sem, a *proxies.json* fájllal is dolgozhat a portálon. Nyissa meg a függvényalkalmazást, válassza **a Platform szolgáltatásai**lehetőséget, és válassza az App Service **Editor**lehetőséget. Ezzel megtekintheti a függvényalkalmazás teljes fájlszerkezetét, majd módosíthatja.

*A Proxies.json-t* egy proxy objektum határozza meg, amely elnevezett proxykból és azok definícióiból áll. Ha a szerkesztő támogatja, hivatkozhat egy [JSON-sémára](http://json.schemastore.org/proxies) a kód kiegészítéséhez. Egy példafájl a következőnek tűnhet:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Minden proxy nak van egy rövid neve, például *proxy1* az előző példában. A megfelelő proxydefiníciós objektumot a következő tulajdonságok határozzák meg:

* **matchCondition**: Kötelező – a proxy végrehajtását kiváltó kérelmeket meghatározó objektum. Két, [HTTP-eseményindítókkal]megosztott tulajdonságot tartalmaz:
    * _metódusok_: A HTTP-metódusok azon tömbje, amelyre a proxy válaszol. Ha nincs megadva, a proxy válaszol az útvonalon lévő összes HTTP-metódusra.
    * _route_: Kötelező - meghatározza az útvonalsablont, amely azt szabályozza, hogy a proxy mely kérési URL-címekre válaszol. A HTTP-eseményindítókkal ellentétben nincs alapértelmezett érték.
* **backendUri**: Annak a háttér-erőforrásnak az URL-címe, amelyhez a kérelmet ki kell egészíteni. Ez az érték hivatkozhat az eredeti ügyfélkérelem alkalmazásbeállításaira és paramétereire. Ha ez a tulajdonság nem szerepel, az Azure Functions http 200 OK-val válaszol.
* **requestOverrides**: Olyan objektum, amely meghatározza a háttér-kérelem átalakítását. Lásd: [RequestOverrides objektum definiálása.]
* **responseOverrides**: Olyan objektum, amely az ügyfélválasz átalakítását határozza meg. Lásd: [Válaszfelülírások definiálása objektum.]

> [!NOTE] 
> Az Azure Functions proxyk *útvonaltulajdonsága* nem tartja tiszteletben a Function App gazdagép konfigurációjának *routePrefix* tulajdonságát. Ha olyan előtagot szeretne megadni, mint `/api`például a, annak szerepelnie kell az *útvonal* tulajdonságban.

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a>Az egyes proxyk letiltása

Az egyes proxykat letilthatja, ha hozzáadja `"disabled": true` a `proxies.json` fájlproxyját. Ennek hatására a matchCondition-nek megfelelő kéréseket a 404-es állapot adja vissza.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a>Alkalmazás beállításai

A proxy viselkedését több alkalmazásbeállítás sal is szabályozhatja. Ezek mindegyike a [Funkciók alkalmazás beállításai hivatkozásban](./functions-app-settings.md) található.

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a>Fenntartott karakterek (karakterlánc-formázás)

A proxyk a JSON-fájlösszes karakterláncát kiolvassák, a \ parancsot használva escape szimbólumként. A proxyk kapcsos zárójeleket is értelmeznek. Lásd az alábbi példák teljes sorozatát.

|Karakter|Megmenekült karakter|Példa|
|-|-|-|
|{ vagy }|{{ vagy }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>RequestOverrides objektum definiálása

A requestOverrides objektum határozza meg a kérelemben a háttér-erőforrás hívásakor végrehajtott módosításokat. Az objektumot a következő tulajdonságok határozzák meg:

* **backend.request.method**: A háttérrendszer hívására használt HTTP-módszer.
* **háttérend.request.querystring. ParameterName\>: Olyan lekérdezési karakterlánc-paraméter, amely beállítható a háttérrendszer hívásához. \<** Cserélje * \<\> * le a ParameterName paraméternevet a beállítani kívánt paraméter nevére. Kérjük, vegye figyelembe, hogy ha az üres karakterlánc meg van adva, a paraméter továbbra is szerepel a háttér-kérelemben.
* **háttérend.kérés.headers. HeaderName\>: A háttérhíváshoz beállítható \<** fejléc. Cserélje * \<\> * le a HeaderName fejlécet a beállítani kívánt fejléc nevére. Ha megadja az üres karakterláncot, a fejléc nem szerepel a háttérkérelemben.

Az értékek hivatkozhatnak az eredeti ügyfélkérelem alkalmazásbeállításaira és paramétereire.

Egy példa konfigurációja a következőre mutathat:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>ResponseOverrides objektum definiálása

A requestOverrides objektum határozza meg az ügyfélnek visszaadott válasz módosításait. Az objektumot a következő tulajdonságok határozzák meg:

* **response.statusCode**: Az ügyfélnek visszaküldendő HTTP-állapotkód.
* **response.statusReason**: Az ügyfélnek visszaadandó HTTP okkifejezés.
* **response.body**: Az ügyfélnek visszaküldendő test karakterlánc-ábrázolása.
* **response.headers. HeaderName\>: Olyan fejléc, amely beállítható az ügyfélnek adott válaszhoz. \<** Cserélje * \<\> * le a HeaderName fejlécet a beállítani kívánt fejléc nevére. Ha megadja az üres karakterláncot, a fejléc nem szerepel a válaszban.

Az értékek hivatkozhatnak az alkalmazás beállításokra, az eredeti ügyfélkérelem paramétereire és a háttérválasz paramétereire.

Egy példa konfigurációja a következőre mutathat:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> Ebben a példában a választörzs közvetlenül van beállítva, így nincs `backendUri` szükség tulajdonságra. A példa bemutatja, hogyan használhatja az Azure Functions proxyk at az API-k gúnyolódásához.

[Azure-portál]: https://portal.azure.com
[HTTP-eseményindítók]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[RequestOverrides objektum definiálása]: #requestOverrides
[ResponseOverrides objektum definiálása]: #responseOverrides
[alkalmazás beállításai]: #use-appsettings
[Változók használata]: #using-variables
[paraméterek az eredeti ügyfélkérelemből]: #request-parameters
[paraméterek a háttér-válaszból]: #response-parameters
