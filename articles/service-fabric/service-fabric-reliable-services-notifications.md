---
title: Megbízható szolgáltatásokról szóló értesítések
description: A Service Fabric megbízható szolgáltatásokértesítéseinek koncepcionális dokumentációja a Megbízható állapotkezelő és a Megbízható szótár számára
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 1f3239ea1da252ccd84c6572b562756c8fd1677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639564"
---
# <a name="reliable-services-notifications"></a>Megbízható szolgáltatásokról szóló értesítések
Az értesítések lehetővé teszik az ügyfelek számára, hogy nyomon kövessék az őket érdeklő objektumon végrehajtott módosításokat. Kétféle objektum támogatja az értesítéseket: *Megbízható állapotkezelő* és *megbízható szótár.*

Az értesítések használatának gyakori okai a következők:

* Épület materializált nézetek, például a másodlagos indexek vagy a replika állapotának összesített szűrt nézetek. Egy példa a Megbízható szótár összes kulcsának rendezett indexe.
* Figyelési adatok küldése, például az elmúlt órában hozzáadott felhasználók száma.

Az értesítések a műveletek alkalmazásának részeként aktiválódnak. Emiatt az értesítéseket a lehető leggyorsabban kell kezelni, és a szinkron események nem tartalmazhatnak költséges műveleteket.

## <a name="reliable-state-manager-notifications"></a>Megbízható állapotkezelői értesítések
A Reliable State Manager a következő eseményekről biztosít értesítéseket:

* Tranzakció
  * Véglegesítés
* Államkezelő
  * Újraépítés
  * Megbízható állapot hozzáadása
  * A megbízható állapot eltávolítása

Megbízható állapotkezelő nyomon követi az aktuális bejövő tranzakciókat. A tranzakcióállapot egyetlen olyan változása, amely az értesítés ellövését okozza, az a véglegesített tranzakció.

Megbízható állapotkezelő megbízható állapotok, például a megbízható szótár és a megbízható várólista gyűjteményét tartja fenn. Megbízható Állapotkezelő tüzek értesítések, ha ez a gyűjtemény megváltozik: egy megbízható állapot hozzáadása vagy eltávolítása, vagy a teljes gyűjtemény újraépül.
A Megbízható állapotkezelő gyűjtemény három esetben épül újjá:

* Helyreállítás: Amikor egy replika elindul, helyreállítja a korábbi állapotát a lemezről. A helyreállítás végén az **NotifyStateManagerChangedEventArgs** segítségével indít el egy olyan eseményt, amely a helyreállított megbízható állapotok készletét tartalmazza.
* Teljes másolat: Mielőtt egy replika csatlakozhatna a konfigurációkészlethez, meg kell építeni. Néha ez megköveteli a reliable state manager állapotának teljes másolatát az elsődleges replika az alapjárati másodlagos replika kell alkalmazni. Megbízható állapotkezelő a másodlagos replika használja **NotifyStateManagerChangedEventArgs** a tűz egy eseményt, amely tartalmazza a megbízható állapotok, amelyek az elsődleges replika általbeszerzett.
* Visszaállítás: Vész-helyreállítási forgatókönyvekben a replika állapota visszaállítható egy biztonsági másolatból **a RestoreAsync**segítségével. Ilyen esetekben az elsődleges replika Megbízható állapotkezelője az **NotifyStateManagerChangedEventArgs** használatával indít egy olyan eseményt, amely a biztonsági másolatból visszaállított megbízható állapotok készletét tartalmazza.

A tranzakciós értesítések és/vagy állapotkezelő értesítések regisztrálásához regisztrálnia kell a **TransactionChanged** vagy **statemanagerChanged** eseményeket a Reliable State Manager-en. Az eseménykezelőkkel való regisztráció gyakori helye az állapotalapú szolgáltatás konstruktora. Amikor regisztrál a konstruktoron, nem fog kihagyni egyetlen olyan értesítést sem, amelyet az **IReliableStateManager**élettartama során bekövetkező változás okoz.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

A **TransactionChanged** eseménykezelő **notifytransactionchangedeventargs** segítségével adja meg az esemény részleteit. A művelettulajdonságot (például **NotifyTransactionChangedAction.Commit)** tartalmazza, amely a módosítás típusát adja meg. Tartalmazza a tranzakciótulajdonságot is, amely a módosított tranzakcióra mutató hivatkozást tartalmaz.

> [!NOTE]
> Ma **a TransactionChanged** események csak akkor kerülnek előzésre, ha a tranzakció véglegesítve van. A művelet ekkor megegyezik **az NotifyTransactionChangedAction.Commit**. De a jövőben események jelenhetnek meg más típusú tranzakcióállapot-változások esetén. Javasoljuk, hogy csak akkor ellenőrizze a műveletet, és csak akkor dolgozza fel az eseményt, ha az a várt.
> 
> 

Az alábbiakban egy példa **TransactionChanged** eseménykezelő.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

