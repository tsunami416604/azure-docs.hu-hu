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
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902577"
---
# <a name="azure-infrastructure-availability"></a>Azure-infrastruktúra rendelkezésre állása
Az Azure biztosít hatékony rendelkezésre állása alapján kiterjedt redundancia, a virtualizálási technológia érhető el. Az Azure számos szintű ügyfelek adatok maximális rendelkezésre állást biztosít redundanciát biztosít.

## <a name="temporary-outages-and-natural-disaster"></a>Átmeneti kimaradásainak és természeti katasztrófa esetére
A Microsoft felhő-infrastruktúra és a műveleti csapat tervez, működik, létrehozásával és biztonságossá teszi a felhőalapú infrastruktúra. Ez a csapat biztosítja, hogy az Azure-infrastruktúra elkötelezett a magas rendelkezésre állás és megbízhatóság, nagy hatékonyságú, intelligens méretezhetőséget és egy biztonságos, privát és megbízható felhő.

Szünetmentes tápegységek és a nagy bankok akkumulátorok győződjön meg arról, hogy az elektromos áram egy rövid távú power bekövetkező szolgáltatáskimaradás esetén a folyamatos marad.

Vészhelyzeti generátorok biztonsági mentési power kiterjesztett leállásainak és a tervezett karbantartás adja meg. Az Adatközpont helyszíni ösztönzésének tartalék a szolgáltatást a természeti katasztrófa esetére.

Nagy sebességű és hatékony fiber száloptikás hálózatok csatlakoztatása adatközpontok más jelentős hubok és internetes felhasználók. Számítási csomópontok közelebb a gazdagép számítási feladatainak a végfelhasználók számára, a késés csökkentése érdekében, adja meg a georedundáns tárolás, és növelje a teljes szolgáltatás rugalmasság. Mérnökökből álló csapat a profilkészítésen szolgáltatások meg tartósan érhetők el ügyfeleink számára.

A Microsoft biztosítja a magas rendelkezésre állás, a fejlett monitorozási és incidensmegoldás, szolgáltatás támogatása, és készítsen biztonsági másolatot a feladatátvételi képességének. A Microsoft operations földrajzilag elosztott adatközpontok 24/7/365 működnek. Az Azure-hálózatot egyike a legnagyobb a világon. A fiber száloptikás és a tartalom terjesztési hálózati adatközpontok és a magas teljesítmény és megbízhatóság biztosításához élcsomópontok csatlakozik.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Azure tartja a vásárlói adatok tartós két helyen kellene teszi, hogy válassza ki a helykiszolgáló biztonsági mentése a helyét, az ügyféllel. Mindkét helyen az Azure folyamatosan több (3) kifogástalan replikáit vásárlói adatokat tárolja.

## <a name="database-availability"></a>Adatbázis-elérhetőségi
Az Azure biztosítja, hogy egy adatbázis egy adatbázis folyamatos rendelkezésre állású Internet-átjárón keresztül elérhető az internetről. Figyelés felméri állapotának és az aktív adatbázisok állapotát 5 perces időközönként.

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
Az Azure storage segítségével egy hatékonyan méretezhető és tartós tárolási szolgáltatás, így lehetővé téve, hogy közvetlenül egy fogyasztó alkalmazás általi érhető el kapcsolat végpontok tesz lehetővé. A storage szolgáltatáson keresztül bejövő tárolási kérelmek kerül feldolgozásra, hatékony tranzakciós integritás.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure-ban tárolt ügyféladatok védelme](azure-protection-of-customer-data.md)
