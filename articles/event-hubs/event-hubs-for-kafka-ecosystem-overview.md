---
title: Az Azure Event Hubs a Kafka ökoszisztéma |} Microsoft Docs
description: Áttekintés és Kafka bemutatása engedélyezve van az Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Az Event Hubs a Kafka ökoszisztéma

Az Event Hubs a Kafka ökoszisztéma biztosít egy Kafka végponttal, amely használható a meglévő Kafka alapján alkalmazások saját Kafka fürt futtatása helyett. Az Event Hubs Kafka ökoszisztéma támogatja a [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) és újabb ügyfél-verziónkénti, és együttműködik a meglévő Kafka alkalmazások, beleértve a MirrorMaker. Módosítsa a kapcsolati karakterláncot, és indítsa el az alkalmazások, az Event Hubsban a Kafka protokollt használó származó események streaming.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>Mi a Kafka ökoszisztéma az Event Hubs biztosítja?

Az Event Hubs a Kafka ökoszisztéma fölött, amely Kafka 1.0-s verziójával, és később a olvasása és írása Kafka témakörök bináris kompatibilis Azure Event Hubs egy protokoll head biztosít. Fogalmilag Kafka és Eseményközpontok is majdnem megegyezik: mindkét particionált adatfolyam készült naplók. Az alábbi táblázat a maps fogalmak Kafka és Eseményközpontok között.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka és az Event Hubs fogalmi leképezése

| Kafka koncepció | Event Hubs koncepció|
| --- | --- |
| Fürt | Névtér |
| Témakör | Event Hubs |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka és Eseményközpontok közötti legfőbb különbségek

Amíg [Apache Kafka](https://kafka.apache.org/) olyan szoftver, amely futtathatja, amennyiben úgy dönt, az Event Hubs hasonló Azure Blob Storage egy felhőalapú szolgáltatás. Nem találhatók kiszolgálók vagy hálózatok kezeléséhez és nem brókerek konfigurálásához. Hozzon létre egy névtér, amely a témakörök működés közbeni FQDN-t, és ezután hozzon létre az Event Hubs vagy témakörök az adott névtérben. Az Event Hubs és a névterekkel kapcsolatos további információkért lásd: [Mi az az Event Hubs](event-hubs-what-is-event-hubs.md). Felhő alapú szolgáltatásként a Event Hubs egy egyetlen stabil virtuális IP-cím és a végpontnak használ, így az ügyfelek nem kell tudnia a brókerek vagy fürtön belüli gépek be a számítógépre. 

Az Event Hubs skála hány átviteli egységet által megvásárolt, kimutatás, 1 MB / s átviteli egységenként vagy 1000 esemény érkező másodpercenkénti vezérlik. Alapértelmezés szerint az Event Hubs rendkívül átviteli egységeket a korlát elérésekor a [automatikus megnöveli](event-hubs-auto-inflate.md) szolgáltatás; Ez a funkció is működik az Event Hubs Kafka ökoszisztéma. 

### <a name="security-and-authentication"></a>Biztonság és hitelesítés

Az Azure Event Hubs minden kommunikáció SSL vagy TLS igényel, és megosztott hozzáférési aláírásokkal (SAS) használ a hitelesítéshez. Ezt a követelményt egy Kafka végpont az Event Hubs belül is igaz. Kafka kompatibilisek az Event Hubs SASL egyszerű hitelesítés és SASL SSL, használ a transport security. Az Event Hubs biztonsággal kapcsolatos további információkért lásd: [Event Hubs hitelesítési és biztonsági](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Más Event Hubs szolgáltatásait Kafka

Az Event Hubs a Kafka ökoszisztéma lehetővé teszi egy protokoll írása, és egy másik, olvassa el, hogy az aktuális Kafka gyártók továbbra is a közzététel Kafka keresztül, és az Event Hubs, például az Azure Stream Analytics vagy az Azure Functions olvasók adhat hozzá. Emellett az Event Hubs funkcióinak [rögzítése](event-hubs-capture-overview.md) és [földrajzi vész-helyreállítási](event-hubs-geo-dr.md) is használható az Event Hubs a Kafka ökoszisztéma.

## <a name="features-that-are-not-supported-in-the-preview"></a>A kép nem támogatott funkciók

A nyilvános előzetes verzióhoz az Event hubs Kafka ökoszisztéma integrációra a következő Kafka funkciók nem támogatottak:

*   Az Idempotent készítő
*   Tranzakció
*   Tömörítés
*   Méret megtartási
*   Napló tömörítés
*   Partíciók hozzáadása egy meglévő témakör
*   HTTP Kafka API-támogatás
*   Kafka csatlakozás
*   Kafka adatfolyamok

## <a name="next-steps"></a>További lépések

Ez a cikk előírt Event Hubs bemutatása Kafka ökoszisztéma. További tudnivalókért tekintse meg a következőket:

* [Kafka létrehozása engedélyezve van az Event Hubs](event-hubs-create-kafka-enabled.md)
* [Az Event Hubsban adatfolyam a Kafka alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

 
 

