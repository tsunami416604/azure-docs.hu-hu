---
title: "Az Azure Service Bus-kérelem-válasz-alapú műveletekben AMQP 1.0-s |} Microsoft Docs"
description: "A Microsoft Azure Service Bus kérelem/válasz alapú műveletek listáját."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.openlocfilehash: 756565b3da6e0a818d1ee3d5e17f942d96be14f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>A Microsoft Azure Service Bus AMQP 1.0: kérelem-válasz-alapú műveletek

Ez a témakör a Microsoft Azure Service Bus kérelem/válasz alapú műveletek listájának meghatározása. Ezek az információk az 1.0-s verziójának AMQP felügyeleti működő vázlat alapul.  
  
Részletes vezetékszintű AMQP 1.0 protokoll, amelyből megtudhatja, hogyan Service Bus valósítja meg, és a OASIS AMQP műszaki leírás épül, váltásról a [protokoll útmutatóban Azure Service Bus és az Event Hubs AMQP 1.0-s](service-bus-amqp-protocol-guide.md).  
  
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
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
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
  
|Entitás típusa|Cím|Példa|  
|-----------------|-------------|-------------|  
|Várólista|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|A témakör|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|előfizetést|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Üzenetművelet  
  
### <a name="message-renew-lock"></a>Üzenet megújítása zárolása  

A zárolás üzenet kiterjeszti a kérdéses entitás leírása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
 A kérelem az üzenet törzse egy leképezést az alábbi bejegyzéseket tartalmazó amqp-érték szakaszból áll:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|uuid tömbje|Igen|Üzenet zárolási-jogkivonat megújításához.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
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
|művelet|Karakterlánc|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|hosszú|Igen|A következőtől betekintés indítására.|  
|`message-count`|int|Igen|Betekintés üzenetek maximális száma.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelöl egy üzenet üzenetek listáját.|  
  
A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Üzenet|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
### <a name="schedule-message"></a>Ütemezés üzenet  

Üzenetek ütemezi.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelöl egy üzenet üzenetek listáját.|  
  
A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenetazonosító|Karakterlánc|Igen|`amqpMessage.Properties.MessageId`karakterlánc|  
|munkamenet-azonosító|Karakterlánc|Igen|`amqpMessage.Properties.GroupId as string`|  
|a partíciókulcs|Karakterlánc|Igen|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|Üzenet|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
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
|művelet|Karakterlánc|Igen|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|a hosszú tömb|Igen|A feladatütemezési ütemezett üzenetet megszakítja a műveletet.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült.|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
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
|művelet|Karakterlánc|Igen|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** egy társítást az alábbi bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejárati|időbélyeg|Igen|Új lejárati.|  
  
### <a name="peek-session-message"></a>Munkamenet-üzenet megtekintése  

Munkamenet-üzenetek szinkron nélküli.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a-sorozat-szám|hosszú|Igen|A következőtől betekintés indítására.|  
|üzenet-száma|int|Igen|Betekintés üzenetek maximális száma.|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** egy társítást az alábbi bejegyzéseket tartalmazó szakasz:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Minden térkép jelöl egy üzenet üzenetek listáját.|  
  
 A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Üzenet|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
### <a name="set-session-state"></a>Munkamenet-állapot beállítása  

A munkamenet állapotának beállítása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosítót.|  
|a munkamenet-állapot|bájttömb|Igen|Nem átlátszó bináris adatok.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
### <a name="get-session-state"></a>A munkamenet-állapot beolvasása  

A munkamenet állapotának beolvasása.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|Karakterlánc|Igen|Munkamenet-azonosítót.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
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
|művelet|Karakterlánc|Igen|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|legutóbbi frissítése idő|időbélyeg|Igen|Csak a megadott idő múlva frissíteni munkamenetek kiszűrik.|  
|Kihagyása|int|Igen|Adott számú munkamenetet kihagyása.|  
|Felső|int|Igen|Munkamenetek maximális számát.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – a további üzeneteket tartalmaz<br /><br /> 0xcc: nem tartalom – nincs további üzenetek|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|Kihagyása|int|Igen|Ha állapotkód 200 kihagyott munkamenetek száma.|  
|munkamenet-azonosítók|Karakterláncok|Igen|Munkamenet-azonosítókat, ha állapotkód 200 tömbje.|  
  
## <a name="rule-operations"></a>Műveletek  
  
### <a name="add-rule"></a>Szabály hozzáadása  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a szabály-név|Karakterlánc|Igen|A szabály nevét, nem beleértve az előfizetés és a témakör neve.|  
|a szabály leírása|térkép|Igen|Szabály a következő szakaszban megadott leírása.|  
  
