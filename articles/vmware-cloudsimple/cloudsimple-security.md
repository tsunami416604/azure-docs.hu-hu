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
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358123"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple biztonsági áttekintése

Ez a cikk áttekintést hogyan biztonság van alkalmazva CloudSimple szolgáltatás, az infrastruktúra és a datacenter.  Ismerje meg az adatvédelem és a biztonsági, hálózati biztonsági és biztonsági rések és javítások kezeléséről.

## <a name="shared-responsibility"></a>Közös felelősség

Azure VMware-megoldás által CloudSimple biztonsági egy közös felelősség modellt használ. Megbízható a felhőbeli biztonság témakörében a gazdafájlon keresztül, a megosztott felelősséget ügyfelek és a Microsoft a szolgáltató. Ez a mátrix a felelősségi magasabb szintű biztonságot nyújt, és kiküszöböli a hibaérzékeny pontokat. 

## <a name="azure-infrastructure"></a>Azure-infrastruktúra 

Azure-infrastruktúra biztonsági szempontok közé tartozik az adatközpontok és a készülék helyét. 

### <a name="datacenter-security"></a>Adatközpont-biztonság 

A Microsoft rendelkezik egy teljes részlegre tervezése, létrehozása és működtetése az Azure támogatási fizikai eszközök számára. Ez a csapat befektettek állapota-a-a legújabb fizikai biztonság fenntartása. Fizikai biztonság részletes ismertetéséért lásd: [Azure létesítményekben, a helyi és a fizikai biztonság](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Berendezések helyét

Az operációs rendszer nélküli hardveres berendezések, amely futtatja a Magánfelhők létrehozása az Azure-adatközpont-helyeinek üzemel.  Az állatokat, ahol az adott berendezés, biometrikus alapján kéttényezős hitelesítéssel való hozzáféréshez szükséges.

## <a name="dedicated-hardware"></a>Dedikált hardver

A CloudSimple szolgáltatás részeként minden CloudSimple ügyfél más bérlő hardvert fizikailag elkülönített helyi csatlakoztatott adatlemezekkel rendelkező dedikált operációs rendszer nélküli gazdagépek beolvasása. Egy vsan-hoz az ESXi-hipervizorra minden csomóponton futnak. A csomópontok dedikált ügyfél VMware vCenter és NSX keresztül történik. Nem osztoznak a bérlők között hardver elkülönítési és biztonsági védelmi réteget biztosít.

## <a name="data-security"></a>Adatbiztonság

Ügyfelek tartsa meg a vezérlő és tulajdonosi viszonyainak adataikat. Az ügyféladatok adatok termékkezelési feladata az ügyfél.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Adatvédelem az inaktív adatok és a belső hálózaton belül mozgásban lévő adatok

A Magánfelhő-környezetben az inaktív adatok, a titkosítás a vsan-hoz is használhatja. a saját virtuális hálózaton vagy a helyszíni hitelesített külső kulcskezelő kiszolgálókat (KMS) VMware vSAN titkosítási működik.  Szabályozhatja az adattitkosítási kulcsokat saját magának. A mozgásban lévő adatok a privát felhőben, az vSphere támogatja a titkosítást az adatok minden vmkernel forgalom (a vMotion-forgalmat is beleértve) a hálózaton keresztül.

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Váltás a nyilvános hálózatok szükséges adatokat az adatok védelmére

Ugyanazokon a nyilvános hálózatok adatok védelme érdekében létrehozhat IPsec és SSL VPN-alagutat a Magánfelhők számára. Gyakran használt titkosítási módszerek támogatottak, például AES 128-bájtos és 256 bájt. A standard szintű titkosítási mechanizmusok (az SSH, a TLS 1.2-es és biztonságos RDP) titkosított adatokat átvitel közben (például a hitelesítést, a rendszergazdai hozzáférést és a vásárlói adatokat). Bizalmas adatok szállítja kommunikációt a standard szintű titkosítási mechanizmust használ.

### <a name="secure-disposal"></a>Biztonságos értékesítés 

Ha a CloudSimple szolgáltatás jár, vagy le van állítva, Ön felelős eltávolításával vagy az adatok törlése. CloudSimple együttműködik, hogy törli vagy tárolt összes ügyféladathoz visszaadása, azzal a különbséggel a mértékben CloudSimple van törvény vagy azok egy részét a személyes adatok megőrzése az ügyfél szerződése meg. Személyes adatok megőrzéséhez szükség esetén CloudSimple az adatok archiválása, és megakadályozza, hogy a vásárlói adatokat további feldolgozás ésszerű intézkedéseket.

### <a name="data-location"></a>Az adatok helye

A Magánfelhők beállításakor válassza ki az Azure-régió hol szeretné telepíteni. VMware virtuális gépek adatainak nem helyezi át, hogy a fizikai adatközpontban működnek, ha az adatok áttelepítését vagy a külső helyszíni adatok biztonsági mentése hajt végre. Számítási feladatokat üzemeltethet, és több Azure-régióban lévő adatok tárolása, ha az igényeinek megfelelő is.

A vásárlói adatokat, hogy a Magánfelhő-alapú hiperkonvergens csomópontokon rezidens nem haladnak át a helyek nélkül a bérlői rendszergazda explicit művelet. A feladata a számítási feladatok megvalósítása az magas rendelkezésre állással.

### <a name="data-backups"></a>Adatok biztonsági mentése
CloudSimple nem biztonsági mentése vagy archiválása a vásárlói adatokat. CloudSimple hajtsa végre a felügyeleti kiszolgálók magas rendelkezésre állás biztosításához vCenter és NSX adatok rendszeres biztonsági mentés. Minden adat titkosítása előtt biztonsági mentést, a vCenter-forrás VMware API-k használatával. A titkosított adatok átvitelét és az Azure-blobban tárolt. Titkosítási kulcsok biztonsági mentésekhez CloudSimple virtuális hálózatban az Azure-ban futó rendkívül biztonságos CloudSimple felügyelt tárolóban vannak tárolva.

## <a name="network-security"></a>Hálózati biztonság

A CloudSimple megoldás a hálózati biztonság rétegeit támaszkodik.

### <a name="azure-edge-security"></a>Az Azure edge biztonsági

A CloudSimple szolgáltatások az Azure által biztosított alap hálózati biztonság épülnek. Azure jellegű defense technikák észlelése és időben választ hálózatalapú támadások, rendellenes bejövő vagy kimenő forgalmi mintázatait, és elosztott-szolgáltatásmegtagadásos (DDoS) támadások társított vonatkozik. A biztonsági ellenőrzést a Magánfelhő-környezetek és az adatsík CloudSimple által fejlesztett szoftver vonatkozik.

### <a name="segmentation"></a>Szegmentálás

A CloudSimple szolgáltatás rendelkezik a 2. rétegbeli hálózati hozzáférés korlátozása a saját privát a Magánfelhő környezetben logikailag elkülönített hálózatok. A Magánfelhő hálózatok tűzfal használatával további védelmet biztosíthat. A CloudSimple portál segítségével új és a Névkiszolgálói hálózati forgalom vezérlők vonatkozó szabályok meghatározásához, beleértve a Magánfelhő-forgalom belüli, közötti privát felhő forgalom, általános forgalom az internethez, az összes hálózati forgalom és a hálózati forgalmat a helyszíni IPsec VPN-kapcsolaton keresztül, vagy Az ExpressRoute-kapcsolat.

## <a name="vulnerability-and-patch-management"></a>Biztonsági rések és a javítások kezelését 

Rendszeres biztonsági javítás (ESXi, vCenter és NSX) felügyelt VMware szoftver CloudSimple feladata.

## <a name="identity-and-access-management"></a>Identitáskezelés és hozzáférés-menedzsment (Identity and Access Management)

Ügyfeleink az Azure-fiókjához (az Azure ad-ben) előnyben részesített a multi-factor authentication vagy az SSO használatával hitelesítheti. Az Azure Portalról indítsa el a CloudSimple portál ismételt a hitelesítő adatok megadása nélkül.

CloudSimple identitás forrás választható konfiguráció támogatja a Magánfelhő vcenter. Használhatja egy [helyszíni identitásforrásról](https://docs.azure.cloudsimple.com/set-vcenter-identity), a Magánfelhő számára egy új identitásforrásról vagy [Azure ad-ben](https://docs.azure.cloudsimple.com/azure-ad).

Alapértelmezés szerint felhasználók kapnak a mindennapos belül a privát felhő vCenter szükséges jogosultságokkal. Ezt a jogosultsági szintet nem tartalmazza a vCenter rendszergazdai hozzáféréssel. Ha a rendszergazdai hozzáférés ideiglenesen kötelező, [eszkalálni a jogosultságokat](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) korlátozott ideig a felügyeleti feladatok végrehajtása.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [CloudSimple szolgáltatás létrehozása az Azure-ban](quickstart-create-cloudsimple-service.md)
* Ismerje meg, hogyan [magánfelhő létrehozása](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Ismerje meg, hogyan [egy magánfelhő-környezet konfigurálása](quickstart-create-private-cloud.md)
