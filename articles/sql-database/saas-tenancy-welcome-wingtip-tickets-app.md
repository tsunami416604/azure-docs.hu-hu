---
title: "Üdvözli a Wingtips alkalmazás – az Azure SQL adatbázis |} Microsoft Docs"
description: "Ismerje meg az adatbázis-bérlőhöz modelleket, és a minta Wingtips SaaS-alkalmazáshoz, az Azure SQL Database a felhőalapú környezetben."
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Üdvözli a Wingtip jegyek SaaS Azure SQL Database bérleti mintaalkalmazás

Üdvözli a Wingtip jegyek SaaS Azure SQL Database bérleti mintaalkalmazást, és az oktatóprogramok. Adatbázis-bérlőhöz az elkülönítési módja, amely az alkalmazás biztosít az ügyfelek számára, akik után kell fizetnie, az alkalmazás üzemeltetését hivatkozik. Keresztül leegyszerűsítése egyelőre vagy minden egyes ügyfélnek van magát egy egész adatbázist, vagy azonos adatbázist más ügyféllel.

## <a name="wingtip-tickets-app"></a>A Wingtip jegyek alkalmazás

A Wingtip jegyek minta bemutatja eredő különböző adatbázis bérlős modell a tervezési és vezetése a több-bérlős SaaS-alkalmazásokhoz. A hozzá tartozó oktatóanyagok közvetlenül ezek azonos hatások írják le. A Wingtip jegyek Azure SQL Database épül.

A Wingtip jegyek tényleges Szolgáltatottszoftver-ügyfelek által használt különböző kialakítási és felügyeleti forgatókönyvek kezelésére terveztek. A mintákat keressen az, ami kiderült Wingtip jegyek venni az.

A Wingtip jegyek alkalmazást is telepíthető a saját Azure-előfizetés öt perc múlva. A rendszer több bérlő mintaadatok beszúrását is. Biztonságosan telepíthető az alkalmazás és a parancsfájlok a modellek, mert a telepítés nem interaktív, vagy ütközik egymással.

#### <a name="code-in-github"></a>Kódja a Githubon

Alkalmazáskód, és a parancsfájlok, amelyek összes elérhető a Githubon:

- **Önálló alkalmazás** modell: [WingtipTicketsSaaS-StandaloneApp tárház](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- **Bérlőnként adatbázis** modell: [WingtipTicketsSaaS-DbPerTenant tárház](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
- **Horizontálisan skálázott több-bérlős** modell: [WingtipTicketsSaaS-MultiTenantDB tárház](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB).

A azonos egy kódbázis a Wingtip jegyek alkalmazás az összes felsorolt előző típust használja fel újra. A kódot a Githubról használhatja saját Szolgáltatottszoftver-projektek elindításához.



## <a name="major-database-tenancy-models"></a>Fő adatbázis bérleti modellek

Wingtip jegyek egy eseményt, listázása és jegykezelési SaaS-alkalmazáshoz. A Wingtip helyszínek szükséges szolgáltatásokat is nyújt. A következő elemek egyes helyszínekkel vonatkoznak:

- Fizet, hogy az alkalmazás-ban üzemeltetett.
- Van egy *bérlői* a Wingtip.
- Gazdagépek események. A következő események történnek:
    - A jegy árak.
    - A jegy értékesítési.
    - Felhasználói jegyek megvásárlása.

Az alkalmazás, kezelési parancsprogramok és oktatóanyagok bővíthető teljes Szolgáltatottszoftver-forgatókönyv. A forgatókönyv a következő tevékenységet tartalmazza:

- Bérlők kiépítését.
- Figyelés és a teljesítmény kezelése.
- Séma kezelése.
- Határokon bérlői jelentéskészítés és elemzés.

Adott tevékenységek kapnak, függetlenül a skála van szükség.



## <a name="code-samples-for-each-tenancy-model"></a>Minden bérlőhöz modell mintakódok

Alkalmazás modellek készlete megcélzó vannak. Azonban más két vagy több modell elemeinek sikerült kombinálhatók.

#### <a name="standalone-app-model"></a>Önálló alkalmazás modell

![Önálló alkalmazás modell][standalone-app-model-62s]

Ez a modell egy bérlői egyetlen alkalmazás használja. Ezért a modellnek támogatnia kell a csak egy adatbázis, és tárolja az adatokat csak a több bérlő számára. A bérlő élvez teljes elkülönítése a bérlőktől az adatbázisban.

Ezt a modellt használja eladása példányok az alkalmazás számos különböző ügyfelekre, az egyes ügyfelek futtathatnak saját előfordulhat, hogy. Az ügyfél ezután csak a bérlői. Az adatbázis tárolja az adatokat csak egy ügyfelet, amíg az adatbázis tárolja az adatokat az ügyfél sok felhasználónál.

#### <a name="database-per-tenant"></a>Bérlőnként adatbázis

![Bérlői model adatbázishoz][database-per-tenant-model-35d]

Ez a modell több bérlő az az alkalmazás-példány van. Még minden egyes új bérlő egy másik adatbázis is lefoglalta használatra csak az új bérlőhöz.

Ebben a modellben az egyes bérlők számára a teljes adatbázis elkülönítést is biztosít. Az Azure SQL Database szolgáltatás a képzettsége, hogy ez a modell egyértelmű rendelkezik.

- [Egy SQL-adatbázis több-bérlős SaaS app példa bemutatása] [ saas-dbpertenant-wingtip-app-overview-15d] -a modellel kapcsolatos további részleteket tartalmaz.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>Horizontálisan skálázott több-bérlős adatbázisok, a hibrid

![Horizontálisan skálázott több-bérlős adatbázismodell, a hibrid][sharded-multitenantdb-model-hybrid-79m]

Ez a modell több bérlő az az alkalmazás-példány van. Ez a modell több bérlő némelyike vagy mindegyike adatbázisa is rendelkezik. Ez a modell kiválóan alkalmas különböző szolgáltatásrétegeiben használt funkciókkal kínál, így az ügyfelek is kell fizetnie több Ha az érték a teljes adatbázis elkülönítési a.

A séma összes adatbázis tartalmazza a bérlő azonosítója. A bérlői azonosító még akkor is, ezeket az adatbázisokat tároló csak egy bérlő.

- [Egy SQL-adatbázis több-bérlős SaaS app példa bemutatása][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>Minden bérlőhöz modell oktatóprogramok

Minden bérlőhöz modell a részletes ismertetését lásd:

- Útmutató cikkek készlete.
- A forráskód egy Github-tárház, amely a modellben tárolva:
    - A Wingtip jegyek alkalmazás kódját.
    - A felügyeleti lehetőségeket parancsprogramkódot.

#### <a name="tutorials-for-management-scenarios"></a>Oktatóanyagok felügyeleti forgatókönyvek esetén

Az egyes útmutató cikkek terjed ki a következő kezelési forgatókönyveket:

- A bérlő kiépítésének.
- Teljesítmény figyelése és kezelése.
- Séma kezelése.
- Határokon bérlői jelentéskészítés és elemzés.
- Visszaállítás egy korábbi időpontbeli időben egy bérlő.
- Vészhelyreállítás.



## <a name="next-steps"></a>Következő lépések

- [Egy SQL-adatbázis több-bérlős SaaS app példa bemutatása] [ saas-dbpertenant-wingtip-app-overview-15d] -a modellel kapcsolatos további részleteket tartalmaz.

- [Több-bérlős SaaS adatbázis bérleti minták][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "Önálló alkalmazás modell"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "Bérlői model adatbázishoz"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "Horizontálisan skálázott több-bérlős adatbázismodell, a hibrid"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


