---
title: "Ajánlott eljárások az Azure Service Bus teljesítményének javítására |} Microsoft Docs"
description: "Ismerteti, hogyan használható a Service Bus a teljesítmény optimalizálása, amikor a közvetítőalapú üzenetek cseréjét."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e756c15d-31fc-45c0-8df4-0bca0da10bb2
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: aba53fcadb9cefa70afc175dd02e4723eb6e5f5d
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Ajánlott eljárások használatával a Service Bus üzenetkezelés teljesítménnyel kapcsolatos fejlesztések

Ez a cikk ismerteti, hogyan használható [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) teljesítményének optimalizálásához, amikor a közvetítőalapú üzenetek cseréjét. Ez a cikk első része ismerteti a különböző mechanizmusok, felajánlott, a teljesítmény növelése érdekében. A második rész a Service Bus használatával úgy, hogy egy adott szituációban a lehető legjobb teljesítményt tud nyújtani nyújt útmutatást.

Ez a témakör teljes az "ügyfél" kifejezés minden entitás, aki hozzáfér a Service Bus. Egy ügyfél a szerepkör a feladó vagy egy címzett is igénybe vehet. A "feladó" kifejezés egy Service Bus-üzenetsor vagy témakör ügyfél, amely üzeneteket küld egy Service Bus-üzenetsor vagy témakör előfizetést. A "címzett" kifejezés egy Service Bus várólista vagy előfizetés ügyfél fogad üzeneteket egy Service Bus-üzenetsorba, vagy az előfizetés.

Ezek a szakaszok, akkor a program teljesítmény használja a Service Bus számos fogalmat.

## <a name="protocols"></a>Protokollok

A Service Bus lehetővé teszi, hogy az ügyfelek számára három protokollok segítségével üzenetek küldése és fogadása:

1. Speciális üzenetsor-kezelési protokoll (AMQP)
2. A Service Bus üzenetkezelés protokoll (SBMP)
3. HTTP

AMQP és SBMP hatékonyabbak, mert azok a Service Bus kapcsolatot tart, mindaddig, amíg az üzenetkezelési gyárból létezik. Megvalósít továbbá kötegelés és prefetching. Kivéve, ha explicit módon azt korábban említettük, az összes tartalom ebben a témakörben azt feltételezi, hogy az AMQP vagy SBMP használata.

## <a name="reusing-factories-and-clients"></a>Újbóli felhasználása a gyárat, illetve az ügyfelek

Service Bus-ügyfélalkalmazást objektumok, például a [QueueClient] [ QueueClient] vagy [MessageSender][MessageSender], keresztül jönnek létre a [MessagingFactory] [ MessagingFactory] objektum, amely belső felügyeleti kapcsolatok is biztosít. Meg nem után zárja be a üzenetkezelési gyárat vagy várólista, a témakör és előfizetés-ügyfelek elküldeni egy üzenetet, és ezután hozza létre azokat a következő üzenet küldésekor. Egy üzenetkezelési gyárból bezárása törli a kapcsolatot a Service Bus szolgáltatással, és egy új kapcsolat jön létre, amikor a gyári újbóli létrehozása. Egy drága művelet, amely a azonos factory és az ügyfél objektumok több műveletben újra használatával elkerülheti, hogy kapcsolatot létesít. Biztonságosan használhatja a [QueueClient] [ QueueClient] üzenetküldésre egyidejű aszinkron műveletek és több szál objektum. 

## <a name="concurrent-operations"></a>Párhuzamos műveletek

Egy műveletet (Küldés, kapni, törlés, stb.) némi időt vesz igénybe. Most a Service Bus szolgáltatás mellett a kérelem és a válasz késleltetése a művelet feldolgozása tartalmaz. Az idő alatt műveletek számának növeléséhez, műveletek végre kell hajtani egyidejűleg. Ez a különféle módokon teheti meg:

