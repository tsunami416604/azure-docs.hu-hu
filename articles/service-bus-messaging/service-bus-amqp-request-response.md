---
title: Az Azure Service Bus-kérelem-válasz-alapú műveletekben AMQP 1.0-s |} Microsoft Docs
description: A Microsoft Azure Service Bus kérelem/válasz alapú műveletek listáját.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: cda313085d197558e969309eaed928421b0b1924
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752904"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>A Microsoft Azure Service Bus AMQP 1.0: kérelem-válasz-alapú műveletek

Ez a cikk a Microsoft Azure Service Bus kérelem/válasz alapú műveletek listájának meghatározása. Ezek az információk az 1.0-s verziójának AMQP felügyeleti működő vázlat alapul.  
  
Részletes vezetékszintű AMQP 1.0 protokoll, amelyből megtudhatja, hogyan Service Bus valósítja meg, és a OASIS AMQP műszaki leírás épül, váltásról a [protokoll útmutatóban Azure Service Bus és az Event Hubs AMQP 1.0-s][amqp 1.0 protokoll – útmutató].  
  
## <a name="concepts"></a>Alapelvek  
  
### <a name="entity-description"></a>Entitás leírása  

Egy entitás leírást hivatkozik, vagy egy Service Bus [QueueDescription osztály](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription osztály](/dotnet/api/microsoft.servicebus.messaging.topicdescription), vagy [SubscriptionDescription osztály](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) objektum.  
  
### <a name="brokered-message"></a>A közvetítőalapú üzenet  

