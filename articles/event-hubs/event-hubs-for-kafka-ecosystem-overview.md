---
title: Használja az eseményközpont alkalmazásból Apache kafka platformmal – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk az Azure Event Hubs által az Apache Kafka támogatási információkat biztosít.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: fc81226e754178ad0edfff96a494dd7522662261
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395440"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Az Azure Event Hubs használata az Apache Kafka-alkalmazásokból
Az Event Hubs egy Kafka-végpontot, amely segítségével biztosít a meglévő által a Kafka-alapú alkalmazások futtatása a saját Kafka-fürt helyett. Event Hubs támogatja a [1,0-es és újabb verziójú Apache Kafka protokollt](https://kafka.apache.org/documentation/), és együttműködik a meglévő Kafka-alkalmazásokkal, beleértve a MirrorMaker is.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Mit nyújt a Kafka az Event Hubs?

Az Event Hubs, Kafka szolgáltatáshoz biztosít egy protokoll a fő Azure Event Hubs, Kafka 1.0-s verziójával, és később a olvasása és írása a Kafka-témakörökhöz bináris kompatibilis felett. Nincs kód megváltoztatására, de a minimális konfigurációs változást előfordulhat, hogy a Kafka-végpont az alkalmazások használatához. Frissítse a kapcsolati karakterláncot a konfigurációk, a Kafka-végpontra mutat a Kafka-fürt helyett az eseményközpont által elérhetővé tett mutasson. Ezt követően megkezdheti az Event hubsba a Kafka-protokollt használó alkalmazások az események streamelése. Ez az integráció olyan keretrendszereket is támogat, mint a [Kafka-csatlakozás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), amely jelenleg előzetes verzióban érhető el. 

Elméletileg Kafka és Event Hubs közel azonosak: ezek mind a folyamatos átviteli adattovábbításra épülő particionált naplók. Az alábbi táblázat a fogalmak közötti Kafka és az Event Hubs képezi le.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>A Kafka és az Eseményközpont elméleti leképezését

| A Kafka fogalom | Event Hubs fogalma|
| --- | --- |
| Fürt | Névtér |
| Témakör | Eseményközpont |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-kafka-and-event-hubs"></a>A Kafka és az Event Hubs közötti fő különbségeket

A [Apache Kafka](https://kafka.apache.org/) egy szoftver, amelyet bárhol kiválasztva futtathat, Event Hubs az Azure Blob Storagehoz hasonló felhőalapú szolgáltatás. Nincsenek kiszolgálók vagy hálózatok kezelése és konfigurálása nélkül közvetítők. Hozzon létre egy névteret, amely egy teljes Tartománynevet, amelyben a témakörök élő, és hozzon létre az Event Hubs vagy az adott névtérben témakörök. További információ a Event Hubs és a névterekről: [Event Hubs szolgáltatások](event-hubs-features.md#namespace). Felhőalapú szolgáltatásként Event Hubs a végpontként egyetlen stabil virtuális IP-címet használ, így az ügyfeleknek nem kell tudniuk a fürtön belüli közvetítőket vagy gépeket. 

Az Event Hubs méretezhető szabályozott szerint hány átviteli egységet vásárol, az egyes kapacitásegységek, amely feljogosítja arra, 1 MB / másodperc, vagy 1000 esemény szolgálhat. Alapértelmezés szerint a Event Hubs az átviteli egységeket méretezi, amikor eléri a korlátot az [automatikus](event-hubs-auto-inflate.md) feltöltési funkcióval. Ez a funkció a Kafka szolgáltatás Event Hubs is működik. 

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

#### <a name="shared-access-signature-sas"></a>Közös hozzáférésű aláírás (SAS)
A Event Hubs a **közös hozzáférésű aláírásokat (SAS)** is biztosítja a Event Hubs számára a Kafka-erőforrásokhoz való delegált hozzáféréshez. Az OAuth 2,0 token-alapú mechanizmus használatával történő hozzáférés engedélyezése a biztonsági és a könnyű használatot lehetővé teszi az SAS használatával. A beépített szerepkörök kihasználhatják az ACL-alapú hitelesítés szükségességét is, amelyet a felhasználónak kell karbantartani és felügyelni. Ezt a funkciót a Kafka-ügyfelekkel úgy használhatja, hogy megadta **SASL_SSL** a protokollhoz, és **egyszerű** a mechanizmus számára. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Példák 
**A Kafka** -kompatibilis Event hub létrehozásához és Sas-vagy OAuth-alapú eléréséhez szükséges részletes utasításokért lásd: gyors üzembe helyezés [Event Hubs a Kafka protokoll használatával](event-hubs-quickstart-kafka-enabled-event-hubs.md).

A OAuth és az Event Hubs for Kafka használatát bemutató **példákért** lásd: [minták a githubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Más Kafka elérhető Event Hubs-szolgáltatásokról

Az Event Hubs, Kafka funkció lehetővé teszi egy protokoll írása, és a egy másik, olvassa el, hogy az aktuális Kafka gyártók továbbra is a közzététel a Kafka-n keresztül, és az Event hubs szolgáltatással, például az Azure Stream Analytics vagy az Azure Functions olvasók is hozzáadhat. Emellett Event Hubs funkciók, mint például a [Capture](event-hubs-capture-overview.md) és a [geo-alapú katasztrófa-helyreállítás](event-hubs-geo-dr.md) is együttműködik a Kafka-szolgáltatás Event Hubsával.

## <a name="features-that-are-not-yet-supported"></a>Még nem támogatott funkciók 

Itt látható a listában, a Kafka-funkciók még nem támogatottak:

*   Idempotens producer
*   Tranzakció
*   Tömörítés
*   Méret-alapú megőrzése
*   Tömörítési naplózása
*   A partíciók hozzáadása egy meglévő témakörhöz
*   HTTP Kafka API támogatása
*   A Kafka Streams

## <a name="next-steps"></a>Következő lépések

Ez a cikk az Event Hubs bemutatása Kafka biztosítunk. További információkért lásd az alábbi hivatkozásokat:

- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)


