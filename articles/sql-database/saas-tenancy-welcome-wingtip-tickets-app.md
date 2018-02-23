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
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: 3f1a8bf6a0f05308f643f24dd4db7400c49b9e14
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="the-wingtip-tickets-saas-application"></a>A Wingtip jegyek SaaS-alkalmazáshoz

Azonos *Wingtip jegyek* SaaS-alkalmazás meg van valósítva egyes három minta. Az alkalmazás az egyszerű esemény listázása és jegykezelési SaaS-alkalmazás célzó kis helyszínek - színház, treff, stb. Egyes helyszínekkel a bérlő az alkalmazást, és a saját adatok rendelkezik: helyszínére részleteit, listák események, az ügyfelek, jegy rendelések, stb.  Az alkalmazás, kezelési parancsprogramok és oktatóanyagok egy végpontok közötti Szolgáltatottszoftver-forgatókönyvek bővíthető. Ez magában foglalja a létesítési bérlők, figyeléséhez és felügyeletéhez a teljesítmény, séma kezelése, és több-bérlős jelentéskészítés és elemzés.

## <a name="three-saas-application-and-tenancy-patterns"></a>Három SaaS alkalmazás és a vállalat kiszolgálása minták

Az alkalmazás három változatban érhetők el; az Azure SQL Database minden egyes felderíti egy másik adatbázishoz bérleti mintát.  Az első bérlőnként egy önálló alkalmazás, a saját adatbázis használ. A második bérlőnként egy adatbázist egy több-bérlős alkalmazást használ. A harmadik minta szilánkos több-bérlős adatbázisok használja egy több-bérlős alkalmazást.

![Három bérleti minták][image-three-tenancy-patterns]

 A mintákat a alkalmazáskód, valamint a felügyeleti parancsfájlok és a oktatóprogramot kínál, amelyek számos különböző kialakítási és felügyeleti minták felfedezése tartalmazza.  A mintákat a kisebb telepíti, hogy öt perc.  Három lehet egymás melletti telepített, így a különbségek a tervezési és kezelése.

## <a name="standalone-application-per-tenant-pattern"></a>Bérlői minta egy önálló alkalmazás

Az önálló app típusú bérlői mintában egy egybérlős alkalmazást használó adatbázisokban az egyes bérlők számára. Mindegyik bérlő alkalmazás, az adatbázisát, beleértve az önálló Azure erőforráscsoport üzembe van helyezve. Az erőforráscsoport üzembe helyezve a szolgáltató előfizetés vagy a bérlői előfizetéshez, és a bérlői nevében a szolgáltató által kezelt. Az önálló app típusú bérlői mintában a legnagyobb bérlői elkülönítést is biztosít, de általában a legtöbb költséges, mivel nincs lehetőség megosztott erőforrások közötti több bérlő.  Ebben a mintában, előfordulhat, hogy bonyolult és bérlők kisebb számú telepített alkalmazások megfelelőek.  Az önálló verziója telepítéseinek, az alkalmazás testre szabható, az egyes bérlők számára egyszerűbb, mint a más kombinációját.  

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-sa] és kódja a Githubon [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Adatbázis típusú bérlői mintában

Az adatbázis típusú bérlői mintában érvényben, amely érintett a bérlők elszigetelésére, és szeretné futtatni a központi szolgáltatása költséghatékony, a megosztott erőforrások használatát lehetővé. Egy adatbázis minden helyszínére vagy bérlő jön létre, és az adatbázisok központilag felügyelt. Adatbázisok rugalmas készletek biztosításához költséghatékony és könnyen teljesítménykezelés, amely kihasználja a bérlők előre nem látható munkaterhelés mintáinak lehet üzemeltetni. Katalógus-adatbázis tárolja a bérlők és a hozzájuk tartozó adatbázisok közötti leképezést. Ez a leképezés kezelése a shard térkép felügyeleti szolgáltatását használja a [Elastic Database ügyféloldali kódtár](sql-database-elastic-database-client-library.md), amely biztosítja, hogy az alkalmazás hatékony kapcsolatok kezeléséhez.

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-dpt] és kódja a Githubon [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Horizontálisan skálázott több-bérlős adatbázis minta

Több-bérlős adatbázisok érvényben a bérlők és az alacsonyabb bérlői elkülönítés gépházban / alacsonyabb költségek keres szolgáltatók. Ebben a mintában lehetővé teszi a bérlők nagy számú csomagolási egyetlen adatbázisba, a költség-/-bérlő vezetői le. Közel végtelen méretezési által horizontális lehetőség a bérlők több adatbázis között. Katalógus-adatbázis bérlők adatbázisok rendeli hozzá.  

Ebben a mintában is lehetővé teszi, hogy egy *hibrid* modell, amelyben egy adatbázis több bérlő költséget optimalizálás, vagy egy egyetlen bérlővel saját adatbázis elkülönítési optimalizálás. A választott végre lehessen hajtani a bérlő-bérlő alapon, vagy a bérlő az kiosztott vagy újabb, az alkalmazás érintő forgalomkiesés nélkül.  Ez a modell hatékonyan használható, ha a bérlő csoportok eltérően kell kezelni. Például alacsony költségű bérlők rendelhet megosztott adatbázisok, amíg prémium bérlők saját adatbázisok lehet hozzárendelni. 

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-mt] és kódja a Githubon [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>További lépések

#### <a name="conceptual-descriptions"></a>Fogalmi leírása

- Annak az alkalmazás a vállalat kiszolgálása részletes ismertetése megtalálható [több-bérlős Szolgáltatottszoftver-adatbázis bérleti minták][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Oktatóanyagok és kód

- Önálló alkalmazás bérlőnként:
    - [Az önálló app oktatóanyagok ] [ docs-tutorials-for-wingtip-sa].
    - [Önálló alkalmazás, a Githubon Code][github-code-for-wingtip-sa].

- Adatbázis bérlőnként:
    - [Bérlőnként adatbázis oktatóanyagok][docs-tutorials-for-wingtip-dpt].
    - [A Githubon bérlőnként adatbázis kódját][github-code-for-wingtip-dpt].

- Horizontálisan skálázott több-bérlős:
    - [Horizontálisan skálázott több-bérlős oktatóanyag][docs-tutorials-for-wingtip-mt].
    - [Horizontálisan skálázott több-bérlős a Githubon kódját][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Három bérleti kombinációját."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

