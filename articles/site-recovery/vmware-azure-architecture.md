---
title: VMware – Azure vészhelyreállítási helyreállítási architektúra az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk áttekintést az Azure-bA az Azure Site Recovery a helyszíni VMware virtuális gépek vész-helyreállítási beállításakor használt összetevőkről és architektúráról
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 962ced808f97dd1fea3805fa8c953e6d7563cd17
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871744"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware – Azure vészhelyreállítási recovery architektúrájáról

Ez a cikk ismerteti az architektúra és a vész-helyreállítási replikáció, feladatátvétel és helyreállítás VMware virtuális gépek (VM) telepítéséhez használt folyamatok között egy helyszíni VMware-hely és az Azure-ban a [Azure Site Recovery](site-recovery-overview.md) servuce.


## <a name="architectural-components"></a>Az architektúra összetevői

A következő táblázat és grafikus adja meg a VMware-vészhelyreállításhoz az Azure-ban használt összetevők magas szintű nézetét.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, az Azure Storage-fiók és Azure-hálózatra. | A tárfiókot a replikált adatokat a helyszíni virtuális gépek tárolódik. Ha feladatátvételt végez a helyszínről az Azure-bA az Azure virtuális gépek a replikált adatokkal jönnek létre. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló gép** | Egy a helyszíni gépen. Azt javasoljuk, hogy kell futtatnia, VMware virtuális gépként helyezhető a letöltött OVF-sablon.<br/><br/> A gép fut, a helyszíni Site Recovery minden összetevőjét, többek között a konfigurációs kiszolgáló, folyamatkiszolgáló és fő célkiszolgáló. | **Konfigurációs kiszolgáló**: koordinálja a helyszíni és Azure közötti kommunikációt, és felügyeli az adatreplikációt.<br/><br/> **A folyamatkiszolgáló**: a konfigurációs kiszolgáló alapértelmezés szerint telepítve. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítással optimalizálja őket és elküldi azt az Azure Storage. A folyamatkiszolgáló Ezenfelül telepíti az Azure Site Recovery mobilitási szolgáltatást a virtuális gépeket szeretne replikálni, és elvégzi a helyszíni gépek automatikus felderítését. Az üzembe helyezés növekedésével további, külön folyamatkiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.<br/><br/> **Fő célkiszolgáló**: a konfigurációs kiszolgáló alapértelmezés szerint telepítve. Az Azure-ból a feladat-visszavétel során kezeli azt a replikációs adatokat. Nagyméretű környezetekben hozzáadhat egy további, különálló fő célkiszolgálót a feladat-visszavételhez.
**VMware-kiszolgálók** | VMware virtuális gépek helyszíni vSphere ESXi-kiszolgálókon lévő üzemeltetett. Azt javasoljuk, hogy a gazdagépek felügyeletéhez egy vCenter-kiszolgálóra. | Site Recovery üzembe helyezése során a Recovery Services-tároló vegyen fel VMware-kiszolgálókat.
**Replikált gépek** | A mobilitási szolgáltatás telepítve van az egyes replikált VMware virtuális Gépeken. | Azt javasoljuk, hogy lehetővé tegye az automatikus telepítési adatok a folyamatkiszolgálótól. Azt is megteheti a szolgáltatás manuális telepítése vagy egy automatikus központi telepítési módszer, például a System Center Configuration Managerrel használja.

**VMware-ről Azure-ra architektúra**

