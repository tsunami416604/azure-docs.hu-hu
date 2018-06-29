---
title: Azure-infrastruktúra integritása
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 18d7fab30c0bbaa5292fe5d3003b7f2309b34d3b
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102317"
---
# <a name="integrity-of-azure-infrastructure"></a>Azure-infrastruktúra integritása   

## <a name="software-installation"></a>Szoftvertelepítés
A szoftververem minden olyan összetevő, az Azure környezetben telepített egyéni kialakítású követően a Microsoft biztonsági fejlesztési életciklus (SDL) folyamat. A változás- és a Kiadáskezelés folyamat részeként telepített összes szoftver összetevőkben (például az operációs rendszer lemezképeket és az SQL-adatbázis). Az összes csomóponton futó operációs rendszer Windows Server 2008 vagy Windows Server 2012 testreszabott verziója telepítve. A szerepkör az operációs rendszer számára, hogy a kívánt megfelelően FC tudja kiválasztani a pontos verziót. Emellett a gazda operációs Rendszerével telepítés nem folytatható jogosulatlan szoftverek összetevők.

Néhány Microsoft Azure-összetevők (például az RDFE, fejlesztői portálján, stb.) a Vendég virtuális Gépen futó vendég operációs rendszer Azure ügyfelek üzemelnek.

## <a name="virus-scans-on-builds"></a>A vírusvédelmi vizsgálatok buildek
Az Azure szoftver (beleértve az operációs rendszer) összetevő buildek kell végrehajtania a Microsoft Endpoint Protection (MEP) víruskereső eszközzel víruskeresést. Minden egyes víruskeresést hoz létre a napló a kapcsolódó összeállítási könyvtárban, és részletesen leírja, milyen megvizsgált és a vizsgálat eredményeit. A víruskeresés Azure-ban minden összetevő build forráskódja részét képezi. Kód nem kerül üzemi környezetben anélkül, hogy egy tiszta és sikeres vírus vizsgálata. Ha probléma merül fel az áttelepítés előtt feljegyzett, a build zárolva van, és ezután halad át a Microsoft Security azonosítására, ahol a "engedélytelen" kódot beírni, a build belül biztonsági csoportokkal.

## <a name="closedlocked-environment"></a>Lezárt/zárolt környezet
Alapértelmezés szerint az Azure infrastruktúra-kiszolgálók és a Vendég virtuális gépek nincs rajtuk létrehozott összes felhasználói fiókot. Emellett alapértelmezett Windows rendszergazdai fiókok is letiltja. A rendszergazdák a Microsoft Azure élő támogatási (WALS) is – a megfelelő hitelesítés – jelentkezzen be ezek a gépek és az Azure üzemi hálózat a sürgős javításához.

## <a name="microsoft-azure-sql-database-authentication"></a>A Microsoft Azure SQL adatbázis-hitelesítés
Egyetlen megvalósítása sem az SQL Server, a felhasználói fiókok kezelése kell szigorú ellenőrzés. A Microsoft Azure SQL Database csak az SQL Server-hitelesítést támogatja. Felhasználói az erős jelszóval rendelkező fiókokat, és adott konfigurált jogosultságok kell használni, valamint a felhasználói adatok biztonsági modell biztosítja.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Tűzfal/hozzáférés-vezérlési listák MSFT vállalati hálózat és a Microsoft Azure-fürt között
A szolgáltatás Platform és MS vállalati hálózat között hozzáférés-vezérlési listák/tűzfal jogosulatlan belső hozzáférés elleni védelmének Microsoft Azure SQL Database. További csak az IP-címtartományok Microsoft CorpNet a felhasználók férhetnek hozzá a WinFabric platform felügyeleti végpontja.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Tűzfal/hozzáférés-vezérlési listákat az Azure SQL-adatbázis a fürt csomópontjai között
Egy további védelmet, a védelmet a mélység-stratégia részeként, mint a hozzáférés-vezérlési listák/tűzfal valósul egy Microsoft Azure SQL-adatbázis a fürt csomópontjai között. A WinFabric platform fürtben található összes kommunikáció, valamint az összes futó kód megbízható.

## <a name="custom-mas-watchdogs"></a>Egyéni MAs (Watchdogs)
A Microsoft Azure SQL Database watchdogs nevű egyéni MAs a Microsoft Azure SQL-adatbázis a fürt állapotának figyelésére alkalmazza.

## <a name="web-protocols"></a>Webes protokollok

### <a name="role-instance-monitoring-and-restart"></a>Szerepkör példány figyelése és újraindítása
Azure biztosítja az összes futó szerepkörök (Internet felé néző webes vagy feldolgozói szerepköröket háttér-feldolgozási) telepített függvényében, hogy azok hatékony monitoring, és hatékonyan továbbítása a szolgáltatások, ahol azok már kiépítve tartós állapotát. Abban az esetben, ha a szerepkör nem kifogástalan, vagy kritikus hiba az alkalmazás maga a szerepkörpéldányt belül üzemeltetett vagy az alapul szolgáló konfigurációs probléma alatt, a Microsoft Azure FC belül a szerepkörpéldányt probléma azonosítja, és javítási állapot kezdeményezése .

### <a name="compute-connectivity"></a>Számítási kapcsolat
Azure biztosítja, hogy a telepített alkalmazás/kiszolgáló web-alapú szabványos protokollok segítségével elérhető. Az Internet felé néző webes szerepkör virtuális példány külső internetkapcsolattal kell, és közvetlenül a felhasználók által elérhető lesz. Háttér-feldolgozási szerepkör virtuális feldolgozópéldányok külső internetkapcsolattal rendelkezik, de nem érhetők el közvetlenül egy külső webes felhasználói védelme az érzékenységi és integritásának a műveletek a feldolgozói szerepkörök hajtsa végre a következő nevében: a nyilvánosan elérhető webkiszolgáló virtuális szerepkörpéldányokat.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)
