---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f8ca716f4ab991fecca52ca2d5fed080e6f4c177
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060466"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Standard SSD Managed Disks-Azure-beli virtuális gépek számítási feladataihoz

Standard folyamatos állapot-meghajtók (SSD) az Azure Managed Disks egy költséghatékony tárolási megoldás az IOPS alacsonyabb szinten egységes teljesítményt igénylő feladatokra optimalizált. Standard SSD azok számára, akik szeretne áthelyezni a felhőbe, különösen akkor, ha problémákat tapasztal a HDD-megoldások a helyszínen futó számítási feladatok varianciáját együtt egy jó belépési szintű megoldást kínál. Standard SSD-k jobb rendelkezésre állást, konzisztencia, megbízhatóságot és késés HDD lemezek képest, és a webkiszolgálók, a alacsony IOPS-alkalmazáskiszolgálók, a kisebb terhelésű vállalati alkalmazások és a fejlesztési és tesztelési számítási feladatok kiválóan alkalmasak.

## <a name="standard-ssd-features"></a>Standard SSD-funkciók

**A Managed Disks**: Standard SSD-k csak felügyelt lemezként érhető el. Standard SSD-a nem támogatottak a nem felügyelt lemezek és Lapblobok. A felügyelt lemez létrehozásakor adja meg a lemez típusát a Standard SSD-t, és azt jelzik, a méretet lemez van szüksége, és az Azure létrehozza és felügyeli a lemezt Ön helyett.
Standard SSD-k támogatják a Managed Disks által kínált összes szolgáltatás felügyeleti műveleteket. Például létrehozhat, és másolja vagy pillanatkép standard szintű SSD Managed Disks megegyező módon elvégezheti a Managed Disks szolgáltatással.

**Virtuális gépek**: Standard SSD-k az Azure virtuális gépeket tartalmazó, beleértve a virtuális gép típusát, amely nem támogatja a prémium szintű lemezeket is használható. Például, ha egy A-sorozatú virtuális Gépet, vagy az N-sorozatú virtuális gépek, vagy a DS sorozatú használ, vagy bármely más Azure Virtuálisgép-sorozatok, Standard SSD-k használata a virtuális Gépeket. Standard SSD bevezetésével korábban használt lemezek SSD-alapú áttérés és az állandó teljesítmény érdekében, a magasabb rendelkezésre állás, a nagyobb késést és a egy általános jobb élményt lemezek HDD-alapú munkaterhelések széles körének azt engedélyezése az SSD-n keresztül elérhető tapasztalható.

**Tartós és magas rendelkezésre állású**: Standard SSD-kkel rendelkezik konzisztens módon kézbesíti, magas rendelkezésre állást és tartósságot lemezek ugyanabban az Azure Disks platform épül. Az Azure Disks 99,999 %-os rendelkezésre állást lettek kialakítva. Az összes felügyelt lemez, például a Standard SSD-k is kínálunk helyi redundáns tárolást (LRS). Az LRS, a platform tart fenn minden lemez adatok több replika és következetesen rendelkezik kézbesíti a nagyvállalati szintű tartósságot iaas lemezeken, az iparágvezető nulla százalékos érvényes évesített hibaaránya.

**A pillanatképek**: például az összes felügyelt lemezek, a Standard SSD-k is támogatja a pillanatképek létrehozását. Pillanatkép típusa vagy a standard szintű (HDD), vagy a prémium (SSD) lehet. A cost saving, javasoljuk, hogy az összes Azure-lemez esetében a standard szintű (HDD-) pillanatkép típusa. Ennek oka az, ha egy felügyelt lemez létrehozása pillanatképből, folyamatos megválaszthatja, például Standard SSD vagy prémium szintű SSD magasabb szintre.

## <a name="scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménycélok

Az alábbi táblázat tartalmazza a jelenleg rendelkezésre állnak az Standard SSD-Tárolóeszközön lemezméretet.

