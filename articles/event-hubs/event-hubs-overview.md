<properties 
    pageTitle="Az Azure Event Hubs áttekintése | Microsoft Azure"
    description="Az Azure Event Hubs bemutatása és áttekintése."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm" />

# Azure Event Hubs – áttekintés

Számos modern megoldás igyekszik alkalmazkodó ügyfélélményt biztosítani, vagy a termékeket a folyamatos visszajelzések és automatikusan működő telemetria segítségével fejleszteni. Az ilyen megoldások azzal a kihívással szembesülnek, hogy miként lehet nagyon nagy mennyiségű, több egyidejű közzétevőtől származó információt biztonságosan és megbízhatóan feldolgozni. A Microsoft Azure Event Hubs egy felügyelt platformszolgáltatás, amely alapot biztosít a nagyléptékű adatbevitelhez a forgatókönyvek széles választékában. Ilyen forgatókönyvek például a viselkedéskövetés a mobilalkalmazásokban, a forgalmi információk gyűjtése a webfarmokról, a játékbeli események rögzítése a konzolos játékokban, vagy az ipari gépekről vagy csatlakoztatott járművekről gyűjtött telemetriaadatok. Az Event Hubs gyakran tölti be az eseményfolyamatok „bejárati ajtajának” a szerepét a megoldásarchitektúrákban, mely szerepet gyakran nevezik *eseménybetöltőnek*. Az eseménybetöltő egy olyan összetevő vagy szolgáltatás, amely az esemény-közzétevők és az eseményfelhasználók közé ékelődve elkülöníti az eseménystream létrehozását az események felhasználásától.

![Event Hubs](./media/event-hubs-overview/IC759856.png)

Az Azure Event Hubs egy eseményfeldolgozási szolgáltatás, amely a felhőbe irányuló, nagy léptékű esemény- és telemetriabevitelt biztosít alacsony késéssel és nagy megbízhatósággal. A szolgáltatás más alárendelt szolgáltatásokkal együtt alkalmazva különösen hatékony az alkalmazások kialakításában, a felhasználói élmények vagy munkafolyamatok feldolgozásában, és az eszközök internetes hálózatát (IoT) érintő forgatókönyvekben. Az Event Hubs üzenetstream-kezelési képességet biztosít, és jóllehet az eseményközpontok az üzenetsorokhoz és témakörökhöz hasonló entitások, olyan tulajdonságokkal rendelkeznek, amelyek nagyon eltérőek a hagyományos vállalati üzenetkezeléstől. A vállalati üzenetkezelési forgatókönyvek általában számos kifinomult képességet igényelnek, mint például az előkészítés, a kézbesítetlen levelek kezelése, a tranzakciók támogatása és az erős kézbesítési biztosítékok, miközben a befelé irányuló események fő problémája az eseménystreamekre jellemző nagy mennyiség és a feldolgozáshoz szükséges rugalmasság. Az Event Hubs képességei ezért eltérnek a Service Bus-témaköröktől, mivel kimondottan a nagy mennyiségre és eseményfeldolgozási forgatókönyvekre vannak optimalizálva. Az Event Hubs így nem valósít meg bizonyos üzenetkezelési képességeket, amelyek a témakörök esetén elérhetők. Amennyiben szüksége van ezekre a képességekre, továbbra is a témakörök használata az optimális választás.

Az eseményközpontok az Event Hubsban névterek szintjén hozhatók létre, hasonlóan a Service Bus üzenetsoraihoz és témaköreihez. Az Event Hubs AMQP-t és HTTP-t használ elsődleges API felületként. Az alábbi ábrán az Event Hubs és a Service Bus közötti kapcsolat látható.

![Event Hubs](./media/event-hubs-overview/IC741188.png)

## Fogalmi áttekintés

