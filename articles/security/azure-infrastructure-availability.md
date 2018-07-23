---
title: Azure-infrastruktúra rendelkezésre állása
description: A cikk ismerteti az ügyfelek adataival a legnagyobb rendelkezésre állást biztosít a redundancia szintjét.
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
ms.openlocfilehash: 3679361522105d1f3c55eae6b1f33c1c6baa1d55
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171493"
---
# <a name="azure-infrastructure-availability"></a>Azure-infrastruktúra rendelkezésre állása
Az Azure robusztus rendelkezésre állás, a virtualizálási technológia érhető el a széles körű redundancia alapján biztosít. Az Azure számos szintű ügyfelek adatok maximális rendelkezésre állást biztosít redundanciát biztosít.

## <a name="temporary-outages-and-natural-disaster"></a>Átmeneti kimaradásainak és természeti katasztrófa esetére
A Microsoft felhő-infrastruktúra és a műveleti csapat tervez, épít, működik, és javítja a felhőalapú infrastruktúra biztonságát. Ez a csapat biztosítja, hogy az Azure-infrastruktúra elkötelezett a magas rendelkezésre állás és megbízhatóság, nagy hatékonyságú és intelligens méretezhetőség. A csapat biztosít egy biztonságos, privát és megbízható felhő.

Szünetmentes tápegységek és a nagy bankok akkumulátorok győződjön meg arról, hogy az elektromos áram egy rövid távú power bekövetkező szolgáltatáskimaradás esetén a folyamatos marad. Vészhelyzeti generátorok biztonsági mentési power kiterjesztett leállásainak és a tervezett karbantartás adja meg. Ha természeti katasztrófa történik, az Adatközpont helyszíni ösztönzésének tartalék használja.

Nagy sebességű és hatékony fiber száloptikás hálózatok adatközpontok össze más jelentős hubok és internetes felhasználók. Számítási csomópontok közelebb a gazdagép számítási feladatainak a felhasználók számára a késés csökkentése érdekében, adja meg a georedundáns tárolás, és növelje a teljes szolgáltatás rugalmasság. Mérnökökből álló csapat a profilkészítésen tartósan érhetők el szolgáltatások biztosításához.

A Microsoft speciális monitorozás és a incidensek, a szolgáltatás támogatása és a biztonsági mentési feladatátvételi képességének magas rendelkezésre állást biztosítja. A Microsoft operations földrajzilag elosztott adatközpontok 24/7/365 működnek. Az Azure-hálózatot egyike a legnagyobb a világon. A fiber száloptikás és a tartalom terjesztési hálózathoz kapcsolódik a magas teljesítmény és megbízhatóság biztosításához adatközpontok és a peremhálózati csomópont.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Azure megőrzi az adatok tartós két helyen. Kiválaszthatja, hogy a helykiszolgáló biztonsági mentése a helyét. Mindkét helyen az Azure folyamatosan fenntartja az adatok három kifogástalan állapotú replika.

## <a name="database-availability"></a>Adatbázis-elérhetőségi
Az Azure biztosítja, hogy egy adatbázis egy adatbázis folyamatos rendelkezésre állású internet-átjárón keresztül elérhető az internetről. Figyelés felméri állapotának és az aktív adatbázisok állapotának öt perces időközönként.

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
Az Azure storage segítségével hatékonyan méretezhető és tartós tárolási szolgáltatás, amely kapcsolati végpontok biztosít tesz lehetővé. Ez azt jelenti, hogy egy alkalmazás közvetlenül hozzáférhet a storage szolgáltatás. A storage szolgáltatás hatékonyan, tranzakciós integritási dolgozza fel a bejövő tárolási kérelmek.

## <a name="next-steps"></a>További lépések
A Microsoft nem biztonságossá tétele az Azure-infrastruktúra kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)
