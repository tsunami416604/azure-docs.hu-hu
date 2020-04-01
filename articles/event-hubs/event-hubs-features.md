---
title: A funkciók áttekintése - Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk az Azure Event Hubs szolgáltatásaival és terminológiájával kapcsolatos részleteket ismerteti.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 76ab92285cace284c187109ca48c6634777ebbc0
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398321"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Az Azure Event Hubs funkciói és terminológiája

Az Azure Event Hubs egy méretezhető eseményfeldolgozó szolgáltatás, amely nagy mennyiségű eseményt és adatot dolgoz fel és dolgoz fel alacsony késéssel és nagy megbízhatósággal. A magas szintű áttekintésért tekintse meg [a Mi az eseményközpontok?](event-hubs-what-is-event-hubs.md)

Ez a cikk az [áttekintő cikkben](event-hubs-what-is-event-hubs.md)található információkra épül, és technikai és megvalósítási részleteket tartalmaz az Event Hubs összetevőiről és szolgáltatásairól.

## <a name="namespace"></a>Névtér
Az Event Hubs névtér egy egyedi hatókörtárolót biztosít, amelyre [a teljesen minősített tartománynév](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)hivatkozik, amelyben egy vagy több eseményközpontot vagy Kafka-témakört hoz létre. 

## <a name="event-hubs-for-apache-kafka"></a>Az Apache Kafkához készült Event Hubs

[Ez a szolgáltatás](event-hubs-for-kafka-ecosystem-overview.md) egy végpontot biztosít, amely lehetővé teszi az ügyfelek számára, hogy a Kafka protokoll használatával beszéljenek az Event Hubs-szal. Ez az integráció biztosítja az ügyfelek számára a Kafka-végpont. Ez lehetővé teszi az ügyfelek számára, hogy konfigurálják a meglévő Kafka-alkalmazásokat az Event Hubs-szal való beszélgetésre, így alternatívát kínálnak a saját Kafka-fürtök futtatásához. Az Apache Kafka Eseményközpontok támogatják a Kafka 1.0-s és újabb protokollját. 

Ezzel az integrációval nem kell futtatnia a Kafka-fürtöket, és nem kell kezelnie őket a Zookeeper segítségével. Ez azt is lehetővé teszi, hogy az Event Hubok, például a Rögzítés, az Automatikus felfújás és a Geo-katasztrófa-helyreállítás néhány legigényesebb funkciójával dolgozzon.

Ez az integráció lehetővé teszi az olyan alkalmazások számára, mint a Mirror Maker vagy a Framework, például a Kafka Connect, hogy fürtnélkül működjenek csak konfigurációs módosításokkal. 

## <a name="event-publishers"></a>Esemény-közzétevők

Minden olyan entitás, amely adatokat küld egy eseményközpontnak, eseménytermelő vagy *eseményközzétevő.* Az eseményközzétevők HTTPS vagy AMQP 1.0 vagy Kafka 1.0-s vagy újabb verziójú eseményeket tehetnek közzé. Az esemény-közzétevők egy közös hozzáférésű jogosultságkód- (SAS-) token használatával azonosítják magukat az eseményközpontok felé, és rendelkezhetnek egyedi azonosítóval vagy közös SAS-tokennel is.

### <a name="publishing-an-event"></a>Esemény közzététele

