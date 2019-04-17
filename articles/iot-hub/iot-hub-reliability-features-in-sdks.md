---
title: Kapcsolat és az Azure IoT Hub eszközoldali SDK-k segítségével megbízható üzenetküldést kezelése
description: Ismerje meg, hogyan javíthatja az eszköz csatlakoztatása és üzenetküldési az Azure IoT Hub eszközoldali SDK-k használatával
services: iot-hub
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 9180c27e64f26c05e6e16007b74f9aa8a98bcfe5
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608826"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Kapcsolat és az Azure IoT Hub eszközoldali SDK-k segítségével megbízható üzenetküldést kezelése

Ez a cikk olyan általános útmutatást biztosít, amelyek rugalmasabb eszköz alkalmazások tervezésekor. Ez bemutatja, hogyan igénybe a kapcsolatot és megbízható üzenetkezelési szolgáltatások az Azure IoT eszközoldali SDK-k. Ez az útmutató célja, amelyek segítségével kezelheti a következő esetekben:

* Hálózati kapcsolat javítása

* Váltás másik hálózati kapcsolatok között

* Újracsatlakozás a szolgáltatás átmeneti kapcsolódási hibák miatt

Nyelv szerint a megvalósítási részletek eltérőek lehetnek. További információkért tekintse meg az API-dokumentáció vagy az adott SDK:

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>Rugalmasságot szem előtt tartó tervezés

IoT-eszközök (például a GSM vagy szatellit) instabil, vagy nem folyamatos hálózati kapcsolatok gyakran támaszkodnak. Hibák akkor fordulhatnak elő, amikor az eszközöket kezelheti az felhőalapú szolgáltatásokat időszakos szolgáltatás rendelkezésre állási és infrastruktúra-szintű vagy átmeneti hibák miatt. Az eszközön futó alkalmazást tartalmaz, a kapcsolat újbóli kapcsolat és az üzenetek küldése és fogadása az újrapróbálkozási logika mechanizmusok kezelése. Ezenkívül az újrapróbálkozási stratégia követelmények függ az eszköz IoT-forgatókönyvet, a környezetben, a képességek.

Az Azure IoT Hub eszközoldali SDK-k célja, hogy egyszerűbb csatlakoztatása és a felhőből az eszközre és eszközről a felhőbe való kommunikációhoz. Ezeket az SDK-k Azure IoT Hub és az üzenetek küldése és fogadása a lehetőségek széles köréhez csatlakozhat egy robusztus módot biztosítanak. A fejlesztők meglévő implementációjával testreszabásához jobb újrapróbálkozási stratégiát egy adott forgatókönyv esetén is módosíthatja.

Az alábbi szakaszok a megfelelő SDK funkcióit, amelyek támogatják a kapcsolatot és a megbízható üzenetküldést terjed ki.

## <a name="connection-and-retry"></a>Kapcsolat, majd próbálkozzon újra

Ez a szakasz áttekintést a rendelkezésre álló újbóli kapcsolatot, majd próbálkozzon újra minták kapcsolatok kezelése során. Eltérő újrapróbálkozási szabályzatok használata az eszköz alkalmazásban implementálási útmutatókban ismertetjük részletesen és a megfelelő API-kat, az eszköz SDK-k sorolja fel.

### <a name="error-patterns"></a>Hiba minták

Csatlakozási hibák számos különböző szinten fordulhat elő:

* Hálózati hibák: leválasztott szoftvercsatorna és name feloldási hibák

* Protokollszintű által jelzett hibákat HTTP, AMQP és MQTT átviteli: hivatkozások leválasztott vagy lejárt a munkamenet

* Alkalmazásszintű hibák, vagy helyi hibák eredményeképpen: érvénytelen hitelesítő adatok vagy a szolgáltatás működését (például a kvóta túllépése vagy szabályozás)

