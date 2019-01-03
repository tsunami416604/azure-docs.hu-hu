---
title: Információk a hálózatleképezésről Hyper-V virtuális gépek (VMM-mel) vész-helyreállítási az Azure Site recoveryvel |} A Microsoft Docs
description: Ismerteti, hogyan állíthat be hálózatleképezés (VMM-felhőkben felügyelt) Hyper-V virtuális gépek vészhelyreállítása az Azure-ba, az Azure Site Recoveryvel.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 3ca3222f47b6a728905f895007269e3c22acd66d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789942"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Az Azure-bA Hyper-V virtuális gép vész-helyreállítási hálózatleképezés előkészítése


Ez a cikk segít könnyebben átláthatja és készítse elő a hálózatleképezésre, ha a System Center Virtual Machine Manager (VMM) felhőkben a Hyper-V virtuális gépek replikálása az Azure-bA vagy egy másodlagos helyre, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Az Azure-bA hálózatleképezés előkészítése

Ha hálózati leképezés maps a forrás VMM-kiszolgálón futó Virtuálisgép-hálózatok között, az Azure-bA replikál, illetve cél Azure virtuális hálózatok. A leképezés a következőket hajtja végre:
    -  **Hálózati kapcsolat**– biztosítja, hogy a csatlakoztatott hálózati replikált Azure virtuális gépek csatlakoznak. Minden olyan gép, amely az azonos hálózaton feladatátvételt is csatlakozni egymáshoz, még akkor is, ha azok az különböző helyreállítási tervek a feladatátvételt.
    - **Hálózati átjáró**– Ha a cél Azure-hálózatban hálózati átjáró be van állítva, a virtuális gépek más helyszíni virtuális gépek csatlakozhat.

A hálózatleképezés a következőképpen történik:

- A forrás VMM-Virtuálisgép-hálózat leképezése egy Azure virtuális hálózatra.
- A forrás Azure virtuális gépek a feladatátvételt követően hálózathoz csatlakoznak a leképezett cél virtuális hálózattal.
- Replikáció során a forrás Virtuálisgép-hálózathoz hozzáadott új virtuális gépek csatlakoznak a leképezett Azure-hálózathoz.
- Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
- Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális alhálózatához csatlakozik.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Replikálás másodlagos helyre hálózatleképezés előkészítése

Ha egy másodlagos helyre replikál, a hálózatleképezés kapcsolatot hoz létre, a forrás VMM-kiszolgálón futó Virtuálisgép-hálózatok és Virtuálisgép-hálózatok VMM célkiszolgálón között meg. A leképezés a következőket hajtja végre:

- **Hálózati kapcsolat**– virtuális gépek kapcsolódik a megfelelő hálózatokhoz a feladatátvételt követően. A replika virtuális gép csatlakoznak a leképezett a Forráshálózat célhálózattal.
- **Optimális Virtuálisgép-elhelyezés**– a replika virtuális gépek optimálisan helyezi el a Hyper-V gazdakiszolgálókra. Replika virtuális gépek kerülnek, a gazdagépeken, amelyek hozzáférhetnek a hozzárendelt Virtuálisgép-hálózatokat.
- **Nincs hálózatleképezés**– Ha nem konfigurálja a hálózatleképezést, a replika virtuális gép nem csatlakozik Virtuálisgép-hálózatokat a feladatátvételt követően.

A hálózatleképezés a következőképpen történik:

- A hálózatleképezés konfigurálható Virtuálisgép-hálózatok két VMM-kiszolgálókon, vagy egyetlen VMM-kiszolgáló ugyanarra a kiszolgálóra, ha két hely között.
- Amikor leképezés megfelelően van konfigurálva és a replikáció engedélyezve van, hálózathoz csatlakoznak egy virtuális Gépet az elsődleges helyen és a replika a célhelyen fog csatlakozni a csatlakoztatott hálózati.
- Céloldali Virtuálisgép-hálózat kiválasztásakor során a Site Recoveryben hálózatleképezést a forrás VMM-felhő, a forrás Virtuálisgép-hálózatot használó jelenik meg, és a rendelkezésre álló terület a a cél a magánfelhőkben a védelemhez használt Virtuálisgép-hálózatok.
- Ha a cél már több alhálózat működik, és ezen alhálózatok egyikéhez tartozik, a neve megegyezik az alhálózatot, amelyen a forrás virtuális gép is található, majd a replika virtuális gép csatlakoznak a cél alhálózathoz a feladatátvételt követően. Ha nincs egyező nevű alhálózat, a virtuális gép csatlakozik az első alhálózat a hálózatban.

