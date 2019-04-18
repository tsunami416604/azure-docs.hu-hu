---
title: Mi az Azure Event Hubs? – egy Big Data-feldolgozó szolgáltatás |} A Microsoft Docs
description: További tudnivalók az Azure Event Hubsról, amely egy másodpercenként több millió eseményt feldolgozó Big Data streamszolgáltatás.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 80c98bea08018780ba1d439e369ab42343d1e615
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680327"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs – Big data streamelési platform és eseménybetöltő szolgáltatás
Az Azure Event Hubs szolgáltatás a big Data típusú adatstreamelési platform és eseményfeldolgozó szolgáltatás. Képes fogadni és feldolgozni másodpercenként több millió. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók.

A következő esetekben a forgatókönyveket, melyekben használhatja az Event Hubs néhány:

- Rendellenességek észlelése (csalások/kiugró értékek)
- Alkalmazásnaplózás
- Elemzési folyamatok, például kattintássorozatoké
- Élő irányítópultok
- Adatok archiválása
- Tranzakciófeldolgozás
- Felhasználói telemetria feldolgozása
- Eszköztelemetria-stream 

## <a name="why-use-event-hubs"></a>Miért érdemes az Event Hubs platformot használni?

Az adatok csak akkor képviselnek értéket, ha könnyen feldolgozhatók, és időszerű elemzések állíthatók össze az adatforrásokból. Az Event Hubs elosztott stream feldolgozási platformot biztosít alacsony késéssel és zökkenőmentes integrációt, az adatok és analitika belül és kívül a teljes körű big Data típusú adatok folyamatot hozhat létre Azure szolgáltatásokkal.

Az Event Hubs az eseményfolyamatok „bejárati ajtaja”, amelyet gyakran neveznek *eseménybetöltőnek* a megoldásarchitektúrákban. Az eseménybetöltő egy olyan összetevő vagy szolgáltatás, amely az esemény-közzétevők és az eseményfelhasználók közé ékelődve elkülöníti az eseménystream létrehozását az események felhasználásától. Az Event Hubs az eseményfelhasználók eseménykészítőkből elválasztás idő megőrzési pufferrel egységes streamelési platformot biztosít. 

A következő szakaszok az Azure Event Hubs szolgáltatás legfontosabb jellemzőit ismertetik: 

## <a name="fully-managed-paas"></a>Teljes mértékben felügyelt PaaS 

Azure Event Hubs szolgáltatás egy teljes körűen felügyelt Platform--szolgáltatásként (PaaS) a kis konfiguráció vagy a felügyeleti terheket, így Ön az üzleti megoldások. [Az Apache Kafkához készült Event Hubs-ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md) anélkül biztosítja a PaaS Kafka előnyeit, hogy saját fürtöket kellene kezelnie, konfigurálnia vagy futtatnia.

## <a name="support-for-real-time-and-batch-processing"></a>Valós idejű kötegelt feldolgozás támogatása

Valós időben töltheti be, pufferelheti, tárolhatja és dolgozhatja fel a streameket, hogy a gyakorlatban is használható elemzésekhez jusson. Az Event Hubs használ egy [particionált felhasználói modell](event-hubs-features.md#partitions), az adatfolyam folyamat több alkalmazás egyidejűleg engedélyezése és a feldolgozás sebessége, ami lehetővé teszi.

[Rögzítés](event-hubs-capture-overview.md) a közel valós időben az adatok egy [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) vagy [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) a hosszú távú megőrzés vagy irányuló mikrokötegelt feldolgozást. Ez a viselkedés használhat valós idejű elemzési származtatás ugyanazon streamben érheti el. Az esemény adatok rögzítése, beállítania gyors. Nincsenek a futtatáshoz felügyeleti költségek, és automatikusan átméreteződik az Event Hubs [átviteli egységek](event-hubs-features.md#throughput-units). Az Event Hubs lehetővé teszi fókusz adatfeldolgozási helyett az adatváltozások rögzítése.

Az Azure Event Hubs az [Azure Functions](/azure/azure-functions/) szolgáltatással is integrálható egy kiszolgáló nélküli architektúra keretein belül.

## <a name="scalable"></a>Méretezhető 

Az Event Hubs szolgáltatással megabájtokban kezdheti az adatstreameket, és onnan továbbléphet a giga- és terabájtokra. Az [automatikus feltöltési](event-hubs-auto-inflate.md) funkció az egyik lehetőség az átviteli egységek számának a használati igényeinek megfelelő módosítására. 

## <a name="rich-ecosystem"></a>Gazdag ökoszisztéma

[Az Event Hubs, az Apache Kafka-ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md) lehetővé teszi, hogy [Apache Kafka (1.0-ás és újabb verziók)](https://kafka.apache.org/) ügyfelek és alkalmazások felvenni a kapcsolatot az Event hubs szolgáltatásba. Nem kell beállítani, konfigurálása és a saját Kafka-fürtök kezelése.
 
A különböző [nyelveken (.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs) elérhető sokoldalú ökoszisztéma megkönnyíti a streamek feldolgozását az Event Hubsból. Az összes támogatott ügyfélnyelv alacsony szintű integrációt biztosít. Az ökoszisztéma emellett lehetőséget biztosít az Azure Stream Analytics és az Azure Functions Azure-szolgáltatásokkal való zökkenőmentes integrációt, és így lehetővé teszi, hogy a kiszolgáló nélküli architektúrákat hozhat létre.

## <a name="key-architecture-components"></a>Az architektúra legfontosabb összetevői
Az Event Hubs az alábbi [fő összetevőket](event-hubs-features.md) foglalja magába:

- **Eseménykészítőkből**: Minden entitás, amely adatokat küld egy eseményközpontnak. Az esemény-közzétevők a HTTPS, az AMQP 1.0 vagy az Apache Kafka (1.0 és újabb) használatával tehetik közzé az eseményeket.
- **A partíciók**: Mindegyik felhasználó csak egy meghatározott részhalmazát, azaz partícióját az üzenetet olvassa be.
- **Fogyasztói csoportok**: Nézete (állapot, pozíció vagy eltolás) teljes eseményközpont. Fogyasztói csoportok engedélyezése felhasználó alkalmazás az eseménystream külön nézetével rendelkezik. A stream egymástól függetlenül saját tempójában és saját eltolásával beolvasni azokat.
- **Átviteli egységek**: Előre megvásárolt kapacitásegységek, amely az Event Hubs átviteli kapacitásának szabályozása.
- **Esemény fogadók**: Minden entitás, amely eseményadatokat olvas egy eseményközpontból. Minden Event Hubs-felhasználó az AMQP 1.0-munkameneten keresztül csatlakozik. Az Event Hubs szolgáltatás kézbesíti az elérhetővé váló programkedvezményekről egy munkamenet-n keresztül. Minden Kafka-fogyasztó a Kafka-protokoll 1.0-s vagy újabb verziójával csatlakozik.

A következő ábrán az Event Hubs streamfeldolgozási architektúrája látható:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>További lépések

Event Hubs használatának első lépései, tekintse meg a **események küldéséhez és fogadásához** oktatóanyagok: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (csak küldése)](event-hubs-c-getstarted-send.md)
- [Az Apache Storm (csak reecive)](event-hubs-storm-getstarted-receive.md)

Az Event Hubsszal kapcsolatos további információkért olvassa el a következő cikkeket:

- [Az Event Hubs-szolgáltatások áttekintése](event-hubs-features.md)
- [Gyakori kérdések](event-hubs-faq.md)


