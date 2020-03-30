---
title: A VMware vészhelyreállítása az Azure Site Recovery szolgáltatással
description: Ez a cikk áttekintést nyújt a VMware virtuális gépek azure-ba az Azure-ba az Azure Site Recovery szolgáltatás használatával történő vészutáni helyreállításáról.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954393"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>VMware virtuális gépek azure-ba való vészhelyreállítása

Ez a cikk áttekintést nyújt a helyszíni VMware virtuális gépek az Azure-ba az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával a vész-helyreállítási szolgáltatás áttekintését.

## <a name="what-is-bcdr"></a>Mi az a BCDR?

Az üzletmenet folytonosságára és a vészhelyreállításra (BCDR) vonatkozó stratégia segít vállalkozása működésének fenntartásában. A tervezett leállások és a váratlan leállások során a BCDR biztonságos és elérhető adatokat biztosít, és biztosítja, hogy az alkalmazások továbbra is fussanak. A platformOS BCDR-funkciók, például a regionális párosítás és a magas rendelkezésre állású tárhely mellett az Azure a BCDR-megoldás szerves részeként helyreállítási szolgáltatásokat is biztosít. A helyreállítási szolgáltatások a következők: 

- [Az Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) biztonsági másolatot készít a helyszíni és az Azure virtuális gép adatairól. Biztonsági másolatot lehet egy fájlról és mappákról, adott munkaterhelésekről vagy egy teljes virtuális gépről. 
- [Az Azure Site Recovery](site-recovery-overview.md) rugalmasságot és vészhelyreállítást biztosít a helyszíni gépeken vagy az Azure IaaS virtuális gépeken futó alkalmazások és számítási feladatok számára. A Site Recovery vezényli a replikációt, és kezeli a feladatátvételt az Azure-ba, ha kimaradások történnek. Azt is kezeli a helyreállítást az Azure-ból az elsődleges helyre. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hogyan történik a Site Recovery vészhelyreállítás?

1. Az Azure és a helyszíni hely előkészítése után beállítja és engedélyezi a replikációt a helyszíni gépekhez.
2. A Site Recovery a házirend-beállításoknak megfelelően vgonálja a gép kezdeti replikációját.
3. A kezdeti replikáció után a Site Recovery replikálja a különbözeti módosításokat az Azure-ba. 
4. Ha minden a várt módon történik, vész-helyreállítási gyakorlatot futtat.
    - A fúró segít biztosítani, hogy a feladatátvétel a várt módon működjön, ha valós szükség merül fel.
    - A fúró tesztfeladat-átvételt hajt végre anélkül, hogy befolyásolna az éles környezetben.
5. Ha kimaradás történik, futtatja a teljes feladatátvételt az Azure-ba. Feladatátvétel egyetlen gépen, vagy létrehozhat egy helyreállítási tervet, amely több gépen egyszerre több feladatátvételt is létrehozhat.
6. Feladatátvételkor az Azure-beli virtuális gépek a felügyelt lemezek en vagy a tárfiókokban lévő virtuális gép adatokból jönnek létre. A felhasználók továbbra is hozzáférhetnek az alkalmazásokhoz és a számítási feladatokhoz az Azure virtuális gépről
7. Ha a helyszíni hely ismét elérhető, akkor az Azure-ból visszaadja a vételt.
8. Miután visszaadja a vételi akta, és még egyszer dolgozik az elsődleges helyről, újra elkezdi replikálni a helyszíni virtuális gépeket az Azure-ba.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Honnan tudhatom, hogy a környezetem alkalmas-e az Azure-ba való vészhelyreállításhoz?

A Site Recovery replikálhatja a támogatott VMware virtuális gépen vagy fizikai kiszolgálón futó munkaterhelést. Itt vannak a dolgok, meg kell ellenőrizni a környezetben:

