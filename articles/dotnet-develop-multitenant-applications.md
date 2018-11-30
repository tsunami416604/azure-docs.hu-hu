---
title: Webalkalmazás több-Bérlős Alkalmazásminta |} A Microsoft Docs
description: Architekturális áttekintéseket és tervezési mintákat, amelyek bemutatják, hogyan valósíthat meg egy több-bérlős webalkalmazás az Azure-ban található.
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
ms.openlocfilehash: 342c7903e58a5c3bc41278152630187fa0c63b7b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425040"
---
# <a name="multitenant-applications-in-azure"></a>Több-bérlős alkalmazásokban az Azure-ban
Egy több-bérlős alkalmazásban, amely lehetővé teszi a különböző felhasználók vagy "bérlőkkel", az alkalmazás megtekintéséhez, mintha az volt a saját megosztott erőforrás. Jellemzően olyan helyzetben, amely egy több-bérlős alkalmazás adatmodelljeinek egyike, amelyben az alkalmazás minden felhasználójának Kezdésként testreszabhatják a felhasználói élményt, de egyébként külön ugyanazon alapvető üzleti követelmények. Nagy több-bérlős alkalmazások olyan Office 365, az Outlook.com-os és a visualstudio.com webhelyen.

Egy alkalmazás szolgáltatója szempontból a több-bérlős módhoz előnyeinek többnyire vonatkoznak működési és költséghatékonyan növelhető a hatékonyság. Számos bérlők vagy ügyfelek igényeit, így a rendszer összevonása felügyeleti feladatokat, például a figyelés, teljesítményhangolás, szoftverkarbantartást és biztonsági mentések megfelel az alkalmazás egy verzióját.

A következők a legfontosabb célok és a egy szolgáltató szempontjából követelmények listáját tartalmazza.

* **Kiépítés**: meg kell tudni az alkalmazást az új bérlők kiépítése.  Több-bérlős alkalmazások bérlők nagy számú Ez a folyamat automatizálására önkiszolgáló kiépítés engedélyezésével általában szükség.
* **Karbantarthatóság**: kell frissíteni az alkalmazást, és egyéb karbantartási feladatok végrehajtása, amíg több bérlő használja azt.
* **Figyelés**: az alkalmazás mindig az esetleges problémák felismeréséhez és elhárításához nyomon kell lennie. Ez magában foglalja, minden bérlő hogyan használja az alkalmazás figyelését.

Egy megfelelően kivitelezett több-bérlős alkalmazásban a következő előnyöket biztosítja a felhasználók számára.

* **Elkülönítés**: az egyes bérlők tevékenységének nincsenek hatással a többi bérlő által az alkalmazás használatát. Bérlők egymás adataihoz nem fér hozzá. Megjelenik a bérlőhöz, mintha rendelkeznek kizárólagos használja az alkalmazást.
* **Rendelkezésre állási**: az egyes bérlői szeretnének-e az alkalmazás folyamatosan elérhető legyen, esetleg a garanciákkal meghatározott SLA-t. Más bérlők tevékenységének ismét nem érinti az alkalmazás rendelkezésre állását.
* **Méretezhetőség**: az alkalmazás az egyes bérlők igény szerint méretezhető. A jelenléti és a többi bérlő műveletek nem érinti az alkalmazás teljesítményét.
* **Költségek**: költségek alacsonyabbak, mint egy dedikált, egybérlős alkalmazás fut, mert a több-bérlős lehetővé teszi, hogy az erőforrás-megosztás.
* **Testreszabhatóság**. Az alkalmazás különböző módokon, például hozzáadása vagy a szolgáltatások eltávolítása, szín és emblémák módosítása vagy akár a saját kódot vagy szkriptet hozzáadása egy adott bérlő testre szabhatók.

Röviden közben számos szempontot kell figyelembe venniük nagy mértékben skálázható szolgáltatás, számos is a célokat és a követelmények, több-bérlős alkalmazások számos közös. Releváns bizonyos forgatókönyvek esetén is, és az egyes célokat és követelményeket fontossága eltérőek lehetnek az egyes esetekben. A több-bérlős alkalmazás szolgáltatója ki is célokat és követelményeket, mint például felel meg a bérlők célok és követelmények, jövedelmezőség, Számlázás, több szolgáltatási szintek, kiépítés, Karbantarthatóság figyelése és automation.

Egy több-bérlős alkalmazás további kialakítási szempontokkal kapcsolatban további információkért lásd: [üzemeltetése az Azure-ban több-Bérlős alkalmazás][Hosting a Multi-Tenant Application on Azure]. A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Az Azure számos funkciót, amelyek lehetővé teszik, hogy a kulcs problémákra a több-bérlős rendszerek tervezése során észlelt biztosít.

**Elkülönítés**

* Állomásfejléc és SSL-kommunikáció anélkül szegmens webhely bérlőket
* Lekérdezési paraméterek szegmens webhely bérlőket
* A webes feldolgozói szerepkörök szolgáltatásokat
  * Feldolgozói szerepkör. amely általában a háttérkiszolgálón egy alkalmazás adatokat feldolgozni.
  * Webes szerepkörök általában alkalmazásokhoz az előtér-kiszolgálóként működő.

**Tárolás**

