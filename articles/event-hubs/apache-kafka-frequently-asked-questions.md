---
title: Gyakori kérdések – Azure Event Hubs a Apache Kafka
description: Ez a cikk bemutatja, hogy a különböző protokollokat (AMQP, Apache Kafka és HTTPS) használó felhasználók és gyártók Hogyan válthatnak be eseményeket az Azure Event Hubs használatakor.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8bdd86d9f299a69d5f2d05bb8ec526ed94780608
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031683"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Gyakori kérdések – Event Hubs Apache Kafka 
Ez a cikk a Apache Kafka Event Hubs való áttelepítésével kapcsolatos gyakori kérdésekre adott válaszokat tartalmaz.

## <a name="do-you-run-apache-kafka"></a>Apache Kafka futtat?

Nem.  A Kafka API-műveleteket a Event Hubs infrastruktúrán hajtjuk végre.  Mivel szoros összefüggés van Apache Kafka és Event Hubs AMQP funkció (azaz a létrehozás, a fogadás, a felügyelet stb.) között, képesek vagyunk az Event Hubs megbízhatóságát a Kafka Péter területére.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs fogyasztói csoport és a Kafka fogyasztói csoport
Mi a különbség az Event hub fogyasztói csoport és a Kafka fogyasztói csoport között Event Hubs? A Event Hubs Kafka fogyasztói csoportjai teljesen különböznek a standard Event Hubs fogyasztói csoportoktól.

**Event Hubs fogyasztói csoportok**

- Ezek a létrehozási, lekérdezési, frissítési és törlési (szifilisz) műveletekkel kezelhetők a portálon, az SDK-ban vagy a Azure Resource Manager-sablonokon keresztül. Event Hubs fogyasztói csoportok nem hozhatók létre.
- Az Event hub gyermek entitásai. Ez azt jelenti, hogy ugyanaz a fogyasztói csoport neve is felhasználható ugyanabban a névtérben lévő Event hubok között, mert ezek különálló entitások.
- Nem használhatók eltolások tárolására. A AMQP-használat a külső Eltolásos tárolással történik, például az Azure Storage-ban.

**Kafka fogyasztói csoportok**

- Ezek önmagukban jönnek létre.  A Kafka-csoportok a Kafka fogyasztói csoport API-jai segítségével kezelhetők.
- Az eltolásokat a Event Hubs szolgáltatásban tárolhatják.
- Ezek kulcsként használatosak a mi valójában egy Eltolásos kulcs-érték tároló. A és a rendszer egyedi párjaként `group.id` `topic-partition` egy eltolást tárolunk az Azure Storage-ban (3x-os replikálás). Event Hubs a felhasználók nem számítanak fel további tárolási költséget a Kafka-eltolások tárolásához. Az eltolások a Kafka fogyasztói csoport API-manipulálható keresztül érhetők el, de az Eltolásos tárolási *fiókok* nem közvetlenül láthatók vagy manipulálható az Event hub felhasználói számára.  
- Egy névteret ölelnek fel. Ha ugyanazt a Kafka-csoportnevet használja több alkalmazáshoz több témakörben, az azt jelenti, hogy minden alkalmazás és a Kafka-ügyfél újra lesz egyenlítve, amikor csak egyetlen alkalmazásnak kell kiegyensúlyoznia.  Adja meg a csoportok nevét okosan.
- Teljesen eltérnek Event Hubs fogyasztói csoportoktól. **Nincs** szükség a (z) "$default" használatára, és nem kell aggódnia, hogy a Kafka-ügyfelek AMQP-munkaterhelésekkel ütköznek.
- Nem láthatók a Azure Portalban. A fogyasztói csoport adatai a Kafka API-kon keresztül érhetők el.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Kafka Event Hubséről és Event Hubsról, tekintse meg a következő cikkeket:  

- [Apache Kafka fejlesztői útmutató Event Hubs](apache-kafka-developer-guide.md)
- [A Event Hubs Apache Kafka áttelepítési útmutatója](apache-kafka-migration-guide.md)
- [Event Hubs Apache Kafka hibaelhárítási útmutatója](apache-kafka-troubleshooting-guide.md)
- [Ajánlott konfigurációk](apache-kafka-configurations.md)

