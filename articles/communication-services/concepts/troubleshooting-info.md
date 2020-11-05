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
ms.openlocfilehash: ff3e7fee87661fb89ba930b7368bd54e71ad57bf
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357623"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Hibaelhárítás az Azure kommunikációs szolgáltatásokban

Ez a dokumentum segítséget nyújt a kommunikációs szolgáltatási megoldásban felmerülő problémák elhárításában. Ha SMS-sel kapcsolatos hibaelhárítást végez, [engedélyezheti a kézbesítési jelentéskészítést Event Grid](../quickstarts/telephony-sms/handle-sms-events.md) az SMS-kézbesítési adatok rögzítéséhez.

## <a name="getting-help"></a>Segítség kérése

Javasoljuk, hogy a fejlesztők a kommunikációs szolgáltatások [GitHub-tárházában](https://github.com/Azure/communication)felmerülő problémákkal kapcsolatos kérdéseket, javaslatokat és problémákat jelentsenek. További fórumok a következők:

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Az Azure-előfizetések [támogatási](https://azure.microsoft.com/support/plans/) csomagjától függően közvetlenül a [Azure Portal](https://azure.microsoft.com/support/create-ticket/)is benyújthatja a támogatási jegyet.

Bizonyos típusú problémák elhárítása érdekében a következő információk bármelyikét kérheti:

* **MS-CV-azonosító** : ez az azonosító a hívások és üzenetek hibakereséséhez használatos. 
* **Call ID** : ez az azonosító a kommunikációs szolgáltatások hívásainak azonosítására szolgál.
* **SMS-üzenet azonosítója** : ez az azonosító az SMS-üzenetek azonosítására szolgál.
* **Hívásnaplók: ezek** a naplók részletes információkat tartalmaznak, amelyek a hívási és hálózati problémák megoldásához használhatók.


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

## <a name="enable-and-access-call-logs"></a>Hívásnaplók engedélyezése és elérése




# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő kód használatával konfigurálhatja a `AzureLogger` naplófájlokat a konzolon a JavaScript ügyféloldali kódtár segítségével:

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

Az iOS rendszerre való fejlesztéskor a naplók `.blog` fájlokba kerülnek. Vegye figyelembe, hogy a naplókat nem lehet közvetlenül megtekinteni, mert azok titkosítottak.

Ezek a Xcode megnyitásával érhetők el. Nyissa meg a Windows > eszközök és szimulátorok > eszközöket. Jelölje ki az eszközét. A telepített alkalmazások területen válassza ki az alkalmazást, és kattintson a "tároló letöltése" elemre. 

Ezzel egy fájlt fog adni `xcappdata` . Kattintson a jobb gombbal a fájlra, és válassza a "csomag tartalmának megjelenítése" lehetőséget. Ekkor megjelenik az `.blog` Azure-támogatási kérelemhez csatolni kívánt fájlok.

# <a name="android"></a>[Android](#tab/android)

Az Android rendszerre való fejlesztéskor a rendszer fájlokba tárolja a naplókat `.blog` . Vegye figyelembe, hogy a naplókat nem lehet közvetlenül megtekinteni, mert azok titkosítottak.

Android Studioon navigáljon az eszköz Fájlkezelőhöz úgy, hogy kijelöli a megtekintés > eszköz Windows > eszköz Fájlkezelő lehetőséget a szimulátorból és az eszközről. A `.blog` fájl az alkalmazás könyvtárában fog megjelenni, és így kell kinéznie `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` . Ezt a fájlt csatolhatja a támogatási kérelemhez. 
   

---


## <a name="related-information"></a>Kapcsolódó információk
- [Naplók és diagnosztika](logging-and-diagnostics.md)
- [Metrikák](metrics.md)
