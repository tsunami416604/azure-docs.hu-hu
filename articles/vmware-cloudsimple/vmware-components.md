---
title: Az Azure VMware-megoldások CloudSimple - magánfelhő VMware-összetevők
description: Ismerteti, hogyan legyenek telepítve a VMware-összetevők a magánfelhőben
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160185"
---
# <a name="private-cloud-vmware-components"></a>Magánfelhő VMware-összetevők

A magánfelhő esetében egy elkülönített VMware-környezetet (ESXi-gazdagépek, vCenter, vsan-hoz és NSX) környezet kezeli a vCenter-kiszolgáló, egy felügyeleti tartományban.  A CloudSimple szolgáltatás lehetővé teszi, hogy VMware natív módon az Azure-helyen lévő Azure operációs rendszer nélküli infrastruktúra üzembe helyezését.  Magánfelhők integrálva vannak az Azure Cloud részeinek.  A következő VMware összetevőinek magánfelhőbe lett telepítve:

* **VMware ESXi -** az Azure hipervizor dedikált csomópontok
* **VMware vCenter -** magánfelhő vSphere környezet központi felügyeletét a készülék
* **A VMware vSAN -** infrastruktúra hiperkonvergens megoldás
* **VMware-NSX adatközpont -** a hálózati virtualizálás és a biztonsági szoftver  

## <a name="vmware-component-versions"></a>VMware-összetevő verziója

A magánfelhő VMware-környezetet a következő szoftver verziója van telepítve.

| Összetevő | Version | Licencelt verzióra |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Vállalati |
| NSX Data Center | 2.3 | Speciális |

## <a name="esxi"></a>ESXi

VMware ESXi magánfelhő létrehozásakor beszerzett CloudSimple csomópontjára telepítse.  ESXi a hipervizor biztosít a számítási feladatok virtuális gépek (VM) üzembe helyezéséről.  Csomópontok saját magánfelhő-alapú hiperkonvergens infrastruktúrát (számítási és tárolási) ad.  A csomópontok a vSphere-fürt a magánfelhő részei.  Mindegyik fürtcsomópont négy felületek fizikai hálózatokhoz csatlakozó underlay hálózati.  Két fizikai hálózati adapter használatával létrehozhat egy **vSphere elosztott kapcsoló (VDS)** vCenter és két használatával létrehozhat egy **NSX felügyelte elosztott virtuáliskapcsoló (N-VDS)** .  Hálózati adapterek vannak konfigurálva a magas rendelkezésre állású aktív – aktív üzemmód.

További információ a VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter server-készüléken

vCenter server-készüléken (VCSA) CloudSimple VMware megoldást, ha a hitelesítés, felügyeleti és vezénylési funkciókat nyújt. A beágyazott Platform szolgáltatások tartományvezérlő (PSC) VCSA van üzembe helyezve, a magánfelhő létrehozásakor.  A vSphere-fürtökön a magánfelhő-alapú központi telepítésekor létrehozott VCSA telepítve van.  Minden egyes magánfelhő a saját VCSA rendelkezik.  Magánfelhő bővítése a csomópontok hozzáadása a VCSA a magánfelhőbe.

### <a name="vcenter-single-sign-on"></a>vCenter egyszeri bejelentkezéshez

