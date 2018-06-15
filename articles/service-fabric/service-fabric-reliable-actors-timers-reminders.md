---
title: Megbízható szereplője időzítők és az emlékeztetők |} Microsoft Docs
description: Bevezetés a Service Fabric Reliable Actors időzítők és az emlékeztetők.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e43aec6630a4a688ffd6c52a5e5bd711243fa662
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206791"
---
# <a name="actor-timers-and-reminders"></a>Aktor időzítők és az emlékeztetők
Szereplője időzítők vagy emlékeztetők regisztrálásával ütemezhet rendszeres munkát saját magukat. Ez a cikk bemutatja, hogyan használja a időzítők és az emlékeztetők, és a különbségeket ismerteti.

## <a name="actor-timers"></a>Aktor időzítők
Aktor időzítők adja meg annak érdekében, hogy a visszahívási módszerek tiszteletben tartják a kapcsolja-alapú feldolgozási .NET vagy Java időzítő egyszerű csomagolásának garantálja, hogy a szereplője futtatókörnyezetet biztosít.

Szereplője használhatja a `RegisterTimer`(C#) vagy `registerTimer`(Java) és `UnregisterTimer`(C#) vagy `unregisterTimer`(Java) módszerek regisztrálásához és azok időzítők az alaposztályban. Az alábbi példában látható időzítő API-k használatát. Az API-k nagyon hasonlóak a .NET időzítés vagy Java időzítőt. Ebben a példában, amikor az időzítő esedékes, a gyakrabban futásidejű fel fogja hívni a `MoveObject`(C#) vagy `moveObject`(Java) metódust. A metódus garantáltan tiszteletben tartják a kapcsolja-alapú feldolgozási. Ez azt jelenti, hogy más szereplő módszerek vagy időzítő/emlékeztető visszahívások nem lesz folyamatban van a visszahívás végrehajtása befejezéséig.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

A következő időszak az időzítő elindítja a visszahívás végrehajtása után. Ez azt jelenti, hogy az időzítő leáll, miközben a visszahívás végrehajtása történik, és a visszahívás befejezése után elindult.

A szereplője futásidejű a visszahívás befejezése után a szereplő állapotkezelője végrehajtott módosítások mentése. Ha hiba lép fel az állapotmentést, inaktiválja a szereplő objektum, és egy új példány aktív lesz.

Összes időzítő le van állítva, ha a szereplő inaktív szemétgyűjtés részeként. Nincs időzítő visszahívások ezt követően hívják. A szereplője futásidejű is, nem őriz meg az időzítők inaktiválása előtt futó semmilyen információt. Esetén a szereplő bármely időzítők újraaktiválásakor azt a jövőben szükséges regisztrálni. További információkért lásd [szereplő szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Aktor emlékeztetők
Emlékeztetők egy olyan mechanizmus való állandó visszahívások egy szereplő a következő alkalommal megadva. Működés időzítők hasonlít. De időzítők, eltérően emlékeztetők által kiváltott minden körülmények addig, amíg a szereplő explicit módon regisztrációjának őket, vagy a szereplő explicit módon törlődik. Pontosabban emlékeztetők által kiváltott szereplő deactivations és a feladatátvétel, mert a szereplője futásidejű továbbra is fennáll, a szereplő emlékeztetők szereplő állapotszolgáltató használatával kapcsolatos információkat. Vegye figyelembe, hogy emlékeztetők megbízhatóságát az aktor állapotszolgáltató által nyújtott állapot megbízhatóság garanciák van kötve. Ez azt jelenti, hogy a szereplőket, amelynek állapot megőrzését értékre van beállítva, a emlékeztetők nem indul el egy feladatátvétel után. 

Emlékeztető regisztrálásához egy szereplő meghívja a `RegisterReminderAsync` metódus az alaposztályban, megadva a következő példában látható módon:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

Ebben a példában `"Pay cell phone bill"` emlékeztető neve. Ez a karakterlánc, amely a szereplő használatával egyedi módon azonosítja az egy emlékeztető az. `BitConverter.GetBytes(amountInDollars)`(C#) a környezetben a felszólítás társított. Az átkerülnek vissza a szereplő emlékeztető visszahívási argumentumaként azaz `IRemindable.ReceiveReminderAsync`(C#) vagy `Remindable.receiveReminderAsync`(Java).

Buborékemlékeztetők használó szereplője meg kell valósítania a `IRemindable` csatoló, az alábbi példában látható módon.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Emlékeztető kiváltásakor a Reliable Actors futtatókörnyezet által aktivált a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`a szereplő (Java) metódust. Egy szereplő regisztrálhat több emlékeztetők, és a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) metódus meghívja Ha bármelyik azok megjelenését elindul. Az aktor is használja a a felszólítás kerül átadásra a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) módszer mérje fel, melyik emlékeztető lett elindítva.

A futásidejű menti a szereplő szereplője állapotba, ha a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) hívás befejezését. Ha hiba lép fel az állapotmentést, inaktiválja a szereplő objektum, és egy új példány aktív lesz.

Emlékeztető regisztrációját, egy szereplő meghívja a `UnregisterReminderAsync`(C#) vagy `unregisterReminderAsync`(Java) metódust, az alábbi példában látható módon.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

A fent látható módon a `UnregisterReminderAsync`(C#) vagy `unregisterReminderAsync`(Java) metódus fogad el egy `IActorReminder`(C#) vagy `ActorReminder`(Java) felületet. Az aktor alaposztály támogatja a `GetReminder`(C#) vagy `getReminder`beolvasásához használható metódus (Java) a `IActorReminder`(C#) vagy `ActorReminder`emlékeztető nevében történő (Java) felületet. Ez akkor hasznos, mivel a szereplő nem kell megőrizni a `IActorReminder`(C#) vagy `ActorReminder`által eredményül adott (Java) felületet a `RegisterReminder`(C#) vagy `registerReminder`(Java) metódus hívása.

## <a name="next-steps"></a>További lépések
További tudnivalók a megbízható szereplő események és rögzítve:
* [Szereplő események](service-fabric-reliable-actors-events.md)
* [Aktor rögzítve](service-fabric-reliable-actors-reentrancy.md)
