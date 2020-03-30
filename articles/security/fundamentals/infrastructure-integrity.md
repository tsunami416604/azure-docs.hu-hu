---
title: Az Azure infrastruktúra integritása
description: Ez a cikk az Azure-infrastruktúra integritásával foglalkozik.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727189"
---
# <a name="azure-infrastructure-integrity"></a>Az Azure infrastruktúra integritása

## <a name="software-installation"></a>Szoftvertelepítés
Az Azure-környezetben telepített szoftververem összes összetevője a Microsoft security development lifecycle (SDL) folyamatot követően készült egyéni. Minden szoftverösszetevő, beleértve az operációs rendszer (OS) lemezképeit és az SQL Database-t, a változáskezelési és kiadáskezelési folyamat részeként van telepítve. Az összes csomóponton futó operációs rendszer a Windows Server 2008 vagy a Windows Server 2012 testreszabott verziója. A pontos verziót a hálóvezérlő (FC) választja ki az operációs rendszer által játszani kívánt szerepnek megfelelően. Ezenkívül a gazdaoperációs rendszer nem engedélyezi a jogosulatlan szoftverösszetevők telepítését.

Egyes Azure-összetevők Azure-ügyfélként vannak telepítve egy vendég operációs rendszeren futó vendég virtuális gépen.

## <a name="virus-scans-on-builds"></a>Vírusvizsgál -ra épít
Az Azure szoftverösszetevő (beleértve az operációs rendszert is) olyan vírusvizsgálaton kell átesnie, amely az Endpoint Protection antivírus eszközt használja. Minden víruskeresés létrehoz egy naplót a társított build könyvtárban, amely részletezi a beolvasott adatokat és a vizsgálat eredményeit. A víruskeresés az Azure-on belüli összes összetevő buildforráskódjának része. A kód nem kerül át a termelésbe tiszta és sikeres víruskeresés nélkül. Ha bármilyen problémát észlel, a build lefagyott, majd a Microsoft Security biztonsági csapataihoz kerül, hogy azonosítsa, hol adta meg a "gazember" kód a buildet.

## <a name="closed-and-locked-environment"></a>Zárt és zárt környezet
Alapértelmezés szerint az Azure-infrastruktúra-csomópontok és a vendég virtuális gépek nem rendelkeznek felhasználói fiókok at. Ezenkívül az alapértelmezett Windows-rendszergazdai fiókok is le vannak tiltva. Az Azure live-támogatás rendszergazdái megfelelő hitelesítéssel bejelentkezhetnek ezekbe a gépekbe, és felügyelhetik az Azure éles hálózatát a vészhelyzeti javításokhoz.

## <a name="azure-sql-database-authentication"></a>Az Azure SQL Database hitelesítése
Az SQL Server minden implementációjához hasonlóan a felhasználói fiókok kezelését is szigorúan ellenőrizni kell. Az Azure SQL Database csak az SQL Server-hitelesítést támogatja. Az ügyfél adatbiztonsági modelljének kiegészítéseként erős jelszavakkal rendelkező és meghatározott jogokkal konfigurált felhasználói fiókokat is kell használni.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>A Microsoft vállalati hálózata és egy Azure-fürt közötti ACL-k és tűzfalak
A szolgáltatásplatform és a Microsoft vállalati hálózata közötti hozzáférés-vezérlési listák (ACL-k) és tűzfalak védik az SQL Database-példányokat a jogosulatlan belső hozzáféréssel szemben. Továbbá csak a Microsoft vállalati hálózatának IP-címtartományaiból származó felhasználók férhetnek hozzá a Windows Fabric platformfelügyeleti végpontjához.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>AcL-k és tűzfalak az SQL Database-fürt csomópontjai között
További védelemként a védelmi mélységi stratégia részeként az ACL-ek és a tűzfal egy SQL Database-fürt csomópontjai között lettek megvalósítva. A Windows Fabric platformfürtön belüli összes kommunikáció, valamint az összes futó kód megbízható.

## <a name="custom-monitoring-agents"></a>Egyéni figyelési ügynökök
Az SQL Database egyéni figyelési ügynököket (MA-kat) ( más néven figyelők) alkalmaz az SQL Database-fürt állapotának figyelésére.

## <a name="web-protocols"></a>Webes protokollok

### <a name="role-instance-monitoring-and-restart"></a>Szerepkörpéldány figyelése és újraindítása
Az Azure biztosítja, hogy az összes üzembe helyezett, futó szerepkörök (internet-alapú webes vagy háttérfeldolgozó feldolgozói szerepkörök) folyamatos állapotfigyelés i. Ha egy szerepkör állapota nem megfelelő, vagy a központi állapotú alkalmazás kritikus hibája, vagy a szerepkörpéldányon belüli mögöttes konfigurációs probléma miatt az FC észleli a problémát a szerepkörpéldányon belül, és korrekciós állapotot kezdeményez.

### <a name="compute-connectivity"></a>Számítási kapcsolat
Az Azure biztosítja, hogy az üzembe helyezett alkalmazás vagy szolgáltatás elérhető legyen a szabványos webalapú protokollokon keresztül. Az internetre néző webes szerepkörök virtuális példányai külső internetkapcsolattal rendelkeznek, és közvetlenül a webes felhasználók számára elérhetők. A feldolgozói szerepkörök által a nyilvánosan elérhető webes szerepkör virtuális példányai nevében végrehajtott műveletek érzékenységének és integritásának védelme érdekében a háttérfeldolgozói szerepkörök virtuális példányai külső internetkapcsolattal rendelkeznek, de nem lehetnek közvetlenül a külső webes felhasználók által.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)
