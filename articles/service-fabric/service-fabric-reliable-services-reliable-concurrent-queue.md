---
title: ReliableConcurrentQueue az Azure Service Fabricben
description: A ReliableConcurrentQueue egy nagy átviteli sebességű várólista, amely lehetővé teszi a párhuzamos várólistákat és a várólisták törlését.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: a7115db8259fde0e87e53557ecef730f8e82d2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462736"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Bevezetés a ReliableConcurrentQueue szolgáltatásba az Azure Service Fabricben
A Megbízható egyidejű várólista egy aszinkron, tranzakciós és replikált várólista, amely magas egyidejűségi adatokat tartalmaz az enqueue és a dequeue műveletekhez. Úgy tervezték, hogy nagy átviteli és alacsony késleltetést biztosítson a [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx) által biztosított szigorú FIFO rendelés lazításával, és ehelyett a legjobb megrendelést biztosítja.

## <a name="apis"></a>API-k

|Egyidejű várólista                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T elem)           | Feladat EnqueueAsync(ITransaction tx, T elem)                       |
| bool TryDequeue(ki T eredmény)  | A Feltételes érték< < T > > A TryDequeueAsync(ITransaction tx) feladat  |
| int Count()                    | hosszú szám()                                                     |

## <a name="comparison-with-reliable-queue"></a>Összehasonlítás [a megbízható várólistával](https://msdn.microsoft.com/library/azure/dn971527.aspx)

A megbízható egyidejű várólista a [Megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx)alternatívájaként érhető el. Olyan esetekben kell használni, amikor nincs szükség szigorú FIFO rendelésre, mivel a FIFO garantálása kompromisszumot igényel az egyidejűséggel.  [A Megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx) zárolásokat használ a FIFO-rendelés kényszerítéséhez, és legbőlegegy tranzakció tanusíthatja a várólistát, és egyszerre legtöbb tranzakció torkig van a várólistával. Összehasonlításképpen, megbízható egyidejű várólista ellazítja a rendelési megkötést, és lehetővé teszi, hogy tetszőleges számú egyidejű tranzakciók interleave a várólistán, és dequeue műveleteket. A legjobb rendelés biztosított, azonban a megbízható egyidejű várólistában lévő két érték relatív sorrendje soha nem garantálható.

