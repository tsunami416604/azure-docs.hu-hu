---
title: "Az Azure Event Hubs-szolgáltatások áttekintése |} Microsoft Docs"
description: "Áttekintés és az Azure Event Hubs szolgáltatások részleteit"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: sethm
ms.openlocfilehash: aa9fc3b03e24d0b4d1a7ecd9a945b67d8d182492
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="event-hubs-features-overview"></a>Event Hubs szolgáltatások – áttekintés

Az Azure Event Hubs egy méretezhető Eseményfeldolgozási szolgáltatás, amely ingests és dolgozza fel az eseményeket és adatok, nagy mennyiségű, alacsony késéssel és nagy megbízhatósággal. Lásd: [Mi az az Event Hubs?](event-hubs-what-is-event-hubs.md) a szolgáltatás magas szintű áttekintését.

Ebben a cikkben szereplő információk épül a [a cikk áttekintése](event-hubs-what-is-event-hubs.md), és részletesen bemutatja a műszaki és megvalósítási Event Hubs-összetevők és szolgáltatásokkal kapcsolatban.

## <a name="event-publishers"></a>Esemény-közzétevők

Minden entitás, amely adatokat küld egy eseményközpontba egy esemény készítő vagy *esemény-közzétevő*. Az esemény-közzétevők a HTTPS vagy az AMQP 1.0 használatával tehetik közzé az eseményeket. Az esemény-közzétevők egy közös hozzáférésű jogosultságkód- (SAS-) token használatával azonosítják magukat az eseményközpontok felé, és rendelkezhetnek egyedi azonosítóval vagy közös SAS-tokennel is.

### <a name="publishing-an-event"></a>Esemény közzététele

