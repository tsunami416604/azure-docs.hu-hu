---
title: Az IoT Hub-kapcsolat kezelése & megbízható üzenetküldési w/device SDK-k
description: Ismerje meg, hogyan javíthatja az eszközkapcsolatot és az üzenetküldést az Azure IoT Hub-eszköz SDK-ihasználata esetén
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 1ca7219824a00a5af0bed7d42da75fc06ce2010d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284446"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>A kapcsolat és a megbízható üzenetküldés kezelése az Azure IoT Hub sdk-k használatával

Ez a cikk magas szintű útmutatást nyújt a rugalmasabb eszközalkalmazások tervezéséhez. Bemutatja, hogyan használhatja ki a kapcsolat és a megbízható üzenetkezelési funkciók az Azure IoT-eszköz SDK-k előnyeit. Az útmutató célja, hogy segítsen a következő esetek kezelésében:

* Megszakadt hálózati kapcsolat javítása

* Váltás a különböző hálózati kapcsolatok között

* Újracsatlakozás szolgáltatásátmeneti csatlakozási hibái miatt

A megvalósítás részletei nyelvtől függően változhatnak. További információt az API dokumentációjában vagy az Adott SDK-ban talál:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (A megbízhatóság még nincs megvalósítva)

## <a name="designing-for-resiliency"></a>Rugalmasságot szem előtt tartó tervezés

Az IoT-eszközök gyakran nem folyamatos vagy instabil hálózati kapcsolatokra (például GSM vagy műhold) támaszkodnak. Hibák akkor fordulhatnak elő, ha az eszközök együttműködnek a felhőalapú szolgáltatásokkal a szolgáltatás időszakos rendelkezésre állása és az infrastruktúra szintű vagy átmeneti hibák miatt. Az eszközön futó alkalmazásoknak kezelniük kell a kapcsolat, az újracsatlakozás és az üzenetek küldésére és fogadására szolgáló újrapróbálkozási logikát. Az újrapróbálkozási stratégia követelményei nagymértékben függnek az eszköz IoT-forgatókönyvétől, környezetétől, képességeitől.

Az Azure IoT Hub-eszköz SDK-k célja, hogy egyszerűsítse a felhőből az eszközre és az eszközről a felhőbe való csatlakozást és kommunikációt. Ezek az SDK-k robusztus módot biztosítanak az Azure IoT Hubhoz való csatlakozásra, valamint az üzenetek küldésére és fogadására vonatkozó átfogó lehetőségek et. A fejlesztők is módosíthatja a meglévő megvalósítás testreszabása egy jobb újrapróbálkozási stratégia egy adott forgatókönyv.

A kapcsolatés a megbízható üzenetküldést támogató sdk-funkciók a következő szakaszokban találhatók.

## <a name="connection-and-retry"></a>Csatlakozás és újrapróbálkozás

Ez a szakasz áttekintést nyújt a kapcsolatok kezelése során elérhető újracsatlakozásról és újrapróbálkozási mintákról. Ismerteti a megvalósítási útmutatást egy másik újrapróbálkozási szabályzat az eszközalkalmazásban, és felsorolja az eszköz SDK-k releváns API-k használatával.

### <a name="error-patterns"></a>Hibaminták

A csatlakozási hibák több szinten is előfordulhatnak:

* Hálózati hibák: leválasztott szoftvercsatorna- és névfeloldási hibák

* Protokollszintű hibák HTTP- és AMQP- és MQTT-átvitelesetén: leválasztott kapcsolatok vagy lejárt munkamenetek

* Helyi hibákból eredő alkalmazásszintű hibák: érvénytelen hitelesítő adatok vagy szolgáltatásviselkedés (például a kvóta túllépése vagy szabályozás)

