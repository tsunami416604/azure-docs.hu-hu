---
title: Az Azure infrastruktúra-integritás
description: Ez a cikk foglalkozik az Azure-infrastruktúra integritása.
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
ms.openlocfilehash: 65fe541f61389a2e52033cdaedcfcec4944faf35
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171459"
---
# <a name="azure-infrastructure-integrity"></a>Az Azure infrastruktúra-integritás

## <a name="software-installation"></a>Szoftvertelepítés
Minden szoftver veremben telepített összetevőinek támogatásához az Azure-környezetben a Microsoft biztonságos fejlesztési Életciklussal (SDL) folyamat a következő beépített egyéni. A szoftver összetevők többek között az operációs rendszer (OS) lemezképeket és az SQL Database változáskezelési részeként telepített, és a kibocsátási folyamat. Az összes csomóponton futó operációs rendszer egy Windows Server 2008 vagy Windows Server 2012 testre szabott verzióját. A pontos verzió van kiválasztani a hálóvezérlő (FC) tájékoztatnia az operációs rendszer lejátszása a kijelölt szerepkörnek megfelelően. Emellett a gazda operációs rendszer nem engedélyezi a nem engedélyezett szoftverek összetevők telepítése.

Egyes Azure-összetevőket üzemelnek Azure-ügyfelek egy Vendég virtuális gép futó vendég operációs rendszer.

## <a name="virus-scans-on-builds"></a>A buildek vírus vizsgálatok
Azure szoftvert (beleértve az operációs rendszer) összetevő buildek kell alávetni, amelyek az Endpoint Protection víruskereső eszköz víruskeresést. Minden egyes víruskeresés során létrehoz egy naplófájlt belül a társított build címtárat, milyen megvizsgált részletező és a vizsgálat eredményeit. A víruskeresés során minden Azure-ban összetevő build forráskódja részét képezi. Kód nem került éles vizsgálata sikeres és a tiszta vírus nélkül. Ha problémákat jeleztük, a build lefagytak, és a biztonsági csoportok belül a Microsoft Security azonosításához, ahol a "engedélytelen" kódot beírni, a build halad.

## <a name="closed-and-locked-environment"></a>A lezárt és a zárolt környezet
Alapértelmezés szerint az Azure infrastruktúra-csomópontok és a Vendég virtuális gépek nem rendelkeznek felhasználói fiókokat létrehozni. Emellett alapértelmezett Windows rendszergazdai fiókok szintén le lesznek tiltva. Az élő Azure-támogatási rendszergazdák is, a megfelelő hitelesítés, jelentkezzen be ezek a gépek és felügyelheti az Azure éles hálózati környezetben a költséghatékonyságát.

## <a name="azure-sql-database-authentication"></a>Az Azure SQL Database-hitelesítés
Az SQL Server minden olyan végrehajtása, a felhasználói fiók felügyeleti kell szorosan vezérelni. Az Azure SQL Database csak az SQL Server-hitelesítést támogatja. A vásárlói adatok biztonsági modell kiegészíteni, felhasználói erős jelszóval rendelkező fiókokat és az adott jogokat is használható.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACL-EK és tűzfalak között a Microsoft vállalati hálózat és a egy Azure-fürtön
Hozzáférés-vezérlési listák (ACL), és tűzfalak a service platformot és a Microsoft vállalati hálózat között insider jogosulatlan hozzáférés elleni védelmének SQL Database-példány. Csak a Microsoft vállalati hálózat IP-címtartományok a felhasználók további, a Windows Fabric platform-felügyeleti végpont érhető el.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Hozzáférés-vezérlési listák és a egy SQL Database-fürtben található csomópontok közötti tűzfalak
Egy további védelmet, a defense a mélység-stratégia részeként, mint ACL-EK és a egy tűzfal történtek egy SQL Database-fürtben található csomópontok között. Minden kommunikáció a Windows Fabric-platform fürtben, valamint az összes futó kód a megbízható.

## <a name="custom-monitoring-agents"></a>Egyéni monitorozási ügynökök
Az SQL Database alkalmaz egyéni monitorozási ügynökök (MAs), más néven watchdogs, az SQL Database-fürt állapotának monitorozásához.

## <a name="web-protocols"></a>Webes protokollok

### <a name="role-instance-monitoring-and-restart"></a>Szerepkör-példányok figyelése és újraindítás
Az Azure biztosítja, hogy minden üzembe helyezett, futó szerepkörök (internet felé néző webes vagy feldolgozói szerepkörök háttér-feldolgozási) annak érdekében, hogy azok és gazdaságosabb azon szolgáltatások biztosításához, amelynek, hogy került kiosztásra a tartomány folyamatos állapotfigyelési vonatkozik. A szerepkör akkor kerül sérült, az alkalmazásban, a üzemeltetési kritikus hibát vagy egy alapul szolgáló konfigurációs probléma a szerepkörpéldány magán belül, ha az FC észleli a szerepkörpéldány belül a problémát, és elindítja a javítási állapot.

### <a name="compute-connectivity"></a>COMPUTE-kapcsolat
Az Azure biztosítja, hogy az üzembe helyezett alkalmazás vagy szolgáltatás szabványos webes protokoll keresztül érhető el. Az internet felé néző webes szerepkörök virtuális példánya külső internetkapcsolattal rendelkezik, és érhetők el közvetlenül a webes felhasználók. Tartalmi és feldolgozói szerepkörök a nyilvánosan elérhető webkiszolgáló virtuális szerepkörpéldányok nevében hajtsa végre a műveleteket sértetlenségének védelme érdekében a virtuális példányt, a háttérbeli feldolgozás feldolgozói szerepkörök külső internetkapcsolattal rendelkezik, de nem lehet közvetlenül a felhasználók a külső webes elérni.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)
