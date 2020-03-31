---
title: Mi az Azure Event Hubs? - big data betöltési szolgáltatás | Microsoft dokumentumok
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
ms.openlocfilehash: 9b3af6a6cee4375a110c894b6b72655605ad077d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372241"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs – Big Data streaming platform és eseménybetöltési szolgáltatás
Az Azure Event Hubs egy big data streamelési platform és eseménybetöltési szolgáltatás. Másodpercenként több millió eseményt képes fogadni és feldolgozni. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók.

A következő forgatókönyvek néhány olyan forgatókönyvet, ahol az Event Hubs használható:

- Rendellenességek észlelése (csalások/kiugró értékek)
- Alkalmazásnaplózás
- Elemzési folyamatok, például kattintássorozatoké
- Élő irányítópultok
- Adatok archiválása
- Tranzakciófeldolgozás
- Felhasználói telemetria feldolgozása
- Eszköztelemetria-stream

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Miért érdemes az Event Hubs platformot használni?

Az adatok csak akkor képviselnek értéket, ha könnyen feldolgozhatók, és időszerű elemzések állíthatók össze az adatforrásokból. Az Event Hubs egy elosztott adatfolyam-feldolgozási platformot biztosít alacsony késleltetéssel és zökkenőmentes integrációval, az Azure-on belüli és kívüli adat- és elemzési szolgáltatásokkal a teljes big data-folyamat létrehozásához.

Az Event Hubs az eseményfolyamatok „bejárati ajtaja”, amelyet gyakran neveznek *eseménybetöltőnek* a megoldásarchitektúrákban. Az eseménybetöltő egy olyan összetevő vagy szolgáltatás, amely az esemény-közzétevők és az eseményfelhasználók közé ékelődve elkülöníti az eseménystream létrehozását az események felhasználásától. Az Event Hubs egy egységes streamelési platformot biztosít időmegőrzési pufferrel, és leválasztja az eseménygyártókat az eseményfogyasztóktól.

A következő szakaszok az Azure Event Hubs szolgáltatás legfontosabb jellemzőit ismertetik:

## <a name="fully-managed-paas"></a>Teljes mértékben felügyelt PaaS

Az Event Hubs egy teljes körűen felügyelt platformszolgáltatás (PaaS), kevés konfigurációval vagy felügyeleti terheléssel, így az üzleti megoldásokra összpontosíthat. [Az Apache Kafkához készült Event Hubs-ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md) anélkül biztosítja a PaaS Kafka előnyeit, hogy saját fürtöket kellene kezelnie, konfigurálnia vagy futtatnia.

## <a name="support-for-real-time-and-batch-processing"></a>Valós idejű kötegelt feldolgozás támogatása

Valós időben töltheti be, pufferelheti, tárolhatja és dolgozhatja fel a streameket, hogy a gyakorlatban is használható elemzésekhez jusson. Az Event Hubs [egy particionált fogyasztói modellt](event-hubs-scalability.md#partitions)használ, amely lehetővé teszi, hogy több alkalmazás egyidejűleg dolgozza fel az adatfolyamot, és lehetővé teszi a feldolgozás sebességének szabályozását.

Közel valós időben [rögzítheti](event-hubs-capture-overview.md) adatait egy [Azure Blob-tárban](https://azure.microsoft.com/services/storage/blobs/) vagy [az Azure Data Lake Storage-ban](https://azure.microsoft.com/services/data-lake-store/) a hosszú távú megőrzéshez vagy a mikroköteges feldolgozáshoz. Ezt a viselkedést ugyanazon az adatfolyamon érheti el, amelyet valós idejű elemzések lefolyásához használ. Az eseményadatok rögzítésének beállítása gyors. A futtatásához nincsenek felügyeleti költségek, és automatikusan méretezhető az Event Hubs [átviteli egységekkel.](event-hubs-scalability.md#throughput-units) Az Event Hubs lehetővé teszi, hogy az adatfeldolgozásra összpontosítson, ne pedig az adatrögzítésre.

Az Azure Event Hubs az [Azure Functions](/azure/azure-functions/) szolgáltatással is integrálható egy kiszolgáló nélküli architektúra keretein belül.

## <a name="scalable"></a>Méretezhető

Az Event Hubs szolgáltatással megabájtokban kezdheti az adatstreameket, és onnan továbbléphet a giga- és terabájtokra. Az [automatikus feltöltési](event-hubs-auto-inflate.md) funkció az egyik lehetőség az átviteli egységek számának a használati igényeinek megfelelő módosítására.

## <a name="rich-ecosystem"></a>Gazdag ökoszisztéma

[Az Apache Kafka-ökoszisztémák eseményközpontjai](event-hubs-for-kafka-ecosystem-overview.md) lehetővé teszik, hogy [az Apache Kafka (1.0-s és újabb)](https://kafka.apache.org/) ügyfelek és alkalmazások az Event Hubs-szal beszéljenek. Nem kell beállítani, konfigurálni és kezelni a saját Kafka-fürtök.

A különböző [nyelveken (.NET, Java, Python, Go, JavaScript)](https://github.com/Azure/azure-event-hubs)elérhető széles ökoszisztémával könnyedén elkezdheti az adatfolyamok feldolgozását az Event Hubs-ból. Az összes támogatott ügyfélnyelv alacsony szintű integrációt biztosít. Az ökoszisztéma zökkenőmentes integrációt biztosít az Azure-szolgáltatásokkal, például az Azure Stream Analytics-szel és az Azure Functions-szel, így lehetővé teszi a kiszolgáló nélküli architektúrák készítését.

## <a name="key-architecture-components"></a>Az architektúra legfontosabb összetevői
Az Event Hubs az alábbi [fő összetevőket](event-hubs-features.md) foglalja magába:

- **Esemény-előállítók**: Minden entitás, amely adatokat küld egy eseményközpontnak. Az esemény-közzétevők a HTTPS, az AMQP 1.0 vagy az Apache Kafka (1.0 és újabb) használatával tehetik közzé az eseményeket.
- **Partíciók**: Mindegyik felhasználó az üzenetstreamnek csak egy adott részét vagy partícióját olvassa.
- **Felhasználói csoportok**: A teljes eseményközpont egyik nézete (állapot, pozíció vagy eltolás). A fogyasztói csoportok lehetővé teszik a fogyasztó alkalmazások mindegyikének külön nézetét az eseménystreamről. Önállóan olvassák a patakot a saját tempójukban és a saját eltolással.
- **Átviteli egységek**: Előre megvásárolt kapacitásegységek, amelyek az eseményközpontok átviteli kapacitását vezérlik.
- **Eseményérzékelő**: Minden entitás, amely eseményadatokat olvas egy eseményközpontból. Az Összes Event Hubs-fogyasztó az AMQP 1.0-s munkameneten keresztül csatlakozik. Az Event Hubs szolgáltatás eseményeket biztosít egy munkameneten keresztül, amint azok elérhetővé válnak. Minden Kafka-fogyasztó a Kafka-protokoll 1.0-s vagy újabb verziójával csatlakozik.

A következő ábrán az Event Hubs streamfeldolgozási architektúrája látható:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>További lépések

Az Event Hubs használatának megkezdéséhez tekintse meg az **Események küldése és fogadása** oktatóanyagokat:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-node-send-v2.md)
- [Ugrás](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)


Az Event Hubsszal kapcsolatos további információkért olvassa el a következő cikkeket:

- [Az Event Hubs-szolgáltatások áttekintése](event-hubs-features.md)
- [Gyakori kérdések](event-hubs-faq.md).


