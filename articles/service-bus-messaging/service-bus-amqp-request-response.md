---
title: AMQP 1,0 Azure Service Bus kérelem-válasz alapú műveletekben | Microsoft Docs
description: Microsoft Azure Service Bus kérelem/válasz alapú műveletek listája.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 4aea0a092ad836c020e23b81d38246ceead22ea0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213294"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 Microsoft Azure Service Bus: kérelem-válasz alapú műveletek

Ez a cikk Microsoft Azure Service Bus kérelmek/válaszok alapú műveletek listáját határozza meg. Ez az információ a AMQP Management 1,0-es verziójának munkatervén alapul.  
  
Részletes vezetékszintű AMQP 1.0 protokoll, amelyből megtudhatja, hogyan Service Bus valósítja meg, és a OASIS AMQP műszaki leírás épül, váltásról a [protokoll útmutatóban Azure Service Bus és az Event Hubs AMQP 1.0-s][amqp 1.0 protokoll – útmutató].  
  
## <a name="concepts"></a>Alapelvek  
  
### <a name="entity-description"></a>Entitás leírása  

Az entitások leírása Service Bus [QueueDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.topicdescription)vagy [SubscriptionDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) hivatkozik.  
  
### <a name="brokered-message"></a>Közvetített üzenet  

Egy üzenetet jelöl Service Busban, amely egy AMQP-üzenetre van leképezve. A leképezés a [Service Bus AMQP protokoll útmutatójában](service-bus-amqp-protocol-guide.md)van meghatározva.  
  
## <a name="attach-to-entity-management-node"></a>Csatolás entitás-felügyeleti csomóponthoz  

A jelen dokumentumban ismertetett összes művelet egy kérelem/válasz mintát követ, hatóköre egy entitás, és az entitás-felügyeleti csomóponthoz való csatolás szükséges.  
  
### <a name="create-link-for-sending-requests"></a>Hivatkozás létrehozása a kérelmek küldéséhez  

Létrehoz egy hivatkozást a felügyeleti csomópontra a kérelmek küldéséhez.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Hivatkozás létrehozása a válaszok fogadásához  

Hivatkozást hoz létre a felügyeleti csomóponttól érkező válaszok fogadásához.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Kérelem üzenetének átvitele  

Átviszi a kérelem üzenetét.  
Tranzakció-állapot adható hozzá a tranzakciót támogató műveletek esetén.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Válaszüzenet fogadása  

A válasz hivatkozását kapja meg.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A válaszüzenet a következő formában jelenik meg:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Service Bus entitás címe  

Service Bus entitásokat a következőképpen kell megoldani:  
  
|Entitástípus|Cím|Példa|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|topic|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Üzenetkezelési műveletek  
  
### <a name="message-renew-lock"></a>Üzenet zárolásának megújítása  

Kiterjeszti egy üzenet zárolását az entitás leírásában megadott idő szerint.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
 A kérelem üzenet törzsének tartalmaznia kell egy amqp szakaszt, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|UUID tömb|Igen|A megújítani kívánt üzenet-zárolási tokenek.|  

> [!NOTE]
> A zárolási tokenek `DeliveryTag` a fogadott üzenetek tulajdonsága. Tekintse meg a következő példát a [.net SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) -ban, amely lekéri ezeket. A jogkivonat a "DeliveryAnnotations" x-opt-Lock-token néven is szerepelhet, ez azonban nem garantált, és a `DeliveryTag` előnyben részesítettnek kell lennie. 
> 
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben sikertelen.|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan amqp szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejáratok|időbélyeg tömbje|Igen|Az üzenet zárolási jogkivonatának új lejárata a kérelem zárolási jogkivonatának megfelelő.|  
  
### <a name="peek-message"></a>Betekintés üzenet  

Üzenetek zárolás nélküli bepillantása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Igen|A betekintési kezdési sorszám.|  
|`message-count`|int|Igen|A bepillantható üzenetek maximális száma.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: Nincs tartalom – nincs több üzenet|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Üzenetek|térképek listája|Igen|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|bájtok tömbje|Igen|AMQP 1,0 dróttal kódolt üzenet.|  
  
### <a name="schedule-message"></a>Ütemezett üzenet  

Üzeneteket ütemezhet. Ez a művelet támogatja a tranzakciót.
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Üzenetek|térképek listája|Igen|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet-azonosító|Karakterlánc|Igen|`amqpMessage.Properties.MessageId`karakterláncként|  
|munkamenet-azonosító|Karakterlánc|Nem|`amqpMessage.Properties.GroupId as string`|  
|partíció – kulcs|Karakterlánc|Nem|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|partíción keresztüli kulcs|Karakterlánc|Nem|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|bájtok tömbje|Igen|AMQP 1,0 dróttal kódolt üzenet.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben sikertelen.|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|hosszú tömb|Igen|Az ütemezett üzenetek sorszáma. A sorozatszámot a rendszer a megszakításra használja.|  
  