Beágyazott Platform szolgáltatások vezérlő a VCSA társítva van egy **vCenter egyszeri bejelentkezés tartomány**.  A tartománynév **cloudsimple.local**.  Egy alapértelmezett felhasználói **CloudOwner@cloudsimple.com** jön létre, hogy hozzáférhessen a vCenter.  A helyszíni vagy Azure active Directoryban hozzáadhat [identitás források vcenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>vSAN storage

Magánfelhők teljesen konfigurált teljes mértékben flash vsan-hoz, storage, a fürt helyi címekkel jönnek létre.  Az azonos termékváltozat minimális három csomóponttal szükségesek egy vSphere-fürt létrehozása a vSAN-adattárhoz.  A deduplikáció és a tömörítés engedélyezve van a vsan-hoz az adattárban alapértelmezés szerint.  Két lemez csoportok jönnek létre a vSphere-fürt mindegyik csomópontján. Minden csoport egy gyorsítótárazási lemezhez és a kapacitás három lemezt tartalmaz.

Egy alapértelmezett vSAN tárolási a vSphere-fürtön létrehozott, és alkalmazza a vSAN-adattárhoz.  Ez a szabályzat határozza meg, hogyan a virtuális gép tárolóobjektumokhoz kiépített és lefoglalt belül a szükséges szolgáltatási szintet garantálja az adattárhoz.  A storage szabályzat határozza meg a **kezelni kívánt meghibásodások (FTT)** és a **hiba tolerancia metódus**.  Új tárolási szabályzatokat hozhat létre, és azokat a virtuális gépekre vonatkozik. SLA-t fenntartásához, 25 %-os tartalék kapacitással fenn kell tartani a vsan-hoz az adattárban.  

### <a name="default-vsan-storage-policy"></a>Alapértelmezett vsan-hoz a storage házirend

Az alábbi táblázat az alapértelmezett tároló szabályzatparaméterek vsan-hoz.

| A vSphere fürt csomópontjainak számát | FTT | Hiba tolerancia metódus |
|------------------------------------|-----|--------------------------|
| 3 – 4 csomópont | 1 | RAID 1 (tükrözés) – 2 másolatot hoz létre. |
| 5 – 16 csomópontok | 2 | RAID 1 (tükrözés) – 3 másolattal hoz létre. |

## <a name="nsx-data-center"></a>NSX Data Center

NSX adatközpont hálózatvirtualizálás, a micro szegmentálását és a hálózati biztonsági képességeket nyújt a magánfelhő.  A magánfelhőben keresztül NSX NSX adatközpont által támogatott összes szolgáltatás konfigurálható.  Magánfelhő létrehozásakor a következő NSX összetevők telepítve és konfigurálva.

* NSXT Manager
* Átviteli zónák
* Gazdagép és a peremhálózati kimenőport-profil
* Az Edge átviteli, Ext1 és Ext2 logikai kapcsoló
* ESXi-átviteli csomóponthoz tartozó IP-készlet
* IP-címkészletet az Élcsomópontra szállítás
* Élcsomópontok
* Vezérlő és a peremhálózati virtuális gépek affinitást megakadályozó beállítása DRS szabály
* 0\. rétegbeli útválasztó
* A réteg0 útválasztón BGP engedélyezése

## <a name="vsphere-cluster"></a>a vSphere-fürt

ESXi-gazdagépek a magánfelhő magas rendelkezésre állásának biztosításához fürtként vannak konfigurálva.  Magánfelhő létrehozásakor vSphere felügyeleti összetevői telepítve vannak, az első fürtöt.  Erőforráskészlet jön létre összetevőket, és ez az erőforráskészlet összes felügyeleti virtuális gépek vannak üzembe helyezve. Az első fürt nem lehet törölni a magánfelhő zsugorítani.  a vSphere-fürt magas rendelkezésre állást biztosít a virtuális gépek használatával **vSphere magas rendelkezésre ÁLLÁSÚ**.  Kezelni kívánt meghibásodások érhető el a fürtben található csomópontok számát alapul.  A képlet használható ```Number of nodes = 2N+1``` ahol ```N``` kezelni kívánt meghibásodások száma.

### <a name="vsphere-cluster-limits"></a>a vSphere-fürt korlátok

| Resource | Korlát |
|----------|-------|
| Magánfelhő létrehozása a csomópontok minimális száma (első vSphere-fürt) | 3 |
| A magánfelhőbe fürt egy vsphere csomópontok maximális száma | 16 |
| A magánfelhőben csomópontok maximális száma | 64 |
| A magánfelhőben fürtök vSphere maximális száma | 21 |
| Egy új vSphere fürtben lévő csomópontok minimális száma | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware-infrastruktúra karbantartása

Alkalmanként legyen szükséges, módosíthatja a VMware-infrastruktúra konfigurálása. Jelenleg intervallumok fordulhat elő, 1 – 2 havonta, de idővel elutasítása várt gyakorisága. Az ilyen típusú karbantartási általában elvégezhető a CloudSimple szolgáltatások normál használatalapú megszakítása nélkül. A VMware karbantartási időszakban a következő szolgáltatások továbbra is működnek nem érinti:

* VMware-felügyeleti sík és az alkalmazások
* vCenter-hozzáférés
* Az összes hálózati és tárolási
* Minden Azure-forgalmat

## <a name="updates-and-upgrades"></a>Frissítések és verziófrissítések

CloudSimple felelős VMware szoftvereket (ESXi, vCenter, PSC és NSX) a magánfelhő életciklus-felügyelete.

Szoftverfrissítések a következők:

* **Javítások**. Biztonsági javítások vagy a VMware által kiadott hibajavításokat tartalmaz.
* **Frissítések**. A VMware-verem összetevő alverzió megváltozott.
* **Frissítések**. A VMware-verem összetevő főverzió megváltozott.

CloudSimple teszteli a kritikus fontosságú biztonsági javítást, amint a VMware-ből elérhetővé válik. Egy SLA-t CloudSimple vezet be a biztonsági javítási szintnek a magánfelhő-környezetekben egy héten belül.

CloudSimple negyedéves karbantartási frissíti a VMware biztosít. Ha VMware szoftvereket fő új verziója érhető el, CloudSimple működik, az ügyfelek számára, hogy koordinálja a frissítésre alkalmas karbantartási időszak.  

## <a name="next-steps"></a>További lépések

* [CloudSimple karbantartás és frissítések](cloudsimple-maintenance-updates.md)