A **szabályleírás** térkép tartalmaznia kell az alábbi bejegyzéseket, ahol **sql-szűrő** és **korreláció-szűrő** kölcsönösen kizárják egymást:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|SQL-szűrő|térkép|Igen|`sql-filter`, a következő szakaszban meghatározott.|  
|korreláció-szűrő|térkép|Igen|`correlation-filter`, a következő szakaszban meghatározott.|  
|SQL-szabályművelet|térkép|Igen|`sql-rule-action`, a következő szakaszban meghatározott.|  
  
Az sql-szűrő térkép tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|Karakterlánc|Igen|SQL szűrőkifejezés.|  
  
A **korreláció-szűrő** térkép tartalmaznia kell legalább egyet az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|korrelációs azonosító|Karakterlánc|Nem||  
|üzenetazonosító|Karakterlánc|Nem||  
|erre:|Karakterlánc|Nem||  
|Válaszcím|Karakterlánc|Nem||  
|Címke|Karakterlánc|Nem||  
|munkamenet-azonosító|Karakterlánc|Nem||  
|válasz a munkamenet azonosítója|Karakterlánc|Nem||  
|tartalomtípus|Karakterlánc|Nem||  
|properties|térkép|Nem|A Service Bus leképezve [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties).|  
  
A **sql szabályművelet** térkép tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|Karakterlánc|Igen|SQL-művelet kifejezést.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
### <a name="remove-rule"></a>Szabály eltávolítása  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|a szabály-név|Karakterlánc|Igen|A szabály nevét, nem beleértve az előfizetés és a témakör neve.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
## <a name="deferred-message-operations"></a>A késleltetett üzenetművelet  
  
### <a name="receive-by-sequence-number"></a>Fogadási sorszáma szerint  

Sorszám által késleltetett üzeneteket fogad.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|a hosszú tömb|Igen|Sorozatszámok száma.|  
|fogadó módú kiegyenlítéséhez|ubyte|Igen|**Fogadó kiegyenlítéséhez** AMQP 1.0-s core meghatározott módban.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|  
  
A válasz az üzenet törzse kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|a maps listája|Igen|Ha minden térkép jelöli egy üzenet üzenetek listáját.|  
  
A jelző üzenet térképnek tartalmaznia kell az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|zárolási-jogkivonat|UUID|Igen|Zárolási token if `receiver-settle-mode` 1.|  
|Üzenet|a tömb bájt|Igen|AMQP 1.0-s átviteli kódolású üzenet.|  
  
### <a name="update-disposition-status"></a>Témakör állapotának frissítése  

Frissítések késleltetett üzenetek törlése állapotának.  
  
#### <a name="request"></a>Kérés  

A kérelemüzenetben tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|Karakterlánc|Igen|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nem|A művelet kiszolgáló időtúllépése milliszekundumban.|  
  
A kérelem üzenettörzs kell állnia egy **amqp-érték** tartalmazó szakasz egy **térkép** az alábbi bejegyzéseket:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|témakör-állapot|Karakterlánc|Igen|Befejeződött<br /><br /> Elhagyott<br /><br /> Felfüggesztve|  
|zárolási-tokenek|uuid tömbje|Igen|Üzenet zárolási jogkivonatok törlése állapotának frissítése.|  
|kézbesítetlen levelek-OK|Karakterlánc|Nem|Előfordulhat, hogy állítható be, ha szabályozó beállítás **felfüggesztve**.|  
|kézbesítetlen levelek – leírás|Karakterlánc|Nem|Előfordulhat, hogy állítható be, ha szabályozó beállítás **felfüggesztve**.|  
|tulajdonságok-módosítása|térkép|Nem|Lista a Service Bus közvetítőalapú üzenet tulajdonságainak módosításához.|  
  
#### <a name="response"></a>Válasz  

A válaszüzenet tartalmaznia kell a következő alkalmazás tulajdonságai:  
  
|Kulcs|Érték típusa|Szükséges|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Igen|HTTP-válaszkód [RFC2616]<br /><br /> 200: OK – sikeres, ellenkező esetben nem sikerült|  
|StatusDescription|Karakterlánc|Nem|Az állapot leírása.|

## <a name="next-steps"></a>Következő lépések

Amqp-t és a Service Bus kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [Particionált Service Bus-üzenetsorok és témakörök AMQP 1.0 támogatása]
* [A Service Bus a Windows Server AMQP]

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[Particionált Service Bus-üzenetsorok és témakörök AMQP 1.0 támogatása]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[A Service Bus a Windows Server AMQP]: https://msdn.microsoft.com/library/dn574799.asp