|Standard SSD-lemez típusa  |Lemezméret  |Iops-érték lemezenként  |Adattovábbítás lemezenként  |
|---------|---------|---------|---------|
|E10     |128 GiB         |Legfeljebb 500         |Másodpercenként legfeljebb 60 MiB         |
|E15     |256 GiB         |Legfeljebb 500         |Másodpercenként legfeljebb 60 MiB         |
|E20     |512 GiB         |Legfeljebb 500         |Másodpercenként legfeljebb 60 MiB         |
|E30     |1 024 GB       |Legfeljebb 500         |Másodpercenként legfeljebb 60 MiB         |
|E40     |2048 giB       |Legfeljebb 500         |Másodpercenként legfeljebb 60 MiB         |
|E50     |4095 giB       |Legfeljebb 500         |Másodpercenként legfeljebb 60 MiB         |
|E60     |Futtathat 8192 giB       |Akár 1300       |Másodpercenként legfeljebb 300 MiB        |
|E70     |16 384 giB      |Legfeljebb 2000       |Másodpercenként akár 500 MiB        |
|E80     |– 32 767 giB      |Legfeljebb 2000       |Másodpercenként akár 500 MiB        |

Standard SSD-k úgy tervezték, egyszámjegyű ezredmásodperces késéseket nyújt a legtöbb i/o-műveletek és az IOPS és átviteli sebesség, akár a fenti táblázat ismerteti a korlátokat. Tényleges IOPS és átviteli sebesség függvénye néha a forgalmi minták. Standard SSD-k, mint a HDD-lemezek stabilabb teljesítményt biztosít a kisebb hálózati késést.

Prémium szintű SSD-k, másrészt jobban teljesítenek, mint Standard SSD-k, az alacsony késés érdekében, a magas IOPS és átviteli sebesség és a lemezteljesítményt együtt még jobb konzisztencia. Ez a kritikus fontosságú éles számítási feladatokhoz ajánljuk lemezes típusa. Ha a számítási feladatok nagy teljesítményű, kis késleltetésű lemeztámogatás van szüksége, érdemes a Premium Storage tárolást használ.

A prémium szintű SSD-k, például a Standard SSD-k i/o-egységek mérete 256 KiB is használhatja. Ha az átvitt adatok kisebb, mint 256 KiB, akkor számít 1 i/o-egységek. Nagyobb méretű i/o-méretek számítanak több i/o-mérete 256 KiB. Ha például öt i/o-egységek 1100 KiB i/o számít.

## <a name="pricing-and-billing"></a>Árak és számlázás

Standard SSD-k használatakor az alábbi számlázási szempontok érvényesek:

- Felügyelt lemez mérete
- Pillanatképek
- Kimenő adatforgalom
- Tranzakciók

**Felügyelt lemez mérete**: felügyelt lemezek a kiépítési méret számítjuk fel. Azure maps-(kerekítve) a legközelebbi lemez mérete ajánlat kiosztott méretét. A lemezméretek érhető el a részletekért lásd: a tábla, méretezhetőségi és Teljesítménycéljai a fenti szakaszban. Minden lemez képez le egy támogatott kiosztott lemez méretét, és ennek megfelelően számlázzuk. Például, ha az Ön által üzembe helyezett egy 200 GB Standard SSD, azt lesz leképezve a lemez mérete vonatkozó ajánlatot E15 (256 GB). Minden üzembe helyezett lemez használata óradíjas a Premium Storage-ajánlat a havi díjak használatával. Például ha üzembe helyezett egy E10 lemezt, és 20 óra múlva törli azt, a számlázás a 20 óra arányosan E10 előfizetésért. Ez a, függetlenül a lemezre írt adatok tényleges mennyiségét.

**A pillanatképek**: a Managed Disks pillanatképek számlázása a kapacitás felhasználása a pillanatképek a cél és a forrásban, ha van ilyen. A pillanatképek további információkért lásd: [felügyelt lemez-pillanatképek](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Kimenő adatforgalom**: [kimenő adatforgalom](https://azure.microsoft.com/pricing/details/bandwidth/) (adatok csak az Azure adatközpontok kimenő adatforgaloma) díjak lépnek fel a sávszélesség-használat.

**Tranzakciók**: Standard HDD hasonló, szabványos SSD meghajtókon tranzakciók díjak lépnek fel. Tranzakciók olvasása is tartalmazza, és írási műveleteket a lemezen. I/o egység a tranzakciók számlázás Standard SSD használt mérete 256 KiB. Nagyobb méretű i/o-méretek számítanak több i/o-mérete 256 KiB.

A virtuális gépek és a Managed Disks díjszabása további információkért lásd:

- [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [A Managed Disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>További lépések

Standard SSD-k létrehozásával kapcsolatos további tudnivalókért tekintse meg a minta bemutatja, hogyan több standard SSD-kkel rendelkező virtuális gép létrehozásához.

> [!div class="nextstepaction"]
> [Standard SSD-k több virtuális gép létrehozása](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)