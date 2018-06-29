---
title: Az Azure éles műveletek és kezelése
description: Ez a cikk az Azure éles hálózati környezetben működésének és a felügyeleti általános leírását tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102330"
---
# <a name="azure-production-operations-and-management"></a>Az Azure éles műveletek és kezelése    
Felügyeleti és az Azure éles hálózati környezetben működésének egy Azure műveletek csoportjai és az Azure SQL Database a koordinált szükséges. Több rendszer- és teljesítményfigyelő eszközök alkalmaz a környezetben. Hálózati eszközök, kiszolgálók, szolgáltatások és alkalmazások folyamatok figyelhetők a megfelelő eszközökkel.

A figyelés, naplózási és jelentéskészítési valósíthatók meg annak a Microsoft Azure környezetben futó szolgáltatások biztonságos végrehajtása érdekében több szintjéről többek között a következő műveleteket:

- A Microsoft Azure figyelési ügynök (MA), beleértve az FC és a gyökér operációs rendszer számos helyről összegyűjti a megfigyelési és diagnosztikai információkat, és írja azt a naplófájlokba. Az előre konfigurált Azure Storage-fiókot az információk emésztett részhalmazának végül kimenő. Emellett a megfigyelési és diagnosztikai szolgáltatás (MDS) olyan szabadon álló szolgáltatás, amely különböző megfigyelési és diagnosztikai naplófájl adatokat olvas, és a tudnivalókat foglalja össze. Az MDS egy integrált naplófájlba írja az adatokat. Azure az Azure felügyeleti rendszer bővítménye, vagyis az egyedi Azure biztonsági figyelése (ASM), használja. Figyelje meg, elemzése és a különböző pontokról a platform biztonsági kapcsolódó események jelentés összetevőket tartalmaz.
- A Microsoft Azure SQL adatbázis WinFabric platform felügyeleti, telepítési, fejlesztési, és működési felügyeletet szolgáltatásokat biztosít a Microsoft Azure SQL-adatbázis. Elosztott, több lépés telepítési, állapotfigyelés, automatikus javítási és szolgáltatás verziója megfelelőségi kínál. A következő szolgáltatásokat tartalmazza:

   - Szolgáltatás modellezési képességekkel valósághű fejlesztői környezetet (datacenter fürtök költséges és szűkös).
   - Egy kattintással telepítési és frissítési munkafolyamatok service rendszerindítási és karbantartási.
   - Jelentéskészítés a automatizált javítás munkafolyamatok self-healing engedélyezése állapotát.
   - Valós idejű figyelés, riasztás és hibakeresés létesítményekben elosztott rendszer csomópontjai között.
   - Működési adatok és az elosztott alapvető metrikák központi gyűjteménye elemzést, a szolgáltatás betekintést miatt.
   - Üzembe helyezés esetén tooling eszköz működési módosítások kezelése és figyelése.
   - A Microsoft Azure SQL adatbázis WinFabric platform és figyelő parancsfájlok folyamatos futtatás, és valós idejű figyelése.

Bármely rendellenességeket fordulhat elő, ha az Incidensmegoldási folyamat az Azure incidens osztályozás csapat követ aktiválva van. A megfelelő Azure technikai támogatási csoporthoz válaszolni az incidens értesítést kap. Probléma- és névfeloldási dokumentált, és a központosított hibajegyalapú rendszert felügyelni. Rendszer hasznos üzemidő mérőszámok alapján a nem adathozzáférést szerződés (titoktartási) és a kérés esetén érhetők el.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Vállalati hálózat és a többtényezős hozzáférés üzemi környezetben
A vállalati hálózat felhasználói bázis magában foglalja a Microsoft Azure technikai támogatási csoporthoz. A vállalati hálózat belső vállalati funkciókat támogatja, és tartoznak a belső alkalmazások, amelyeket az Azure ügyfélszolgálatához. A vállalati hálózat logikailag és fizikailag elkülönül az Azure éles hálózati környezetben. Microsoft Azure személyzet hozzáférhetnek a vállalati hálózathoz, a Microsoft Azure munkaállomásokat és laptopok. Az összes felhasználónak rendelkeznie kell Active Directory (AD) adatait, beleértve a felhasználónevet és jelszót, a vállalati hálózati erőforrások eléréséhez. CorpNet hozzáférést AD-fiókokat, amelyek minden Microsoft személyzete, alvállalkozói, szállítók kibocsátott, és kezeli az MSIT használja. Egyedi felhasználói azonosítók megkülönböztetéséhez a Microsoftnál azok foglalkoztatottsági állapot alapján csoporthoz.

Belső Azure alkalmazásokhoz való hozzáférés szabályozását hitelesítéssel az Active Directory összevonási szolgáltatások (ADFS). Az AD FS egy olyan szolgáltatás, amely a CorpNet felhasználók biztonságos token és a felhasználói jogcímek alkalmazása keresztül hitelesítést nyújt MSIT üzemelteti. Az AD FS lehetővé teszi, hogy a belső Microsoft Azure-alkalmazások a Microsoft vállalati AD-tartomány alapján a felhasználók hitelesítéséhez. A vállalati hálózat környezetből a termelési hálózat eléréséhez a felhasználónak hitelesítenie kell magát a multi-factor authentication használatával.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)
