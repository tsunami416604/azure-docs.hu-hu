---
title: Azure Functions-alkalmazás fejlesztése & konfigurálása – Azure-jelző
description: A kiszolgáló nélküli valós idejű alkalmazások fejlesztésének és konfigurálásának részletei a Azure Functions és az Azure Signaler szolgáltatás használatával
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: f86a63315798d982f7e78fd1ff293061daf50132
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786775"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions fejlesztés és konfigurálás az Azure Signaler szolgáltatással

Azure Functions alkalmazások a valós idejű funkciók hozzáadására használhatják az [Azure signaler szolgáltatás kötéseit](../azure-functions/functions-bindings-signalr-service.md) . Az ügyfélalkalmazások számos nyelven elérhető ügyféloldali SDK-kat használnak az Azure Signaler szolgáltatáshoz való csatlakozáshoz és valós idejű üzenetek fogadásához.

Ez a cikk a Signaler szolgáltatással integrált Azure Function-alkalmazások fejlesztésére és konfigurálására vonatkozó fogalmakat ismerteti.

## <a name="signalr-service-configuration"></a>A signaler szolgáltatás konfigurációja

Az Azure Signaler szolgáltatás különböző módokon konfigurálható. Azure Functions használata esetén a szolgáltatást *kiszolgáló* nélküli módban kell konfigurálni.

A Azure Portal keresse meg a szignáló szolgáltatás erőforrásának *Beállítások* lapját. Állítsa a *szolgáltatási módot* *kiszolgáló*nélküli értékre.

