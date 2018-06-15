---
title: Több-Bérlős webes Alkalmazásminta |} Microsoft Docs
description: Az architektúra áttekintése és -kialakítási minta, azt ismertetik, hogyan megvalósításához egy több-bérlős webalkalmazást az Azure-on található.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: 57ba0e46139bda2d74c9f7db0ffab2f2122b0df2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850713"
---
# <a name="multitenant-applications-in-azure"></a>Több-bérlős alkalmazásokhoz az Azure-ban
Egy több-bérlős alkalmazás, amely lehetővé teszi a különböző felhasználók vagy "bérlők," az alkalmazás megtekintéséhez, mintha az volt a saját megosztott erőforrás. A jellemző forgatókönyv, amely egy több-bérlős alkalmazás adatmodelljeinek egyike, amelyben az alkalmazás minden felhasználó kíván a felhasználói élmény testreszabásáról, de egyébként külön az ugyanazon alapvető üzleti követelmények. A nagy több-bérlős alkalmazások többek között az Office 365, az Outlook.com-os és a visualstudio.com webhelyre.

Egy alkalmazás szolgáltató szempontjából több vállalat kiszolgálása előnyei többnyire vonatkozik költség és működési hatékonyság. Az alkalmazás egy verzióját sok bérlők vagy ügyfelek, így a rendszer összevonása felügyeleti feladatokhoz, mint a figyelést, teljesítményhangolás, szoftverkarbantartást és az adatok biztonsági képes igényeinek.

A következő a legjelentősebb célokat és követelményeket a szolgáltató szempontjából a listáját tartalmazza.

* **Kiépítés**: be kell tudnia új bérlők számára az alkalmazás telepítéséhez.  Sok bérlő több-bérlős alkalmazásokhoz fontos általában ez a folyamat automatizálására önkiszolgáló kiépítés engedélyezése.
* **Karbantartási követelmények**: be kell tudnia, frissítse az alkalmazás és más karbantartási feladatokat végez, amíg a több bérlő-k használják.
* **Figyelési**: mindig az alkalmazást, az esetleges problémák felismeréséhez és a hibakereséshez képesnek kell lennie. Ez magában foglalja, minden bérlői hogyan használja az alkalmazás figyelése.

A megfelelően megvalósított több-bérlős alkalmazás a következő előnyökkel jár a felhasználók számára.

* **Elkülönítési**: az egyes bérlők tevékenységeit nem befolyásolják a más bérlők által az alkalmazás használatát. Bérlők nem érhető el egymás adatokat. Valószínűleg a bérlő számára, hogy rendelkeznek-e az alkalmazás kizárólagos használatát.
* **Rendelkezésre állási**: az egyes bérlők szeretné, hogy az alkalmazás folyamatosan elérhető legyen, esetleg a szolgáltatásiszint-szerződésben garantált definiált garanciát. Ebben az esetben a tevékenységeket a többi bérlő nem érinti az alkalmazás rendelkezésre állását.
* **Méretezhetőség**: az alkalmazás méretezi az egyes bérlők igény kielégítésére. A jelenléti és a műveletek a többi bérlő nem érintik az alkalmazás teljesítményét.
* **Költségek**: költségek alacsonyabbak dedikált, egyetlen-bérlő alkalmazást futtat, mert a több-bérlős lehetővé teszi, hogy az erőforrás-megosztás.
* **Testreszabhatóság miatt**. Lehetővé teszi az alkalmazás különböző módokon, például fel szolgáltatásokat, színek és az emblémát módosításával, vagy még a saját kód vagy parancsfájl hozzáadása egy adott bérlő testreszabásához.

Röviden hogy közben számos szempontot, figyelembe kell venni egy kiválóan méretezhető kiszolgálása, számos is a célok és számos több-bérlős alkalmazás vonatkozó követelmények. Néhány nem lehet megfelelő, az adott forgatókönyveket, és egyéni célokat és követelményeket fontosságát eltérőek, mindkét esetben. A több-bérlős alkalmazás-szolgáltatóként ki is célokat és követelményeket, többek között a bérlők célok és követelmények, nyereségességével, számlázási, több szolgáltatási szintek, kiépítés, karbantartási követelmények figyelése és automatizálási értekezlet.

A több-bérlős alkalmazás további kialakítási szempontokkal kapcsolatban további információkért lásd: [egy több-Bérlős alkalmazást az Azure-on][Hosting a Multi-Tenant Application on Azure]. A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure számos olyan szolgáltatásokat nyújt, engedélyezi, hogy a kulcs a több-bérlős rendszerek tervezése során észlelt problémákat.

**Elkülönítés**

* Szegmens webhely bérlők által állomásfejléc vagy anélkül SSL-kommunikáció
* Szegmens webhely bérlők által lekérdezés-paraméterek
* A feldolgozói szerepkörök webszolgáltatások
  * Feldolgozói szerepköröket. amely általában a háttérkiszolgáló az alkalmazás adatokat feldolgozó.
  * Általában működjön, és az alkalmazások előtérbeli webes szerepkörök.

**Tárolás**

