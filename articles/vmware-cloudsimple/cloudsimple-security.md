---
title: A CloudSimple Services biztonsága
description: Ismerteti a CloudSimple-szolgáltatások biztonságának közös felelősségi modelljeit
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff1bd3c6e1f3cf98e92e10eecf972681ed6c7819
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816179"
---
# <a name="cloudsimple-security-overview"></a>A CloudSimple biztonsági áttekintése

Ez a cikk áttekintést nyújt arról, hogy a CloudSimple szolgáltatás, az infrastruktúra és az adatközpont hogyan valósítja meg a biztonságot az Azure VMware-megoldáson. Megismerheti az adatvédelem és a biztonság, a hálózati biztonság, valamint a biztonsági rések és javítások kezelését.

## <a name="shared-responsibility"></a>Közös felelősség

A CloudSimple által nyújtott Azure VMware-megoldás megosztott felelősségi modellt használ a biztonsághoz. A Felhőbeli megbízható biztonság az ügyfelek és a Microsoft szolgáltatói felelősségével érhető el. A felelősségi mátrix nagyobb biztonságot nyújt, és kiküszöböli az egyes meghibásodási pontokat.

## <a name="azure-infrastructure"></a>Azure-infrastruktúra 

Az Azure-infrastruktúra biztonsági szempontjai tartalmazzák az adatközpontok és a berendezések helyét.

### <a name="datacenter-security"></a>Adatközpont biztonsága 

