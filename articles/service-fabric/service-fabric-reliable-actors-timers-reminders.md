---
title: A Reliable actors – időzítők és emlékeztetők |} A Microsoft Docs
description: A Service Fabric Reliable actors – időzítők és emlékeztetők bemutatása.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 323de842645cced3c6f490e98112fcbcd184aa64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667429"
---
# <a name="actor-timers-and-reminders"></a>Actors – időzítők és emlékeztetők
Actors ütemezheti rendszeres munkát maguk időzítők vagy emlékeztetők regisztrálásával. Ez a cikk bemutatja, hogyan használhatja az időzítők és emlékeztetők, és ismerteti a különbségeket.

## <a name="actor-timers"></a>Aktor időzítők
Aktor időzítők adja meg annak érdekében, hogy a visszahívás módszerek tiszteletben a fordulókra egyidejűségi .NET vagy Java időzítő egyszerű burkolója garantálja, hogy az Aktorok modul biztosítja.

Aktorok használhatja a `RegisterTimer`(C#) vagy `registerTimer`(Java) és `UnregisterTimer`(C#) vagy `unregisterTimer`regisztrálásához és azok időzítők az alaposztálya (Java) metody. Az alábbi példában látható időzítő API-k használatát. Az API-k nagyon hasonló a .NET-időzítő vagy a Java időzítő. Ebben a példában, amikor az időzítő esedékes, az Actors modul hívni fogja a `MoveObject`(C#) vagy `moveObject`(Java) metódust. A metódus figyelembe veszi a fordulókra egyidejűségi garantált. Ez azt jelenti, hogy más aktor módszerek vagy időzítő/emlékeztető visszahívások nem lesz a folyamatban lévő mindaddig, amíg a visszahívás végrehajtása befejeződött.

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

A következő időszak az időzítő elindítja a visszahívás végrehajtása után. Ez azt jelenti, hogy az időzítő leáll, amíg a visszahívás végrehajtása, és a visszahívás befejezése elindult.

Az Aktorok modul a visszahívás befejezése az aktor State Manager végrehajtott módosítások mentése. Hiba esetén az állapotmentést a az aktor objektum inaktiválva lesz, és a egy új példányt aktiválódik.

Összes időzítő le lesz állítva, ha az aktor inaktiváltuk szemétgyűjtés részeként. Nincs időzítő visszahívások, amely után kerül meghívásra. Az Aktorok modul is, nem őriz meg az inaktiválást előtt futó időzítő semmilyen információt. Bármely időzítők újraaktiválásakor, a jövőben működéséhez szükséges regisztrálni az aktor esetén. További információkért lásd a szakasz [aktor szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Aktor emlékeztetők
Emlékeztetők egy mechanizmust, amely állandó visszahívások egy szereplő a-trigger megadott időpontok. Időzítők funkcióikkal hasonlít. De időzítők, eltérően emlékeztetők aktivált minden körülmények mindaddig, amíg az aktor explicit módon megszünteti őket, vagy az aktor explicit módon törlődött. Pontosabban emlékeztetők aktivált aktor deactivations és feladatátvételi teszteket, mert az Actors modul továbbra is fennáll, az aktor emlékeztetők aktor riasztásiállapot-szolgáltató használatával kapcsolatos információkat. Vegye figyelembe, hogy az emlékeztetők megbízhatóságát az aktor állapotszolgáltató által nyújtott garanciák állapot megbízhatóság van kötve. Ez azt jelenti, hogy az aktorok, amelynek állapot megőrzését None értékre van állítva, az emlékeztetők nem indulnak el feladatátvétel után. 

Emlékeztető regisztrálásához egy szereplő meghívja a `RegisterReminderAsync` metódus az alaposztály alaposztályát, a megadott, az alábbi példában látható módon:

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

Ebben a példában `"Pay cell phone bill"` emlékeztető neve. Ez a karakterlánc, amely az aktor emlékeztető egyedi azonosítására használ. `BitConverter.GetBytes(amountInDollars)`(C#) a környezet, amely az emlékeztető társítva van. Azt lesznek átadva vissza az aktor jako argument Pro az emlékeztető visszahívás, azaz `IRemindable.ReceiveReminderAsync`(C#) vagy `Remindable.receiveReminderAsync`(Java).

Emlékeztetők használó actors meg kell valósítani a `IRemindable` felület, az alábbi példában látható módon.

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

Emlékeztető aktiválásakor meghívja a Reliable Actors-futtatókörnyezet a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) metódust. Egy szereplő regisztrálhat több emlékeztetők, és a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) metódus meghívásakor kerül ezen emlékeztetők bármelyikét esetén. Az aktor válassza az emlékeztető, amely kerül átadásra a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) módszer döntse el, melyik emlékeztető lett elindítva.

A modul menti az aktor szereplők állapotba, ha a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) hívás befejezését. Hiba esetén az állapotmentést a az aktor objektum inaktiválva lesz, és a egy új példányt aktiválódik.

Emlékeztető regisztrációjának törléséhez egy szereplő meghívja a `UnregisterReminderAsync`(C#) vagy `unregisterReminderAsync`(Java) módszerhez, ahogy az alábbi példákban szemléltetett módon.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

A fent látható módon a `UnregisterReminderAsync`(C#) vagy `unregisterReminderAsync`(Java) metódus fogad el paraméterként egy `IActorReminder`(C#) vagy `ActorReminder`(Java) felületet. Az aktor alaposztály támogatja egy `GetReminder`(C#) vagy `getReminder`(Java) módszernek is lehet lekérni a `IActorReminder`(C#) vagy `ActorReminder`(Java) felületen az emlékeztető név megadásával. Ez akkor hasznos, mivel az aktor nem kell megőrizni a `IActorReminder`(C#) vagy `ActorReminder`által eredményül adott (Java) felületet a `RegisterReminder`(C#) vagy `registerReminder`(Java) metódus meghívása.

## <a name="next-steps"></a>További lépések
Tudnivalók a Reliable Actors-események és – újbóli belépés:
* [Actors-események](service-fabric-reliable-actors-events.md)
* [Actors – újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
