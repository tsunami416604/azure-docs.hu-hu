---
title: A Reliable Services-értesítések |} A Microsoft Docs
description: Service Fabric Reliable Services értesítések fogalmi dokumentációja
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: a3df5f28475b03f1799dc1e245c3a7e904b49cb3
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662669"
---
# <a name="reliable-services-notifications"></a>A Reliable Services-értesítések
Értesítések engedélyezése az ügyfelek nyomon követheti, hogy érdeklődik az objektumhoz végrehajtott módosításokat. Két típusú objektumok értesítéseket támogatja: *A Reliable State Manager* és *megbízható szótárban*.

Értesítések a leggyakoribb okai a következők:

* Épület materializált nézetek, például a másodlagos indexeket, vagy szűrt nézeteinek a replika állapotát összesíti. Ilyen például, egy megbízható szótárban az összes kulcs rendezett indexét.
* Küldő figyelési adatok, például az elmúlt órában hozzáadott felhasználók száma.

Értesítések vannak aktivált műveletek alkalmazása részeként. Ezért értesítések lehetséges és szinkron események ne tartalmazza azokat a költséges műveleteket minél gyorsabban kell kezelni.

## <a name="reliable-state-manager-notifications"></a>A Reliable State Manager értesítések
A Reliable State Manager a következő események értesítéseket biztosít:

* Tranzakció
  * Véglegesítés
* Állapotkezelő
  * Újraépítés
  * Egy megbízható állapot hozzáadása
  * Egy megbízható állapot eltávolítása

A Reliable State Manager nyomon követi az aktuális megszakít tranzakciók. Az egyetlen változás, amelynek hatására aktiválódik egy értesítési tranzakció állapotban a tranzakció véglegesítése.

A Reliable State Manager fenntart egy megbízható szótárban és megbízható várólista például megbízható állapotainak gyűjteményét. A Reliable State Manager értesítések akkor aktiválódik, ha módosítja a gyűjtemény: olyan megbízható állapotban van hozzáadva vagy eltávolítva, vagy a teljes gyűjteményt újraépítésekor.
A Reliable State Manager gyűjtemény újraépítésekor három esetben:

* Helyreállítás: Amikor elindul egy replikát, azt korábbi állapotba állítja helyre a lemezről. A helyreállítás végén használ **NotifyStateManagerChangedEventArgs** üzenetszám egy eseményt, amely azokat a helyreállított megbízható állapotok.
* Teljes másolatát: Replika csatlakozhat a konfigurációs készlet, mielőtt rendelkezik kell létrehozni. Egyes esetekben ehhez az elsődleges replikából a alkalmazni lehessen a tétlen másodlagos replikának a Reliable State Manager állapot teljes másolata. A Reliable State Manager a másodlagos replikát használ **NotifyStateManagerChangedEventArgs** üzenetszám egy eseményt, amely azokat a megbízható állapotok, akkor megszerezte az elsődleges replikából.
* Visszaállítás: A vészhelyreállítási forgatókönyveket, a replika állapota keresztül másolatból visszaállított **RestoreAsync**. Ezekben az esetekben használja az elsődleges replikán a Reliable State Manager **NotifyStateManagerChangedEventArgs** üzenetszám egy eseményt, amely azokat a megbízható, a biztonsági másolatból visszaállított állapotok.

Tranzakció-értesítések és/vagy manager állapotértesítésekre regisztrál, regisztrálnia kell a **TransactionChanged** vagy **StateManagerChanged** a Reliable State Manager eseményeket. Ezek eseménykezelők regisztrálni egy közös helyen, az állapotalapú szolgáltatás a konstruktor. Ha regisztrálja a konstruktort, bármilyen értesítés, hogy a rendszer élettartama során megváltozása miatt nem hagyja ki **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

A **TransactionChanged** eseménykezelő használ **NotifyTransactionChangedEventArgs** adja meg az eseménnyel kapcsolatos adatait. A művelet tulajdonságot tartalmaz (például **NotifyTransactionChangedAction.Commit**), amely meghatározza, hogy egy olyan változást. A tranzakció tulajdonság által biztosított módosult a tranzakció egy hivatkozást is tartalmaz.

> [!NOTE]
> Még ma **TransactionChanged** események aktiválódnak, csak akkor, ha a tranzakció véglegesítése. A művelet egyenlő majd **NotifyTransactionChangedAction.Commit**. De a jövőben események tranzakció állapotváltozások más típusú előfordulhat, hogy generál. Javasoljuk, hogy a művelet ellenőrzi, és az esemény feldolgozása, csak akkor, ha egy várt.
> 
> 

Példa **TransactionChanged** eseménykezelő.

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

A **StateManagerChanged** eseménykezelő használ **NotifyStateManagerChangedEventArgs** adja meg az eseménnyel kapcsolatos adatait.
**NotifyStateManagerChangedEventArgs** két alosztályok rendelkezik: **NotifyStateManagerRebuildEventArgs** és **NotifyStateManagerSingleEntityChangedEventArgs**.
A művelet tulajdonsággal a **NotifyStateManagerChangedEventArgs** típussá **NotifyStateManagerChangedEventArgs** , a megfelelő alosztályát:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** és **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Példa **StateManagerChanged** értesítéskezelőjének.

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