Egy eseményt közzétehet az AMQP 1.0, a Kafka 1.0 (és újabb) vagy a HTTPS protokollon keresztül. Az Event Hubs [ügyfélkönyvtárakat és osztályokat](event-hubs-dotnet-framework-api-overview.md) biztosít az események eseményközpontba való közzétételéhez . Egyéb futtatókörnyezetek és platformok esetén használhatja bármelyik AMQP 1.0-ügyfelet, ilyen például az [Apache Qpid](https://qpid.apache.org/). Az eseményeket közzéteheti egyenként vagy kötegelve is. Egyetlen kiadvány (eseményadat-példány) legfeljebb 1 MB, függetlenül attól, hogy egyetlen eseményről vagy kötegről van-e szó. Az ennél a küszöbértéknél nagyobb események közzététele hibát eredményez. Az ajánlott eljárás, hogy a közzétevők ne tudjanak a partíciókról az eseményközpontban, és csupán egy *partíciókulcsot* (ismertetése a következő szakaszban), vagy az azonosságukat kelljen megadniuk a SAS-token használatával.

Az AMQP vagy HTTPS használata a használati forgatókönyvtől függ. Az AMQP használatához ki kell alakítani egy állandó kétirányú szoftvercsatornát az TLS vagy SSL/TLS mellett. Az AMQP használata a munkamenet inicializálásakor nagyobb hálózati költséggel jár, a HTTPS azonban minden egyes kérés esetében további SSL-többletterhelést igényel. Gyakori közzététel esetén az AMQP nagyobb teljesítményt biztosít.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Az Event Hubs biztosítja, hogy az egyazon partíciókulcs-értékkel rendelkező események sorrendben érkezzenek meg, és egyazon partícióra kerüljenek. Ha a partíciókulcsok használata közzétevői házirendekkel együtt történik, a közzétevő identitásának és a partíciókulcs értékének egyeznie kell. Különben hiba történik.

### <a name="publisher-policy"></a>Közzétevői házirend

Az Event Hubs lehetővé teszi az esemény-közzétevők részletes szabályozását a *közzétevői házirendek* révén. A közzétevői házirendek olyan futásidejű szolgáltatások, amelyek célja, hogy nagy számú független esemény-közzétevőt tegyen lehetővé. A közzétevői házirendek használatával mindegyik közzétevő a saját egyedi azonosítóját használja, amikor eseményeket tesz közzé egy eseményközpontban az alábbi mechanizmust használva:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Nem kell előre létrehoznia a közzétevők neveit, azoknak azonban egyezniük kell az esemény közzétételekor használt SAS-tokennel a független közzétevő-azonosságok biztosítása érdekében. A közzétevői házirendek használatakor a **PartitionKey** értéke a közzétevő neve lesz. A megfelelő működéshez ezeknek az értékeknek egyezniük kell.

## <a name="capture"></a>Rögzítés

[Az Event Hubs Capture](event-hubs-capture-overview.md) lehetővé teszi, hogy automatikusan rögzítse a streamelési adatokat az Event Hubs-ban, és mentse azokat a blobstorage-fiók vagy egy Azure Data Lake Service-fiók közül választott módon. Engedélyezheti a rögzítést az Azure Portalról, és megadhat egy minimális méretet és időablakot a rögzítés végrehajtásához. Az Event Hubs Capture használatával megadhatja saját Azure Blob Storage-fiókját és tárolóját, vagy az Azure Data Lake Service-fiókot, amely a rögzített adatok tárolására szolgál. A rögzített adatok Apache Avro formátumban íródnak.

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-tokenek

Az Event Hubs *megosztott hozzáférésű aláírásokat*használ, amelyek a névtér és az eseményközpont szintjén érhetők el. A SAS-tokent egy SAS-kulcsból hozza létre a rendszer, és egy URL SHA-kivonata egy meghatározott formátumban kódolva. A kulcs neve (házirend) és a token együttes használatával az Event Hubs képes újra létrehozni a kivonatot, és így azonosítani a küldőt. Általában az eseményközzétevők SAS-jogkivonatai csak **küldési** jogosultságokkal jönnek létre egy adott eseményközponton. Ez a SAS-tokenes URL-mechanizmus az alapja a közzétevők a közzétevői házirendben bevezetett azonosításának. További információ a SAS használatával kapcsolatban: [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md) (Közös hozzáférésű jogosultságkóddal való hitelesítés a Service Bus használatával).

## <a name="event-consumers"></a>Eseményfelhasználók

Minden olyan entitás, amely eseményadatokat olvas be egy eseményközpontból, *eseményfogyasztó.* Minden Event Hubs-felhasználó az AMQP 1.0-munkameneten keresztül csatlakozik, amelyben az események azonnal megjelennek, amint elérhetővé válnak. Az ügyfélnek nem kell lekérdeznie az adatok rendelkezésre állását.

### <a name="consumer-groups"></a>Felhasználói csoportok

Az Event Hubs közzétételi/előfizetési mechanizmusa *fogyasztói csoportokon*keresztül engedélyezett. A felhasználói csoport a teljes eseményközpont egyik nézete (állapot, pozíció vagy eltolás). A felhasználói csoportok révén több felhasználó alkalmazás rendelkezhet az eseménystream külön nézetével, és a többitől függetlenül saját tempójában és saját eltolásával olvashatja a streamet.

A streamfeldolgozási architektúrákban mindegyik alárendelt alkalmazás megfelel egy felhasználói csoportnak. Amennyiben eseményadatokat kíván írni egy hosszú távú tárhelyre, az adott tárhelyírási alkalmazás is egy felhasználói csoport. Az összetett eseményfeldolgozást már egy másik, külön felhasználói csoport végzi. A partíciókat csak a felhasználói csoportokon keresztül érheti el. Az eseményközpontokban mindig van egy alapértelmezett felhasználói csoport, és akár 20 felhasználói csoportot is létrehozhat a standard szintű eseményközpontokban.

Fogyasztói csoportonként legbőleg5 egyidejű olvasó lehet egy partíción; **azonban ajánlott, hogy fogyasztói csoportonként csak egy aktív fogadó van egy partíción**. Egyetlen partíción belül minden olvasó megkapja az összes üzenetet. Ha ugyanazon a partíción több olvasó is található, akkor ismétlődő üzeneteket dolgoz fel. Ezt a kódban kell kezelnie, ami nem feltétlenül triviális. Azonban ez egy érvényes megközelítés bizonyos esetekben.


Az alábbiakban néhány példa látható felhasználói csoportok URI-szabályaira:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

A következő ábrán az Event Hubs streamfeldolgozási architektúrája látható:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Streameltolások

Az *eltolás* egy esemény partíción belüli helyzete. Az eltolásokat tekintheti ügyféloldali kurzorként. Az eltolás az esemény bájtalapú sorszáma. Ez az eltolás lehetővé teszi az eseményfelhasználó (olvasó) számára az eseménystream egy olyan pontjának megadását, ahol az események olvasását el szeretné kezdeni. Az eltolás megadható időbélyegzőként vagy eltolásértékként. A felhasználók felelőssége saját eltolásértékeik tárolása az Event Hubs szolgáltatáson kívül. A partíciókon belül mindegyik esemény rendelkezik eltolással.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Ellenőrzőpontok használata

Az *ellenőrzőpontok használatával* az olvasók megjelölhetik vagy véglegesíthetik pozíciójukat a partíciók eseménysorozatában. Az ellenőrzőpontok használata a felhasználó felelőssége, és partíciónkénti alapon történik a felhasználói csoportban. A felelősség itt azt jelenti, hogy mindegyik felhasználói csoport esetében mindegyik partícióolvasónak nyilván kell tartania aktuális pozícióját az eseménystreamben, és tájékoztathatja a szolgáltatást, amikor az adatstreamet befejezettnek tekinti.

Ha egy olvasó lecsatlakozik egy partícióról, az újracsatlakozáskor az adott felhasználói csoportban az adott partíció utolsó olvasója által elküldött ellenőrzőpontnál kezdi az olvasást. Amikor az olvasó csatlakozik, átadja az eltolást az eseményközpontnak, hogy megadja azt a helyet, ahol el szeretné kezdeni az olvasást. Az ellenőrzőpontok használatával az alárendelt alkalmazások így megjelölhetik az eseményeket „befejezettként”, valamint biztosítható a rugalmasság a különböző gépeken futó olvasók közötti feladatátvétel esetén. Lehetséges visszatérni a régebbi adatokhoz egy alacsonyabb értékű eltolás megadásával az ellenőrzőpontok használata során. Ezzel a mechanizmussal az ellenőrzőpontok használata rugalmasságot biztosít feladatátvétel esetén, és lehetővé teszi az eseménystream visszajátszását.

> [!NOTE]
> Ha az Azure Blob Storage-t használja ellenőrzőpont-tárolóként egy olyan környezetben, amely a Storage Blob SDK egy másik verzióját támogatja, mint az Azure-ban általában elérhető, kódot kell használnia a Storage service API-verziójának az adott környezet által támogatott verzióra való módosításához. Ha például az [Event Hubs szolgáltatást egy 2002-es Azure Stack Hub-verzión futtatja,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a Storage szolgáltatás legmagasabb elérhető verziója a 2017-11-09-es verzió. Ebben az esetben a Storage service API-verziójának 2017-11-09-re való célzásához kódot kell használnia. Egy adott Storage API-verzió célzásával kapcsolatos példát tekintse meg ezeket a mintákat a GitHubon: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) vagy [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

### <a name="common-consumer-tasks"></a>Általános felhasználói feladatok

Minden Event Hubs-fogyasztó egy AMQP 1.0-s munkameneten, egy állapottudatos kétirányú kommunikációs csatornán keresztül csatlakozik. Mindegyik partíció rendelkezik AMQP 1.0-munkamenettel, ami megkönnyíti a partíció szerint elkülönített események átvitelét.

#### <a name="connect-to-a-partition"></a>Csatlakozás partícióhoz

Partíciókhoz való kapcsolódás esetén általános gyakorlat a bérlési mechanizmus használata az adott partíciók olvasói kapcsolatainak koordinálására. Így lehetséges, hogy az egyes felhasználói csoportokban minden partíció csak egyetlen aktív olvasóval rendelkezzen. Az ellenőrzőpontok használata, a bérlés, valamint az olvasók kezelése az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) osztály használatával egyszerűsíthető .NET-ügyfelek esetén. Az Event Processor Host egy intelligens felhasználóügynök.

#### <a name="read-events"></a>Események olvasása

Miután egy AMQP 1.0-munkamenet és -hivatkozás meg lett nyitva egy adott partícióra vonatkozóan, az Event Hubs szolgáltatás kézbesíti az eseményeket az AMQP 1.0-ügyfél számára. Ez a kézbesítési mechanizmus nagyobb átviteli teljesítményt és kisebb késést biztosít a lekérésalapú mechanizmusoknál, amilyen például a HTTP GET. Az eseményeknek az ügyfél számára való elküldésekor minden eseményadat-példány fontos metaadatokat tartalmaz, például az eltolást és sorszámot, amelyek az eseménysorozat ellenőrzőpontokkal való jelölése során használhatók.

Eseményadatok:
* Eltolás
* Sorozat száma
* Törzs
* Felhasználói tulajdonságok
* Rendszertulajdonságok

Az eltolás kezelése a felhasználó felelőssége.

## <a name="next-steps"></a>További lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Eseményközpontok mintái][]

[Eseményközpontok mintái]: https://github.com/Azure/azure-event-hubs/tree/master/samples
