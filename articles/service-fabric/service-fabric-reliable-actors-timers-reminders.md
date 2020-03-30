---
title: Megbízható actors időzítők és emlékeztetők
description: Bevezetés az időzítők és emlékeztetők a Service Fabric megbízható szereplők, beleértve az egyes használati útmutatót.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639615"
---
# <a name="actor-timers-and-reminders"></a>Aktor időzítők és emlékeztetők
A szereplők ütemezhetik maguknak az időszakos munkát időzítők vagy emlékeztetők regisztrálásával. Ez a cikk bemutatja, hogyan kell használni az időzítők és emlékeztetők, és ismerteti a különbségeket közöttük.

## <a name="actor-timers"></a>Aktor időzítői
Az aktoridőzítők egy egyszerű burkolót biztosítanak egy .NET vagy Java időzítő körül, hogy a visszahívási módszerek tiszteletben tartsák a sorozatalapú egyidejűségi garanciákat, amelyeket az Actors futásidejű biztosít.

A szereplők `RegisterTimer`használhatják a `registerTimer`(C#) `UnregisterTimer`vagy (Java) és (C#) vagy `unregisterTimer`(Java) metódusokat az alaposztályukon az időzítők regisztrálásához és regisztrációjának leiratkozásához. Az alábbi példa az időzítő API-k használatát mutatja be. Az API-k nagyon hasonlóak a .NET időzítőhöz vagy a Java időzítőhöz. Ebben a példában, amikor az időzítő esedékes, `MoveObject`az Actors futásidejű meghívja a (C#) vagy `moveObject`(Java) metódust. A módszer garantáltan tiszteletben tartja a turn-based egyidejűséget. Ez azt jelenti, hogy nincs más aktor metódusok vagy időzítő/emlékeztető visszahívások lesz folyamatban, amíg ez a visszahívás befejezi a végrehajtást.

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

Az időzítő következő időszaka a visszahívás végrehajtása után kezdődik. Ez azt jelenti, hogy az időzítő leáll a visszahívás végrehajtása közben, és a visszahívás befejezésekor indul el.

Az Actors futásidejű menti a módosításokat az aktor state manager, amikor a visszahívás befejeződik. Ha hiba történik az állapot mentésekor, az aktorobjektum inaktívvá válik, és egy új példány aktiválódik.

Az összes időzítő leáll, ha az aktor a szemétgyűjtés részeként inaktiválva van. Ezután nem hívnak vissza időzítővisszahívásokat. Emellett az Actors futtatótime nem őriz meg semmilyen információt az inaktiválás előtt futó időzítőkről. Az aktor nak kell regisztrálnia az időzítőket, amelyekre szüksége van, amikor a jövőben újraaktiválják. További információt az [aktorszemétgyűjtésről](service-fabric-reliable-actors-lifecycle.md)szóló részben talál.

## <a name="actor-reminders"></a>Aktor emlékeztetők
Az emlékeztetők egy olyan mechanizmust, amely elindítja az állandó visszahívások egy aktor megadott időpontokban. Működésük hasonló az időzítőkhöz. De az időzítőkkel ellentétben az emlékeztetők minden körülmények között aktiválódnak, amíg az aktor kifejezetten törli őket, vagy az aktor explicit módon törlődik. Pontosabban emlékeztetők aktiválódnak az aktor inaktiválásaés a feladatátvételek, mert az Actors futásidejű megőrzi az aktor emlékeztetők az aktor állapotszolgáltató használatával. Kérjük, vegye figyelembe, hogy az emlékeztetők megbízhatósága az aktor állami szolgáltató által nyújtott megbízhatósági garanciákhoz kötődik. Ez azt jelenti, hogy azok a szereplők, akiknek az állapotmegőrzése nincs, az emlékeztetők nem indulnak el a feladatátvétel után. 

Emlékeztető regisztrálásához az aktor meghívja az `RegisterReminderAsync` alaposztályban megadott metódust, ahogy az a következő példában látható:

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

Ebben a `"Pay cell phone bill"` példában az emlékeztető neve. Ez egy olyan karakterlánc, amelyet az aktor az emlékeztető egyedi azonosítására használ. `BitConverter.GetBytes(amountInDollars)`(C#) a felszólításhoz társított környezet. Az emlékeztető visszahívása, azaz `IRemindable.ReceiveReminderAsync`(C#) vagy `Remindable.receiveReminderAsync`(Java) argumentumként visszakerül az aktornak.

Az emlékeztetőket használó szereplőknek végre kell hajtaniuk a `IRemindable` felületet, amint az az alábbi példában látható.

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

Emlékeztető aktiválásakor a Reliable Actors futásidejű `ReceiveReminderAsync`meghívja a `receiveReminderAsync`(C#) vagy (Java) metódust az aktoron. Egy aktor több emlékeztetőt is regisztrálhat, és a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`a (Java) metódus meghívása, ha ezek közül bármelyik emlékeztetők aktiválódik. Az aktor használhatja az emlékeztető `ReceiveReminderAsync`nevét, amely `receiveReminderAsync`átlett a (C#) vagy (Java) metódus, hogy kitaláljuk, melyik emlékeztető aktiválódott.

Az Actors futásidejű menti az aktor állapotát, amikor a `ReceiveReminderAsync`(C#) vagy `receiveReminderAsync`(Java) hívás befejeződik. Ha hiba történik az állapot mentésekor, az aktorobjektum inaktívvá válik, és egy új példány aktiválódik.

Emlékeztető regisztrációjának megszüntetése érdekében `UnregisterReminderAsync`az aktor meghívja a (C#) vagy `unregisterReminderAsync`a (Java) metódust, ahogy az az alábbi példákban látható.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Amint fent `UnregisterReminderAsync`látható, a `unregisterReminderAsync`(C#) vagy `IActorReminder`(Java) metódus `ActorReminder`(C#) vagy (Java) felületet fogad el. Az aktor `GetReminder`alaposztály a `getReminder`(C#) vagy a (Java) metódust `ActorReminder`támogatja, amely a (C#) vagy (Java) `IActorReminder`felület lekéréséhez használható az emlékeztető nevének átadásával. Ez azért kényelmes, mert az aktornak nem kell megpersistnia `IActorReminder`a (C#) vagy `ActorReminder`(Java) felületet, amely a `RegisterReminder`(C#) vagy `registerReminder`a (Java) metódushívásból lett visszaadva.

## <a name="next-steps"></a>Következő lépések
Tudjon meg többet a Megbízható színész eseményekről és a reentrancy-ről:
* [Színész események](service-fabric-reliable-actors-events.md)
* [Színész reentrancy](service-fabric-reliable-actors-reentrancy.md)
