---
title: Az Azure-infrastruktúra elérhetősége – Azure-biztonság
description: Ez a cikk arról nyújt tájékoztatást, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme és az ügyfelek adatainak maximális rendelkezésre állása érdekében.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727218"
---
# <a name="azure-infrastructure-availability"></a>Az Azure-infrastruktúra rendelkezésre állása
Ez a cikk arról nyújt tájékoztatást, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme és az ügyfelek adatainak maximális rendelkezésre állása érdekében. Az Azure robusztus rendelkezésre állást biztosít a virtualizációs technológiával elért kiterjedt redundancia alapján.

## <a name="temporary-outages-and-natural-disaster"></a>Átmeneti leállások és természeti katasztrófák
A Microsoft Cloud Infrastructure and Operations csapata megtervezi, felépíti, működteti és javítja a felhőalapú infrastruktúra biztonságát. Ez a csapat biztosítja, hogy az Azure-infrastruktúra magas rendelkezésre állást és megbízhatóságot, nagy hatékonyságot és intelligens méretezhetőséget biztosítson. A csapat biztonságosabb, privátabb és megbízhatóbb felhőt biztosít.

Szünetmentes tápegységek és hatalmas bankok az elemek biztosítják, hogy a villamos energia folyamatos marad, ha egy rövid távú áramkimaradás lép fel. A vészhelyzeti generátorok tartalék áramellátást biztosítanak a hosszabb üzemszünetek és a tervezett karbantartás érdekében. Természeti katasztrófa esetén az adatközpont helyszíni üzemanyag-tartalékokat használhat.

A nagy sebességű és robusztus optikai hálózatok összekapcsolják az adatközpontokat más nagy központokkal és internetezőkkel. Számítási csomópontok a felhasználókhoz közelebb idomulnak a késés csökkentéséhez, a georedundancia biztosításához és az általános szolgáltatásrugalmasság növeléséhez. A mérnökök csapata éjjel-nappal azon dolgozik, hogy a szolgáltatások tartósan elérhetők legyenek.

A Microsoft magas rendelkezésre állást biztosít a fejlett figyelési és incidens-reagálás, a szolgáltatástámogatás és a biztonsági mentési feladatátvételi képesség révén. A földrajzilag elosztott Microsoft műveleti központok a 24/7/365-öt üzemeltetik. Az Azure-hálózat az egyik legnagyobb a világon. Az optikai és tartalomterjesztési hálózat adatközpontokat és peremhálózati csomópontokat köt össze a nagy teljesítmény és megbízhatóság biztosítása érdekében.

## <a name="disaster-recovery"></a>Vészhelyreállítás
Az Azure két helyen is megőrzi az adatokat. Kiválaszthatja a biztonsági mentési hely helyét. Mindkét helyen az Azure folyamatosan három kifogástalan állapotú replikát tart fenn az adatokról.

## <a name="database-availability"></a>Adatbázis elérhetősége
Az Azure biztosítja, hogy az adatbázis elérhető egy internetes átjárón keresztül, tartós adatbázis-rendelkezésre állással. A figyelés ötperces időközönként értékeli az aktív adatbázisok állapotát és állapotát.

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
Az Azure nagy mértékben méretezhető és tartós tárolási szolgáltatáson keresztül biztosít tárhelyet, amely kapcsolódási végpontokat biztosít. Ez azt jelenti, hogy egy alkalmazás közvetlenül hozzáférhet a tárolási szolgáltatáshoz. A tárolási szolgáltatás hatékonyan, tranzakciós integritással dolgozza fel a bejövő tárolási kérelmeket.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)