A **StateManagerChanged** eseménykezelő **az NotifyStateManagerChangedEventArgs** segítségével adja meg az esemény részleteit.
**Az NotifyStateManagerChangedEventArgs** két alosztályt rendelkezik: **NotifyStateManagerRebuildEventArgs** és **NotifyStateManagerSingleEntityChangedEventArgs**.
Az **NotifyStateManagerChangedEventArgs** művelettulajdonsággal a megfelelő alosztályra vetheti az **NotifyStateManagerChangedEventArgs tulajdonságot:**

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** és **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Az alábbiakban egy példa **StateManagerChanged** értesítéskezelő.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Megbízható szótári értesítések
A Reliable Dictionary a következő eseményekről biztosít értesítéseket:

* Újraépítés: Akkor hívható meg, ha a **ReliableDictionary** helyreállt az állapota egy helyreállított vagy másolt helyi állapotból vagy biztonsági másolatból.
* Egyértelmű: Akkor hívható, ha a **ReliableDictionary** állapota törlődött a **ClearAsync** metóduson keresztül.
* Hozzáadás: Akkor hívva, ha egy elem hozzá lett adva a **ReliableDictionary-hez.**
* Frissítés: Meghívva, ha az **IReliableDictionary** egy eleme frissítve lett.
* Eltávolítás: Akkor hívható, ha az **IReliableDictionary** egy elemét törölték.

A Megbízható szótár értesítések lehívásához regisztrálnia kell az **IReliableDictionary Dictionary** **Dictionary Dictionary Dictionary szótárszótár szótárszótárszótárszótárszótárszótár-kapcsolatán.** Az eseménykezelőkkel való regisztráció gyakori helye a **ReliableStateManager.StateManagerChanged** add notification.
Az **IReliableDictionary** **iReliableStateManager-hez** való hozzáadásakor történő regisztráció biztosítja, hogy nem marad le egyetlen értesítésről sem.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** az előző **OnStateManagerChangedHandler** példa által meghívott mintametódus.
> 
> 

Az előző kód beállítja az **IReliableNotificationAsyncCallback** felületet a **DictionaryChanged**-el együtt. Mivel **az NotifyDictionaryRebuildEventArgs** egy **IAsyncEnumerable** felületet tartalmaz – amelyet aszinkron módon kell számba venni –, az újraépítési értesítések az **OnDictionaryChangedHandler**helyett **a RebuildNotificationAsyncCallback -, hanem a RebuildNotificationAsyncCallback** metóduson keresztül indulnak el.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> Az előző kódban az újraépítési értesítés feldolgozásának részeként először a karbantartott összesített állapot törlődik. Mivel a megbízható gyűjtemény új állapottal épül újjá, minden korábbi értesítés lényegtelen.
> 
> 

A **DictionaryChanged** eseménykezelő **az NotifyDictionaryChangedEventArgs** segítségével adja meg az esemény részleteit.
**Az NotifyDictionaryChangedEventArgs** öt alosztályt rendelkezik. Az **NotifyDictionaryChangedEventArgs** művelettulajdonsággal a megfelelő alosztályra adja az **NotifyDictionaryChangedEventArgs tulajdonságot:**

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Javaslatok
* *Végezze el* a teljes értesítési eseményeket a lehető leggyorsabban.
* *Ne* hajtson végre költséges műveleteket (például I/O-műveleteket) szinkron események részeként.
* *Az* esemény feldolgozása előtt ellenőrizze a művelet típusát. A jövőben új művelettípusok adhatók hozzá.

Íme néhány dolog, amit szem előtt kell tartania:

* Az értesítések egy művelet végrehajtásának részeként aktiválódnak. Például egy visszaállítási értesítés a visszaállítási művelet utolsó lépéseként aktiválódik. A visszaállítás nem fejeződik be, amíg az értesítési esemény feldolgozása meg nem történik.
* Mivel az értesítések az alkalmazási műveletek részeként aktiválódnak, az ügyfelek csak a helyileg véglegesített műveletekértesítéseit látják. És mivel a műveletek garantáltan csak helyileg véglegesített (más szóval, naplózott), lehet, hogy a jövőben visszavonhatók vagy nem.
* A redo elérési út, minden alkalmazott művelet egyetlen értesítés aktiválódik. Ez azt jelenti, hogy ha a T1 tranzakció tartalmazza a Create(X), Delete(X) és Create(X) tranzakciót, akkor egy értesítést kap az X létrehozásáról, egyet a törlésről, és egyet a létrehozáshoz, ebben a sorrendben.
* A több műveletet tartalmazó tranzakciók esetén a műveletek abban a sorrendben kerülnek alkalmazásra, ahogyan az elsődleges replikán a felhasználótól érkeztek.
* A hamis folyamat feldolgozásának részeként előfordulhat, hogy egyes műveletek et visszavonni. Az ilyen visszavonási műveletekről értesítések kerülnek elő, és a replika állapota visszaáll egy stabil pontra. A visszavonási értesítések egyik fontos különbsége, hogy az ismétlődő kulcsokkal rendelkező események összesítése történik. Ha például a T1 tranzakciót visszavonni, egyetlen értesítés jelenik meg a Delete(X) számára.

## <a name="next-steps"></a>További lépések
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Megbízható szolgáltatások – gyorsindítás](service-fabric-reliable-services-quick-start.md)
* [Megbízható szolgáltatások biztonsági mentése és visszaállítása (vészhelyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Fejlesztői referencia a megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

