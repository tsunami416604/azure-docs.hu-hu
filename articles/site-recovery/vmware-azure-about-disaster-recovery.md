---
title: Azure Site Recovery VMware virtuális gépek vészhelyreállítása az Azure használatával |} A Microsoft Docs
description: Ez a cikk áttekintést VMware virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: raynew
ms.openlocfilehash: 9368ff848c9be075a08d5a80a49ffc64f5392cad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214698"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Tudnivalók a VMware virtuális gépek vészhelyreállítása az Azure-bA

Ez a cikk a vész-helyreállítási áttekintést nyújt a helyszíni VMware virtuális gépek Azure-bA a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

## <a name="what-is-bcdr"></a>Mit jelent a BCDR?

(Egy üzleti folytonossági és vészhelyreállítási BCDR) stratégia segít bízhatja az üzleti. Tervezett és nem várt leállások, során BCDR tartja az adatokat, biztonságos és elérhető, és biztosítja, hogy az alkalmazások továbbra is fut. BCDR platformfunkciók például régiónkénti párosítás és a magas rendelkezésre állású tároló, mellett az Azure Recovery Services-szerves részeként a BCDR-megoldás biztosít. A Recovery services a következők: 

- [Az Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) a helyszíni és az Azure-beli Virtuálisgép-adatok biztonsági mentését. Képes biztonsági másolatot készíteni egy fájlt és mappát, adott munkaterhelés konkrét vagy egy teljes virtuális Gépet. 
- [Az Azure Site Recovery](site-recovery-overview.md) rugalmasság és vészhelyreállítást biztosít az alkalmazások és a helyszíni gépeket, vagy Azure IaaS virtuális gépeken futó számítási feladatokat. A Site Recovery koordinálja a replikációt, és kezeli az Azure-bA feladatátvételi leállások esetén. Helyreállítás az Azure-ból az elsődleges hely is kezeli. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hogyan hajtsa végre a Site Recovery vész-helyreállítási?

1. Után az Azure és a helyszíni hely előkészítése, állítsa be, és engedélyezze a replikációt a helyszíni gépek.
2. A Site Recovery koordinálja a házirend-beállításoknak a gép kezdeti replikálása.
3. A kezdeti replikációt követően a Site Recovery az Azure-bA replikálja a változásokat. 
4. Replikáláskor minden a várt módon, vészhelyreállítási gyakorlatának futtatása.
    - A részletezés segítségével, győződjön meg arról, hogy a feladatátvétel egy valódi igény szerint növelhesse várt módon fog működni.
    - A részletezés az éles környezet befolyásolása nélkül hajtja végre feladatátvételi tesztet.
5. Egy kimaradás lép fel, ha feladatátvételt végez teljes Azure-bA. Egyetlen gép feladatai, vagy létrehozhat egy helyreállítási tervbe, hogy átadja a feladatokat több gép egyszerre.
6. Feladatátvétel esetén a virtuális gép adatait az Azure Storage-ban az Azure virtuális gépek jönnek létre. Felhasználók továbbra is hozzáférő alkalmazások és számítási feladatok az Azure virtuális Gépen
7. Ha a helyszíni hely megint elérhetővé válik, átadja az Azure-ból.
8. Miután a feladat-visszavételt, és az elsődleges webhelyről még egyszer dolgozik, először a helyszíni virtuális gépek replikálása Azure-bA újra.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Honnan tudhatom meg, hogy saját környezetben vészhelyreállítás az Azure-ba való használatra alkalmas?

A Site Recovery a támogatott VMware virtuális gép vagy fizikai kiszolgálón bármilyen számítási feladatot képes replikálni. Az alábbiakban a legfontosabb tudnivaló ellenőrzése a környezetben:

