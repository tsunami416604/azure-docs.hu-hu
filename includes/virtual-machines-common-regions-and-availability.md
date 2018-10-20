---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: zr-msft
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: include file
ms.openlocfilehash: 231dcee1a9b330b2f8721283c39c71002a75fcac
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49476285"
---
# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek régiók szerinti csoportosítása és rendelkezésre állása
Az Azure világszerte számos adatközpontban működik. Ezek az adatközpontok földrajzi régiók szerint vannak csoportosítva, ami kellő mozgásteret biztosít az alkalmazások létrehozási helyének megválasztásához. Fontos átlátni, hogy a virtuális gépek hol és hogyan működnek az Azure-ban, illetve hogy a teljesítmény, a rendelkezésre állás és a redundancia maximalizálása terén milyen lehetőségek vannak. Ez a cikk az Azure rendelkezésre állással és redundanciával kapcsolatos szolgáltatásairól nyújt áttekintést.

## <a name="what-are-azure-regions"></a>Mik azok az Azure-régiók?
Hozza létre az Azure-erőforrások meghatározott földrajzi régiókban, mint az "West US", Észak-Európában vagy Délkelet-Ázsia. [A régiók és a kapcsolódó helyek listáját itt](https://azure.microsoft.com/regions/) tekintheti meg. A redundancia és a rendelkezésre állás biztosítása érdekében minden egyes régióban egyszerre több adatközpont működik. Ez nagyobb rugalmasságot biztosít a felhasználókhoz legközelebb eső virtuális gépek létrehozása és a felel meg a jogi, megfelelőségi és adóügyi alkalmazások tervezésekor.

## <a name="special-azure-regions"></a>Különleges Azure-régiók
Az Azure rendelkezik néhány speciális régióban, előfordulhat, hogy szeretne használni az alkalmazások megfelelőségi vagy jogi okokból kiépítésekor. Ezek a különleges régiók a következők:

* **US Gov Virginia** és **US Gov Iowa**
  * Az Azure fizikailag és logikailag elszigetelt példánya az USA-beli államigazgatási szervek és partnereik számára, amelyet biztonsági szempontból átvilágított, USA-beli személyek kezelnek. Olyan további megfelelőségi tanúsítványokat is tartalmaz, mint a [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) vagy a [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Tudjon meg többet az [Azure Governmentről](https://azure.microsoft.com/features/gov/).
* **Kelet-Kína** és **Észak-Kína**
  * Ezek a régiók a Microsoft és a 21Vianet közötti különleges partnerség révén érhetők el, amelyben az adatközpontok fenntartója nem közvetlenül a Microsoft. Tudjon meg többet a [Microsoft Azure kínai működéséről](http://www.windowsazure.cn/).
* **Közép-Németország** és **Északkelet-Németország**
  * Ezekben a régiókban érhetők el egy megbízott adatkezelői modell, amellyel ügyféladatok Németországban maradnak a Deutsche Telekom leányvállalata vállalata és a német megbízott adatkezelő T-Systems felügyelete alatt.

## <a name="region-pairs"></a>Régiópárok
Minden egyes Azure-régió párban áll egy másik, azonos földrajzi helyhez tartozó régióval (amilyen például az USA, Európa vagy Ázsia). Ez lehetővé teszi az erőforrások, így például a virtuálisgép-tárolók földrajzi hely szerinti replikálását abból a megfontolásból, hogy természeti katasztrófák, zavargások, áramkimaradások vagy a fizikai hálózat fennakadásai kisebb eséllyel jelentkeznek egyszerre mindkét régióban. A régiópárok további előnyei még a következők:

* Az Azure szélesebb körű leállása esetén minden párból az egyik régió előnyt élvez, ami segít csökkenteni az alkalmazások helyreállításához szükséges időt. 
* A tervezett Azure-frissítések egyszerre csak a régiópár egyik tagján jelennek meg, ami csökkenti az állásidőt és az alkalmazáskimaradás kockázatát.
* Az adatok a pár mindkét tagja esetében ugyanazon a földrajzi helyen maradnak (Dél-Brazília kivételével), ami adóügyi és törvényi szempontból fontos.

Néhány példa a régiópárokra:

| Elsődleges | Másodlagos |
|:--- |:--- |
| USA nyugati régiója |USA keleti régiója |
| Észak-Európa |Nyugat-Európa |
| Délkelet-Ázsia |Kelet-Ázsia |

[A régiópárok teljes listája](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions) itt érhető el.

## <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása
Néhány szolgáltatás és virtuálisgép-funkció, például meghatározott méretű virtuális gépek vagy adott tárolótípusok csak bizonyos régiókban érhetők el. Emellett létezik pár olyan globális Azure-szolgáltatás is, amelyhez nem kell régiót választani. Ilyen például az [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), a [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) vagy az [Azure DNS](../articles/dns/dns-overview.md). Az alkalmazáskörnyezet megtervezéséhez javasoljuk, hogy tekintse meg [az Azure-szolgáltatások régiók szerinti rendelkezésre állását](https://azure.microsoft.com/regions/#services). Emellett [programozott módon a Virtuálisgép-méretek és a korlátozásokat az egyes régiókban támogatott lekérdezési](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
A rendelkezésre álló tárreplikációs lehetőségek mérlegeléséhez fontos átlátni az Azure-régiók és földrajzi helyek működését. A tárterület típusától függően eltérő replikálási lehetőségek állnak rendelkezésre.

**Azure Managed Disks**
* Helyileg redundáns tárolás (LRS)
  * A rendszer háromszor replikálja az adatokat abban a régióban, amelyben a tárfiókot létrehozták.

**Tárfiókalapú lemezek**
* Helyileg redundáns tárolás (LRS)
  * A rendszer háromszor replikálja az adatokat abban a régióban, amelyben a tárfiókot létrehozták.
* Zónaredundáns tárolás (ZRS)
  * A rendszer két vagy három intézményben háromszor replikálja az adatokat, amelyek egy vagy két régióban is lehetnek.
* Georedundáns tárolás (GRS)
  * A rendszer egy másodlagos régióba replikálja az adatokat, amely a forrásadatok elsődleges helyétől több száz kilométerre található.
* Írásvédett georedundáns tárolás (RA-GRS)
  * A rendszer egy másodlagos régióba replikálja az adatokat (csakúgy, mint a GRS esetében), és az adatokhoz a másodlagos helyszínen is írásvédett hozzáférést biztosít.

A következő táblázat a tárreplikáció típusai közötti különbségek rövid összefoglalását tartalmazza:

| Replikációs stratégia | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| A rendszer több intézményben replikálja az adatokat. |Nem |Igen |Igen |Igen |
| Az adatok a másodlagos és az elsődleges helyről is olvashatók. |Nem |Nem |Nem |Igen |
| A külön csomópontokon fenntartott adatmásolatok száma. |3 |3 |6 |6 |

[Az Azure tárreplikációs lehetőségeiről itt](../articles/storage/common/storage-redundancy.md) olvashat bővebben. További információ a felügyelt lemezekről: [Azure Managed Disks – áttekintés](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Tárolási költségek
Az árak a választott tárolótípus és rendelkezésre állás függvényében változnak.

**Azure Managed Disks**
* Prémium szintű Managed Disks élvezik Solid-State meghajtók (SSD-kkel) és a standard szintű Managed Disks élvezik rendszeres tartalomfogyasztás lemezek. Mind a Premium, mind a Standard Managed Disks szolgáltatás díjszabása a kiépített lemezkapacitás szerint alakul.

**Nem felügyelt lemezek**
* A Premium storage Solid-State meghajtók (SSD-k) használatával, és a lemez kapacitását díját.
* A Standard szintű tárterület általános merevlemezekkel működik, a díjszabás pedig a kihasznált kapacitástól és a rendelkezésre álló, választott tárterülettől függ.
  * Az RA-GRS esetében egy további georeplikációs adatátviteli díj is érvényben van, az adatok másik Azure-régióba történő replikálásához használt sávszélességre vonatkozóan.

A különböző tártípusokhoz és rendelkezésre állási lehetőségekhez tartozó díjszabásról lásd: [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Egy rendelkezésre állási csoportban, amely lehetővé teszi az Azure tudni, hogyan épül fel az alkalmazás a redundancia és rendelkezésre állás biztosításához adatközpontokon belüli virtuális gépek logikai csoportosítása. Azt javasoljuk, hogy két vagy több virtuális gépet hoz létre a rendszer egy rendelkezésre állási csoport nyújt a magas rendelkezésre állású alkalmazások, és megfelelnek a [99,95 %-os Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Van a rendelkezésre állási csoport számára ingyenesen magát, az Ön által létrehozott minden egyes Virtuálisgép-példány csak kell fizetnie. Ha az önálló virtuális gép [Azure Premium Storage](../articles/virtual-machines/windows/premium-storage.md) csomagot használ, az Azure SLA a nem tervezett karbantartási események során is érvényben van. 

Rendelkezésre állási csoport két további csoportosítást is tartalmaz, amelyek hardverhibák ellen, és lehetővé teszik a frissítések biztonságosan kell alkalmazni – a tartalék tartományok és frissítési tartományok (frissítési) áll. Tudjon meg többet a [Linux-alapú virtuális gépek](../articles/virtual-machines/linux/manage-availability.md), illetve a [Windows-alapú virtuális gépek](../articles/virtual-machines/windows/manage-availability.md) rendelkezésre állásának kezeléséről.

### <a name="fault-domains"></a>Tartalék tartományok
A tartalék tartomány a mögöttes hardverelemek logikus csoportja, ahol az áramforrás és a hálózati kapcsoló közös, a helyszíni adatközpontok állványaihoz hasonlóan. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a tartalék tartományok között. Ez a módszer korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy a tápellátás megszakadásának hatását.

### <a name="update-domains"></a>Frissítési tartományok
A frissítési tartomány a mögöttes hardverelemek logikus csoportja, amelyen egyszerre végezhető karbantartás vagy újraindítás. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a frissítési tartományok között. Ez lehetővé teszi, hogy az alkalmazás legalább egy példánya mindig fusson akkor is, ha az Azure platformon épp rendszeres karbantartás folyik. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre.

![Rendelkezésre állási csoportok](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>Felügyelt lemezes tartalék tartományokra
Az [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) használatával létrehozott virtuális gépek felügyelt rendelkezésre állási csoportok használata esetén felügyelt lemezes tartalék tartományokra vannak elosztva. Ez a kiosztás biztosítja, hogy a virtuális géphez csatolt mindegyik felügyelt lemez ugyanabban a felügyelt lemezes tartalék tartományban legyen. Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet. További információkért ezekkel a felügyelt lemezes tartalék tartományokra vonatkozó [Linux rendszerű virtuális gépek](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) vagy [Windows virtuális gépek](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Felügyelt rendelkezésre állási csoport](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>Rendelkezésre állási zónák

[A rendelkezésre állási zónák](../articles/availability-zones/az-overview.md), másik rendelkezésre állási csoportok, bontsa ki a szabályozás rendelkezik az alkalmazások és adatok a virtuális gépek rendelkezésre állásának fenntartása érdekében. A rendelkezésre állási zónák egy Azure-régió fizikailag elkülönített zónáit jelentik. Nincsenek három rendelkezésre állási zónákhoz támogatott Azure-régiónként. Mindegyik rendelkezésre állási zóna különálló áramforrással, hálózattal és hűtéssel rendelkezik. A replikált virtuális gépek használata a zónák a megoldások tervezése, védheti alkalmazásait és adatait az egy kínai adatközpont származik. Ha egy zóna integritása sérül, majd a replikált alkalmazások és adatok érhetők el azonnal egy másik zónában. 

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

További információ a központi telepítése egy [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) vagy [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) virtuális gép egy rendelkezésre állási zónában.

## <a name="next-steps"></a>További lépések
Mostantól a saját Azure-környezetében is használhatja ezeket a rendelkezésre állási és redundanciával kapcsolatos szolgáltatásokat. Javasoljuk, hogy tájékozódjon [az Azure rendelkezésre állásával kapcsolatos ajánlott eljárásokról](../articles/best-practices-availability-checklist.md).

