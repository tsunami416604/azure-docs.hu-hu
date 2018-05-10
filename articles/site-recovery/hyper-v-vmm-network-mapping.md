---
title: A hálózatleképezés a Hyper-V virtuális gép (a VMM-mel) replikáció az Azure Site Recovery szolgáltatással kapcsolatos |} Microsoft Docs
description: Ismerteti, hogyan állíthatja be a hálózatra való leképezést a VMM-felhőkben, az Azure Site Recovery szolgáltatással felügyelt Hyper-V virtuális gépek replikációját.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: f7c6e3eeb8db75a3857e687fecc9ee2748e92696
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="prepare-network-mapping-for-hyper-v-vm-replication-to-azure"></a>Hálózatleképezés előkészítése Hyper-V rendszerű virtuális gépek Azure-ba történő replikálásához


Ez a cikk segít az megértéséhez, valamint a hálózatleképezés előkészítése a System Center Virtual Machine Manager (VMM) felhők Hyper-V virtuális gépek replikálása az Azure-bA vagy másodlagos helyre, ha használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>A replikáció az Azure hálózatleképezés előkészítése

Ha replikál az Azure-ba, a hálózatleképezés kapcsolatot hoz létre a forrás VMM-kiszolgálót, Virtuálisgép-hálózatok között, és cél Azure virtuális hálózatok. Leképezés a következőket teszi:
    -  **Hálózati kapcsolat**– biztosítja, hogy a replikált Azure virtuális gépek csatlakoznak-e a csatlakoztatott hálózati. Minden olyan gép, amelyek ugyanazon a hálózaton keresztül nem lehet csatlakozni egymáshoz, akkor is, ha azokat a különböző helyreállítási tervben feladatátvételt.
    - **Hálózati átjáró**– Ha a cél Azure-hálózatban hálózati átjáró be van állítva, a virtuális gépek más helyszíni virtuális gépek csatlakozhat.

A hálózatleképezés a következőképpen működik:

- A forrás VMM-Virtuálisgép-hálózathoz csatlakoztat egy Azure virtuális hálózatra.
- A forrás Azure virtuális gépek a feladatátvételt követően hálózati fog csatlakozni a csatlakoztatott virtuális célhálózat.
- A forrás Virtuálisgép-hálózathoz hozzáadott új virtuális gépek replikáláskor csatlakoznak a leképezett Azure-hálózathoz.
- Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
- Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális alhálózatához csatlakozik.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Egy másodlagos helyre replikációt a hálózatleképezés előkészítése

Ha egy másodlagos helyre replikál, hálózatleképezés kapcsolatot hoz létre a forrás VMM-kiszolgálón a Virtuálisgép-hálózatok és a célként megadott VMM-kiszolgálón a Virtuálisgép-hálózatok között. Leképezés a következőket teszi:

- **Hálózati kapcsolat**– kapcsolódó virtuális gépek a feladatátvételt követően a megfelelő hálózatokhoz. A replika virtuális gép csatlakoznak-e a cél hálózati egy forrásoldali hálózatot csatlakoztatott.
- **Optimális Virtuálisgép-elhelyezési**– optimális helyezi el a replika virtuális gépek a Hyper-V gazdakiszolgálókra. A replika virtuális gépek gazdagépek férhetnek hozzá a csatlakoztatott Virtuálisgép-hálózatok kerül.
- **Nincs hálózati leképezés**– Ha nem adja meg a hálózatleképezés, a replika virtuális gépek nem csatlakozik Virtuálisgép-hálózatok a feladatátvételt követően.

A hálózatleképezés a következőképpen működik:

- A hálózatleképezés konfigurálható két VMM-kiszolgálókon, vagy a VMM-kiszolgálón egy egyetlen Ha két hely ugyanarra a kiszolgálóra által kezelt Virtuálisgép-hálózatok között.
- Ha leképezést megfelelően van konfigurálva és engedélyezve van a replikáció, az elsődleges helyen a virtuális gépek csatlakoznak-e a hálózati és fogja a replikáját a célhelyen csatlakoznak-e a csatlakoztatott hálózati.
- Cél Virtuálisgép-hálózat kiválasztásakor hálózatra való leképezést a helyreállítás során a forrás Virtuálisgép-hálózatot használó VMM forrás felhők jelenik meg, az elérhető célkiszolgálók Virtuálisgép-hálózatok a cél felhők, amely a védelemhez használt együtt.
- Ha a célhálózatban már több alhálózat működik, és ezek egyikének a neve megegyezik az alhálózat, amelyen a forrás virtuális gép is található, majd a replika virtuális gép csatlakoznak a cél alhálózathoz feladatátvételt követően. Ha nem található a megfelelő nevet cél alhálózat, a virtuális gép csatlakoznak-e az első alhálózat a hálózaton.

