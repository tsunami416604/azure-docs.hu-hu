---
title: Üdvözli a wingtips alkalmazás – az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg az adatbázis-bérlős modelleket, és arról a minta wingtips SaaS-alkalmazás, az Azure SQL Database a felhőalapú környezetben.
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
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: a05a8ad495e33734a531405902ce34e3591bfe15
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056318"
---
# <a name="the-wingtip-tickets-saas-application"></a>A Wingtip Tickets SaaS-alkalmazás

Azonos *Wingtip Tickets* minden három minta SaaS-alkalmazás megvalósítása. Az alkalmazás az egyszerű esemény listázása és jegykiadás SaaS-alkalmazás célzó kis helyszínek - mozik, sportklubokat, stb. Minden egyes helyszín egy bérlőt az alkalmazás, és rendelkezik a saját adatai: helyszín részleteit, listák, események, ügyfelek, a jegy megrendelések, stb.  Az alkalmazást, a kezelő parancsfájlok és oktatóanyagok és bemutatja egy teljes körű SaaS-környezetben. Ez magában foglalja a kiépítési bérlők, figyeléséhez és felügyeletéhez a teljesítmény, sémakezelés, és a bérlők közötti jelentéskészítés és elemzés.

## <a name="three-saas-application-and-tenancy-patterns"></a>Három SaaS alkalmazás- és bérlős minták

Az alkalmazás három változatban érhetők el; az Azure SQL Database egy másik adatbázishoz bérlős minta egyes ismerteti.  Az első saját adatbázis bérlőnként egy önálló alkalmazás használ. A második egy több-bérlős alkalmazás bérlőnkénti adatbázis használ. A harmadik példa egy több-bérlős alkalmazást több-bérlős szilánkokra osztott adatbázisok.

![Három bérlős minták][image-three-tenancy-patterns]

 Minden egyes mintát tartalmaz, az alkalmazás kódját, és -kezelés parancsfájljai és oktatóanyagok kialakításokat és felügyeleti mintákat számos tárgyaljuk.  Minden mintához belül helyez üzembe, hogy öt perc alatt.  Mindhárom lehet egymás mellett telepíthető, így összehasonlíthatja a különbségek a tervezési és kezelése.

## <a name="standalone-application-per-tenant-pattern"></a>Önálló alkalmazás bérlői mintában

Bérlő mintában az önálló app-adatbázissal egy egybérlős alkalmazást használ az egyes bérlők számára. Minden bérlő alkalmazást, beleértve az adatbázisát, helyezünk üzembe egy külön Azure-erőforráscsoportot. Az erőforráscsoport a szolgáltató előfizetés vagy a bérlő előfizetés telepíthető és a bérlő nevében a szolgáltató felügyeli. A bérlő mintában önálló alkalmazás biztosítja a legnagyobb bérlők elkülönítését, de általában a legtöbb költséges, mivel nincs lehetőség az erőforrások több bérlő közötti megosztása nem lett.  Ez a minta kiválóan alkalmazható, amely összetettebb lehet, és a bérlők kisebb számú telepített alkalmazások számára.  Az önálló verziója telepítéseinek, az alkalmazás testre szabható az egyes bérlők számára egyszerűbb, mint a többi mintákat.  

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-sa] és a kódját a Githubon [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Bérlő mintában adatbázis

Az adatbázis bérlői mintában szolgáltatók által érintett a bérlők elkülönítését és szeretne futtatni egy központosított szolgáltatásba, amely lehetővé teszi a megosztott erőforrások költséghatékony használata esetén. Egy adatbázis jön létre minden egyes helyszín, vagy a bérlő számára, és központilag kezelt összes adatbázishoz. A rugalmas készletek az biztosítanak költséghatékony és egyszerűen alkalmazásteljesítmény-felügyelet, amely kihasználja a kiszámíthatatlan számítási feladat mintázatait, a bérlők adatbázisok lehet üzemeltetni. A katalógus-adatbázisban tárolja a bérlők és az adatbázisok közötti leképezéseket. Ez a leképezés a a szegmensek felügyeleti funkcióinak használatával kezeli a [Elastic Database-Ügyfélkódtár](sql-database-elastic-database-client-library.md), amely biztosítja a hatékony kapcsolat kezelése az alkalmazáshoz.

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-dpt] és a kódját a Githubon [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Több-bérlős szilánkokra osztott adatbázis minta

Több-bérlős adatbázisok az olyan hatékony szolgáltatók,-bérlőt és -rendben csökkentett bérlők elkülönítését az alacsonyabb költség keres. Ez a minta lehetővé teszi a bérlők nagy számú csomagolási egy önálló adatbázis, a költség-bérlőnkénti vezetési le azokat. Majdnem végtelen skálázási lehetőség szerint horizontális skálázási a bérlők több adatbázis között. A katalógus-adatbázis bérlői adatbázisok rendeli hozzá.  

Ez a minta lehetővé teszi egy *hibrid* modell, amelyben optimalizálás költsége az adatbázisban több bérlő számára, vagy optimalizálás elkülönített, a saját adatbázis létrehozása egyetlen bérlő számára. A választás lehet tenni a bérlő-bérlő alapon, vagy amikor a bérlő az üzembe helyezett vagy újabb verzió, nincs hatással az alkalmazásra.  Ez a modell hatékonyan használható, amikor a bérlők csoportjai eltérően kell kezelni kell. Például alacsony költségű bérlők rendelhető megosztott adatbázisokhoz, amíg a prémium szintű bérlők esetén hozzárendelheti a saját adatbázisok. 

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-mt] és a kódját a Githubon [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>További lépések

#### <a name="conceptual-descriptions"></a>Fogalmi leírása

- A részletes ismertetése a alkalmazásminták bérlős mindig elérhető legyen [több-bérlős SaaS-bérlős adatbázis][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Az oktatóanyagok és kód

- Önálló alkalmazás bérlőnkénti:
    - [Önálló alkalmazás oktatóanyagok ] [ docs-tutorials-for-wingtip-sa].
    - [Önálló alkalmazás, a Githubon kódját][github-code-for-wingtip-sa].

- Bérlőnkénti adatbázis:
    - [Bérlőnkénti adatbázis számára oktatóanyagokkal][docs-tutorials-for-wingtip-dpt].
    - [Kódját a Githubon bérlőnkénti adatbázis][github-code-for-wingtip-dpt].

- Szilánkokra osztott több-bérlős:
    - [Szilánkokra osztott több-bérlős oktatóanyagok][docs-tutorials-for-wingtip-mt].
    - [Szilánkokra osztott több-bérlős a Githubon kódját][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Három bérlős mintákat."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

