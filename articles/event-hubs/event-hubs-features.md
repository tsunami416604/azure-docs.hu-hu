---
title: Szolgáltatások – Azure Event Hubs áttekintése |} A Microsoft Docs
description: Ez a cikk részletesen funkciók és az Azure Event hubs terminológiát.
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
ms.openlocfilehash: 568a21cee5b50a8914c603976f5951d0235dbff7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281482"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkciók és az Azure Event Hubs terminológiája

Az Azure Event Hubs egy rugalmasan méretezhető Eseményfeldolgozási szolgáltatás, amely feltölti és dolgozza fel az eseményeket és az adatok, nagy mennyiségű, alacsony késéssel és magas megbízhatósággal. Lásd: [Mi az Event Hubs?](event-hubs-what-is-event-hubs.md) a magas szintű áttekintéshez.

Ez a cikk az [áttekintő cikkben](event-hubs-what-is-event-hubs.md)található információkra épül, valamint a Event Hubs összetevőkkel és szolgáltatásokkal kapcsolatos technikai és megvalósítási részleteket tartalmaz.

## <a name="namespace"></a>Névtér
Az Event Hubs névtér egy egyedi hatókörű tárolót biztosít, amelyet a [teljes tartományneve](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)hivatkozik, amelyben létrehoz egy vagy több Event hub-vagy Kafka-témakört. 

## <a name="event-hubs-for-apache-kafka"></a>Az Apache Kafkához készült Event Hubs

[Ez a szolgáltatás](event-hubs-for-kafka-ecosystem-overview.md) egy olyan végpontot biztosít, amely lehetővé teszi, hogy az ügyfelek a Kafka protokoll használatával beszéljenek Event Hubs. Ez az integráció Kafka végpontot biztosít az ügyfeleknek. Ez lehetővé teszi az ügyfelek számára, hogy konfigurálja a meglévő Kafka alkalmazásaik felvenni a kapcsolatot az Event hubs szolgáltatásba, így a saját Kafka-fürtök futtatása helyett használhatók. Az Event Hubs-beli Apache kafka támogatja a Kafka-protokoll 1.0-s és újabb verziók. 

Ebben az integrációban nem kell Kafka-fürtöket futtatnia, vagy a Zookeeper-mel kezelnie. Ez lehetővé teszi, hogy az Event Hubs, az automatikus feltöltési és a Geo-disaster Recovery rögzítése a legnagyobb erőforrás-igényű funkcióit.

Ez az integráció is lehetővé teszi alkalmazások, mint a tükör Maker vagy keretrendszert, például Kafka csatlakozás működéséhez clusterless csak konfigurációs módosítások. 

## <a name="event-publishers"></a>Esemény-közzétevők

Bármely entitás, amely adatokat küld az Event hub-nak, esemény-előállító vagy *esemény-közzétevő*. Az esemény-közzétevők tehetik közzé az eseményeket, a HTTPS vagy AMQP 1.0-s vagy Kafka 1.0-s és újabb verziók. Az esemény-közzétevők egy közös hozzáférésű jogosultságkód- (SAS-) token használatával azonosítják magukat az eseményközpontok felé, és rendelkezhetnek egyedi azonosítóval vagy közös SAS-tokennel is.

### <a name="publishing-an-event"></a>Esemény közzététele

