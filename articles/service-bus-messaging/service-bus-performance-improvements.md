---
title: Ajánlott eljárások az Azure Service Bus használata a teljesítmény fokozása |} A Microsoft Docs
description: Ismerteti, hogyan lehet optimalizálni a teljesítményt felügyelt üzenetváltásokban a Service Bus használatával.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 37e2dcc13ed41911c8117dc1841a389c14e5867f
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848572"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Ajánlott eljárások a teljesítmény Service Bus-üzenetkezelés használatával

Ez a cikk ismerteti az Azure Service Bus közvetítőalapú üzenetek szolgáltatásbusszal teljesítmény optimalizálása érdekében. Ez a cikk első részében érhetők el a teljesítmény növeléséhez használható különböző mechanizmusokat írja le. A második rész útmutatást nyújt a Service Bus használatát úgy, hogy egy adott helyzetben a lehető legjobb teljesítményt kínálnak.

Ez a cikk során az "ügyfél" kifejezés minden entitás, amely hozzáfér a Service Bus. Egy ügyfél a szerepkör a küldő vagy fogadó is igénybe vehet. A "feladó" kifejezés egy Service Bus-üzenetsor vagy témakör ügyfél, amely üzeneteket küld egy Service Bus-üzenetsor vagy témakör-előfizetés számára. A "címzett" kifejezés egy Service Bus üzenetsorok vagy előfizetések ügyfél fogad üzeneteket egy Service Bus-üzenetsor vagy előfizetést.

Ezekben a szakaszokban a teljesítmény növelése érdekében használja a Service Bus számos koncepciót megalkotott vezetnek be.

## <a name="protocols"></a>Protokollok

A Service Bus lehetővé teszi, hogy az ügyfelek számára, hogy három protokoll használatával üzeneteket küldjön és fogadjon:

1. Advanced Message Queueing Protocol (AMQP)
2. Service Bus-üzenetkezelés protokoll (SBMP)
3. HTTP

Amqp-t és SBMP hatékonyabbak, mivel azok a Service Bus kapcsolat fenntartását, mindaddig, amíg az üzenetkezelési előállító létezik. Kötegelés és prefetching is végrehajtja. Kivéve, ha explicit módon azt korábban említettük, ez a cikk az összes tartalom feltételezi, hogy az AMQP vagy SBMP használata.

## <a name="reusing-factories-and-clients"></a>Előállítók és az ügyfelek újbóli használata

Service Bus-ügyfélalkalmazást objektumok, például [QueueClient] [ QueueClient] vagy [MessageSender][MessageSender], keresztül létrehozott egy [ MessagingFactory] [ MessagingFactory] objektum, amely a belső felügyeleti kapcsolatok is biztosít. Javasoljuk, hogy azt ne zárja be üzenetkezelési gyárat vagy üzenetsor, témakör és előfizetés ügyfelek után küldjön egy üzenetet, és ezután hozza létre őket ismét a következő üzenet küldésekor. A Service Bus szolgáltatással létesített kapcsolat bezárása egy üzenetkezelési előállító törli, és újra létre kellene hoznia a gyári létrejön az új kapcsolatot. A kapcsolat létrehozása, amely a azonos gyári és az ügyfél objektumok több műveletek újrafelhasználásával elkerülheti a költséges művelet. Ezek az objektumok ügyfél egyidejű aszinkron műveleteknél és több szálon biztonságosan használhatja. 

## <a name="concurrent-operations"></a>Az egyidejű művelet

Egy műveletet (Küldés, kap, törlés, stb.) eltarthat egy ideig. Ezúttal a művelet feldolgozása tartalmazza a Service Bus szolgáltatás a kéréseket és a válasz késés mellett. Növelje a műveletek száma idő, műveleteket végre kell hajtani egyidejűleg. 

