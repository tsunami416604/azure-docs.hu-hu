---
title: Az Azure Advisor magas rendelkezésre állás – javaslatok |} A Microsoft Docs
description: Az Azure Advisor használata az Azure-környezetek, magas rendelkezésre állás javítása érdekében.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: ea8d8d0adbc7cf5a8dfb6e9af51257b9d2ba8db2
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264082"
---
# <a name="advisor-high-availability-recommendations"></a>Az Advisor magas rendelkezésre állás – javaslatok

Az Azure Advisor segítségével győződjön meg arról, és javíthatja az üzleti szempontból kritikus fontosságú alkalmazások folytonosságát. Megjelenik a magas rendelkezésre állás – javaslatok az Advisor által a **magas rendelkezésre állású** az Advisor irányítópult lapon.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Virtuális gép hibatűrő képesség biztosítása

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor azonosítja a virtuális gépeket, amelyek nem részei rendelkezésre állási csoport, és javasolja áthelyezése egy rendelkezésre állási csoportba. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e az Azure-beli virtuálisgép-SLA. Kiválaszthatja, hogy hozzon létre egy rendelkezésre állási csoportot a virtuális gép vagy a virtuális gép hozzáadása egy meglévő rendelkezésre állási csoporthoz.

> [!NOTE]
> Ha egy rendelkezésre állási csoport létrehozása, hozzá kell adnia legalább egy virtuális gépet bele. Azt javasoljuk, hogy szolgáltatáskimaradások alkalmával, csoport, győződjön meg arról, hogy legalább egy gép beállítása két vagy több virtuális gépet egy rendelkezésre állási érhető el.

## <a name="ensure-availability-set-fault-tolerance"></a>Győződjön meg, hogy a rendelkezésre állási csoport hibatűrése 

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor egy egyetlen virtuális gépet tartalmazó rendelkezésre állási csoportok azonosítja, és javasolja, hogy egy vagy több virtuális gép hozzáadása. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e az Azure-beli virtuálisgép-SLA. Kiválaszthatja a virtuális gép létrehozása vagy meglévő virtuális gép hozzáadása a rendelkezésre állási csoporthoz.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Application gateway hibatűrő képesség biztosítása
Az az üzletmenet folytonosságának biztosítása az alapvető fontosságú alkalmazások, amelyek az application Gateway átjárók által, az Advisor azonosítja az application gateway-példány hibatűrése nem konfigurált, és azt sugallja, hogy elvégezhető javítási műveleteket. Az Advisor azonosítja a közepes vagy nagy egy példányban – az application Gateway átjárók, és legalább egy további példányok hozzáadása javasolja. Egyetlen vagy több instance kis application Gateway átjárókon azonosítja és a közepes vagy nagy SKU-ba való migrálás javasolja. Ezeket a műveleteket, ellenőrizze, hogy az application gateway-példány megfelelnek a jelenlegi SLA ezekhez az erőforrásokhoz javasol.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>A teljesítmény és a virtuálisgép-lemezek megbízhatóságának javítása

Az Advisor azonosít, a standard szintű lemezek virtuális gépekhez, és javasolja, hogy prémium szintű lemezek telepítse.
 
Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokat futtató virtuális gépek tesz lehetővé. Premium storage-fiókok használó virtuálisgép-lemezek tartós állapotú meghajtókhoz (SSD-kkel) adatokat tárolja. Az alkalmazás a legjobb teljesítmény érdekében javasoljuk, hogy a virtuális gép lemezei a premium storage magas iops-t igénylő telepíti át. 

A lemezek nem igényelnek magas iops értéket, ha standard szintű tárolóban megőrzése őket korlátozhatja költségeket. Standard szintű tárolás virtuálisgép-lemez adatokat (merevlemezes HDD) meghajtók helyett SSD-k tárolja. Kiválaszthatja, hogy a virtuális gép lemezeinek migrálása a prémium szintű lemezek. A legtöbb virtuális gépek azon Termékváltozatai támogatja a prémium szintű lemezeket. Azonban bizonyos esetekben ha azt szeretné használni a prémium szintű lemezek, előfordulhat, hogy frissíteni szeretne a virtuális gépek azon Termékváltozatai is.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>A virtuális gép adatainak véletlen törlés elleni védelem

Virtuális gépek biztonsági mentésének beállításához az üzleti szempontból kritikus fontosságú adatok rendelkezésre állását biztosítja, és véletlen törlés és adatsérülések elleni védelmet nyújt.  Az Advisor azonosítja a virtuális gépek, ahol a biztonsági mentés nem engedélyezett, és javasolja a biztonsági mentés engedélyezése. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ellenőrizze, hogy szakértői Azure-felhőben való hozzáférést, ha szükséges