Az eseményeket az AMQP 1.0 vagy HTTPS használatával teheti közzé. Az Event Hubs biztosít [klienskódtárak és -osztályok](event-hubs-dotnet-framework-api-overview.md) számára események közzétételét egy eseményközpontba a .NET-ügyfelekről. Egyéb futtatókörnyezetek és platformok esetén használhatja bármelyik AMQP 1.0-ügyfelet, ilyen például az [Apache Qpid](http://qpid.apache.org/). Az eseményeket közzéteheti egyenként vagy kötegelve is. Az egyes közzétételekre (eseményadat-példány) 256 KB-os korlát érvényes, függetlenül attól, hogy önálló vagy kötegelt közzétételről van-e szó. Nagyobb, mint a küszöbérték eredmények események közzététele hibát eredményez. Az ajánlott eljárás, hogy a közzétevők ne tudjanak a partíciókról az eseményközpontban, és csupán egy *partíciókulcsot* (ismertetése a következő szakaszban), vagy az azonosságukat kelljen megadniuk a SAS-token használatával.

Az AMQP vagy HTTPS használata a használati forgatókönyvtől függ. Az AMQP használatához ki kell alakítani egy állandó kétirányú szoftvercsatornát az TLS vagy SSL/TLS mellett. Az AMQP használata a munkamenet inicializálásakor nagyobb hálózati költséggel jár, a HTTPS azonban minden egyes kérés esetében további SSL-többletterhelést igényel. Gyakori közzététel esetén az AMQP nagyobb teljesítményt biztosít.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Az Event Hubs biztosítja, hogy az egyazon partíciókulcs-értékkel rendelkező események sorrendben érkezzenek meg, és egyazon partícióra kerüljenek. Ha a partíciókulcsok használata közzétevői házirendekkel együtt történik, a közzétevő identitásának és a partíciókulcs értékének egyeznie kell. Különben hiba történik.

### <a name="publisher-policy"></a>Közzétevői házirend

Az Event Hubs lehetővé teszi az esemény-közzétevők részletes szabályozását a *közzétevői házirendek* révén. A közzétevői házirendek olyan futásidejű szolgáltatások, amelyek célja, hogy nagy számú független esemény-közzétevőt tegyen lehetővé. A közzétevői házirendek használatával mindegyik közzétevő a saját egyedi azonosítóját használja, amikor eseményeket tesz közzé egy eseményközpontban az alábbi mechanizmust használva:

```
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Nem kell előre létrehoznia a közzétevők neveit, azoknak azonban egyezniük kell az esemény közzétételekor használt SAS-tokennel a független közzétevő-azonosságok biztosítása érdekében. A közzétevői házirendek használatakor a **PartitionKey** értéke a közzétevő neve lesz. A megfelelő működéshez ezeknek az értékeknek egyezniük kell.

## <a name="capture"></a>Rögzítés

[Event Hubs rögzítése](event-hubs-capture-overview.md) lehetővé teszi, hogy automatikusan rögzítheti az Event Hubs streamadatok, és mentse a Blob storage-fiók, vagy egy Azure Data Lake szolgáltatásfiók választott. Rögzítési engedélyezése az Azure-portálon, és adja meg a minimális és a rögzítés végrehajtásához időkerete. Használatával Event Hubs rögzítése Megadja, hogy a saját Azure Blob Storage-fiók és a tároló vagy az Azure Data Lake szolgáltatás fiók, amely a rögzített adatok tárolására szolgál. A rögzített adatok írása az Apache Avro formátumban.

## <a name="partitions"></a>Partíciók

Az Event Hubs üzenetstreamelést biztosít egy particionált felhasználói mintán keresztül, amelyben mindegyik felhasználó az üzenetstream csak egy adott részét, vagyis partícióját olvassa. Ez a minta biztosítja a horizontális skálázhatóságot az eseményfeldolgozáshoz, és egyéb, streamközpontú szolgáltatásokat is nyújt, amelyek az üzenetsorokban vagy témakörökben nem érhetők el.

A partíció események egy rendezett sorozata az eseményközpontban. Ha új esemény érkezik, az a sorozat végére kerül. A partíció elképzelhető egy „véglegesítési naplóként”.

![Event Hubs](./media/event-hubs-features/partition.png)

Az Event Hubs adatok őrzi meg a konfigurált megőrzési időtartamig összes partíciójára érvényes a központ. Az események időalapon évülnek el – nem törölhetők külön. Mivel a partíciók függetlenek egymástól, és saját adatsorozataikat tartalmazzák, gyakran különböző ütemben nőnek.

![Event Hubs](./media/event-hubs-features/multiple_partitions.png)

A partíciók száma az eseményközpont létrehozásakor határozható meg, és 2 és 32 közé eshet. A partíciószám nem módosítható, a megadásakor tehát hosszú távú szempontokat érdemes mérlegelni. A partíció egy adatrendezési mechanizmus, és a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamossághoz köthető. Az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. A partíciószám 32 fölé növeléséhez vegye fel a kapcsolatot az Event Hubs-csapattal.

Partíciók azonosíthatóak, és közvetlenül lehet küldeni, közvetlenül küld egy partíció nem ajánlott. Helyette használhatja az [Esemény-közzétevő](#event-publishers) és a [Kapacitás](#capacity) című szakaszokban bemutatott magasabb szintű szerkezeteket. 

A partíciók eseményadatok sorozatát tartalmazzák, az eseményadatok pedig az esemény törzsét, a felhasználó által definiált tulajdonságcsomagot és olyan metaadatokat foglalnak magukban, mint az esemény eltolása a partícióban vagy a száma a streamsorozatban.

További információt a partíciókról és a rendelkezésre állás és a megbízhatóság közötti kellő egyensúly kialakításáról az [Event Hubs programozási útmutatójában](event-hubs-programming-guide.md#partition-key) és az [Event Hubs rendelkezésre állásával és következetességével](event-hubs-availability-and-consistency.md) foglalkozó cikkben talál.

### <a name="partition-key"></a>Partíciókulcs

A [partíciókulccsal](event-hubs-programming-guide.md#partition-key) a beérkező eseményadatok képezhetők le adott partíciókra az adatok elrendezése céljából. A partíciókulcs az eseményközpontnak átadott, a küldő által megadott érték. A feldolgozása egy statikus kivonatoló függvénnyel történik, amely létrehozza a partíció-hozzárendelést. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ.

Az esemény-közzétevő csak a partíciókulcsot ismeri, azt a partíciót nem, amelyre az esemény közzé lesz téve. A kulcs és a partíció szétválasztása révén a küldőnek nem szükséges behatóan ismernie az alárendelt feldolgozási folyamatokat. Az eszközszintű vagy egyedi felhasználói identitás remek partíciókulcs lehet, de más tulajdonságok, például a földrajzi hely alapján szintén lehetséges az események csoportosítása egyetlen partícióra.

## <a name="sas-tokens"></a>SAS-tokenek

Az Event Hubs olyan *közös hozzáférésű jogosultságkódokat* használ, amelyek a névtér és az eseményközpont szintjén érhetők el. A SAS-tokent egy SAS-kulcsból hozza létre a rendszer, és egy URL SHA-kivonata egy meghatározott formátumban kódolva. A kulcs neve (házirend) és a token együttes használatával az Event Hubs képes újra létrehozni a kivonatot, és így azonosítani a küldőt. Az esemény-közzétevők SAS-tokenje általában egy adott eseményközpontban, csak **küldési** jogosultságokkal hozható létre. Ez a SAS-tokenes URL-mechanizmus az alapja a közzétevők a közzétevői házirendben bevezetett azonosításának. További információ a SAS használatával kapcsolatban: [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md) (Közös hozzáférésű jogosultságkóddal való hitelesítés a Service Bus használatával).

## <a name="event-consumers"></a>Eseményfelhasználók

Minden entitás, amely eseményadatokat olvas egy eseményközpontból, *eseményfelhasználó*. Minden Event Hubs-felhasználó az AMQP 1.0-munkameneten keresztül csatlakozik, amelyben az események azonnal megjelennek, amint elérhetővé válnak. Az ügyfélnek nem kell lekérdeznie az adatok rendelkezésre állását.

### <a name="consumer-groups"></a>Felhasználói csoportok

Az Event Hubs közzétételi/feliratkozási mechanizmusa *felhasználói csoportokon* keresztül engedélyezhető. A felhasználói csoport a teljes eseményközpont egyik nézete (állapot, pozíció vagy eltolás). A felhasználói csoportok révén több felhasználó alkalmazás rendelkezhet az eseménystream külön nézetével, és a többitől függetlenül saját tempójában és saját eltolásával olvashatja a streamet.

A streamfeldolgozási architektúrákban mindegyik alárendelt alkalmazás megfelel egy felhasználói csoportnak. Amennyiben eseményadatokat kíván írni egy hosszú távú tárhelyre, az adott tárhelyírási alkalmazás is egy felhasználói csoport. Az összetett eseményfeldolgozást már egy másik, külön felhasználói csoport végzi. A partíciókat csak a felhasználói csoportokon keresztül érheti el. Lehet, legfeljebb 5 egyidejű olvasók egy partíciót engedélyez fogyasztói csoportot; azonban **javasoljuk, hogy nincs-e csak egy aktív fogadó egy fogyasztói csoporton partíciók**. Az eseményközpontokban mindig van egy alapértelmezett felhasználói csoport, és akár 20 felhasználói csoportot is létrehozhat a standard szintű eseményközpontokban.

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

Ha egy olvasó lecsatlakozik egy partícióról, az újracsatlakozáskor az adott felhasználói csoportban az adott partíció utolsó olvasója által elküldött ellenőrzőpontnál kezdi az olvasást. Amikor az olvasó csatlakozik, átadja ezt az eltolásértéket az eseményközpontnak azon hely meghatározásához, ahol az olvasást kezdi. Az ellenőrzőpontok használatával az alárendelt alkalmazások így megjelölhetik az eseményeket „befejezettként”, valamint biztosítható a rugalmasság a különböző gépeken futó olvasók közötti feladatátvétel esetén. Lehetséges visszatérni a régebbi adatokhoz egy alacsonyabb értékű eltolás megadásával az ellenőrzőpontok használata során. Ezzel a mechanizmussal az ellenőrzőpontok használata rugalmasságot biztosít feladatátvétel esetén, és lehetővé teszi az eseménystream visszajátszását.

### <a name="common-consumer-tasks"></a>Általános felhasználói feladatok

Minden Event Hubs-felhasználó keresztül egy AMQP 1.0-munkamenet, egy állapot-kompatibilis kétirányú kommunikációs csatorna csatlakozzon. Mindegyik partíció rendelkezik AMQP 1.0-munkamenettel, ami megkönnyíti a partíció szerint elkülönített események átvitelét.

#### <a name="connect-to-a-partition"></a>Csatlakozás partícióhoz

Partíciókhoz való kapcsolódás esetén általános gyakorlat a bérlési mechanizmus használata az adott partíciók olvasói kapcsolatainak koordinálására. Így lehetséges, hogy az egyes felhasználói csoportokban minden partíció csak egyetlen aktív olvasóval rendelkezzen. Az ellenőrzőpontok használata, a bérlés, valamint az olvasók kezelése az [EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost) osztály használatával egyszerűsíthető .NET-ügyfelek esetén. Az Event Processor Host egy intelligens felhasználóügynök.

#### <a name="read-events"></a>Események olvasása

Miután egy AMQP 1.0-munkamenet és -hivatkozás meg lett nyitva egy adott partícióra vonatkozóan, az Event Hubs szolgáltatás kézbesíti az eseményeket az AMQP 1.0-ügyfél számára. Ez a kézbesítési mechanizmus nagyobb átviteli teljesítményt és kisebb késést biztosít a lekérésalapú mechanizmusoknál, amilyen például a HTTP GET. Az eseményeknek az ügyfél számára való elküldésekor minden eseményadat-példány fontos metaadatokat tartalmaz, például az eltolást és sorszámot, amelyek az eseménysorozat ellenőrzőpontokkal való jelölése során használhatók.

Eseményadatok:
* Eltolás
* Sorozat száma
* Törzs
* Felhasználói tulajdonságok
* Rendszertulajdonságok

Az eltolás kezelése a felhasználó felelőssége.

## <a name="capacity"></a>Kapacitás

Az Event Hubs hatékonyan méretezhető párhuzamos architektúrával rendelkezik, amelynek méretezésekor és skálázásakor számos fontos szempontot figyelembe kell venni.

### <a name="throughput-units"></a>Átviteli egységek

Az Event Hubs átviteli kapacitásának szabályozása *átviteli egységek* révén történik. Az átviteli egységek előre megvásárolt kapacitásegységek. Egy átviteli egység a következő kapacitást biztosítja:

* Bemenő forgalom: másodpercenként legfeljebb 1 MB vagy 1000 esemény (amelyik előbb teljesül)
* Kimenő forgalom: másodpercenként legfeljebb 2 MB

A megvásárolt átviteli egységek kapacitásán túli bemenő forgalmat a rendszer korlátozza, és [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) választ ad vissza. A kimenő forgalom nem eredményez korlátozási kivételeket, azonban a megvásárolt átviteli egységek kapacitására van korlátozva. Ha közzétételi sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt a névtérhez. Az átviteli egységek kezelheti a **méretezési** panel a névterek a [Azure-portálon](https://portal.azure.com). Átviteli egységek programozott módon is kezelhetők a [Event Hubs API-k](event-hubs-api-overview.md).

Az átviteli egységek óraalapú díjszabással rendelkeznek, és előre kell megvásárolni őket. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Az Event Hubs-névterekhez legfeljebb 20 átviteli egység vásárolható, és azok a névtér összes Event Hubs-központján megoszthatók.

További átviteli egységek vásárlásához vegye fel a kapcsolatot az Azure támogatási szolgálatával, ahol 20-as csomagokban vásárolhat átviteli egységeket 100 egységig. Efölött 100-as csomagokban vásárolhat átviteli egységeket.

Azt javasoljuk, hogy optimális mértékben átviteli egységek és partíciók elosztása. Egy partíció legfeljebb egy átviteli egységgel rendelkezhet. Az egyes eseményközpontokban az átviteli egységek száma nem haladhatja meg a partíciók számát.

Részletes információk az Event Hubs díjszabásáról: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Következő lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag][Event Hubs tutorial]
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Event Hubs – minták][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Event Hubs – minták]: https://github.com/Azure/azure-event-hubs/tree/master/samples