## <a name="example"></a>Példa

Íme egy példa a mechanizmus mutatja be. A következőkben két helyen található Győr és Chicagói rendelkező szervezeteknél.

**Hely** | **VMM-kiszolgáló** | **A Virtuálisgép-hálózatok** | **Leképezve**
---|---|---|---
New York | A VMM-NewYork| VMNetwork1-NewYork | VMNetwork1-Chicagói leképezve
 |  | VMNetwork2-NewYork | Nincsenek leképezve:
Chicago | A VMM-Chicagói| VMNetwork1-Chicagói | VMNetwork1-NewYork leképezve
 | | VMNetwork1-Chicagói | Nincsenek leképezve:

Ebben a példában:

- Ha egy replika virtuális gép bármely VMNetwork1-NewYork kapcsolódó virtuális gép kerül létrehozásra, VMNetwork1-Chicagóba csatlakoznak.
- Ha egy replika virtuális gép kerül létrehozásra VMNetwork2-NewYork vagy VMNetwork2-Chicago, akkor nem csatlakozik semmilyen hálózathoz.

Ez hogyan VMM-felhő beállítása a szervezet példája, és a logikai hálózatok felhőkhöz van társítva.

### <a name="cloud-protection-settings"></a>Felhő védelmi beállításait

**Védett felhő** | **Felhő védelme** | **Logikai hálózat (New Yorkban)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicagói</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicagói</p>

### <a name="logical-and-vm-network-settings"></a>Logikai és a virtuális gép hálózati beállításai

**Hely** | **Logikai hálózat** | **Társított Virtuálisgép-hálózat**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicagói | VMNetwork1-Chicagói
 | LogicalNetwork2Chicago | VMNetwork2-Chicagói

### <a name="target-network-settings"></a>Cél hálózati beállításai

Ezek a beállítások alapján a célként megadott Virtuálisgép-hálózat kiválasztásakor, az alábbi táblázat a lehetőségeket, amelyek elérhetők lesznek.

**Kiválasztás** | **Védett felhő** | **Felhő védelme** | **A célhálózat érhető el**
---|---|---|---
VMNetwork1-Chicagói | SilverCloud1 | SilverCloud2 | Elérhető
 | GoldCloud1 | GoldCloud2 | Elérhető
VMNetwork2-Chicagói | SilverCloud1 | SilverCloud2 | Nincs
 | GoldCloud1 | GoldCloud2 | Elérhető


Ha a célhálózatban már több alhálózat működik, és ezek egyikének a neve megegyezik az alhálózat, amelyen a forrás virtuális gép is található, majd a replika virtuális géphez csatlakoznak a cél alhálózathoz feladatátvételt követően. Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.


### <a name="failback-behavior"></a>Feladat-visszavétel viselkedése

Mi történik, a feladat-visszavétel (visszirányú replikálás) esetén megtekintéséhez tegyük fel, hogy VMNetwork1-NewYork van leképezve VMNetwork1-Chicago, a következő beállításokkal.


**VM** | **Virtuálisgép-hálózathoz csatlakozik**
---|---
VM1 | VMNetwork1-Network
Vm2 virtuális gépnek (VM1 replika) | VMNetwork1-Chicagói

Ezekkel a beállításokkal tekintsük át, mi történik, több lehetséges forgatókönyv szerint.

**Forgatókönyv** | **Eredménye**
---|---
A feladatátvételt követően VM-2 hálózati tulajdonságok nem módosult. | VM-1 a forrás hálózati kapcsolatban marad.
VM-2 hálózati tulajdonságainak a feladatátvételt követően módosulnak, és le van választva. | VM-1 le van választva.
VM-2 hálózati tulajdonságainak a feladatátvételt követően módosulnak, és VMNetwork2-Chicagói csatlakozik. | Ha nincs leképezve VMNetwork2-Chicago, VM-1 le lesz választva.
Hálózati leképezése VMNetwork1-Chicagói módosul. | VM-1 VMNetwork1-Chicagói most leképezve a hálózathoz csatlakoznak.



## <a name="next-steps"></a>További lépések

- [További tudnivalók](hyper-v-vmm-networking.md) IP-címzés VMM másodlagos helyre a feladatátvételt követően.
- [További tudnivalók](concepts-on-premises-to-azure-networking.md) IP-címzést, az Azure-bA a feladatátvételt követően.
