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
ms.openlocfilehash: cffa9f566c6b2f4feb0fd7426eeea54a4644fda8
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140149"
---
# <a name="azure-event-hubs---a-big-data-streaming-platform-and-event-ingestion-service"></a>Az Azure Event Hubs - egy big Data típusú adatstreamelési platform és Eseményfeldolgozási szolgáltatás

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. 

Az Event Hubs többek között a következő forgatókönyvekhez használható:

- Rendellenességek észlelése (csalások/kiugró értékek)
- Alkalmazásnaplózás
- Elemzési folyamatok, például kattintássorozatoké
- Élő irányítópultok
- Adatok archiválása
- Tranzakciófeldolgozás
- Felhasználói telemetria feldolgozása
- Eszköztelemetria-stream 

## <a name="why-use-event-hubs"></a>Miért érdemes az Event Hubs platformot használni?

Az adatok csak akkor képviselnek értéket, ha könnyen feldolgozhatók, és időszerű elemzések állíthatók össze az adatforrásokból. Az Event Hubs egy elosztott streamfeldolgozó platformot biztosít kis késéssel és zökkenőmentes integrációval, valamint az Azure-on belüli és kívüli adat- és elemzési szolgáltatásokkal egy teljes körű Big Data-folyamat kiépítése érdekében.

Az Event Hubs az eseményfolyamatok „bejárati ajtaja”, amelyet gyakran neveznek *eseménybetöltőnek* a megoldásarchitektúrákban. Az eseménybetöltő egy olyan összetevő vagy szolgáltatás, amely az esemény-közzétevők és az eseményfelhasználók közé ékelődve elkülöníti az eseménystream létrehozását az események felhasználásától. Az Event Hubs egységes adatstreamelési platformot biztosít időalapú megőrzési pufferrel, leválasztva az esemény-előállítókat az eseményfelhasználókról. 

A következő szakaszok az Azure Event Hubs szolgáltatás legfontosabb jellemzőit ismertetik: 

## <a name="fully-managed-paas"></a>Teljes mértékben felügyelt PaaS 

Az Event Hubs egy kevés konfigurációs vagy felügyeleti többletterhelést okozó felügyelt szolgáltatás, amely elősegíti, hogy az üzleti megoldásokra összpontosíthasson. [Az Apache Kafkához készült Event Hubs-ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md) anélkül biztosítja a PaaS Kafka előnyeit, hogy saját fürtöket kellene kezelnie, konfigurálnia vagy futtatnia.

## <a name="support-for-real-time-and-batch-processing"></a>Valós idejű kötegelt feldolgozás támogatása

