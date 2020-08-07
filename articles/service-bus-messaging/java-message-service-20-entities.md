---
title: Azure Service Bus üzenetkezelés – Java Message Service-entitások (előzetes verzió)
description: Ez a cikk áttekintést nyújt a Java Message Service API-n keresztül elérhető Azure Service Bus üzenetkezelési entitásokról.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801581"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Java Message Service (JMS) 2,0 entitások (előzetes verzió)

A Azure Service Bus Premiumhoz csatlakozó ügyfélalkalmazások és a [Azure Service Bus JMS-kódtár](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) használata az alábbi entitásokat használhatja.

## <a name="queues"></a>Üzenetsorok

A JMS lévő várólisták szemantikailag összehasonlíthatóak a hagyományos [Service Bus várólistákkal](service-bus-queues-topics-subscriptions.md#queues).

Üzenetsor létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Témakörök

A JMS-ban található témakörök szemantikailag összehasonlíthatóak a hagyományos [Service Bus témakörökkel](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Témakör létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Ideiglenes várólisták

Ha egy ügyfélalkalmazás olyan ideiglenes entitást igényel, amely már létezik az alkalmazás élettartamához, használhat ideiglenes várólistákat. Ezeket a [kérelem-válasz](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) mintában használják.

Ideiglenes várólista létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Ideiglenes témakörök

Az ideiglenes várólistákhoz hasonlóan ideiglenes témakörök is léteznek, amelyek lehetővé teszik a közzétételt és az előfizetést egy olyan ideiglenes entitáson keresztül, amely már létezik az alkalmazás élettartamára.

Egy ideiglenes témakör létrehozásához használja az alábbi metódusokat a `JMSContext` osztályban:

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java Message Service-(JMS-) előfizetések

Habár ezek szemantikai hasonlóságot mutatnak az [előfizetésekhez](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (például a témakörben, és lehetővé teszik a közzétételi/előfizetési szemantikai), a Java Message Service spec bevezeti az adott előfizetés **megosztott**, nem **megosztott**, **tartós** és **nem tartós** attribútumainak fogalmait.

> [!NOTE]
> Az alábbi előfizetések Azure Service Bus prémium szinten érhetők el, ha a [Azure Service Bus JMS könyvtár](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)használatával a Azure Service Bushoz csatlakozó ügyfélalkalmazások számára előzetes verzió érhető el.
>
> A nyilvános előzetes verzió esetében ezek az előfizetések nem hozhatók létre a Azure Portal használatával.
>

### <a name="shared-durable-subscriptions"></a>Közös tartós előfizetések

A rendszer közös tartós előfizetést használ, ha egy adott témakörben közzétett összes üzenetet egy alkalmazásnak kell megkapnia és feldolgoznia, függetlenül attól, hogy az alkalmazás az előfizetésből aktívan használja-e az alkalmazást.

Mivel ez egy megosztott előfizetés, az Service Bustól fogadott összes alkalmazás az előfizetésből is fogadhat.

Megosztott tartós előfizetés létrehozásához használja az alábbi metódusokat a osztályban: `JMSContext`

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

A megosztott tartós előfizetés továbbra is fennáll, hacsak nem törli a `unsubscribe` metódust az `JMSContext` osztályban.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Nem megosztott tartós előfizetések

A közös tartós előfizetéshez hasonlóan a rendszer nem megosztott tartós előfizetést használ, ha egy adott témakörben közzétett összes üzenetet fogadni és feldolgozni kívánja egy alkalmazás, függetlenül attól, hogy az alkalmazás az előfizetésből aktívan használja-e az előfizetést.

Mivel azonban ez egy nem megosztott előfizetés, csak az előfizetést létrehozó alkalmazás tud fogadni.

Nem megosztott tartós előfizetés létrehozásához használja az alábbi metódusokat az `JMSContext` osztályból: 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> A `noLocal` szolgáltatás jelenleg nem támogatott és figyelmen kívül lesz hagyva.
>

A nem megosztott tartós előfizetés továbbra is fennáll, hacsak nem törli a `unsubscribe` metódust az `JMSContext` osztályban.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Megosztott nem tartós előfizetések

Megosztott, nem tartós előfizetést akkor kell használni, ha több ügyfélalkalmazás számára egyetlen előfizetésből származó üzeneteket kell fogadnia és feldolgoznia, csak addig, amíg aktívan nem veszik igénybe, illetve nem kapják meg őket.

Mivel az előfizetés nem tartós, nem őrzi meg a rendszer. Az előfizetés nem fogad üzenetet, ha nincs aktív felhasználó.

Megosztott, nem tartós előfizetés létrehozásához hozzon létre egy, az `JmsConsumer` alábbi metódusokban látható módon a `JMSContext` osztályból:

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

A megosztott nem tartós előfizetés addig is fennáll, amíg aktív fogyasztók érkeznek.

### <a name="unshared-non-durable-subscriptions"></a>Nem megosztva nem tartós előfizetések

A nem megosztott, nem tartós előfizetést akkor használja a rendszer, ha az ügyfélalkalmazás egy előfizetésből származó üzenetet kap és dolgoz fel, csak addig, amíg aktívan nem veszik igénybe. Ezen az előfizetésen csak egy fogyasztó létezhet, azaz az előfizetést létrehozó ügyfél.

Mivel az előfizetés nem tartós, nem őrzi meg a rendszer. Az előfizetés nem fogad üzenetet, ha nincs aktív felhasználó.

A nem megosztott, nem tartós előfizetés létrehozásához hozzon létre egy, az `JMSConsumer` alábbi metódusokban látható módon a `JMSContext` osztályból:

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> A `noLocal` szolgáltatás jelenleg nem támogatott és figyelmen kívül lesz hagyva.
>

A nem megosztott, nem tartós előfizetés addig is fennáll, amíg aktív fogyasztó nem kap tőle.

### <a name="message-selectors"></a>Üzenetek választói

A normál Service Bus-előfizetésekhez hasonlóan a **szűrők és a műveletek** is léteznek, a JMS-előfizetések esetében pedig **üzenetek választhatók** .

Az üzenet-választókat beállíthatja az egyes JMS-előfizetések esetében, és szűrési feltételként létezik az üzenetfejléc tulajdonságaiban. Csak az üzenet-választó kifejezésnek megfelelő fejléc-tulajdonságokkal rendelkező üzenetek érkeznek. A Null érték vagy egy üres karakterlánc azt jelzi, hogy az JMS előfizetés/fogyasztó számára nincs megadva üzenet.

## <a name="next-steps"></a>Következő lépések

További információt és példákat a Service Bus üzenetkezelés használatával kapcsolatban a következő speciális témakörökben talál:

* [Service Bus üzenetkezelés áttekintése](service-bus-messaging-overview.md)
* [A Java Message Service 2,0 API és a Azure Service Bus Premium (előzetes verzió) használata](how-to-use-java-message-service-20.md)



