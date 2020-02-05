---
title: Azure VMware Solutions (AVS) – az AVS-szolgáltatások biztonsága
description: Ismerteti az AVS-szolgáltatások biztonságának megosztott felelősségi modelljeit
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024993"
---
# <a name="avs-security-overview"></a>Az AVS biztonsági áttekintése

Ez a cikk áttekintést nyújt arról, hogy az AVS szolgáltatás, az infrastruktúra és az adatközpont hogyan valósítja meg a biztonságot az Azure VMware-megoldáson. Megismerheti az adatvédelem és a biztonság, a hálózati biztonság, valamint a biztonsági rések és javítások kezelését.

## <a name="shared-responsibility"></a>Közös felelősség

Az AVS által nyújtott Azure VMware-megoldás egy megosztott felelősségi modellt használ a biztonsághoz. A Felhőbeli megbízható biztonság az ügyfelek és a Microsoft szolgáltatói felelősségével érhető el. A felelősségi mátrix nagyobb biztonságot nyújt, és kiküszöböli az egyes meghibásodási pontokat.

## <a name="azure-infrastructure"></a>Azure-infrastruktúra

Az Azure-infrastruktúra biztonsági szempontjai tartalmazzák az adatközpontok és a berendezések helyét.

### <a name="datacenter-security"></a>Adatközpont biztonsága

A Microsoft egy teljes részleggel rendelkezik, amely az Azure-t támogató fizikai létesítmények tervezésével, létrehozásával és működtetésével foglalkozik. A csapat a legkorszerűbb fizikai biztonság fenntartásával lett befektetve. A fizikai biztonsággal kapcsolatos részletekért tekintse meg az [Azure-létesítményeket, a telephelyeket és a fizikai biztonságot](../security/azure-physical-security.md).

### <a name="equipment-location"></a>Berendezések helye

Az AVS Private-felhőket futtató operációs rendszer nélküli hardveres hardvereszközök az Azure-adatközpontok helyein futnak. Azok a ketrecek, amelyeken a berendezés van, biometrikus alapú kéttényezős hitelesítést igényelnek a hozzáféréshez.

## <a name="dedicated-hardware"></a>Dedikált hardver

Az AVS szolgáltatás részeként minden AVS-ügyfél dedikált operációs rendszer nélküli gazdagépeket kap, amelyek helyi csatlakoztatott lemezekkel rendelkeznek, amelyek fizikailag el vannak különítve a többi bérlői hardvertől. VSAN-t futtató ESXi hypervisor minden csomóponton. A csomópontokat az ügyfél dedikált VMware vCenter és NSX kezeli. Ha nem oszt meg hardvert a bérlők között, egy további elkülönítési réteget és biztonsági védelmet biztosít.

## <a name="data-security"></a>Adatbiztonság

Az ügyfelek továbbra is megőrzik az adatkezelést és a tulajdonjogot. A vásárlói adatkezelés az ügyfél feladata.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Inaktív adatok és a belső hálózatokon belüli adatok védelme

Az AVS Private Cloud-környezetben található inaktív adatok esetében vSAN titkosítást is használhat. a vSAN titkosítás a VMware Certified külső kulcskezelő kiszolgálókkal (KMS) működik a saját virtuális hálózatában vagy a helyszínen. Saját maga vezérli az adattitkosítási kulcsokat. Az AVS Private Cloud-on belüli adatok esetében a vSphere támogatja az adatok titkosítását a huzalon az összes vmkernel-forgalomhoz (beleértve a vMotion-forgalmat is).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Adatvédelem a nyilvános hálózatokon való áthelyezéshez szükséges adatvédelemben

A nyilvános hálózatokon átmenő adatforgalom védelme érdekében létrehozhat IPsec-és SSL VPN-alagutakat az AVS privát Felhőkhöz. Az általános titkosítási módszerek támogatottak, beleértve a 128 bájtos és a 256 bájtos AES-t. Az átvitt adatforgalom (beleértve a hitelesítést, a rendszergazdai hozzáférést és a vásárlói adatforgalmat) szabványos titkosítási mechanizmusokkal (SSH, TLS 1,2 és biztonságos RDP) van titkosítva. A bizalmas adatokat továbbító kommunikáció a szabványos titkosítási mechanizmusokat használja.

### <a name="secure-disposal"></a>Biztonságos kivezetés

