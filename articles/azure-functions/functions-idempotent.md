---
title: Azure Functions megtervezése azonos bevitelhez
description: A idempotens Azure Functions kiépítése
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 39e785a1ca7a158ddb90a3e6ba914582c405612a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997392"
---
# <a name="designing-azure-functions-for-identical-input"></a>Azure Functions megtervezése azonos bevitelhez

Az eseményvezérelt és az üzenetsor-alapú architektúra valósága azt diktálja, hogy az adatok integritásának és a rendszer stabilitásának megőrzése mellett azonos kérelmeket is el kell fogadnia.

A bemutatóhoz vegye fontolóra egy lift hívási gombját. A gomb megnyomásakor a rendszer kigyullad és egy liftet kap a padlóra. Néhány pillanat múlva valaki más csatlakozik a lobbyban. Ez a személy mosolyog Önnel, és másodszor is megnyomja a megvilágított gombot. Mosolyogjon újra, és ne feledje, hogy a idempotens meghívására szolgáló parancs.

Egy lift hívási gombjának megnyomásakor a második, a harmadik vagy a negyedik időpont nem veszi figyelembe a végeredményt. Amikor megnyomja a gombot, az időpontok számától függetlenül a rendszer elküldje a liftet a padlóra. A idempotens rendszerek, mint például a lift, ugyanazt az eredményt eredményezik, függetlenül attól, hogy hányszor adtak ki azonos parancsokat.

Az alkalmazások létrehozásához vegye figyelembe a következő forgatókönyveket:

- Mi történik, ha a leltár-ellenőrzési alkalmazás többször is megpróbálja törölni ugyanazt a terméket?
- Hogyan viselkedik az emberi erőforrás alkalmazása, ha több kérelem is létezik egy alkalmazotti rekord létrehozásához ugyanahhoz a személyhez?
- Hová kerül a pénz abban az esetben, ha a banki alkalmazás 100 kérést kap, hogy ugyanazt a kivonást végezze el?

Számos kontextusban előfordulhat, hogy a függvények kérései azonos parancsokat fogadnak. Bizonyos helyzetek például a következők:

- Újrapróbálkozási szabályzatok többször is elküldik ugyanazt a kérést
- Az alkalmazás visszajátszotta a gyorsítótárazott parancsokat
- Több azonos kérést küldő alkalmazáshiba

Az adatok integritásának és a rendszerállapotának védelme érdekében egy idempotens-alkalmazás olyan logikát tartalmaz, amely a következő viselkedéseket tartalmazhatja:

- Az adatlétezés ellenőrzése a törlési kísérlet végrehajtása előtt
- Annak ellenőrzése, hogy a létrehozási művelet végrehajtása előtt már léteznek-e az adathalmazok
- A végleges konzisztenciát létrehozó logika összeegyeztetése
- Egyidejűségi vezérlők
- Ismétlődések észlelése
- Az adatfrissesség ellenőrzése
- Őr logikája a bemeneti adatok ellenőrzéséhez

Végső soron a idempotencia egy adott művelet lehetséges, és csak egyszer kell végrehajtani.