Valós időben töltheti be, pufferelheti, tárolhatja és dolgozhatja fel a streameket, hogy a gyakorlatban is használható elemzésekhez jusson. Az Event Hubs egy [particionált felhasználói modellt](event-hubs-features.md#partitions) használ, amellyel több alkalmazás dolgozhatja fel egyszerre a streamet, és a feldolgozási sebesség szabályozását is lehetővé teszi.

Az adatokat közel valós időben [rögzítheti](event-hubs-capture-overview.md) egy [Azure Blob Storage-tárolóban](https://azure.microsoft.com/services/storage/blobs/) vagy [Azure Data Lake Store-ban](https://azure.microsoft.com/services/data-lake-store/)  a hosszú idejű megőrzés vagy mikrokötegelt feldolgozás érdekében. Ezt ugyanabban a streamben érheti el, amelyet a valós idejű elemzések származtatásához is használ. A Capture beállítása gyors, a futtatása nem jár felügyeleti költségekkel, és automatikusan méretezhető az Event Hubs  [átviteli egységeivel](event-hubs-features.md#throughput-units). Az Event Hubs Capture lehetővé teszi, hogy az adatok rögzítése helyett a feldolgozásukra összpontosíthasson.

Az Azure Event Hubs az [Azure Functions](/azure/azure-functions/) szolgáltatással is integrálható egy kiszolgáló nélküli architektúra keretein belül.

## <a name="scalable"></a>Méretezhető 

Az Event Hubs szolgáltatással megabájtokban kezdheti az adatstreameket, és onnan továbbléphet a giga- és terabájtokra. Az [automatikus feltöltési](event-hubs-auto-inflate.md) funkció az egyik lehetőség az átviteli egységek számának a használati igényeinek megfelelő módosítására. 

## <a name="rich-ecosystem"></a>Gazdag ökoszisztéma

[Az Apache Kafkához készült Event Hubs-ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md) lehetővé teszi, hogy az [(1.0-s és újabb) Apache Kafka](https://kafka.apache.org/) ügyfelei és alkalmazásai fürtök kezelése nélkül kommunikálhassanak az Event Hubs szolgáltatással.
 
A különböző [nyelveken (.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs) elérhető sokoldalú ökoszisztéma megkönnyíti a streamek feldolgozását az Event Hubsból. Az összes támogatott ügyfélnyelv alacsony szintű integrációt biztosít. Az ökoszisztéma olyan Azure-szolgáltatásokkal is zökkenőmentes integrációt biztosít, mint például a Stream Analytics és az Azure Functions, így kiszolgáló nélküli architektúrákat építhet ki.

## <a name="key-architecture-components"></a>Az architektúra legfontosabb összetevői

Az Event Hubs üzenetstream-kezelési képességet is biztosít, de olyan tulajdonságokkal rendelkezik, amelyek eltérnek a hagyományos vállalati üzenetkezelés jellemzőitől. Az Event Hubs képességei kimondottan a nagy mennyiségre és eseményfeldolgozási forgatókönyvekre vannak optimalizálva. Az Event Hubs az alábbi [fő összetevőket](event-hubs-features.md) foglalja magába:

- **Eseménykészítőkből**: Minden entitás, amely adatokat küld egy eseményközpontnak. Az esemény-közzétevők a HTTPS, az AMQP 1.0 vagy az Apache Kafka (1.0 és újabb) használatával tehetik közzé az eseményeket.
- **A partíciók**: Mindegyik felhasználó csak egy meghatározott részhalmazát, azaz partícióját az üzenetet olvassa be.
- **Fogyasztói csoportok**: Nézete (állapot, pozíció vagy eltolás) teljes eseményközpont. A felhasználói csoportok révén több felhasználó alkalmazás rendelkezhet az eseménystream külön nézetével, és a többitől függetlenül saját tempójában és saját eltolásával olvashatja a streamet.
- **Átviteli egységek**: Előre megvásárolt kapacitásegységek, amely az Event Hubs átviteli kapacitásának szabályozása.
- **Esemény fogadók**: Minden entitás, amely eseményadatokat olvas egy eseményközpontból. Minden Event Hubs-felhasználó az AMQP 1.0-munkameneten keresztül csatlakozik, amelyben az események azonnal megjelennek, amint elérhetővé válnak. Minden Kafka-fogyasztó a Kafka-protokoll 1.0-s vagy újabb verziójával csatlakozik.

A következő ábrán az Event Hubs streamfeldolgozási architektúrája látható:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>További lépések

Az Event Hubs használatának első lépéseit a következő cikkek ismertetik:

1. **Létrehoz egy eseményközpontot**: [Az Azure portal](event-hubs-create.md), [Azure CLI-vel](event-hubs-quickstart-cli.md), [Azure PowerShell-lel](event-hubs-quickstart-powershell.md), [Azure Resource Manager-sablon](event-hubs-resource-manager-namespace-event-hub.md)
2. **Események küldése eseményközpontba**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
3. **Események fogadása az event hub**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js ](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)   

Az Event Hubsszal kapcsolatos további információkért olvassa el a következő cikkeket:

- [Az Event Hubs-szolgáltatások áttekintése](event-hubs-features.md)
- [Gyakori kérdések](event-hubs-faq.md)


