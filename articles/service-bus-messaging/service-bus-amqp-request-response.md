---
title: AMQP 1.0 kérés-válasz műveletek az Azure Service Busban
description: Ez a cikk az AMQP-kérelem- és válaszalapú műveletek listáját határozza meg a Microsoft Azure Service Bus szolgáltatásban.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: aschhab
ms.openlocfilehash: 338b411eade35fa357f64131c64b8ef2034c92b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761083"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 a Microsoft Azure Service Bus szolgáltatásban: kérés-válasz alapú műveletek

Ez a cikk a Microsoft Azure Service Bus kérés-/válaszalapú műveletek listáját határozza meg. Ez az információ az AMQP Management 1.0-s verziójú vázlaton alapul.  
  
Az AMQP 1.0 protokoll részletes útmutatóját, amely bemutatja, hogyan valósítja meg a Service Bus az OASIS AMQP műszaki specifikációját, és hogyan épül össze, tekintse meg az [AMQP 1.0 az Azure Service Bus és az Event Hubs protokollútmutatójában,]az[AMQP 1.0 protokollútmutatóban.]  
  
## <a name="concepts"></a>Alapelvek  
  
### <a name="entity-description"></a>Entitás leírása  

Az entitás leírása egy Service Bus [QueueDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [A TopicDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.topicdescription)vagy [a SubscriptionDescription osztályobjektumra](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) hivatkozik.  
  
### <a name="brokered-message"></a>Közvetített üzenet  

Egy üzenetet jelöl a Service Busban, amely AMQP-üzenethez van rendelve. A leképezés a [Service Bus AMQP protokollútmutatójában van definiálva.](service-bus-amqp-protocol-guide.md)  
  
## <a name="attach-to-entity-management-node"></a>Csatolás az entitásfelügyeleti csomóponthoz  

Az ebben a dokumentumban leírt összes művelet egy kérés/válasz mintát követ, hatóköre egy entitáshoz, és egy entitásfelügyeleti csomóponthoz kell kapcsolódnia.  
  
### <a name="create-link-for-sending-requests"></a>Hivatkozás létrehozása a kérelmek küldéséhez  

Kapcsolatot hoz létre a felügyeleti csomópontra a kérelmek küldéséhez.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Hivatkozás létrehozása válaszok fogadására  

Kapcsolatot hoz létre a felügyeleti csomóponttól érkező válaszok fogadására.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Kérésüzenet átvitele  

Kérésüzenet átvitele.  
A tranzakciót támogató műveletekhez tetszés szerint tranzakcióállapot is hozzáadható.

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

A válaszüzenet fogadása a válaszhivatkozásról.  
  
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
  
### <a name="service-bus-entity-address"></a>A Service Bus entitás címe  

A Service Bus entitások címzése a következőképpen történhet:  
  
|Entitástípus|Cím|Példa|  
|-----------------|-------------|-------------|  
|üzenetsor|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|témakör|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|előfizetést|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Üzenetműveletek  
  
### <a name="message-renew-lock"></a>Üzenet zárolásának megújítása  

Kiterjeszti az üzenet zárolását az entitás leírásában megadott időpontig.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
 A kérésüzenet törzsének egy amqp-érték ből kell állnia, amely a következő bejegyzéseket tartalmazó térképet tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|uuid tömb|Igen|Üzenet zárolási tokenek megújításához.|  

> [!NOTE]
> A zárolási `DeliveryTag` jogkivonatok a fogadott üzenetek tulajdonsága. Lásd a következő példát a [.NET SDK-ban,](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) amely ezeket olvassa be. A token is megjelenhet a "DeliveryAnnotations" mint "x-opt-lock-token" azonban `DeliveryTag` ez nem garantált, és a előnyben kell részesíteni. 
> 
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült.|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy amqp-érték ből kell állnia, amely a következő bejegyzéseket tartalmazó térképet tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Expirations|időbélyeg tömbje|Igen|Üzenetzárolási jogkivonat új lejárati, a kérelemzárolási jogkivonatoknak megfelelő új lejárat.|  
  
### <a name="peek-message"></a>Betekintő üzenet  

Betekintés az üzenetek zárolása nélkül.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Igen|Az a sorszám, amelyből a betekintés elindul.|  
|`message-count`|int|Igen|A betekintő üzenetek maximális száma.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - több üzenetet<br /><br /> 204: Nincs tartalom - nincs több üzenet|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Igen|Azoknak az üzeneteknek a listája, amelyekben minden térkép egy üzenetet jelöl.|  
  
Az üzenetet ábrázoló térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|tömbbájt|Igen|AMQP 1.0 vezetékes üzenet.|  
  
### <a name="schedule-message"></a>Üzenet ütemezése  

Üzenetek ütemezése. Ez a művelet támogatja a tranzakciót.
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Igen|Azoknak az üzeneteknek a listája, amelyekben minden térkép egy üzenetet jelöl.|  
  
Az üzenetet ábrázoló térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet-azonosító|sztring|Igen|`amqpMessage.Properties.MessageId`karakterláncként|  
|munkamenet-azonosító|sztring|Nem|`amqpMessage.Properties.GroupId as string`|  
|partíció-kulcs|sztring|Nem|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|keresztül-partíció-kulcs|sztring|Nem|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|tömbbájt|Igen|AMQP 1.0 vezetékes üzenet.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült.|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó térképet tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szekvenciaszámok|tömb nyi|Igen|Az ütemezett üzenetek sorszáma. A sorszám a megszakításra szolgál.|  
  
### <a name="cancel-scheduled-message"></a>Ütemezett üzenet visszavonása  

Megszakítja az ütemezett üzeneteket.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szekvenciaszámok|tömb nyi|Igen|A megszakítandó ütemezett üzenetek sorszámai.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült.|  
|állapotleírás|sztring|Nem|Az állapot leírása.|   
  
## <a name="session-operations"></a>Munkamenet-műveletek  
  
### <a name="session-renew-lock"></a>Munkamenet zárolásának megújítása  

Kiterjeszti az üzenet zárolását az entitás leírásában megadott időpontig.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - több üzenetet<br /><br /> 204: Nincs tartalom - nincs több üzenet|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó térképet tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Lejárati|időbélyeg|Igen|Új lejárat.|  
  
### <a name="peek-session-message"></a>Betekintő munkamenet-üzenet  

Betekintés a munkamenet-üzenetek zárolása nélkül.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szekvenciaszámból|long|Igen|Az a sorszám, amelyből a betekintés elindul.|  
|üzenetszám|int|Igen|A betekintő üzenetek maximális száma.|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - több üzenetet<br /><br /> 204: Nincs tartalom - nincs több üzenet|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó térképet tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Igen|Azoknak az üzeneteknek a listája, amelyekben minden térkép egy üzenetet jelöl.|  
  
 Az üzenetet ábrázoló térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|tömbbájt|Igen|AMQP 1.0 vezetékes üzenet.|  
  
### <a name="set-session-state"></a>Munkamenet-állapot beállítása  

Beállítja a munkamenet állapotát.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosító.|  
|munkamenet-állapot|tömbbájtok|Igen|Átlátszatlan bináris adatok.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
### <a name="get-session-state"></a>Munkamenet-állapot beszereznie  

A munkamenet állapotát kapja.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-állapot|tömbbájtok|Igen|Átlátszatlan bináris adatok.|  
  
### <a name="enumerate-sessions"></a>Munkamenetek számbavétele  

Egy üzenetkezelő entitás munkameneteinek számbavétele.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|utolsó frissítés időpontja|időbélyeg|Igen|Szűrő, amely csak az adott idő után frissített munkameneteket tartalmazza.|  
|Ugrál|int|Igen|Több munkamenet kihagyása.|  
|felül|int|Igen|A munkamenetek maximális száma.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - több üzenetet<br /><br /> 204: Nincs tartalom - nincs több üzenet|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Ugrál|int|Igen|Kihagyott munkamenetek száma, ha az állapotkód 200.|  
|munkamenet-azonosítók|sztringek tömbje|Igen|Munkamenet-azonosítók tömbje, ha az állapotkód 200.|  
  
## <a name="rule-operations"></a>Szabály műveletek  
  
### <a name="add-rule"></a>Szabály hozzáadása  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabálynév|sztring|Igen|Szabály neve, az előfizetés és a témakörnevek nélkül.|  
|szabály leírása|térkép|Igen|A következő szakaszban megadott szabályleírás.|  
  
A **szabályleíró leképezésnek** a következő bejegyzéseket kell tartalmaznia, ahol **az sql-szűrő** és **a korrelációs szűrő** kölcsönösen kizárják egymást:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sql-szűrő|térkép|Igen|`sql-filter`, a következő szakaszban meghatározottak szerint.|  
|korrelációs szűrő|térkép|Igen|`correlation-filter`, a következő szakaszban meghatározottak szerint.|  
|sql-szabály-művelet|térkép|Igen|`sql-rule-action`, a következő szakaszban meghatározottak szerint.|  
  
Az sql-szűrőtérképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Igen|Sql szűrő kifejezés.|  
  
A **korrelációs szűrőtérképnek** a következő bejegyzések legalább egyikét tartalmaznia kell:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|korrelációs azonosító|sztring|Nem||  
|üzenet-azonosító|sztring|Nem||  
|erre:|sztring|Nem||  
|válasz-válasz|sztring|Nem||  
|label|sztring|Nem||  
|munkamenet-azonosító|sztring|Nem||  
|válasz-munkamenet-id|sztring|Nem||  
|tartalomtípus|sztring|Nem||  
|properties|térkép|Nem|Leképezi a Service Bus [BrokeredMessage.Properties webhelyet.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)|  
  
Az **sql-rule-action** térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Igen|Sql műveletkifejezés.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
### <a name="remove-rule"></a>Szabály eltávolítása  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabálynév|sztring|Igen|Szabály neve, az előfizetés és a témakörnevek nélkül.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
### <a name="get-rules"></a>Szabályok beszereznie

#### <a name="request"></a>Kérés

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:

|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  

A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|felül|int|Igen|Az oldalon behozandó szabályok száma.|  
|Ugrál|int|Igen|Az áthagyandó szabályok száma. A szabálylista kezdő indexét (+1) határozza meg. | 

#### <a name="response"></a>Válasz

A válaszüzenet a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült|  
|szabályok| térkép tömbje|Igen|Szabályok tömbje. Minden szabályt egy térkép jelöl.|

A tömb minden egyes térképbejegyzése a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály leírása|leírt objektumok tömbje|Igen|`com.microsoft:rule-description:list`a 0x000001370000004 jelű AMQP kóddal| 

`com.microsoft.rule-description:list`A leírt objektumok tömbje. A tömb a következőket tartalmazza:

|Index|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | leírt objektumok tömbje | Igen | `filter`az alábbiakban meghatározottak szerint. |
| 1 | leírt objektum tömbje | Igen | `ruleAction`az alábbiakban meghatározottak szerint. |
| 2 | sztring | Igen | a szabály nevét. |

`filter`a következő típusok bármelyike lehet:

| Leíró neve | Leíró kódja | Érték |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x00000137000006 | SQL-szűrő |
| `com.microsoft:correlation-filter:list` | 0x00000137000009 | Korrelációs szűrő |
| `com.microsoft:true-filter:list` | 0x00000137000007 | 1=1-et reprezentált igaz szűrő |
| `com.microsoft:false-filter:list` | 0x00000137000008 | 1=0-t reprezentált hamis szűrő |

`com.microsoft:sql-filter:list`egy leírt tömb, amely a következőket tartalmazza:

|Index|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | sztring | Igen | Sql Filter kifejezés |

`com.microsoft:correlation-filter:list`egy leírt tömb, amely a következőket tartalmazza:

|Index (ha létezik)|Érték típusa|Érték tartalma|  
|---------|----------------|--------------|
| 0 | sztring | Korrelációs azonosító |
| 1 | sztring | Üzenet azonosítója |
| 2 | sztring | Művelet |
| 3 | sztring | Válasz |
| 4 | sztring | Címke |
| 5 | sztring | Munkamenet-azonosító |
| 6 | sztring | Válasz a munkamenet azonosítójának|
| 7 | sztring | Tartalomtípus |
| 8 | Térkép | Alkalmazásdefiniált tulajdonságok leképezése |

`ruleAction`a következő típusok lehetnek:

| Leíró neve | Leíró kódja | Érték |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x000001370000005 | Üres szabályművelet – nincs jelen szabályművelet |
| `com.microsoft:sql-rule-action:list` | 0x000001370000006 | SQL szabály művelet |

`com.microsoft:sql-rule-action:list`Olyan leírt objektumok tömbje, amelyek első bejegyzése egy karakterlánc, amely az SQL szabályművelet kifejezését tartalmazza.

## <a name="deferred-message-operations"></a>Késleltetett üzenetműveletek  
  
### <a name="receive-by-sequence-number"></a>Fogadás sorszám szerint  

Halasztott üzenetek fogadása sorszám szerint.  
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szekvenciaszámok|tömb nyi|Igen|Sorszámokat.|  
|vevő-állandó üzemmód|ubyte|Igen|Az AMQP core v1.0-ban megadott **vevői letelepedési** mód.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült|  
|állapotleírás|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Igen|Azoknak az üzeneteknek a listája, amelyekben minden térkép egy üzenetet jelöl.|  
  
Az üzenetet ábrázoló térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|zárolás-token|uuid|Igen|Token zárolása, ha `receiver-settle-mode` 1.|  
|message|tömbbájt|Igen|AMQP 1.0 vezetékes üzenet.|  
  
### <a name="update-disposition-status"></a>Intézkedésállapotának frissítése  

Frissíti a halasztott üzenetek intézkedési állapotát. Ez a művelet támogatja a tranzakciókat.
  
#### <a name="request"></a>Kérés  

A kérésüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Művelet|sztring|Igen|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nem|A műveleti kiszolgáló időmeghosszabbítása ezredmásodpercben.|  
  
A kérésüzenet törzsének egy **amqp-érték** ből kell állnia, amely a következő bejegyzéseket tartalmazó **térképet** tartalmazza:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|intézkedés-állapot|sztring|Igen|Befejezett<br /><br /> Elhagyott<br /><br /> Felfüggesztett|  
|zárolás-tokenek|uuid tömb|Igen|Üzenetzárolási tokenek a leintézkedés állapotának frissítéséhez.|  
|deadletter-ok|sztring|Nem|Beállítható, ha az intézkedés állapota **felfüggesztve**van.|  
|deadletter-leírás|sztring|Nem|Beállítható, ha az intézkedés állapota **felfüggesztve**van.|  
|tulajdonságok-módosítás|térkép|Nem|A módosítandó Service Bus által közvetített üzenettulajdonságok listája.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenetnek a következő alkalmazástulajdonságokat kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK - siker, egyébként nem sikerült|  
|állapotleírás|sztring|Nem|Az állapot leírása.|

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az AMQP-ről és a Service Bus-ról, látogasson el az alábbi linkekre:

* [Service Bus AMQP – áttekintés]
* [AMQP 1.0 protokoll – útmutató]
* [AMQP a Windows Server service busszolgáltatásban]

[Service Bus AMQP – áttekintés]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md
[AMQP a Windows Server service busszolgáltatásban]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
