---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/04/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0ff1e31e52c7db5c41f92cb9e4cb1a17f28dea6f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806400"
---
Az alábbi táblázat a Service Bus üzenetkezelés kvóta adatokat. Információk a díjszabásról és más Service Bus kvótái, tekintse meg a [Service Bus árképzési](https://azure.microsoft.com/pricing/details/service-bus/) áttekintése.

| Kvóta neve | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- | --- |
| Egy Azure-előfizetés alapszintű / standard névterek maximális száma |Névtér |További alapvető / standard névterek későbbi kérelmeket visszautasítja a portálon. |100|
| Prémium névterekben / Azure-előfizetések maximális száma |Névtér |A portál elutasítja a további prémium névterekhez későbbi kérelmeket. |10 |
| Várólista/témakör ezen méretét |Entitás |Határozza meg a várólista/témakör létrehozása után. <br/><br/> Egymást követő bejövő üzenetek utasítja el, és egy kivételt a hívó kód érkezik. |1, 2, 3, 4 GB-os vagy 5 GB.<br /><br />A Premium Termékváltozat, valamint a Standard [particionálás](/azure/service-bus-messaging/service-bus-partitioning) engedélyezve van, a maximális várólista/témakör mérete 80 GB. |
| A névtér egyidejű kapcsolatok száma |Névtér |A további kapcsolatokhoz későbbi kérelmek azért lettek elutasítva, és kivételt megkapta a hívó kód. REST-műveletek nem számítanak bele egyidejű TCP-kapcsolatok. |NetMessaging: 1000<br /><br />AMQP: 5000 |
| Az egyidejű száma a várólista-üzenettémakör-előfizetésben entitás kérelmek fogadására. |Entitás |További kap kérelmek azért lettek elutasítva, és a kivételt a hívó kód érkezik. Ez a kvóta vonatkozik a kombinált száma párhuzamos fogadási műveletek között a témakör az előfizetéseket. |5000 |
| Témakörök/várólisták névtér másodpercenkénti száma |Névtér |Új témakör vagy várakozási sorának hossza a névtér létrehozásához későbbi kérelmeket visszautasítja. Ennek eredményeképpen ha konfigurálva a [Azure-portálon][Azure portal], egy hibaüzenet jön létre. A felügyeleti API hívása, ha kivétel megkapta a hívó kód. |10,000<br /><br />Témakörök és névtérben levő sorok száma összesen legfeljebb 10 000-re kell lennie. |
| Száma [témakörök/várólisták particionálva](/azure/service-bus-messaging/service-bus-partitioning) névtér száma |Névtér |Egy új particionált témakör vagy sor a névtér létrehozásához későbbi kérelmeket visszautasítja. Ennek eredményeképpen ha konfigurálva a [Azure-portálon][Azure portal], egy hibaüzenet jön létre. Ha a felügyeleti API-hívása egy **QuotaExceededException** kivétel megkapta a hívó kód. |Basic és Standard rétegek - 100<br/><br/>Particionált entitások nem támogatottak a [prémium](../articles/service-bus-messaging/service-bus-premium-messaging.md) réteg.<br/><br />Minden egyes particionált üzenetsor vagy témakör száma 10 000 entitás / névtér a beállított kvótát felé. |
| Minden üzenetküldési entitás elérési út maximális mérete: üzenetsor vagy témakör |Entitás |- |260 karakternél |
| Minden üzenetküldési entitás neve maximális mérete: névtér, az előfizetés vagy előfizetés szabály |Entitás |- |50 karakter hosszú lehet |
| Az üzenet maximális méretét [munkamenet-azonosító](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitás |- | 128 |
| A várólista-üzenettémakör-előfizetésben entitás üzenet mérete |Entitás |Meghaladnia ezek mely százalékértékénél kéri, hogy a bejövő üzenetek utasítja el, és egy kivételt a hívó kód érkezik. |Maximális méret: 256 KB ([Standard csomagra](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([prémium csomagban](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Rendszer-terhelés miatt a ezt a határt nem éri el ezeket az értékeket.<br /><br />A fejléc maximális mérete: 64 KB<br /><br />A tulajdonságcsomag fejléc tulajdonságok maximális száma: **bájt/egész szám. MaxValue**<br /><br />A tulajdonságcsomag tulajdonság maximális mérete: nincs explicit korlát. Attól függ, hogy a fejléc maximális méretét. |
| A várólista-üzenettémakör-előfizetésben entitás üzenetméret tulajdonság |Entitás |A **SerializationException** kivétel jön létre. |Üzenetek maximális tulajdonság minden egyes tulajdonsága mérete 32 K. összesített mérete levő összes tulajdonság nem lehet hosszabb 64 k Ez a korlátozás vonatkozik teljes fejlécében a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), egyaránt rendelkezik felhasználói tulajdonságok, valamint a rendszer tulajdonságai (például [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid), és így tovább). |
| Előfizetések témakör másodpercenkénti száma |Entitás |A témakör további előfizetések létrehozásához későbbi kérelmeket visszautasítja. Ennek eredményeképpen ha konfigurálva a portálon keresztül, egy hibaüzenet jelenik meg. Ha a felügyeleti API hívása kivételt a hívó kód érkezik. |2,000 |
| A témakör egy SQL-szűrők száma |Entitás |A témakör további szűrő létrehozásához későbbi kérelmek azért lettek elutasítva, és kivételt megkapta a hívó kód. |2,000 |
| Korrelációs szűrők témakör másodpercenkénti száma |Entitás |A témakör további szűrő létrehozásához későbbi kérelmek azért lettek elutasítva, és kivételt megkapta a hívó kód. |100,000 |
| Méret SQL szűrők/művelet |Névtér |További szűrők létrehozása a későbbi kérelmek azért lettek elutasítva, és kivételt a hívó kód érkezik. |Szűrési feltétel karakterlánc maximális hossza: 1024 (1-K).<br /><br />A szabály művelet karakterlánc maximális hossza: 1024 (1-K).<br /><br />Szabály műveletenként kifejezések maximális száma: 32. |
| Száma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) névtér, üzenetsor vagy témakör / szabályok |Az entitás-, névtér |További szabályok létrehozását a későbbi kérelmek azért lettek elutasítva, és a kivételt a hívó kód érkezik. |Szabályok maximális száma: 12. <br /><br /> Szabályok konfigurálása a Service Bus-névtér összes üzenetsorok és témakörök a névtérre vonatkozik. |
| Tranzakciónként üzenetek száma | Tranzakció | További bejövő üzenetek elutasították, és egy kivétel szerint "nem lehet több mint 100 üzenetek küldése egy tranzakció" megkapta a hívó kód. | 100 <br /><br /> Mindkét **Send()** és **SendAsync()** műveletek. |

[Azure portal]: https://portal.azure.com