---
title: Fejlesztés és az Azure Functions SignalR Service-alkalmazások konfigurálása
description: Megtudhatja, hogyan fejleszthet és kiszolgáló nélküli valós idejű alkalmazások az Azure Functions és az Azure SignalR Service konfigurálása
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569152"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Az Azure Functions fejlesztői és az Azure SignalR Service configuration

Az Azure Functions-alkalmazások használhatják a [Azure SignalR Service kötések](../azure-functions/functions-bindings-signalr-service.md) segítségével valós idejű funkciókkal. Ügyfélalkalmazások ügyfél SDK-k több nyelven is elérhető az Azure SignalR Service csatlakoztatása és valós idejű üzenetek fogadása használja.

Ez a cikk ismerteti a fogalmakat és egy Azure-függvényalkalmazást, amely integrálva van a SignalR Service konfigurálása.

## <a name="signalr-service-configuration"></a>A SignalR szolgáltatás konfigurációja

Az Azure SignalR Service különböző módban konfigurálható. Az Azure Functions használatakor a szolgáltatás kell konfigurálni a *kiszolgáló nélküli* mód.

Az Azure Portalon keresse meg a *beállítások* a SignalR Service erőforrás lapján. Állítsa be a *szolgáltatás mód* való *kiszolgáló nélküli*.