Például az Azure SQL Database vagy az Azure Storage szolgáltatások, például a Table service, amely szolgáltatásokat kínál a nagy mennyiségű strukturálatlan adat tárolására és a Blob szolgáltatás, amelynek nagy mennyiségű strukturálatlan szöveges vagy bináris tárolására szolgáltatást biztosítja az adatok kezelése adatok, például video-, hang- és képfájlokat.

* A megfelelő SQL-adatbázis biztonságossá tétele több-Bérlős adatok bérlőnként felügyelt SQL Server-bejelentkezésekről.
* Azure-beli táblák az alkalmazás erőforrások megadásával a tároló szintű hozzáférési szabályzat használatával állíthatja be az engedélyek nélkül adja ki az új URL-CÍMEK a közös hozzáférésű jogosultságkódok használata védett erőforrásokhoz.
* Alkalmazás-erőforrások Azure-üzenetsorok az Azure-üzenetsorok gyakran használják a bérlők nevében meghajtó feldolgozásra, de a kiépítés vagy felügyeleti szükséges munkát egymás között is használható.
* Service Bus-üzenetsorok, hogy az alkalmazás-erőforrások használhatók, ha egy megosztott egy szolgáltatást, használja egyetlen üzenetsorhoz ahol minden egyes bérlő küldő csak engedélyezett (mivel az ACS-ből kiadott jogcímek származtatva) nyomni az üzenetsornak, csak a fogadók a szolgáltatástól Az üzenetsorból lekérheti az adatokat, egyszerre több bérlőtől érkező engedély.

**Kapcsolati és biztonsági szolgáltatások**

* Az Azure Service Bus olyan üzenetküldési infrastruktúra, amely az alkalmazások működve az alkalmazásközi üzenetváltást lazán kötődő módon a és a rugalmasság.

**Hálózati szolgáltatások**

Az Azure számos olyan hálózati szolgáltatás, amely támogatja a hitelesítést, és javíthatja az alkalmazások kezelhetőségi biztosít. Ezek a szolgáltatások a következők:

* Az Azure virtuális hálózat lehetővé teszi, üzembe helyezése és kezelése a virtuális magánhálózatok (VPN) az Azure-ban, valamint biztonságos módon összekapcsolhatók a helyszíni informatikai infrastruktúrát.
* Virtuális hálózat a Traffic Manager lehetővé teszi a bejövő adatforgalom elosztását több üzemeltetett Azure-szolgáltatást, ugyanabban az adatközpontban vagy a világ különböző pontjain üzemelő.
* Az Azure Active Directory (Azure AD) szolgáltatás modern REST-alapú szolgáltatás, amely identitás és hozzáférés-vezérlési funkciókat biztosít a felhőbeli alkalmazásokhoz. Az Azure AD alkalmazás-erőforrások az Azure ad-ben, miközben lehetővé teszi a hitelesítési és engedélyezési, a kód kívül megosztani funkcióját a webes alkalmazások és szolgáltatások eléréséhez és felhasználók hitelesítésére egyszerű megoldást kínál.
* Az Azure Service Bus egy biztonságos üzenetküldést biztosít, és elosztott adatok folyamat funkció és hibrid alkalmazások, például az Azure közötti kommunikáció üzemeltetett alkalmazások és a helyszíni alkalmazások és szolgáltatások, anélkül, hogy bonyolult tűzfal- és biztonsági infrastruktúra. Service Bus Relay használatával az alkalmazás-erőforrások a szolgáltatásokat, amelyek ki vannak téve végpontként előfordulhat, hogy a bérlő (például kívül a rendszer, például a helyszínen üzemeltetett) tartozik, vagy lehetnek (mivel kifejezetten a bérlő számára kiosztott szolgáltatásokhoz adat-és nagybetűket, a bérlő-specifikus halad át őket).

**Erőforrások kiépítése**

Az Azure új bérlők kiépítése biztosít számos módon az alkalmazáshoz. Több-bérlős alkalmazások bérlők nagy számú Ez a folyamat automatizálására önkiszolgáló kiépítés engedélyezésével általában szükség.

* Feldolgozói szerepkörök lehetővé teszi a kiépítés és bérlőnként megszüntetni hozzárendeléseket erőforrásokat (például amikor egy új bérlőt regisztrál vagy megszakítása), a szoftverhasználat-mérő használatát és kezelését a méretezési csoport meghatározott ütemezés vagy a teljesítmény küszöbértékeit átlépésével válaszul begyűjtése mutatók. Ez a szerepkör is használható frissítések leküldenie és a megoldáshoz.
* Azure-Blobok számítási épít ki, vagy előre inicializálva tárolási erőforrások védelme érdekében a számítási művelet során gondoskodik a tároló hozzáférési házirendek az új bérlők számára szolgáltatás Virtuálismerevlemez-képek és más erőforrások, a csomagok.
* Az SQL Database-erőforrások kiépítése egy bérlő lehetőségek a következők:
  
  * DDL-szkriptekkel vagy beágyazott erőforrásként szerelvények belül
  * Az SQL Server 2008 R2 DAC-csomagok üzembe helyezett programozott módon.
  * A fő referencia-adatbázis másolása
  * Adatbázis importálása és exportálása használatával egy új adatbázisok létrehozásához.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
