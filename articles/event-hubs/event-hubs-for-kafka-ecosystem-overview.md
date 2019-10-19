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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d3271d6e8cb7d423e1dccd235b244688e7ab5683
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555789"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Az Azure Event Hubs használata Apache Kafka alkalmazásokból
A Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Kafka-alapú alkalmazásai használhatnak a saját Kafka-fürt futtatására. Event Hubs támogatja a [1,0-es és újabb verziójú Apache Kafka protokollt](https://kafka.apache.org/documentation/), és együttműködik a meglévő Kafka-alkalmazásokkal, beleértve a MirrorMaker is.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Mire szolgál a Kafka Event Hubs?

A Kafka-szolgáltatás Event Hubsja az Azure Event Hubs-ra épülő protokoll-fejlécet biztosít, amely a Kafka 1,0-as és újabb verzióival kompatibilis, és a Kafka-témakörök olvasására és írására is alkalmas. Előfordulhat, hogy a Kafka-végpontot a kód módosítása nélkül, de minimális konfigurációs módosítással szeretné használni az alkalmazásaiban. A konfigurációk kapcsolati sztringjét úgy frissítheti, hogy az Event hub által közzétett Kafka-végpontra mutasson, ahelyett, hogy a Kafka-fürtre mutat. Ezután elindíthatja a folyamatos átviteli eseményeket a Kafka protokollt használó alkalmazásokból Event Hubsba. Ez az integráció olyan keretrendszereket is támogat, mint a [Kafka-csatlakozás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), amely jelenleg előzetes verzióban érhető el. 

Elméletileg Kafka és Event Hubs közel azonosak: a folyamatos adatátvitelhez készült particionált naplók is. A következő táblázat a Kafka és a Event Hubs közötti fogalmakat térképezi fel.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>A Kafka és az Event hub fogalmi leképezése

| Kafka-koncepció | Event Hubs koncepció|
| --- | --- |
| Fürt | Névtér |
| Témakör | Event Hubs-eseményközpontok |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-kafka-and-event-hubs"></a>A Kafka és a Event Hubs közötti fő különbségek

A [Apache Kafka](https://kafka.apache.org/) egy szoftver, amelyet bárhol kiválasztva futtathat, Event Hubs az Azure Blob Storagehoz hasonló felhőalapú szolgáltatás. Nincsenek felügyelhető kiszolgálók vagy hálózatok, és nincsenek konfigurálható brókerek. Létre kell hoznia egy névteret, amely egy teljes tartománynevet tartalmaz, amelyben a témakörök életbe kerülnek, majd létre kell hozni Event Hubs vagy témákat a névtéren belül. További információ a Event Hubs és a névterekről: [Event Hubs szolgáltatások](event-hubs-features.md#namespace). Felhőalapú szolgáltatásként Event Hubs a végpontként egyetlen stabil virtuális IP-címet használ, így az ügyfeleknek nem kell tudniuk a fürtön belüli közvetítőket vagy gépeket. 

A Event Hubs méretezését a megvásárolt átviteli egységek száma vezérli, az egyes átviteli egységek pedig másodpercenként 1 MB-onként vagy 1000 esemény másodpercenként. Alapértelmezés szerint a Event Hubs az átviteli egységeket méretezi, amikor eléri a korlátot az [automatikus](event-hubs-auto-inflate.md) feltöltési funkcióval. Ez a funkció a Kafka szolgáltatás Event Hubs is működik. 

### <a name="security-and-authentication"></a>Biztonság és hitelesítés

Az Azure Event Hubs az SSL vagy a TLS használatát igényli minden kommunikációhoz, és megosztott hozzáférési aláírásokat (SAS) használ a hitelesítéshez. Ez a követelmény az Event Hubson belüli Kafka-végpont esetén is igaz. A Kafka-vel való kompatibilitás érdekében a Event Hubs a hitelesítéshez és a SASL SSL-t SASL PLAIN használ a szállítás biztonsága érdekében. A Event Hubs biztonságával kapcsolatos további információkért lásd: [Event Hubs hitelesítés és biztonság](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>A Kafka számára elérhető egyéb Event Hubs funkciók

A Kafka funkció Event Hubs lehetővé teszi, hogy egy protokollal írjon, és beolvassa azt egy másikkal, hogy a jelenlegi Kafka-termelők továbbra is a Kafka használatával tudjanak közzétenni, és az olvasókat felveheti Event Hubs, például Azure Stream Analytics vagy Azure Functions. Emellett Event Hubs funkciók, mint például a [Capture](event-hubs-capture-overview.md) és a [geo-alapú katasztrófa-helyreállítás](event-hubs-geo-dr.md) is együttműködik a Kafka-szolgáltatás Event Hubsával.

## <a name="features-that-are-not-yet-supported"></a>A még nem támogatott szolgáltatások 

Itt látható a még nem támogatott Kafka-funkciók listája:

*   Idempotens gyártó
*   tranzakció
*   Tömörítés
*   Méret-alapú megőrzés
*   Naplózási tömörítés
*   Partíciók hozzáadása egy meglévő témakörhöz
*   HTTP Kafka API-támogatás
*   Kafka streamek

## <a name="next-steps"></a>Következő lépések

Ez a cikk a Kafka-Event Hubs bevezetését ismertette. További információért lásd az alábbi hivatkozásokat:

- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)


