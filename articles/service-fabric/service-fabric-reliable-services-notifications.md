---
title: "Megbízható szolgáltatások értesítések |} Microsoft Docs"
description: "Service Fabric Reliable Services értesítések fogalmi dokumentációja"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: c080ee1f67b558988e35613f7357137c89c872fc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="reliable-services-notifications"></a>Megbízható szolgáltatások értesítések
Értesítések engedélyezése az ügyfelek nyomon követni a kíváncsiak vagyunk objektum végrehajtott módosításokat. Kétféle típusú objektumok támogatja értesítéseket: *megbízható állapotkezelője* és *megbízható szótár*.

Értesítések használata gyakori okai a következők:

* Épület materializált nézetek, például a másodlagos indexek vagy szűrt nézeteinek a másodpéldány állapotát összesíti. Példa: egy rendezett index összes kulcsok megbízható szótárban.
* Küldő figyelési adatok, például az elmúlt órában hozzáadott felhasználók száma.

Értesítések elindulása részeként műveleteket alkalmazása esetén. Miatt, amely értesítések gyors lehetséges és szinkron események nem tartalmazhatja a költséges műveleteket kell kezelni.

## <a name="reliable-state-manager-notifications"></a>Megbízható állapot Manager értesítéseire
Megbízható állapotkezelő a következő események értesítéseket biztosít:

* Tranzakció
  * Véglegesítés
* Állapot manager
  * Újraépítés
  * A megbízható állapot hozzáadása
  * A megbízható állapot törlése

Megbízható állapotkezelője követi nyomon, hogy az aktuális aktív tranzakciók. Mindössze annyi a változás aktiválódik értesítést okozó tranzakció állapotban egy tranzakció véglegesítés alatt áll.

Megbízható állapotkezelője tart fenn például megbízható szótár és megbízható várólista megbízható állapotok gyűjteménye. Megbízható állapotkezelője értesítések következik be, amikor megváltozik a gyűjtemény: olyan megbízható állapotban van hozzáadásakor vagy eltávolításakor, vagy a teljes gyűjteményt újraépítésekor.
A megbízható állapotkezelője gyűjtemény újraépítésekor három esetben:

* Helyreállítás: Replika indításakor azt anélkül állíthatóak vissza korábbi állapotba a lemezről. A helyreállítás végén, használja **NotifyStateManagerChangedEventArgs** az érvényesítést a helyreállított megbízható állapotok készletét tartalmazó esemény.
* Másolás teljes: replika csatlakozhat a konfigurációs készlet, mielőtt rendelkezik kialakítani. Egyes esetekben ehhez az elsődleges replikából a tétlen másodlagos replikán alkalmazni kívánt megbízható állapotkezelője állapot teljes másolata. Használja a másodlagos replikán megbízható állapotkezelője **NotifyStateManagerChangedEventArgs** az érvényesítést egy eseményt, amely azokat a megbízható állapotok azért szerzett, az elsődleges replikából.
* Visszaállítás: A vész-helyreállítási helyzetekben a replika visszaállítható egy biztonsági másolatból keresztül **RestoreAsync**. Ilyen esetben az elsődleges replikán megbízható állapotkezelője használ **NotifyStateManagerChangedEventArgs** az érvényesítést, amely azokat a megbízható állapotok azt a biztonsági másolatból visszaállított esemény.

Tranzakció értesítések és/vagy az állapot manager értesítéseinek regisztrálásához regisztrálni kell a **TransactionChanged** vagy **StateManagerChanged** megbízható állapotkezelője eseményeit. A közös ezek eseménykezelők regisztrálására helye az állapotalapú szolgáltatás konstruktor. Ha regisztrálja a konstruktora, teljesíti-e nem élettartama során változás által okozott értesítésekhez **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

A **TransactionChanged** eseménykezelő használ **NotifyTransactionChangedEventArgs** arra, hogy az esemény részleteit. A művelet tulajdonságot tartalmaz (például **NotifyTransactionChangedAction.Commit**), amely megadja, hogy olyan változást. A transaction tulajdonság, amely módosult a tranzakció egy hivatkozást is tartalmaz.

> [!NOTE]
> Napjainkban **TransactionChanged** események aktiválódnak csak akkor, ha a tranzakció. A művelet megegyezik **NotifyTransactionChangedAction.Commit**. De a jövőben események előfordulhat, hogy generál tranzakciót állapotváltozások más típusú. Javasoljuk, hogy a művelet ellenőrzése és az esemény feldolgozása, csak akkor, ha egy, a várt.
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