Az eszközoldali SDK-k minden három szinten hibák észlelése. Operációs rendszer kapcsolatos hibákat és a hardveres hibák nem észleli és kezeli az eszköz SDK-k. Az SDK terv alapján [az átmeneti hibák kezelése útmutatást](/azure/architecture/best-practices/transient-faults#general-guidelines) , az Azure Architecture Centert.

### <a name="retry-patterns"></a>Ismételje meg a minták

Az alábbi lépések bemutatják az újrapróbálkozási eljárást kapcsolati hibák észlelésekor:

1. Az SDK-t a hibát, és az azokhoz kapcsolódó hiba észleli a hálózat, a protokoll vagy az alkalmazásban.

2. Az SDK az a hiba szűrő meghatározni a hiba, majd döntse el, hogy szükség van-e egy újra.

3. Ha az SDK azonosítja egy **helyrehozhatatlan hiba**, műveletek, mint a kapcsolat, küldése és fogadása le lesz állítva. Az SDK értesíti a felhasználót. Helyreállíthatatlan hibák közé hitelesítési hiba és a egy rossz végponti hiba.

4. Ha az SDK azonosítja egy **Helyrehozható hiba**, újra megpróbálja a megadott újrapróbálkozási szabályzat szerint, amíg a megadott időkorlát lejárta.  Vegye figyelembe, hogy az SDK az **Exponential visszalépéssel a jittert** újrapróbálkozási szabályzat alapértelmezés szerint.
5. A megadott időkorlát lejár, az SDK leállítja az próbál csatlakozni, vagy küldjön. Ez értesíti a felhasználót.

6. Az SDK lehetővé teszi, hogy a felhasználó csatolása egy visszahívási kapcsolat állapotmódosítások fogadásához.

Az SDK-kkal nyújtanak három újrapróbálkozási szabályzatok:

* **Exponenciális visszatartás a jittert**: Az alapértelmezett újrapróbálkozási szabályzat általában kezdetekor agresszív, és addig maximális késleltetés idővel lelassíthatja. A terv alapján [újrapróbálkozási útmutatás az Azure Architecture Centert](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 

* **Egyéni újrapróbálkozási**: Egyes SDK nyelvek egy egyéni újrapróbálkozási szabályzatot, amely a forgatókönyv esetén jobban használhatók, és elhelyezése a RetryPolicy is tervezhet. Egyéni újrapróbálkozási nem érhető el a C SDK-val.

* **Nincs újrapróbálkozás**: Beállíthatja az újrapróbálkozási házirendje "nincs újrapróbálkozás," letiltja az újrapróbálkozási logika, amely a. Az SDK megpróbálja egyszer csatlakozhat, és küldjön üzenetet egyszer, feltéve, hogy létrejött a kapcsolat. Ez a szabályzat forgatókönyvek és a sávszélesség vagy a költségek problémák általában használatban van. Ha ezt a lehetőséget választja, nem küld üzeneteket is elvesznek, és nem állítható helyre.

### <a name="retry-policy-apis"></a>Újrapróbálkozási szabályzat API-k

   | SDK | SetRetryPolicy metódus | Házirend-megvalósítás | Implementálási segédlet |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Alapértelmezett**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Egyéni:** elérhető használata [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Nincs újrapróbálkozás:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS végrehajtása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Alapértelmezett**: [ExponentialBackoffWithJitter osztályban](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Egyéni:** megvalósítása [RetryPolicy felület](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-megvalósítás](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Alapértelmezett**: [ExponentialBackoff osztályban](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Egyéni:** megvalósítása [IRetryPolicy felület](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# végrehajtása](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Csomópont| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Alapértelmezett**: [ExponentialBackoffWithJitter osztályban](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Egyéni:** megvalósítása [RetryPolicy felület](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Csomópont-megvalósítás](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

A következő kódot a minták azt mutatják be, ezt a folyamatot:

#### <a name="net-implementation-guidance"></a>.NET-implementálási segédlet

Az alábbi példakód bemutatja, hogyan határozza meg, és az alapértelmezett újrapróbálkozási szabályzat beállítása:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Magas CPU-használat elkerülése érdekében az újrapróbálkozások kezelésére van szabályozva, ha a kód azonnal meghiúsul. Például, ha nem hálózati vagy a cél útvonalat. Hajtsa végre a következő újrapróbálkozás minimális idő értéke 1 másodperc.

Ha a szolgáltatás válaszol a sávszélesség-szabályozási hibával leáll, az újrapróbálkozási szabályzat eltérő, és nem lehet módosítani a nyilvános API-n keresztül:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Az újrapróbálkozási mechanizmus után leáll `DefaultOperationTimeoutInMilliseconds`, amely jelenleg beállított 4 perc.

#### <a name="other-languages-implementation-guidance"></a>Más nyelvek implementálási segédlet

Kódminták más nyelven tekintse át a következő megvalósítási dokumentumokat. A tárház tartalmaz, amelyek bemutatják az újrapróbálkozási szabályzat API-k használatát.

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>További lépések

* [Eszköz- és szolgáltatásoldali SDK-k használata](./iot-hub-devguide-sdks.md)

* [A C IoT eszközoldali SDK-jának használata](./iot-hub-device-sdk-c-intro.md)

* [Fejlesztés korlátozott eszközökhöz](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Fejlesztés mobileszközökhöz](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Hibaelhárítás eszköz bontja a kapcsolatot](iot-hub-troubleshoot-connectivity.md)