Az Event Hubs üzenetstreamelést biztosít egy particionált felhasználói mintán keresztül. Az üzenetsorok és témakörök a [versengő felhasználó](https://msdn.microsoft.com/library/dn568101.aspx) modellt használják, amelyben mindegyik felhasználó ugyanabból az üzenetsorból vagy erőforrásból próbál olvasni. Az erőforrásokért való versengés végül összetettségi és skálázhatósági korlátokat eredményez a streamfeldolgozási alkalmazásokban. Az Event Hubs egy particionált felhasználói mintát használ, amelyben mindegyik felhasználó az üzenetstream csak egy adott részhalmazát, vagyis partícióját olvassa. Ez a minta biztosítja a horizontális skálázhatóságot az eseményfeldolgozáshoz, és egyéb, streamközpontú szolgáltatásokat is nyújt, amelyek az üzenetsorokban vagy témakörökben nem érhetők el.

### Partíciók

A partíció események egy rendezett sorozata az eseményközpontban. Ha új esemény érkezik, az a sorozat végére kerül. A partíció elképzelhető egy „véglegesítési naplóként”.

![Event Hubs](./media/event-hubs-overview/IC759857.png)

A partíciók az eseményközpont szintjén konfigurált megőrzési időtartamig őrzik meg az adatokat. Ez a beállítás az eseményközpont összes partíciójára érvényes. Az események időalapon évülnek el – nem törölhetők külön. Az eseményközpontok több partíciót tartalmaznak. Mindegyik partíció független, és a saját adatsorozatát tartalmazza. Ennek eredményeként az egyes partíciók gyakran különböző ütemben nőnek.

![Event Hubs](./media/event-hubs-overview/IC759858.png)

A partíciók száma az eseményközpont létrehozásakor határozható meg, és ez a szám 2 és 32 közé eshet (az alapértelmezett szám a 4). A partíció egy adatrendezési mechanizmus, és jobban köthető a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamosság mértékéhez, mint az Event Hubs átviteli kapacitásához. Így az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. Az eseményközpontok létrehozását követően a partíciók száma nem módosítható, ezért a számot a hosszú távú skálázási igények figyelembe vételével érdemes meghatároznia. A 32-es partíciós korlát növelhető – ehhez vegye fel a kapcsolatot a Service Bus csapatával.

Jóllehet a partíciók azonosíthatók, és közvetlenül is küldhetők adatok rájuk, jobb elkerülni az adatok adott partíciókra való küldését. Helyette használhatja az [Esemény-közzétevő](#event-publisher) és a [Közzétevői házirend](#capacity-and-security) című szakaszban bemutatott magasabb szintű szerkezeteket.

Az Event Hubs környezetében az üzenetekre *eseményadat* néven hivatkozunk. Az eseményadatok az esemény törzsét, a felhasználó által definiált tulajdonságcsomagot és az eseménnyel kapcsolatos különféle metaadatokat tartalmazzák, például az esemény eltolását a partícióban vagy a számát a streamsorozatban. A partíciók eseményadatok sorozatát tartalmazzák.

## Esemény-közzétevő

Minden entitás, amely eseményeket vagy adatokat küld egy eseményközpontnak *esemény-közzétevőnek* számít. Az esemény-közzétevők a HTTPS vagy az AMQP 1.0 használatával tehetik közzé az eseményeket. Az esemény-közzétevők egy közös hozzáférésű jogosultságkód- (SAS-) token használatával azonosítják magukat az eseményközpontok felé, és rendelkezhetnek egyedi azonosítóval vagy közös SAS-tokennel is, a forgatókönyv követelményeitől függően.

További információ a SAS használatával kapcsolatban: [Shared Access Signature Authentication with Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md) (Közös hozzáférésű jogosultságkóddal való hitelesítés a Service Bus használatával).

### Általános közzétevői feladatok

Ez a szakasz az esemény-közzétevők gyakori feladatait ismerteti.

#### SAS-token beszerzése

A közös hozzáférésű jogosultságkód (SAS) az Event Hubs hitelesítési mechanizmusa. A Service Bus biztosítja a SAS-házirendeket a névtér és az eseményközpont szintjén. A SAS-tokent egy SAS-kulcsból hozza létre a rendszer, és egy URL SHA-kivonata egy meghatározott formátumban kódolva. A kulcs neve (házirend) és a token együttes használatával a Service Bus képes újra létrehozni a kivonatot, és így azonosítani a küldőt. Az esemény-közzétevők SAS-tokenje általában egy adott eseményközpontban, csak **küldési** jogosultságokkal hozható létre. Ez a SAS-tokenes URL-mechanizmus az alapja a közzétevők a közzétevői házirendben bevezetett azonosításának. További információ a SAS használatával kapcsolatban: [Shared Access Signature Authentication with Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md) (Közös hozzáférésű jogosultságkóddal való hitelesítés a Service Bus használatával).

#### Esemény közzététele

Az eseményeket az AMQP 1.0 vagy HTTPS használatával teheti közzé. A Service Bus az [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) osztályt biztosítja az események a .NET-ügyfelekről az eseményközpontokba való közzétételéhez. Egyéb futtatókörnyezetek és platformok esetén használhatja bármelyik AMQP 1.0-ügyfelet, ilyen például az [Apache Qpid](http://qpid.apache.org/). Az eseményeket közzéteheti egyenként vagy kötegelve is. Az egyes közzétételekre (eseményadat-példány) 256 KB-os korlát érvényes, függetlenül attól, hogy önálló vagy kötegelt közzétételről van-e szó. Az ennél nagyobb események közzététele hibát eredményezhet. Az ajánlott eljárás, hogy a közzétevők ne tudjanak a partíciókról az eseményközpontban, és csupán egy *partíciókulcsot* (ismertetése a következő szakaszban), vagy az azonosságukat kelljen megadniuk a SAS-token használatával.

Az AMQP vagy HTTPS használata a használati forgatókönyvtől függ. Az AMQP használatához ki kell alakítani egy állandó kétirányú szoftvercsatornát az TLS vagy SSL/TLS mellett. Ez a hálózati forgalom szempontjából erőforrás-igényes művelet lehet, ennek azonban csak az AMQP-munkamenet elején kell megtörténnie. A HTTPS alacsonyabb kezdeti többletterhelést okoz, azonban további SSL-többletterhelést okoz minden egyes kérés esetében. Események gyakran közzétevő közzétevők esetén az AMQP jelentős teljesítmény-, késés- és átvitelbeli megtakarítást biztosít.

### Partíciókulcs

A partíciókulcs egy érték, amelynek használatával a beérkező eseményadatok leképezhetők adott partíciókra az adatszervezet céljainak megfelelően. A partíciókulcs az eseményközpontnak átadott, a küldő által megadott érték. A feldolgozása egy statikus kivonatoló függvénnyel történik, amelynek az eredménye létrehozza a partíció-hozzárendelést. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ. Partíciókulcsok használatakor az esemény-közzétevő csak a partíciókulcsát ismeri, a partíciót nem, amelyre az esemény közzé lesz téve. A kulcs és a partíció szétválasztása révén a küldőnek nem szükséges behatóan ismernie az alárendelt feldolgozási folyamatokat és az események tárolását. A partíciókulcsok fontosak az adatok rendezéséhez az alárendelt feldolgozáshoz, azonban alapvetően függetlenek maguktól a partícióktól. Az eszközszintű vagy egyedi felhasználói identitás remek partíciókulcs lehet, de más tulajdonságok, például a földrajzi hely alapján szintén lehetséges az események csoportosítása egyetlen partícióra. Az alábbi képen olyan eseményküldők láthatóak, akik partíciókulcsok használatával rögzítik az eseményeket adott partíciókra.

![Event Hubs](./media/event-hubs-overview/IC759859.png)

Az Event Hubs biztosítja, hogy az egyazon partíciókulcs-értékkel rendelkező események sorrendben érkezzenek meg, és egyazon partícióra kerüljenek. Fontos, hogy amennyiben partíciókulcsok használata közzétevői házirendekkel (lásd a következő szakaszban) együtt történik, a közzétevő identitásának és a partíciókulcs értékének egyeznie kell. Különben hiba történik.

### Eseményfelhasználó

Minden entitás, amely eseményadatokat olvas egy eseményközpontból, eseményfelhasználó. Az eseménystreamet mindegyik eseményfelhasználó partíciókon keresztül olvassa a felhasználói csoportban. Mindegyik partíció csak egyetlen aktív olvasóval rendelkezhet egy adott pillanatban. Minden Event Hubs-felhasználó az AMQP 1.0-munkameneten keresztül csatlakozik, amelyben az események azonnal megjelennek, amint elérhetővé válnak. Az ügyfélnek nem kell lekérdeznie az adatok rendelkezésre állását.

#### Felhasználói csoportok

Az Event Hubs közzétételi/feliratkozási mechanizmusa felhasználói csoportokon keresztül engedélyezhető. A felhasználói csoport a teljes eseményközpont egyik nézete (állapot, pozíció vagy eltolás). A felhasználói csoportok révén több felhasználó alkalmazás rendelkezhet az eseménystream külön nézetével, és a többitől függetlenül saját tempójában és saját eltolásával olvashatja a streamet. A streamfeldolgozási architektúrákban mindegyik alárendelt alkalmazás megfelel egy felhasználói csoportnak. Amennyiben eseményadatokat kíván írni egy hosszú távú tárhelyre, az adott tárhelyírási alkalmazás is egy felhasználói csoport. Az összetett eseményfeldolgozást egy másik külön felhasználói csoport végzi. A partíciókat csak a felhasználói csoportokon keresztül érheti el. Az eseményközpontokban mindig van egy alapértelmezett felhasználói csoport, és akár 20 felhasználói csoportot is létrehozhat a standard szintű eseményközpontokban.

Az alábbiakban néhány példa látható felhasználói csoportok URI-szabályaira:

    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>

Az alábbi ábrán az eseményfelhasználók láthatóak a felhasználói csoportokban.

![Event Hubs](./media/event-hubs-overview/IC759860.png)

#### Streameltolások

Az eltolás egy esemény pozíciója a partíción belül. Az eltolásokat tekintheti ügyféloldali kurzorként. Az eltolás az esemény bájtalapú sorszáma. Ez lehetővé teszi az eseményfelhasználó (olvasó) számára az eseménystream egy olyan pontjának megadását, ahol az események olvasását kezdeni szeretné. Az eltolás megadható időbélyegzőként vagy eltolásértékként. A felhasználók felelőssége saját eltolásértékeik tárolása az Event Hubs szolgáltatáson kívül.

![Event Hubs](./media/event-hubs-overview/IC759861.png)

A partíciókon belül mindegyik esemény rendelkezik eltolással. A felhasználók az eltolás segítségével jelenítik meg a helyet az egyes partíciók eseménysorozatában. Az eltolás számként vagy időbélyegértékként adható át az eseményközpontnak, amikor egy olvasó csatlakozik.

#### Ellenőrzőpontok használata

Az *ellenőrzőpontok használatával* az olvasók megjelölhetik vagy véglegesíthetik pozíciójukat a partíciók eseménysorozatában. Az ellenőrzőpontok használata a felhasználó felelőssége, és partíciónkénti alapon történik a felhasználói csoportban. Ez azt jelenti, hogy mindegyik felhasználói csoport esetében mindegyik partícióolvasónak nyilván kell tartania aktuális pozícióját az eseménystreamben, és tájékoztathatja a szolgáltatást, amikor az adatstreamet befejezettnek tekinti. Ha egy olvasó lecsatlakozik egy partícióról, az újracsatlakozáskor az adott felhasználói csoportban az adott partíció utolsó olvasója által elküldött ellenőrzőpontnál kezdi az olvasást. Amikor az olvasó csatlakozik, átadja ezt az eltolásértéket az eseményközpontnak azon hely meghatározásához, ahol az olvasást kezdi. Az ellenőrzőpontok használatával az alárendelt alkalmazások így megjelölhetik az eseményeket „befejezettként”, valamint biztosítható a rugalmasság a különböző gépeken futó olvasók közötti feladatátvétel esetén. Mivel a rendszer az eseményközpont létrehozásakor meghatározott megőrzési ideig őrzi meg az eseményadatokat, vissza lehet térni a régebbi adatokhoz egy alacsonyabb értékű eltolás megadásával az ellenőrzőpontok használata során. Ezzel a mechanizmussal az ellenőrzőpontok használata egyaránt lehetővé teszi a rugalmasság biztosítását feladatátvétel esetén, és az eseménystream felügyelt visszajátszását.

#### Általános felhasználói feladatok

Ez a szakasz az Event Hubs-eseményfelhasználók vagy -olvasók által végzett gyakori feladatokat ismerteti. Minden Event Hubs-felhasználó az AMQP 1.0-n keresztül csatlakozik. Az AMQP 1.0 egy munkamenet- és állapotközpontú kétirányú kommunikációs csatorna. Mindegyik partíció rendelkezik AMQP 1.0 hivatkozási munkamenettel, ami megkönnyíti a partíció szerint elkülönített események átvitelét.

##### Csatlakozás partícióhoz

Az eseményközpontban lévő események felhasználásához a felhasználóknak csatlakozniuk kell egy partícióhoz. Amint azt korábban említettük, a partíciók elérése mindig felhasználói csoportokon keresztül történik. A particionált felhasználói modell részeként egy időben csak egyetlen olvasó lehet aktív egy partíción egy adott felhasználói csoportban. Általános gyakorlat a partíciókhoz való közvetlen kapcsolódás esetén bérlési mechanizmus használata az adott partíciók olvasói kapcsolatainak koordinálására. Így lehetséges, hogy az egyes felhasználói csoportokban minden partíció csak egyetlen aktív olvasóval rendelkezzen. Fontos feladat a sorrendekben elfoglalt pozíciók kezelése, ami az ellenőrzőpontok használatával hajtható végre. Ez a funkció az [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) osztály használatával egyszerűsíthető a .NET-ügyfelek esetén. Az [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) egy intelligens felhasználóügynök, amelyet a következő szakasz ismertet.

##### Események olvasása

Miután egy AMQP 1.0-munkamenet és -hivatkozás meg lett nyitva egy adott partícióra vonatkozóan, az Event Hubs szolgáltatás kézbesíti az eseményeket az AMQP 1.0-ügyfél számára. Ez a kézbesítési mechanizmus nagyobb átviteli teljesítményt és kisebb késést biztosít a lekérésalapú mechanizmusoknál, amilyen például a HTTP GET. Az eseményeknek az ügyfél számára való elküldésekor minden eseményadat-példány fontos metaadatokat tartalmaz, például az eltolást és sorszámot, amelyek az eseménysorozat ellenőrzőpontokkal való jelölése során használhatók.

![Event Hubs](./media/event-hubs-overview/IC759862.png)

Az Ön felelőssége, hogy az eltolást oly módon kezelje, amely a legjobban támogatja a streamfeldolgozás folyamatának kezelését.

## Kapacitás és biztonság

Az Event Hubs egy rendkívüli mértékben skálázható párhuzamos architektúra a bemenő stream kezelésére. Mint ilyen, több kulcsfontosságú szempontot is figyelembe kell venni az Event Hubs-alapú megoldások méretezése és skálázása során. Az első kapacitásvezérlő az *átviteli egység*, amelyet a következő szakasz ismertet.

### Átviteli egységek

Az Event Hubs átviteli kapacitásának szabályozása az átviteli egységek révén történik. Az átviteli egységek előre megvásárolt kapacitásegységek. Egy átviteli egység a következőket foglalja magában:

- Bemenő forgalom: másodpercenként legfeljebb 1 MB vagy 1000 esemény.

- Kimenő forgalom: másodpercenként legfeljebb 2 MB.

A befelé irányuló forgalmat a megvásárolt átviteli egységek mennyisége által biztosított kapacitás szabályozza. Az ezen felül küldött adatmennyiség esetén a rendszer a „kvóta túllépve” kivételt adja vissza. Ez a mennyiség másodpercenként 1 MB vagy 1000 esemény, amelyiket a forgalom előbb eléri. A kimenő forgalom nem eredményez szabályozási kivételeket, azonban a megvásárolt átviteli egységek által biztosított adatátviteli kapacitásra van korlátozva, ami másodpercenként 2 MB átviteli egységenként. Ha közzétételi-sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt ahhoz a névtérhez, amelyben az eseményközpont létre lett hozva. További átviteli egységek beszerzéséhez módosíthatja a [klasszikus Azure portál][] a **Scale** (Méret) lapján található **Namespaces** (Névterek) lap megfelelő beállítását. A beállítást az Azure API-k használatával is módosíthatja.

Amíg a partíció egy adatrendezési fogalom, addig az átviteli egység tisztán kapacitási. Az átviteli egységek óraalapú díjszabással rendelkeznek, és előre kell megvásárolni őket. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Az Event Hubs-névterekhez legfeljebb 20 átviteli egység vásárolható, és Azure-fiókonként is legfeljebb 20 átviteli egység szerezhető be. Az átviteli egységek közösen használhatóak egy adott névtér összes eseményközpontjában.

Az átviteli egységek kiosztása az elérhetőség alapján történik, és előfordulhat, hogy nem vásárolhatók meg azonnal. Amennyiben egy meghatározott kapacitásra van szüksége, javasoljuk, hogy az átviteli egységeket időben vásárolja meg. Amennyiben több mint 20 átviteli egységre van szüksége, vegye fel a kapcsolatot az Azure ügyfélszolgálatával, és kötelezettségvállalás ellenében 20-as csomagokban vásárolhat átviteli egységeket az első 100 átviteli egységig. Efölött 100-as csomagokban vásárolhat átviteli egységeket.

Javasoljuk, hogy az átviteli egységek és partíciók mennyiségét gondosan egyensúlyozza ki, hogy optimális mértékben vegye igénybe az Event Hubs szolgáltatást. Egy partíció legfeljebb egy átviteli egységgel rendelkezhet. Az egyes eseményközpontban az átviteli egységek száma nem haladhatja meg a partíciók számát.

Részletes díjszabási információk: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

### Közzétevői házirend

Az Event Hubs lehetővé teszi az esemény-közzétevők részletes szabályozását a *közzétevői házirendek* révén. A közzétevői házirend futásidejű szolgáltatások összessége, amelyek célja, hogy nagy számú független esemény-közzétevőt tegyen lehetővé. A közzétevői házirendek használatával mindegyik közzétevő a saját egyedi azonosítóját használja, amikor eseményeket tesz közzé egy eseményközpontban az alábbi mechanizmust használva:

    //<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>

Nem kell előre létrehoznia a közzétevők neveit, azoknak azonban egyezniük kell az esemény közzétételekor használt SAS-tokennel a független közzétevő-azonosságok biztosítása érdekében. További információ a SAS használatával kapcsolatban: [Shared Access Signature Authentication with Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md) (Közös hozzáférésű jogosultságkóddal való hitelesítés a Service Bus használatával). A közzétevői házirendek használatakor a **PartitionKey** értéke a közzétevő neve lesz. A megfelelő működéshez ezeknek az értékeknek egyezniük kell.

## Összefoglalás

Az Azure Event Hubs egy nagy kapacitású esemény- és telemetria-feldolgozási szolgáltatás, amelynek használatával általános alkalmazás- és felhasználói munkafolyamat-figyelés végezhető bármilyen léptékben. Az alacsony késésű és nagy méretű közzétételi-feliratkozási képességek biztosításával az Event Hubs az „első lépcsőfok” a Big Data kezelése irányában. A közzététel-alapú identitás- és visszavonási listák révén ezek a képességek kiterjeszthetőek az eszközök internetes hálózatát (IoT) érintő általános forgatókönyvekre is. További információ az Event Hubs-alkalmazások fejlesztésével kapcsolatban: [Event Hubs programming guide](event-hubs-programming-guide.md) (Event Hubs programozási útmutató).

## Következő lépések

Most, hogy megismerkedett az Event Hubshoz kapcsolódó fogalmakkal, továbbléphet a következő forgatókönyvekre:

- Bevezetés az [Event Hubs használatába oktatóanyag].
- [Az Event Hubsot használó teljes mintaalkalmazás].
- [Üzenetsor-kezelési megoldás] a Service Bus által kezelt üzenetsorok használatával.

[klasszikus Azure portál]: http://manage.windowsazure.com
[Event Hubs használatába oktatóanyag]: event-hubs-csharp-ephcs-getstarted.md
[Az Event Hubsot használó teljes mintaalkalmazás]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[Üzenetsor-kezelési megoldás]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 



<!--HONumber=sep16_HO1-->


