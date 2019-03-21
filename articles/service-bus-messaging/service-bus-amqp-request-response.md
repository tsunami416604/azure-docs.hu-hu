---
title: Az AMQP 1.0 kérés-válasz alapú műveletek az Azure Service Bus |} A Microsoft Docs
description: A Microsoft Azure Service Bus-kérés/válasz alapú műveletek listája.
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
ms.openlocfilehash: c22ba0b57ed1161e1f7e2082d2ba21f27b656da1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121570"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>A Microsoft Azure Service Bus AMQP 1.0-s: kérés-válasz alapú műveletek

Ez a cikk a Microsoft Azure Service Bus-kérés/válasz alapú műveletek listájának meghatározása. Ez az információ az AMQP felügyeleti 1.0-ás verziójában működő draft alapul.  
  
Részletes vezetékszintű AMQP 1.0 protokoll, amelyből megtudhatja, hogyan Service Bus valósítja meg, és a OASIS AMQP műszaki leírás épül, váltásról a [protokoll útmutatóban Azure Service Bus és az Event Hubs AMQP 1.0-s][amqp 1.0 protokoll – útmutató].  
  
## <a name="concepts"></a>Alapelvek  
  
### <a name="entity-description"></a>Entitás leírása  

Egy entitás leírása hivatkozik, vagy egy Service Bus [QueueDescription osztály](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription osztály](/dotnet/api/microsoft.servicebus.messaging.topicdescription), vagy [SubscriptionDescription osztály](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) objektum.  
  
### <a name="brokered-message"></a>Közvetítőalapú üzenet  

