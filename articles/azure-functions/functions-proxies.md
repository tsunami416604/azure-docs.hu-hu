---
title: Az Azure Functions proxies együttműködve |} A Microsoft Docs
description: Az Azure Functions-proxyk használata áttekintése
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 2fbf29385b9a14cf5d4a9df621f0767a32079587
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094575"
---
# <a name="work-with-azure-functions-proxies"></a>Az Azure Functions-proxyk használata

Ez a cikk bemutatja, hogyan konfigurálhat és az Azure Functions-proxyk használata. Ezzel a funkcióval a függvényalkalmazás, egy másik erőforrás által végrehajtott végpontokat adhat meg. Ilyen proxyk segítségével egy nagy méretű API felosztása több függvényalkalmazás (ahogy a mikroszolgáltatási architektúra) esetében, miközben továbbra is egyetlen API-felületet ügyfelek számára.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standard funkciók számlázási proxy végrehajtások vonatkozik. További információkért lásd: [Azure Functions árképzése](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Hozzon létre egy proxyt

Ez a szakasz bemutatja, hogyan hozhat létre egy proxyt a Functions portálján.

1. Nyissa meg a [Azure Portal], és folytassa a függvényalkalmazáshoz.
2. A bal oldali panelen válassza ki a **új proxy**.
3. Adja meg a proxykiszolgáló nevét.
4. Konfigurálja a végpontot, amely ki van téve a függvényalkalmazás megadásával a **útvonalsablonhoz** és **HTTP-metódusok**. Ezeket a paramétereket a szabályainak megfelelően viselkednek [HTTP-eseményindítók].
5. Állítsa be a **háttérkiszolgáló URL-cím** egy másik végpontra. Ezt a végpontot egy másik függvényalkalmazás egy függvényt, vagy lehet, hogy bármely más API-t. Az érték nem lehet statikus kell, és azt is lehet hivatkozni [Alkalmazásbeállítások] és [paramétert az eredeti ügyfélkérelemben].
6. Kattintson a **Create** (Létrehozás) gombra.

A proxy már létezik a függvényalkalmazás az új végpont. Az ügyfél szempontjából legyen az Azure Functions-HttpTrigger egyenértékű. Az új proxykiszolgáló kipróbálhatja a proxykiszolgáló URL-cím másolása és tesztelésére is a kedvenc HTTP-ügyféllel.

## <a name="modify-requests-responses"></a>Módosítsa a kérelmek és válaszok

Az Azure Functions-proxyk módosíthatja a kérelmek és válaszok a háttérrendszerből. Ezekkel az adatátalakításokkal változókat is használhat, ahogyan az az [Változók használata].

### <a name="modify-backend-request"></a>Módosítsa a háttér-kérelem

Alapértelmezés szerint a háttér-kérelem egy másolatát az eredeti kérelem inicializálása. Amellett, hogy a háttér-URL-cím beállítása, a HTTP módszert, fejlécek és lekérdezési karakterlánc paraméterei módosításokat végezheti el. A módosított értékek hivatkozhatnak [Alkalmazásbeállítások] és [paramétert az eredeti ügyfélkérelemben].

Háttér-kérelmek által bővítése módosítható a portálon a *kérés felülbírálása* proxy részletek lapon szakaszában. 

### <a name="modify-response"></a>A válasz módosítása

Alapértelmezés szerint az ügyfél válaszára inicializálva van a háttér-válasz egy másolatát. A válasz állapotkódja, indoklás, fejlécek és törzs módosításokat végezheti el. A módosított értékek hivatkozhatnak [Alkalmazásbeállítások], [paramétert az eredeti ügyfélkérelemben], és [paraméterek a háttér-válaszból].

Háttér-kérelmek által bővítése módosítható a portálon a *válasz felülbírálása* proxy részletek lapon szakaszában. 

## <a name="using-variables"></a>Változók használata

A proxy konfigurációját nem kell lehet statikus. Feltétel, hogy az ügyfél eredeti kérést, a háttér-válasz vagy alkalmazásbeállítások változókat használja.

### <a name="reference-localhost"></a>Útmutató helyi funkciók
Használhat `localhost` egy függvényre ugyanazon függvényalkalmazáson belül, közvetlenül egy körbejárási proxy kérés nélkül.

`"backendurl": "https://localhost/api/httptriggerC#1"` a használatával hivatkozik egy helyi HTTP által aktivált függvényt az útvonal: `/api/httptriggerC#1`

 
>[!Note]  
>Ha a függvényt használ *funkciója, a rendszergazda vagy a sys* engedélyezési szintek, szüksége lesz a kód és a clientId, az eredeti függvény URL-Címének megfelelően adja meg. Ebben az esetben a hivatkozás módon jelenik meg: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`

### <a name="request-parameters"></a>Hivatkozás kérés paraméterei

Használhatja a kérelem paramétereit, a háttér-URL-cím tulajdonsága bemeneteként vagy módosítja a kérelmek és válaszok részeként. Néhány paraméter van megadva a kiindulási proxykonfigurációt útvonal sablon is köthetők, és mások származhatnak a bejövő kérelem tulajdonságai.

#### <a name="route-template-parameters"></a>Útválasztási sablon paraméterei
Az útvonal-sablonban használt paraméterek név szerint lehet hivatkozni a érhetők el. A paraméternév kapcsos zárójelek között van ({}).

Például, ha a proxy tartozik egy útvonal-sablont, például `/pets/{petId}`, a háttér-URL-cím tartalmazhat értékét `{petId}`, mint a `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Ha az útvonalsablonhoz megszakítja a helyettesítő karakter, például `/api/{*restOfPath}`, az érték `{restOfPath}` a bejövő kérelem elérési útja a fennmaradó szegmensek karakterláncként van.

#### <a name="additional-request-parameters"></a>További kérés paraméterei
Az útvonal Sablonparaméterek mellett a következő értékeket is használható konfigurációs értékek:

* **{request.method}** : A HTTP-metódus, amely az eredeti kérésére szolgál.
* **{request.headers. \<HeaderName\>}**: A fejlécet, amely az eredeti kérést is olvasható. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevét. Ha a kérelem nem tartalmazza a fejléc, az érték nem üres karakterlánc.
* **{request.querystring. \<ParameterName\>}**: Lekérdezési sztring paramétereként, amely az eredeti kérést is olvasható. Cserélje le *\<ParameterName\>* az olvasni kívánt paraméter nevére. A kérelem nem tartalmazza a paramétert, ha az érték lesz az üres karakterlánc.

### <a name="response-parameters"></a>Háttér-válasz paraméterek

Válasz paraméterek módosítása a válasz az ügyfélhez részeként is használható. A következő értékeket a konfigurációs értékek használhatók:

* **{backend.response.statusCode}**: A háttér-válasz visszaadott HTTP-állapotkódot.
* **{backend.response.statusReason}**: A háttér-válasz visszaadott HTTP indoklás.
* **{backend.response.headers. \<HeaderName\>}**: A fejlécet, amely a háttér-válaszból olvasható. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevét. Ha a válasz nem tartalmazza a fejléc, az érték nem üres karakterlánc.

### <a name="use-appsettings"></a>Referencia-Alkalmazásbeállítások

Is hivatkozhat [a függvényalkalmazás definiált Alkalmazásbeállítások](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) téve a beállítás nevét százalékjelek (%).

Ha például egy háttér-URL-címe *https://%ORDER_PROCESSING_HOST%/api/orders* "% ORDER_PROCESSING_HOST %" ORDER_PROCESSING_HOST beállítás értéke lenne.

> [!TIP] 
> Alkalmazásbeállítások használja a háttér-gazdagépek több központi telepítést, ha vagy tesztelési környezetben. Így biztosíthatja, hogy mindig beszélünk a megfelelő háttér-környezetnek.

## <a name="debugProxies"></a>Proxyk hibaelhárítása

A jelző hozzáadásával `"debug":true` bármely a proxy a `proxies.json` hibakeresési naplózás lehetővé teszi. Naplók találhatók `D:\home\LogFiles\Application\Proxies\DetailedTrace` érhető el a speciális eszközök (kudu) keresztül. Bármilyen HTTP-válaszok is tartalmazni fog egy `Proxy-Trace-Location` fejléc eléréséhez a naplófájl URL-címet.

Az ügyféloldalról proxy hozzáadásával is hibakeresést egy `Proxy-Trace-Enabled` fejléc beállítása `true`. Ezzel is egy nyomkövetési naplózása a fájlrendszerhez, és a nyomkövetési URL-cím egy fejléccel a válaszban visszaadandó.

### <a name="block-proxy-traces"></a>Proxy-nyomkövetések letiltása

Biztonsági okokból előfordulhat, hogy nem szeretné, hogy a szolgáltatás hozza létre a nyomkövetési hívó minden. Nem lesz a bejelentkezési hitelesítő adatok nélkül nyomkövetési tartalmához való hozzáféréshez, de a nyomkövetés erőforrásokat használ fel, és elérhetővé teszi, hogy használ-e az Függvényproxykat.

Hozzáadásával teljesen tiltsa le a nyomkövetések `"debug":false` bármely adott proxyval való a `proxies.json`.

## <a name="advanced-configuration"></a>Speciális konfiguráció

Az Ön által konfigurált proxyk vannak tárolva egy *proxies.json* fájlt, amely a függvény alkalmazás könyvtár gyökerében található. Manuálisan szerkesztheti ezt a fájlt, és üzembe helyezése során az alkalmazás bármelyik használatakor a [telepítési módszerek](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) a Functions támogatja. 

> [!TIP] 
> Ha nem állított be az üzembe helyezési módszerekkel, akkor is együttműködik a *proxies.json* fájlt a portálon. Lépjen a függvényalkalmazásban, jelölje be **platformfunkciók**, majd válassza ki **az App Service Editor**. Ezzel a módszerrel a függvényalkalmazás teljes-fájl szerkezete megtekintheti és majd a módosításokat.

*Proxies.JSON* határozza meg a proxyk objektum, amely a nevesített proxyk és a definíciójukat áll. Ha szükséges, ha a szerkesztő támogatja, hivatkozhat egy [JSON-sémájában](http://json.schemastore.org/proxies) kód befejezésére. Egy példa fájlt a következőhöz hasonlóan nézhet ki:

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

Minden egyes proxy tartozik egy rövid nevet, például *proxy1* az előző példában. A megfelelő proxykiszolgáló-definíciós objektummal határozzák meg a következő tulajdonságokkal:

* **matchCondition**: Kötelező – meghatározása a kéréseket, amelyek a proxy végrehajtásának aktiválása egy objektumot. Megosztott két tulajdonságot tartalmaz [HTTP-eseményindítók]:
    * _metódusok_: A proxy válaszol a HTTP-metódusok tömbje. Ha nincs megadva, a proxy válaszol az összes HTTP-metódusok a útvonalon.
    * _útvonal_: Kötelező – meghatározza az útvonalsablonhoz, szabályozásával, amely kérelem URL-címek a proxy válaszol. Ellentétben a HTTP-eseményindítók esetén nincs alapértelmezett érték.
* **backendUri**: Az URL-címe a háttér-erőforrás, amelyhez a kérés küldése a proxyn keresztül kell lennie. Ez az érték hivatkozhat alkalmazás beállítás- és az eredeti ügyfél kérelemből. Ha ez a tulajdonság nem található, az Azure Functions válaszol egy HTTP 200 OK.
* **requestOverrides**: Egy objektum, amely meghatározza az átalakítások a háttér-kérelemre. Lásd: [egy requestOverrides objektum meghatározása].
* **responseOverrides**: Az átalakítások meghatározása az ügyfél válaszára való objektum. Lásd: [egy responseOverrides objektum meghatározása].

> [!NOTE] 
> A *útvonal* tulajdonság frissítése az Azure Functions-proxyk nem fogadja el a *routePrefix* a Függvényalkalmazás állomáskonfiguráció tulajdonságát. Ha például tartalmazzon egy előtagot `/api`, bele kell foglalni a *útvonal* tulajdonság.

### <a name="disableProxies"></a> Az egyes proxyk letiltása

Letilthatja egyes proxyk hozzáadásával `"disabled": true` , a proxy a `proxies.json` fájlt. Ez azt eredményezi, bármilyen kérelmeket felel meg a matchCondition visszaadandó 404-es.
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

### <a name="applicationSettings"></a> Nastavení aplikace

A proxy viselkedését több alkalmazás beállításai vezérlik. Azok az összes leírt a [funkciók App beállításainak ismertetése](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a> (Karakterlánc formázása) fenntartott karaktereket

Proxyk, olvassa el minden kívül egy JSON-karakterlánc fájl használatával \, egy escape szimbólumot. Proxyk is tudja értelmezni a kapcsos zárójelek közé. Tekintse meg az alábbi példák teljes körű.

|Karakter|Escape-karaktert|Példa|
|-|-|-|
|{vagy}|{{és}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Egy requestOverrides objektum meghatározása

A requestOverrides objektuma határozza meg, amikor a háttér-erőforrás neve a kérés végzett módosítások. Az objektum az alábbi tulajdonságokat határozzák meg:

* **backend.Request.Method**: A háttéralkalmazás meghívásához használt HTTP-metódus.
* **backend.request.querystring.\<ParameterName\>**: Lekérdezési sztring paramétereként, amely állíthat be a háttéralkalmazás meghívásához. Cserélje le *\<ParameterName\>* , amely beállítja a paraméter nevével. Ha az üres karakterlánc van megadva, a paraméter nem szerepel a háttér-kérés.
* **backend.Request.Headers. \<HeaderName\>**: A fejlécet, amely állíthat be a háttéralkalmazás meghívásához. Cserélje le *\<HeaderName\>* nevét a fejlécet, amely a következőt kívánja beállítani. Az üres karakterláncot adjon meg, ha a háttér-kérelem nem tartalmazza a fejlécet.

Értékek hivatkozhatnak Alkalmazásbeállítások és a paraméterek az eredeti ügyfél kérelemből.

Konfiguráció például előfordulhat, hogy a következőhöz hasonló:

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

### <a name="responseOverrides"></a>Egy responseOverrides objektum meghatározása

A requestOverrides objektuma határozza meg, hogy a válasz az ügyfélhez átadott végrehajtott módosításokat. Az objektum az alábbi tulajdonságokat határozzák meg:

* **response.statusCode**: A HTTP-állapotkódot kell visszaadni az ügyfélnek.
* **response.statusReason**: A HTTP indoklás, az ügyfél számára.
* **Response.body**: Az ügyfél számára, hogy a szervezet karakteres formáját.
* **Response.Headers. \<HeaderName\>**: A fejlécet, amely beállítható a válasz az ügyfélhez. Cserélje le *\<HeaderName\>* nevét a fejlécet, amely a következőt kívánja beállítani. Az üres karakterláncot adjon meg, ha a fejléc nem szerepel a válasz.

Értékek Alkalmazásbeállítások, az ügyfél eredeti kérés paraméterei és paramétereket is lehet hivatkozni a háttér-válaszból.

Konfiguráció például előfordulhat, hogy a következőhöz hasonló:

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
> Ebben a példában a válasz törzse értéke közvetlenül, ezért nem `backendUri` tulajdonság van szükség. A példa bemutatja, hogyan használhatja az Azure Functions-proxyk utánzási API-k esetében.

[Azure Portal]: https://portal.azure.com
[HTTP-eseményindítók]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Egy requestOverrides objektum meghatározása]: #requestOverrides
[Egy responseOverrides objektum meghatározása]: #responseOverrides
[Alkalmazásbeállítások]: #use-appsettings
[Változók használata]: #using-variables
[paramétert az eredeti ügyfélkérelemben]: #request-parameters
[paraméterek a háttér-válaszból]: #response-parameters
