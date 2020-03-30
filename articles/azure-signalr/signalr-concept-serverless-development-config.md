---
title: Az Azure Functions alkalmazás fejlesztése & konfigurálása – Azure SignalR
description: Részletek a kiszolgáló nélküli valós idejű alkalmazások fejlesztéséről és konfigurálásáról az Azure Functions és az Azure SignalR Szolgáltatás használatával
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523170"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással

Az Azure Functions-alkalmazások kihasználhatják az [Azure SignalR-szolgáltatás kötéseit,](../azure-functions/functions-bindings-signalr-service.md) hogy valós idejű képességeket adjanak hozzá. Az ügyfélalkalmazások több nyelven elérhető ügyfélSDK-k használatával csatlakoznak az Azure SignalR Szolgáltatáshoz, és valós idejű üzeneteket kapnak.

Ez a cikk ismerteti a SignalR-szolgáltatással integrált Azure Függvényalkalmazás fejlesztésének és konfigurálásának koncepcióit.

## <a name="signalr-service-configuration"></a>A SignalR szolgáltatás konfigurációja

Az Azure SignalR szolgáltatás különböző módokban konfigurálható. Ha az Azure Functions használatával használja, a szolgáltatást kiszolgáló nélküli módban kell *konfigurálni.*

Az Azure Portalon keresse meg a *SignalR* Service-erőforrás Beállítások lapját. Állítsa a *Szolgáltatás módot* *Kiszolgáló nélküli*re.

