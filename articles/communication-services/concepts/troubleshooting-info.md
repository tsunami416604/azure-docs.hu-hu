---
title: Hibaelhárítás az Azure kommunikációs szolgáltatásokban
description: Ismerje meg, hogyan gyűjtheti össze a kommunikációs szolgáltatások megoldásához szükséges információkat.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754743"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Hibaelhárítás az Azure kommunikációs szolgáltatásokban

Ez a dokumentum segítséget nyújt a kommunikációs szolgáltatások megoldásához szükséges információk összegyűjtésében.

## <a name="getting-help"></a>Segítség kérése

Javasoljuk, hogy a fejlesztők a kommunikációs szolgáltatások [GitHub-tárházában](https://github.com/Azure/communication)felmerülő problémákkal kapcsolatos kérdéseket, javaslatokat és problémákat jelentsenek. További fórumok a következők:

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Az Azure-előfizetések [támogatási](https://azure.microsoft.com/support/plans/) csomagjától függően közvetlenül a [Azure Portal](https://azure.microsoft.com/support/create-ticket/)is benyújthatja a támogatási jegyet.

Bizonyos típusú problémák elhárítása érdekében a következő információk bármelyikét kérheti:

* **MS-CV-azonosító** : ez az azonosító a hívások és üzenetek hibakereséséhez használatos. 
* **Call ID** : ez az azonosító a kommunikációs szolgáltatások hívásainak azonosítására szolgál.
* **SMS-üzenet azonosítója** : ez az azonosító az SMS-üzenetek azonosítására szolgál.

## <a name="access-your-ms-cv-id"></a>Hozzáférés az MS-CV-AZONOSÍTÓhoz

Az MS-CV AZONOSÍTÓját a diagnosztika konfigurálásával érheti el az `clientOptions` Object példányban az ügyféloldali kódtárak inicializálásakor. A diagnosztika bármely Azure-ügyfél kódtára számára konfigurálható, beleértve a csevegést, a felügyeletet és a VoIP-hívást.

### <a name="client-options-example"></a>Ügyfél-beállítások – példa

A következő kódrészletek szemléltetik a diagnosztika konfigurációját. Ha az ügyféloldali kódtárakat a diagnosztika engedélyezve van, a rendszer a diagnosztikai adatokat a beállított esemény-figyelőhöz fogja kiadni:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Hozzáférés a hívási AZONOSÍTÓhoz

Ha egy támogatási kérést a hívási problémákkal kapcsolatos Azure Portal küld, a rendszer megkérheti, hogy adja meg a hivatkozott hívás AZONOSÍTÓját. Ez a hívó ügyféloldali könyvtárán keresztül érhető el:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Az SMS-üzenet AZONOSÍTÓjának elérése

SMS-problémák esetén a Response objektumból gyűjtheti az üzenet AZONOSÍTÓját.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Kapcsolódó információk
- [Naplók és diagnosztika](logging-and-diagnostics.md)
- [Metrikák](metrics.md)