Egy esemény az AMQP 1.0-t, a Kafka 1.0-ás (és újabb) vagy a HTTPS használatával teheti közzé. A Event Hubs [ügyféloldali kódtárakat és osztályokat](event-hubs-dotnet-framework-api-overview.md) biztosít a .net-ügyfelek eseményeinek közzétételéhez. Egyéb futtatókörnyezetek és platformok esetén használhatja bármelyik AMQP 1.0-ügyfelet, ilyen például az [Apache Qpid](https://qpid.apache.org/). Az eseményeket közzéteheti egyenként vagy kötegelve is. Egyes közzétételekre (eseményadat-példány) a korlát 1 MB-ot, függetlenül attól, hogy-e egy önálló vagy kötegelt rendelkezik. Nagyobb, mint a küszöbérték eredmények események közzététele hibát. Az ajánlott eljárás, hogy a közzétevők ne tudjanak a partíciókról az eseményközpontban, és csupán egy *partíciókulcsot* (ismertetése a következő szakaszban), vagy az azonosságukat kelljen megadniuk a SAS-token használatával.

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

A [Event Hubs Capture](event-hubs-capture-overview.md) lehetővé teszi, hogy automatikusan rögzítse a streaming-adatEvent Hubs, és mentse azt egy blob Storage-fiók vagy egy Azure Data Lake szolgáltatásfiók számára. Rögzítés funkció engedélyezése az Azure Portalról, és adjon meg egy minimális méret és időtartomány hajtsa végre a rögzítést. Az Event Hubs Capture révén, adja meg a saját Azure Blob Storage-fiók és a tároló vagy az Azure Data Lake szolgáltatás-fiókot, ilyen például a rögzített adatok tárolására szolgál. A rögzített adatok az Apache Avro formátum nyelven van megírva.

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-tokenek

Az Event Hubs olyan *közös hozzáférésű jogosultságkódokat* használ, amelyek a névtér és az eseményközpont szintjén érhetők el. A SAS-tokent egy SAS-kulcsból hozza létre a rendszer, és egy URL SHA-kivonata egy meghatározott formátumban kódolva. A kulcs neve (házirend) és a token együttes használatával az Event Hubs képes újra létrehozni a kivonatot, és így azonosítani a küldőt. Az esemény-közzétevők SAS-tokenje általában egy adott eseményközpontban, csak **küldési** jogosultságokkal hozható létre. Ez a SAS-tokenes URL-mechanizmus az alapja a közzétevők a közzétevői házirendben bevezetett azonosításának. További információ a SAS használatával kapcsolatban: [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md) (Közös hozzáférésű jogosultságkóddal való hitelesítés a Service Bus használatával).

## <a name="event-consumers"></a>Eseményfelhasználók

Minden entitás, amely eseményadatokat olvas egy eseményközpontból, *eseményfelhasználó*. Minden Event Hubs-felhasználó az AMQP 1.0-munkameneten keresztül csatlakozik, amelyben az események azonnal megjelennek, amint elérhetővé válnak. Az ügyfélnek nem kell lekérdeznie az adatok rendelkezésre állását.

### <a name="consumer-groups"></a>Felhasználói csoportok

Az Event Hubs közzétételi/feliratkozási mechanizmusa *felhasználói csoportokon* keresztül engedélyezhető. A felhasználói csoport a teljes eseményközpont egyik nézete (állapot, pozíció vagy eltolás). A felhasználói csoportok révén több felhasználó alkalmazás rendelkezhet az eseménystream külön nézetével, és a többitől függetlenül saját tempójában és saját eltolásával olvashatja a streamet.

A streamfeldolgozási architektúrákban mindegyik alárendelt alkalmazás megfelel egy felhasználói csoportnak. Amennyiben eseményadatokat kíván írni egy hosszú távú tárhelyre, az adott tárhelyírási alkalmazás is egy felhasználói csoport. Az összetett eseményfeldolgozást már egy másik, külön felhasználói csoport végzi. A partíciókat csak a felhasználói csoportokon keresztül érheti el. Az eseményközpontokban mindig van egy alapértelmezett felhasználói csoport, és akár 20 felhasználói csoportot is létrehozhat a standard szintű eseményközpontokban.

A felhasználónkénti csoportonként legfeljebb 5 egyidejű olvasó lehet egy partíción. **azt javasoljuk azonban, hogy a partíciók felhasználónkénti csoportjain csak egy aktív fogadó legyen**. Belül egyetlen partícióra minden egyes olvasó kap az összes üzenetet. Ha ugyanazon a partíción kibővítése több olvasóra, majd, ismétlődő üzenetek feldolgozását. Szeretné ezt a kódban, amely nem feltétlenül triviális kezeli. Azonban egy érvényes módszer bizonyos esetekben.


Az alábbiakban néhány példa látható felhasználói csoportok URI-szabályaira:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

A következő ábrán az Event Hubs streamfeldolgozási architektúrája látható:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Streameltolások

Az *eltolás* egy esemény pozíciója a partíción belül. Az eltolásokat tekintheti ügyféloldali kurzorként. Az eltolás az esemény bájtalapú sorszáma. Ez az eltolás lehetővé teszi az eseményfelhasználó (olvasó) számára az eseménystream egy olyan pontjának megadását, ahol az események olvasását el szeretné kezdeni. Az eltolás megadható időbélyegzőként vagy eltolásértékként. A felhasználók felelőssége saját eltolásértékeik tárolása az Event Hubs szolgáltatáson kívül. A partíciókon belül mindegyik esemény rendelkezik eltolással.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Ellenőrzőpontok használata

Az *ellenőrzőpontok használatával* az olvasók megjelölhetik vagy véglegesíthetik pozíciójukat a partíciók eseménysorozatában. Az ellenőrzőpontok használata a felhasználó felelőssége, és partíciónkénti alapon történik a felhasználói csoportban. A felelősség itt azt jelenti, hogy mindegyik felhasználói csoport esetében mindegyik partícióolvasónak nyilván kell tartania aktuális pozícióját az eseménystreamben, és tájékoztathatja a szolgáltatást, amikor az adatstreamet befejezettnek tekinti.

Ha egy olvasó lecsatlakozik egy partícióról, az újracsatlakozáskor az adott felhasználói csoportban az adott partíció utolsó olvasója által elküldött ellenőrzőpontnál kezdi az olvasást. Amikor az olvasó csatlakozik, átadja az eltolás az event hubs segítségével megadhatja a helyet, ahol az olvasást kezdi. Az ellenőrzőpontok használatával az alárendelt alkalmazások így megjelölhetik az eseményeket „befejezettként”, valamint biztosítható a rugalmasság a különböző gépeken futó olvasók közötti feladatátvétel esetén. Lehetséges visszatérni a régebbi adatokhoz egy alacsonyabb értékű eltolás megadásával az ellenőrzőpontok használata során. Ezzel a mechanizmussal az ellenőrzőpontok használata rugalmasságot biztosít feladatátvétel esetén, és lehetővé teszi az eseménystream visszajátszását.

### <a name="common-consumer-tasks"></a>Általános felhasználói feladatok

Minden Event Hubs-felhasználó AMQP 1.0-munkamenettel, állapotközpontú kétirányú kommunikációs csatorna csatlakozás. Mindegyik partíció rendelkezik AMQP 1.0-munkamenettel, ami megkönnyíti a partíció szerint elkülönített események átvitelét.

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

## <a name="next-steps"></a>Következő lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Event Hubs minták][]

[Event Hubs minták]: https://github.com/Azure/azure-event-hubs/tree/master/samples