Ha az AVS-szolgáltatás lejár vagy leáll, az Ön felelőssége, hogy eltávolítsa vagy törli az adatait. Az AVS együttműködik Önnel, hogy törölje vagy visszaállítsa az összes vásárlói adatát az ügyfél-szerződésben foglaltak szerint, kivéve, ha az alkalmazandó jogszabályok a személyes adat egy részét vagy egészét megőrzik. Ha a személyes adatok megőrzéséhez szükséges, az AVS archiválja az adatait, és ésszerű mértékeket valósít meg, hogy megakadályozza a vásárlói adatok további feldolgozását.

### <a name="data-location"></a>Az adatok helye

Az AVS privát felhők beállításakor ki kell választania azt az Azure-régiót, ahol üzembe szeretné helyezni őket. A VMware virtuális gépek adatait nem helyezi át az adott fizikai adatközpontból, kivéve, ha adatáttelepítést vagy telephelyen kívüli biztonsági mentést végez. Az igényeknek megfelelően több Azure-régióban is üzemeltetheti a számítási feladatokat, és tárolhatja azokat.

Az AVS Private Cloud Hyper-konvergált csomópontokban rezidens ügyféladatok nem a bérlői rendszergazda kifejezett művelete nélkül haladnak át a helyen. Az Ön felelőssége, hogy a számítási feladatokat nagyon is elérhető módon implementálja.

### <a name="data-backups"></a>Az adatbiztonsági másolatok

Az AVS nem készít biztonsági mentést vagy archiválja az ügyféladatokat. Az AVS az vCenter-és NSX-adatok rendszeres biztonsági mentését végzi a felügyeleti kiszolgálók magas rendelkezésre állásának biztosítása érdekében. A biztonsági mentést megelőzően az összes adattitkosítás a vCenter-forrásban a VMware API-k használatával történik. A titkosított adatforgalom az Azure-blobban van elküldve és tárolva. A biztonsági mentések titkosítási kulcsait az Azure-ban az AVS virtuális hálózatban futó, nagyon biztonságos, AVS által felügyelt tároló tárolja.

## <a name="network-security"></a>Hálózati biztonság

Az AVS-megoldás a hálózati biztonság rétegeire támaszkodik.

### <a name="azure-edge-security"></a>Azure Edge-biztonság

Az AVS-szolgáltatások az Azure által biztosított alap hálózati biztonságra épülnek. Az Azure a rendellenes bejövő vagy kimenő forgalmi mintákhoz és az elosztott szolgáltatásmegtagadási (DDoS) támadásokhoz kapcsolódó hálózati támadások észleléséhez és időben történő megválaszolásához nyújt védelmet részletes technikákat. Ez a biztonsági ellenőrzés az AVS Private Cloud Environments és az AVS által fejlesztett Control Plane szoftverre vonatkozik.

### <a name="segmentation"></a>Szegmentálás

Az AVS szolgáltatás logikailag elkülöníti a 2. rétegbeli hálózatokat, amelyek korlátozzák a saját privát hálózatokhoz való hozzáférést az AVS Private Cloud-környezetben. Az AVS Private Cloud Networks szolgáltatás tűzfallal is védhető. Az AVS-portál lehetővé teszi az EW és az NS hálózati forgalom szabályozásának meghatározását az összes hálózati forgalomra vonatkozóan, beleértve a belső AVS-felhő forgalmát, a privát felhőalapú forgalmat, az internetre irányuló általános forgalmat, valamint a helyszíni hálózati forgalmat az IPsec VPN-en keresztül. ExpressRoute-kapcsolatok.

## <a name="vulnerability-and-patch-management"></a>Sebezhetőség és javítás kezelése

Az AVS feladata a felügyelt VMware-szoftverek (ESXi, vCenter és NSX) rendszeres biztonsági javításának elvégzése.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Az ügyfelek az Azure-fiókban (az Azure AD-ben) a többtényezős hitelesítést vagy az SSO-t használják előnyben részesítettként. A Azure Portal a hitelesítő adatok újbóli megadása nélkül is elindíthatja az AVS-portált.

Az AVS az AVS Private Cloud vCenter nem kötelezővé teszi a személyazonossági forrás opcionális konfigurációját. Használhatja [a helyszíni identitás forrását](set-vcenter-identity.md), az AVS Private Cloud vagy az [Azure ad](azure-ad.md)új identitási forrását.

Alapértelmezés szerint az ügyfelek az AVS Private Cloud-on belüli vCenter napi műveleteihez szükséges jogosultságokat kapják meg. Ez a jogosultsági szint nem tartalmaz rendszergazdai hozzáférést a vCenter. Ha átmenetileg szükség van a rendszergazdai hozzáférésre, akkor a rendszergazdai feladatok elvégzése után a jogosultságokat korlátozott időtartamra is [kiterjesztheti](escalate-private-cloud-privileges.md) .
