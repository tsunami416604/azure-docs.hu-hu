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
ms.openlocfilehash: 2a36df0e45af5bcce5338d04b7e1ba44221ae964
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="the-wingtip-tickets-saas-application"></a>A Wingtip jegyek SaaS-alkalmazáshoz

Azonos *Wingtip jegyek* alkalmazás meg van valósítva egyes három minta. Az alkalmazás az egyszerű esemény listázása és jegykezelési SaaS-alkalmazás célzó kis helyszínek - színház, treff, stb. Egyes helyszínekkel a bérlő az alkalmazást, és a saját adatok rendelkezik: helyszínére részleteit, listák események, az ügyfelek, jegy rendelések, stb.  Az alkalmazás, kezelési parancsprogramok és oktatóanyagok egy végpontok közötti Szolgáltatottszoftver-forgatókönyvek bővíthető. Ez magában foglalja a létesítési bérlők, figyeléséhez és felügyeletéhez a teljesítmény, séma kezelése, és több-bérlős jelentéskészítés és elemzés.

## <a name="three-saas-application-patterns"></a>Három SaaS-alkalmazás minták

Az alkalmazás három változatban érhetők el; az Azure SQL Database minden egyes felderíti egy másik adatbázishoz bérleti mintát.  Az első egy bérlői egyetlen alkalmazás egy elkülönített bérlői egyetlen adatbázist használ. A második bérlőnként egy adatbázist egy több-bérlős alkalmazást használ. A harmadik minta szilánkos több-bérlős adatbázisok használja egy több-bérlős alkalmazást.

![Három bérleti minták][image-three-tenancy-patterns]

 Minden egyes minta felügyeleti parancsfájlok és a oktatóprogramot kínál, amelyek számos különböző kialakítási felfedezése és a felügyeleti kombinációját használhatja a saját alkalmazás is tartalmaz.  A mintákat a kisebb telepíti, hogy öt perc.  Három lehet egymás melletti telepített, így a különbségek a tervezési és kezelése.

## <a name="standalone-application-pattern"></a>Önálló alkalmazásminta

Az önálló app mintát használ egy bérlő egyetlen alkalmazás egybérlős adatbázis az egyes bérlők számára. Mindegyik bérlő alkalmazás telepítve van egy külön Azure erőforráscsoport-csoporthoz. Ez a szolgáltató előfizetés vagy a bérlői előfizetéshez lehet, és a bérlő nevében a szolgáltató által kezelt. Ebben a mintában a legnagyobb bérlői elkülönítést is biztosít, de ez általában a legtöbb költséges, mivel nincs lehetőség az között több bérlő osztozik az erőforrásokon.

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-sa] és kódja a Githubon [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Adatbázis típusú bérlői mintában

Az adatbázis típusú bérlői mintában érvényben, amely érintett a bérlők elszigetelésére, és szeretné futtatni a központi szolgáltatása költséghatékony, a megosztott erőforrások használatát lehetővé. Egy adatbázis minden helyszínére vagy bérlő jön létre, és az adatbázisok központilag felügyelt. Adatbázisok rugalmas készletek biztosításához költséghatékony és könnyen teljesítménykezelés, amely kihasználja a bérlők előre nem látható munkaterhelés mintáinak lehet üzemeltetni. Katalógus-adatbázis tárolja a bérlők és a hozzájuk tartozó adatbázisok közötti leképezést. Ez a leképezés kezelése a shard térkép felügyeleti szolgáltatását használja a [Elastic Database ügyféloldali kódtár](sql-database-elastic-database-client-library.md), amely biztosítja, hogy az alkalmazás hatékony kapcsolatok kezeléséhez.

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-dpt] és kódja a Githubon [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Horizontálisan skálázott több-bérlős adatbázis minta

Több-bérlős adatbázisok érvényben a bérlők és az alacsonyabb bérlői elkülönítés gépházban / alacsonyabb költségek keres szolgáltatók. Ebben a mintában lehetővé teszi a bérlők nagy számú csomagolási egyetlen adatbázisba, a költség-/-bérlő vezetői le. Közel végtelen méretezési által horizontális lehetőség a bérlők több adatbázis között.  Katalógus adatbázis újra leképezi a bérlők adatbázisokhoz.  

Ebben a mintában is lehetővé teszi a hibrid modellek, amelyben egy adatbázis több bérlő költséget optimalizálás, vagy egy egyetlen bérlővel saját adatbázis elkülönítési optimalizálás. A választott végre lehessen hajtani a bérlő-bérlő alapon, vagy a bérlő az kiosztott vagy újabb, az alkalmazás érintő forgalomkiesés nélkül.

Tekintse meg a [oktatóanyagok] [ docs-tutorials-for-wingtip-mt] és kódja a Githubon [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Következő lépések

#### <a name="conceptual-descriptions"></a>Fogalmi leírása

- Annak az alkalmazás a vállalat kiszolgálása részletes ismertetése megtalálható [több-bérlős Szolgáltatottszoftver-adatbázis bérleti minták][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Oktatóanyagok és kód

- Önálló alkalmazás:
    - [Az önálló app oktatóanyagok][docs-tutorials-for-wingtip-sa].
    - [Az önálló, a Githubon Code][github-code-for-wingtip-sa].

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

