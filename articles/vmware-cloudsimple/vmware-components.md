---
title: Magánfelhős VMware-összetevők
titleSuffix: Azure VMware Solution by CloudSimple
description: A VMware-összetevők magánfelhőben való telepítésének ismertetése
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279506"
---
# <a name="private-cloud-vmware-components"></a>Privát felhőbeli VMware-összetevők

A magánfelhő egy elkülönített VMware-verem (ESXi gazdagépek, vCenter, vSAN és NSX) környezet, amelyet egy felügyeleti tartományban lévő vCenter-kiszolgáló kezel.  A CloudSimple szolgáltatás lehetővé teszi a VMware natív üzembe helyezését az Azure operációs rendszer nélküli infrastruktúrán az Azure-beli helyszíneken.  A privát felhők integrálva vannak az Azure Cloud többi részével.  A magánfelhő a következő VMware veremösszetevőkkel van telepítve:

* **VMware ESXi -** Hipervizor az Azure dedikált csomópontjain
* **VMware vCenter -** Készülék a magánfelhő vSphere környezet központosított kezeléséhez
* **VMware vSAN -** Hiperkonvergens infrastruktúra-megoldás
* **VMware NSX adatközpont -** Hálózati virtualizációs és biztonsági szoftver  

## <a name="vmware-component-versions"></a>VMware-összetevők verziói

A privát felhőbeli VMware-verem a következő szoftververzióval van telepítve.

| Összetevő | Verzió | Licencelt verzió |
|-----------|---------|------------------|
| Esxi | 6,7U2 | Enterprise Plus |
| vCenter | 6,7U2 | vCenter szabvány |
| vSAN között | 6.7 | Enterprise |
| NSX adatközpont | 2.4.1 | Speciális |

## <a name="esxi"></a>Esxi

A VMware ESXi a kiépített CloudSimple csomópontokra van telepítve, amikor privát felhőt hoz létre.  Az ESXi biztosítja a számítási feladatok virtuális gépek (VM-ek) üzembe helyezéséhez szolgáló hipervizort.  A csomópontok hiperkonvergens infrastruktúrát (számítási és tárolási) biztosítanak a magánfelhőben.  A csomópontok a vSphere-fürt részét képezik a magánfelhőben.  Minden csomópont négy fizikai hálózati kapcsolattal rendelkezik, amelyek az alávetítési hálózathoz csatlakoznak.  Két fizikai hálózati adapterek létrehozásához használt **vSphere elosztott switch (VDS)** a vCenter és két segítségével hozzon létre egy **NSX által kezelt virtuális elosztott kapcsoló (N-VDS)**.  A hálózati adapterek aktív-aktív módban vannak konfigurálva a magas rendelkezésre állás érdekében.

További információ a VMware ESXi-ről

## <a name="vcenter-server-appliance"></a>vCenter-kiszolgálóberendezés

VCenter server appliance (VCSA) biztosítja a hitelesítési, felügyeleti és vezénylési funkciók VMware megoldás a CloudSimple. A VCSA beágyazott platformszolgáltatások vezérlőjével (PSC) a magánfelhő létrehozásakor kerül üzembe helyezésre.  A VCSA a magánfelhő telepítésekor létrehozott vSphere-fürtön van telepítve.  Minden magánfelhő saját VCSA-val rendelkezik.  A magánfelhő bővítése hozzáadja a csomópontokat a VCSA-hoz a magánfelhőben.

### <a name="vcenter-single-sign-on"></a>vCenter egyszeri bejelentkezés

A VCSA beágyazott platformszolgáltatások vezérlője egy **vCenter Single Sign-On tartományhoz**van társítva.  A tartománynév **cloudsimple.local**.  Egy alapértelmezett **CloudOwner@cloudsimple.com** felhasználó jön létre a vCenter eléréséhez.  Hozzáadhatja a helyszíni/Azure active directory [identitásforrások at vCenter.](set-vcenter-identity.md)

## <a name="vsan-storage"></a>vSAN tárolás

A privát felhők a fürt helyi, teljesen konfigurált, teljesen konfigurált vSAN-tárolójával jönnek létre.  Ugyanannak a termékváltozatnak legalább három csomópontja szükséges egy vSAN-adattárral rendelkező vSphere-fürt létrehozásához.  A de-duplikáció és a tömörítés alapértelmezés szerint engedélyezve van a vSAN adattárban.  A vSphere-fürt minden csomópontján két lemezcsoport jön létre. Minden lemezcsoport egy gyorsítótár-lemezt és három kapacitáslemezt tartalmaz.

Egy alapértelmezett vSAN tárolási szabályzat jön létre a vSphere-fürtön, és a vSAN adattárban alkalmazva.  Ez a házirend határozza meg, hogy a virtuális gép tárolóobjektumok ki vannak építve és lefoglalva az adattárban a szükséges szolgáltatási szint biztosítása érdekében.  A tárolási házirend határozza meg a **tolerálható hibákat (FTT)** és a **Hiba tűréshatár módszert.**  Új tárolási szabályzatokat hozhat létre, és alkalmazhatja őket a virtuális gépekre. Az SLA fenntartásához 25%-os tartalékkapacitást kell fenntartani a vSAN adattárban.  

