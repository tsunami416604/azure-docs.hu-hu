---
title: Az Apache Kafka Azure Event Hubs szolgáltatással kapcsolatos problémák elhárítása
description: Ez a cikk bemutatja, hogyan háríthatók el az Apache Kafka Azure Event Hubs szolgáltatással kapcsolatos problémák
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606729"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Az Apache Kafka hibaelhárítási útmutatója az Event Hubs-hoz
Ez a cikk hibaelhárítási tippeket tartalmaz az okkal kapcsolatban, amelyekkel az Apache Kafka Eseményközpontok használatakor problémák léphetnek fel. 

## <a name="server-busy-exception"></a>Kiszolgáló foglaltsági kivétele
Előfordulhat, hogy a Kafka szabályozása miatt kiszolgáló foglalt sági kivételt kap. AMQP-ügyfelek esetén az Event Hubs azonnal egy **kiszolgáló foglalt** kivételt ad vissza a szolgáltatás szabályozásakor. Ez egyenértékű a "próbálja újra később" üzenetet. A Kafka alkalmazásban az üzenetek elhalasztása a befejezése előtt. A késleltetési hossz t `throttle_time_ms` ezredmásodpercben adja vissza a termék/lehívás válasznak. A legtöbb esetben ezek a késleltetett kérelmek nem naplózva ServerBusy kivételek az Event Hubs irányítópultokon. Ehelyett a válasz `throttle_time_ms` értékét kell használni, mint egy mutató, hogy az átviteli sebesség meghaladta a kiépített kvótát.

Ha a forgalom túlzott, a szolgáltatás a következő viselkedés:

- Ha a kérelem késése meghaladja a kérelem időtúllépését, az Event Hubs **házirend-megsértési** hibakódot ad vissza.
- Ha a lehívási kérelem késleltetése meghaladja a kérelem időtúllépését, az Event Hubs a kérelmet szabályozottként naplózza, és üres rekordkészlettel válaszol, hibakód nélkül.

[A dedikált fürtök](event-hubs-dedicated-overview.md) nem rendelkeznek szabályozási mechanizmusokkal. Szabadon használhatja fel az összes fürterőforrást.

## <a name="no-records-received"></a>Nem érkezett rekord
Előfordulhat, hogy a fogyasztók nem kapnak rekordokat, és folyamatosan kiegyensúlyozzák az egyensúlyt. Ebben a forgatókönyvben a fogyasztók nem kap semmilyen rekordot, és folyamatosan egyensúlyba. Nincs kivétel vagy hiba, ha ez megtörténik, de a Kafka naplók azt mutatják, hogy a fogyasztók megragadt próbál újra csatlakozni a csoporthoz, és partíciók hozzárendelése. Van néhány lehetséges oka:

- Győződjön meg `request.timeout.ms` arról, hogy az Ön legalább az `session.timeout.ms` ajánlott 60000 értéket, és legalább az ajánlott 30000 értéket. Ha ezek a beállítások túl alacsonyak, az fogyasztói időtúlterheléseket okozhat, ami aztán újraegyensúlyozást okozhat (ami több időtúltöltést okoz, ami több újraegyensúlyozást okoz, és így tovább) 
- Ha a konfiguráció megfelel az ajánlott értékeknek, és még mindig folyamatosan egyensúlyoz, nyugodtan nyisson meg egy problémát (győződjön meg róla, hogy a teljes konfigurációt belefoglalja a problémába, hogy segíthessünk a hibakeresésben)!

## <a name="compressionmessage-format-version-issue"></a>A tömörítési/üzenetformátum verziószáma
A Kafka támogatja a tömörítést, a Kafka eseményközpontok pedig jelenleg nem. Az üzenetformátumú verziót megemlítő `The message format version on the broker does not support the request.`hibák (például ) akkor keletkeznek, amikor egy ügyfél tömörített Kafka üzeneteket próbál küldeni brókereinknek.

Ha tömörített adatokra van szükség, az adatok tömörítése a brókereknek való küldés előtt, és a bevételezés utáni kibontás érvényes megoldás. Az üzenet törzse csak egy bájttömb a szolgáltatáshoz, így az ügyféloldali tömörítés/dekompresszió nem okoz problémát.