### <a name="cancel-scheduled-message"></a>Ütemezett üzenet megszakítása  

Az ütemezett üzenetek megszakítása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|hosszú tömb|Igen|A megszakítani kívánt ütemezett üzenetek sorozatszámai.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben sikertelen.|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|   
  
## <a name="session-operations"></a>Munkamenet-műveletek  
  
### <a name="session-renew-lock"></a>Munkamenet-megújítás zárolása  

Kiterjeszti egy üzenet zárolását az entitás leírásában megadott idő szerint.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: Nincs tartalom – nincs több üzenet|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejárat|timestamp|Igen|Új lejárat.|  
  
### <a name="peek-session-message"></a>Betekintési munkamenet üzenete  

A munkamenet üzeneteinek zárolás nélküli bepillantása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|Igen|A betekintési kezdési sorszám.|  
|üzenetek száma|int|Igen|A bepillantható üzenetek maximális száma.|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: Nincs tartalom – nincs több üzenet|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Üzenetek|térképek listája|Igen|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
 Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|bájtok tömbje|Igen|AMQP 1,0 dróttal kódolt üzenet.|  
  
### <a name="set-session-state"></a>Munkamenet állapotának beállítása  

Egy munkamenet állapotának beállítása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosító.|  
|munkamenet-állapot|bájtok tömbje|Igen|Átlátszatlan bináris adatértékek.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
### <a name="get-session-state"></a>Munkamenet-állapot beolvasása  

Egy munkamenet állapotának beolvasása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-állapot|bájtok tömbje|Igen|Átlátszatlan bináris adatértékek.|  
  
### <a name="enumerate-sessions"></a>Munkamenetek számbavétele  

Üzenetküldési entitások munkameneteinek enumerálása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|utolsó frissítés időpontja|timestamp|Igen|A szűréssel csak egy adott időpont után frissülő munkamenetek jelennek meg.|  
|kihagyás|int|Igen|Több munkamenet kihagyása.|  
|Top|int|Igen|A munkamenetek maximális száma.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: Nincs tartalom – nincs több üzenet|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kihagyás|int|Igen|A kihagyott munkamenetek száma, ha az állapotkód 200.|  
|munkamenetek – azonosítók|Karakterláncok tömbje|Igen|Munkamenet-azonosítók tömbje, ha az állapotkód 200.|  
  
## <a name="rule-operations"></a>Szabály műveletei  
  
### <a name="add-rule"></a>Szabály felvétele  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály neve|Karakterlánc|Igen|Szabály neve, nem tartalmazza az előfizetés és a témakör nevét.|  
|szabály – Leírás|térkép|Igen|A szabály leírása a következő szakaszban megadott módon.|  
  
A **szabály-leírási** térképnek tartalmaznia kell a következő bejegyzéseket, ahol az **SQL-Filter** és a **korrelációs szűrő** kölcsönösen kizárható:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sql-filter|térkép|Igen|`sql-filter`, a következő szakaszban leírtak szerint.|  
|korrelációs szűrő|térkép|Igen|`correlation-filter`, a következő szakaszban leírtak szerint.|  
|sql-rule-action|térkép|Igen|`sql-rule-action`, a következő szakaszban leírtak szerint.|  
  
Az SQL-Filter térképnek tartalmaznia kell a következő bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|expression|Karakterlánc|Igen|SQL-szűrő kifejezése|  
  