A **StateManagerChanged** eseménykezelő használ **NotifyStateManagerChangedEventArgs** arra, hogy az esemény részleteit.
**NotifyStateManagerChangedEventArgs** két alosztályok rendelkezik: **NotifyStateManagerRebuildEventArgs** és **NotifyStateManagerSingleEntityChangedEventArgs**.
A művelet tulajdonsággal a **NotifyStateManagerChangedEventArgs** konvertálni **NotifyStateManagerChangedEventArgs** a helytelen alosztályának létrehozására:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** és **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Példa **StateManagerChanged** értesítéskezelő.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Megbízható szótár értesítések
Megbízható szótár a következő események értesítéseket biztosít:

* Újraépítés: Meghívva **ReliableDictionary** helyreállt állapotában a helyreállított vagy másolt helyi állapot vagy a biztonsági mentés.
* Törlés: Meghívva állapotának **ReliableDictionary** keresztül törölve lett a **ClearAsync** metódust.
* Adja hozzá: Hívható meg, ha egy elem hozzáadva **ReliableDictionary**.
* Frissítés: Hívható meg, ha az elem **IReliableDictionary** frissítve lett.
* Eltávolítás: Hívható meg, ha az elem **IReliableDictionary** törölve lett.

Ahhoz, hogy megbízható szótár értesítéseket, és regisztrálnia kell a **DictionaryChanged** eseménykezelő a **IReliableDictionary**. Van olyan közös helyen ezek eseménykezelők regisztrálni a **ReliableStateManager.StateManagerChanged** értesítési hozzáadása.
Ha regisztrálja **IReliableDictionary** hozzáadódik **IReliableStateManager** biztosítja, hogy nem hagyott ki belőle értesítéseket.

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
> **ProcessStateManagerSingleEntityNotification** a minta módszer, amely az előző **OnStateManagerChangedHandler** példa hívások.
> 
> 

Az előző kód beállítása a **IReliableNotificationAsyncCallback** kommunikáljanak, jelszavat **DictionaryChanged**. Mivel **NotifyDictionaryRebuildEventArgs** tartalmaz egy **IAsyncEnumerable** illesztő – amely aszinkron módon--számba kell belüli értesítések elindulása esetén keresztül **RebuildNotificationAsyncCallback** helyett **OnDictionaryChangedHandler**.

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
> Az előzőekben látható kód rebuild értesítés feldolgozása részeként az először a karbantartott összesített állapota nincs bejelölve. A megbízható gyűjtemény újraépíti az új állapot, mert az összes korábbi értesítés irrelevánsak.
> 
> 

A **DictionaryChanged** eseménykezelő használ **NotifyDictionaryChangedEventArgs** arra, hogy az esemény részleteit.
**NotifyDictionaryChangedEventArgs** öt alosztályok rendelkezik. A művelet tulajdonsággal **NotifyDictionaryChangedEventArgs** konvertálni **NotifyDictionaryChangedEventArgs** a helytelen alosztályának létrehozására:

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
* *Ne* hajtható végre a költséges műveleteket (például i/o-műveletek) szinkron események részeként.
* *Tegye* a művelet típusának ellenőrzése előtt feldolgozni a eseményt. Új művelettípusok előfordulhat, hogy a jövőben hozzáadni.

Az alábbiakban néhány dolgot figyelembe venni:

* Értesítések elindulása esetén egy művelet végrehajtásának részeként. Például egy visszaállítási értesítést a visszaállítási művelet utolsó lépéseként lép működésbe. A visszaállítás nem fejeződik be, amíg a értesítő eseményt dolgoz fel.
* Értesítések a alkalmazása műveletek részeként elindulása esetén, mert az ügyfelek csak a helyileg véglegesített műveletek értesítések:. Ezért műveletek garantáltan csak helyi véglegesített (más szóval naplózása), előfordulhat, hogy, vagy előfordulhat, hogy nem lehet visszavonni a jövőben.
* A visszaállítási útvonalon egyetlen értesítést minden alkalmazott művelethez lép működésbe. Ez azt jelenti, hogy ha tranzakció T1 Create(X) Delete(X) és Create(X) tartalmaz, kap egy értesítési X, egy, a törlésre, és egy újbóli létrehozásához létrehozásához abban a sorrendben.
* Az egyes tranzakciókra vonatkozóan, több műveletet tartalmazó műveletek lesznek alkalmazva a sorrendet, amelyben azok érkezett a felhasználótól az elsődleges replikán.
* Feldolgozási hamis folyamat részeként néhány művelet lehet, hogy vonható vissza. Értesítések a visszavonási műveletek, a replika állapotának visszaállítása egy stabil ponton aktiválódnak. Egy fontos visszavonási értesítések különbség, hogy eseményeket, amelyek ismétlődő kulcsok összesítése. Például ha tranzakció T1 visszavonja, Delete(X) egyetlen értesítést láthatja.

## <a name="next-steps"></a>További lépések
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
* [Megbízható szolgáltatások biztonsági mentése és visszaállítása (katasztrófa utáni helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Fejlesztői leírás megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