### <a name="default-vsan-storage-policy"></a>Alapértelmezett vSAN-tárolási házirend

Az alábbi táblázat az alapértelmezett vSAN tárolási házirend-paramétereket mutatja be.

| Csomópontok száma a vSphere-fürtben | FTT | Hibatűrési módszer |
|------------------------------------|-----|--------------------------|
| 3 és 4 csomópont | 1 | RAID 1 (tükrözés) - létrehoz 2 példányban |
| 5–16 csomópont | 2 | RAID 1 (tükrözés) - létrehoz 3 példányban |

## <a name="nsx-data-center"></a>NSX adatközpont

Az NSX Data Center hálózati virtualizációt, mikroszegmentálást és hálózati biztonsági lehetőségeket biztosít a magánfelhőben.  Az NSX Data Center által támogatott összes szolgáltatást a magánfelhőn keresztül konfigurálhatja.  Privát felhő létrehozásakor a következő NSX-összetevők települnek és konfigurálódnak.

* NSXT-kezelő
* Szállítási zónák
* Állomás- és peremhálózati kapcsolati profil
* Logikai kapcsoló az Élátvitelhez, az Ext1 és az Ext2-hez
* IP-készlet ESXi átviteli csomóponthoz
* IP-készlet élhálózati átviteli csomóponthoz
* Szegélycsomópontok
* DRS affinitásgátló szabály vezérlőhöz és peremhálózati virtuális gépekhez
* 0. rétegútválasztó
* BGP engedélyezése a Tier0 útválasztón

## <a name="vsphere-cluster"></a>vSphere klaszter

Az ESXi-állomások fürtként vannak konfigurálva a magánfelhő magas rendelkezésre állásának biztosítása érdekében.  Amikor létrehoz egy magánfelhőt, a vSphere felügyeleti összetevői az első fürtön kerülnek üzembe helyezésre.  Egy erőforráskészlet jön létre a felügyeleti összetevők és az összes felügyeleti virtuális gépek üzembe ebben az erőforráskészletben. Az első fürt nem törölhető a magánfelhő zsugorításához.  A vSphere cluster magas rendelkezésre állást biztosít a **vSphere HA**használatával használt virtuális gépek számára.  A tolerálható hibák a fürtben rendelkezésre álló csomópontok számán alapulnak.  Használhatja a ```Number of nodes = 2N+1``` képletet, ahol ```N``` a tolerálható hibák száma.

### <a name="vsphere-cluster-limits"></a>vSphere fürt korlátok

| Erőforrás | Korlát |
|----------|-------|
| A magánfelhő létrehozásához szükséges csomópontok minimális száma (első vSphere-fürt) | 3 |
| A vSphere-fürt csomópontjainak maximális száma magánfelhőben | 16 |
| A csomópontok maximális száma magánfelhőben | 64 |
| A vSphere-fürtök maximális száma magánfelhőben | 21 |
| Az új vSphere-fürt csomópontjainak minimális száma | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware infrastruktúra karbantartása

Esetenként szükség van a VMware-infrastruktúra konfigurációjának módosítására. Jelenleg ezek az intervallumok 1-2 havonta fordulhatnak elő, de a gyakoriság idővel várhatóan csökkenni fog. Az ilyen típusú karbantartás általában a CloudSimple-szolgáltatások normál felhasználásának megszakítása nélkül végezhető el. A VMware karbantartási időköze alatt a következő szolgáltatások továbbra is minden hatással járnak:

* VMware felügyeleti sík és alkalmazások
* vCenter-hozzáférés
* Minden hálózat és tárolás
* Minden Azure-forgalom

## <a name="updates-and-upgrades"></a>Frissítések és frissítések

A CloudSimple felelős a VMware-szoftverek (ESXi, vCenter, PSC és NSX) életciklus-kezeléséért a magánfelhőben.

A szoftverfrissítések a következők:

* **Javítások**. A VMware által kiadott biztonsági javítások vagy hibajavítások.
* **Frissítések**. A VMware veremösszetevő alverziómódosítása.
* **Frissítések**. A VMware veremösszetevő főverzióváltása.

A CloudSimple teszteli a kritikus biztonsági javítást, amint elérhetővé válik a VMware-ből. Az SLA-n ként a CloudSimple egy héten belül kivezeti a biztonsági javítást a magánfelhő-környezetekbe.

A CloudSimple negyedéves karbantartási frissítéseket biztosít a VMware szoftverösszetevőihez. Amikor a VMware szoftver új főverziója elérhető, a CloudSimple együttműködik az ügyfelekkel, hogy összehangolja a megfelelő karbantartási időszakot a frissítéshez.  

## <a name="next-steps"></a>További lépések

* [CloudSimple karbantartás és frissítések](cloudsimple-maintenance-updates.md)
