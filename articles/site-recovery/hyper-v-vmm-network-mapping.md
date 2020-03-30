---
title: Hyper-V (VMM-mel) hálózati leképezés a Site Recovery segítségével
description: Ez a témakör azt ismerteti, hogy miként állíthatja be a hálózati leképezést a (VMM-felhőkben kezelt) hyper-V virtuális gépek vész-helyreállítási rendszeréhez az Azure-ba az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082563"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Hálózati leképezés előkészítése a Hyper-V VM vész-helyreállítási azure-ba


Ez a cikk segít megérteni és felkészülni a hálózati leképezésre, amikor a System Center virtuálisgép-kezelőben (VMM) replikálja a Hyper-V VM-felhőket az Azure-ba vagy egy másodlagos helyre az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Hálózati hozzárendelés előkészítése az Azure-beli replikációhoz

Amikor az Azure-ba replikál, hálózati leképezések a virtuális gép hálózatok egy forrás VMM-kiszolgálón, és a cél az Azure virtuális hálózatok. A leképezés a következőket hajtja végre:
-  **Hálózati kapcsolat**– Biztosítja, hogy a replikált Azure-beli virtuális gépek a csatlakoztatott hálózathoz csatlakozzanak. Minden olyan gép, amely ugyanazon a hálózaton feladatátvételt ad, csatlakozhat egymáshoz, még akkor is, ha különböző helyreállítási tervekben feladatátvételt adtak le.
- **Hálózati átjáró**– Ha egy hálózati átjáró van beállítva a cél Azure-hálózaton, a virtuális gépek csatlakozhatnak más helyszíni virtuális gépekhez.

A hálózatleképezés a következőképpen működik:

- Leképezhet egy forrás VMM VM-hálózatot egy Azure virtuális hálózathoz.
- Miután a feladatátvételi Azure-beli virtuális gépek a forráshálózatban csatlakozik a leképezett cél virtuális hálózathoz.
- A forrás virtuálisgép-hálózathoz hozzáadott új virtuális gépek replikáció esetén csatlakoznak a leképezett Azure-hálózathoz.
- Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
- Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális alhálózatához csatlakozik.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Hálózati hozzárendelés előkészítése másodlagos helyre való replikációhoz

Amikor egy másodlagos helyre replikál, hálózati leképezések a virtuális gép hálózatok a forrás VMM-kiszolgálón, és a virtuális gép hálózatok a cél VMM-kiszolgálón. A leképezés a következőket hajtja végre:

- **Hálózati kapcsolat**– A feladatátvétel után a megfelelő hálózatokhoz csatlakoztatja a virtuális gépeket. A replika virtuális gép csatlakozik a célhálózathoz, amely le van képezve a forráshálózathoz.
- **Optimális virtuális gép elhelyezése**– Optimálisan helyezi a replika virtuális gépek a Hyper-V gazdakiszolgálókon. A replika virtuális gépek olyan állomásokon vannak elhelyezve, amelyek hozzáférhetnek a leképezett virtuálisgép-hálózatokhoz.
- **Nincs hálózati hozzárendelés**– Ha nem konfigurálja a hálózati hozzárendelést, a replika virtuális gépek nem csatlakoznak semmilyen virtuálisgép-hálózathoz a feladatátvétel után.

A hálózatleképezés a következőképpen működik:

- A hálózati leképezés konfigurálható virtuálisgép-hálózatok között két VMM-kiszolgálón, vagy egyetlen VMM-kiszolgálón, ha két helyet ugyanaz a kiszolgáló kezel.
- Ha a leképezés megfelelően van konfigurálva, és a replikáció engedélyezve van, az elsődleges helyen lévő virtuális gép csatlakozik a hálózathoz, és a célhelyen lévő replikája csatlakozik a csatlakoztatott hálózathoz.
- Amikor kiválaszt egy cél virtuálisgép-hálózatot a site recovery hálózati leképezése során, a forrás virtuálisgép-hálózatot használó VMM-forrásfelhők jelennek meg, valamint a védelemre használt célfelhőkben elérhető célvirtuálisgép-hálózatok.
- Ha a célhálózat több alhálózattal rendelkezik, és az egyik alhálózat neve megegyezik azzal az alhálózattal, amelyen a forrásvirtuális gép található, majd a replika virtuális gépe a feladatátvétel után csatlakozik a célalhálózathoz. Ha nincs egyező nevű célalhálózat, a virtuális gép a hálózat első alhálózatához csatlakozik.

## <a name="example"></a>Példa