* **Az aszinkron műveletek**: az ügyfél ütemezi műveletek aszinkron műveletek elvégzésével. A következő kérés elindult az előző kérelem befejeződése előtt. A következő egy példa egy aszinkron küldés művelet:
  
 ```csharp
  BrokeredMessage m1 = new BrokeredMessage(body);
  BrokeredMessage m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  Itt látható egy példa egy aszinkron fogadás művelet:
  
  ```csharp
  Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  
  Task.WaitAll(receive1, receive2);
  Console.WriteLine("All messages received");
  
  async void ProcessReceivedMessage(Task<BrokeredMessage> t)
  {
    BrokeredMessage m = t.Result;
    Console.WriteLine("{0} received", m.Label);
    await m.CompleteAsync();
    Console.WriteLine("{0} complete", m.Label);
  }
  ```

* **Több előállítók**: ugyanazon gyár által létrehozott összes ügyfél (feladók mellett a fogadók) megosztani egy TCP-kapcsolatot. A maximális átviteli sebesség Lépkedjen végig a TCP-kapcsolat műveletek száma korlátozza. Az átviteli sebesség egyetlen Factory elérhető jelentősen is különbözhet a TCP körbejárási alkalommal és az üzenet mérete. Nagyobb átviteli sebesség beszerzéséhez használja több üzenetkezelési gyárat.

## <a name="receive-mode"></a>Fogadás módban

A várólista vagy előfizetés ügyfél létrehozásakor megadhatja egy fogadási módot: *betekintés-lock* vagy *fogadásához és törléséhez*. Az alapértelmezett fogadási mód van [PeekLock][PeekLock]. Ebben a módban üzemel, az ügyfél üzenetet fogadni a Service Bus kérést küld. Miután az ügyfél a üzenetet kapott, küld az üzenet befejezéséhez.

Amikor a fogadás mód beállítása [ReceiveAndDelete][ReceiveAndDelete], két lépést a rendszer kombinálja az egy kérelemhez. Ez csökkenti a műveletek összesített száma, és javíthatja a teljes üzenet teljesítményt. Ez jobb teljesítménye kockázatára üzenetek elvesztése származnak.

A Service Bus fogadni-és-delete művelet esetén nem támogatja a tranzakciókat. Ezenkívül betekintés-lock szemantikáját szükségesek az összes olyan forgatókönyvet, amelyben az ügyfél szeretné késleltető vagy [kézbesítetlen levelek](service-bus-dead-letter-queues.md) üzenetet.

## <a name="client-side-batching"></a>Ügyféloldali kötegelés

Ügyféloldali kötegelés lehetővé teszi egy üzenet küldhető egy bizonyos ideig késleltethető üzenetsor vagy témakör ügyfél. Ha az ügyfél elküldi a további üzeneteket ebben az időszakban, továbbítja a kötegek található üzenetek. Ügyféloldali kötegelés is következtében a várólista vagy előfizetés ügyfél több kötegelt **Complete** kérelmek az egy kérelemhez. Kötegelés érhető el csak aszinkron **küldése** és **Complete** műveletek. Szinkron műveletek a Service Bus szolgáltatás azonnal kerülnek. Kötegelés nem fordulnak elő a betekintés és fogadási műveletek, és nem az ügyfélre kötegelés fordul elő.

Alapértelmezés szerint az ügyfél egy kötegelt időköz 20ms használja. A időköze módosítható úgy, hogy a [BatchFlushInterval] [ BatchFlushInterval] tulajdonság az üzenetkezelési gyárból létrehozása előtt. Ez a beállítás az adat-előállító által létrehozott összes ügyfélre vonatkozik.

Tiltsa le a kötegelés, állítsa be a [BatchFlushInterval] [ BatchFlushInterval] tulajdonságot **TimeSpan.Zero**. Példa:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Kötegelés nem befolyásolja a számlázható üzenetkezelési műveletek számát, és csak a Service Bus-ügyfél protokoll érhető el. A HTTP protokoll nem támogatja a kötegelés.

## <a name="batching-store-access"></a>Kötegelési áruházhoz való hozzáférés

A várólista, a témakör és előfizetés átviteli sebesség növelése érdekében a Service Bus több üzenetet kötegek, a belső tárolójába írásakor. Ha engedélyezve van, az üzenetsor vagy témakör, a üzeneteket írna a tárolóba kötegelni lesz. Ha engedélyezve van a várólista vagy előfizetés, a üzenetek törlése a tárolóból kötegelni lesz. Egy entitás kötegelt áruházhoz való hozzáférés engedélyezve van, ha a Service Bus késlelteti tároló írási művelet kapcsolatban, hogy az entitás legfeljebb 20 MS által. 

> [!NOTE]
> Nem áll fenn az elveszett üzenetek kötegelés, még akkor is, ha hiba történik a Service Bus egy 20ms kötegelési időköz végén. 

Ez az időtartam alatt során felmerülő további tárolási műveletek kerülnek a kötegelt. Tároló hozzáférés csak befolyásolja kötegelni **küldése** és **Complete** műveletekkel; fogadási műveletek nem érinti. Kötegelt áruházhoz való hozzáférés entitás tulajdonság értéke. Kötegelt áruházhoz való hozzáférés engedélyezése az összes entitások közötti kötegelés következik be.

Amikor egy új várólistát, üzenettémakört vagy előfizetést hoz létre, az alapértelmezés szerint engedélyezve van a kötegelt áruházhoz való hozzáférés. A kötegelt áruházhoz való hozzáférés letiltásához állítsa be a [EnableBatchedOperations] [ EnableBatchedOperations] tulajdonságot **hamis** az entitás létrehozása előtt. Példa:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Kötegelt áruházhoz való hozzáférés nem befolyásolja a számlázható üzenetkezelési műveletek számát, és üzenetsor, témakör vagy előfizetés tulajdonsága. A fogadás mód és az ügyfél és a Service Bus szolgáltatás között használt protokoll függetlenül is.

## <a name="prefetching"></a>Prefetching

[Prefetching](service-bus-prefetch.md) lehetővé teszi a várólista vagy előfizetés ügyfél további üzeneteket betölteni a szolgáltatásból, a fogadási művelet végrehajtása során. Az ügyfél a helyi gyorsítótárban tárolja ezeket az üzeneteket. A gyorsítótár méretét határozza meg a [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] vagy [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] tulajdonságait. Minden ügyfél, amely lehetővé teszi, hogy prefetching tart fenn a saját gyorsítótárába. A gyorsítótár nincsenek megosztva, az ügyfelek között. Ha az ügyfél indít el egy fogadási művelet és a gyorsítótár üres, a szolgáltatás továbbítja az üzenetkötegek. A Köteg mérete eléri a 256 KB-os vagy a gyorsítótár méretét, attól függően, kisebb. Ha az ügyfél indít el egy fogadási művelet, és a gyorsítótár tartalmaz egy üzenetet, az üzenet szükséges a gyorsítótárból.

Egy üzenet prefetched van, ha a szolgáltatás zárolja a prefetched üzenet. Ezzel a prefetched üzenet egy másik címzett nem fogadható. Ha a címzett nem tudja végrehajtani az üzenetet, a zárolás lejárata előtt, akkor az üzenet többi fogadó számára elérhetővé válnak. Az üzenet prefetched példányát a gyorsítótárban marad. A fogadó, amely a gyorsítótárban található példányát a lejárt akkor kapnak kivétel, amikor megpróbálja végrehajtani az üzenet. Alapértelmezés szerint az üzenet zár 60 másodperc múlva lejár. Ez az érték 5 perc kiterjeszthető. Lejárt üzenetek használat megelőzése érdekében a gyorsítótár méretét mindig kisebbnek kell lennie a zárolási időtúllépés belül ügyfél által felhasználható üzenetek számánál.

Az alapértelmezett zárolás lejárati ideje 60 másodperc használatakor jó értékének [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] van 20 alkalommal a maximális feldolgozási sebességet az összes fogadó gyár. Például olyan adat-előállítóval 3 fogadók hoz létre, és mindegyik fogadó másodpercenként legfeljebb 10 üzenetek feldolgozásához. A lehívott száma nem lehet hosszabb 20 X 3 X 10 = 600. Alapértelmezés szerint [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] értéke 0, ami azt jelenti, hogy a szolgáltatás további üzenetek már beolvasott.

A teljes teljesítményt, vagy egy előfizetés üzenetek prefetching növeli, mivel csökkenti a teljes számát üzenetművelet, vagy a kiszolgálókkal való adatváltások számát. Az első üzenet beolvasása, azonban hosszabb ideig tart (mert az nagyobb üzenet mérete). Prefetched üzenetek fogadása gyorsabb lesz, mert ezek az üzenetek a az ügyfél már töltve.

Az üzenet idő a működés közbeni (TTL) tulajdonsága be van jelölve a kiszolgáló által a kiszolgáló elküldi az ügyfélnek az üzenet időpontjában. Az ügyfél nem ellenőrzi az üzenet TTL tulajdonsága az üzenet fogadásakor. Ehelyett az üzenet fogadhatók akkor is, ha az üzenet TTL-t ment, amíg az üzenet volt az ügyfél gyorsítótárába.

Prefetching nem befolyásolja a számlázható üzenetkezelési műveletek számát, és csak a Service Bus-ügyfél protokoll érhető el. A HTTP protokoll nem támogatja a prefetching. Prefetching cím is szinkron és aszinkron műveletek fogadására.

## <a name="express-queues-and-topics"></a>Express üzenetsorok és témakörök

Expressz entitások engedélyezése a nagy mennyiségre és csökkentett késési igényű helyzetekben, és csak a standard szintű üzenetkezelési réteg támogatottak. A létrehozott entitásokat [prémium névterekben](service-bus-premium-messaging.md) nem támogatják a kifejezett beállítást. Az expressz entitások Ha egy üzenetet küld egy üzenetsor vagy témakör, az üzenet nem azonnal tárolja az üzenetküldési tárolóban. Ehelyett a memóriában vannak gyorsítótárazva. Ha egy üzenetet a várólistában lévő marad csak néhány másodperc, automatikusan írás állandó tárolóra, így a nem tervezett kimaradás miatti veszteségeinek elleni védelem azt. Az üzenet egy memória gyorsítótárba írása növeli az adatátviteli sebességet, és csökkenti a késést, mert nem érhető el az üzenet küldési időpontjában állandó tárolóra. Néhány másodpercen belül felhasznált üzenetek nem kerülnek a üzenetküldési tárolóban. Az alábbi példa létrehoz egy expressz témakör.

```csharp
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Expressz entitás, amely nem lehet nem kritikus fontosságú adatokat tartalmazó üzenetet küld, ha a küldő kényszerítheti a Service Bus azonnal megőrizni az állandó tároló úgy, hogy az üzenet a [ForcePersistence] [ ForcePersistence] tulajdonságot **igaz**.

