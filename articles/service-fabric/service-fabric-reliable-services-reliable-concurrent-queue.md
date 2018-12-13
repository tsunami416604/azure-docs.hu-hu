---
title: Az Azure Service Fabric ReliableConcurrentQueue
description: ReliableConcurrentQueue, amely lehetővé teszi a párhuzamos enqueues és dequeues nagy átviteli sebességű üzenetsor.
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: twhitney
ms.openlocfilehash: 61b53a23fdbb08b226878d9b702ec6bb2879f8bc
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185035"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Az Azure Service Fabric ReliableConcurrentQueue bemutatása
Megbízható egyidejű üzenetsor egy aszinkron, a tranzakciós és a replikált üzenetsor mely funkciók nagy feldolgozási sorba helyezni a, és eltávolítása a sorból műveletek. Célja a nagy átviteli sebességű és kis késése révén a szigorú FIFO rendezése által biztosított lazítani [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx) és a egy legjobb rendezése biztosítja.

## <a name="apis"></a>API-k

|Egyidejű várólista                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| typ void Enqueue(T item)           | A feladat EnqueueAsync (ITransaction tx, T elem)                       |
| logikai TryDequeue (végre T eredmény)  | < < T >> ConditionalValue feladat TryDequeueAsync (ITransaction tx)  |
| int Count()                    | hosszú Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Összehasonlítás a [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Megbízható egyidejű várólista érhető el alternatív megoldásként [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx). Használandó azokban az esetekben, ahol szigorú FIFO rendezése nem kötelező, mint garantáló FIFO igényel a kompromisszummal jár együtt.  [Megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx) kényszerítéséhez FIFO rendezése a legfeljebb engedélyezett állítható sorba egy-egy tranzakció és engedélyezett a eltávolítása a sorból egyszerre legfeljebb egy-egy tranzakció zárolásokat használja. Ezzel szemben a megbízható egyidejű várólista visszaállítja a rendelési korlátozás, és lehetővé teszi, hogy azok sorba interleave és eltávolítása a sorból műveletek száma egyidejű tranzakciók. Legjobb sorrendje nincs megadva, a azonban két érték egy megbízható egyidejű üzenetsorban relatív sorrendjének soha nem garantálható.

Megbízható egyidejű üzenetsor nagyobb átviteli sebességet és kisebb késést biztosít [megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx) , amikor több egyidejű tranzakciók végrehajtása enqueues és/vagy dequeues.

A minta használati eset az ReliableConcurrentQueue van a [üzenet-várólista](https://en.wikipedia.org/wiki/Message_queue) forgatókönyv. Ebben a forgatókönyvben egy vagy több üzenet gyártók létrehozása és elemek hozzáadása az üzenetsorba, és egy vagy több üzenet a fogyasztók a várólistában lévő üzenetek és feldolgozni azokat. Több előállítók és fogyasztók dolgozhatnak egymástól függetlenül, annak érdekében, hogy a várólista feldolgozása több tranzakció használatával.

## <a name="usage-guidelines"></a>Használati útmutató
* A várólista vár, hogy a várólistában lévő elemek alacsony megőrzési idővel van-e. Azt jelenti az elemek lenne nem marad a várólistán lévő hosszú ideje.
* A várólista nem garantálja a szigorú FIFO rendezése.
* A várólista nem olvassa a saját írási műveletek. Ha egy elem várólistán lévő tranzakción belül, nem lesz látható egy dequeuer ugyanazon a tranzakción belül.
* Dequeues amelyek nem különítve egymástól. Ha elem *A* el távolítva a sorból tranzakcióban *txnA*, annak ellenére, hogy *txnA* nem lesz érvényes, elem *A* nem lenne látható egy egyidejű tranzakció *txnB*.  Ha *txnA* megszakítása, *A* számára látható lesz *txnB* azonnal.
* *TryPeekAsync* viselkedés valósítható használatával egy *TryDequeueAsync* és majd a tranzakció megszakítása. Ilyen például a programozási minták szakaszában található.
* Számláló értéke nem tranzakciós. Azt reális képet kaphat az elemek számát az üzenetsorban, használható, de egy-időponthoz jelöli, és nem lehet hivatkozni.
* Dequeued elemek költséges terhelése kell hajtható végre, amíg a tranzakció aktív, hosszú ideig futó tranzakciókban, amely a rendszeren a teljesítményre gyakorolt hatás elkerülése érdekében.

## <a name="code-snippets"></a>Kódrészletek
Lássunk néhány kódrészleteket és a kibocsátásukra várt. Kivételkezelés ebben a szakaszban figyelmen kívül hagyja.

### <a name="enqueueasync"></a>EnqueueAsync
Az alábbiakban néhány kódrészleteket követi a várt kimeneti EnqueueAsync használatával.

- *1. eset: Egyetlen sorba feladat*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és, hogy voltak-e a várólista módosítása egyidejű tranzakciók. A felhasználó a várólistán, a következő rendelését bármely tartalmaznia várható:

> 10, 20

> 20, 10


- *2. eset: A párhuzamos sorba feladat*

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

Tegyük fel, hogy a feladatokat, sikeresen befejeződött, hogy futtatta-e feladatok párhuzamosan, és, hogy voltak-e a várólista módosítása más egyidejű tranzakciók. A várólistában lévő elemek sorrendjének nincs következtetésekhez végezhető. Ez a kódrészlet esetében a is megjelenhetnek a 4 bármelyikét! lehetséges található.  A várólista megkísérli az elemek megtartása az eredeti (sorba) sorrendben, de sorrendjét húzással módosíthatja az egyidejű művelet vagy hibák miatt is kényszeríthető.


### <a name="dequeueasync"></a>DequeueAsync
Az alábbiakban néhány kódrészleteket követi a várt kimeneti TryDequeueAsync használatával. Tegyük fel, hogy a várólista már megjelenik a következő a várólistában lévő elemek:
> 10, 20, 30, 40, 50, 60

- *1. eset: Egyetlen feladat eltávolítása a sorból*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és, hogy voltak-e a várólista módosítása egyidejű tranzakciók. Mivel nincs következtetésekhez végezhető a várólistában lévő elemek sorrendjét, bármely harmadik elem előfordulhat, hogy lehet el távolítva a sorból, bármilyen sorrendben. A várólista megkísérli az elemek megtartása az eredeti (sorba) sorrendben, de sorrendjét húzással módosíthatja az egyidejű művelet vagy hibák miatt is kényszeríthető.  

- *2. eset: A feladat párhuzamos feldolgozásához*

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

Tegyük fel, hogy a feladatokat, sikeresen befejeződött, hogy futtatta-e feladatok párhuzamosan, és, hogy voltak-e a várólista módosítása más egyidejű tranzakciók. Mivel a várólistára, a lista elemeinek sorrendje nincs következtetésekhez végezhető *dequeue1* és *dequeue2* egyes bármely két elemet tartalmaz, bármilyen sorrendben.

Az azonos elem lesz *nem* mindkét lista jelenik meg. Ezért ha dequeue1 *10*, *30*, majd dequeue2 kellene *20*, *40*.

- *3. eset: Rendezés a tranzakció megszakítása a sorból*

Az átvitel alatt egy tranzakció megszakítása dequeues elemek biztonsági fejlécére, az üzenetsorban lévő helyez. A sorrendet, amelyben a cikkek kerüljenek vissza a fejlécére, az üzenetsor nem garantált. Ossza meg velünk tekintse meg a következő kódot:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Tegyük fel, hogy elemek lettek el távolítva a sorból a következő sorrendben:
> 10, 20

A tranzakció megszakítása azt, ha az elemek kell hozzáadni vissza fejlécére, az üzenetsor, a következő rendelések egyikében:
> 10, 20

> 20, 10

Ugyanez érvényes minden olyan esetben, ha a tranzakció nem volt sikeres *lekötött*.

## <a name="programming-patterns"></a>Programozási minták
Ebben a szakaszban lássunk néhány programozási mintázatok használatával ReliableConcurrentQueue hasznosak lehetnek.

### <a name="batch-dequeues"></a>A Batch Dequeues
Programozási minta van a felhasználói tevékenység kötegelt ajánljuk a helyett egyikével dequeues eltávolítása a sorból, egyszerre. A felhasználó kiválaszthat a szabályozás között minden batch vagy a Köteg mérete. A következő kódrészlet azt mutatja be, a programozási modellt.  Vegye figyelembe, hogy ebben a példában a feldolgozás befejezése után a tranzakció véglegesítése, így ha egy tartalék feldolgozásakor fordulhat elő, a feldolgozatlan elemek feldolgozás nélkül vesznek el.  Azt is megteheti a feldolgozási megteheti a tranzakció-hatókörben, azonban ez negatív hatással lehet a teljesítményre, és már feldolgozta a cikkek kezelése igényel.

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

### <a name="best-effort-notification-based-processing"></a>Legjobb Notification-alapú feldolgozás
Egy másik, érdekesebb programozási minta a Count API-t használja. Itt hoznunk legjobb notification-alapú, hogy a várólista feldolgozása. A várólista száma egy sorba vagy eltávolítása onnan feladat szabályozás használható.  Előfordulhat, hogy az előző példában látható módon, mert a feldolgozás akkor fordul elő, a tranzakción kívülre feldolgozatlan elemek elveszett feldolgozásakor hiba esetén.

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
A kiürítési várólista nem garantálható az adatok struktúrája egyidejű jellege miatt.  Ez nem lehetséges, hogy akkor is, ha nincsenek felhasználói műveletek, az üzenetsor átvitel közben, egy adott hívás TryDequeueAsync nesmí vracet korábban várólistára helyezett elem és véglegesített.  A várólistán lévő elem garantáltan *végül* láthatóvá eltávolítása a sorból, azonban anélkül, hogy egy sávon kívüli kommunikációs mechanizmus egy független fogyasztói nem tudja, hogy a várólista elérte a egyenletes még akkor is, ha minden gyártó a rendszer leállt, nem új sorba műveletek engedélyezettek. A kiürítési művelet, így a legjobb alábbi megvalósított módon.

A felhasználó minden további előállítói és fogyasztói feladatok állítsa le, és várjon, amíg a véglegesítés vagy megszakítás, mielőtt megkísérelné a várólista kiürítési szükségszerű tranzakciók.  Ha a felhasználó tudja a várólistában lévő elemek várt száma, egy értesítés, amely azt jelzi, hogy minden elem rendelkezik lett el távolítva a sorból tudja állítani.

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
ReliableConcurrentQueue nem biztosít a *TryPeekAsync* API-t. Felhasználók férhetnek betekintés szemantikai használatával egy *TryDequeueAsync* és majd a tranzakció megszakítása. Ebben a példában dequeues csak akkor, ha az elem értéke nagyobb mint feldolgozása *10*.

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
* [A Reliable Services – Gyorsútmutató](service-fabric-reliable-services-quick-start.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [A Reliable Services-értesítések](service-fabric-reliable-services-notifications.md)
* [A Reliable Services biztonsági mentési és visszaállítási (katasztrófa utáni helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [A Reliable State Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* [Ismerkedés a Service Fabric webes API-szolgáltatások](service-fabric-reliable-services-communication-webapi.md)
* [A Reliable Services programozási modell speciális használata](service-fabric-reliable-services-advanced-usage.md)
* [A Reliable Collections – fejlesztői referencia](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
