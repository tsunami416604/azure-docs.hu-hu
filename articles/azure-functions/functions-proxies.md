---
title: "Az Azure Functions-proxy használata |} Microsoft Docs"
description: "Hogyan használható az Azure Functions proxyk áttekintése"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 75b568c12fd58d5599b6878dedb6c2266b6cb649
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="work-with-azure-functions-proxies"></a>Az Azure Functions proxyk használata

Ez a cikk azt ismerteti, hogyan konfigurálását és az Azure Functions proxyk használatát. Ez a szolgáltatás végpontok is megadhat, a függvény alkalmazások, amelyeket a rendszer egy másik erőforrás. Ezek proxyk segítségével nagy API felosztása több függvény alkalmazásokat (akár egy mikroszolgáltatási architektúra), miközben továbbra is egyetlen API felülete ügyfelek számára.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Szabványos függvényekben számlázási proxy végrehajtások vonatkozik. További információkért lásd: [Azure Functions díjszabási](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>A proxy létrehozása

Ez a szakasz bemutatja, hogyan proxy létrehozására a Functions portálon.

1. Nyissa meg a [Azure-portálon], és keresse meg a függvény alkalmazást.
2. A bal oldali panelen válassza ki a **új proxy**.
3. Adja meg a proxykiszolgáló nevét.
4. Konfigurálja a végpontot, amely fel van fedve függvény alkalmazás megadásával a **útvonalsablonhoz** és **HTTP-metódus**. Ezek a paraméterek szabályainak megfelelően működnek [HTTP eseményindítók].
5. Állítsa be a **háttérkiszolgáló URL-cím** egy másik végponthoz. Ehhez a végponthoz lehet egy másik függvény alkalmazásban függvény, vagy más API-k lehet. Az érték nem lehet statikus kell, és azt is hivatkozni lehessen [Alkalmazásbeállítások] és [az eredeti ügyfélkérés származó paraméterek].
6. Kattintson a **Create** (Létrehozás) gombra.

A proxy most már létezik a függvény alkalmazás egy új végponton. Az ügyfél szempontjából akkor az Azure Functions egy HttpTrigger egyenértékű. Az új proxy kipróbálhatja a proxykiszolgáló URL-cím másolásával, és vizsgálja, hogy a kedvenc HTTP-ügyféllel.

## <a name="modify-requests-responses"></a>Kérelem és válasz módosítása

Az Azure Functions proxykat módosíthatja a kérelmek és a háttér-válaszát. Az átalakításokat változókat is használhat, a [változókkal].

### <a name="modify-backend-request"></a>Módosítsa a háttér-kérés

Alapértelmezés szerint a háttér-kérelem, az eredeti kérelem másolatként inicializálása. A háttérkiszolgáló URL-cím mellett, a HTTP metódus, a fejlécek és a lekérdezési karakterlánc paraméterek módosításokat végezheti el. A módosított értékek hivatkozhatnak [Alkalmazásbeállítások] és [az eredeti ügyfélkérés származó paraméterek].

Háttér-kérelmek módosíthatják a portálon expading a *felülbírálás kérelem* proxy Részletek lap részében. 

### <a name="modify-response"></a>A válasz módosítása

Alapértelmezés szerint az ügyfél válaszára inicializálva van a háttér-válasz egy másolatát. A válasz állapotkódja, indoklás, fejlécek és body módosításokat végezheti el. A módosított értékek hivatkozhatnak [Alkalmazásbeállítások], [az eredeti ügyfélkérés származó paraméterek], és [paramétert a háttér-válasz].

Háttér-kérelmek módosíthatják a portálon expading a *válasz felülbírálás* proxy Részletek lap részében. 

## <a name="using-variables"></a>Változók használata

A proxy konfigurációját nem kell lehet statikus. Feltétel úgy, hogy az eredeti ügyfélkérés, a háttér-válasz vagy alkalmazásbeállítások változókat használja.

### <a name="reference-localhost"></a>Hivatkozás helyi funkciók
Használhat `localhost` egy függvényre belüli függvény ugyanazon alkalmazás közvetlenül, körbejárási proxy kérelmet nélkül.

`"backendurl": "https://localhost/api/httptriggerC#1"` az útvonal egy helyi indított HTTP függvény használatával hivatkozik `/api/httptriggerC#1`

 
>[!Note]  
>Ha a funkció *függvény, a rendszergazda vagy a sys* hitelesítési szintjeinek meg kell adnia a kód és a clientId, az eredeti függvény URL-cím szerint. Ebben az esetben a hivatkozás alábbihoz hasonlóan fog kinézni: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Hivatkozás a kérelemben szereplő paraméterek

A kérelemben szereplő paraméterek háttér-URL-cím tulajdonsága bemeneti adatokat vagy a kérelem és válasz módosítása részeként használható. Egyes paraméterek köthető az alap proxykonfigurációt megadott útvonal sablonból, és mások számára a bejövő kérelem tulajdonságok származhatnak.

#### <a name="route-template-parameters"></a>Útvonal-Sablonparaméterek
Az útvonal-sablonban használt paraméterek név szerint hivatkozni érhetők el. A paraméterek nevei vannak kapcsos zárójelek között ({}).

Például, ha a proxy például rendelkezik egy útvonalsablonhoz `/pets/{petId}`, a háttér-URL-címet tartalmazhat értékének `{petId}`, mint a `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Ha az útvonalsablonhoz megszakítja a helyettesítő karakter, például a `/api/{*restOfPath}`, az érték `{restOfPath}` a fennmaradó szegmenst a bejövő kérelem egy karakterlánc-ábrázolása.

#### <a name="additional-request-parameters"></a>A további kérelemben szereplő paraméterek
Az útvonal Sablonparaméterek mellett a következő értékek is használható a konfigurációs értékeket:

* **{request.method}** : A HTTP-metódus, amely az eredeti kérés szolgál.
* **{request.headers. \<Fejléc neve\>}**: az eredeti kérelem olvasható fejléc. Cserélje le  *\<fejléc neve\>*  a fejléc az olvasni kívánt nevét. A kérelem nem tartalmazza a fejlécet, ha az érték lesz az üres karakterlánc.
* **{request.querystring. \<ParameterName\>}**: A lekérdezési karakterlánc paraméterként, az eredeti kérelem olvasható. Cserélje le  *\<ParameterName\>*  az olvasni kívánt paraméter nevével. Ha a paraméter nem szerepel-e a kérelem, az érték lesz az üres karakterlánc.

### <a name="response-parameters"></a>Háttér-válasz paraméterek

Válasz paraméterek módosítása az ügyfél válasz részeként használhatók. A következő értékeket a konfigurációs értékek használhatók:

* **{backend.response.statusCode}** : A HTTP-állapotkód, amely akkor adja vissza a háttér-válasz.
* **{backend.response.statusReason}** : A háttér-válasz visszaadott HTTP-indoklás.
* **{backend.response.headers. \<Fejléc neve\>}**: egy fejléc a háttér-válaszban szereplő olvasható. Cserélje le  *\<fejléc neve\>*  olvasni szeretné a fejlécet nevével. A válasz nem tartalmazza a fejlécet, ha az érték lesz az üres karakterlánc.

### <a name="use-appsettings"></a>Referencia-Alkalmazásbeállítások

Is hivatkozhat [alkalmazás beállításait, a függvény alkalmazás definiált](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) által körülvevő a beállítás nevét százalékjelek (%).

Például a háttér-URL-t *https://%ORDER_PROCESSING_HOST%/api/orders* kellene "% ORDER_PROCESSING_HOST %" ORDER_PROCESSING_HOST beállítás értékével.

> [!TIP] 
> Háttér-gazdagépek Alkalmazásbeállítások használni, ha több központi telepítését vagy tesztelési környezetben. Így biztosíthatja, hogy mindig beszélünk a megfelelő háttér-környezet számára.

## <a name="debugProxies"></a>Proxyk hibaelhárítása

A jelző hozzáadásával `"debug":true` bármely proxy a a `proxy.json` teszi lehetővé a hibakeresési naplózást. Naplója `D:\home\LogFiles\Application\Proxies\DetailedTrace` és a speciális eszközök (a kudu) keresztül érhető el. A HTTP-válaszok is tartalmazni fog egy `Proxy-Trace-Location` fejléc egy URL-cím, a fájl elérésére.

A proxy ügyféloldali megoldhassuk hozzáadásával egy `Proxy-Trace-Enabled` fejléc beállítása `true`. Ezzel is nyomkövetés bejelentkezni a fájlrendszer, és térjen vissza a követési URL-cím, egy fejléc a következő a válasz.

### <a name="block-proxy-traces"></a>Proxy-nyomkövetések letiltása

Biztonsági okokból nem érdemes lehet létrehozni a nyomkövetési a szolgáltatás hívása bárki. Nem fogja tudni elérni a bejelentkezési hitelesítő adatok nélkül nyomkövetési tartalma, de a nyomkövetés-erőforrásokat használ fel és függvény proxyk használatát mutatja.

Letilthatja nyomkövetések teljesen `"debug":false` számára bármely adott proxyt a `proxy.json`.

## <a name="advanced-configuration"></a>Speciális konfiguráció

Az Ön által konfigurált proxyk vannak tárolva egy *proxies.json* fájl, a függvény alkalmazás gyökérkönyvtáraként található. Manuálisan szerkessze a fájlt és központi telepítése során az alkalmazás bármelyik használatakor a [telepítési módszerek](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) a Functions támogatja. 

> [!TIP] 
> Ha nem állította be a központi telepítési módszerekkel, is dolgozhat a *proxies.json* fájl a portálon. Nyissa meg a függvény alkalmazáshoz, jelölje be **Platform funkciói**, majd válassza ki **App Service-szerkesztő**. Ezzel a módszerrel a teljes fájl struktúra az függvény alkalmazás megtekintheti és majd a módosításokat.

*Proxies.JSON* határozzák meg a proxyk objektum, amely megnevezett proxyk és a definíciójukat. Nem kötelező, ha a szerkesztő lehetővé teszi, melyeket referenciaként használhat egy [JSON-séma](http://json.schemastore.org/proxies) kód befejezésére. Egy példa fájl nézhet ki például a következőket:

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

Minden egyes proxy van egy rövid nevet, például a *proxy1* az előző példában. A megfelelő proxy-definíciós objektumban van definiálva a következő tulajdonságokkal:

* **matchCondition**: szükséges – az objektum meghatározása, amelynek hatására a proxy végrehajtása a kérelmeket. A megosztott két tulajdonságok tartalmaz [HTTP eseményindítók]:
    * _módszerek_: tömbje, amely a proxy válaszol a HTTP-metódus. Ha nincs megadva, a proxy válaszol-e az összes HTTP-metódus útvonalhoz.
    * _útvonal_: szükséges – meghatározza az útvonalsablonhoz szabályozása, amely kérelem URL-címeket a proxy válaszol-e. Ellentétben a HTTP-eseményindítók, nincs alapértelmezett értéke.
* **backendUri**: az URL-cím, a háttér-erőforrás, amelyhez a kérés küldése a proxyn keresztül kell lennie. Ez az érték az eredeti ügyfélkérés is hivatkozik az alkalmazásbeállítások és paramétereket. Ha ez a tulajdonság nincs megadva, az Azure Functions válaszol egy HTTP 200 OK gombra.
* **requestOverrides**: egy objektum, amely definiálja a háttér-kérelem átalakításokat. Lásd: [megadása egy requestOverrides objektum].
* **responseOverrides**: egy objektum, amely az ügyfél válaszára átalakítások definiálja. Lásd: [megadása egy responseOverrides objektum].

> [!NOTE] 
> A *útvonal* tulajdonság az Azure Functions proxyk nem veszi figyelembe a *routePrefix* a függvény Alkalmazáskonfiguráció állomás tulajdonsága. Ha olyan előtagot, mint például `/api`, azt kell szerepelnie a *útvonal* tulajdonság.

### <a name="disableProxies"></a>Egyes proxyk letiltása

Letilthatja egyes proxyk hozzáadásával `"disabled": true` a proxy a `proxies.json` fájlt. Ennek hatására a kéréseit a matchCondidtion értekezlet 404 vissza.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "www.example.com"
        }
    }
}
```

### <a name="requestOverrides"></a>Adja meg a requestOverrides objektum

A requestOverrides objektum határozza meg a változások a kérelem a háttér-erőforrás neve. Az objektum a következő tulajdonságok határozzák meg:

* **backend.Request.Method**: A HTTP-metódus hívása a háttér-használt.
* **backend.Request.QueryString. \<ParameterName\>**: A lekérdezési karakterlánc paraméterként, amely a háttér-hívásának állítható be. Cserélje le  *\<ParameterName\>*  nevű, a beállítani kívánt paramétert. Az üres karakterlánc valósul meg, ha a paraméter nem szerepel a háttér-kérelemre.
* **backend.Request.Headers. \<Fejléc neve\>**: egy fejléc a háttér-hívásának állítható be. Cserélje le  *\<fejléc neve\>*  be szeretné állítani a fejléc neve. Az üres karakterláncot adjon meg, ha a fejléc nem szerepel a háttér-kérelemre.

Értékek hivatkozhatnak Alkalmazásbeállítások és a paraméterek az eredeti ügyfél kérelemből.

Egy példa konfiguráció látható a következő:

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

### <a name="responseOverrides"></a>Adja meg a responseOverrides objektum

A requestOverrides objektum a válaszhoz, az ügyfél átadott végzett módosításokat határozza meg. Az objektum a következő tulajdonságok határozzák meg:

* **response.statusCode**: vissza kell helyezni az ügyfél a HTTP-állapotkódot.
* **response.statusReason**: A HTTP indoklás vissza kell helyezni az ügyfél.
* **Response.body**: A karakterlánc-ábrázolása, a szervezet számára, hogy az ügyfél számára.
* **Response.Headers. \<Fejléc neve\>**: egy fejlécet tartalmazta, amely az ügyfél válasz állítható be. Cserélje le  *\<fejléc neve\>*  be szeretné állítani a fejléc neve. Az üres karakterláncot adjon meg, ha a fejléc nem szerepel a válasz.

Értékek hivatkozhatnak alkalmazás beállításait, az eredeti ügyfélkérés származó paraméterek és paraméterek a a háttér-válaszban.

Egy példa konfiguráció látható a következő:

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
> Ebben a példában a választörzs értéke közvetlenül, ezért nem `backendUri` tulajdonság van szükség. A példa bemutatja, hogyan használhatja Azure Functions proxyk mocking API-k esetében.

[Azure-portálon]: https://portal.azure.com
[HTTP eseményindítók]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[megadása egy requestOverrides objektum]: #requestOverrides
[megadása egy responseOverrides objektum]: #responseOverrides
[Alkalmazásbeállítások]: #use-appsettings
[változókkal]: #using-variables
[az eredeti ügyfélkérés származó paraméterek]: #request-parameters
[paramétert a háttér-válasz]: #response-parameters
