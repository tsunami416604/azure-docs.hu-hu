---
title: "Aktor megbízható szereplője kiegészítő írja be a szerializálási |} Microsoft Docs"
description: "Ismerteti, amelyek alapvető követelményeivel szerializálható olyan osztállyal, amelynek segítségével határozza meg a Service Fabric Reliable Actors állapotait és az illesztők meghatározása"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: b1b7c8af6d852599359baab2188bfc3395d33a28
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Tudnivalók a Service Fabric Reliable Actors írja be a szerializálási
Összes metódus argumentumaként, egy szereplő felület minden metódusa által visszaadott eredmény típusú a feladatokat, és egy szereplő állapotkezelője tárolt objektumok kell [adategyezmény-szerializálható](https://msdn.microsoft.com/library/ms731923.aspx). Ez vonatkozik az argumentumok a meghatározott módszerek [szereplő eseményfelület](service-fabric-reliable-actors-events.md). (Szereplő esemény a felület metódusai mindig visszaadniuk.)

## <a name="custom-data-types"></a>Egyéni adattípusok
Ebben a példában a következő szereplő kapcsolat határozza meg, amely visszaadja az egy egyéni adattípus nevű metódus `VoicemailBox`:

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

A felület megvalósítása egy szereplő használ az állapotkezelő által a `VoicemailBox` objektum:

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

* Az objektum szereplő példány és egy hívó között továbbított.
* Az objektum az állapotkezelő, amennyiben megőrizni a lemezen és egyéb csomópontjához replikált kerül.

A megbízható szereplő keretrendszer DataContract szerializálása használja. Ezért az egyéni objektumok és azok tagjait kell feliratozni a a **DataContract** és **DataMember** attribútumok, illetve.

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


## <a name="next-steps"></a>Következő lépések
* [Aktor életciklusának és szemétgyűjtési gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [Aktor időzítők és az emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Szereplő események](service-fabric-reliable-actors-events.md)
* [Aktor rögzítve](service-fabric-reliable-actors-reentrancy.md)
* [Aktor polimorfizmus és objektumorientált tervezési minták](service-fabric-reliable-actors-polymorphism.md)
* [Aktor diagnosztika és teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
