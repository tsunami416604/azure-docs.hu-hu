---
title: Azure VMware Solutions (AVS) – AVS Private Cloud VMware-összetevők
description: Leírja, hogyan települnek a VMware-összetevők az AVS Private Cloud-ra
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016782"
---
# <a name="avs-private-cloud-vmware-components"></a>AVS Private Cloud VMware-összetevők

Az AVS Private Cloud egy elszigetelt VMware stack (ESXi hosts, vCenter, vSAN és NSX) környezet, amelyet egy vCenter-kiszolgáló felügyel egy felügyeleti tartományban. Az AVS szolgáltatás lehetővé teszi, hogy natív módon telepítse a VMware-et az Azure-beli operációs rendszer nélküli Azure-infrastruktúrában. Az AVS Private felhők integrálva vannak az Azure-felhő többi részébe. Az AVS Private Cloud üzembe helyezése a következő VMware stack-összetevőkkel történik:

* **VMWare ESXi –** Hypervisor az Azure dedikált csomópontjain
* **VMware vCenter –** Készülék az AVS Private Cloud vSphere-környezet központi felügyeletéhez
* **VMware vSAN –** Hyper-konvergens infrastruktúra-megoldás
* **VMware NSX adatközpont –** Hálózati virtualizálási és biztonsági szoftverek  

## <a name="vmware-component-versions"></a>VMware-összetevők verziói

Az AVS Private Cloud VMware stack a következő szoftveres verzióval van üzembe helyezve.

| Component (Összetevő) | Verzió | Licencelt verzió |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | vCenter standard |
| vSAN | 6.7 | Enterprise |
| NSX adatközpont | 2.4.1 | Extra szintű |

## <a name="esxi"></a>ESXi

A VMware ESXi a kiépített AVS-csomópontokra van telepítve az AVS Private Cloud létrehozásakor. Az ESXi biztosítja a számítási feladatok virtuális gépei (VM-EK) üzembe helyezését. A csomópontok Hyper-konvergens infrastruktúrát (számítási és tárolási) biztosítanak az AVS privát felhőben. A csomópontok a vSphere-fürt részét képezik az AVS Private Cloud-on. Mindegyik csomóponthoz négy fizikai hálózati adapter csatlakozik az alátét hálózathoz. A rendszer két fizikai hálózati adaptert használ egy **vSphere elosztott kapcsoló (VDS)** létrehozására a vCenter, a kettő pedig egy **NSX által felügyelt virtuális elosztott kapcsoló (N-VDS)** létrehozásához használatos. A hálózati adapterek aktív-aktív módban vannak konfigurálva a magas rendelkezésre állás érdekében.

További információ a VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter-kiszolgáló készülék

a vCenter Server Appliance (VCSA) a VMware-megoldások (AVS) hitelesítési, felügyeleti és előkészítési funkcióit biztosítja. Az AVS Private Cloud létrehozásakor a rendszer üzembe helyezi az VCSA-t a Embedded platform Services-vezérlővel (PSC). A VCSA a vSphere-fürtön van telepítve, amely az AVS Private Cloud telepítésekor jön létre. Mindegyik AVS Private Cloud saját VCSA rendelkezik. Az AVS Private Cloud bővítése hozzáadja a csomópontokat az AVS Private Cloud VCSA.

### <a name="vcenter-single-sign-on"></a>vCenter egyszeri bejelentkezés

