---
title: A Reliable Actors megjegyzések az aktortípusok szerializálásával |} A Microsoft Docs
description: Ismerteti az alapvető követelményeknek, amelyek segítségével Service Fabric Reliable Actors-állapotok és illesztők szerializálható osztályok definiálása
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667507"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>A Service Fabric Reliable actors – megjegyzések szerializálásával kapcsolatban
Az összes módszer argumentumok, az aktor illesztőjét az egyes módszerek által visszaadott eredmény típusú a feladatokat, és a egy szereplő állapotkezelője tárolt objektumok kell [adatokat szerződéses szerializálható](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Ez vonatkozik az argumentumok a meghatározott módszerek [aktor eseményfelület](service-fabric-reliable-actors-events.md). (Az aktorok esemény illesztőmetódusait mindig vrácené hodnoty void.)

## <a name="custom-data-types"></a>Egyéni adatok típusa
Ebben a példában a következő aktorillesztő határozza meg egy egyéni adattípus nevű visszaadó metódus `VoicemailBox`:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

A felület megvalósítása vystavené objektem actor, amelyet a állapotkezelője használ egy `VoicemailBox` objektum:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

Ebben a példában a `VoicemailBox` szerializált objektum során:

* Az objektum egy actor-példány és a egy hívó között továbbított adatok köre.
* Az objektum az állapot Manager amennyiben megőrzi a lemezen és replikálja a többi csomópont lesz mentve.

A Reliable Actors keretrendszerben DataContract szerializálási használ. Ezért az egyéni objektumok és azok tagjait fel kell vezetni a **DataContract** és **DataMember** attribútumok jelölik.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>További lépések
* [Actors-életciklus-kezelés és szemétgyűjtés gyűjtemény](service-fabric-reliable-actors-lifecycle.md)
* [Actors – időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Actors-események](service-fabric-reliable-actors-events.md)
* [Actors – újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Aktor polimorfizmus és objektumorientált tervezési minták](service-fabric-reliable-actors-polymorphism.md)
* [Actors diagnosztizálása és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
