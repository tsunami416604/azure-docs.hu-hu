---
title: Az Azure Service Fabric ReliableConcurrentQueue
description: ReliableConcurrentQueue a nagy átviteli beolvasása, amely lehetővé teszi, hogy a párhuzamos enqueues és dequeues.
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: e04123f7870921a2979564d0f6c68424d4d7711c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Az Azure Service Fabric ReliableConcurrentQueue bemutatása
Megbízható egyidejű várólista egy aszinkron, a tranzakciós és a replikált sor mely szolgáltatások magas CONCURRENCY paraméterének értékét sorba helyezni, és műveletek feldolgozásához. Szolgáltatás célja, hogy nagyobb teljesítményt és alacsony késést kézbesítendő lazítani a szigorú FIFO rendelési által biztosított [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx) és biztosítja a legjobb rendezést.

## <a name="apis"></a>API-k

|Egyidejű várólista                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| "void" Enqueue(T item)           | A feladat EnqueueAsync (ITransaction tx, T elem)                       |
| logikai TryDequeue (kimenő T eredmény)  | A feladat < ConditionalValue < T >> TryDequeueAsync (ITransaction tx)  |
| int Count()                    | hosszú Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Az összehasonlítás [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Megbízható egyidejű várólista tartományregisztráció alternatívájaként [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx). Olyan esetekben, ahol szigorú FIFO rendezés nem szükséges, használjon, FIFO együtt van szükség egy kompromisszumot biztosítása.  [Megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx) zárolások kényszerítéséhez FIFO rendezés használatakor a várakozási sorba állítani engedélyezett legfeljebb egy tranzakciót és engedélyezett a Created egyszerre legfeljebb egy tranzakciót használ. Összehasonlításképpen megbízható egyidejű várólista visszaállítja a rendezési korlátozás, és lehetővé teszi, hogy azok sorba helyezni interleave és műveletek created száma párhuzamos tranzakciók. Legjobb rendelési kerül, azonban a két érték megbízható egyidejű várólista relatív sorrendjének soha nem garantálható.

Megbízható egyidejű várólista biztosít nagyobb átviteli teljesítményt és kisebb késést biztosít [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx) vannak enqueues végrehajtása több egyidejű tranzakciókat és/vagy dequeues.

Egy minta-és nagybetűhasználattal a ReliableConcurrentQueue érték a [üzenet-várólista](https://en.wikipedia.org/wiki/Message_queue) forgatókönyv. Ebben a forgatókönyvben egy vagy több üzenetek létrehozói létre elemek hozzáadására a várólista és egy vagy több üzenetet fogyasztók húzza az üzenetsorból érkezett üzeneteket, és dolgozza fel őket. Több létrehozói és felhasználói használható önállóan, egyidejű tranzakciókat használó sablonokat a feldolgozáshoz a várólistát.

## <a name="usage-guidelines"></a>Használatára vonatkozó irányelvek
* A várólista vár, a várólistában lévő elemek rendelkezik-e kis megőrzési időtartamot. Ez azt jelenti, hogy a cikkek volna nem marad a várólistán lévő hosszú ideig.
* A várólista nem garantálja a szigorú FIFO rendezést.
* A várólista nem olvassa a saját írási műveleteket. Ha a cikk a várólistában levő tranzakción belül, azt nem lesznek láthatók a egy dequeuer ugyanazon a tranzakción belül.
* Dequeues nem el különítve egymástól. Ha a cikk *A* várólistából tranzakcióban van-e kivéve *txnA*, annak ellenére, hogy *txnA* nincs véglegesítve, elem *A* nem fogják látni a párhuzamos tranzakció *txnB*.  Ha *txnA* megszakítja, *A* számára látható lesz *txnB* azonnal.
* *TryPeekAsync* viselkedés használatával valósítható egy *TryDequeueAsync* és majd a tranzakció megszakítása. Példa erre a minták programozás szakaszában található.
* Számláló értéke nem tranzakciós. Azt a várólistában lévő elemek száma a képet kapjon használható, de egy-időpontban jelöli, és nem lehet hivatkozni.
* A dequeued elemek költséges terhelése nem hajtható végre, amíg a tranzakció aktív, a teljesítmény hatással lehet a rendszer a hosszan futó tranzakciókat elkerülése érdekében.

## <a name="code-snippets"></a>Kódtöredékek
Ossza meg velünk tekintse meg néhány kódtöredékek és a várható kimenetekkel. Kivétel ebben a szakaszban figyelmen kívül hagyja.

### <a name="enqueueasync"></a>EnqueueAsync
Az alábbiakban néhány kódrészleteket a várt kimeneti követ EnqueueAsync használatával.

- *1. eset: Egy sorba helyezni a feladat*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és, hogy van-e a várólista módosítása párhuzamos tranzakciók. A felhasználó számíthat a várólistában, a következő rendelések valamelyikében tartalmaznia:

> 10, 20

> 20, 10


- *2. eset: Párhuzamos sorba helyezni a feladat*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, hogy a feladatok párhuzamosan futtatta-e, és, hogy történtek-e módosítani a várólista nincs más egyidejű tranzakciókat. A megállapítás nem végezhető a várólistában lévő elemek sorrendjét. A következő kódrészletet a is megjelenhetnek a 4 bármelyikét! lehetséges rendezés.  A várólista megpróbálja elemeket tartani az eredeti (várólistán lévő) sorrendben, de újrarendezéshez párhuzamos műveletek vagy hibák miatt is kényszeríthető.


### <a name="dequeueasync"></a>DequeueAsync
Az alábbiakban néhány kódrészleteket a várt kimeneti követ TryDequeueAsync használatával. Tegyük fel, hogy a várólista már fel van töltve, az a várólista következő elemeit:
> 10, 20, 30, 40, 50, 60

- *1. eset: Egyetlen created feladat*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és, hogy van-e a várólista módosítása párhuzamos tranzakciók. Óta nincs megállapítás végezhető a várólistában lévő elemek sorrendjét, bármely három elemek előfordulhat, hogy lehet várólistából kivéve, bármilyen sorrendben. A várólista megpróbálja elemeket tartani az eredeti (várólistán lévő) sorrendben, de újrarendezéshez párhuzamos műveletek vagy hibák miatt is kényszeríthető.  

- *2. eset: Párhuzamosan created feladat*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, hogy a feladatok párhuzamosan futtatta-e, és, hogy történtek-e módosítani a várólista nincs más egyidejű tranzakciókat. Mivel a várólistára, a lista elemeinek sorrendje nincs megállapítás végezhető *dequeue1* és *dequeue2* egyes bármely két elemet tartalmaz, bármilyen sorrendben.

Konfigurációelem-ugyanaz lesz *nem* mindkét listán. Ezért ha dequeue1 *10*, *30*, majd dequeue2 kellene *20*, *40*.

- *3. eset: Created rendelését tranzakció megszakítása*

Az üzenetsoroktól a tranzakció megszakítása dequeues visszahelyezi a munkaelemeket visszaküldeni a várólista vezetője a. A sorrendet, amelyben a cikkek kerülnek vissza a head a várólista nem garantált. Ossza meg velünk nézze meg a következő kódot:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Tegyük fel, hogy a cikkek várólistából volt-e kivéve az alábbi sorrendben:
> 10, 20

A tranzakció megszakítása azt, ha a elemek volna kerülnek vissza a várólista következő rendelések valamelyikében vezetője:
> 10, 20

> 20, 10

Ugyanez érvényes minden olyan esetben, ha a tranzakció nem volt sikeresen *lekötött*.

## <a name="programming-patterns"></a>Programozási minták
Ebben a szakaszban ossza meg velünk tekintse meg néhány programozási minta, érdemes lehet megfontolni a ReliableConcurrentQueue használatával.

### <a name="batch-dequeues"></a>Kötegelt Dequeues
A javasolt programozási mintát a fogyasztói feladat kötegelt annak egyik végzett helyett dequeues created egyszerre. A felhasználó minden köteg vagy a Köteg mérete közötti üzenetváltás miatti késésekre szabályozás választhat. A következő kódrészletet a programozási modell jeleníti meg.  Vegye figyelembe, hogy ebben a példában a nem hajtja végre a tranzakció véglegesítése, után, ha egy tartalék feldolgozása közben, a feldolgozatlan elemek elvesznek feldolgozás nélkül.  Azt is megteheti feldolgozásával végezhető a tranzakció hatókörében, azonban ez negatív hatással lehet a teljesítményre, és a cikkek feldolgozása már kezelése igényel.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Legjobb értesítés-alapú feldolgozási
Egy másik érdekes programozási mintát a Count API-t használja. Itt ket lehet megvalósítani legjobb értesítési alapú a várólista feldolgozása. A sor számának segítségével egy sorba helyezni vagy egy dequeue feladat szabályozás.  Előfordulhat, hogy az előző példában szemléltetett óta a feldolgozás a tranzakción kívül történik feldolgozatlan elemek elvesznek, ha a hiba akkor fordul elő, feldolgozása során.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Legjobb kiürítési
A kiürítési a várólista nem garantálható az adatok szerkezete egyidejű jellemzői miatt.  Lehetséges, hogy akkor is, ha nincsenek felhasználói műveletek várakozási üzenetsoroktól, TryDequeueAsync adott hívása nem ad egy elem, amely korábban a várólistában levő és véglegesítve.  A várólistában levő elem garantáltan *végül* created, azonban nélkül egy sávon kívüli kommunikációs mechanizmus egy független fogyasztó nem tudja, hogy a várólista elérte a stabil állapot, még akkor is, ha minden gyártók le lett állítva, és nincsenek-e új sorba helyezni műveletek engedélyezettek számára láthatóvá válnak. A kiürítési művelet, így a legjobb alatt megvalósított módon.

A felhasználó kell minden további gyártó és a felhasználói feladatok leállítása, majd várja meg, vagy a várólista kiürítése előtt elvetéséhez üzenetsoroktól tranzakciók esetében.  Ha a felhasználó a várólistában lévő elemek várt száma tudja, egy értesítés, amely jelzi, hogy minden elem rendelkezik várólistából lett kivéve tudja állítani.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Betekintés
ReliableConcurrentQueue nem biztosít a *TryPeekAsync* api. Felhasználók férhetnek a betekintés szemantikai használatával egy *TryDequeueAsync* és majd a tranzakció megszakítása. Ebben a példában dequeues csak akkor, ha az elem értéke nagyobb, mint a feldolgozásuk *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Olvasási kell
* [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Megbízható szolgáltatások értesítések](service-fabric-reliable-services-notifications.md)
* [Megbízható szolgáltatások biztonsági mentése és visszaállítása (katasztrófa utáni helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Megbízható állapot Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* [Bevezetés a Service Fabric webszolgáltatások API használatába](service-fabric-reliable-services-communication-webapi.md)
* [A megbízható szolgáltatások programozási modell speciális használati](service-fabric-reliable-services-advanced-usage.md)
* [Fejlesztői leírás megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