Az ügyfél egyidejű művelet ütemezi az aszinkron műveletek végrehajtásával. A következő kérelmet el van indítva az előző kérelem befejeződése előtt. A következő kódrészletet a következő egy példa egy aszinkron küldési művelet:
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
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
  
  Az alábbi kód például egy aszinkron művelet kap. Tekintse meg a teljes program [Itt](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Fogadás módban

Egy üzenetsor vagy előfizetés ügyfél létrehozásához, megadhatja a receive mód: *Service Bus-* vagy *fogadása és törlése*. Az alapértelmezett mód fogadására van [PeekLock][PeekLock]. Az ebben a módban működő, az ügyfél üzenetet fogadni a Service Bus kérést küld. Miután az ügyfél az üzenetet kapott, azt végezze el az üzenetet kérelmet küld.

Ha a receive mód beállítása [ReceiveAndDelete][ReceiveAndDelete], mindkét lépést egyetlen kérelem van összevonva. Ezek a lépések csökkenteni a műveletek teljes száma, és javítja az általános üzeneteinek átviteli sebessége. Ez a teljesítmény nyereség kockázatára üzenetek elvesztése származnak.

A Service Bus nem támogatja a tranzakciókat fogadása és-törlés műveletekhez. Emellett betekintési zárolással való szemantika szükségesek az összes olyan forgatókönyvet, amelyben az ügyfél szeretne késleltetése vagy [kézbesíthetetlen levelek](service-bus-dead-letter-queues.md) üzenetet.

## <a name="client-side-batching"></a>Ügyféloldali kötegelés

Ügyféloldali kötegelés lehetővé teszi az üzenetsor vagy témakör ügyfelek üzenet küldése egy bizonyos ideig késleltethető. Ha az ügyfél további üzeneteket küld ezen időszakon belül, a rendszer egyetlen kötegben továbbítja ezen üzeneteket. Ügyféloldali kötegelés jelentkezésekor a batch-több üzenetsor vagy előfizetés ügyfél **Complete** egyetlen kérelmeket. Kötegelés csak akkor érhető el az aszinkron **küldése** és **Complete** műveleteket. A szinkron műveletek a Service Bus szolgáltatás azonnal érkeznek. Kötegelés nem fordulhat elő, a betekintési és fogadási műveletek, és nem csak az ügyfelek közötti kötegelés fordul elő.

Alapértelmezés szerint az ügyfél használja a batch időköz 20 ms. A batch időköz módosításához állítsa a [BatchFlushInterval] [ BatchFlushInterval] tulajdonság az üzenetkezelési előállító létrehozása előtt. Ez a beállítás hatással van az előállító által létrehozott összes ügyfél.

Kötegelés letiltásához állítsa be a [BatchFlushInterval] [ BatchFlushInterval] tulajdonságot **TimeSpan.Zero**. Példa:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Kötegelés nem befolyásolja a üzenetkezelési számlázandó műveletek száma, és csak érhető el a Service Bus ügyfél protokoll használatával a [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) könyvtár. A HTTP-protokoll nem támogatja a kötegelés.

## <a name="batching-store-access"></a>Kötegelés store-hozzáférés

Egy üzenetsor, témakör vagy előfizetés, az átviteli sebesség növelése érdekében a Service Bus több üzenetet kötegeli, ha a belső tárolójába ír. Ha engedélyezve van a egy üzenetsorba vagy témakörbe, üzeneteket írna a tárolóba lesznek kötegelni. Ha engedélyezve van, az üzenetsor vagy előfizetés, üzenetek törlése a tárolóból fogja kötegelni. Kötegelt store-hozzáférés engedélyezve van egy entitás, ha a Service Bus kapcsolatban, hogy az entitás tároló írási művelet késlelteti legfeljebb 20 ms által. 

> [!NOTE]
> Nem áll fenn az ügyfeleknél-üzenetek kötegelése, még akkor is, ha egy 20 MS kötegelés időszak végén a Service Bus hiba történik. 

Ez az időtartam során felmerülő további tárolási műveletek bekerülnek a köteg. Store hozzáférés csak a befolyásolja kötegelni **küldése** és **Complete** műveleteket; kap műveletek nem érinti. Kötegelt store-hozzáférés egy entitás tulajdonságára. Kötegelés összes entitás, amely kötegelt store-hozzáférés engedélyezése között történik.

Egy új üzenetsor, témakör vagy előfizetés létrehozásakor kötegelt store-hozzáférés alapértelmezés szerint engedélyezve van. Kötegelt store-hozzáférés letiltásához állítsa be a [EnableBatchedOperations] [ EnableBatchedOperations] tulajdonságot **hamis** az entitás létrehozása előtt. Példa:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Kötegelt store-hozzáférés nem befolyásolja a üzenetkezelési számlázandó műveletek száma, és a egy üzenetsor, témakör vagy előfizetés tulajdonsága. Célszerű a független a receive mód és a egy ügyfél és a Service Bus szolgáltatás között használt protokoll.

## <a name="prefetching"></a>Prefetching

[Prefetching](service-bus-prefetch.md) lehetővé teszi, hogy az üzenetsor vagy előfizetés ügyfél további üzeneteket betölteni a szolgáltatásból a fogadási művelet elvégzésekor. Az ügyfél a helyi gyorsítótárban tárolja ezeket az üzeneteket. A gyorsítótár méretét határozza meg a [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] vagy [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] tulajdonságait. Minden ügyfél, amely lehetővé teszi, hogy prefetching kezeli a saját gyorsítótár. A gyorsítótár nem közösen használja az ügyfelek számára. Ha az ügyfél kezdeményezi a fogadási művelet és a gyorsítótár üres, a szolgáltatás továbbítja az üzenetkötegek. A Köteg mérete 256 KB-os vagy a gyorsítótár méretének eredménye közül a kisebbik. Ha az ügyfél kezdeményezi a receive műveletet, és a gyorsítótár tartalmaz egy üzenetet, az üzenet forrása a gyorsítótárban.

Amikor egy üzenet prefetched van, a szolgáltatás zárolja a prefetched üzenetet. A zárolás, a prefetched üzenet nem érkezett egy másik fogadó. Ha a címzett nem tudja végrehajtani az üzenetet, a zárolás lejárta előtt, akkor az üzenet a többi fogadó számára elérhetővé válik. A gyorsítótárban marad az üzenet prefetched példányát. A fogadó lejárt gyorsítótárazott másolatát feldolgozó kivételt kap, való végezze el az üzenetet. Az üzenet zárolási alapértelmezés szerint 60 másodperc múlva jár le. Ez az érték 5 perc is kiterjeszthető. Lejárt üzenetek a használat megelőzése érdekében a gyorsítótár mérete mindig kisebbnek kell lennie, amely a zárolási időtúllépés belül egy ügyfél által felhasználható üzenetek száma.

60 másodperces alapértelmezett zárolás lejárta használatakor egy helyes értéket [PrefetchCount] [ SubscriptionClient.PrefetchCount] 20 alkalommal a legmagasabb feldolgozása folyamatban van az előállító összes fogadók mértékét. Például egy gyári három fogadók hoz létre, és minden címzett tud feldolgozni másodpercenként legfeljebb 10 üzenetet. Az előzetes betöltési száma nem haladhatja meg a 20 X 3 × 10 = 600. Alapértelmezés szerint [PrefetchCount] [ QueueClient.PrefetchCount] értéke 0, ami azt jelenti, hogy a szolgáltatásból további üzenetek már beolvasott.

A teljes átviteli sebesség az üzenetsor vagy előfizetés üzeneteket prefetching növeli, mivel csökkenti a message operations vagy adatváltások számát. Az első üzenet beolvasása, azonban hosszabb időt vesz igénybe (miatt a nagyobb üzenetek mérete). Prefetched üzenetek fogadása gyorsabb lesz, mivel ezeket az üzeneteket az ügyfél már letöltött.

Az üzenet a time-to-live (Élettartam TTL) tulajdonság be van jelölve a kiszolgáló által a kiszolgáló elküldi az ügyfélnek az üzenet időpontjában. Az ügyfél nem ellenőrzi az üzenet TTL tulajdonsága az üzenet fogadásakor. Ehelyett az is lehet üzenet akkor is, ha az üzenet Élettartama lejárt, amíg az üzenet volt az ügyfél gyorsítótárába.

Prefetching nem befolyásolja a üzenetkezelési számlázandó műveletek száma, és csak a Service Bus-ügyfél protokoll érhető el. A HTTP-protokoll nem támogatja a prefetching. Prefetching áll rendelkezésre, mind a szinkron és aszinkron műveletek kapni.

## <a name="multiple-queues"></a>Több üzenetsort

A várható terhelést egyetlen particionált üzenetsor vagy témakör nem kell kezelnie, ha több üzenetküldési entitások kell használnia. Több entitás használata esetén hozzon létre dedikált ügyfél-az egyes entitásokhoz ugyanaz az ügyfél az összes entitás helyett.

## <a name="development-and-testing-features"></a>Fejlesztési és tesztelési funkciók

A Service Bus rendelkezik egy funkciót is, kifejezetten a fejlesztés, amelyhez **soha nem használható éles konfigurációkban**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Ha új szabályokat vagy a szűrők hozzá vannak adva a témakört, [TopicDescription.EnableFilteringMessagesBeforePublishing][] , győződjön meg arról, hogy a várt módon működik-e az új szűrőkifejezés.

## <a name="scenarios"></a>Forgatókönyvek

Az alábbi szakaszok ismertetik a üzenetküldési forgatókönyvre jellemző, és a kívánt beállításokat a Service Bus szerkezeti. (Kevesebb mint 1 üzenet/másodperc) átviteli sebességet besorolt legkisebb, közepes szintű (üzenet/másodperc 1 vagy nagyobb, de 100-nál kevesebb üzenetek/másodperc) és a magas (100 üzenetek/második vagy nagyobb). Ügyfelek száma besorolt kicsi (5 vagy kevesebb), a közepes (több mint 5, de legfeljebb 20), és a nagy (több mint 20).

### <a name="high-throughput-queue"></a>Nagy átviteli sebességű várólista

Cél: Maximális átviteli sebességének egyetlen üzenetsorhoz. A küldők és fogadók száma, kis méretű.

* A teljes küldések a várólistába növeléséhez használja több üzenetkezelési gyárat feladók létrehozásához. Minden egyes küldő használja az aszinkron műveletek vagy több szálon.
* Az üzenetsorból az általános receive arány növelése érdekében a fogadók létrehozásához használja több üzenetkezelési gyárat.
* Az aszinkron műveletek használatával kihasználhatja kötegelésére ügyféloldali.
* A kötegelés időköz beállítása csökkentése érdekében a Service Bus-ügyfél protokoll átvitelek száma 50 ms. Több feladók használata esetén növelje a kötegelés 100 ms, időköz.
* Hagyja üresen a kötegelt store-hozzáférés engedélyezve van. Ezt a hozzáférést a példánytérváltás átfogó, amellyel üzeneteket az üzenetsorba írja.
* Állítsa a lehívott száma 20 alkalommal a maximális feldolgozási sebességre gyár összes fogadók. Ez a szám a Service Bus-ügyfél protokoll átvitelek száma csökken.
* Egy particionált üzenetsorra használja a jobb teljesítmény és rendelkezésre állás.

### <a name="multiple-high-throughput-queues"></a>Több nagy átviteli sebességű üzenetsorok

Cél: Maximalizálja a teljes átviteli sebességének több üzenetsort. Az átviteli sebességet egy adott üzenetsor, közepes vagy magas.

Maximális átviteli sebesség között több üzenetsort beszerzéséhez használja egyetlen üzenetsorhoz, az átviteli teljesítmény ismertetett beállításokat. Emellett a különböző előállítók használatával létrehozhat elküldeni vagy fogadni a különböző üzenetsorok a ügyfelek.

### <a name="low-latency-queue"></a>Közel valós idejű várólista

Cél: Az üzenetsor vagy témakör végpontok közötti késés minimalizálása. A küldők és fogadók száma, kis méretű. Az átviteli sebességet a várólista, kis és közepes.

* Tiltsa le az ügyféloldali kötegelés. Az ügyfél közvetlenül egy üzenetet küld.
* Tiltsa le a kötegelt store-hozzáférés. A szolgáltatás azonnal ír az üzenetet a tárolóban.
* Ha egy ügyfél használja, a lehívott száma értékre 20 alkalommal a feldolgozási sebességét a fogadó. Ha több üzenet érkezik a várólistában egyszerre, a Service Bus-ügyfél protokoll továbbítja azokat a ugyanabban az időben. Amikor az ügyfél a következő üzenetet kap, az üzenet már a helyi gyorsítótárban. A gyorsítótár kis méretűnek kell lenniük.
* Ha több ügyfelet használ, adja meg a lehívott száma 0. Állítsa a száma, a második ügyfél kaphat a második üzenet az első ügyfél továbbra is az első üzenet feldolgozása közben.
* Egy particionált üzenetsorra használja a jobb teljesítmény és rendelkezésre állás.

### <a name="queue-with-a-large-number-of-senders"></a>A küldők nagy számú üzenetsort

Cél: Az üzenetsor vagy témakör feladók nagy számú az átviteli sebesség maximalizálása. Minden egyes küldő mérsékelt arány üzeneteket küld. A fogadók szám kisebb.

A Service Bus használatával legfeljebb 1000 párhuzamos kapcsolatot egy üzenetküldési entitásra (5000-es vagy AMQP használatával). Ezt a korlátot, a névterek szintjén, és a várólisták és témakörök/előfizetések által a korlátot, az egyidejű kapcsolatok száma névterenként fedett. A várólisták Ez a szám megosztott küldők és fogadók között. Ha feladók szükséges összes 1000 kapcsolatot, cserélje le a várólista egy témakört, és egyetlen előfizetéssel. A témakör a küldők, legfeljebb 1000 párhuzamos kapcsolatot fogad el, mivel az előfizetés egy további 1000 egyidejű érkező kapcsolatokat fogad fogadónak is. Ha 1000-nél több egyidejű feladók szükség, a feladó kell üzeneteket küldeni a Service Bus-protokoll HTTP-n keresztül.

A maximális átviteli sebesség érdekében hajtsa végre az alábbi lépéseket:

* Minden egyes küldő található egy másik folyamat, ha használata csak egyetlen gyári folyamatonként.
* Az aszinkron műveletek használatával kihasználhatja kötegelésére ügyféloldali.
* Az alapértelmezett 20 ms, időköz kötegelés használatával csökkenthető a Service Bus-ügyfél protokoll átvitelek száma.
* Hagyja üresen a kötegelt store-hozzáférés engedélyezve van. Ezt a hozzáférést a példánytérváltás átfogó, amellyel üzeneteket az üzenetsor vagy témakör írja.
* Állítsa a lehívott száma 20 alkalommal a maximális feldolgozási sebességre gyár összes fogadók. Ez a szám a Service Bus-ügyfél protokoll átvitelek száma csökken.
* Egy particionált üzenetsorra használja a jobb teljesítmény és rendelkezésre állás.

### <a name="queue-with-a-large-number-of-receivers"></a>A fogadók nagy számú üzenetsort

Cél: Maximalizálja az üzenetsor vagy -előfizetést, a fogadók nagy számú receive arányát. Minden címzett gyakorisággal mérsékelt üzeneteket fogad. Feladók a szám kisebb.

A Service Bus lehetővé teszi, hogy az entitás legfeljebb 1000 párhuzamos kapcsolatot. Ha egy üzenetsorban 1000-nél több fogadóval igényel, cserélje le a várólista egy témakört, és több előfizetést is. Az egyes előfizetésekhez legfeljebb 1000 párhuzamos kapcsolatot támogat. Másik lehetőségként fogadók férhetnek hozzá a várólista a HTTP protokollon keresztül.

Maximális átviteli sebesség, tegye a következőket:

* Minden címzett található egy másik folyamat, ha csak egyetlen gyári folyamatonként használata
* Fogadók szinkron vagy aszinkron műveleteket használhatja. Adja meg egy egyedi fogadó mérsékelt receive arányát, ügyféloldali kötegelés teljes kérelem nem befolyásolja fogadó átviteli sebességet.
* Hagyja üresen a kötegelt store-hozzáférés engedélyezve van. Ez a hozzáférés csökkenti az entitás teljes terhelése. Azt is csökkenti a teljes, amellyel üzeneteket az üzenetsor vagy témakör írja.
* Az előzetes betöltési száma kisebb értékre (például PrefetchCount = 10). Ez a szám megakadályozza, hogy a fogadók üresjárati, míg a többi fogadó számára gyorsítótárazott üzenetek nagy számban.
* Egy particionált üzenetsorra használja a jobb teljesítmény és rendelkezésre állás.

### <a name="topic-with-a-small-number-of-subscriptions"></a>A témakör az előfizetések kis számú

Cél: Maximalizálja a teljesítményt, a témakör az előfizetések kis számú. Egy üzenet érkezik sok előfizetést, ami azt jelenti, az összes előfizetés kombinált receive aránya nagyobb, mint a küldési sebesség. Feladók a szám kisebb. Az előfizetésenként fogadók száma, kis méretű.

Maximális átviteli sebesség, tegye a következőket:

* A teljes küldések témakörben növeléséhez használja több üzenetkezelési gyárat feladók létrehozásához. Minden egyes küldő használja az aszinkron műveletek vagy több szálon.
* Növekedjen általános fogadása egy előfizetésből, fogadók hoz létre több üzenetkezelési gyárat használatával. Minden címzett számára használja az aszinkron műveletek vagy több szálon.
* Az aszinkron műveletek használatával kihasználhatja kötegelésére ügyféloldali.
* Az alapértelmezett 20 ms, időköz kötegelés használatával csökkenthető a Service Bus-ügyfél protokoll átvitelek száma.
* Hagyja üresen a kötegelt store-hozzáférés engedélyezve van. Ezt a hozzáférést a példánytérváltás átfogó, ahol az üzenetek témakörben írhatók.
* Állítsa a lehívott száma 20 alkalommal a maximális feldolgozási sebességre gyár összes fogadók. Ez a szám a Service Bus-ügyfél protokoll átvitelek száma csökken.
* A particionált témakör használja a jobb teljesítmény és rendelkezésre állás.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Az előfizetések sok témakör

Cél: Az átviteli sebességet, a témakör az előfizetések nagy számú maximalizálása érdekében. Egy üzenet érkezik számos előfizetéseket, ami azt jelenti, hogy az összes előfizetés kombinált receive mérték jóval nagyobb, mint a küldési sebesség. Feladók a szám kisebb. Az előfizetésenként fogadók száma, kis méretű.

Témakörök, előfizetések nagy számú általában tehetnek közzé a egy alacsony teljes átviteli sebesség, ha az összes üzenetet a rendszer az összes előfizetés irányítja. Az alacsony átviteli sebesség oka az, hogy minden üzenetet sokszor, és abban a témakörben található összes üzenet és az összes előfizetés ugyanabban a tárolóban tárolja. Azt feltételezzük, hogy a küldők és fogadók előfizetésenként, számát kis. A Service Bus támogatja a legfeljebb 2000 előfizetések témánként.

A maximális átviteli sebesség érdekében kövesse az alábbi lépéseket:

* Az aszinkron műveletek használatával kihasználhatja kötegelésére ügyféloldali.
* Az alapértelmezett 20 ms, időköz kötegelés használatával csökkenthető a Service Bus-ügyfél protokoll átvitelek száma.
* Hagyja üresen a kötegelt store-hozzáférés engedélyezve van. Ezt a hozzáférést a példánytérváltás átfogó, ahol az üzenetek témakörben írhatók.
* Állítsa a lehívott száma 20 alkalommal a várt fogadási sebesség másodpercek alatt. Ez a szám a Service Bus-ügyfél protokoll átvitelek száma csökken.
* A particionált témakör használja a jobb teljesítmény és rendelkezésre állás.

## <a name="next-steps"></a>További lépések

A Service Bus teljesítményének optimalizálása kapcsolatos további információkért lásd: [particionált üzenetküldési entitások][Partitioned messaging entities].

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