![Jelző szolgáltatás üzemmódja](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions fejlesztés

A Azure Functions és az Azure Signaler szolgáltatással létrehozott kiszolgáló nélküli valós idejű alkalmazásnak általában két Azure Functions kell lennie:

* Egy "egyeztetés" függvény, amelyet az ügyfél hív meg egy érvényes jelző szolgáltatás hozzáférési jogkivonatának és szolgáltatási végpont URL-címének beszerzéséhez.
* Egy vagy több olyan függvény, amely üzeneteket küld vagy kezel csoporttagság

### <a name="negotiate-function"></a>egyeztetési függvény

Az ügyfélalkalmazás érvényes hozzáférési jogkivonatot igényel az Azure Signaler szolgáltatáshoz való kapcsolódáshoz. A hozzáférési tokenek névtelenek vagy egy adott felhasználói AZONOSÍTÓra hitelesíthetők. A kiszolgáló nélküli szignáló szolgáltatás alkalmazásai egy "egyeztetés" nevű HTTP-végpontot igényelnek a jogkivonat és egyéb kapcsolódási információk beszerzéséhez, például a Signaler szolgáltatás végpontjának URL-címét.

A kapcsolati információ objektum létrehozásához használjon HTTP-triggert használó Azure-függvényt és a *SignalRConnectionInfo* bemeneti kötését. A függvénynek olyan HTTP-útvonalon kell lennie, amely `/negotiate`ban ér véget.

Az egyeztetési függvény létrehozásával kapcsolatos további információkért tekintse meg a [ *SignalRConnectionInfo* bemeneti kötési referenciáját](../azure-functions/functions-bindings-signalr-service.md#input).

A hitelesített tokenek létrehozásáról a [app Service hitelesítés használata](#using-app-service-authentication)című témakörben olvashat bővebben.

### <a name="sending-messages-and-managing-group-membership"></a>Üzenetek küldése és csoporttagság kezelése

A *signaler* kimeneti kötés használatával üzeneteket küldhet az Azure Signaler szolgáltatáshoz csatlakozó ügyfeleknek. Az üzeneteket az összes ügyfél számára továbbíthatja, vagy egy adott felhasználói AZONOSÍTÓval hitelesített vagy egy adott csoportba felvett ügyfelek egy részhalmazára is elküldheti.

A felhasználók hozzáadhatók egy vagy több csoporthoz. A *jelző* kimeneti kötés használatával felhasználókat adhat hozzá vagy távolíthat el a csoportokból.

További információ: a [ *signaler* output kötési referenciája](../azure-functions/functions-bindings-signalr-service.md#output).

### <a name="signalr-hubs"></a>Szignáló hubok

A jelző a "hubok" fogalmát mutatja. Az egyes ügyfélkapcsolatok és a Azure Functionsból küldött összes üzenet hatóköre egy adott hubhoz tartozik. A hubokat a kapcsolatok és az üzenetek logikai névterekre való elkülönítésére használhatja.

## <a name="client-development"></a>Ügyfél-fejlesztés

A signaler ügyfélalkalmazások a Signaler Client SDK-t több nyelven is kihasználhatják, így egyszerűen csatlakozhatnak és fogadhatnak üzeneteket az Azure Signaler szolgáltatástól.

### <a name="configuring-a-client-connection"></a>Ügyfélkapcsolat konfigurálása

A Signaler szolgáltatáshoz való kapcsolódáshoz az ügyfélnek sikeres kapcsolódási egyeztetést kell végrehajtania, amely a következő lépésekből áll:

1. A fentiekben ismertetett *egyeztetési* http-végpontra vonatkozó kérelem elvégzése érvényes kapcsolódási adatok beszerzéséhez
1. Kapcsolódás a Signaler szolgáltatáshoz a szolgáltatási végpont URL-címével és az *egyeztetési* végponttól kapott hozzáférési jogkivonat használatával

A signaler ügyféloldali SDK-k már tartalmazzák az egyeztetési kézfogás végrehajtásához szükséges logikát. Adja át az egyeztetési végpont URL-címét, mínusz a `negotiate` szegmenst az SDK `HubConnectionBuilder`. Íme egy példa a JavaScriptben:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Az SDK az egyezmény szerint automatikusan hozzáfűzi `/negotiate` az URL-címhez, és az egyeztetés megkezdéséhez használja azt.

> [!NOTE]
> Ha a JavaScript/írógéppel SDK-t használja egy böngészőben, engedélyeznie kell az [CORS-alapú erőforrás-megosztást](#enabling-cors) a függvényalkalmazás.

A Signaler Client SDK használatával kapcsolatos további információkért tekintse meg a nyelv dokumentációját:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Üzenetek küldése az ügyféltől a szolgáltatásnak

Bár a Signaler SDK lehetővé teszi az ügyfélalkalmazások számára a háttérbeli logika meghívását a Signaler hub-ban, ez a funkció még nem támogatott, ha a Signaler szolgáltatást a Azure Functions használatával használja. Használjon HTTP-kérelmeket a Azure Functions meghívásához.

## <a name="azure-functions-configuration"></a>Azure Functions konfiguráció

Az Azure Signaler szolgáltatással integrálható Azure functions alkalmazások ugyanúgy telepíthetők, mint bármely jellemző Azure Function-alkalmazás, például a [folyamatos üzembe helyezés](../azure-functions/functions-continuous-deployment.md), a [zip-telepítés](../azure-functions/deployment-zip-push.md)és [a csomagból való futtatás](../azure-functions/run-functions-from-deployment-package.md).

A Signaler szolgáltatás kötéseit használó alkalmazások esetében azonban néhány speciális szempontot figyelembe kell venni. Ha az ügyfél böngészőben fut, engedélyezni kell a CORS. Ha az alkalmazás hitelesítést igényel, akkor App Service hitelesítéssel integrálhatja az egyeztetési végpontot.

### <a name="enabling-cors"></a>CORS engedélyezése

A JavaScript/írógéppel ügyfél HTTP-kérelmeket tesz elérhetővé az egyeztetési függvénynek a kapcsolódási egyeztetés elindításához. Ha az ügyfélalkalmazás az Azure Function alkalmazástól eltérő tartományban található, az eltérő eredetű erőforrás-megosztást (CORS) engedélyezni kell a Function alkalmazásban, vagy a böngésző letiltja a kérelmeket.

#### <a name="localhost"></a>Localhost

Ha a Function alkalmazást a helyi számítógépen futtatja, a CORS engedélyezéséhez hozzáadhat egy `Host` szakaszt a *Local. Settings. JSON* fájlhoz. A `Host` szakaszban vegyen fel két tulajdonságot:

* `CORS` – Itt adhatja meg az ügyfélalkalmazás forrásaként szolgáló alap URL-címet
* `CORSCredentials` – beállíthatja, hogy `true` engedélyezze a "withCredentials" kérelmeket

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

#### <a name="cloud---azure-functions-cors"></a>Felhőbeli Azure Functions CORS

Ha engedélyezni szeretné a CORS az Azure Function alkalmazásban, lépjen a CORS konfigurációs képernyőjére a Function alkalmazás *platform szolgáltatások* lapján a Azure Portal.

> [!NOTE]
> A CORS-konfiguráció még nem érhető el Azure Functions Linux-használati tervben. Az [Azure API Management](#cloud---azure-api-management) használatával engedélyezze a CORS.

A CORS hozzáférés-vezérlést engedélyező hitelesítő adatokat engedélyezni kell a Signaler-ügyfélnek az egyeztetési függvény meghívásához. Jelölje be a jelölőnégyzetet az engedélyezéshez.

Az *engedélyezett eredetek* szakaszban adjon hozzá egy bejegyzést a webalkalmazás forrás alap URL-címével.

![CORS konfigurálása](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Felhő – Azure API Management

Az Azure API Management olyan API-átjárót biztosít, amely funkciókkal bővíti a meglévő háttér-szolgáltatásokat. Használhatja CORS hozzáadásához a Function alkalmazáshoz. A szolgáltatás díjköteles díjszabással és havi ingyenes támogatással rendelkezik.

Az [Azure functions-alkalmazások importálásával](../api-management/import-function-app-as-api.md)kapcsolatos információkért tekintse meg az API Management dokumentációját. Az importálás után hozzáadhat egy bejövő szabályzatot, amely lehetővé teszi a CORS hozzáférés-vezérlés – engedélyezés – hitelesítő adatok támogatását.

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

Konfigurálja a Signaler-ügyfeleket a API Management URL-cím használatára.

### <a name="using-app-service-authentication"></a>App Service hitelesítés használata

A Azure Functions beépített hitelesítéssel támogatja a népszerű szolgáltatók, például a Facebook, a Twitter, a Microsoft-fiók, a Google és a Azure Active Directory támogatását. Ez a funkció integrálható a *SignalRConnectionInfo* -kötéssel, hogy kapcsolatot hozzon létre az Azure-beli jelző szolgáltatással, amelyek hitelesítése felhasználói azonosítóra történt. Az alkalmazás képes üzeneteket küldeni az adott felhasználói AZONOSÍTÓra irányuló *szignáló* kimeneti kötés használatával.

A Azure Portal a Function app *platform-funkciók* lapján nyissa meg a *hitelesítési/engedélyezési* beállítások ablakot. A hitelesítés konfigurálásához kövesse az [app Service-hitelesítés](../app-service/overview-authentication-authorization.md) dokumentációját.

A konfigurálást követően a hitelesített HTTP-kérelmek közé tartoznak a hitelesített identitás felhasználónevét és felhasználói AZONOSÍTÓját tartalmazó `x-ms-client-principal-name` és `x-ms-client-principal-id` fejlécek is.

A *SignalRConnectionInfo* kötési konfigurációjában ezeket a fejléceket használhatja a hitelesített kapcsolatok létrehozásához. Íme egy példa C# az egyeztetési függvényre, amely a `x-ms-client-principal-id` fejlécet használja.

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

Ezután üzeneteket küldhet az adott felhasználónak a jelző üzenet `UserId` tulajdonságának beállításával.

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

Más nyelvekkel kapcsolatos információkért tekintse meg az [Azure signaler szolgáltatás kötéseit](../azure-functions/functions-bindings-signalr-service.md) Azure functions-referenciához.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan fejlesztheti és konfigurálhatja a kiszolgáló nélküli Signal Service-alkalmazásokat Azure Functions használatával. Hozzon létre egy alkalmazást saját maga a [signaler szolgáltatás áttekintés lapján](index.yml)található gyors indítás vagy oktatóanyag használatával.