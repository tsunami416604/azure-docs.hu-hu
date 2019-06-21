---
title: CloudSimple szolgáltatás biztonsága
description: A biztonság CloudSimple szolgáltatások közös felelősség modellek
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: db093f6aef4f1bdb28a96bf89b3013d359a7b796
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154113"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple biztonsági áttekintése

Ez a cikk áttekintést hogyan biztonság van alkalmazva a Azure VMware megoldás által CloudSimple szolgáltatás, az infrastruktúra és a datacenter. Ismerje meg az adatvédelem és a biztonsági, hálózati biztonsági és biztonsági rések és javítások kezeléséről.

## <a name="shared-responsibility"></a>Közös felelősség

Azure VMware-megoldás által CloudSimple biztonsági egy közös felelősség modellt használ. Megbízható a felhőbeli biztonság témakörében a gazdafájlon keresztül, a megosztott felelősséget ügyfelek és a Microsoft a szolgáltató. Ez a mátrix a felelősségi magasabb szintű biztonságot nyújt, és kiküszöböli a hibaérzékeny pontokat.

## <a name="azure-infrastructure"></a>Azure-infrastruktúra 

Azure-infrastruktúra biztonsági szempontok közé tartozik az adatközpontok és a készülék helyét.

### <a name="datacenter-security"></a>Adatközpont-biztonság 