A VCSA Embedded platform Services-vezérlője egy **vCenter egyszeri bejelentkezési tartománnyal**van társítva. A tartománynév az **AVS. local**nevet adja meg. A rendszer létrehoz egy alapértelmezett felhasználói **CloudOwner@AVS.com** a vCenter eléréséhez. Felveheti a helyszíni/Azure Active Directory [Identity sources for vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>vSAN-tároló

Az AVS Private-felhők teljesen konfigurált összes Flash vSAN-tárolóval jönnek létre a fürtben. Az vSAN adattárral rendelkező vSphere-fürtök létrehozásához legalább három csomópontnak kell futnia ugyanazon SKU-ban. A vSAN adattárolón alapértelmezés szerint engedélyezve van a Duplikálás és a tömörítés. Két lemezcsoport jön létre a vSphere-fürt minden egyes csomópontján. Mindegyik lemezcsoport egy gyorsítótár-lemezt és három kapacitású lemezt tartalmaz.

A rendszer létrehoz egy alapértelmezett vSAN-tárolási házirendet a vSphere-fürtön, és alkalmazza őket a vSAN adattárra. Ez a házirend határozza meg, hogy a virtuális gép tárolási objektumai hogyan legyenek kiépítve és lefoglalva az adattáron belül, hogy garantálható legyen a szükséges szolgáltatási szint. A tárolási házirend határozza meg a **tolerálható (tranzakciós)** és a **hibatűrési módszer**hibáit. Új tárolási szabályzatokat hozhat létre, és alkalmazhatja őket a virtuális gépekre. Az SLA fenntartása érdekében a 25%-os tartalék kapacitást a vSAN adattáron kell karbantartani. 

### <a name="default-vsan-storage-policy"></a>Alapértelmezett vSAN tárolási házirend

Az alábbi táblázat a vSAN alapértelmezett tárolási házirendjének paramétereit mutatja be.

| Csomópontok száma a vSphere-fürtben | FTT | Hibatűrési módszer |
|------------------------------------|-----|--------------------------|
| 3 és 4 csomópont | 1 | RAID 1 (Tükrözés) – 2 másolat létrehozása |
| 5 – 16 csomópont | 2 | RAID 1 (Tükrözés) – 3 másolat létrehozása |

## <a name="nsx-data-center"></a>NSX adatközpont

A NSX adatközpont hálózati virtualizációs, mikro-szegmentálási és hálózati biztonsági képességeket biztosít az AVS Private-felhőben. Az NSX-adatközpont által támogatott összes szolgáltatást a NSX használatával konfigurálhatja az AVS privát felhőben. Ha létrehoz egy AVS Private-felhőt, a következő NSX-összetevők vannak telepítve és konfigurálva.

* NSXT-kezelő
* Szállítási zónák
* Gazdagép és Edge kimenő profil
* Logikai kapcsoló az Edge Transport, az Ext1 és az EXT2 rendszerhez
* Az ESXi átviteli csomópontjának IP-készlete
* Az Edge Transport csomópont IP-készlete
* Peremhálózati csomópontok
* DRS-affinitási szabály a vezérlő és a peremhálózati virtuális gépek számára
* 0\. rétegű útválasztó
* A BGP engedélyezése a Tier0-útválasztón

## <a name="vsphere-cluster"></a>vSphere-fürt

Az ESXi-gazdagépek fürtként vannak konfigurálva, hogy magas rendelkezésre állást biztosítsanak az AVS privát felhő számára. Az AVS Private Cloud létrehozásakor a rendszer az első fürtön telepíti a vSphere felügyeleti összetevőit. Létrejön egy erőforráskészlet a felügyeleti összetevőkhöz, és az összes felügyeleti virtuális gép üzembe van hozva ebben az erőforráskészletban. Az első fürtöt nem lehet törölni az AVS Private Cloud csökkentése érdekében. a vSphere-fürt magas rendelkezésre állást biztosít a **vSphere**-t használó virtuális gépek számára. A tolerálható hibák a fürtben elérhető csomópontok számától függenek. Használhatja a ```Number of nodes = 2N+1``` képletet, ahol ```N``` az elviselni kívánt hibák száma.

### <a name="vsphere-cluster-limits"></a>vSphere-fürt korlátai

| Erőforrás | Korlát |
|----------|-------|
| Az AVS Private Cloud (első vSphere-fürt) létrehozásához szükséges csomópontok minimális száma | 3 |
| VSphere-fürtben lévő csomópontok maximális száma egy AVS Private-felhőben | 16 |
| Csomópontok maximális száma egy AVS Private-felhőben | 64 |
| VSphere-fürtök maximális száma egy AVS Private-felhőben | 21 |
| Csomópontok minimális száma egy új vSphere-fürtön | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware-infrastruktúra karbantartása

Időnként szükség van a VMware-infrastruktúra konfigurációjának módosítására. Jelenleg ezek az intervallumok minden 1-2 hónapban előfordulhatnak, de a gyakoriság várhatóan az idő múlásával csökken. Az ilyen típusú karbantartás általában az AVS-szolgáltatások normál felhasználásának megszakítása nélkül végezhető el. A VMware karbantartási időköze során a következő szolgáltatások továbbra is hatással vannak a működésre:

* VMware felügyeleti sík és alkalmazások
* vCenter-hozzáférés
* Minden Hálózatkezelés és tárterület
* Minden Azure-forgalom

## <a name="updates-and-upgrades"></a>Frissítések és frissítések

Az AVS a VMware-szoftverek (ESXi, vCenter, PSC és NSX) életciklus-kezelési feladata az AVS Private Cloud-ban.

A szoftverfrissítések a következők:

* **Javítások**. A VMware által kiadott biztonsági javítások vagy hibajavítások.
* **Frissítések**. Egy VMware stack-összetevő másodlagos verziója megváltozik.
* **Frissítések**. VMware stack-összetevő főverziójának módosítása.

Az AVS kritikus biztonsági javítást végez, amint elérhetővé válik a VMware-ről. SLA-n belül az AVS a biztonsági javítást az AVS Private Cloud Environments szolgáltatásba összesíti.

Az AVS negyedéves karbantartási frissítéseket biztosít a VMware szoftver összetevőihez. Ha elérhetővé válik a VMware szoftver új főverziója, az AVS együttműködik az ügyfelekkel, hogy koordinálja a megfelelő karbantartási időszakot a frissítéshez. 

## <a name="next-steps"></a>Következő lépések

* [AVS-karbantartás és-frissítések](cloudsimple-maintenance-updates.md)
