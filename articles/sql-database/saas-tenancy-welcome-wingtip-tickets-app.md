---
title: Üdvözli a Wingtips alkalmazás
description: Ismerje meg az adatbázis-bérlői modelleket, és a minta Wingtips SaaS-alkalmazást, az Azure SQL Database-t a felhőalapú környezetben.
keywords: sql database-oktatóanyag
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 4e0b3afe51ac7c7a6b9213fcee79af57cbbd8197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818316"
---
# <a name="the-wingtip-tickets-saas-application"></a>A Wingtip Jegyek SaaS alkalmazás

Ugyanaz a *Wingtip Jegyek* SaaS-alkalmazás mindhárom mintában be van valósítva. Az alkalmazás egy egyszerű esemény lista és jegykiadás SaaS app célzó kis helyszínek - színházak, klubok, stb Minden helyszín az alkalmazás bérlője, és saját adatokkal rendelkezik: helyszín adatok, események listája, ügyfelek, jegyrendelések stb.  Az alkalmazás a felügyeleti parancsfájlokkal és oktatóanyagokkal együtt egy teljes körű SaaS-forgatókönyvet mutat be. Ez magában foglalja a bérlők kiépítése, a teljesítmény figyelése és kezelése, séma kezelése, valamint a bérlők közötti jelentéskészítés és elemzés.

## <a name="three-saas-application-and-tenancy-patterns"></a>Három SaaS alkalmazás és bérleti minták

Az alkalmazás három verziója érhető el; mindegyik egy másik adatbázis-bérleti mintát fedez fel az Azure SQL Database-ben.  Az első bérlőnként egy önálló alkalmazást használ saját adatbázissal. A második egy több-bérlős alkalmazást használ bérlőnként egy adatbázissal. A harmadik minta egy több-bérlős alkalmazást használ a szilánkos több-bérlős adatbázisok.

![Három bérleti minták][image-three-tenancy-patterns]

 Minden minta tartalmazza az alkalmazás kódját, valamint a felügyeleti parancsfájlok és oktatóanyagok, amelyek számos tervezési és felügyeleti minták feltárása.  Minden minta kevesebb, mint öt perc alatt üzembe helyeződik.  Mindhárom telepíthető egymás mellett, így össze lehet hasonlítani a tervezési és felügyeleti különbségeket.

## <a name="standalone-application-per-tenant-pattern"></a>Önálló alkalmazás bérlői mintánként

Az önálló alkalmazás bérlői mintánként egyetlen bérlői alkalmazást használ, amely minden bérlő adatbázisával van elválasztva. Minden bérlői alkalmazás, beleértve az adatbázist, egy külön Azure-erőforráscsoportba van telepítve. Az erőforráscsoport telepíthető a szolgáltató előfizetésében vagy a bérlő előfizetésében, és a szolgáltató a bérlő nevében kezelhető. Az önálló alkalmazás bérlői minta biztosítja a legnagyobb bérlői elkülönítést, de általában a legdrágább, mivel nincs lehetőség erőforrások megosztására több bérlő között.  Ez a minta kiválóan alkalmas olyan alkalmazásokhoz, amelyek összetettebbek lehetnek, és amelyek kisebb számú bérlőre vannak telepítve.  Az önálló központi telepítések, az alkalmazás testreszabható minden bérlő könnyebben, mint más minták.  

Nézze meg az [oktatóanyagokat][docs-tutorials-for-wingtip-sa] és a kódot a GitHubon [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Adatbázis bérlői mintánként

Az adatbázis bérlői minta hatékony a bérlők elkülönítése, és szeretné futtatni a központi szolgáltatás, amely lehetővé teszi a megosztott erőforrások költséghatékony használatát. Minden helyszínhez vagy bérlőhöz létrejön egy adatbázis, és az összes adatbázis központilag van kezelve. Az adatbázisok rugalmas készletekben üzemeltethetők, hogy költséghatékony és egyszerű teljesítménykezelést biztosítsanak, amely kihasználja a bérlők előre nem látható munkaterhelési mintáit. A katalógus-adatbázis tartalmazza a bérlők és azok adatbázisai közötti leképezést. Ezt a leképezést a rugalmas [adatbázis-ügyfélkódtár](sql-database-elastic-database-client-library.md)shard térképfelügyeleti szolgáltatásaival kezeli, amely hatékony kapcsolatkezelést biztosít az alkalmazáshoz.

Nézze meg az [oktatóanyagokat][docs-tutorials-for-wingtip-dpt] és a kódot a GitHubon [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Szilánkos több-bérlős adatbázis-minta

A több-bérlős adatbázisok hatékonyak a bérlőkalacsonyabb bérlőnkénti költségére kereső szolgáltatók számára, és a bérlők csökkentett elkülönítése esetén. Ez a minta lehetővé teszi a bérlők nagy számának csomagolását egy egyedi adatbázisba, így a bérlőnkénti költség csökken. Közel végtelen méretű lehetséges horizontálisan a bérlők több adatbázis között horizontálisan. A katalógus-adatbázis leképezi a bérlők et az adatbázisokhoz.  

Ez a minta is lehetővé teszi a *hibrid* modell, amelyben optimalizálhatja a költségek több bérlő egy adatbázisban, vagy optimalizálja az elkülönítés egyetlen bérlővel a saját adatbázisban. A választás bérlőről-bérlőre történik, akár a bérlő kiépítésekor, akár később, az alkalmazásra gyakorolt hatás nélkül.  Ez a modell hatékonyan használható, ha a bérlők csoportjait eltérően kell kezelni. Például az alacsony költségű bérlők hozzárendelhetők a megosztott adatbázisokhoz, míg a prémium szintű bérlők a saját adatbázisaikhoz rendelhetők hozzá. 

Nézze meg az [oktatóanyagokat][docs-tutorials-for-wingtip-mt] és a kódot a GitHubon [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>További lépések

#### <a name="conceptual-descriptions"></a>Koncepcionális leírások

- Az alkalmazás bérleti minták részletesebb magyarázata érhető el a [több-bérlős SaaS-adatbázis bérleti minták][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Oktatóanyagok és kód

- Önálló alkalmazás bérlőnként:
    - [Oktatóanyagok önálló app][docs-tutorials-for-wingtip-sa].
    - [Az önálló alkalmazás kódja a GitHubon.][github-code-for-wingtip-sa]

- Adatbázis bérlőnként:
    - [Oktatóanyagok adatbázis bérlőnként][docs-tutorials-for-wingtip-dpt].
    - [Adatbázis-kód bérlőnként, a GitHubon.][github-code-for-wingtip-dpt]

- Szilánkos több-bérlős:
    - [Oktatóanyagok a szilánkos több-bérlős][docs-tutorials-for-wingtip-mt].
    - [A sharded több-bérlős kód, a GitHubon.][github-code-for-wingtip-mt]



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Három bérleti minta."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