> [!NOTE]
> Expressz entitások nem támogatja a tranzakciókat.

## <a name="use-of-partitioned-queues-or-topics"></a>A particionált várólisták vagy kapcsolatos témakörök

Belső a Service Bus használ ugyanahhoz a csomóponthoz, és üzenetküldési feldolgozást és tárolást egy üzenetküldési entitásra (üzenetsor vagy témakör) üzenetek tárolásához. A [particionált üzenetsor vagy témakör](service-bus-partitioning.md), másrészt, több csomópont is terjeszthető és üzenetküldési tárolók. A particionált üzenetsorok és témakörök csak nem fed fel rendszeres üzenetsorok és témakörök-nál nagyobb átviteli, is mutathat felső szintű elérhetőség. Hozzon létre egy particionált entitás, állítsa be a [EnablePartitioning] [ EnablePartitioning] tulajdonságot **igaz**, a következő példában látható módon. Particionált entitások kapcsolatos további információkért lásd: [particionált üzenetküldési entitások][Partitioned messaging entities].

```csharp
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Több várólisták használata

Ha nincs lehetőség a particionált üzenetsor vagy témakör használja, vagy nem várható terhelése egyetlen particionált üzenetsor vagy témakör kezelhetik, több üzenetküldési entitások kell használnia. Minden entitás, ugyanazt az ügyfelet az összes entitás helyett dedikált ügyfelet létrehozni a több egység használatakor.

## <a name="development-and-testing-features"></a>Fejlesztési és tesztelési szolgáltatások

A Service Bus rendelkezik egy olyan funkció, amely kifejezetten a fejlesztési szolgál amely **soha nem használható termelési konfigurációk**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Ha új szabályokat vagy a szűrők hozzáadódnak a témakört, [TopicDescription.EnableFilteringMessagesBeforePublishing][] ellenőrzése, hogy a várt módon működik-e az új szűrőkifejezés.

## <a name="scenarios"></a>Forgatókönyvek

A következő szakaszok tipikus üzenetkezelési forgatókönyvek mutatják be, és helyzeteit vázolják fel, a kívánt Service Bus-beállításokat. (Kevesebb mint 1 üzenet/másodperc) átviteli sebességet sorolják legkisebb, közepes szintű (üzenet/másodperc 1 vagy nagyobb, de 100-nál kevesebb üzenetek/másodperc) és nagy (100 üzenetek/második vagy nagyobb). Ügyfelek száma kisebb minősülnek (5 vagy kevesebb), mérsékelt (több mint 5, de legfeljebb 20), és nagy (több mint 20).

### <a name="high-throughput-queue"></a>Nagy átviteli várólista

Cél: Az átviteli sebessége egy adott sorba maximalizálása érdekében. A küldő és száma nem nagy.

* Használjon egy particionált várólista javítja a teljesítményt és rendelkezésre állás.
* A várólistán a teljes küldések növeléséhez használja több üzenetkezelési gyárat feladók létrehozásához. Minden egyes feladó aszinkron műveletek vagy több szál használja.
* Az általános receive arány az üzenetsorból növeléséhez használja több üzenetkezelési gyárat fogadók létrehozásához.
* Az aszinkron műveletek segítségével ügyféloldali kötegelés előnyeit.
* Állítsa a kötegelési időköz 50ms Service Bus ügyfél protokoll átvitelt számának csökkentése érdekében. Több feladók használata esetén növelje a 100 MS kötegelési időköz.
* Hagyja meg a kötegelt áruházhoz való hozzáférés engedélyezve. Ez növeli a teljes sebesség, amellyel üzenetek lehet írni a várólistán.
* A lehívott száma 20 alkalommal a maximális feldolgozási sebességet az összes fogadó gyár beállítása. Ez csökkenti a Service Bus ügyfél protokoll átvitelek adattitkosítását.

### <a name="multiple-high-throughput-queues"></a>Több nagy átviteli várólisták

Cél: Több várólisták teljes átviteli sebesség maximalizálása érdekében. Az átviteli sebessége egyedi várólista közepes vagy magas.

Maximális átviteli sebesség több várólistában beszerzéséhez a egy adott sorba kapacitásának maximalizálása vázolt beállítást használja. Ezenkívül a különböző előállítók elküldeni vagy fogadni a különböző várólistákból ügyfelek létrehozásához.

### <a name="low-latency-queue"></a>Kis késleltetésű várólista

Cél: Az üzenetsor vagy témakör végpontok közötti késés minimalizálása érdekében. A küldő és száma nem nagy. A várólista átviteli a kis és közepes.

* Használjon egy particionált várólista jobb rendelkezésre állása.
* Tiltsa le az ügyféloldali kötegelés. Az ügyfél azonnal üzenetet küld.
* Tiltsa le a kötegelt áruházhoz való hozzáférés. A szolgáltatás azonnal ír az üzenet az tárolójába.
* Ha egy ügyfél használja, az előzetes betöltési count értékre 20 alkalommal feldolgozási sebessége a fogadó. Ha a várólista egyszerre több üzenet érkezik, a Service Bus-ügyfél protokoll továbbítja azokat ugyanabban az időpontban. Ha az ügyfél a következő üzenetet kap, az üzenet már szerepel a helyi gyorsítótárban. A gyorsítótár a kisméretű kell lennie.
* Ha több ügyfelet használ, adja meg az előzetes betöltési száma 0. Ha így tesz, a második ügyfél kaphat a második ilyen üzenet az első ügyfél továbbra is az első üzenet feldolgozása közben.

### <a name="queue-with-a-large-number-of-senders"></a>Feldolgozási sor küldők sok

Cél: Átviteli sebességgel üzenetsor vagy témakör feladók nagy számú maximalizálása érdekében. Minden egyes küldő mérsékelt sebességet üzeneteket küld. A fogadók kisméretű.

A Service Bus használatával legfeljebb 1000 egyidejű kapcsolatok egy üzenetküldési entitásra (vagy 5000 AMQP használatával). Ezt a határt a névterek szintjén van érvényben, várólisták/témakörök/előfizetések fedett névtér egyidejű kapcsolatok határa. A várólisták Ez a szám megosztott küldők és a fogadók között. Ha az összes 1000 kapcsolat feladók szükségesek, akkor kell cserélni a várólista a témakör és egy-egy előfizetéshez. A témakör feladók, legfeljebb 1000 egyidejű kapcsolatát fogad el, mivel az előfizetés egy további 1000 egyidejű érkező kapcsolatokat fogad fogadók. Ha 1000-nél több egyidejű feladók szükség, a küldők kell üzeneteket küldeni HTTP Protokollon keresztül a Service Bus protokoll.

Átviteli sebesség maximalizálása érdekében tegye a következőket:

* Használjon egy particionált várólista javítja a teljesítményt és rendelkezésre állás.
* Ha feladók található, egy másik folyamat, használja csak egyetlen gyári folyamatonként.
* Az aszinkron műveletek segítségével ügyféloldali kötegelés előnyeit.
* Az alapértelmezett időköz 20ms kötegelés segítségével Rövidítse le a Service Bus ügyfél protokoll átvitelek adattitkosítását.
* Hagyja meg a kötegelt áruházhoz való hozzáférés engedélyezve. Ez növeli a teljes sebesség, amellyel üzenetek írható üzenetsor vagy témakör.
* A lehívott száma 20 alkalommal a maximális feldolgozási sebességet az összes fogadó gyár beállítása. Ez csökkenti a Service Bus ügyfél protokoll átvitelek adattitkosítását.

### <a name="queue-with-a-large-number-of-receivers"></a>Feldolgozási sor fogadók nagy számú

Cél: Teljes méret üzenetsor vagy fogadók nagy számú előfizetés receive arányát. Minden egyes fogadó fogadja az üzeneteket mérsékelt sebességgel. A küldők kisméretű.

A Service Bus lehetővé teszi, hogy egy entitás legfeljebb 1000 egyidejű kapcsolatok. Ha várólista 1000-nél több fogadóval igényel, akkor kell cserélni a várólista a témakör és több előfizetéssel. Minden előfizetés legfeljebb 1000 egyidejű kapcsolatok is támogatja. Alternatív megoldásként fogadók férhetnek hozzá a HTTP protokoll segítségével a várólista.

Átviteli sebesség maximalizálása érdekében tegye a következőket:

* Használjon egy particionált várólista javítja a teljesítményt és rendelkezésre állás.
* Minden egyes fogadó egy másik folyamat helyezkedik el, ha csak egyetlen gyári folyamatonként használata
* Fogadók használhatja a szinkron vagy aszinkron műveletek. Egy egyedi fogadó mérsékelt receive mértéke a megadott, ügyféloldali kötegelés teljes kérelem nem érinti a fogadó átviteli sebesség.
* Hagyja meg a kötegelt áruházhoz való hozzáférés engedélyezve. Ez csökkenti az entitás teljes terhelése. A teljes sebesség, amellyel üzenetek írható üzenetsor vagy témakör is csökkenti.
* Az előzetes betöltési száma kisebb értékre beállítva (például PrefetchCount = 10). Ez megakadályozza, hogy fogadók tétlen, míg a többi fogadó gyorsítótárazott üzenetek nagy számban.

### <a name="topic-with-a-small-number-of-subscriptions"></a>A témakör egy kis mennyiségű előfizetések

Cél: Maximalizálhatja a teljesítményt, a témakör egy kis mennyiségű előfizetések. Egy üzenet jelenik meg sok előfizetések, ami azt jelenti, hogy minden előfizetés keresztül kombinált receive sebessége nagyobb, mint a küldések. A küldők kisméretű. A fogadók előfizetésenként kisméretű.

Átviteli sebesség maximalizálása érdekében tegye a következőket:

* Használjon egy particionált témakör javítja a teljesítményt és rendelkezésre állás.
* A témakör azokat a teljes küldések növeléséhez használja több üzenetkezelési gyárat feladók létrehozásához. Minden egyes feladó aszinkron műveletek vagy több szál használja.
* Az általános receive arány előfizetésből növeléséhez használja több üzenetkezelési gyárat fogadók létrehozásához. A címzettek, az aszinkron műveletek vagy több szál használja.
* Az aszinkron műveletek segítségével ügyféloldali kötegelés előnyeit.
* Az alapértelmezett időköz 20ms kötegelés segítségével Rövidítse le a Service Bus ügyfél protokoll átvitelek adattitkosítását.
* Hagyja meg a kötegelt áruházhoz való hozzáférés engedélyezve. Ez növeli a témakör írható üzenetek, amellyel teljes száma másodpercenként.
* A lehívott száma 20 alkalommal a maximális feldolgozási sebességet az összes fogadó gyár beállítása. Ez csökkenti a Service Bus ügyfél protokoll átvitelek adattitkosítását.

### <a name="topic-with-a-large-number-of-subscriptions"></a>A témakör egy nagy mennyiségű előfizetések

Cél: Az átviteli sebessége a témakörök sok előfizetések maximalizálása érdekében. Egy üzenet jelenik meg több előfizetés, ami azt jelenti, hogy a kombinált receive előfizetéseket keresztül Ez sokkal nagyobb, mint a küldési sebesség. A küldők kisméretű. A fogadók előfizetésenként kisméretű.

Témakörök, előfizetések nagy számú általában egy kis teljes teljesítményt tehetnek közzé, ha a minden üzenet előfizetéseket legyenek átirányítva. Ennek oka az, hogy minden üzenet jelenik meg többször, és a témakörben található összes üzenet és az előfizetések ugyanarra a tároló tárolja. Feltételezzük, hogy a küldők és előfizetésenként fogadók számát kis. A Service Bus témakör száma legfeljebb 2000 előfizetések támogatja.

Átviteli sebesség maximalizálása érdekében tegye a következőket:

* Használjon egy particionált témakör javítja a teljesítményt és rendelkezésre állás.
* Az aszinkron műveletek segítségével ügyféloldali kötegelés előnyeit.
* Az alapértelmezett időköz 20ms kötegelés segítségével Rövidítse le a Service Bus ügyfél protokoll átvitelek adattitkosítását.
* Hagyja meg a kötegelt áruházhoz való hozzáférés engedélyezve. Ez növeli a témakör írható üzenetek, amellyel teljes száma másodpercenként.
* A lehívott száma beállított 20 alkalommal a várt receive érték másodperc. Ez csökkenti a Service Bus ügyfél protokoll átvitelek adattitkosítását.

## <a name="next-steps"></a>További lépések

A Service Bus teljesítmény optimalizálása kapcsolatos további információkért lásd: [particionált üzenetküldési entitások][Partitioned messaging entities].

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
