---
title: Azure-infrastruktúra rendelkezésre állása
description: A cikk szintű redundancia ügyfelek adatok maximális rendelkezésre állás biztosításához.
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
ms.openlocfilehash: db13e4835e483b4738074a71861737c4851d8dbc
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102306"
---
# <a name="availability-of-azure-infrastructure"></a>Azure-infrastruktúra rendelkezésre állása
Azure megoldással robusztus érhető el, a virtualizálási technológia nagy mennyiségű redundancia alapján. Azure számos szintű redundancia számára elérhető maximális ügyfelek adatokat biztosít.

## <a name="temporary-outages-and-natural-disaster"></a>Átmeneti kimaradásainak és természeti katasztrófa
A Microsoft felhőalapú infrastruktúra, valamint műveletek team tervez, alkot, működik, és titkosítja a felhőalapú infrastruktúra. A csapat biztosítja, hogy az Azure-infrastruktúra olyan, magas rendelkezésre állás és a megbízhatóság, nagy hatékonyságú, intelligens méretezhetőséget és biztonságos, a személyes és a megbízható felhő.

Szünetmentes áramforrások és hatalmas bankokhoz akkumulátorok győződjön meg arról, hogy elektromosság kialakulása továbbra is folyamatos, ha a rövid távú power szüneteltetése következik be.

Vészhelyzeti generátorokat biztosít a hosszabb kimaradások és tervezett karbantartást. A datacenter üzemeltetik a helyszínen üzemanyag tartalék természeti katasztrófa esetén.

Gyors és hatékony fiber optikai hálózatok csatlakozás adatközpontok más fő hubs és a felhasználók. A számítási csomópontok gazdagép munkaterhelések közelebb a késés csökkentése érdekében a végfelhasználók számára, földrajzi redundanciát, és növelje a teljes szolgáltatás a rugalmasságot. Egy egész szakemberei éjjel működik, győződjön meg arról, szolgáltatások tartósan érhetők el az ügyfél számára.

A Microsoft a speciális figyelésére és az incidensekre adott reakciók, a szolgáltatás támogatása, és készítsen biztonsági másolatot a feladatátvételi képességének magas rendelkezésre állást biztosítja. Földrajzilag elosztott Microsoft műveletek központok 24/7/365 fog működni. Az Azure-hálózatot egyike a legnagyobb a világon. A részszálas optikai és a tartalom terjesztési hálózati adatközpontok és a peremhálózati csomópontokat a magas teljesítmény és megbízhatóság érdekében csatlakozik.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Azure tartja ügyféladatok tartósságát két külön helyen a hely a biztonsági mentési hely kiválasztásához képes ügyféllel. Mindkét helyen Azure folyamatosan több (3) kifogástalan replikáit ügyféladatok tárolja.

## <a name="database-availability"></a>Adatbázis rendelkezésre állása
Azure biztosítja, hogy egy adatbázis Internet egy internetes átjáró a tartós adatbázis rendelkezésre állását keresztül érhető el. Figyelési értékelésére állapotát és az aktív adatbázisok állapotát 5 perces időközönként.

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
Azure storage rugalmasan méretezhető, tartós storage szolgáltatást, így lehetővé téve a fogyasztó alkalmazás általi közvetlenül érhető el kapcsolat végpontok nyújt. A tárolási szolgáltatásán keresztül bejövő tárolási kérelmeket dolgoz fel hatékonyan tranzakciós integritási.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)
