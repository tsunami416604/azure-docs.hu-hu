---
title: Csatlakozási és megbízható üzenetküldést használatával az Azure IoT Hub eszközoldali SDK-k kezelése
description: Ismerje meg, hogyan javíthatja az eszköz csatlakoztatása és üzenetküldési az Azure IoT Hub eszközoldali SDK-k használatával
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a318a1ef8b13b8fcb4f4401ac4d0e45037958d63
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127586"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Csatlakozási és megbízható üzenetküldést használatával az Azure IoT Hub eszközoldali SDK-k kezelése

Ez az útmutató részletes útmutatást talál az Azure IoT eszközoldali SDK-k a kapcsolatok és megbízható üzenetkezelési szolgáltatások előnyeit kihasználva rugalmas eszköz alkalmazások tervezéséhez. Ez a cikk célja, válaszoljon a kérdésekre és ezek a forgatókönyvek kezeléséhez:

- hálózati kapcsolat kezelése
- Váltás másik hálózati kapcsolatok kezelése
- újrakapcsolódási szolgáltatás átmeneti kapcsolódási hiba miatt kezelése

Megvalósítási részletei is lehet régiónként eltérő nyelv, társított API dokumentációjában vagy a megadott SDK további részletekért.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [NODE SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Rugalmasságot szem előtt tartó tervezés

IoT-eszközök nem folytonos és/vagy instabil hálózati kapcsolatok, például a GSM vagy szatellit gyakran támaszkodnak. Emellett amikor felhőalapú szolgáltatással való interakcióhoz, hibák akkor fordulhatnak elő átmeneti körülmények például időszakos szolgáltatás rendelkezésre állása és infrastruktúra-szintű hibák (más néven az átmeneti hibák) miatt. Egy alkalmazás olyan eszközön fut a kapcsolat és újracsatlakozási mechanizmusok, valamint az újrapróbálkozási logika küldése/fogadása üzenetek felügyelnie kell. Ezenkívül az újrapróbálkozási stratégia követelmények függ az IoT-forgatókönyvet, az eszköz vesz részt, és az eszköz környezeti és funkciókat.

Az Azure IoT Hub eszközoldali SDK-k célja, hogy egyszerűsítse a csatlakozás és a egy hatékony és átfogó módszert, csatlakozás és az Azure IoT Hub üzeneteket küldő/fogadó azáltal, hogy a felhőből az eszközre és eszközről a felhőbe való kommunikáció. A fejlesztők meglévő implementációjával fejleszthet a megfelelő újrapróbálkozási stratégiát egy adott forgatókönyv esetén is módosíthatja.

Az alábbi szakaszok a megfelelő SDK funkcióit, amelyek támogatják a kapcsolatot és a megbízható üzenetküldést terjed ki.

## <a name="connection-and-retry"></a>Kapcsolat, majd próbálkozzon újra

Ez a szakasz áttekintést elérhető újrakapcsolódási, majd próbálkozzon újra minták kapcsolatok, implementálási útmutatókban ismertetjük az alkalmazást, és a megfelelő API-kat eltérő újrapróbálkozási szabályzatok használata az eszköz SDK-k kezelése során.

### <a name="error-patterns"></a>Hiba minták
Csatlakozási hibák sok szint fordulhat elő:

-  A hálózati hibákat, például egy leválasztott szoftvercsatorna és name feloldási hibák
- HTTP, AMQP és MQTT protokoll szintű hibáinak átviteli például hivatkozások leválasztott vagy a munkamenet lejárt
- Alkalmazásszintű hibák, amelyek például érvénytelen hitelesítő adatokat vagy helyi hibák eredményeképpen vagy szolgáltatás például kvóta túllépése vagy szabályozási viselkedés

Az eszközoldali SDK-k minden három szinten észleli a hibákat.  Operációs rendszer kapcsolatos hibákat és a hardveres hibák nem észleli és kezeli az eszköz SDK-k.  A terv alapján [az átmeneti hibák kezelése útmutatást](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) az Azure Architecture Centert.

### <a name="retry-patterns"></a>Ismételje meg a minták

Ismételje meg a kapcsolati hibák észlelésekor általános folyamata a következő: 
1. Az SDK-t a hibát, és az azokhoz kapcsolódó hiba észleli a hálózat, a protokoll vagy alkalmazás.
2. Hiba típusa alapján az SDK-t használ, döntse el, ha a szűrőt a hiba újra kell elvégezni.  Ha egy **helyrehozhatatlan hiba** azonosítja az SDK által műveleteket (kapcsolódási és küldési/fogadási) leáll, és az SDK értesíti a felhasználót. Helyreállíthatatlan hiba, amely az SDK-val azonosíthatja, és határozza meg, hogy nem állítható helyre, például hiba, hitelesítési vagy hibás végponti hiba.
3. Ha egy **Helyrehozható hiba** van azonosítva, az SDK-val kezdődik próbálkozzon újra, amíg le nem jár egy meghatározott időkorlát megadott újrapróbálkozási szabályzat használatával.
4. Amikor a meghatározott időkorlátja lejár, az SDK leállítja próbál csatlakozni, vagy küldjön, és értesíti a felhasználót.
5.  Az SDK lehetővé teszi, hogy a felhasználó csatolása egy visszahívási kapcsolat állapotmódosítások fogadásához. 

Három újrapróbálkozási szabályzatok áll rendelkezésre:
- **Exponenciális visszatartás a jittert**: Ez a alkalmazni alapértelmezett újrapróbálkozási szabályzatát.  Azt általában agresszív kezdetekor lelassul, és majd eléri a maximális késleltetés, amely nem jár.  A terv alapján [újrapróbálkozási útmutatás az Azure Architecture Centert](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Egyéni újrapróbálkozási**: egyéni újrapróbálkozási szabályzat megvalósítása, és képes beszúrni a RetryPolicy a választott nyelvtől függően. A forgatókönyv egy újrapróbálkozási szabályzatot, amely a megfelelő is tervezhet.  Ez a nem érhető el a C SDK-val.
- **Nincs újrapróbálkozás**: "nincs újrapróbálkozás,", amely letiltja az újrapróbálkozási logika újrapróbálkozási házirend beállításához lehetőség van.  Az SDK megpróbálja egyszer csatlakozhat, és küldjön üzenetet egyszer, feltéve, hogy létrejött a kapcsolat. Ez a szabályzat jellemző használatát azokban az esetekben, ahol sávszélesség vagy a költségek vonatkozik.   Ha ezt a lehetőséget választja, nem küld üzeneteket is elvesznek, és nem állítható helyre. 

### <a name="retry-policy-apis"></a>Újrapróbálkozási szabályzat API-k

   | SDK | SetRetryPolicy metódus | Házirend-megvalósítás | Implementálási segédlet |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Alapértelmezett**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Egyéni:** elérhető használata [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Nincs újrapróbálkozás:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS végrehajtása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Alapértelmezett**: [ExponentialBackoffWithJitter osztályban](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Egyéni:** megvalósítása [RetryPolicy felület](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-megvalósítás](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Alapértelmezett**: [ExponentialBackoff osztályban](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Egyéni:** megvalósítása [IRetryPolicy felület](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# végrehajtása]() |
   | Csomópont| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Alapértelmezett**: [ExponentialBackoffWithJitter osztályban](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Egyéni:** megvalósítása [RetryPolicy felület](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Csomópont-megvalósítás](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Az alábbiakban kódmintákért ezt a folyamatot. 

#### <a name="net-implementation-guidance"></a>.NET-implementálási segédlet

Az alábbi példakód bemutatja, hogyan határozza meg, és az alapértelmezett újrapróbálkozási szabályzat beállítása:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Magas CPU-használat elkerülése érdekében az újrapróbálkozások kezelésére van szabályozva, ha azonnal (például, ha nem hálózati vagy a cél útvonal) meghiúsul a kódot úgy, hogy hajtsa végre a következő újrapróbálkozás minimálisan mennyi 1 másodperc. 

A szolgáltatás válaszol szabályozási hibával leáll, ha az újrapróbálkozási szabályzat eltérő, és nem lehet módosítani a nyilvános API-n keresztül:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Az újrapróbálkozási mechanizmus után le fog állni `DefaultOperationTimeoutInMilliseconds`, amely jelenleg beállított 4 perc.

#### <a name="other-languages-implementation-guidance"></a>Más nyelvek implementálási segédlet
Más nyelven tekintse meg az alábbi megvalósítási dokumentációt.  A minta API-kat a rendszer a tárházban megadott újrapróbálkozási szabályzat használatának bemutatásához.
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [NODE SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

