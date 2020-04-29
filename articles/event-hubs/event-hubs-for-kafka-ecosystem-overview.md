---
title: Az Event hub használata Apache Kafka alkalmazásból – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs Apache Kafka támogatásáról nyújt információt.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 91e2d70bab8c1be4b3e5b400ce21122eccb1e9eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811376"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Az Azure Event Hubs használata Apache Kafka alkalmazásokból
A Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Kafka-alapú alkalmazásai használhatnak a saját Kafka-fürt futtatására. Event Hubs támogatja a [1,0-es és újabb verziójú Apache Kafka protokollt](https://kafka.apache.org/documentation/), és együttműködik a meglévő Kafka-alkalmazásokkal, beleértve a MirrorMaker is.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Mire szolgál a Kafka Event Hubs?

A Kafka-szolgáltatás Event Hubsja az Azure Event Hubs-ra épülő protokoll-fejlécet biztosít, amely a Kafka 1,0-as és újabb verzióival kompatibilis, és a Kafka-témakörök olvasására és írására is alkalmas. Előfordulhat, hogy a Kafka-végpontot a kód módosítása nélkül, de minimális konfigurációs módosítással szeretné használni az alkalmazásaiban. A konfigurációk kapcsolati sztringjét úgy frissítheti, hogy az Event hub által közzétett Kafka-végpontra mutasson, ahelyett, hogy a Kafka-fürtre mutat. Ezután elindíthatja a folyamatos átviteli eseményeket a Kafka protokollt használó alkalmazásokból Event Hubsba. Ez az integráció olyan keretrendszereket is támogat, mint a [Kafka-csatlakozás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), amely jelenleg előzetes verzióban érhető el. 

Elméletileg Kafka és Event Hubs közel azonosak: ezek mind a folyamatos átviteli adattovábbításra épülő particionált naplók. A következő táblázat a Kafka és a Event Hubs közötti fogalmakat térképezi fel.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>A Kafka és az Event hub fogalmi leképezése

| Kafka-koncepció | Event Hubs koncepció|
| --- | --- |
| Fürt | Névtér |
| Témakör | Eseményközpont |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-kafka-and-event-hubs"></a>A Kafka és a Event Hubs közötti fő különbségek

A [Apache Kafka](https://kafka.apache.org/) egy szoftver, amelyet bárhol kiválasztva futtathat, Event Hubs az Azure Blob Storagehoz hasonló felhőalapú szolgáltatás. Nincsenek felügyelhető kiszolgálók vagy hálózatok, és nincsenek konfigurálható brókerek. Létre kell hoznia egy névteret, amely egy teljes tartománynevet tartalmaz, amelyben a témakörök életbe kerülnek, majd létre kell hozni Event Hubs vagy témákat a névtéren belül. További információ a Event Hubs és a névterekről: [Event Hubs szolgáltatások](event-hubs-features.md#namespace). Felhőalapú szolgáltatásként Event Hubs a végpontként egyetlen stabil virtuális IP-címet használ, így az ügyfeleknek nem kell tudniuk a fürtön belüli közvetítőket vagy gépeket. 

A Event Hubs méretezését a megvásárolt átviteli egységek száma vezérli, az egyes átviteli egységek pedig másodpercenként 1 MB-onként vagy 1000 esemény másodpercenként. Alapértelmezés szerint a Event Hubs az átviteli egységeket méretezi, amikor eléri a korlátot az [automatikus](event-hubs-auto-inflate.md) feltöltési funkcióval. Ez a funkció a Kafka szolgáltatás Event Hubs is működik. 

### <a name="security-and-authentication"></a>Biztonság és hitelesítés
Minden alkalommal, amikor eseményeket tesz közzé vagy használ fel egy Event Hubs a Kafka számára, az ügyfél megpróbál hozzáférni a Event Hubs erőforrásokhoz. Biztosítani szeretné, hogy az erőforrások elérhetők legyenek egy jogosult entitás használatával. Ha Apache Kafka protokollt használ az ügyfelekkel, beállíthatja a konfigurációt a hitelesítéshez és a titkosításhoz a SASL mechanizmusok használatával. A Event Hubs for Kafka használatához TLS-titkosításra van szükség (mivel az Event Hubs TLS-alapú adatforgalom titkosítva van). Ezt megteheti a konfigurációs fájlban lévő SASL_SSL beállítás megadásával. 

Az Azure Event Hubs több lehetőséget kínál a biztonságos erőforrásokhoz való hozzáférés engedélyezésére. 

- OAuth
- Közös hozzáférésű jogosultságkód (SAS)

#### <a name="oauth"></a>OAuth
A Event Hubs a Azure Active Directory (Azure AD) szolgáltatással integrálódik, amely egy **OAuth** 2,0-kompatibilis központi engedélyezési kiszolgálót biztosít. Az Azure AD-vel szerepköralapú hozzáférés-vezérlés (RBAC) segítségével részletesen megadhatja az ügyfelek identitásait. Ezt a funkciót a Kafka-ügyfelekkel a mechanizmus protokoll-és **OAUTHBEARER** **SASL_SSL** megadásával veheti igénybe. További információk a RBAC szerepköreiről és szintjeiről: hozzáférés [engedélyezése az Azure ad-vel](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Közös hozzáférésű jogosultságkód (SAS)
A Event Hubs a **közös hozzáférésű aláírásokat (SAS)** is biztosítja a Event Hubs számára a Kafka-erőforrásokhoz való delegált hozzáféréshez. Az OAuth 2,0 token-alapú mechanizmus használatával történő hozzáférés engedélyezése a biztonsági és a könnyű használatot lehetővé teszi az SAS használatával. A beépített szerepkörök kihasználhatják az ACL-alapú hitelesítés szükségességét is, amelyet a felhasználónak kell karbantartani és felügyelni. Ezt a funkciót a Kafka-ügyfelekkel úgy használhatja, hogy megadta **SASL_SSL** a protokollhoz, és **egyszerű** a mechanizmus számára. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Példák 
Az Event hub létrehozásával **és a sas** vagy OAuth használatával való elérésével kapcsolatos részletes utasításokért lásd: gyors útmutató [: adatstream Event Hubs a Kafka protokoll használatával](event-hubs-quickstart-kafka-enabled-event-hubs.md).

A OAuth és az Event Hubs for Kafka használatát bemutató **példákért** lásd: [minták a githubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>A Kafka számára elérhető egyéb Event Hubs funkciók

A Kafka funkció Event Hubs lehetővé teszi, hogy egy protokollal írjon, és beolvassa azt egy másikkal, hogy a jelenlegi Kafka-termelők továbbra is a Kafka használatával tudjanak közzétenni, és az olvasókat felveheti Event Hubs, például Azure Stream Analytics vagy Azure Functions. Emellett Event Hubs funkciók, mint például a [Capture](event-hubs-capture-overview.md) és a [geo-alapú katasztrófa-helyreállítás](event-hubs-geo-dr.md) is együttműködik a Kafka-szolgáltatás Event Hubsával.

## <a name="features-that-are-not-yet-supported"></a>A még nem támogatott szolgáltatások 

Itt látható a még nem támogatott Kafka-funkciók listája:

*   Idempotens gyártó
*   Tranzakció
*   Tömörítés
*   Méret-alapú megőrzés
*   Naplózási tömörítés
*   Partíciók hozzáadása egy meglévő témakörhöz
*   HTTP Kafka API-támogatás
*   Kafka streamek

## <a name="next-steps"></a>További lépések
Ez a cikk a Kafka-Event Hubs bevezetését ismertette. További információ: [Apache Kafka fejlesztői útmutató az Azure Event Hubshoz](apache-kafka-developer-guide.md).