## <a name="reliable-dictionary-notifications"></a>Megbízható szótár értesítések
Megbízható szótárban az értesítések a következő események biztosítja:

* Építse újra: Meghívva **ReliableDictionary** állt állapotában a helyreállított vagy másolt helyi állapot vagy a biztonsági mentés.
* Törlése: Meghívva állapotát **ReliableDictionary** keresztül törölve lett a **ClearAsync** metódust.
* Adja hozzá a következőket: Egy elem hozzáadása után a nevű **ReliableDictionary**.
* Frissítés: Meghívva, ha az egyik elemére **IReliableDictionary** frissítve lett.
* Távolítsa el: Meghívva, ha az egyik elemére **IReliableDictionary** törölve lett.

Megbízható szótárban értesítéseket kaphat, regisztrálnia kell a **DictionaryChanged** eseménykezelőt a **IReliableDictionary**. Ezek eseménykezelők regisztrálni egy közös helyen szerepel a **ReliableStateManager.StateManagerChanged** értesítés beállítása.
Ha regisztrálja **IReliableDictionary** adnak hozzá **IReliableStateManager** biztosítja, hogy minden olyan értesítések nem hagyja ki.

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
> **ProcessStateManagerSingleEntityNotification** a mintául szolgáló módszer, amely az előző **OnStateManagerChangedHandler** példa.
> 
> 

Az előző kód beállítja a **IReliableNotificationAsyncCallback** kommunikáljanak, valamint a **DictionaryChanged**. Mivel **NotifyDictionaryRebuildEventArgs** tartalmaz egy **IAsyncEnumerable** illesztő – amely aszinkron módon – számba kell Újraépítés értesítések keresztül vannak aktivált  **RebuildNotificationAsyncCallback** helyett **OnDictionaryChangedHandler**.

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
> A fenti kóddal, a Újraépítés értesítés feldolgozása során az első karbantartott összesített állapota nincs bejelölve. Mivel a megbízható gyűjteményben újraépíti az új állapot, az összes korábbi értesítés küldése nem releváns, mivel.
> 
> 

A **DictionaryChanged** eseménykezelő használ **NotifyDictionaryChangedEventArgs** adja meg az eseménnyel kapcsolatos adatait.
**NotifyDictionaryChangedEventArgs** öt alosztályok rendelkezik. A művelet tulajdonsággal **NotifyDictionaryChangedEventArgs** típussá **NotifyDictionaryChangedEventArgs** , a megfelelő alosztályát:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** és **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
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
* *Tegye* értesítési események lehető legnagyobb befejezéséhez.
* *Ne* költséges műveleteket (például i/o-műveletek) végrehajtása szinkron események részeként.
* *Tegye* a művelettípus ellenőrzése előtt az eseményt dolgoz fel. Új művelettípusok később hozzáadhatók.

Az alábbiakban néhány dolgot figyelembe kell venni:

* Értesítések rendszer aktivált egy művelet végrehajtásának részeként. Például egy visszaállítási értesítést a visszaállítási művelet utolsó lépéseként lép működésbe. A visszaállítás nem fejeződik be, amíg az értesítési esemény feldolgozása.
* Értesítések elindulása részeként az alkalmazása műveletek esetén, mert az ügyfelek csak helyileg véglegesített műveletek értesítések:. És mivel a műveletek garantáltan csak helyileg véglegesített (más szóval naplózása), előfordulhat, hogy vagy a nem lehet visszavonni a jövőben.
* A visszaállítási elérési úton egyetlen értesítés minden alkalmazott művelet lesz elindítva. Ez azt jelenti, hogy ha tranzakció T1 Create(X) Delete(X) és Create(X) tartalmaz, egy értesítést kap egy, a Törlés és egy, a létrehozásához, az x létrehozásához ebben a sorrendben.
* Több műveletet tartalmazó tranzakciókhoz műveletek alkalmazza a rendszer a, amelyben érkezési sorrendben a felhasználó az elsődleges replikán.
* Feldolgozási hamis folyamat részeként néhány művelet előfordulhat, hogy vonható vissza. Értesítések a ilyen visszavonási műveletek esetében a replika állapota stabil pontra visszagörgetése aktiválódnak. A visszavonás értesítések egy fontos különbség, hogy eseményeket, amelyek ismétlődő kulcsok vannak összesítve. Például ha tranzakció T1 visszavonja, látni fogja a Delete(X) egyetlen értesítést is.

## <a name="next-steps"></a>További lépések
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [A Reliable Services – gyorsútmutató](service-fabric-reliable-services-quick-start.md)
* [Reliable Services biztonsági mentési és visszaállítási (katasztrófa utáni helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [A Reliable Collections – fejlesztői referencia](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

