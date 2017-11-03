---
title: "Az Azure Service Bus témakör szűrők |} Microsoft Docs"
description: "Azure Service Bus-üzenettémakörök szűrése"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: sethm
ms.openlocfilehash: b3fe467b7d6ae9b207956ece4980bf558a69761f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="topic-filters-and-actions"></a>A témakör szűrők és műveletek

Előfizetők témakör fogadása szeretnék üzenetet adhat meg. Ezek az üzenetek egy vagy több elnevezett előfizetési szabályokat formájában vannak megadva. Minden egyes szabály egy feltétel, ami az adott üzeneteket, és egy műveletet, amely jelzi a kijelölt üzenet áll. Minden egyező szabályt feltétel az előfizetés az üzenet, amely előfordulhat, hogy másképp feliratozni minden egyező szabály másolatot hoz létre.

Minden egyes újonnan létrehozott üzenettémakör-előfizetésben a kezdeti alapértelmezett előfizetés szabályt tartalmaz. Ha nem Ön kifejezetten megad egy szűrési feltételt a szabályhoz, van-e az alkalmazott szűrő a **igaz** szűrő, amely lehetővé teszi az összes üzenet ki kell választani a előfizetéssé. Az alapértelmezett szabály nincs társítva a jegyzet művelet van.

A Service Bus három szűrőfeltételei támogatja:

-   *Logikai szűrők* – a **TrueFilter** és **FalseFilter** vagy következtében az összes beérkező üzenetek (**igaz**) vagy a beérkező üzenetek egyike (**hamis**) ki kell választani az előfizetéshez.

-   *Az SQL-szűrők* - A **SqlFilter** egy SQL-szerű feltételt, amely értékeli a beérkező üzenetek felhasználói és a rendszer tulajdonságai elleni broker tartalmazza. Az összes rendszertulajdonsághoz előtaggal kell `sys.` a feltételes kifejezés. A [SQL-nyelv részhalmaza a szűrési feltételeket](service-bus-messaging-sql-filter.md) tulajdonságok (EXISTS) is null értékek (IS NULL), a logikai nem/és/vagy, a relációs operátorok, a egyszerű numerikus aritmetika és az egyszerű szöveges minta megléte meglétének ellenőrzése a hasonló megfelelő.

-   *Korrelációs szűrők* - A **CorrelationFilter** tárolja a feltételek, amelyek megfelelnek egy vagy több, egy beérkező üzenetet felhasználói és a rendszer tulajdonság ellen. A közös használata az egyeztetéshez a **CorrelationId** tulajdonságot, de az alkalmazás is beállíthatja az egyeztetéshez **ContentType**, **címke**,  **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **való**, és minden felhasználó által definiált tulajdonságok. Van egyezés, ha egy beérkező üzenetet egy tulajdonság értéke a korrelációs szűrőben megadott értéknél. A karakterlánc kifejezése eredményű összehasonlítás esetén kis-és nagybetűket. Több egyező tulajdonság megadása esetén a szűrő logikai AND feltétele, tehát a megfelelő szűrő egyesíti őket, minden feltétel meg kell egyeznie.

Az összes szűrő üzenettulajdonságok kiértékeléséhez. Szűrők az üzenettörzs nem értékelhető ki.

Összetett szabályokhoz feldolgozási kapacitás. SQL Állapotszűrő-szabályok használata különösen az előfizetés, a témakör és a névtér szintjén alacsonyabb teljes üzenet teljesítményt eredményez. Amikor csak lehetséges, alkalmazások válasszon korrelációs szűrők SQL-szerű szűrők, mivel sokkal hatékonyabban a feldolgozása, és ezért az átviteli sebesség rendelkezik kisebb mértékű befolyásolása mellett.

## <a name="actions"></a>Műveletek

SQL szűrési feltételeket, és csak azokat definiálhat egy műveletet, amely az üzenet fűzhet hozzá, eltávolítása vagy cseréje a tulajdonságok és értékeik. A művelet [SQL-szerű kifejezést használ](service-bus-messaging-sql-filter.md) , amely lazán leans meg a frissítés az SQL-utasítás szintaxisa. A művelet végrehajtását az üzenet után egyeztetett, mielőtt az üzenetet a témakörbe van kiválasztva. Az üzenet tulajdonságainak a módosítása csak az előfizetés átmásolja az üzenetet a.

## <a name="usage-patterns"></a>Használati minták

A legegyszerűbb használati forgatókönyvtől témakör, hogy minden előfizetés lekérdezi egy példányát minden üzenetet elküldenek egy témakörbe, amely lehetővé teszi egy szórási mintát.

Szűrők és műveletek lehetővé teszik annak a két további csoportok: particionálás és útválasztást.

A particionáló használ szűrők üzenetek szét több meglévő üzenettémakör-előfizetéseket a kiszámítható és egymást kölcsönösen kizáró módon. A particionálási mintát használatos, amikor a rendszer gyakorlatilag azonos szegmenst, hogy mindegyik rendelkezik-e az összesített adatok; egy részét a sok különböző környezetekben kezelésére van horizontálisan például a felhasználói profil adatait. A particionálás, a közzétevő küldi el az üzenetet egy témakörbe anélkül, hogy a particionáló modell ismerete. Az üzenet majd átkerül a megfelelő előfizetés, amelyből, majd lekérheti a partíció üzenetkezelő.

Üzenetek szét üzenettémakör-előfizetéseket, kiszámítható módon szűrők útválasztást használ, de nem feltétlenül kizárólagos. Együtt a [automatikus továbbítási](service-bus-auto-forwarding.md) funkció, szűrők segítségével összetett műveletterv témakör grafikonokon üzenet terjesztési belül egy Azure-régió, a Service Bus-névtér belül. Az Azure Functions vagy Azure Logic Apps hidat Azure Service Bus-névtér között működött az üzletági alkalmazások közvetlen integrálása összetett globális topológiák hozhat létre.

## <a name="next-steps"></a>Következő lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [SQLFilter szintaxis](service-bus-messaging-sql-filter.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)