Amikor egy üzleti szempontból kritikus fontosságú számítási feladatot futtat, fontos a technikai támogatáshoz, amikor szükség van. Az Advisor azonosítja a potenciális üzleti szempontból kritikus fontosságú előfizetéseket, amelyek nem rendelkeznek a támogatási csomagot a benne foglalt terméktámogatással, és javasolja, hogy olyan beállítás, amely tartalmazza a technikai támogatási telepítse.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Értesítés az Önt érintő problémái az Azure az Azure Service Health-riasztások létrehozása

Azt javasoljuk, hogy az Azure Service Health-riasztások beállítása értesítés az Azure-szolgáltatási problémák hatása a felhasználókra. [Az Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely során egy Azure-szolgáltatási probléma által érintett, személyre szabott útmutatást és támogatást nyújt. Az Advisor azonosítja, amelyekhez nincs riasztás konfigurálva az előfizetések, és javasolja, hogy hozzon létre egyet.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Rugalmasság a Traffic Manager-végpontok konfigurálása

Több végpont a TRAFFIC Manager-profilok élmény magasabb rendelkezésre állás, ha bármely megadott végpont nem sikerül. További különböző régiókban lévő végpontok elhelyezése javítja a szolgáltatás megbízhatóságát. Az Advisor azonosítja a Traffic Manager-profilok, ahol csak egy végpont van és javasolni a felhasználóknak legalább egy további végpont hozzáadása egy másik régióban.

Ha minden végpont a Traffic Manager-profil, amely konfigurálva van a közelségi útválasztás ugyanabban a régióban, a felhasználók el más régiókból tapasztalhatnak csatlakozási késések fordulhatnak elő. Hozzáadásával vagy a végpont áthelyezése egy másik régióba teljesítményjavítás általános, és adja meg a jobb rendelkezésre állás, ha egy adott régióban szereplő összes végpont sikertelen. Az Advisor azonosítja a Traffic Manager-profilok közelségi útválasztás, ahol az összes végpont ugyanabban a régióban vannak konfigurálva, és javasolja a hozzáadásával vagy a végpont áthelyezése egy másik Azure-régióban.

Ha a Traffic Manager-profil földrajzi útválasztásra van konfigurálva, majd adatforgalmat végpontokra alapján meghatározott régióban. Ha meghibásodik egy régióban, nem nincs előre definiált feladatátvétel. Kellene egy végpontot, ahol van konfigurálva a regionális csoportosítás "Az összes (globális)" eldobott forgalom elkerülése érdekében, és szolgáltatás rendelkezésre állásának javítása. Az Advisor azonosítja a Traffic Manager-profilok konfigurált földrajzi útválasztásának, ahol nincs úgy konfigurálva, hogy a regionális csoportosítás rendelkezik, mint az "Összes (globális)" végpont, és javasolja, hogy a konfigurációs módosítás elvégzése.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Döntés az SQL data warehouse-tábla lekérdezési teljesítmény érdekében az adatok eltávolítása

Adatok torzulása szükségtelen adatok mozgását vagy az erőforrás szűk keresztmetszeteket okozhat, a számítási feladatok futtatásakor. Az Advisor észleli terjesztési adatok tevékenységdiagramon nagyobb, mint 15 %, és javasoljuk, hogy az adatok újraterjesztése, és nyissa meg újra az a tábla terjesztési kulcs beállításokat. További információk azonosítása és torzulása eltávolításával kapcsolatban lásd: [hibaelhárítási torzulása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Az SQL data warehouse-tábla lekérdezési teljesítmény érdekében az elavult tábla statisztikák létrehozása vagy frissítése

Az Advisor azonosítja, amelyek nem rendelkeznek naprakész táblák [táblastatisztikák](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) és táblastatisztikák javasolja létrehozása vagy frissítése. Az SQL data warehouse-optimalizáló naprakész ukazatelé segítségével megbecsülheti a számosság vagy a lekérdezés eredményét, amely lehetővé teszi a lekérdezésoptimalizáló hozhat létre kiváló minőségű lekérdezésterv a leggyorsabb teljesítmény sorainak lekérdezés.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Magas rendelkezésre állás – javaslatok az Advisor elérése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **magas rendelkezésre állású** fülre.

## <a name="next-steps"></a>További lépések

Advisor-javaslatok kapcsolatos további információkért lásd:
* [Bevezetés az Azure Advisor](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor díjakkal kapcsolatos ajánlások](advisor-performance-recommendations.md)
* [Advisor-teljesítményajánlások](advisor-performance-recommendations.md)
* [Az Advisor biztonsági javaslatok](advisor-security-recommendations.md)

