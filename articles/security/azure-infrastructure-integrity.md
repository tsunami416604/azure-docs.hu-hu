---
title: Az Azure infrastruktúra-integritás
description: Ez a cikk foglalkozik az Azure infrastruktúra integritása.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901316"
---
# <a name="azure-infrastructure-integrity"></a>Az Azure infrastruktúra-integritás

## <a name="software-installation"></a>Szoftvertelepítés
Minden szoftver veremben telepített összetevőinek támogatásához az Azure-környezetben a Microsoft biztonságos fejlesztési Életciklussal (SDL) folyamat a következő beépített egyéni. (Beleértve az operációsrendszer-lemezképeket és az SQL Database) összes szoftverösszetevőket vannak üzembe helyezve, a változás- és a Kiadáskezelés folyamat részeként. Az összes csomóponton futó operációs rendszer egy Windows Server 2008 vagy Windows Server 2012 testre szabott verzióját. A pontos verzió az FC tájékoztatnia az operációs rendszer lejátszása a kijelölt szerepkörnek megfelelően tudja kiválasztani. Emellett a gazda operációs rendszer nem engedélyezi a nem engedélyezett szoftverek összetevők telepítése.

Bizonyos Microsoft Azure-összetevők (például az RDFE, fejlesztői portált, stb.) Azure-ügyfelek a Vendég virtuális Gépen futó vendég operációs rendszer üzemelnek.

## <a name="virus-scans-on-builds"></a>A buildek vírus vizsgálatok
Az Azure-szoftverek (beleértve az operációs rendszer) összetevő buildek kell a Microsoft Endpoint Protection (MEP) víruskereső eszközzel víruskeresést. Minden egyes víruskeresés során hoz létre a napló a társított build könyvtárban, részletesen ismertetve a mi megvizsgált és a vizsgálat eredményeit. A víruskeresés során minden Azure-ban összetevő build forráskódja részét képezi. Kód nem kerül az éles környezetben anélkül, hogy a vizsgálat sikeres és a tiszta vírus kellene. Feljegyzett merül fel, ha a build szüneteltetve van, és ezután halad át a biztonsági csoportok belül a Microsoft Security azonosításához, ahol a "engedélytelen" kódot beírni, a build.

## <a name="closedlocked-environment"></a>A környezet lezárt/zárolva van
Alapértelmezés szerint az Azure infrastruktúra-csomópontok és a Vendég virtuális gépek nem rendelkeznek a számukra létrehozott összes felhasználói fiókot. Emellett alapértelmezett Windows rendszergazdai fiókok szintén le lesznek tiltva. A rendszergazdák a Microsoft Azure Live támogatási (WALS) is – a megfelelő hitelesítés – jelentkezzen be ezek a gépek és felügyelheti az Azure éles hálózati környezetben a költséghatékonyságát.

## <a name="microsoft-azure-sql-database-authentication"></a>A Microsoft Azure SQL Database hitelesítése
Az SQL Server minden olyan végrehajtása, a felhasználói fiók felügyeleti kell szorosan vezérelni. A Microsoft Azure SQL Database csak az SQL Server-hitelesítést támogatja. Felhasználói fiókok erős jelszavakat és az adott jogokkal, valamint a vásárlói adatok biztonsági modell kiegészíteni használandó.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Tűzfal/ACL-ek MSFT vállalati hálózat és a Microsoft Azure-fürt
Hozzáférés-vezérlési listák, illetve a tűzfal a Service platformot és MS vállalati hálózat között a Microsoft Azure SQL Database insider jogosulatlan hozzáférés elleni védelmének. Csak az IP-címtartományok Microsoft CorpNet a felhasználók további, a WinFabric platform felügyeleti végpont érhető el.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Tűzfal/ACL-ek egy Azure SQL Database-fürtben található csomópontok között
Egy további védelmet, a defense a mélység-stratégia részeként, mint ACL-EK, illetve a tűzfal végrehajtották a Microsoft Azure SQL Database-fürtben található csomópontok között. A WinFabric platform fürtön belüli összes kommunikációt, valamint az összes futó kód a megbízható.

## <a name="custom-mas-watchdogs"></a>Egyéni MAs (Watchdogs)
A Microsoft Azure SQL Database a Microsoft Azure SQL Database-fürt állapotának figyeléséhez watchdogs nevű egyéni MAs alkalmaz.

## <a name="web-protocols"></a>Webes protokollok

### <a name="role-instance-monitoring-and-restart"></a>Szerepkör-példányok figyelése és újraindítás
Az Azure biztosítja az összes futó szerepkörök (Internet felé néző webes vagy feldolgozói szerepkörök háttér-feldolgozási) üzembe helyezett vonatkoznak rá a tartós állapot figyelése annak érdekében, hogy azok hatékony és hatékonyan továbbítása a szolgáltatások, ahol azok már került kiosztásra a tartomány. Abban az esetben, ha egy szerepkör sérült, vagy egy kritikus fontosságú tartalék folyamatban van az alapul szolgáló vagy tárolt konfigurációs probléma belül a szerepkörpéldány maga az alkalmazás által, a Microsoft Azure FC lesz a szerepkörpéldány belül a probléma észlelése, és a egy javítási állapota .

### <a name="compute-connectivity"></a>COMPUTE-kapcsolat
Az Azure biztosítja, hogy a telepített alkalmazások vagy szolgáltatások szabványos webes protokoll keresztül érhető el. Az Internet felé néző webes szerepkör virtuális példányt külső elérhetőséget az Internet, és közvetlenül a webes felhasználók számára elérhető lesz. Háttér-feldolgozási feldolgozói szerepkör virtuális példányt külső internetkapcsolattal rendelkezik, de nem érhető el közvetlenül egy külső webes felhasználói tartalmi és feldolgozói szerepkörök nevében hajtsa végre a műveleteket sértetlenségének védelme érdekében a nyilvánosan elérhető webes szerepkör virtuális példányt.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure-ban tárolt ügyféladatok védelme](azure-protection-of-customer-data.md)