![SignalR Service mód](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions-fejlesztés

Egy kiszolgáló nélküli valós idejű alkalmazás, általában az Azure Functions és az Azure SignalR Service-ben létrehozott két Azure Functions van szükség:

* "Egyeztetni" függvény, amely az ügyfél meghívja a egy érvényes SignalR Service beszerzése hozzáférési tokent, és szolgáltatási végpont URL-címe
* Egy vagy több függvényt, amely üzeneteket küldeni vagy a csoporttagság kezelése

### <a name="negotiate-function"></a>függvény egyeztetése

Egy ügyfélalkalmazás kell csatlakozni az Azure SignalR Service egy érvényes hozzáférési jogkivonatot. Hozzáférési jogkivonat lehet névtelen vagy hitelesített egy megadott felhasználói azonosító. Kiszolgáló nélküli SignalR Service alkalmazásoknak egy HTTP-végpontot nevű, "egyeztetni" a jogkivonatot, és egyéb kapcsolati adatok, például a SignalR Service-végpont URL-címe.

Használata egy HTTP által aktivált Azure-függvényt, és a *SignalRConnectionInfo* bemeneti kötést létrehozni a kapcsolati információ objektum. A függvénynek egy HTTP-útvonal, amely `/negotiate`.

Az egyeztetés függvény létrehozásáról további információkért lásd: a [ *SignalRConnectionInfo* bemeneti kötést referencia](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Egy hitelesített jogkivonat létrehozásával kapcsolatos további tudnivalókért tekintse meg [App Service-hitelesítés használatával](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Üzenetek küldése és csoporttagság kezelése

Használja a *SignalR* kimeneti kötés küldhet üzeneteket az ügyfelek az Azure SignalR Service csatlakozik. Összes ügyfélre elküldheti üzeneteket, vagy küldhet nekik egy részhalmazára hitelesítése egy adott felhasználói azonosítóval vagy egy adott csoporthoz lettek hozzáadva.

Felhasználók egy vagy több csoportot is hozzáadhatók. Is használhatja a *SignalR* kimeneti kötésének hozzáadása vagy eltávolítása a felhasználók/csoportok.

További információkért lásd: a [ *SignalR* kimeneti kötés referencia](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>SignalR Hubs

A SignalR rendelkezik egy "központokat" fogalma. Minden ügyfél kapcsolat és az Azure Functions által küldött minden üzenet egy adott hub hatókörét. Hubs használhatja arra, hogy a kapcsolatok és az üzenetek szét logikai névterek.

## <a name="client-development"></a>Ügyféloldali fejlesztés

A SignalR ügyfélalkalmazások kihasználhatják a SignalR ügyfél-SDK több nyelven valamelyik könnyedén csatlakozhat, és üzenet fogadása az Azure SignalR Service.

### <a name="configuring-a-client-connection"></a>Egy ügyfél-kapcsolat beállítása

Szeretne csatlakozni a SignalR Service, egy ügyfél a sikeres kapcsolat egyeztetési, amely az alábbi lépéseket kell elvégeznie:

1. Kérheti az *egyeztetni* érvényes kapcsolati információk beszerzése a fent ismertetett HTTP-végpont
1. SignalR Service, a szolgáltatás-végponti URL-cím használatával csatlakozhat, és a hozzáférési jogkivonat érkezett a *egyeztetni* végpont

A SignalR ügyfél SDK-k már tartalmazzák az egyeztetési kézfogás elvégzéséhez szükséges logikát. Adja át az egyeztetés végpont URL-CÍMÉT, csökkentve a `negotiate` szegmens, az SDK `HubConnectionBuilder`. Íme egy példa a JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Szabályok szerint az SDK automatikusan hozzáfűzi `/negotiate` az URL-címet, és használja a egyeztetését.

> [!NOTE]
> Ha a böngészőben JavaScript/TypeScript az SDK-t használ, akkor kell [engedélyezi eltérő eredetű erőforrások megosztása (CORS)](#enabling-cors) a Függvényalkalmazásban.

A SignalR ügyfél-SDK használatának további információkért tekintse meg a kívánt nyelvet a dokumentáció:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Üzenetek küldése egy ügyfél a szolgáltatás

Bár a SignalR SDK lehetővé teszi az ügyfél egy SignalR-központon a háttérrendszer-logika meghívásához alkalmazások, ez a funkció még nem támogatott az Azure Functions SignalR Service használata esetén. Meghívása Azure Functions használata a HTTP kérelmeket.

## <a name="azure-functions-configuration"></a>Az Azure Functions konfigurálása

Az Azure Function apps, amelyekbe beépül az Azure SignalR Service hasonlóan minden szokásos Azure-függvényalkalmazást, technikák használatával telepíthető [folyamatos üzembe helyezés](../azure-functions/functions-continuous-deployment.md), [üzembe helyezés zip](../azure-functions/deployment-zip-push.md), és [futtassa a csomag](../azure-functions/run-functions-from-deployment-package.md).

Azonban van néhány különleges szempontok a SignalR Service kötéseket használó alkalmazások esetében. Ha az ügyfél egy böngészőben fut, a CORS engedélyezve kell lennie. És ha az alkalmazás hitelesítést igényel, az App Service-hitelesítés is integrálhatja az egyeztetés végpont.

### <a name="enabling-cors"></a>A CORS engedélyezése

A JavaScript-vagy TypeScript-ügyfél a kapcsolat egyeztetés az egyeztetés függvény HTTP-kéréseket hajt végre. Az ügyfélalkalmazás egy másik tartományban található, mint az Azure-függvényalkalmazást a jöhet szóba, ha eltérő eredetű erőforrások megosztása (CORS) engedélyezni kell a függvényalkalmazást, vagy a böngésző blokkolja a kérelmeket.

#### <a name="localhost"></a>Localhost

Amikor a függvényalkalmazás a helyi számítógépen futó, hozzáadhat egy `Host` szakasz *local.settings.json* CORS engedélyezése. Az a `Host` két tulajdonságai területen:

* `CORS` -Adja meg, amely a forrás az ügyfél-alkalmazás alap URL-címe
* `CORSCredentials` – Állítsa `true` "withCredentials" kérelem engedélyezéséhez

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

#### <a name="azure"></a>Azure

Egy Azure-függvényalkalmazás CORS engedélyezéséhez nyissa meg a CORS konfigurálása képernyő alatt a *platformfunkciók* az Azure Portalon a függvényalkalmazás lapján.

Egyeztetés függvény a SignalR-ügyfél engedélyezni kell a CORS hozzáférés-vezérlés – engedélyezése-hitelesítő adatokkal. Jelölje be a jelölőnégyzetet az engedélyezéshez.

Az a *engedélyezett eredetek* területén adjon hozzá egy bejegyzést a forrás alap URL-címet a webalkalmazás.

![A CORS konfigurálása](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>App Service-hitelesítés használatával

Az Azure Functions támogatja a népszerű szolgáltatókhoz például Facebook, Twitter, a Microsoft Account, a Google és Azure Active Directory beépített hitelesítés rendelkezik. Ez a szolgáltatás integrálható a *SignalRConnectionInfo* kötés létrehozása az Azure SignalR Service egy felhasználói azonosítót. a hitelesített kapcsolatokat Az alkalmazás használatával üzeneteket küldhetnek a *SignalR* kimeneti kötést, amelyekre vonatkozik a felhasználói azonosító.

Az Azure Portalon a függvényalkalmazás *platformfunkciók* lap meg van nyitva a *hitelesítés/engedélyezés* beállítások ablak. Kövesse a dokumentációban [App Service-hitelesítés](../app-service/overview-authentication-authorization.md) tetszőleges egy identitásszolgáltató használatával hitelesítésének konfigurálása.

Hitelesített HTTP-kérések tartalmazza a beállítása után `x-ms-client-principal-name` és `x-ms-client-principal-id` a hitelesített identitástól felhasználónevet és felhasználói azonosítója, illetve tartalmazó fejlécek.

Ezek a fejlécek is használhatja a *SignalRConnectionInfo* kötelező konfigurációs hitelesített kapcsolatot létrehozni. Íme egy példa C# használó függvényt egyeztetni a `x-ms-client-principal-id` fejléc.

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

Ezután elküldheti üzeneteket, hogy a felhasználó beállításával a `UserId` tulajdonság egy SignalR-üzenet.

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

Más nyelvekre vonatkozó információkért lásd: a [Azure SignalR Service kötések](../azure-functions/functions-bindings-signalr-service.md) az Azure Functions hivatkozhat.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulhatta, hogyan fejleszthet és az Azure Functions szolgáltatással kiszolgáló nélküli SignalR Service-alkalmazások konfigurálása. Próbálja meg létrehozni egy alkalmazás segítségével a rövid útmutatók és oktatóanyagok a saját a [SignalR Service – Áttekintés lap](index.yml).