- Ha VMware virtuális gépeket replikál, a VMware virtualizációs kiszolgálók megfelelő verzióit futtatja? [Ellenőrizze itt](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Támogatott operációs rendszert futtatni kívánt gépek? [Ellenőrizze itt](vmware-physical-azure-support-matrix.md#replicated-machines).
- Linux vész-helyreállítási, a gépek egy támogatott fájlrendszer / vendég tároló? [Ellenőrizze itt](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- A replikálni kívánt gépek megfelelnek az Azure követelményeinek? [Ellenőrizze itt](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Támogatott a hálózati konfiguráció? [Ellenőrizze itt](vmware-physical-azure-support-matrix.md#network).
- Támogatott a tárolási konfiguráció? [Ellenőrizze itt](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Mit kell beállítani az Azure-ban, mielőtt elkezdené?

Az Azure-ban a következőket kell előkészítenie:

1. Ellenőrizze, hogy az Azure-fiók rendelkezik-e engedélyekkel virtuális gépek létrehozásához az Azure-ban.
2. Hozzon létre egy Azure-hálózatot, amelyhez az Azure virtuális gépei csatlakoznak, amikor a feladatátvétel után létrejönnek a tárfiókokból vagy a felügyelt lemezekről.
3. Hozzon létre egy Azure Recovery Services-tárolót a Site Recovery számára. A tároló az Azure Portalon található, és a Site Recovery központi telepítésének üzembe helyezésére, konfigurálására, vezénylésére, figyelésére és hibaelhárítására szolgál.

*További segítségre van szüksége?*

Megtudhatja, hogyan állíthatja be az Azure-t [a fiók ellenőrzésével,](tutorial-prepare-azure.md#verify-account-permissions) [a hálózat](tutorial-prepare-azure.md#set-up-an-azure-network)létrehozásával és a [tároló beállításával.](tutorial-prepare-azure.md#create-a-recovery-services-vault)



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Mire van szükségem a helyszíni beállításhoz az indítás előtt?

A helyszíni teendőket a következőkre kell tennie:

1. Be kell állítania néhány fiókot:

    - Ha VMware virtuális gépekreplikálása, egy fiók szükséges a Site Recovery a vCenter Server vagy a vSphere ESXi gazdagépek eléréséhez a virtuális gépek automatikus felderítéséhez.
    - A site recovery mobilitási szolgáltatásügynök telepítéséhez fiókszükséges minden egyes replikálni kívánt fizikai gépen vagy virtuális gépen.

2. Ellenőriznie kell a VMware-infrastruktúra kompatibilitását, ha ezt korábban nem tette meg.
3. Győződjön meg arról, hogy a feladatátvétel után csatlakozhat az Azure-beli virtuális gépekhez. Az RDP-t helyszíni Windows-gépeken vagy SSH-t Linux-gépeken állítja be.

*További segítségre van szüksége?*
- Készítse elő a fiókokat az [automatikus felderítésre](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) és [a Mobilitás szolgáltatás telepítésére.](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)
- [Ellenőrizze,](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) hogy a VMware-beállítások kompatibilisek-e.
- [Készüljön fel,](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) hogy a feladatátvétel után csatlakozzon az Azure-ban.
- Ha részletesebb segítségre van szüksége az Azure-beli virtuális gépek IP-címzésének feladatátvétel után történő beállításával kapcsolatban, [olvassa el ezt a cikket.](concepts-on-premises-to-azure-networking.md)

## <a name="how-do-i-set-up-disaster-recovery"></a>Hogyan állíthatom be a vészhelyreállítást?

Miután az Azure és a helyszíni infrastruktúra a helyén, beállíthat vész-helyreállítási.

1. A telepíteni kívánt összetevők megértéséhez tekintse át a [VMware-t az Azure-architektúrához,](vmware-azure-architecture.md)valamint a [fizikai és az Azure-architektúrát.](physical-azure-architecture.md) Számos összetevő van, ezért fontos megérteni, hogyan illeszkednek egymáshoz.
2. **Forráskörnyezet**: A telepítés első lépéseként be kell állítania a replikációs forráskörnyezetet. Megadhatja, hogy mit szeretne replikálni, és hová szeretne replikálni.
3. **Konfigurációs kiszolgáló**: A helyszíni forráskörnyezetben be kell állítania egy konfigurációs kiszolgálót:
    - A konfigurációs kiszolgáló egyetlen helyszíni gép. A VMware vész-helyreállítási, azt javasoljuk, hogy telepítse a VMware vm, amely telepíthető egy letölthető OVF sablon.
    - A konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt
    - Néhány más összetevő is fut a konfigurációs kiszolgálógépen.
        - A folyamatkiszolgáló fogadja, optimalizálja és elküldi a replikációs adatokat az Azure-beli tárfiók gyorsítótárába. Emellett kezeli a Mobility szolgáltatás automatikus telepítését a replikálni kívánt gépeken, és automatikusan elvégzi a virtuális gépek felderítését a VMware-kiszolgálókon.
        - A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.
    - A beállítás magában foglalja a konfigurációs kiszolgáló regisztrálását a tárolóban, a MySQL Server és a VMware PowerCLI letöltését, valamint az automatikus felderítéshez és a mobilitási szolgáltatás telepítéséhez létrehozott fiókok megadását.
4. **Célkörnyezet:** A cél Azure-környezetet az Azure-előfizetés és a hálózati beállítások megadásával állíthatja be.
5. **Replikációs házirend**: Megadhatja a replikáció bekövetkezésének módját. A beállítások között szerepel, hogy milyen gyakran jönnek létre és tárolnak helyreállítási pontok, és hogy létre kell-e hozni az alkalmazáskonzisztens pillanatképeket.
6. **A replikáció engedélyezése**. A helyszíni gépek replikációját engedélyezi. Ha létrehozott egy fiókot a Mobilitás szolgáltatás telepítéséhez, akkor az akkor lesz telepítve, amikor engedélyezi a replikációt egy gépszámára. 

*További segítségre van szüksége?*

- A lépések gyors átjárásához kipróbálhatja [a VMware oktatóanyagunkat](vmware-azure-tutorial.md)és a [fizikai kiszolgálóforgatókönyvet.](physical-azure-disaster-recovery.md)
- [További információ](vmware-azure-set-up-source.md) a forráskörnyezet beállításáról.
- Információ a konfigurációs kiszolgáló [követelményeiről,](vmware-azure-deploy-configuration-server.md) valamint a konfigurációs kiszolgáló beállítása a VMware replikációhoz való OVF-sablonnal. Ha valamilyen okból nem tud sablont használni, vagy fizikai kiszolgálókat replikál, [kövesse az alábbi utasításokat.](physical-azure-set-up-source.md#set-up-the-source-environment)
- [További információ](vmware-azure-set-up-target.md) a célbeállításokról.
- [További információ](vmware-azure-set-up-replication.md) a replikációs házirend beállításáról.
- [További információ](vmware-azure-enable-replication.md) a replikáció engedélyezéséről és a lemezek replikációból [való kizárásáról.](vmware-azure-exclude-disk.md)


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Valami elromlott, hogyan háríthatom el a hibát?

- Első lépésként [kísérelje meg a telepítés figyelése](site-recovery-monitor-and-troubleshoot.md) a replikált elemek, feladatok és infrastruktúra problémák állapotát, és azonosítsa a hibákat.
- Ha nem tudja végrehajtani a kezdeti replikációt, vagy a folyamatos replikáció nem a várt módon működik, tekintse át ezt a [cikket](vmware-azure-troubleshoot-replication.md) a gyakori hibákért és hibaelhárítási tippekért.
- Ha problémái vannak a replikálni kívánt gépeken a Mobilitás szolgáltatás automatikus telepítésével, tekintse át a cikkben található gyakori [hibákat.](vmware-azure-troubleshoot-push-install.md)
- Ha a feladatátvétel nem a várt módon működik, ellenőrizze a cikkben található gyakori [hibákat.](site-recovery-failover-to-azure-troubleshoot.md)
- Ha a feladat-visszavétel nem működik, ellenőrizze, hogy a probléma megjelenik-e ebben a [cikkben.](vmware-azure-troubleshoot-failback-reprotect.md)



## <a name="next-steps"></a>További lépések

A replikáció most a helyén, [futtasson egy vész-helyreállítási gyakorlatot](tutorial-dr-drill-azure.md) annak érdekében, hogy a feladatátvétel a várt módon működik. 
