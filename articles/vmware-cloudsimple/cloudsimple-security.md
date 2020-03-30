---
title: Azure VMware-megoldás a CloudSimple-től – Biztonság a CloudSimple szolgáltatásokhoz
description: A CloudSimple szolgáltatások biztonságáért felelős megosztott felelősségi modellek ismertetése
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024993"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple biztonsági áttekintés

Ez a cikk áttekintést nyújt arról, hogyan valósítja meg a biztonságot az Azure VMware-megoldás a CloudSimple szolgáltatás, az infrastruktúra és az adatközpont. Megismerheti az adatvédelmet és a biztonságot, a hálózati biztonságot, valamint a biztonsági rések és javítások kezelésének módját.

## <a name="shared-responsibility"></a>Megosztott feladatkörök

Az Azure VMware Solution by CloudSimple megosztott felelősségi modellt használ a biztonság érdekében. A felhőmegbízható biztonság az ügyfelek és a Microsoft mint szolgáltató megosztott felelősségein keresztül valósul meg. Ez a felelősségi mátrix nagyobb biztonságot nyújt, és kiküszöböli az egyes meghibásodási pontokat.

## <a name="azure-infrastructure"></a>Azure-infrastruktúra

Az Azure-infrastruktúra biztonsági szempontok közé tartozik az adatközpontok és a berendezések helyét.

### <a name="datacenter-security"></a>Adatközpont biztonsága

A Microsoft egy teljes részleggel rendelkezik az Azure-t támogató fizikai létesítmények tervezésére, építésére és üzemeltetésére. Ez a csapat a legkorszerűbb fizikai biztonság fenntartásába fektet. A fizikai biztonságról az [Azure létesítményeit, helyiségeit és fizikai biztonságát.](../security/azure-physical-security.md)

### <a name="equipment-location"></a>A berendezés helye

A privát felhőket futtató operációs rendszer nélküli hardverberendezések az Azure adatközpont-helyeken találhatók.  A ketrecek, ahol a berendezés, megköveteli konmetrikus alapú kétfaktoros hitelesítést, hogy hozzáférjenek.

## <a name="dedicated-hardware"></a>Dedikált hardver

A CloudSimple szolgáltatás részeként minden CloudSimple-ügyfél dedikált, csupasz fémgazdagépeket kap helyi csatlakoztatott lemezekkel, amelyek fizikailag elkülönülnek más bérlői hardverektől. Egy vSAN-nal rendelkező ESXi hipervizor fut minden csomóponton. A csomópontok kezelése az ügyfél által dedikált VMware vCenter és NSX. A hardver bérlők közötti megosztásának elnem e nélkül nem megosztása további elkülönítési és biztonsági szintű védelmet biztosít.

## <a name="data-security"></a>Adatbiztonság

Az ügyfelek továbbra is ellenőrzik és birtokolják az adataikat. Az ügyféladatok adatkezelési feladata az ügyfél felelőssége.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Az indóadatok és a belső hálózatokon belül mozgásban lévő adatok adatvédelme

A privát felhőkörnyezetben inaktív adatokhoz használhatja a vSAN titkosítást. A vSAN titkosítás a VMware által hitelesített külső kulcskezelő kiszolgálókkal (KMS) működik a saját virtuális hálózatában vagy a helyszínen.  Az adattitkosítási kulcsokat saját maga szabályozhatja. A privát felhőn belül mozgásban lévő adatok esetében a vSphere támogatja az adatok titkosítását a hálózaton keresztül az összes vmkernel forgalom (beleértve a vMotion-forgalmat is).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Adatvédelem a nyilvános hálózatokon való mozgáshoz szükséges adatokra

A nyilvános hálózatokon áthaladó adatok védelme érdekében IPsec és SSL VPN-alagutakat hozhat létre a privát felhők számára. A gyakori titkosítási módszerek támogatottak, beleértve a 128 bájtos és a 256 bájtos AES-t. Az átvitel alatt álló adatok (beleértve a hitelesítést, a rendszergazdai hozzáférést és az ügyféladatokat) szabványos titkosítási mechanizmusokkal (SSH, TLS 1.2 és Secure RDP) vannak titkosítva. A bizalmas adatokat szállító kommunikáció a szabványos titkosítási mechanizmusokat használja.

### <a name="secure-disposal"></a>Biztonságos ártalmatlanítás

