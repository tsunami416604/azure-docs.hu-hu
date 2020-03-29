---
title: Megbízható szereplők megjegyzi, aktor típusú szerializálás
description: A szolgáltatásfabric megbízható szereplői állapotok és felületek definiálására használható szerializálható osztályok meghatározásának alapvető követelményeit ismerteti.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349308"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Megjegyzések a Service Fabric megbízható szereplők típusú szerializálás
Az összes metódus argumentumainak, az egyes metódusok által az aktor felületen visszaadott feladatok eredménytípusainak, valamint az aktor állapotkezelőjében tárolt objektumoknak [az adatszerződés szerializálhatónak](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)kell lennie. Ez vonatkozik az [aktor eseményinterfészekben](service-fabric-reliable-actors-events.md)meghatározott módszerek argumentumaira is . (Az aktor eseményfelület-metódusai mindig érvénytelenítik a pontokat.)

## <a name="custom-data-types"></a>Egyéni adattípusok
Ebben a példában a következő aktor felület egy `VoicemailBox`olyan metódust határoz meg, amely egy egyéni adattípust ad vissza:

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

A felületet egy olyan szereplő valósítja meg, amely az állapotkezelőt használja egy `VoicemailBox` objektum tárolására:

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

Ebben a példában az `VoicemailBox` objektum szerializálódik, ha:

* Az objektum átvitele egy aktorpéldány és egy hívó között történik.
* Az objektum az állapotkezelőbe kerül, ahol lemezre kerül, és más csomópontokra replikálódik.

A Reliable Actor keretrendszer datacontract szerializálást használ. Ezért az egyéni adatobjektumokat és azok tagjait a **DataContract** és a **DataMember** attribútumokkal kell eljegyzetelni.

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
* [A szereplő életciklusa és a szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
* [Aktor időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Színész események](service-fabric-reliable-actors-events.md)
* [Színész reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Színész polimorfizmus és objektum-orientált tervezési minták](service-fabric-reliable-actors-polymorphism.md)
* [Aktor diagnosztikája és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
