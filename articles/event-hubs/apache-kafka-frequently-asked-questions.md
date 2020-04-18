---
title: Gyakori kérdések – Azure Event Hubs for Apache Kafka
description: Ez a cikk bemutatja, hogy a különböző protokollokat (AMQP, Apache Kafka és HTTPS) használó felhasználók és gyártók hogyan cserélhetnek eseményeket az Azure Event Hubs használatakor.
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
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606742"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Gyakori kérdések – Az Apache Kafka eseményközpontjai 
Ez a cikk választ ad az Apache Kafka eseményközpontjaiba való áttelepítéssel kapcsolatos gyakori kérdésekre.

## <a name="do-you-run-apache-kafka"></a>Az Apache Kafkát futtatod?

Nem.  Kafka API-műveleteket hajtunk végre az Event Hubs-infrastruktúrán.  Mivel szoros korreláció van az Apache Kafka és az Event Hubs AMQP funkciói között (azaz gyártani, fogadni, felügyelet, így tovább), képesek vagyunk az Event Hubs ismert megbízhatóságát a Kafka PaaS-területre hozni.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs fogyasztói csoport vs. Kafka fogyasztói csoport
Mi a különbség az Event Hub-fogyasztói csoport és az Event Hubs Kafka-fogyasztói csoport között? Az Event Hubs kafka fogyasztói csoportjai teljes mértékben különböznek a szabványos Event Hubs fogyasztói csoportoktól.

**Eseményközpontok fogyasztói csoportjai**

- Ezek kezelése létrehozása, beolvasása, frissítése és törlése (CRUD) műveletek portálon keresztül, SDK vagy Az Azure Resource Manager-sablonok. Az Event Hubs fogyasztói csoportok nem hozhatók létre automatikusan.
- Ezek egy eseményközpont gyermekentitásai. Ez azt jelenti, hogy ugyanazt a fogyasztói csoport nevet lehet újra felhasználni az eseményközpontok között ugyanabban a névtérben, mert különálló entitások.
- Nem használhatók eltolások tárolására. Az amqp-felhasználás külső ofszettárolóval, például az Azure Storage-tárolással történik.

**Kafka fogyasztói csoportok**

- Automatikusan létrehozták őket.  A Kafka csoportok a Kafka fogyasztói csoport API-kon keresztül kezelhetők.
- Az Event Hubs szolgáltatásban tárolhatják az ellentámadásokat.
- Ezek kulcsként használatosak egy tosztási kulcs-érték tárolóban. Egy egyedi `group.id` pár, `topic-partition`és , tárolunk egy eltolás t az Azure Storage (3x replikáció). Az Event Hubs-felhasználók nem vállalnak többlettárolási költségeket a Kafka-eltolások tárolása miatt. Az eltolások manipulálhatók a Kafka fogyasztói *accounts* csoport API-kon keresztül, de az ofszettár-fiókok közvetlenül nem láthatók vagy manipulálhatók az Event Hub-felhasználók számára.  
- Egy névteret fednek le. Ugyanazt a Kafka-csoportnevet több alkalmazás több témakörben azt jelenti, hogy az összes alkalmazás és a Kafka-ügyfelek lesz kiegyensúlyozva, ha csak egy alkalmazás újraegyensúlyozása.  Bölcsen válaszd ki a csoportneveket.
- Teljesen különböznek az Event Hubs fogyasztói csoportoktól. **Nem** kell használnia a "$Default", és nem kell aggódnia, hogy a Kafka-ügyfelek zavarják az AMQP-számítási feladatokat.
- Nem láthatók az Azure Portalon. A fogyasztói csoport adatai a Kafka API-kon keresztül érhetők el.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Kafka eseményközpontjairól és eseményközpontjairól, olvassa el az alábbi cikkeket:  

- [Az Apache Kafka fejlesztői útmutatója az Event Hubs-hoz](apache-kafka-developer-guide.md)
- [Apache Kafka áttelepítési útmutató az Eseményközpontokhoz](apache-kafka-migration-guide.md)
- [Az Apache Kafka hibaelhárítási útmutatója az Event Hubs-hoz](apache-kafka-troubleshooting-guide.md)
- [Ajánlott konfigurációk](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