Például az Azure SQL Database vagy az Azure Storage szolgáltatások, például a Table szolgáltatás, ami nagy mennyiségű strukturálatlan adatok tárolási szolgáltatásokat és a Blob szolgáltatás, amely nagy mennyiségű strukturálatlan szöveges vagy bináris tárolására szolgáltatásokat nyújt adatok kezelése adatok, például a video-, hang- és lemezképek.

* Az SQL-adatbázis megfelelő biztonságossá tétele több-Bérlős adatok / bérlői SQL Server bejelentkezési azonosítók.
* Azure-táblák az alkalmazás erőforrások megadásával a tároló szintű hozzáférési házirend használatával állíthatja be az engedélyeket anélkül, hogy ki az új URL-Címeket a megosztott hozzáférési aláírásokkal védett erőforrásokhoz.
* Alkalmazás-erőforrások Azure várólisták Azure várólisták gyakran használják a bérlők nevében meghajtó feldolgozásra, de a kiépítés vagy felügyeleti szükséges munka terjesztését is felhasználhatók.
* Service Bus-üzenetsorok leküldéses értesítések alkalmazás-erőforrásokkal működik egy megosztott egy szolgáltatást, használja egy adott sorba ahol mindegyik bérlő küldő csak jogosult (mivel az ACS-től kiadott jogcímeket származó) nyomni a várólistára, a szolgáltatás csak a fogadók a több bérlő érkező adatokat az üzenetsorból lekéréses engedéllyel.

**Kapcsolati és biztonsági szolgáltatások**

* Az Azure Service Bus, egy üzenetkezelési infrastruktúra, amely az alkalmazások engedélyezi azok az exchange-üzenetek a lazán összekapcsolt megoldást, hogy a javított méretezési és rugalmassági között.

**Hálózati szolgáltatások**

Azure, amely támogatja a hitelesítést, és javíthatja a kezelhetőségi az alkalmazások több hálózati szolgáltatásokat biztosít. Ezek a szolgáltatások a következők:

* Az Azure virtuális hálózat megadható, hogy Ön kiépítése és virtuális magánhálózatok (VPN) kezelése az Azure-ban, valamint biztonságos hivatkozás ezen a helyszíni informatikai infrastruktúrát.
* Virtuális hálózati Traffic Manager betöltése oszthatja el a bejövő forgalmat több üzemeltetett az Azure szolgáltatásban, hogy azok futtatja ugyanabban az adatközpontban, vagy a világ különböző üzemeltetésében teszi lehetővé.
* Azure Active Directory (Azure AD) szolgáltatás modern REST-alapú szolgáltatás, amely identitás kezelése és hozzáférés-vezérlés képességeinek biztosít a felhőalapú alkalmazásokhoz. Az Azure AD az Azure AD-be és felhasználók hitelesítésére ahhoz, hogy hozzáférjenek a webes alkalmazások és szolgáltatások miközben lehetővé teszi a hitelesítési és engedélyezési kívül a kódot kell figyelembe venni a funkciók egyszerű lehetőséget biztosít az alkalmazás-erőforrásokat.
* Az Azure Service Bus egy biztonságos üzenetküldést biztosít és adatok folyamata funkció elosztott és hibrid alkalmazások, például az Azure közötti kommunikáció üzemeltetett alkalmazások és a helyszíni alkalmazásokhoz és szolgáltatásokhoz, anélkül, hogy bonyolult tűzfal- és biztonsági infrastruktúra. A bérlő (például kívül a rendszer, például a helyszínen üzemeltetett) alkalmazás-erőforrásokat a Service Bus Relay használatával végpontként ki vannak téve a szolgáltatások tartozhat, és lehetnek (mivel kimondottan a bérlő kiépített szolgáltatások -és nagybetűket, a bérlő-specifikus adatok áthaladó őket).

**Erőforrások kiépítése**

Azure számos módon rendelkezés új bérlők számára az alkalmazás tartalmazza. Sok bérlő több-bérlős alkalmazásokhoz fontos általában ez a folyamat automatizálására önkiszolgáló kiépítés engedélyezése.

* Feldolgozói szerepkörök lehetővé teszi a kiépítés és deaktiválás rendelkezés bérlőnként erőforrások (például amikor egy új bérlőt jelentkezik be, vagy megszakítja a), gyűjtéséhez mérési használja, és bizonyos ütemezés kezelésére, vagy a teljesítmény küszöbértékeit metsző válaszul mutatók. Ez ugyanarra a szerepkörre is felhasználhatók leküldéses frissítéseit és a frissítések a megoldáshoz.
* Azure-blobokat számítási létrehozásához használt, vagy már inicializálva tárolási erőforrások védelme érdekében a számítási ugyanakkor biztosítható a tároló hozzáférési házirendek az új bérlők számára szolgáltatás csomagokat, a virtuális merevlemez képek és egyéb erőforrásokhoz.
* SQL adatbázis-erőforrások egy bérlő kialakítási lehetőségek a következők:
  
  * A parancsfájlok DDL vagy beágyazott erőforrásként szerelvények belül
  * SQL Server 2008 R2 DAC-csomagokat telepített programozott módon.
  * A fő referencia-adatbázis másolása
  * Használja az adatbázis importálási és exportálási fájlból új adatbázisok létrehozásához.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