Az eszköz SDK-k hibák észlelése mindhárom szinten. Az operációs rendszerrel kapcsolatos hibákat és hardverhibákat az eszköz SDK-i nem észlelik és kezelik. Az SDK-kialakítás az Azure Architecture Center [átmeneti hibakezelési útmutatóján](/azure/architecture/best-practices/transient-faults#general-guidelines) alapul.

### <a name="retry-patterns"></a>Mintázatok újrapróbálkozása

A következő lépések az újrapróbálkozási folyamatot ismertetik, amikor csatlakozási hibákat észleltek:

1. Az SDK észleli a hibát és a kapcsolódó hibát a hálózatban, a protokollban vagy az alkalmazásban.

2. Az SDK a hibaszűrő segítségével határozza meg a hiba típusát, és döntse el, hogy szükség van-e újrapróbálkozásra.

3. Ha az SDK **helyrehozhatatlan hibát**észlel, a műveletek, például a kapcsolat, a küldés és a fogadás le állnak. Az SDK értesíti a felhasználót. A helyreállíthatatlan hibák közé tartozik például egy hitelesítési hiba és egy hibás végponthiba.

4. Ha az SDK **helyreállítható hibát**észlel, a megadott újrapróbálkozási házirend nek megfelelően újrapróbálkozik, amíg a megadott időtúlérték el nem telik.  Vegye figyelembe, hogy az SDK **exponenciális back-off és jitter** retry házirend alapértelmezés szerint.
5. Amikor a megadott időjárat lejár, az SDK leáll a csatlakozásvagy a küldés közben. Értesíti a felhasználót.

6. Az SDK lehetővé teszi a felhasználó számára, hogy visszahívást csatoljon a kapcsolat állapotváltozásainak fogadásához.

Az SDK-k három újrapróbálkozási szabályzatot biztosítanak:

* **Exponenciális back-off jitter:** Ez az alapértelmezett újrapróbálkozási házirend általában agresszív az elején, és lassítja az idő múlásával, amíg el nem éri a maximális késleltetést. A kialakítás az [Azure Architecture Center újrapróbálkozási útmutatóján](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)alapul. 

* **Egyéni újrapróbálkozás:** Egyes SDK-nyelvek esetében egyéni újrapróbálkozási szabályzatot tervezhet, amely jobban megfelel a forgatókönyvnek, majd beadhatja azt a RetryPolicy-be. Egyéni újrapróbálkozás nem érhető el a C SDK, és jelenleg nem támogatott a Python SDK. A Python SDK szükség szerint újracsatlakozik.

* **Nincs újrapróbálkozás**: Az újrapróbálkozási házirendet beállíthatja "nincs újrapróbálkozás" beállításra, amely letiltja az újrapróbálkozási logikát. Az SDK megpróbál csatlakozni egyszer, és üzenetet küldeni egyszer, feltéve, hogy a kapcsolat létrejött. Ezt a házirendet általában sávszélességgel vagy költséggel kapcsolatos helyzetekben használják. Ha ezt a lehetőséget választja, a nem küldhető üzenetek elvesznek, és nem visszatéríthetők.

### <a name="retry-policy-apis"></a>Házirend API-k újrapróbálkozása

   | SDK | SetRetryPolicy metódus | A szakpolitika végrehajtása | Végrehajtási útmutató |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Alapértelmezett**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Egyéni:** az elérhető [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) használata<BR>**Nincs újrapróbálkozás:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS implementáció](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Alapértelmezett**: [ExponentialBackoffWithJitter osztály](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Egyéni:** a [RetryPolicy felület megvalósítása](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java megvalósítás](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Alapértelmezett**: [ExponentialBackoff osztály](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Egyéni:** [iRetryPolicy felület megvalósítása](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# megvalósítás](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Csomópont| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Alapértelmezett**: [ExponentialBackoffWithJitter osztály](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Egyéni:** a [RetryPolicy felület megvalósítása](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Nincs újrapróbálkozás:** [NoRetry osztály](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Csomópont megvalósítása](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Egyelőre nem támogatott | Egyelőre nem támogatott | Egyelőre nem támogatott |

A következő kódminták ezt a folyamatot szemléltetik:

#### <a name="net-implementation-guidance"></a>.NET megvalósítási útmutató

A következő kódminta bemutatja, hogyan definiálhatja és állíthatja be az alapértelmezett újrapróbálkozási házirendet:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

A magas CPU-használat elkerülése érdekében az újrapróbálkozások szabályozása, ha a kód sikertelen azonnal. Ha például nincs hálózat vagy útvonal a célhoz. A következő újrapróbálkozás végrehajtásához szükséges minimális idő 1 másodperc.

Ha a szolgáltatás szabályozási hibával válaszol, az újrapróbálkozási házirend más, és nem módosítható nyilvános API-n keresztül:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Az újrapróbálkozási `DefaultOperationTimeoutInMilliseconds`mechanizmus leáll a utána, amely jelenleg 4 percben van beállítva.

#### <a name="other-languages-implementation-guidance"></a>Egyéb nyelvek megvalósítási útmutatója

Más nyelvű kódminták esetén tekintse át a következő végrehajtási dokumentumokat. A tárház olyan mintákat tartalmaz, amelyek bemutatják az újrapróbálkozási házirend API-k használatát.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>További lépések

* [Eszköz- és szolgáltatásoldali SDK-k használata](./iot-hub-devguide-sdks.md)

* [A C IoT eszközoldali SDK-jának használata](./iot-hub-device-sdk-c-intro.md)

* [Fejlesztés korlátozott eszközökhöz](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Fejlesztés mobileszközökhöz](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Az eszköz lebontásának elhárítása – problémamegoldás](iot-hub-troubleshoot-connectivity.md)
