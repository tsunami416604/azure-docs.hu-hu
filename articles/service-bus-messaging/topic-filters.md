---
title: Az Azure Service Bus témakörszűrői | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy az előfizetők hogyan határozhatják meg, hogy mely üzeneteket szeretnék kapni egy témakörből szűrők megadásával.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: fb6092b7ccb3d1a4214f8d26119d9dc50b0ed317
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482066"
---
# <a name="topic-filters-and-actions"></a>Témakörszűrők és -műveletek

Az előfizetők meghatározhatják, hogy mely üzeneteket szeretnék megkapni egy témakörön belül. Ezen üzenetek egy vagy több névvel ellátott előfizetési szabály formájában adhatók meg. Minden szabály egy feltételből áll, amely kiválasztja az adott üzeneteket, és egy műveletet, amely jegyzetekkel jelöli a kijelölt üzenetet. Az előfizetés megfelelő szabályfeltételenként létrehoz egy üzenetmásolatot, amelyek azután megfelelő szabályonként más és más jelöléssel láthatók el.

Minden újonnan létrehozott témakör-előfizetés rendelkezik egy kezdeti alapértelmezett előfizetési szabállyal. Ha nem ad meg kifejezetten szűrőfeltételt a szabályhoz, az alkalmazott szűrő a **valódi** szűrő, amely lehetővé teszi az összes üzenet kiválasztását az előfizetésben. Az alapértelmezett szabályhoz nincs jegyzetelési művelet.

A Service Bus három szűrőfeltételt támogat:

-   *Logikai szűrők* - A **TrueFilter** és a **FalseFilter** vagy az összes érkező üzenetet **(igaz)** vagy az érkező üzenetek **(hamis**) egyikét sem jelöli ki az előfizetéshez.

-   *SQL-szűrők* – Az **SqlFilter** rendelkezik egy SQL-szerű feltételes kifejezés, amely kiértékeli a közvetítő ellen az érkező üzenetek felhasználó által definiált tulajdonságok és a rendszer tulajdonságait. A feltételes kifejezésben minden `sys.` rendszertulajdonságnak előtaggal kell rendelkeznie. A [szűrőfeltételek SQL-nyelvű részhalmaza](service-bus-messaging-sql-filter.md) a tulajdonságok`EXISTS`( )`IS NULL`létezésére vonatkozó nullértékű ( logikai NEM/ÉS/OR, relációs operátorok, `LIKE`egyszerű numerikus aritmetikai és egyszerű szövegminta- egyezést tesztel a rendszerrel.

-   *Korrelációs szűrők* – A **CorrelationFilter** olyan feltételeket tartalmaz, amelyek egy vagy több érkező üzenet felhasználói és rendszertulajdonságaihoz egyeztetve vannak. Gyakori használata a **CorrelationId tulajdonság egyeztetése,** de az alkalmazás is választhatja, hogy egyezik a következő tulajdonságokkal:

    - **ContentType (Tartalomtípus)**
     - **Címke**
     - **MessageID azonosító**
     - **Válasz**
     - **ReplyToSessionId**
     - **Munkamenet** 
     - **Címzett**
     - a felhasználó által definiált tulajdonságokat. 
     
     Egyezés akkor van, ha egy érkező üzenet értéke egy tulajdonsághoz megegyezik a korrelációs szűrőben megadott értékkel. Karakterlánc-kifejezések esetén az összehasonlítás ban a kis- és nagybetűket kell figyelembe véve. Ha több egyezési tulajdonságot ad meg, a szűrő logikai ÉS feltételként egyesíti őket, ami azt jelenti, hogy a szűrőnek meg kell egyeznie, minden feltételnek egyeznie kell.

Minden szűrő kiértékeli az üzenetek tulajdonságait. A szűrők nem tudják kiértékelni az üzenet törzsét.

Az összetett szűrőszabályok feldolgozási kapacitást igényelnek. Az SQL-szűrőszabályok használata különösen alacsonyabb általános üzenetátviteli-éket okoz az előfizetés, a témakör és a névtér szintjén. Amikor csak lehetséges, az alkalmazásoknak az SQL-szerű szűrőkkel szemben kell korrelációs szűrőket választaniuk, mert azok feldolgozása sokkal hatékonyabb, és kisebb hatást gyakorolnak az átviteli.

## <a name="actions"></a>Műveletek

Az SQL-szűrő feltételek, megadhatja a műveletet, amely jegyzetekkel az üzenetet hozzáadásával, eltávolításával vagy cseréjével tulajdonságok és azok értékeit. A művelet [sql-szerű kifejezést használ,](service-bus-messaging-sql-filter.md) amely lazán támaszkodik az SQL UPDATE utasítás szintaxisára. A művelet az üzeneten történik, miután egyeztetett, és mielőtt az üzenet be van jelölve az előfizetésbe. Az üzenet tulajdonságainak módosításai az előfizetésbe másolt üzenet magánjellegűek.

## <a name="usage-patterns"></a>Használati minták

A témakör legegyszerűbb használati forgatókönyve az, hogy minden előfizetés kap egy másolatot minden egyes üzenetet egy témakörbe küldött, amely lehetővé teszi a szórási mintát.

A szűrők és műveletek két további mintacsoportot engedélyeznek: a particionálást és az útválasztást.

Particionálás szűrők segítségével üzeneteket oszt a több meglévő témakör-előfizetések kiszámítható és kölcsönösen kizáró módon. A particionálási minta akkor használatos, ha egy rendszert úgy osztanak ki, hogy számos különböző környezetet kezeljenek funkcionálisan azonos rekeszekben, amelyek mindegyike a teljes adat egy részhalmazával rendelkezik; például az ügyfélprofil adatait. Particionálás sal a közzétevő elküldi az üzenetet egy témakörbe anélkül, hogy bármilyen ismerete a particionálási modell. Az üzenet ezután a megfelelő előfizetésbe kerül, amelyből a partíció üzenetkezelője lekérheti.

Az Útválasztás szűrőksegítségével kiszámítható módon, de nem feltétlenül kizárólagos módon osztja el az üzeneteket a témakör-előfizetések között. Az automatikus [továbbítási](service-bus-auto-forwarding.md) funkcióval együtt a témakörszűrők segítségével összetett útválasztási grafikonokat hozhat létre egy Service Bus-névtérben az Azure-régión belüli üzenetterjesztéshez. Az Azure Functions vagy az Azure Logic Apps az Azure Service Bus névterei közötti hídként működik, így összetett globális topológiákat hozhat létre az üzletági alkalmazásokba való közvetlen integrációval.


> [!NOTE]
> Jelenleg az Azure Portalon nem engedi, hogy szűrőszabályokat adjon meg az előfizetésekhez. A támogatott SDK-k vagy az Azure Resource Manager-sablonok bármelyikét használhatja az előfizetési szabályok meghatározásához. 

## <a name="next-steps"></a>További lépések
Lásd a következő mintákat: 

- [.NET – Egyszerű küldési és fogadási oktatóanyag szűrőkkel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Témakörszűrők](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/javascript/advanced/topicFilters.js)
- [Parancsfájl beírása](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/typescript/src/advanced/topicFilters.ts)
- [Azure Resource Manager-sablon](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


