---
title: Az Azure Event Hubs-beli Apache kafka |} A Microsoft Docs
description: Áttekintés és Kafka bemutatása az Azure Event Hubs engedélyezve
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 11/07/2018
ms.author: bahariri
ms.openlocfilehash: 7f452fe9797e688dc05b644c003117461b4f8bb9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277494"
---
# <a name="azure-event-hubs-for-apache-kafka"></a>Az Azure Event Hubs-beli Apache kafka 

Az Event Hubs egy Kafka-végpontot, amely segítségével biztosít a meglévő által a Kafka-alapú alkalmazások futtatása a saját Kafka-fürt helyett. Az Event Hubs támogatja [Apache Kafka protokoll 1.0-s és újabb verziók](https://kafka.apache.org/documentation/), és együttműködik a meglévő Kafka-alkalmazásokkal, beleértve a MirrorMaker. 

## <a name="what-does-event-hubs-for-kafka-provide"></a>Mit nyújt a Kafka az Event Hubs?

Az Event Hubs, Kafka szolgáltatáshoz biztosít egy protokoll a fő Azure Event Hubs, Kafka 1.0-s verziójával, és később a olvasása és írása a Kafka-témakörökhöz bináris kompatibilis felett. Nincs kód megváltoztatására, de a minimális konfigurációs változást előfordulhat, hogy a Kafka-végpont az alkalmazások használatához. Frissítse a kapcsolati karakterláncot a konfigurációk, a Kafka-végpontra mutat a Kafka-fürt helyett az eseményközpont által elérhetővé tett mutasson. Ezt követően megkezdheti az Event hubsba a Kafka-protokollt használó alkalmazások az események streamelése. Ez az integráció is keretrendszereket támogat, mint [Kafka csatlakozás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), amely jelenleg előzetes verzióban érhető el. 

Elméleti szinten a Kafka és az Event Hubs majdnem azonos: mindkét particionált naplók tervezve a folyamatos átviteli adatok. Az alábbi táblázat a fogalmak közötti Kafka és az Event Hubs képezi le.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>A Kafka és az Eseményközpont elméleti leképezését

| A Kafka fogalom | Event Hubs fogalma|
| --- | --- |
| Fürt | Névtér |
| Témakör | Event Hubs |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-kafka-and-event-hubs"></a>A Kafka és az Event Hubs közötti fő különbségeket

Miközben [Apache Kafka](https://kafka.apache.org/) szoftver, amely is futtathatja, bárhol is választja, hasonló az Azure Blob Storage egy felhőalapú szolgáltatás Azure Event Hubs szolgáltatás. Nincsenek kiszolgálók vagy hálózatok kezelése és konfigurálása nélkül közvetítők. Hozzon létre egy névteret, amely egy teljes Tartománynevet, amelyben a témakörök élő, és hozzon létre az Event Hubs vagy az adott névtérben témakörök. Az Event Hubs és a névterek kapcsolatos további információkért lásd: [Event Hubs-szolgáltatások](event-hubs-features.md#namespace). Felhőalapú szolgáltatás, az Event Hubs egyetlen stabil virtuális IP-címet használ végpontként, így az ügyfelek nem kell tudnia a közvetítők vagy fürtön belüli gépek. 

Az Event Hubs méretezhető szabályozott szerint hány átviteli egységet vásárol, az egyes kapacitásegységek, amely feljogosítja arra, 1 MB / másodperc, vagy 1000 esemény szolgálhat. Alapértelmezés szerint az Event Hubs méretezhető átviteli egységek Ha egyenlege eléri a korlátot, az a [automatikus feltöltésről](event-hubs-auto-inflate.md) funkció; Ez a funkció is működik az Event Hubs Kafka szolgáltatáshoz. 

### <a name="security-and-authentication"></a>Biztonság és hitelesítés

Az Azure Event Hubs minden kommunikáció SSL vagy TLS igényel, és a közös hozzáférésű Jogosultságkódok (SAS) használja a hitelesítéshez. Ez a követelmény egy Kafka-végponton, az Event Hubs belül is igaz. Kompatibilitás érdekében a Kafka az Event Hubs használ a SASL egyszerű a hitelesítéshez és az SASL SSL átviteli biztonsághoz. Az Event Hubs biztonságával kapcsolatos további információkért lásd: [az Event Hubs hitelesítési és biztonsági](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Más Kafka elérhető Event Hubs-szolgáltatásokról

Az Event Hubs, Kafka funkció lehetővé teszi egy protokoll írása, és a egy másik, olvassa el, hogy az aktuális Kafka gyártók továbbra is a közzététel a Kafka-n keresztül, és az Event hubs szolgáltatással, például az Azure Stream Analytics vagy az Azure Functions olvasók is hozzáadhat. Emellett az Event Hubs funkciói, például [rögzítése](event-hubs-capture-overview.md) és [Geo-vészhelyreállítás](event-hubs-geo-dr.md) együttműködnek az Event Hubs, Kafka szolgáltatáshoz.

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

## <a name="next-steps"></a>További lépések

Ez a cikk az Event Hubs bemutatása Kafka biztosítunk. További információkért lásd az alábbi hivatkozásokat:

- [A Kafka létrehozása az Event Hubs engedélyezve](event-hubs-create-kafka-enabled.md)
- [Stream az Event Hubsba, a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Egy Kafka-közvetítő Kafka-kompatibilis eseményközpont tükrözéséhez.](event-hubs-kafka-mirror-maker-tutorial.md)
- [Csatlakozás az Apache Spark egy Kafka-kompatibilis event hubs](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakozni egy Kafka-kompatibilis eseményközpont](event-hubs-kafka-flink-tutorial.md)
- [Egy Kafka-kompatibilis eseményközpont Kafka csatlakozás integrálása](event-hubs-kafka-connect-tutorial.md)
- [Egy Kafka-kompatibilis eseményközpont Akka Streamek csatlakozni](event-hubs-kafka-akka-streams-tutorial.md)
- [Minták megtekintése a githubon](https://github.com/Azure/azure-event-hubs-for-kafka)

 
 