Íme egy példa, hogy bemutassa ezt a mechanizmust. Vegyünk egy szervezetet, két helyszínnel New Yorkban és Chicagóban.

**Helyen** | **VMM-kiszolgáló** | **Virtuálisgép-hálózatok** | **Hozzárendelve**
---|---|---|---
New York | VMM-NewYork között| VMNetwork1-NewYork | Leképezve a VMNetwork1-Chicago-ra
 |  | VMNetwork2-NewYork | Nincs leképezve
Chicago | VMM-Chicago között| VMNetwork1-Chicago | Leképezve a VMNetwork1-NewYork-ra
 | | VMNetwork2-Chicago | Nincs leképezve

Ebben a példában:

- Ha egy replika virtuális gép jön létre minden virtuális gép, amely csatlakozik a VMNetwork1-NewYork, akkor csatlakozik a VMNetwork1-Chicago.
- Ha egy replika virtuális gép jön létre a VMNetwork2-NewYork vagy VMNetwork2-Chicago, nem fog csatlakozni semmilyen hálózathoz.

A VMM-felhők a példaszervezetben és a felhőkhöz társított logikai hálózatok beállításával hozhatók létre.

### <a name="cloud-protection-settings"></a>Felhővédelmi beállítások

**Védett felhő** | **A felhő védelme** | **Logikai hálózat (New York)**  
---|---|---
Aranyfelhő1 | Aranyfelhő2 |
SilverCloud1| SilverCloud2 |
Aranyfelhő2 | <p>NA</p><p></p> | <p>LogikaiHálózat1-NewYork</p><p>LogikaiHálózat1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogikaiHálózat1-NewYork</p><p>LogikaiHálózat1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Logikai és virtuális gép hálózati beállításai

**Helyen** | **Logikai hálózat** | **Kapcsolódó virtuális gép hálózat**
---|---|---
New York | LogikaiHálózat1-NewYork | VMNetwork1-NewYork
Chicago | LogikaiHálózat1-Chicago | VMNetwork1-Chicago
 | LogikaiHálózat2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Hálózati beállítások célzása

Ezek a beállítások alapján, amikor kiválasztja a cél virtuális gép hálózat, az alábbi táblázat ban látható a választási lehetőségek, amelyek elérhetők lesznek.

**Kiválasztás** | **Védett felhő** | **A felhő védelme** | **Rendelkezésre álló célhálózat**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Elérhető
 | Aranyfelhő1 | Aranyfelhő2 | Elérhető
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Nem érhető el
 | Aranyfelhő1 | Aranyfelhő2 | Elérhető


Ha a célhálózat több alhálózattal rendelkezik, és az egyik alhálózat neve megegyezik azzal az alhálózattal, amelyen a forrásvirtuális gép található, akkor a replika virtuális gépe a feladatátvétel után csatlakozik a célalhálózathoz. Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.


### <a name="failback-behavior"></a>Feladat-visszavételi viselkedés

Ha meg szeretné tekinteni, hogy mi történik a feladat-visszavétel (fordított replikáció) esetén, tegyük fel, hogy a VMNetwork1-NewYork le van képezve a VMNetwork1-Chicago-ra, a következő beállításokkal.


**Vm** | **Virtuálisgép-hálózathoz csatlakoztatva**
---|---
VM1 | VMNetwork1-Hálózat
VM2 (a VM1 másolata) | VMNetwork1-Chicago

Ezekkel a beállításokkal tekintse át, mi történik néhány lehetséges forgatókönyv esetén.

**Forgatókönyv** | **Eredmény**
---|---
Nincs változás a virtuális gép-2 hálózati tulajdonságaiban a feladatátvétel után. | A VM-1 továbbra is csatlakozik a forráshálózathoz.
A Virtuálisgép-2 hálózati tulajdonságai a feladatátvétel után módosulnak, és le vannak választva. | A VM-1 nincs csatlakoztatva.
A Virtuálisgép-2 hálózati tulajdonságai a feladatátvétel után módosulnak, és a VMNetwork2-Chicago-hoz csatlakoznak. | Ha a VMNetwork2-Chicago nincs leképezve, a VM-1 lelesz választva.
A VMNetwork1-Chicago hálózati leképezése megváltozott. | A VM-1 csatlakozik a hálózathoz, amely most a VMNetwork1-Chicago-hoz lesz leképezve.



## <a name="next-steps"></a>További lépések

- [További információ a](hyper-v-vmm-networking.md) IP-címzés feladatátvétel után egy másodlagos VMM-helyre.
- [További információ a](concepts-on-premises-to-azure-networking.md) IP-címzés az Azure-ba történő feladatátvétel után.