## <a name="example"></a>Példa

Íme egy példa mutatja be ezt a mechanizmust. Vessünk egy két helyen található, New York-i és Chicago szervezetben.

**Hely** | **VMM-kiszolgáló** | **Virtuálisgép-hálózatok** | **Hozzárendelve**
---|---|---|---
New York | A VMM-NewYork| VMNetwork1-NewYork | VMNetwork1-Chicago leképezve
 |  | VMNetwork2-NewYork | Nincs leképezve
Chicago | A VMM-Chicago| VMNetwork1-Chicago | VMNetwork1-NewYork leképezve
 | | VMNetwork2-Chicago | Nincs leképezve

Ebben a példában:

- Ha egy replika virtuális gép létrehozása az összes virtuális gép csatlakozik-e VMNetwork1-NewYork, azt VMNetwork1-Chicagóba csatlakoznak.
- Ha egy replika virtuális gép létrehozása VMNetwork2-NewYork vagy VMNetwork2-Chicago, akkor nem csatlakozik egyetlen hálózathoz sem.

Itt látható, hogyan VMM-felhőkben található példa szervezet számára, és a-felhőkhöz társított logikai hálózatok beállítása.

### <a name="cloud-protection-settings"></a>Felhő védelmi beállításait

**Védett felhőhöz** | **Felhőalapú védelem** | **Logikai hálózat (Győrben)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Logikai és a virtuális gép hálózati beállításai

**Hely** | **Logikai hálózat** | **Társított Virtuálisgép-hálózat**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>A célhálózat beállításai

Ezek a beállítások alapján, a céloldali VM-hálózat kiválasztásakor, az alábbi táblázat az elérhető lehetőségek.

**Kiválasztás** | **Védett felhőhöz** | **Felhőalapú védelem** | **A célhálózat érhető el**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Elérhető
 | GoldCloud1 | GoldCloud2 | Elérhető
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Nem érhető el
 | GoldCloud1 | GoldCloud2 | Elérhető


Ha a cél már több alhálózat működik, és ezen alhálózatok egyikéhez tartozik, a neve megegyezik az alhálózatot, amelyen a forrás virtuális gép is található, majd a replika virtuális gép csatlakoznak a cél alhálózathoz a feladatátvételt követően. Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.


### <a name="failback-behavior"></a>Feladat-visszavétel viselkedés

Ha szeretné látni, mi történik, a feladat-visszavétel (visszirányú replikálás) esetén, tegyük fel, hogy VMNetwork1-NewYork le van képezve VMNetwork1-Chicago, a következő beállításokkal.


**VM** | **Virtuálisgép-hálózathoz csatlakozik**
---|---
VM1 | VMNetwork1-Network
VM2 (VM1 replika) | VMNetwork1-Chicago

Ezekkel a beállításokkal tekintse át mi történik, néhány lehetséges forgatókönyv szerint.

**Forgatókönyv** | **Eredmény**
---|---
2. virtuális gép hálózati tulajdonságait a feladatátvételt követően nem változik. | VM-1 marad a forrás-hálózathoz csatlakozik.
2. virtuális gép hálózati tulajdonságait a feladatátvételt követően megváltoznak, és le van választva. | 1. virtuális gép le van választva.
2. virtuális gép hálózati tulajdonságait a feladatátvételt követően megváltoznak, és VMNetwork2-Chicago csatlakozik. | Ha VMNetwork2-Chicago nincs leképezve, 1. virtuális gép le lesz választva.
Hálózatleképezés VMNetwork1-Chicago, változik. | VM-1 VMNetwork1-Chicago most leképezve a hálózathoz fog csatlakozni.



## <a name="next-steps"></a>További lépések

- [Ismerje meg](hyper-v-vmm-networking.md) IP-címkezelés másodlagos VMM-helyre történő feladatátvétel után.
- [Ismerje meg](concepts-on-premises-to-azure-networking.md) IP-címkezelés az Azure-bA a feladatátvételt követően.