Egy üzenet, amely le van képezve egy AMQP üzenet Service Bus jelöli. A leképezés van definiálva a [Service Bus AMQP protokoll-útmutató](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Entitás kezelési csomópont csatolása  

A jelen dokumentumban ismertetett műveleteket kérés/válasz mintát követi, egy entitás hatóköre és csatolása egy entitáshoz kezelési csomópont szükséges.  
  
### <a name="create-link-for-sending-requests"></a>Küldési kérelmek hivatkozás létrehozása  

A felügyeleti csomópont kérelmeket hivatkozást hoz létre.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>A válaszfogadás hivatkozás létrehozása  

A visszajelzések fogadása a felügyeleti csomópont hivatkozást hoz létre.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Átadási kérelem üzenet  

Egy üzenet továbbítja.  
Egy tranzakció állapota igény szerint, amely támogatja a tranzakciós műveletek is hozzáadhatók.

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
  
### <a name="receive-a-response-message"></a>A válasz üzenet fogadása  

A válasz hivatkozást kap a válaszüzenet.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A következő formában kell megadni a válaszüzenetben:
  
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
  
### <a name="service-bus-entity-address"></a>A Service Bus-entitás címe  

Service Bus-entitások vonhat módon:  
  
|Entitástípus|Cím|Példa|  
|-----------------|-------------|-------------|  
|üzenetsor|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|témakör|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|előfizetést|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Message operations  
  
### <a name="message-renew-lock"></a>Üzenet zárolásának megújítása  

Entitás leírása a kérdéses kiterjeszti az üzenet zárolását.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
 A kérelem üzenet törzse egy egy leképezés a következő bejegyzéseket tartalmazó amqp-érték szakaszból áll:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|uuid tömbje|Igen|Üzenet zárolási-token megújításához.|  

> [!NOTE]
> Zárolás a következő tokenek: a `DeliveryTag` fogadott üzenetek tulajdonsága. A következő példa a a [.NET SDK-val](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) ezek kérdezi le. A jogkivonat is megjelenhetnek "DeliveryAnnotations", "x-jóváhagyás-lock-token" azonban, ez nem garantált, és a `DeliveryTag` előnyben részesített kell lennie. 
> 
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében egy egy leképezés a következő bejegyzéseket tartalmazó amqp-érték szakaszból áll:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejárhat|időbélyeg tömbje|Igen|Üzenet zárolási jogkivonat új lejárati megfelelő a zárolás jogkivonatok kérelmezésére.|  
  
### <a name="peek-message"></a>Belepillantás üzenetbe  

Üzenet szinkron zárolás nélkül.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|hosszú|Igen|Indítsa el a betekintés, amely feladatütemezést.|  
|`message-count`|int|Igen|Belepillantás üzenetek maximális száma.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: Rendben – a további üzeneteket tartalmaz<br /><br /> 204: Nincs tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelenti egy üzenetet üzenetek listája.|  
  
A térkép egy üzenetet jelző tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|Megjeleníti a tömb|Igen|Az AMQP 1.0-s átviteli kódolt üzenet.|  
  
### <a name="schedule-message"></a>Ütemezés üzenet  

Üzenetek ütemezi. Ez a művelet podporuje transakci.
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelenti egy üzenetet üzenetek listája.|  
  
A térkép egy üzenetet jelző tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message-id|sztring|Igen|`amqpMessage.Properties.MessageId` karakterlánc|  
|munkamenet-azonosító|sztring|Nem|`amqpMessage.Properties.GroupId as string`|  
|partition-key|sztring|Nem|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|sztring|Nem|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|Megjeleníti a tömb|Igen|Az AMQP 1.0-s átviteli kódolt üzenet.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** egy leképezés a következő bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|a hosszú tömbje|Igen|Ütemezett üzenetek sorszáma. Sorszám Mégse szolgál.|  
  
### <a name="cancel-scheduled-message"></a>Ütemezett üzenet törlése  

Ütemezett üzenetek megszakítja.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|a hosszú tömbje|Igen|Ütemezett üzenetek megszakítja a sorozatszámok.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** egy leképezés a következő bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|a hosszú tömbje|Igen|Ütemezett üzenetek sorszáma. Sorszám Mégse szolgál.|  
  
## <a name="session-operations"></a>Munkamenet-műveletek  
  
### <a name="session-renew-lock"></a>Munkamenet zárolásának megújítása  

Entitás leírása a kérdéses kiterjeszti az üzenet zárolását.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: Rendben – a további üzeneteket tartalmaz<br /><br /> 204: Nincs tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** egy leképezés a következő bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejárat|időbélyeg|Igen|Új lejárati.|  
  
### <a name="peek-session-message"></a>Munkamenet-üzenet megtekintése  

Munkamenet-üzenetek szinkron zárolás nélkül.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|hosszú|Igen|Indítsa el a betekintés, amely feladatütemezést.|  
|üzenetszám|int|Igen|Belepillantás üzenetek maximális száma.|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: Rendben – a további üzeneteket tartalmaz<br /><br /> 204: Nincs tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** egy leképezés a következő bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelenti egy üzenetet üzenetek listája.|  
  
 A térkép egy üzenetet jelző tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|Megjeleníti a tömb|Igen|Az AMQP 1.0-s átviteli kódolt üzenet.|  
  
### <a name="set-session-state"></a>Munkamenet-állapot beállítása  

A munkamenet állapotának beállítása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
|a munkamenet-állapot|bájttömb|Igen|Bináris adat nem átlátszó.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
### <a name="get-session-state"></a>A munkamenet-állapot lekérése  

A munkamenet állapotának beolvasása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a munkamenet-állapot|bájttömb|Igen|Bináris adat nem átlátszó.|  
  
### <a name="enumerate-sessions"></a>Munkamenetek enumerálása  

Egy üzenetküldési entitásra-munkamenetek enumerálása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|időbélyeg|Igen|Szűrés csak egy adott időpont után frissíti munkamenetek tartalmazza.|  
|kihagyás|int|Igen|Hagyja ki a munkamenetek száma.|  
|felső|int|Igen|Munkamenetek maximális számát.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: Rendben – a további üzeneteket tartalmaz<br /><br /> 204: Nincs tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kihagyás|int|Igen|200-as állapotkód-e a rendszer kihagyta munkamenetek száma.|  
|munkamenet-azonosítók|karakterláncok tömbje|Igen|Munkamenet-azonosítók 200-as állapotkód-e tömbje.|  
  
## <a name="rule-operations"></a>Műveletek  
  
### <a name="add-rule"></a>Szabály hozzáadása  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály neve|sztring|Igen|Szabály neve nem az előfizetés és a témakör neve együtt.|  
|szabály leírása|térkép|Igen|A szabály a következő szakaszban megadott leírása.|  
  
A **szabályleírás** térkép tartalmaznia kell a következő bejegyzéseket, ahol **sql-szűrő** és **korrelációs szűrő** kölcsönösen kizárják egymást:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sql-filter|térkép|Igen|`sql-filter`, a következő szakaszban meghatározottak szerint.|  
|korrelációs szűrő|térkép|Igen|`correlation-filter`, a következő szakaszban meghatározottak szerint.|  
|sql-rule-action|térkép|Igen|`sql-rule-action`, a következő szakaszban meghatározottak szerint.|  
  
Az sql-szűrő térkép tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Igen|SQL szűrőkifejezést.|  
  
A **korrelációs szűrő** térkép tartalmaznia kell legalább egy, az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|korrelációs azonosító|sztring|Nem||  
|message-id|sztring|Nem||  
|erre:|sztring|Nem||  
|Válasz címzettje|sztring|Nem||  
|label|sztring|Nem||  
|munkamenet-azonosító|sztring|Nem||  
|válasz a munkamenet azonosítója|sztring|Nem||  
|content-type|sztring|Nem||  
|properties|térkép|Nem|A Service Bus Maps [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
A **sql szabályművelet** térkép tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Igen|SQL-műveleti kifejezés.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
### <a name="remove-rule"></a>Szabály eltávolítása  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály neve|sztring|Igen|Szabály neve nem az előfizetés és a témakör neve együtt.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
### <a name="get-rules"></a>Szabályok beolvasása

#### <a name="request"></a>Kérés

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  

A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|felső|int|Igen|A lap lehívására szabályok száma.|  
|kihagyás|int|Igen|A kihagyandó szabályok számát. Határozza meg, a kezdőindex (+ 1) szabályok listáját. | 

#### <a name="response"></a>Válasz

A válaszüzenet a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|szabályok| térkép tömbje|Igen|Szabályok tömbje. Minden egyes szabály egy térkép képviseli.|

A tömb minden egyes leképező bejegyzés a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály leírása|itt ismertetett objektumok tömbje|Igen|`com.microsoft:rule-description:list` az AMQP használatával leírt 0x0000013700000004 kód| 

`com.microsoft.rule-description:list` az itt ismertetett objektumokból álló tömb. A tömb a következőket tartalmazza:

|Index|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | itt ismertetett objektumok tömbje | Igen | `filter` alábbiakban meghatározottak szerint. |
| 1 | tömb leírt objektum | Igen | `ruleAction` alábbiakban meghatározottak szerint. |
| 2 | sztring | Igen | a szabály nevét. |

`filter` lehet, a következő típusok egyikét:

| Leíró neve | Leíró kód | Érték |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-szűrőkkel |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelációs szűrő |
| `com.microsoft:true-filter:list` | 0x000001370000007 | 1 = 1 jelölő igaz szűrő |
| `com.microsoft:false-filter:list` | 0x000001370000008 | FALSE (hamis) szűrő jelölő, 1 = 0 |

`com.microsoft:sql-filter:list` a leírt tömbből, amely tartalmazza a következő:

|Index|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | sztring | Igen | SQL-szűrőkkel kifejezés |

`com.microsoft:correlation-filter:list` a leírt tömbből, amely tartalmazza a következő:

|Index (ha létezik)|Érték típusa|Érték tartalma|  
|---------|----------------|--------------|
| 0 | sztring | Korrelációs azonosító |
| 1 | sztring | Üzenet azonosítója |
| 2 | sztring | Művelet |
| 3 | sztring | Válasz címzettje |
| 4 | sztring | Címke |
| 5 | sztring | Munkamenet azonosítója |
| 6 | sztring | Válasz címzettjének munkamenet-azonosítója|
| 7 | sztring | Tartalomtípus |
| 8 | Térkép | Térkép az alkalmazás által meghatározott tulajdonságai |

`ruleAction` a következők valamelyike lehet:

| Leíró neve | Leíró kód | Érték |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Üres szabály hatására végrehajtott művelet – nincs jelen szabály hatására végrehajtott művelet |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL Rule Action |

`com.microsoft:sql-rule-action:list` van, amelynek első bejegyzés egy karakterlánc, amely az SQL szabályművelet kifejezést tartalmaz, itt ismertetett objektumokból álló tömb.

## <a name="deferred-message-operations"></a>Késleltetett üzenet műveletek  
  
### <a name="receive-by-sequence-number"></a>Feljogosítja sorozatszáma  

Késleltetett üzenet sorszáma szerint kap.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|a hosszú tömbje|Igen|Sorozatszámok száma.|  
|receiver-settle-mode|ubyte|Igen|**Fogadó kiegyenlítéséhez** AMQP core 1.0-s verzió megadott módú.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válaszüzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Ahol a minden térkép jelenti egy üzenetet üzenetek listája.|  
  
A térkép egy üzenetet jelző tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|zárolási jogkivonat|uuid|Igen|Zárolási jogkivonat if `receiver-settle-mode` : 1.|  
|message|Megjeleníti a tömb|Igen|Az AMQP 1.0-s átviteli kódolt üzenet.|  
  
### <a name="update-disposition-status"></a>Intézkedési állapotának frissítése  

Frissítések késleltetett üzenet törlése állapotát. Ez a művelet támogatja a tranzakciókat.
  
#### <a name="request"></a>Kérés  

A kérelemüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időkorlátja ezredmásodpercben.|  
  
A kérelem üzenet törzsében kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapot törlése|sztring|Igen|completed<br /><br /> elhagyott<br /><br /> Felfüggesztve|  
|Lock-jogkivonatok|uuid tömbje|Igen|Üzenet zárolási jogkivonat intézkedési állapotának frissítése.|  
|deadletter-reason|sztring|Nem|Ha disposition állapot értéke lehet beállítani **felfüggesztve**.|  
|deadletter-description|sztring|Nem|Ha disposition állapot értéke lehet beállítani **felfüggesztve**.|  
|properties-to-modify|térkép|Nem|Listája a Service Bus közvetítőalapú üzenet tulajdonságainak módosításához.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|

## <a name="next-steps"></a>További lépések

Az AMQP és a Service Bus kapcsolatos további információkért látogasson el az alábbi hivatkozásokat:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0 protokoll – útmutató]
* [A Windows Server a Service Bus AMQP]

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md
[A Windows Server a Service Bus AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