![Signalr szolgáltatás mód](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Az Azure Functions fejlesztése

Az Azure Functions és az Azure SignalR szolgáltatással létrehozott kiszolgáló nélküli, valós idejű alkalmazásokhoz két Azure-függvényre van szükség:

* A „negotiate” függvényre, amelyet az ügyfél egy érvényes SignalR-szolgáltatási hozzáférési jogkivonat és a szolgáltatásvégpont URL-címének lekéréséhez hív meg
* Egy vagy több olyan függvényre, amelyek üzeneteket küldenek, vagy csoporttagságokat kezelnek

### <a name="negotiate-function"></a>egyeztetési funkció

Egy ügyfélalkalmazás hoz egy érvényes hozzáférési jogkivonatot az Azure SignalR szolgáltatáshoz való csatlakozáshoz. A hozzáférési jogkivonat lehet névtelen vagy hitelesíthető egy adott felhasználói azonosítóhoz. A kiszolgáló nélküli SignalR-szolgáltatás alkalmazások nak szüksége van egy "negotiate" nevű HTTP-végpontra egy token és egyéb kapcsolati információk, például a SignalR szolgáltatás végponturl-címének beszerzéséhez.

Http-n aktivált Azure-függvény és a *SignalRConnectionInfo* bemeneti kötés a kapcsolatinformációs objektum létrehozásához. A függvénynek http-útvonallal `/negotiate`kell rendelkeznie, amely a végződése a.

Az egyeztetési függvény létrehozásáról a [ *SignalRConnectionInfo* bemeneti kötési hivatkozása című témakörben talál](../azure-functions/functions-bindings-signalr-service-input.md)további információt.

A hitelesített jogkivonat létrehozásáról az [App Service-hitelesítés használata](#using-app-service-authentication)című dokumentumban olvashat.

### <a name="sending-messages-and-managing-group-membership"></a>Üzenetek küldése és csoporttagság kezelése

A *SignalR* kimeneti kötés használatával üzeneteket küldhet az Azure SignalR Szolgáltatáshoz kapcsolódó ügyfeleknek. Az üzeneteket az összes ügyfélnek közvetítheti, vagy elküldheti az ügyfelek egy olyan részhalmazának, amely egy adott felhasználói azonosítóval van hitelesítve, vagy egy adott csoporthoz lett hozzáadva.

A felhasználók egy vagy több csoporthoz is hozzáadhatók. A *SignalR* kimenetkötéssel felhasználókat adhat hozzá vagy távolíthat el csoportokba.

További információt a [ *SignalR* kimeneti kötési hivatkozásban](../azure-functions/functions-bindings-signalr-service-output.md)talál.

### <a name="signalr-hubs"></a>SignalR hubok

A SignalR a "hubok" fogalma. Minden ügyfélkapcsolat és az Azure Functions által küldött üzenetek hatóköre egy adott hubra kerül. A hubok segítségével a kapcsolatokat és az üzeneteket logikai névterekbe is elválaszthatja.

## <a name="client-development"></a>Ügyfélfejlesztés

A SignalR ügyfélalkalmazások a SignalR ügyfél SDK-t számos nyelv egyikén használhatják az Azure SignalR Szolgáltatáshoz való egyszerű csatlakozáshoz és üzenetek fogadásához.

### <a name="configuring-a-client-connection"></a>Ügyfélkapcsolat konfigurálása

A SignalR szolgáltatáshoz való csatlakozáshoz az ügyfélnek végre kell végeznie a sikeres kapcsolategyeztetést, amely a következő lépésekből áll:

1. Az érvényes kapcsolati adatok beszerzése *érdekében* kérjen kérelmet a fent tárgyalt http-végpontra
1. Csatlakozás a SignalR szolgáltatáshoz a szolgáltatás végpontjának URL-címével és az *egyeztetési* végpontról kapott hozzáférési jogkivonattal

A SignalR ügyfél SDK-k már tartalmazzák az egyeztetési kézfogás végrehajtásához szükséges logikát. Adja át az egyeztetési végpont `negotiate` URL-címét, a szegmens `HubConnectionBuilder`nélkül, az SDK-nak. Íme egy példa a JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Az SDK konvenció szerint `/negotiate` automatikusan hozzáfűzi az URL-címet, és az egyeztetés megkezdéséhez használja.

> [!NOTE]
> Ha a JavaScript/TypeScript SDK-t használja egy böngészőben, engedélyeznie kell a [forrásközi erőforrások megosztását (CORS)](#enabling-cors) a Függvényalkalmazásban.

A SignalR client SDK használatával kapcsolatos további információkért tekintse meg az Ön nyelvének dokumentációját:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Üzenetek küldése az ügyféltől a szolgáltatásnak

Bár a SignalR SDK lehetővé teszi az ügyfélalkalmazások számára, hogy háttérlogikát hívjanak meg egy SignalR hubban, ez a funkció még nem támogatott, ha az Azure Functions szolgáltatás használatával használja. Http-kérelmek használatával hívja meg az Azure Functions.

## <a name="azure-functions-configuration"></a>Az Azure Functions konfigurációja

Az Azure SignalR Szolgáltatással integrálható Azure Function-alkalmazások ugyanúgy telepíthetők, mint bármely tipikus Azure Function alkalmazás, olyan technikák alkalmazásával, mint a [folyamatos üzembe helyezés,](../azure-functions/functions-continuous-deployment.md) [a zip-telepítés](../azure-functions/deployment-zip-push.md)és [a csomagból való futtatás.](../azure-functions/run-functions-from-deployment-package.md)

A SignalR szolgáltatás kötéseit használó alkalmazásokhoz azonban néhány speciális szempont is felmerül. Ha az ügyfél böngészőben fut, engedélyezni kell a CORS-t. És ha az alkalmazás hitelesítést igényel, integrálhatja a egyeztetési végpont app szolgáltatás hitelesítése.

### <a name="enabling-cors"></a>Cors engedélyezése

A JavaScript/TypeScript ügyfél HTTP-kéréseket küld az egyeztetési függvénynek a kapcsolategyeztetés kezdeményezéséhez. Ha az ügyfélalkalmazás az Azure Function alkalmazástól eltérő tartományban található, a függvényközi erőforrás-megosztást (CORS) engedélyezni kell a Függvényalkalmazásban, különben a böngésző blokkolja a kérelmeket.

#### <a name="localhost"></a>Localhost

Ha a Helyi számítógépen futtatja a Function `Host` alkalmazást, hozzáadhat egy szakaszt a *local.settings.json* hoz a CORS engedélyezéséhez. A `Host` szakaszban adjon hozzá két tulajdonságot:

* `CORS`- adja meg az ügyfélalkalmazás forrását tartalmazó alap URL-t
* `CORSCredentials`- állítsa `true` be, hogy engedélyezze a "withCredentials" kérelmeket

Példa:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Felhő - Azure Functions CORS

Cors engedélyezéséhez egy Azure Function-alkalmazásban, lépjen a CORS konfigurációs képernyő alatt a *Platform funkciók* fülön a Függvény alkalmazás az Azure Portalon.

> [!NOTE]
> A CORS-konfiguráció még nem érhető el az Azure Functions Linux-felhasználási csomagban. Használja az [Azure API Management](#cloud---azure-api-management) használatával a CORS engedélyezéséhez.

Az Access-Control-Allow-Credentials funkcióval rendelkező CORS-t engedélyezni kell ahhoz, hogy a SignalR ügyfél meghívja az egyeztetési funkciót. Az engedélyezéshez jelölje be a jelölőnégyzetet.

Az *Engedélyezett eredetek* szakaszban adjon hozzá egy bejegyzést a webalkalmazás eredeti alap URL-címével.

![CORS konfigurálása](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Felhő – Azure API-kezelés

Az Azure API Management olyan API-átjárót biztosít, amely képességeket ad a meglévő háttérszolgáltatásokhoz. Cors-t adhat hozzá a függvényalkalmazáshoz. Ez felajánl egy fogyasztás szint -val pay-per-tett árkialakítás és egy havonkénti szabad támogatás.

Az [Azure Function-alkalmazások importálásával](../api-management/import-function-app-as-api.md)kapcsolatos tudnivalókat az API Management dokumentációjában találja. Importálás után hozzáadhat egy bejövő házirendet, amely engedélyezi a CORS-t az Access-Control-Allow-Credentials támogatással.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Állítsa be a SignalR-ügyfeleket az API Management URL használatára.

### <a name="using-app-service-authentication"></a>Az App Service-hitelesítés használata

Az Azure Functions beépített hitelesítéssel rendelkezik, amely olyan népszerű szolgáltatókat támogat, mint a Facebook, a Twitter, a Microsoft-fiók, a Google és az Azure Active Directory. Ez a funkció integrálható a *SignalRConnectionInfo* kötéssel, hogy olyan kapcsolatokat hozzon létre az Azure SignalR szolgáltatáshoz, amelyek egy felhasználói azonosítóhoz lettek hitelesítve. Az alkalmazás üzeneteket küldhet a *SignalR* kimeneti kötés, amely az adott felhasználói azonosítót célozza.

Az Azure Portalon a Függvényalkalmazás *platformfunkciók* lapján nyissa meg a *Hitelesítési/engedélyezési* beállítások ablakot. Kövesse az [App Service-hitelesítés dokumentációját](../app-service/overview-authentication-authorization.md) a hitelesítés konfigurálásához egy ön által választott identitásszolgáltató használatával.

A konfigurálás után a `x-ms-client-principal-name` hitelesített `x-ms-client-principal-id` HTTP-kérelmek tartalmazzák a hitelesített identitás felhasználónevét és felhasználói azonosítóját tartalmazó fejléceket.

Ezeket a fejléceket a *SignalRConnectionInfo* kötési konfigurációban használhatja hitelesített kapcsolatok létrehozásához. Íme egy példa C# egyeztetési `x-ms-client-principal-id` függvény, amely a fejlécet használja.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Ezután üzenetet küldhet az adott `UserId` felhasználónak egy SignalR üzenet tulajdonságának beállításával.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

A más nyelvekről az Azure Functions referencia [az Azure SignalR Service kötései](../azure-functions/functions-bindings-signalr-service.md) című témakörben talál.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan fejleszthet és konfigurálhat kiszolgáló nélküli SignalR-szolgáltatásalkalmazásokat az Azure Functions használatával. Próbáljon meg saját maga létrehozni egy alkalmazást a [SignalR-szolgáltatás áttekintése oldalon](index.yml)található gyorsindítások vagy oktatóanyagok egyikével.