A Microsoft egy teljes részleggel rendelkezik, amely az Azure-t támogató fizikai létesítmények tervezésével, létrehozásával és működtetésével foglalkozik. A csapat a legkorszerűbb fizikai biztonság fenntartásával lett befektetve. A fizikai biztonsággal kapcsolatos további információkért lásd: [Azure-létesítmények,-telephely és fizikai biztonság https://docs.microsoft.com/azure/security/fundamentals/physical-security ] (.

### <a name="equipment-location"></a>Berendezések helye

A privát felhőket futtató operációs rendszer nélküli hardvereszközök az Azure-adatközpont helyein futnak. A biometrikus alapú kétfaktoros hitelesítés szükséges ahhoz, hogy hozzáférjenek a ketrecekhez, ahol a berendezés található.

## <a name="dedicated-hardware"></a>Dedikált hardver

A CloudSimple szolgáltatás részeként minden CloudSimple-ügyfél dedikált operációs rendszer nélküli gazdagépeket kap, amelyek helyi csatlakoztatott lemezekkel rendelkeznek, amelyek fizikailag el vannak különítve a többi bérlői hardvertől. VSAN-t futtató ESXi hypervisor minden csomóponton. A csomópontokat az ügyfél által dedikált VMware, vCenter és NSX kezelik. Ha nem oszt meg hardvert a bérlők között, egy további elkülönítési réteget és biztonsági védelmet biztosít.

## <a name="data-security"></a>Adatbiztonság

Az ügyfelek továbbra is megőrzik az adatkezelést és a tulajdonjogot. A vásárlói adatkezelés az ügyfél feladata.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Inaktív adatok és a belső hálózatokon belüli adatok védelme

A privát felhőalapú környezetben lévő inaktív adatok esetében vSAN titkosítást is használhat. a vSAN-titkosítás VMware-tanúsítvánnyal rendelkező külső kulcskezelő kiszolgálókkal (KMS) működik a saját virtuális hálózatában vagy a helyszínen. Saját maga vezérli az adattitkosítási kulcsokat. A vSphere a privát felhőben lévő adatok esetében támogatja az adatok titkosítását a huzalon keresztül az összes VMkernel-forgalom számára, amely magában foglalja a vMotion-forgalmat is.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Adatvédelem a nyilvános hálózatokon való áthelyezéshez szükséges adatvédelemben

A nyilvános hálózatokon átmenő adatforgalom védelme érdekében létrehozhat IPsec-és SSL VPN-alagutakat a privát felhők számára. Az általános titkosítási módszerek támogatottak, beleértve a 128 bájtos és a 256 bájtos AES-t. Az átvitt, valamint a hitelesítést, a rendszergazdai hozzáférést és az ügyféladatokat tartalmazó adatforgalom szabványos titkosítási mechanizmusokkal (például SSH, TLS 1,2 és Secure RDP) titkosított. A bizalmas adatokat továbbító kommunikáció a szabványos titkosítási mechanizmusokat használja.

### <a name="secure-disposal"></a>Biztonságos kivezetés 

Ha a CloudSimple szolgáltatás lejár vagy leállt, az Ön felelőssége, hogy eltávolítsa vagy törli az adatait. A CloudSimple együttműködik Önnel, hogy törölje vagy visszaállítsa az összes vásárlói adatát az ügyfél-szerződésben foglaltak szerint, kivéve, ha az alkalmazandó jogszabályok a személyes adat egy részét vagy egészét megőrzik a CloudSimple. Ha a személyes adatok megőrzéséhez szükséges, a CloudSimple archiválja az adatait, és ésszerű mértékeket valósít meg, hogy megakadályozza a vásárlói adatok további feldolgozását.

### <a name="data-location"></a>Az adatok helye

A privát felhők beállításakor ki kell választania az Azure-régiót, ahol üzembe vannak helyezve. A VMware virtuális gép adatai nem kerülnek át az adott fizikai adatközpontból, kivéve, ha adatáttelepítést vagy telephelyen kívüli biztonsági mentést végeznek Az igényeknek megfelelően több Azure-régióban is üzemeltetheti a számítási feladatokat, és tárolhatja azokat.

A saját felhőalapú hiperkonvergens-csomópontokban rezidens ügyféladatok nem a bérlői rendszergazda kifejezett művelete nélkül haladnak át a helyen. Az Ön felelőssége, hogy a számítási feladatokat nagyon is elérhető módon implementálja.

### <a name="data-backups"></a>Az adatbiztonsági másolatok
A CloudSimple nem készít biztonsági mentést vagy archiválja az ügyféladatokat. A CloudSimple a vCenter és a NSX adatok rendszeres biztonsági mentését végzi a felügyeleti kiszolgálók magas rendelkezésre állásának biztosítása érdekében. A biztonsági mentés előtt a VMware API-k használatával a rendszer az összes adattitkosítást a vCenter-forrásban titkosítja. A titkosított adatforgalom egy Azure-blobban van elküldve és tárolva. A biztonsági mentések titkosítási kulcsait egy, az Azure CloudSimple virtuális hálózatán futó, szigorúan biztonságos CloudSimple felügyelt tároló tárolja.

## <a name="network-security"></a>Hálózati biztonság

A CloudSimple megoldás a hálózati biztonság rétegeire támaszkodik.

### <a name="azure-edge-security"></a>Azure Edge-biztonság

A CloudSimple szolgáltatások az Azure által biztosított alap hálózati biztonságra épülnek. Az Azure a rendellenes bejövő vagy kimenő forgalmi mintákhoz és az elosztott szolgáltatásmegtagadási (DDoS) támadásokhoz kapcsolódó hálózati támadások észleléséhez és időben történő megválaszolásához nyújt védelmet részletes technikákat. Ez a biztonsági ellenőrzés a CloudSimple által fejlesztett saját felhőalapú környezetekre és a vezérlő síkja szoftverre vonatkozik.

### <a name="segmentation"></a>Szegmentálás

A CloudSimple szolgáltatás logikailag elkülöníti a 2. rétegbeli hálózatokat, amelyek korlátozzák a saját privát hálózatokhoz való hozzáférést a saját felhőalapú környezetében. A saját felhőalapú hálózatait tűzfal használatával tovább védetté teheti. A CloudSimple-portálon az összes hálózati forgalomra vonatkozó kelet-nyugati és észak-déli hálózati forgalom vezérlési szabályait definiáljuk, beleértve a belső Felhőbeli forgalmat, a belső felhőalapú forgalmat, az internetre irányuló általános forgalmat, valamint a helyszíni hálózati forgalmat. IPsec VPN-vagy Azure ExpressRoute-kapcsolaton keresztül.

## <a name="vulnerability-and-patch-management"></a>Sebezhetőség és javítás kezelése 

A CloudSimple feladata a felügyelt VMware-szoftverek, például az ESXi, a vCenter és a NSX rendszeres biztonsági javítása.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Az ügyfelek az Azure-fiókkal (Azure Active Directory) hitelesíthetők a többtényezős hitelesítés vagy az egyszeri bejelentkezés használata esetén. A Azure Portal a hitelesítő adatok újbóli megadása nélkül is elindíthatja a CloudSimple-portált.

A CloudSimple támogatja a saját Felhőbeli vCenter tartozó személyazonossági forrás opcionális konfigurációját. Használhatja [a helyszíni identitás forrását](https://docs.azure.cloudsimple.com/set-vcenter-identity), a saját felhőhöz tartozó új személyazonossági forrást, vagy a [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Alapértelmezés szerint az ügyfelek a privát felhőben a vCenter napi műveleteihez szükséges jogosultságokat kapják meg. Ez a jogosultsági szint nem tartalmaz rendszergazdai hozzáférést a vCenter. Ha átmenetileg szükség van a rendszergazdai hozzáférésre, [](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) akkor a rendszergazdai feladatok elvégzése után a jogosultságokat korlátozott időtartamra is kiterjesztheti.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre CloudSimple szolgáltatást az Azure](quickstart-create-cloudsimple-service.md)-ban.
* Megtudhatja, hogyan [hozhat létre privát felhőket](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Megtudhatja, hogyan [konfigurálhat egy saját felhőalapú környezetet](quickstart-create-private-cloud.md).