- Ha VMware virtuális gépeket replikál, akkor a megfelelő verziót futtat VMware virtualizálási kiszolgálók? [Itt ellenőrizze](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Azok a támogatott operációs rendszert futtató replikálni kívánt gépek? [Itt ellenőrizze](vmware-physical-azure-support-matrix.md#replicated-machines).
- Gépek Linux vész-helyreállítási futnak egy támogatott system/Vendég fájltároló? [Jelölje be](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Felelnek meg a replikálni kívánt gépek az Azure-követelményeknek? [Itt ellenőrizze](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- A hálózati konfiguráció támogatott? [Itt ellenőrizze](vmware-physical-azure-support-matrix.md#network).
- A tárolási konfiguráció támogatott? [Itt ellenőrizze](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Mit kell állítania az Azure-ban, megkezdése előtt?

Az Azure-ban kell a következő:

1. Ellenőrizze, hogy az Azure-fiókja rendelkezik-e az Azure-beli virtuális gépek létrehozásához szükséges engedélyek.
2. Hozzon létre egy tárfiókot a replikált gépek rendszerképek tárolásához.
3. Hozzon létre egy Azure-hálózatra, amelyhez az Azure virtuális gépek csatlakozni fog a feladatátvételt követően létrehozott storage-ból.
4. Állítsa be az Azure Recovery Services-tárolót a Site Recovery. A tároló az Azure Portalon található, és központi telepítése, konfigurálása, szervezését, figyelése és hibaelhárítása a Site Recovery üzembe helyezése szolgál.

*További segítségre van szüksége?*

Ismerje meg, hogyan állítható be az Azure által [a fiók ellenőrzésének](tutorial-prepare-azure.md#verify-account-permissions)létrehozását, egy [tárfiók](tutorial-prepare-azure.md#create-a-storage-account) és [hálózati](tutorial-prepare-azure.md#set-up-an-azure-network), és [tároló beállítása](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Mit kell állítania a helyszíni megkezdése előtt?

A helyszíni mit kell tennie:

1. Állítsa be a fiókok több kell:

    - Ha VMware virtuális gépeket replikál, virtuális gépek felderítéséhez fiók szükséges a Site Recovery a vCenter-kiszolgáló vagy vSphere ESXi-gazdagépek eléréséhez.
    - Egy fiók minden olyan fizikai gép vagy a replikálni kívánt virtuális Gépet a Site Recovery mobilitási szolgáltatás ügynökének telepítéséhez van szükség.

2. A VMware-infrastruktúra a kompatibilitás ellenőrzése, ha korábban, amely esetben kell.
3. Győződjön meg arról, hogy képes csatlakozni az Azure virtuális gépek egy feladatátvétel után. Beállíthatja az RDP a helyi Windows-számítógépek és az SSH a Linux rendszerű gépeken.

*További segítségre van szüksége?*
- Készítse elő a fiókok [automatikus felderítés](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) és [telepíteni a mobilitási szolgáltatást](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Győződjön meg arról](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) , hogy a VMware-beállítások kompatibilisek.
- [Készítse elő](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) , hogy a feladatátvételt követően csatlakozni az Azure-ban.
- Ha azt szeretné, hogy IP-címkezelés az Azure virtuális gépek a feladatátvételt követően beállításával kapcsolatos további részletes súgó [Ez a cikk](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Hogyan állíthatok be vészhelyreállítást?

Miután az Azure-ban és a helyszíni infrastruktúra helyen, akkor vész-helyreállítási állíthatja.

1. Az összetevőket kell telepíteni, tekintse át a [VMware-ből az Azure-architektúra](vmware-azure-architecture.md), és a [fizikai az Azure-architektúra](physical-azure-architecture.md). Nincsenek számos összetevőnek, ezért fontos megérteni, hogyan mindannyian működnek együtt.
2. **Forráskörnyezet**: az első lépés a telepítés, állítsa be a replikációs forrás-környezetet. Mit szeretne replikálni, és ahol a replikálni kívánt kell megadni.
3. **Konfigurációs kiszolgáló**: állítsa be a konfigurációs kiszolgáló az a helyszíni forráskörnyezetben kell:
    - A konfigurációs kiszolgálón egy egyetlen helyszíni gépre. VMware-vészhelyreállítás javasoljuk, hogy telepíteni a VMware virtuális gépként helyezhető egy letölthető OVF-sablon alapján.
    - A konfigurációs kiszolgáló koordinálja a helyszíni és Azure közötti kommunikációt
    - A konfigurációs kiszolgáló gépen futó egyéb összetevők néhány.
        - A folyamatkiszolgáló fogadja, optimalizálja, és elküldi a replikációs adatokat az Azure storage. Is kezeli a mobilitási szolgáltatás gépekre szeretne replikálni, az automatikus telepítés és a virtuális gépek automatikus felderítését végzi, VMware-kiszolgálók.
        - A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.
    - Beállítása magában foglalja a konfigurációs kiszolgáló regisztrálása a tárolóban, a MySQL-kiszolgáló és a VMware powercli-t, letöltése és a fiókok megadása létrehozott automatikus felderítését és a mobilitási szolgáltatás telepítése.
4. **Célkörnyezet**: beállítása a cél Azure-környezet az Azure-előfizetéssel, tárolási és hálózati beállításainak megadásával.
5. **Replikációs házirend**: azt adja meg, hogyan történjen a replikáció. A beállítások tartalmazzák, milyen gyakran helyreállítási pontok létrehozása és tárolása, és hogy alkalmazáskonzisztens pillanatképek kell létrehozni.
6. **Engedélyezze a replikációt**. Engedélyezheti a helyszíni gépek replikációját. Ha a mobilitási szolgáltatás telepítése egy fiókot, majd azt esetén telepítve lesz az adott gép replikálását engedélyezi. 

*További segítségre van szüksége?*

- Az alábbi lépések gyors bemutató, kipróbálhatja az [VMware oktatóanyag](vmware-azure-tutorial.md), és [fizikai kiszolgáló forgatókönyv](physical-azure-disaster-recovery.md).
- [További](vmware-azure-set-up-source.md) a forráskörnyezet beállítása.
- [Ismerje meg](vmware-azure-deploy-configuration-server.md) konfigurációs kiszolgálóra vonatkozó követelményekről, és beállítása a konfigurációs kiszolgáló OVF-sablonnal a VMware replikálásához. Ha valamilyen okból nem használhat egy sablont, vagy fizikai kiszolgálókat replikál [kövesse ezeket az utasításokat](physical-azure-set-up-source.md#set-up-the-source-environment).
- [További](vmware-azure-set-up-target.md) cél-beállításokról.
- [További információ](vmware-azure-set-up-replication.md) replikációs szabályzat beállításáról.
- [Ismerje meg,](vmware-azure-enable-replication.md) a replikáció engedélyezése és [kizárása](vmware-azure-exclude-disk.md) lemezeket a replikációból.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Hiba történt, hogyan háríthatom?

- Első lépésként próbálja [figyelése a központi telepítés](site-recovery-monitor-and-troubleshoot.md) ellenőrizheti a replikált elemek, feladatok és infrastrukturális problémák állapotát és azonosítani az esetleges hibákat.
- Ha Ön nem sikerült befejezni a kezdeti replikálást, vagy folyamatban lévő replikáció nem várt módon működik, [ebből a cikkből](vmware-azure-troubleshoot-replication.md) gyakori hibák és hibaelhárítási tippek.
- Ha a mobilitási szolgáltatás automatikus telepítési problémák merültek fel a replikálni kívánt gépeken, tekintse át az előforduló gyakori hibák [Ez a cikk](vmware-azure-troubleshoot-push-install.md).
- Feladatátvétel nem várt módon működik, ha ellenőrizze a gyakori hibák [Ez a cikk](site-recovery-failover-to-azure-troubleshoot.md).
- Ha a feladat-visszavétel nem működik, ellenőrizze, hogy megjelenik-e a probléma megoldásához a [Ez a cikk](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>További lépések

Replikáció mostantól helye, akkor [vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md) annak érdekében, hogy a feladatátvétel a várt módon működik. 