A megbízható egyidejű várólista nagyobb átviteli és kisebb késleltetést biztosít, mint [a Megbízható várólista,](https://msdn.microsoft.com/library/azure/dn971527.aspx) ha több egyidejű tranzakció van várólistákés/vagy dequeues végrehajtása korlátja.

A ReliableConcurrentQueue mintahasználati esete az [üzenetvárólista](https://en.wikipedia.org/wiki/Message_queue) forgatókönyv. Ebben az esetben egy vagy több üzenetkészítő hoz létre és ad hozzá elemeket a várólistához, és egy vagy több üzenetfogyasztó lekéri az üzeneteket a várólistából, és feldolgozza azokat. Több gyártó és fogyasztó is dolgozhat egymástól függetlenül, egyidejű tranzakciók használatával a várólista feldolgozásához.

## <a name="usage-guidelines"></a>Használati irányelvek
* A várólista arra számít, hogy a várólistában lévő elemek megőrzési ideje alacsony. Ez azt jelenti, hogy az elemek hosszú ideig nem maradnak a sorban.
* A várólista nem garantálja a szigorú FIFO rendelést.
* A várólista nem olvassa be a saját írási műveleteket. Ha egy cikk várólistára kerül egy tranzakción belül, az nem lesz látható az ugyanazon tranzakción belüli dequeuer számára.
* A várólisták nincsenek elszigetelve egymástól. Ha az *A* cikk várólistán marad a *txnA*tranzakcióban, annak ellenére, hogy a *txnA* nincs véglegesítve, az *A* cikk nem lesz látható az egyidejű tranzakció *txnB számára.*  Ha *a txnA* megszakítja a műveletet, az *A* azonnal láthatóvá válik a *txnB számára.*
* *A TryPeekAsync* viselkedése egy *TryDequeueAsync* használatával valósítható meg, majd a tranzakció megszakításával. Egy példa erre a viselkedésre a Programozási minták szakaszban található.
* A számlálás nem tranzakciós. Használható a várólista elemeinek számának megismerésére, de pontaz időpontban, és nem lehet rájuk támaszkodni.
* A várólistán lévő cikkek költséges feldolgozását nem szabad végrehajtani, amíg a tranzakció aktív, hogy elkerülhető legyen a hosszú ideig futó tranzakciók, amelyek hatással lehetnek a rendszer teljesítményére.

## <a name="code-snippets"></a>Kódtöredékek
Nézzünk meg néhány kódrészletet és azok várható kimeneteit. Ebben a szakaszban a kivételkezelést figyelmen kívül hagyja.

### <a name="instantiation"></a>Példányosítás
Egy megbízható egyidejű várólista példányának létrehozása hasonló bármely más megbízható gyűjteményhez.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Íme néhány kódrészlet az EnqueueAsync és a várt kimenetek használatához.

- *1. eset: Egyszeri várólistás feladat*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és hogy nem módosították egyidejűtranzakciók a várólistát. A felhasználó számíthat arra, hogy a várólista az alábbi rendelések bármelyikében tartalmazza az elemeket:

> 10, 20
> 
> 20, 10


- *2. eset: Párhuzamos várólistás feladat*

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

Tegyük fel, hogy a feladatok sikeresen befejeződtek, a feladatok párhuzamosan futottak, és hogy nem volt más egyidejű tranzakció a várólistán módosítva. A várólistában lévő elemek sorrendjére nem lehet következtetéseket levonni. Ehhez a kódrészlethez az elemek a 4- ben megjelenhetnek! lehetséges megrendeléseket.  A várólista megkísérli az elemeket az eredeti (várólistára helyezett) sorrendben tartani, de előfordulhat, hogy egyidejű műveletek vagy hibák miatt át kell rendelniőket.


### <a name="dequeueasync"></a>DequeueAsync
Íme néhány kódrészlet a TryDequeueAsync és a várt kimenetek használatára. Tegyük fel, hogy a várólista már ki van töltve a következő elemekkel a várólistában:
> 10, 20, 30, 40, 50, 60

- *1. eset: Egyszeri várólistára állítási feladat*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Tegyük fel, hogy a feladat sikeresen befejeződött, és hogy nem módosították egyidejűtranzakciók a várólistát. Mivel a várólistában lévő elemek sorrendjére vonatkozóan nem lehet következtetéseket levonni, a várólistán lévő elemek bármelyike bármilyen sorrendben megvonható a várólistából. A várólista megkísérli az elemeket az eredeti (várólistára helyezett) sorrendben tartani, de előfordulhat, hogy egyidejű műveletek vagy hibák miatt át kell rendelniőket.  

- *2. eset: Párhuzamos várólistára állítási feladat*

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

Tegyük fel, hogy a feladatok sikeresen befejeződtek, a feladatok párhuzamosan futottak, és hogy nem volt más egyidejű tranzakció a várólistán módosítva. Mivel a várólistában lévő elemek sorrendjére vonatkozóan nem lehet következtetéseket levonni, a *várólisták1* és a *dequeue2* listák mindegyike két elemet tartalmaz, bármilyen sorrendben.

Ugyanaz az elem *nem* jelenik meg mindkét listában. Ezért ha a dequeue1 *10*, *30*, majd dequeue2 volna *20*, *40*.

- *3. eset: A rendezés megszakítása tranzakciómegszakítással*

Ha megszakítja a művelet közbeni devárálltással rendelkező tranzakciót, az elemek visszakerülnek a várólista fejére. A cikkek várólistára kerülésének sorrendje nem garantált. Nézzük meg a következő kódot:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Tegyük fel, hogy a cikkek várólistára kerültek a következő sorrendben:
> 10, 20

A tranzakció megszakításakor a cikkek a következő rendelések bármelyikében visszakerülnek a várólista élére:
> 10, 20
> 
> 20, 10

Ugyanez vonatkozik minden olyan esetre, amikor a tranzakció *véglegesítése*nem sikerült .

## <a name="programming-patterns"></a>Programozási minták
Ebben a szakaszban tekintse meg néhány programozási minták, amelyek hasznosak lehetnek a ReliableConcurrentQueue használatával.

### <a name="batch-dequeues"></a>Kötegelt várólisták
Az ajánlott programozási minta az, hogy a fogyasztói feladat kötegelje a dequeues végrehajtása helyett egy dequeue egy időben. A felhasználó dönthet úgy, hogy minden köteg vagy a köteg mérete közötti késések szabályozása. A következő kódrészlet ezt a programozási modellt mutatja. Ne feledje, hogy ebben a példában a feldolgozás a tranzakció véglegesítése után történik, így ha a feldolgozás során hiba történik, a feldolgozatlan cikkek feldolgozás nélkül elvesznek.  Másik lehetőségként a feldolgozás elvégezhető a tranzakció hatókörén belül, azonban negatív hatással lehet a teljesítményre, és a már feldolgozott cikkek kezelését igényli.

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

### <a name="best-effort-notification-based-processing"></a>Legjobb értesítésen alapuló feldolgozás
Egy másik érdekes programozási minta a Count API-t használja. Itt valósíthatjuk meg a legjobb an- és kézhezállású értesítés-alapú feldolgozást a várólistához. A várólistaszáma egy enqueue vagy egy dequeue feladat szabályozására használható.  Vegye figyelembe, hogy az előző példához, mivel a feldolgozás a tranzakción kívül történik, a feldolgozatlan cikkek elveszhetnek, ha hiba történik a feldolgozás során.

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

### <a name="best-effort-drain"></a>A legjobb erőfeszítés drain
A várólista kiürítése nem garantálható az adatstruktúra egyidejű jellege miatt.  Lehetséges, hogy még akkor is, ha a várólistán egyetlen felhasználói művelet sem van folyamatban, előfordulhat, hogy a TryDequeueAsync hívása nem ad vissza korábban várólistára helyezett és véglegesített elemet.  A várólistára helyezett elem *garantáltan láthatóvá* válik a várólistából való törléshez, azonban a sávon kívüli kommunikációs mechanizmus nélkül a független fogyasztó nem tudhatja meg, hogy a várólista állandósult állapotba került, még akkor sem, ha az összes gyártó leállt, és nincsenek új várólistára állított műveletek. Így a leeresztési művelet a legjobb erőfeszítés az alábbiak szerint.

A felhasználónak le kell állítania az összes további gyártói és fogyasztói feladatot, és meg kell várnia a repülés közbeni tranzakciók véglegesítését vagy megszakítását, mielőtt megpróbálna kiüríteni a várólistát.  Ha a felhasználó ismeri a várólistában lévő elemek várható számát, beállíthat egy értesítést, amely jelzi, hogy az összes elem várólistán lett.

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
A ReliableConcurrentQueue nem biztosítja a *TryPeekAsync* api-t. A felhasználók a *tryDequeueAsync* használatával lejuthatnak a betekintő szemantikai elemre, majd megszakíthatják a tranzakciót. Ebben a példában a várólisták törlése csak akkor lesz feldolgozva, ha a cikk értéke *10-nél*nagyobb.

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

## <a name="must-read"></a>Kell olvasni
* [Megbízható szolgáltatások rövid útmutató](service-fabric-reliable-services-quick-start.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Megbízható szolgáltatásokról szóló értesítések](service-fabric-reliable-services-notifications.md)
* [Megbízható szolgáltatások biztonsági mentése és visszaállítása (vészhelyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Megbízható állapotkezelő-konfiguráció](service-fabric-reliable-services-configuration.md)
* [A Service Fabric webAPI-szolgáltatások első lépései](service-fabric-reliable-services-communication-webapi.md)
* [A megbízható szolgáltatások programozási modelljének speciális használata](service-fabric-reliable-services-advanced-usage.md)
* [Fejlesztői kézikönyv megbízható gyűjtemények esetén](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