Ha a CloudSimple szolgáltatás lejár, vagy levan állítva, Ön felelős az adatok eltávolításáért vagy törléséért. A CloudSimple együttműködik Önnel az ügyfélszerződésben megadott összes ügyféladat törlésében vagy visszaküldésében, kivéve, ha a CloudSimple-t a vonatkozó jogszabályok megkövetelik a személyes adatok egy részének vagy egészének megőrzéséhez. Ha a személyes adatok megőrzéséhez szükséges, a CloudSimple archiválja az adatokat, és ésszerű intézkedéseket hajt végre annak érdekében, hogy megakadályozza az ügyféladatok további feldolgozását.

### <a name="data-location"></a>Adatok helye

A privát felhők beállításakor kiválaszthatja azt az Azure-régiót, ahol üzembe helyezik őket. A VMware virtuálisgép-adatok csak akkor kerülnek áthelyezésre a fizikai adatközpontból, ha adatáttelepítést vagy telephelyen kívüli adatmentést hajt végre. Számítási feladatokat is üzemeltethet, és adatokat tárolhat több Azure-régióban, ha az igényeinek megfelelően.

A private cloud hiperkonvergens csomópontokban rezidens ügyféladatok nem haladnak át a helyeken a bérlői rendszergazda kifejezett művelete nélkül. Az Ön felelőssége, hogy a számítási feladatok magas rendelkezésre állású módon valósítsa meg.

### <a name="data-backups"></a>Adatok biztonsági mentése

A CloudSimple nem készít biztonsági másolatot az ügyféladatokról, és nem archiválja azadatokat. A CloudSimple rendszeres biztonsági másolatot készít a vCenter- és NSX-adatokról, hogy magas rendelkezésre állást biztosítson a felügyeleti kiszolgálók számára. A biztonsági mentés előtt az összes adat a vCenter-forrásban van titkosítva vmware API-k használatával. A titkosított adatok átvitele és tárolása az Azure blobban történik. A biztonsági mentések titkosítási kulcsait egy rendkívül biztonságos CloudSimple felügyelt tároló tárolja, amely az Azure-beli CloudSimple virtuális hálózaton fut.

## <a name="network-security"></a>Hálózati biztonság

A CloudSimple megoldás a hálózati biztonság rétegeire támaszkodik.

### <a name="azure-edge-security"></a>Az Azure peremhálózati biztonsága

A CloudSimple-szolgáltatások az Azure által biztosított alaphálózati biztonságra épülnek. Az Azure mélyreható technikákat alkalmaz az észleléshez és a rendellenes bejövő vagy kimenő forgalmi mintákhoz és az elosztott szolgáltatásmegtagadási (DDoS) támadásokhoz kapcsolódó hálózati alapú támadásokra való időben történő reagáláshoz. Ez a biztonsági vezérlő a Magánfelhő-környezetre és a CloudSimple által kifejlesztett vezérlősík-szoftverre vonatkozik.

### <a name="segmentation"></a>Szegmentálás

A CloudSimple szolgáltatás logikailag különálló Layer 2 hálózatokkal rendelkezik, amelyek korlátozzák a hozzáférést a saját magánhálózatához a privát felhőkörnyezetben. A magánfelhő-hálózatokat tűzfallal tovább védheti. A CloudSimple portál lehetővé teszi, hogy EW és NS hálózati forgalomvezérlő szabályokat határozzon meg az összes hálózati forgalomra vonatkozóan, beleértve a magánfelhő-n belüli forgalmat, a magánjellegű felhőforgalmat, az általános internetes forgalmat és a helyszíni hálózati forgalmat Az IPsec VPN-en keresztül vagy ExpressRoute-kapcsolat.

## <a name="vulnerability-and-patch-management"></a>A biztonsági rés és a javítás kezelése

A CloudSimple felelős a felügyelt VMware szoftverek (ESXi, vCenter és NSX) időszakos biztonsági javításáért.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Az ügyfelek hitelesíthetik magukat az Azure-fiókjukban (az Azure AD-ben) a többtényezős hitelesítés vagy az egyszeri bejelentkezés előnyben részesített használatával. Az Azure Portalon elindíthatja a CloudSimple portált a hitelesítő adatok újbóli megadása nélkül.

A CloudSimple támogatja a private cloud vCenter identitásforrásának választható konfigurálását. Használhatja [a helyszíni identitásforrás](set-vcenter-identity.md), egy új identitásforrás a magánfelhő, vagy az [Azure AD.](azure-ad.md)

Alapértelmezés szerint az ügyfelek megkapják a virtuális központ napi működéséhez szükséges jogosultságokat a magánfelhőn belül. Ez a jogosultsági szint nem tartalmazza a virtuális központhoz való rendszergazdai hozzáférést. Ha átmenetileg rendszergazdai hozzáférésre van szükség, a felügyeleti feladatok végrehajtása közben korlátozott [időtartamra bővítheti a jogosultságokat.](escalate-private-cloud-privileges.md)