A Microsoft rendelkezik egy teljes részlegre tervezése, létrehozása és működtetése a fizikai létesítményeket, amelyek támogatják az Azure számára. Ez a csapat befektettek állapota-a-a legújabb fizikai biztonság fenntartása. Fizikai biztonság további információkért lásd: [Azure létesítményekben, a helyi és a fizikai biztonság](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Berendezések helyét

Az operációs rendszer nélküli hardvereinek rögzítésére, amely futtatja a magánfelhők üzemeltetett Azure-beli adatközpont-helyen. Biometriai-alapú kétfaktoros hitelesítés szükség az állatokat, ahol az adott berendezés található eléréséhez.

## <a name="dedicated-hardware"></a>Dedikált hardver

A CloudSimple szolgáltatás részeként a vevőket CloudSimple dedikált operációs rendszer nélküli gazdagépek beolvasása a többi bérlő hardvert fizikailag elkülönített helyi csatlakoztatott adatlemezekkel rendelkező. Egy vsan-hoz az ESXi-hipervizorra minden csomóponton futnak. A csomópontok ügyfél dedikált VMware vCenter és NSX keresztül történik. Nem osztoznak a bérlők között hardver elkülönítési és biztonsági védelmi réteget biztosít.

## <a name="data-security"></a>Adatbiztonság

Ügyfelek tartsa meg a vezérlő és tulajdonosi viszonyainak adataikat. Az ügyféladatok adatok termékkezelési feladata az ügyfél.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Adatvédelem az inaktív adatok és a belső hálózaton belül mozgásban lévő adatok

A magánfelhő-alapú környezetben az inaktív adatok, a titkosítás a vsan-hoz is használhatja. vSAN titkosítási együttműködik a saját virtuális hálózaton vagy a helyszíni VMware-minősítéssel rendelkező külső kulcskezelő kiszolgálókat (KMS). Szabályozhatja az adattitkosítási kulcsokat saját magának. A magánfelhőhöz mozgásban lévő adatok esetén vSphere támogatja az adatok titkosítása az VMkernel minden forgalom, amely vMotion-forgalmat is magában foglalja a hálózaton keresztül.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Adatvédelem a nyilvános hálózaton keresztül helyezze át a szükséges adatok

Ugyanazokon a nyilvános hálózatok adatok védelme érdekében létrehozhat IPsec és SSL VPN-alagutat a magánfelhők számára. Gyakran használt titkosítási módszerek támogatottak, például AES 128-bájtos és 256 bájt. Adatok az átvitel során, beleértve a hitelesítést, a rendszergazdai hozzáférést és a vásárlói adatokat, a standard szintű titkosítási mechanizmust, például a biztonságos RDP, SSH és a TLS 1.2 titkosítása. Bizalmas adatok szállítja kommunikációt a standard szintű titkosítási mechanizmust használ.

### <a name="secure-disposal"></a>Biztonságos értékesítés 

A CloudSimple szolgáltatás jár, vagy le van állítva, ha Ön felelős eltávolításával vagy az adatok törlése. CloudSimple törléséhez, vagy vissza tárolt összes ügyféladathoz, azzal a különbséggel a mértékben CloudSimple van törvény vagy azok egy részét a személyes adatok megőrzése az ügyfél szerződése meg Önnel működik. Szükséges megőrizni személyes adatokat, ha CloudSimple az adatok archiválása, és megakadályozza, hogy a vásárlói adatokat további feldolgozás ésszerű mértékek valósítja meg.

### <a name="data-location"></a>Az adatok helye

A magánfelhők beállításakor válassza ki az Azure-régió, melyekre van telepítve. VMware virtuális gépek adatainak nem át, hogy a fizikai adatközpontban működnek, ha az adatok áttelepítését vagy a külső helyszíni adatok biztonsági mentése hajt végre. Is futtatni a számítási feladatok és több Azure-régióban lévő adatok tárolása, ha az igényeinek megfelelő.

A vásárlói adatokat, hogy a magánfelhő-alapú hiperkonvergens csomópontokon rezidens nem haladnak át a helyek nélkül a bérlői rendszergazda explicit művelet. A feladata a számítási feladatok megvalósítása az magas rendelkezésre állással.

### <a name="data-backups"></a>Adatok biztonsági mentése
CloudSimple nem biztonsági mentése vagy archiválása a vásárlói adatokat. CloudSimple hajtsa végre a felügyeleti kiszolgálók magas rendelkezésre állás biztosításához vCenter és NSX adatok rendszeres biztonsági mentés. Biztonsági mentés, mielőtt minden adat titkosítása a vCenter-forrás VMware API-k használatával. A titkosított adatok átvitelét és tárolva az Azure-blobba. Titkosítási kulcsok biztonsági mentésekhez rendkívül biztonságos CloudSimple felügyelt tároló, amely a CloudSimple virtuális hálózatot az Azure-ban vannak tárolva.

## <a name="network-security"></a>Hálózati biztonság

A CloudSimple megoldás a hálózati biztonság rétegeit támaszkodik.

### <a name="azure-edge-security"></a>Az Azure edge biztonsági

A CloudSimple szolgáltatások az Azure által biztosított alap hálózati biztonság épülnek. Azure jellegű defense technikák észlelése és időben választ hálózatalapú támadások, rendellenes bejövő vagy kimenő forgalmi mintázatait, és elosztott-szolgáltatásmegtagadásos (DDoS) támadások társított vonatkozik. A biztonsági ellenőrzést a magánfelhő-környezetekben és az adatsík CloudSimple által fejlesztett szoftver vonatkozik.

### <a name="segmentation"></a>Szegmentálás

A CloudSimple szolgáltatás rendelkezik a 2. rétegbeli hálózati hozzáférés korlátozása a saját privát a magánfelhő-alapú környezetben logikailag elkülönített hálózatok. A tűzfal a magánfelhő-alapú hálózatok további védelmet biztosíthat. A CloudSimple portálon kelet – Nyugat-India és észak – dél-hálózati forgalom ellenőrzési szabályok összes hálózati forgalom, például a magánfelhő-on belüli adatforgalom, közötti magánfelhő-forgalom, az internetre irányuló forgalom általános és a helyszíni hálózati forgalmat definiálása IPsec VPN- vagy Azure ExpressRoute kapcsolaton.

## <a name="vulnerability-and-patch-management"></a>Biztonsági rés és a patch-kezelés 

Rendszeres biztonsági javítás felügyelt VMware-szoftver, például a NSX, ESXi és vCenter CloudSimple feladata.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Ügyfelek előnyben részesített, többtényezős hitelesítést vagy az SSO használatával hitelesítheti az Azure-fiókjához (az Azure Active Directoryban). Az Azure Portalról indítsa el a CloudSimple portál ismételt a hitelesítő adatok megadása nélkül.

CloudSimple identitás forrás választható konfiguráció támogatja a magánfelhő vcenter. Használhat egy [helyszíni identitásforrásról](https://docs.azure.cloudsimple.com/set-vcenter-identity), egy új identitásforrásról a magánfelhő vagy [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Alapértelmezés szerint felhasználók kapnak a mindennapos a magánfelhőhöz vCenter szükséges jogosultságokkal. Ezt a jogosultsági szintet nem tartalmazza a vCenter rendszergazdai hozzáféréssel. Ha a rendszergazdai hozzáférés ideiglenesen kötelező, [eszkalálni a jogosultságokat](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) korlátozott ideig a felügyeleti feladatok végrehajtása.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [CloudSimple szolgáltatás létrehozása az Azure-ban](quickstart-create-cloudsimple-service.md).
* Ismerje meg, hogyan [magánfelhő létrehozása](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Ismerje meg, hogyan [konfigurálása egy magánfelhős környezetet](quickstart-create-private-cloud.md).