![Összetevők](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi egy virtuális gép replikációját, megkezdődik a kezdeti replikálás az Azure storage, a megadott replikációs házirenddel. Vegye figyelembe a következőket:
    - A VMware virtuális gépekhez a replikálás blokkszintű, közel-folyamatos, a virtuális gépen futó mobilitási szolgáltatás használatával történik.
    - Minden replikációs házirend-beállítások érvényesek:
        - **Helyreállítási Időkorlát küszöbértéke**. Ez a beállítás nem befolyásolja a replikáció. Ez segít a megfigyeléshez. Egy esemény jelenik meg, és szükség esetén e-mailt küldeni, ha az aktuális helyreállítási Időkorlát meghaladja a küszöbértéket, amely azt adja meg.
        - **Helyreállítási pont megőrzése**. Ezzel a beállítással illesztésnek vissza szeretne lépni egy bekövetkező szolgáltatáskimaradás esetén időben. A premium storage maximális megőrzési ideje 24 órán keresztül. Standard szintű tárolóban működő érték 72 óra. 
        - **Alkalmazáskonzisztens pillanatképek**. Alkalmazáskonzisztens pillanatkép is kell minden 1 12 órát vehet attól függően, az alkalmazás igényeinek megfelelően. Standard szintű Azure blob-pillanatfelvételek a pillanatképeket. A mobilitási ügynök, a virtuális gépeken futó kérelmek megfelelően ezt a beállítást, és a könyvjelzők, amelyek a replikáció Stream-időponthoz egy konzisztens alkalmazás VSS-pillanatkép.

2. Forgalom replikálja az Azure storage nyilvános végpontokat az interneten keresztül. Másik lehetőségként használhatja az Azure ExpressRoute [nyilvános társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#publicpeering). Forgalom a helyek közötti virtuális magánhálózati (VPN) keresztül egy helyszíni hely Azure-ba történő nem támogatott.
3. Kezdeti replikálás befejezése után kezdődik replikációja az Azure-bA. A gépek nyomon követett módosításait a folyamatkiszolgálónak érkeznek.
4. Kommunikáció a következőképpen történik:

    - Virtuális gépek a helyszíni konfigurációs kiszolgálóval HTTPS a 443-as porton bejövő kommunikációt, a replikáció kezelését.
    - A konfigurációs kiszolgáló koordinálja a replikációt az Azure-ral HTTPS 443-as kimenő porton keresztül.
    - Virtuális gépek a replikációs adatokat a folyamatkiszolgálónak (a konfigurációs kiszolgáló gépen futó) HTTPS 9443-as porton bejövő küldése. Ez a port módosítható.
    - A folyamatkiszolgáló fogadja a replikált adatokat, optimalizálja a és titkosítja azokat, és elküldi azt az Azure storage 443-as porton keresztüli kimenő.




**VMware – Azure replikációs folyamat**

![Replikációs folyamat](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

Miután replikáció be van állítva, és futtat egy vészhelyreállítási próba végrehajtása (teszt feladatátvétel) ellenőrizze, hogy minden a várt módon működik, futtathatja feladatátvétel és feladat-visszavétel szükség szerint.

1. Egyetlen gép feladatátvétel futtatása, vagy hozzon létre egy helyreállítási tervek egyszerre több virtuális gép feladatátvételét. Egyetlen gép feladatátvételi helyett egy helyreállítási terv előnye többek között:
    - Alkalmazás-függőségek modellezheti fel a virtuális gépek között az alkalmazás egy helyreállítási tervben.
    - Parancsfájlok, Azure runbookok hozzáadása, és manuális műveletek esetén.
2. A kezdeti feladatátvitel után véglegesítheti a számítási feladatok elérése az Azure virtuális gép elindításához.
3. Ha az elsődleges helyszíni hely megint elérhetővé válik, előkészítheti a feladat-visszavételhez. Annak érdekében, hogy a feladat-visszavételt, akkor be kell állítania egy feladat-visszavételi infrastruktúrát, beleértve:

    * **Ideiglenes folyamatkiszolgáló az Azure-ban**: sikertelen lesz az Azure-ból, beállíthat egy Azure virtuális gépek kezelése az Azure-ból replikációs folyamat kiszolgálója. Ez a virtuális gép a feladatok visszaadását követően törölhető.
    * **VPN-kapcsolat**: feladat-visszavételt, kell egy VPN-kapcsolat (vagy ExpressRoute), az Azure-hálózatot a helyszíni helyre.
    * **Különálló fő célkiszolgálót**: alapértelmezés szerint a fő célkiszolgáló, amelyen telepítve lett a konfigurációs kiszolgálóval, az a helyszíni VMware virtuális gép feladat-visszavétel kezeli. Ha sikertelen biztonsági nagy mértékű forgalom van szüksége, állítsa be egy önálló helyszíni fő célkiszolgálót erre a célra.
    * **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a szabályzat létrehozásakor egy replikációs házirendet a helyszínről az Azure-ban automatikusan létrejön.
4. Miután az összetevő a következő helyen, feladat-visszavétel három művelet történik:

    - 1. fázis: Az Azure virtuális gépek ismételt védelme, így azok replikálása az Azure-ból a helyszíni VMware virtuális gépek vissza a.
    -  2. fázis: Feladatátvétel futtatása a helyszíni helyre.
    - 3. fázis: Számítási feladatok sikertelenek voltak vissza, miután újból engedélyezi a helyszíni virtuális gépek replikációját.
    
 
**VMware-feladat-visszavétel az Azure-ból**

![Feladat-visszavétel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>További lépések

Hajtsa végre a [ebben az oktatóanyagban](vmware-azure-tutorial.md) VMware engedélyezése az Azure-bA.
