---
title: Az Azure Functions proxies együttműködve |} A Microsoft Docs
description: Az Azure Functions-proxyk használata áttekintése
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 72e359cf5cfef2072d3511990297f67fc4df92bb
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773056"
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

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *kérelmek felülbírálásának* kibontásával módosíthatók a portálon. 

### <a name="modify-response"></a>A válasz módosítása

Alapértelmezés szerint az ügyfél válaszára inicializálva van a háttér-válasz egy másolatát. A válasz állapotkódja, indoklás, fejlécek és törzs módosításokat végezheti el. A módosított értékek hivatkozhatnak [Alkalmazásbeállítások], [paramétert az eredeti ügyfélkérelemben], és [paraméterek a háttér-válaszból].

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *Válasz felülbírálásának* kibontásával módosíthatók a portálon. 

## <a name="using-variables"></a>Változók használata

A proxy konfigurációját nem kell lehet statikus. Feltétel, hogy az ügyfél eredeti kérést, a háttér-válasz vagy alkalmazásbeállítások változókat használja.

### <a name="reference-localhost"></a>Útmutató helyi funkciók
Használhat `localhost` egy függvényre ugyanazon függvényalkalmazáson belül, közvetlenül egy körbejárási proxy kérés nélkül.

`"backendurl": "https://localhost/api/httptriggerC#1"` a használatával hivatkozik egy helyi HTTP által aktivált függvényt az útvonal: `/api/httptriggerC#1`

 
>[!Note]  
>Ha a függvényt használ *funkciója, a rendszergazda vagy a sys* engedélyezési szintek, szüksége lesz a kód és a clientId, az eredeti függvény URL-Címének megfelelően adja meg. Ebben az esetben a hivatkozás a következőképpen fog kinézni: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`Azt javasoljuk, hogy ezeket a kulcsokat az [Alkalmazásbeállítások] között tárolja, és hivatkozni lehessen rájuk a proxyn. Ezzel elkerülhető a forráskódban tárolt titkos kódok tárolása. 

### <a name="request-parameters"></a>Hivatkozás kérés paraméterei

Használhatja a kérelem paramétereit, a háttér-URL-cím tulajdonsága bemeneteként vagy módosítja a kérelmek és válaszok részeként. Néhány paraméter van megadva a kiindulási proxykonfigurációt útvonal sablon is köthetők, és mások származhatnak a bejövő kérelem tulajdonságai.

#### <a name="route-template-parameters"></a>Útválasztási sablon paraméterei
Az útvonal-sablonban használt paraméterek név szerint lehet hivatkozni a érhetők el. A paraméternév kapcsos zárójelek között van ({}).

Például, ha a proxy tartozik egy útvonal-sablont, például `/pets/{petId}`, a háttér-URL-cím tartalmazhat értékét `{petId}`, mint a `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Ha az útvonalsablonhoz megszakítja a helyettesítő karakter, például `/api/{*restOfPath}`, az érték `{restOfPath}` a bejövő kérelem elérési útja a fennmaradó szegmensek karakterláncként van.

#### <a name="additional-request-parameters"></a>További kérés paraméterei
Az útvonal Sablonparaméterek mellett a következő értékeket is használható konfigurációs értékek:

* **{Request. Method}** : Az eredeti kérelemben használt HTTP-metódus.
* **{Request.\< headers. HeaderName\>}** : Az eredeti kérelemből beolvasható fejléc. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevét. Ha a kérelem nem tartalmazza a fejléc, az érték nem üres karakterlánc.
* **{Request. querystring\< . ParameterName\>}** : Egy lekérdezési karakterlánc paraméter, amely az eredeti kérelemből olvasható. Cserélje le *\<ParameterName\>* az olvasni kívánt paraméter nevére. A kérelem nem tartalmazza a paramétert, ha az érték lesz az üres karakterlánc.

### <a name="response-parameters"></a>Háttér-válasz paraméterek

Válasz paraméterek módosítása a válasz az ügyfélhez részeként is használható. A következő értékeket a konfigurációs értékek használhatók:

* **{backend.response.statusCode}** : A háttér-válaszon visszaadott HTTP-állapotkód.
* **{backend.response.statusReason}** : A háttér-válaszon visszaadott HTTP-ok kifejezése.
* **{backend. Response. headers\< . HeaderName\>}** : A háttér-válaszból beolvasható fejléc. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevét. Ha a válasz nem tartalmazza a fejléc, az érték nem üres karakterlánc.

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

* **matchCondition**: Kötelező – a proxy végrehajtását kiváltó kérelmeket meghatározó objektum. Megosztott két tulajdonságot tartalmaz [HTTP-eseményindítók]:
    * _metódusok_: A proxy által válaszoló HTTP-metódusok tömbje. Ha nincs megadva, a proxy válaszol az összes HTTP-metódusok a útvonalon.
    * _útvonal_: Kötelező – meghatározza az útválasztási sablont, amely szabályozza, hogy a proxy milyen URL-címeket válaszol. Ellentétben a HTTP-eseményindítók esetén nincs alapértelmezett érték.
* **backendUri**: Annak a háttér-erőforrásnak az URL-címe, amelyre a kérést proxynak kell lennie. Ez az érték hivatkozhat alkalmazás beállítás- és az eredeti ügyfél kérelemből. Ha ez a tulajdonság nem található, az Azure Functions válaszol egy HTTP 200 OK.
* **requestOverrides**: Egy objektum, amely meghatározza a háttér-kérelem átalakítását. Lásd: [egy requestOverrides objektum meghatározása].
* **responseOverrides**: Egy objektum, amely meghatározza az ügyfél válaszának átalakítását. Lásd: [egy responseOverrides objektum meghatározása].

> [!NOTE] 
> A *útvonal* tulajdonság frissítése az Azure Functions-proxyk nem fogadja el a *routePrefix* a Függvényalkalmazás állomáskonfiguráció tulajdonságát. Ha például tartalmazzon egy előtagot `/api`, bele kell foglalni a *útvonal* tulajdonság.

### <a name="disableProxies"></a> Az egyes proxyk letiltása

Letilthatja egyes proxyk hozzáadásával `"disabled": true` , a proxy a `proxies.json` fájlt. Ennek hatására a matchCondition a 404-as visszaküldésére irányuló kérések lesznek.
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

A proxyk a \ Escape szimbólum használatával beolvassák az összes karakterláncot a JSON-fájlból. A proxyk a kapcsos zárójeleket is értelmezik. Tekintse meg az alábbi példák teljes listáját.

|Karakter|Escape-karaktert|Példa|
|-|-|-|
|{vagy}|{{és}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Egy requestOverrides objektum meghatározása

A requestOverrides objektuma határozza meg, amikor a háttér-erőforrás neve a kérés végzett módosítások. Az objektum az alábbi tulajdonságokat határozzák meg:

* **háttér. Request. Method**: A háttér meghívásához használt HTTP-metódus.
* **háttér. Request. querystring. ParameterName:\> \<** Egy lekérdezési karakterlánc paraméter, amely a háttér felé irányuló híváshoz beállítható. Cserélje le *\<ParameterName\>* , amely beállítja a paraméter nevével. Ha az üres karakterlánc van megadva, a paraméter nem szerepel a háttér-kérés.
* **háttér. Request. headers. HeaderName:\> \<** Egy fejléc, amely beállítható a háttér felé irányuló híváshoz. Cserélje le *\<HeaderName\>* nevét a fejlécet, amely a következőt kívánja beállítani. Az üres karakterláncot adjon meg, ha a háttér-kérelem nem tartalmazza a fejlécet.

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

* **response.statusCode**: Az ügyfélnek visszaadott HTTP-állapotkód.
* **response.statusReason**: Az ügyfélnek visszaadott HTTP-ok kifejezése.
* **Response. Body**: Az ügyfélnek visszaadni kívánt törzs karakterlánc-ábrázolása.
* **Response. headers. HeaderName:\> \<** Egy fejléc, amely beállítható az ügyfélre adott válaszra. Cserélje le *\<HeaderName\>* nevét a fejlécet, amely a következőt kívánja beállítani. Az üres karakterláncot adjon meg, ha a fejléc nem szerepel a válasz.

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