## <a name="unknownserverexception"></a>IsmeretlenKiszolgálóKivétel
A Kafka ügyfélkódtáraktól a következő höz hasonló UnknownServerException-et kaphat: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Nyisson meg egy jegyet a Microsoft támogatásával.  A hibakeresési szintű naplózás és a kivételidőbélyegek utc-ben hasznosak a probléma hibakeresésében. 

## <a name="other-issues"></a>Egyéb problémák
Ellenőrizze a következő elemeket, ha problémákat lát a Kafka event hubs-on való használatakor.

- **Tűzfal blokkolja a forgalmat** – Győződjön meg arról, hogy a **9093-as** portot nem blokkolja a tűzfal.
- **TopicAuthorizationException** – A kivétel leggyakoribb okai a következők:
    - Elírás a konfigurációs fájl kapcsolati karakterláncában, vagy
    - A Kafka Eseményközpontok használata egy alapszintű szintű névtérben. A Kafka eseményközpontjai [csak a standard és dedikált szintű névterek esetében támogatottak.](https://azure.microsoft.com/pricing/details/event-hubs/)
- **Kafka verzió eltérés** - Event Hubs a Kafka ökoszisztémák támogatja a Kafka 1.0-s és újabb verzióit. Egyes, a Kafka 0.10-es és újabb verzióját használó alkalmazások időnként működhetnek a Kafka protokoll visszamenőleges kompatibilitása miatt, de erősen javasoljuk a régi API-verziók használatát. A Kafka 0.9-es és korábbi verziói nem támogatják a szükséges SASL protokollokat, és nem tudnak csatlakozni az Event Hubs-hoz.
- **Furcsa kódolások AMQP fejlécek fogyasztása kor Kafka** - amikor eseményeket küld egy eseményközpont felett AMQP, minden AMQP hasznos adatfejlécek szerializált AMQP kódolás. A Kafka-fogyasztók nem deszeriálják az AMQP fejléceit. A fejlécértékek olvasásához manuálisan dekódolja az AMQP-fejléceket. Azt is megteheti, hogy elkerüli az AMQP-fejlécek használatát, ha tudja, hogy a Kafka protokollon keresztül fog fogyasztani. További információ: [a GitHub-probléma.](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)
- **SASL-hitelesítés** – A keretrendszer nek az Event Hubs által megkövetelt SASL hitelesítési protokollal való együttműködésének leszerzése nehezebb lehet, mint ami a szemnek megfelel. Tekintse meg, hogy a sasl-hitelesítéssel kapcsolatos erőforrások kal elháríthatja-e a konfigurációt. 

## <a name="limits"></a>Korlátok
Apache Kafka vs. Event Hubs Kafka. A kafkai ökoszisztémák eseményközpontjai nak legtöbb esetben ugyanazok az alapértelmezett beállításai, tulajdonságai, hibakódjai és általános viselkedése, mint az Apache Kafka. Az alábbi esetekben sor kerül a következőkre, ha ez a kettő kifejezetten eltér (vagy ha az Event Hubs olyan korlátot ír elő, amelyet a Kafka nem).

- A `group.id` tulajdonság maximális hossza 256 karakter.
- A maximális `offset.metadata.max.bytes` mérete 1024 bájt
- Az eltolási véglegesítések partíciónként 4 hívás/másodperc sebességgel vannak szabályozva, legfeljebb 1 MB belső naplómérettel


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Kafka eseményközpontjairól és eseményközpontjairól, olvassa el az alábbi cikkeket:  

- [Az Apache Kafka fejlesztői útmutatója az Event Hubs-hoz](apache-kafka-developer-guide.md)
- [Apache Kafka áttelepítési útmutató az Eseményközpontokhoz](apache-kafka-migration-guide.md)
- [Gyakori kérdések – Az Apache Kafka eseményközpontjai](apache-kafka-frequently-asked-questions.md)
- [Ajánlott konfigurációk](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
