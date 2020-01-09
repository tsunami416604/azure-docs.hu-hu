---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 09179ff0cf8275e96cb87e0c183eb4a384dcb37b
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564750"
---
Fontos átlátni, hogy a virtuális gépek hol és hogyan működnek az Azure-ban, illetve hogy a teljesítmény, a rendelkezésre állás és a redundancia maximalizálása terén milyen lehetőségek vannak. Ez a cikk az Azure rendelkezésre állással és redundanciával kapcsolatos szolgáltatásairól nyújt áttekintést.


## <a name="what-are-azure-regions"></a>Mik azok az Azure-régiók?
Az Azure világszerte számos adatközpontban működik. Ezek az adatközpontok földrajzi régiók szerint vannak csoportosítva, ami kellő mozgásteret biztosít az alkalmazások létrehozási helyének megválasztásához. 

Az Azure-erőforrásokat olyan meghatározott földrajzi régiókban hozza létre, mint az USA nyugati régiója, Észak-Európa vagy Délkelet-Ázsia. [A régiók és a kapcsolódó helyek listáját itt](https://azure.microsoft.com/regions/) tekintheti meg. A redundancia és a rendelkezésre állás biztosítása érdekében minden egyes régióban egyszerre több adatközpont működik. Ez a megközelítés rugalmasságot biztosít az alkalmazások tervezéséhez, hogy a lehető legközelebb virtuális gépeket hozzon létre a felhasználók számára, és megfeleljen bármilyen jogi, megfelelőségi vagy adózási célra.

## <a name="special-azure-regions"></a>Különleges Azure-régiók
Az Azure néhány speciális régióval rendelkezik, amelyeket az alkalmazások megfelelőségi vagy jogi célokra való kiépítésekor érdemes használni. Ezek a különleges régiók a következők:

* **US Gov Virginia** és **US Gov Iowa**
  * Az Azure fizikailag és logikailag elszigetelt példánya az USA-beli államigazgatási szervek és partnereik számára, amelyet biztonsági szempontból átvilágított, USA-beli személyek kezelnek. Olyan további megfelelőségi tanúsítványokat is tartalmaz, mint a [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) vagy a [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Tudjon meg többet az [Azure Governmentről](https://azure.microsoft.com/features/gov/).
* **Kelet-Kína** és **Észak-Kína**
  * Ezek a régiók a Microsoft és a 21Vianet közötti különleges partnerség révén érhetők el, amelyben az adatközpontok fenntartója nem közvetlenül a Microsoft. További információ az [Azure China 21Vianet](https://www.windowsazure.cn/)-ről.
* **Közép-Németország** és **Északkelet-Németország**
  * Ezek a régiók egy adatkezelői modellen keresztül érhetők el, amely szerint az ügyféladatok Németországban maradnak a T-Systems, a Deutsche Telekom vállalat, amely a németországi adatkezelő.

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
Néhány szolgáltatás és virtuálisgép-funkció, például meghatározott méretű virtuális gépek vagy adott tárolótípusok csak bizonyos régiókban érhetők el. Emellett létezik pár olyan globális Azure-szolgáltatás is, amelyhez nem kell régiót választani. Ilyen például az [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), a [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) vagy az [Azure DNS](../articles/dns/dns-overview.md). Az alkalmazáskörnyezet megtervezéséhez javasoljuk, hogy tekintse meg [az Azure-szolgáltatások régiók szerinti rendelkezésre állását](https://azure.microsoft.com/regions/#services). [A virtuális gépek támogatott méreteit és korlátozásait programozott módon is lekérdezheti az egyes régiókban](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

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
* A prémium szintű Managed Disks SSD-meghajtókkal és standard Managed Disksekkel támogatja a normál fonási lemezek. Mind a Premium, mind a Standard Managed Disks szolgáltatás díjszabása a kiépített lemezkapacitás szerint alakul.

**Nem felügyelt lemezek**
* A Premium Storage szolgáltatás SSD-meghajtókon alapul, és a lemez kapacitása alapján kell fizetni.
* A Standard szintű tárterület általános merevlemezekkel működik, a díjszabás pedig a kihasznált kapacitástól és a rendelkezésre álló, választott tárterülettől függ.
  * Az RA-GRS esetében egy további georeplikációs adatátviteli díj is érvényben van, az adatok másik Azure-régióba történő replikálásához használt sávszélességre vonatkozóan.

A különböző tártípusokhoz és rendelkezésre állási lehetőségekhez tartozó díjszabásról lásd: [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