A Service Busba, amely hozzá van rendelve egy AMQP üzenethez üzenet jelöli. A leképezés van definiálva a [Service Bus AMQP protokoll útmutató](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Entitás csomópontot csatlakoztatni  

A jelen dokumentumban ismertetett összes műveletet hajtsa végre a kérelem/válasz minta entitás hatóköre és egy entitás csomópontot csatolása igényelnek.  
  
### <a name="create-link-for-sending-requests"></a>Kérelmek küldése a hivatkozás létrehozása  

Kapcsolatot hoz létre a felügyeleti csomópontra kérelmek küldéséhez.  
  
```  
requestLink = session.attach(     
role: SENDER,   
    target: { address: "<entity address>/$management" },   
    source: { address: ""<my request link unique address>" }   
)  
  
```  
  
### <a name="create-link-for-receiving-responses"></a>A válaszok hivatkozás létrehozása  

Kapcsolatot hoz létre a válasz fogadása a csomópontot.  
  
```  
responseLink = session.attach(    
role: RECEIVER,   
    source: { address: "<entity address>/$management" }   
    target: { address: "<my response link unique address>" }   
)  
  
```  
  
### <a name="transfer-a-request-message"></a>Egy üzenet átvitele  

Egy üzenet továbbítja.  
A tranzakció állapota a műveletek, amely támogatja a tranzakció nem kötelező lehet hozzáadni.

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
  
### <a name="receive-a-response-message"></a>A válasz üzenet  

A válasz üzenet válasz hivatkozást.  
  
```  
responseMessage = responseLink.receiveTransfer()  
```  
  
A válasz üzenet a következő formában:
  
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
  
### <a name="service-bus-entity-address"></a>A Service Bus személy címe  

Service Bus-entitások az alábbiak szerint kell figyelembe venni:  
  
|Entitástípus|Cím|Példa|  
|-----------------|-------------|-------------|  
|üzenetsor|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|témakör|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|előfizetést|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Üzenetművelet  
  
### <a name="message-renew-lock"></a>Üzenet megújítása zárolása  

A zárolás üzenet kiterjeszti a kérdéses entitás leírása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
 A kérelem az üzenet törzse egy leképezést az alábbi bejegyzéseket tartalmazó amqp-érték szakaszból áll:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|uuid tömbje|Igen|Üzenet zárolási-jogkivonat megújításához.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse egy leképezést az alábbi bejegyzéseket tartalmazó amqp-érték szakaszból áll:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejáratok|Timestamp típusú tömb|Igen|Üzenet zárolás token új lejárati ideje a zárolás jogkivonatok megfelelő.|  
  
### <a name="peek-message"></a>Üzenet megtekintése  

Szinkron üzenetek nélkül zárolását.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|hossz|Igen|A következőtől betekintés indítására.|  
|`message-count`|int|Igen|Betekintés üzenetek maximális száma.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelöl egy üzenet üzenetek listáját.|  
  
A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
### <a name="schedule-message"></a>Ütemezés üzenet  

Üzenetek ütemezi. Ez a művelet a tranzakció támogatja.
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelöl egy üzenet üzenetek listáját.|  
  
A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenetazonosító|sztring|Igen|`amqpMessage.Properties.MessageId` karakterlánc|  
|munkamenet-azonosító|sztring|Nem|`amqpMessage.Properties.GroupId as string`|  
|a partíciókulcs|sztring|Nem|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|keresztül-partíció-kulcs|sztring|Nem|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** egy társítást az alábbi bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|a hosszú tömb|Igen|Ütemezett üzenetek számát. Sorszám használatos megszakítja a műveletet.|  
  
### <a name="cancel-scheduled-message"></a>Ütemezett üzenet törlése  

Megszakítja az üzenetek ütemezett.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|a hosszú tömb|Igen|A feladatütemezési ütemezett üzenetet megszakítja a műveletet.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** egy társítást az alábbi bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|a hosszú tömb|Igen|Ütemezett üzenetek számát. Sorszám használatos megszakítja a műveletet.|  
  
## <a name="session-operations"></a>Munkamenet-műveletek  
  
### <a name="session-renew-lock"></a>Munkamenet Renew zárolása  

A zárolás üzenet kiterjeszti a kérdéses entitás leírása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** egy társítást az alábbi bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejárat|időbélyeg|Igen|Új lejárati.|  
  
### <a name="peek-session-message"></a>Munkamenet-üzenet megtekintése  

Munkamenet-üzenetek szinkron nélküli.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a-sorozat-szám|hossz|Igen|A következőtől betekintés indítására.|  
|üzenet-száma|int|Igen|Betekintés üzenetek maximális száma.|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** egy társítást az alábbi bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelöl egy üzenet üzenetek listáját.|  
  
 A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
### <a name="set-session-state"></a>Munkamenet-állapot beállítása  

A munkamenet állapotának beállítása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
|a munkamenet-állapot|bájttömb|Igen|Nem átlátszó bináris adatok.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
### <a name="get-session-state"></a>A munkamenet-állapot beolvasása  

A munkamenet állapotának beolvasása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a munkamenet-állapot|bájttömb|Igen|Nem átlátszó bináris adatok.|  
  
### <a name="enumerate-sessions"></a>Munkamenetek enumerálása  

Egy üzenetküldési entitásra munkamenetek enumerálása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|legutóbbi frissítése idő|időbélyeg|Igen|Csak a megadott idő múlva frissíteni munkamenetek kiszűrik.|  
|hagyja ki|int|Igen|Adott számú munkamenetet kihagyása.|  
|felső|int|Igen|Munkamenetek maximális számát.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|hagyja ki|int|Igen|Ha állapotkód 200 kihagyott munkamenetek száma.|  
|munkamenet-azonosítók|karakterláncok|Igen|Munkamenet-azonosítókat, ha állapotkód 200 tömbje.|  
  
## <a name="rule-operations"></a>Műveletek  
  
### <a name="add-rule"></a>Szabály hozzáadása  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a szabály-név|sztring|Igen|A szabály nevét, nem beleértve az előfizetés és a témakör neve.|  
|a szabály leírása|Térkép|Igen|Szabály a következő szakaszban megadott leírása.|  
  
A **szabályleírás** térkép tartalmaznia kell az alábbi bejegyzéseket, ahol **sql-szűrő** és **korreláció-szűrő** kölcsönösen kizárják egymást:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sql-filter|Térkép|Igen|`sql-filter`, a következő szakaszban meghatározott.|  
|korreláció-szűrő|Térkép|Igen|`correlation-filter`, a következő szakaszban meghatározott.|  
|sql-rule-action|Térkép|Igen|`sql-rule-action`, a következő szakaszban meghatározott.|  
  
Az sql-szűrő térkép tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Igen|SQL szűrőkifejezés.|  
  
A **korreláció-szűrő** térkép tartalmaznia kell legalább egyet az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|korrelációs azonosító|sztring|Nem||  
|üzenetazonosító|sztring|Nem||  
|erre:|sztring|Nem||  
|Válaszcím|sztring|Nem||  
|label|sztring|Nem||  
|munkamenet-azonosító|sztring|Nem||  
|válasz a munkamenet azonosítója|sztring|Nem||  
|tartalomtípus|sztring|Nem||  
|properties|Térkép|Nem|A Service Bus leképezve [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties).|  
  
A **sql szabályművelet** térkép tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Igen|SQL-művelet kifejezést.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
### <a name="remove-rule"></a>Szabály eltávolítása  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a szabály-név|sztring|Igen|A szabály nevét, nem beleértve az előfizetés és a témakör neve.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
### <a name="get-rules"></a>Szabályok lekérése

#### <a name="request"></a>Kérés

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  

A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|felső|int|Igen|A lapon lehívása szabályok száma.|  
|hagyja ki|int|Igen|Szabályokat kihagyását száma. A kezdő index (+ 1) határozza meg a szabályok listáját. | 

#### <a name="response"></a>Válasz

A válaszüzenet tartalmazza a következő tulajdonságokkal:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|szabályok| térkép tömb|Igen|Szabályok tömbje. Minden egyes szabály egy térkép jelképezi.|

A tömb minden egyes térkép bejegyzés tartalmazza a következő tulajdonságokkal:

|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a szabály leírása|ismertetett objektumokból álló tömb|Igen|`com.microsoft:rule-description:list` az AMQP leírt 0x0000013700000004 kódot| 

`com.microsoft.rule-description:list` az ismertetett objektumokból álló tömb. A tömb az alábbiakat tartalmazza:

|Index|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | ismertetett objektumokból álló tömb | Igen | `filter` alábbiakban meghatározottak szerint. |
| 1 | a tömb ismertetett objektum | Igen | `ruleAction` alábbiakban meghatározottak szerint. |
| 2 | sztring | Igen | A szabály nevét. |

`filter` lehet, a következő típusok egyikét:

| Leíró neve | Leíró kódot | Érték |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-szűrő |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelációs szűrő |
| `com.microsoft:true-filter:list` | 0x000001370000007 | 1 = 1 jelölő igaz szűrő |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Jelző, 1 = 0 hamis szűrő |

`com.microsoft:sql-filter:list` a leírt tömbből, amely tartalmazza a következő:

|Index|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | sztring | Igen | SQL szűrőkifejezés |

`com.microsoft:correlation-filter:list` a leírt tömbből, amely tartalmazza a következő:

|Index (ha létezik)|Érték típusa|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | sztring | Korrelációs azonosító |
| 1 | sztring | Üzenet azonosítója |
| 2 | sztring | Művelet |
| 3 | sztring | Válasz címzettje |
| 4 | sztring | Címke |
| 5 | sztring | Munkamenet-azonosító |
| 6 | sztring | Válasz a munkamenet-azonosító|
| 7 | sztring | Tartalomtípus |
| 8 | Térkép | Az alkalmazás által meghatározott tulajdonságok térkép |

`ruleAction` a következő típusok valamelyike lehet:

| Leíró neve | Leíró kódot | Érték |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Üres szabály művelet – nincs jelen szabályművelet |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL szabályművelet |

`com.microsoft:sql-rule-action:list` az ismertetett, amelynek első bejegyzés az SQL szabályművelet kifejezést tartalmazó karakterlánc objektumokból álló tömb.

## <a name="deferred-message-operations"></a>A késleltetett üzenetművelet  
  
### <a name="receive-by-sequence-number"></a>Fogadási sorszáma szerint  

Sorszám által késleltetett üzeneteket fogad.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|a hosszú tömb|Igen|Sorozatszámok száma.|  
|receiver-settle-mode|ubyte|Igen|**Fogadó kiegyenlítéséhez** AMQP 1.0-s core meghatározott módban.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Ha minden térkép jelöli egy üzenet üzenetek listáját.|  
  
A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|zárolási-jogkivonat|UUID|Igen|Zárolási token if `receiver-settle-mode` 1.|  
|message|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
### <a name="update-disposition-status"></a>Témakör állapotának frissítése  

Frissítések késleltetett üzenetek törlése állapotának. Ez a művelet támogatja a tranzakciókat.
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Igen|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|témakör-állapot|sztring|Igen|befejeződött<br /><br /> elhagyott<br /><br /> Felfüggesztve|  
|zárolási-tokenek|uuid tömbje|Igen|Üzenet zárolási jogkivonatok törlése állapotának frissítése.|  
|kézbesítetlen levelek-OK|sztring|Nem|Előfordulhat, hogy állítható be, ha szabályozó beállítás **felfüggesztve**.|  
|kézbesítetlen levelek – leírás|sztring|Nem|Előfordulhat, hogy állítható be, ha szabályozó beállítás **felfüggesztve**.|  
|tulajdonságok-módosítása|Térkép|Nem|Lista a Service Bus közvetítőalapú üzenet tulajdonságainak módosításához.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|állapotkód|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|statusDescription|sztring|Nem|Az állapot leírása.|

## <a name="next-steps"></a>További lépések

Amqp-t és a Service Bus kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0 protokoll – útmutató]
* [A Service Bus a Windows Server AMQP]

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md
[A Service Bus a Windows Server AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)