A **korreláció-szűrő** térképnek tartalmaznia kell legalább az alábbi bejegyzések egyikét:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|correlation-id|Karakterlánc|Nem||  
|üzenet-azonosító|Karakterlánc|Nem||  
|to|Karakterlánc|Nem||  
|reply-to|Karakterlánc|Nem||  
|label|Karakterlánc|Nem||  
|munkamenet-azonosító|Karakterlánc|Nem||  
|Válasz – munkamenet-azonosító|Karakterlánc|Nem||  
|Content-Type|Karakterlánc|Nem||  
|properties|térkép|Nem|Leképezi Service Bus [BrokeredMessage. properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Az **SQL-Rule-Action** térképnek tartalmaznia kell a következő bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|expression|Karakterlánc|Igen|SQL-művelet kifejezése|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
### <a name="remove-rule"></a>Szabály eltávolítása  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály neve|Karakterlánc|Igen|Szabály neve, nem tartalmazza az előfizetés és a témakör nevét.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
### <a name="get-rules"></a>Szabályok beolvasása

#### <a name="request"></a>Kérés

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  

A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Top|int|Igen|Az oldalon beolvasni kívánt szabályok száma.|  
|kihagyás|int|Igen|A kihagyni kívánt szabályok száma. Meghatározza a szabályok listájának kezdő indexét (+ 1). | 

#### <a name="response"></a>Válasz

A válaszüzenet a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|rules| Térkép tömbje|Igen|A szabályok tömbje. Minden szabályt egy Térkép képvisel.|

A tömb minden leképezési bejegyzése a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály – Leírás|leírt objektumok tömbje|Igen|`com.microsoft:rule-description:list`a AMQP-ben leírt kód 0x0000013700000004| 

`com.microsoft.rule-description:list`a a leírt objektumok tömbje. A tömb a következőket tartalmazza:

|Index|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | leírt objektumok tömbje | Igen | `filter`az alább megadott módon. |
| 1 | a leírt objektum tömbje | Igen | `ruleAction`az alább megadott módon. |
| 2 | Karakterlánc | Igen | a szabály neve. |

`filter`a következő típusok egyike lehet:

| Leíró neve | Leíró kódja | Value |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-szűrő |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelációs szűrő |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Igaz szűrő, amely 1 = 1 értéket jelöl |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Hamis szűrő, amely az 1 = 0 értéket jelöli |

`com.microsoft:sql-filter:list`a egy leírt tömb, amely a következőket tartalmazza:

|Index|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | Karakterlánc | Igen | SQL-szűrő kifejezése |

`com.microsoft:correlation-filter:list`a egy leírt tömb, amely a következőket tartalmazza:

|Index (ha létezik)|Érték típusa|Érték tartalma|  
|---------|----------------|--------------|
| 0 | Karakterlánc | Korrelációs azonosító |
| 1 | Karakterlánc | Üzenet azonosítója |
| 2 | Karakterlánc | Cél |
| 3 | Karakterlánc | Válasz címzettje |
| 4 | Karakterlánc | Címke |
| 5 | Karakterlánc | Munkamenet-azonosító |
| 6 | Karakterlánc | Válasz címzettjének munkamenet-azonosítója|
| 7 | Karakterlánc | Tartalomtípus |
| 8 | Térkép | Alkalmazás által definiált tulajdonságok leképezése |

`ruleAction`a következő típusok egyike lehet:

| Leíró neve | Leíró kódja | Value |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Üres szabály művelet – nincs szabály-művelet |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL-szabály művelete |

`com.microsoft:sql-rule-action:list`a olyan leírt objektumok tömbje, amelyek első bejegyzése egy olyan karakterlánc, amely tartalmazza az SQL-szabály műveletének kifejezését.

## <a name="deferred-message-operations"></a>Késleltetett üzenetküldési műveletek  
  
### <a name="receive-by-sequence-number"></a>Beérkezés sorszám szerint  

A késleltetett üzenetet fogadja sorszám alapján.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|hosszú tömb|Igen|Sorozatszámok.|  
|receiver-settle-mode|ubyte|Igen|A **fogadó rendezési** módja a AMQP Core 1.0-s verzióban megadott módon.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Üzenetek|térképek listája|Igen|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|zárolási token|uuid|Igen|A zárolási `receiver-settle-mode` token if értéke 1.|  
|message|bájtok tömbje|Igen|AMQP 1,0 dróttal kódolt üzenet.|  
  
### <a name="update-disposition-status"></a>Törlési állapot frissítése  

Frissíti a késleltetett üzenetek törlési állapotát. Ez a művelet támogatja a tranzakciókat.
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|operation|Karakterlánc|Igen|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|törlési állapot|Karakterlánc|Igen|Befejezve<br /><br /> elhagyott<br /><br /> függesztve|  
|zárolási tokenek|UUID tömb|Igen|Üzenet zárolási jogkivonatai a törlési állapot frissítéséhez.|  
|kézbesítetlen levelek – ok|Karakterlánc|Nem|Akkor állítható be, ha a törlési állapot **felfüggesztve**értékre van állítva.|  
|kézbesítetlen levelek – Leírás|Karakterlánc|Nem|Akkor állítható be, ha a törlési állapot **felfüggesztve**értékre van állítva.|  
|tulajdonságok – módosítás|térkép|Nem|A módosítani kívánt Service Bus felügyelt üzenet tulajdonságainak listája.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|Karakterlánc|Nem|Az állapot leírása.|

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a AMQP és a Service Busről, látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0 protokoll – útmutató]
* [A Windows Server Service Bus AMQP]

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md
[A Windows Server Service Bus AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
