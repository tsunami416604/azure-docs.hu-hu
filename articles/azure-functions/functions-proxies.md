---
title: Proxyk használata Azure Functions
description: Az Azure Functions-proxyk használata áttekintése
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: dffdffdfa80d940c4a50d0a6630c665164f24d5c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230457"
---
# <a name="work-with-azure-functions-proxies"></a>Az Azure Functions-proxyk használata

Ez a cikk bemutatja, hogyan konfigurálhat és az Azure Functions-proxyk használata. Ezzel a funkcióval a függvényalkalmazás, egy másik erőforrás által végrehajtott végpontokat adhat meg. Ilyen proxyk segítségével egy nagy méretű API felosztása több függvényalkalmazás (ahogy a mikroszolgáltatási architektúra) esetében, miközben továbbra is egyetlen API-felületet ügyfelek számára.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standard funkciók számlázási proxy végrehajtások vonatkozik. További információ: [Azure functions díjszabása](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Proxy létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre egy proxyt a Functions portálján.

1. Nyissa meg a [Azure Portal], majd nyissa meg a Function alkalmazást.
2. A bal oldali panelen válassza az **új proxy**lehetőséget.
3. Adja meg a proxykiszolgáló nevét.
4. Konfigurálja a függvény alkalmazásban elérhető végpontot az **útválasztási sablon** és a **http-metódusok**megadásával. Ezek a paraméterek a [http-eseményindítók]szabályainak megfelelően viselkednek.
5. Állítsa be a **háttérbeli URL-címet** egy másik végpontra. Ezt a végpontot egy másik függvényalkalmazás egy függvényt, vagy lehet, hogy bármely más API-t. Az értéknek nem kell statikusnak lennie, és az [Alkalmazásbeállítások] és [paramétereit is hivatkozhat az eredeti ügyfél-kérelemből].
6. Kattintson a **Létrehozás** gombra.

A proxy már létezik a függvényalkalmazás az új végpont. Az ügyfél szempontjából legyen az Azure Functions-HttpTrigger egyenértékű. Az új proxykiszolgáló kipróbálhatja a proxykiszolgáló URL-cím másolása és tesztelésére is a kedvenc HTTP-ügyféllel.

## <a name="modify-requests-responses"></a>Kérelmek és válaszok módosítása

Az Azure Functions-proxyk módosíthatja a kérelmek és válaszok a háttérrendszerből. Ezek az átalakítások használhatnak változókat a [Változók használata]definiált módon.

### <a name="modify-backend-request"></a>A háttérbeli kérelem módosítása

Alapértelmezés szerint a háttér-kérelem egy másolatát az eredeti kérelem inicializálása. Amellett, hogy a háttér-URL-cím beállítása, a HTTP módszert, fejlécek és lekérdezési karakterlánc paraméterei módosításokat végezheti el. A módosított értékek hivatkozhatnak az [Alkalmazásbeállítások] és [paramétereit is hivatkozhat az eredeti ügyfél-kérelemből].

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *kérelmek felülbírálásának* kibontásával módosíthatók a portálon. 

### <a name="modify-response"></a>Válasz módosítása

Alapértelmezés szerint az ügyfél válaszára inicializálva van a háttér-válasz egy másolatát. A válasz állapotkódja, indoklás, fejlécek és törzs módosításokat végezheti el. A módosított értékek hivatkozhatnak az [Alkalmazásbeállítások], [paramétereit is hivatkozhat az eredeti ügyfél-kérelemből], valamint [a háttérbeli válasz paramétereit].

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *Válasz felülbírálásának* kibontásával módosíthatók a portálon. 

## <a name="using-variables"></a>Változók használata

A proxy konfigurációját nem kell lehet statikus. Feltétel, hogy az ügyfél eredeti kérést, a háttér-válasz vagy alkalmazásbeállítások változókat használja.

### <a name="reference-localhost"></a>Hivatkozás helyi függvények
A `localhost` használatával közvetlenül is hivatkozhat egy függvényre az egyazon függvény alkalmazásban, kétirányú proxy kérés nélkül.

`"backendurl": "https://localhost/api/httptriggerC#1"` az útvonalon helyi HTTP által aktivált függvényre hivatkozik `/api/httptriggerC#1`

 
>[!Note]  
>Ha a függvény *függvény-, rendszergazdai vagy sys* -engedélyezési szinteket használ, meg kell adnia a kódot és a clientId az eredeti függvény URL-címének megfelelően. Ebben az esetben a hivatkozás a következőhöz hasonló lesz: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` javasoljuk, hogy ezeket a kulcsokat az [Alkalmazásbeállítások] között tárolja, és hivatkozni lehessen rájuk a proxyn. Ezzel elkerülhető a forráskódban tárolt titkos kódok tárolása. 

### <a name="request-parameters"></a>Hivatkozási kérelem paraméterei

Használhatja a kérelem paramétereit, a háttér-URL-cím tulajdonsága bemeneteként vagy módosítja a kérelmek és válaszok részeként. Néhány paraméter van megadva a kiindulási proxykonfigurációt útvonal sablon is köthetők, és mások származhatnak a bejövő kérelem tulajdonságai.

#### <a name="route-template-parameters"></a>Útválasztási sablon paraméterei
Az útvonal-sablonban használt paraméterek név szerint lehet hivatkozni a érhetők el. A paraméterek neve kapcsos zárójelbe ({}) van csatolva.

Ha például egy proxy útválasztási sablonnal (például `/pets/{petId}`) rendelkezik, a háttér URL-cím a `{petId}`értékét is tartalmazza, ahogy az `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Ha az útválasztási sablon egy helyettesítő karakterrel végződik (például `/api/{*restOfPath}`), akkor az érték `{restOfPath}` a bejövő kérelemben szereplő hátralévő elérésiút-szegmens karakterlánc-ábrázolása.

#### <a name="additional-request-parameters"></a>További kérés paraméterei
Az útvonal Sablonparaméterek mellett a következő értékeket is használható konfigurációs értékek:

* **{Request. Method}** : az eredeti kérelemben használt http-metódus.
* **{Request. headers.\<HeaderName\>}** : az eredeti kérelemből olvasható fejléc. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevére. Ha a kérelem nem tartalmazza a fejléc, az érték nem üres karakterlánc.
* **{Request. querystring.\<ParameterName\>}** : egy lekérdezési karakterlánc paraméter, amely az eredeti kérelemből olvasható. Cserélje le *\<ParameterName\>* az olvasni kívánt paraméter nevére. A kérelem nem tartalmazza a paramétert, ha az érték lesz az üres karakterlánc.

### <a name="response-parameters"></a>Hivatkozás háttér-válasz paraméterei

Válasz paraméterek módosítása a válasz az ügyfélhez részeként is használható. A következő értékeket a konfigurációs értékek használhatók:

* **{backend. Response. statusCode}** : a háttér-válaszon VISSZAadott http-állapotkód.
* **{backend. Response. statusReason}** : a háttér-válaszon VISSZAadott http-ok kifejezése.
* **{backend. Response. headers.\<HeaderName\>}** : a háttér-válaszból olvasható fejléc. Cserélje le *\<HeaderName\>* az olvasni kívánt fejléc nevére. Ha a válasz nem tartalmazza a fejléc, az érték nem üres karakterlánc.

### <a name="use-appsettings"></a>Hivatkozási alkalmazás beállításai

A Function alkalmazáshoz [definiált Alkalmazásbeállítások](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) is hivatkozhatók, ha a beállítási nevet a százalék jelek (%) értékre helyezi.

Az *https://%ORDER_PROCESSING_HOST%/api/orders* háttérbeli URL-címe például "% ORDER_PROCESSING_HOST%" lesz, a ORDER_PROCESSING_HOST beállítás értéke helyett.

> [!TIP] 
> Alkalmazásbeállítások használja a háttér-gazdagépek több központi telepítést, ha vagy tesztelési környezetben. Így biztosíthatja, hogy mindig beszélünk a megfelelő háttér-környezetnek.

## <a name="debugProxies"></a>Proxyk hibáinak megoldása

Ha a jelölőt `"debug":true` bármely proxyhoz hozzáadja a `proxies.json`, akkor a hibakeresési naplózást is engedélyeznie kell. A naplók tárolása `D:\home\LogFiles\Application\Proxies\DetailedTrace` történik, és a speciális eszközökön (kudu) keresztül érhető el. A HTTP-válaszok egy `Proxy-Trace-Location` fejlécet is tartalmaznak, amely egy URL-címet tartalmaz a naplófájl eléréséhez.

A proxykat az ügyfél oldaláról lehet felvenni, ha hozzáad egy `Proxy-Trace-Enabled` fejlécet `true`hoz. Ezzel is egy nyomkövetési naplózása a fájlrendszerhez, és a nyomkövetési URL-cím egy fejléccel a válaszban visszaadandó.

### <a name="block-proxy-traces"></a>Proxy-nyomkövetések letiltása

Biztonsági okokból előfordulhat, hogy nem szeretné, hogy a szolgáltatás hozza létre a nyomkövetési hívó minden. Nem lesz a bejelentkezési hitelesítő adatok nélkül nyomkövetési tartalmához való hozzáféréshez, de a nyomkövetés erőforrásokat használ fel, és elérhetővé teszi, hogy használ-e az Függvényproxykat.

A nyomkövetést teljes egészében letilthatja, ha `"debug":false`t ad hozzá a `proxies.json`egy adott proxyhoz.

## <a name="advanced-configuration"></a>Speciális konfiguráció

A konfigurált proxyk a Function app-címtár gyökerében található *proxys. JSON* fájlban tárolódnak. Ezt a fájlt manuálisan szerkesztheti, és az alkalmazás részeként telepítheti, ha a függvények által támogatott [központi telepítési módszereket](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) használja. 

> [!TIP] 
> Ha nem állított be egyik telepítési módszert sem, akkor a portálon a *proxys. JSON* fájllal is dolgozhat. Nyissa meg a Function alkalmazást, válassza a **platform szolgáltatások**elemet, majd válassza a **app Service Editor**lehetőséget. Ezzel a módszerrel a függvényalkalmazás teljes-fájl szerkezete megtekintheti és majd a módosításokat.

A *proxys. JSON* -t egy proxy objektum határozza meg, amely névvel ellátott proxykat és azok definícióit alkotja. Ha a szerkesztő támogatja azt, akkor egy [JSON-sémára](http://json.schemastore.org/proxies) hivatkozhat a kód befejezéséhez. Egy példa fájlt a következőhöz hasonlóan nézhet ki:

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

Minden proxynak van egy felhasználóbarát neve, például a *proxy1* az előző példában. A megfelelő proxykiszolgáló-definíciós objektummal határozzák meg a következő tulajdonságokkal:

* **matchCondition**: kötelező – a proxy végrehajtását kiváltó kérelmeket meghatározó objektum. Két olyan tulajdonságot tartalmaz, amelyek a [HTTP-eseményindítók]vannak megosztva:
    * _metódusok_: a proxy által válaszoló http-metódusok tömbje. Ha nincs megadva, a proxy válaszol az összes HTTP-metódusok a útvonalon.
    * _Route_: kötelező – meghatározza az útválasztási sablont, amely szabályozza, hogy a proxy mely URL-címekre válaszol. Ellentétben a HTTP-eseményindítók esetén nincs alapértelmezett érték.
* **backendUri**: annak a háttér-erőforrásnak az URL-címe, amelyre a kérést proxynak kell lennie. Ez az érték hivatkozhat alkalmazás beállítás- és az eredeti ügyfél kérelemből. Ha ez a tulajdonság nem található, az Azure Functions válaszol egy HTTP 200 OK.
* **requestOverrides**: olyan objektum, amely meghatározza a háttér-kérelem átalakításait. Lásd: [requestOverrides objektum definiálása].
* **responseOverrides**: olyan objektum, amely meghatározza az ügyfél válaszának átalakításait. Lásd: [responseOverrides objektum definiálása].

> [!NOTE] 
> A Azure Functions-proxyk *Route* tulajdonsága nem tartja tiszteletben a függvényalkalmazás gazdagép konfigurációjának *routePrefix* tulajdonságát. Ha olyan előtagot szeretne megadni, mint például a `/api`, azt az *Route* tulajdonságba kell foglalni.

### <a name="disableProxies"></a>Egyéni proxyk letiltása

Az egyes proxykat letilthatja `"disabled": true` hozzáadásával a proxyhoz a `proxies.json` fájlban. Ennek hatására a matchCondition a 404-as visszaküldésére irányuló kérések lesznek.
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

### <a name="applicationSettings"></a>Alkalmazásbeállítások

A proxy viselkedését több alkalmazás beállításai vezérlik. Ezek mind a [functions app Settings dokumentációjában](./functions-app-settings.md) szerepelnek.

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a>Fenntartott karakterek (karakterlánc formázása)

A proxyk a \ Escape szimbólum használatával beolvassák az összes karakterláncot a JSON-fájlból. A proxyk a kapcsos zárójeleket is értelmezik. Tekintse meg az alábbi példák teljes listáját.

|Karakter|Escape-karaktert|Példa|
|-|-|-|
|{vagy}|{{és}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>RequestOverrides objektum definiálása

A requestOverrides objektuma határozza meg, amikor a háttér-erőforrás neve a kérés végzett módosítások. Az objektum az alábbi tulajdonságokat határozzák meg:

* **háttér. Request. Method**: a háttér hívásához használt http-metódus.
* **backend. Request. querystring.\<ParameterName\>** : egy lekérdezési karakterlánc paraméter, amely beállítható a háttér felé irányuló híváshoz. Cserélje le *\<ParameterName\>* a beállítani kívánt paraméter nevére. Ha az üres karakterlánc van megadva, a paraméter nem szerepel a háttér-kérés.
* **backend. Request. headers.\<HeaderName\>** : egy fejléc, amely beállítható a háttér felé irányuló híváshoz. Cserélje le *\<HeaderName\>* a beállítani kívánt fejléc nevére. Az üres karakterláncot adjon meg, ha a háttér-kérelem nem tartalmazza a fejlécet.

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

### <a name="responseOverrides"></a>ResponseOverrides objektum definiálása

A requestOverrides objektuma határozza meg, hogy a válasz az ügyfélhez átadott végrehajtott módosításokat. Az objektum az alábbi tulajdonságokat határozzák meg:

* **Response. statusCode**: az ügyfélnek visszaadott http-állapotkód.
* **Response. statusReason**: az ügyfélnek visszaadott http-ok kifejezése.
* **Response. Body**: az ügyfélnek visszaadott törzs karakterlánc-ábrázolása.
* **Response. headers.\<HeaderName\>** : egy fejléc, amely beállítható az ügyfélre adott válaszra. Cserélje le *\<HeaderName\>* a beállítani kívánt fejléc nevére. Az üres karakterláncot adjon meg, ha a fejléc nem szerepel a válasz.

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
> Ebben a példában a válasz törzse közvetlenül van beállítva, így nincs szükség `backendUri` tulajdonságra. A példa bemutatja, hogyan használhatja az Azure Functions-proxyk utánzási API-k esetében.

[Azure Portal]: https://portal.azure.com
[HTTP-eseményindítók]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[RequestOverrides objektum definiálása]: #requestOverrides
[ResponseOverrides objektum definiálása]: #responseOverrides
[Alkalmazásbeállítások]: #use-appsettings
[Változók használata]: #using-variables
[paramétereit is hivatkozhat az eredeti ügyfél-kérelemből]: #request-parameters
[a háttérbeli válasz paramétereit]: #response-parameters
