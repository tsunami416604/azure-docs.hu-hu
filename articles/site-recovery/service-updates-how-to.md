---
title: Az Azure Site Recovery-frissítések |} A Microsoft Docs
description: Szolgáltatási hírek és frissítése az Azure Site Recoveryben használt összetevők áttekintést nyújt.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/05/2019
ms.author: rajanaki
ms.openlocfilehash: e27dee213baf8365c3ad4efc69602f66e2081abe
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311152"
---
# <a name="service-updates-in-azure-site-recovery"></a>Az Azure Site Recovery szolgáltatási hírek
Szervezetként kell döntse el, hogyan fog az adatok biztonságban és futására tervezett alkalmazások és számítási feladatok, és nem tervezett leállások esetén. Az Azure Site Recovery azzal segíti a BCDR-stratégia, hogy virtuális gépeken és fizikai kiszolgálókon érhető el, ha egy hely leállása futtatja az alkalmazásokat. A Site Recovery replikálja a virtuális gépeken és fizikai kiszolgálókon futó folyamatokat, hogy azok egy másodlagos helyen elérhetők maradjanak az elsődleges hely elérhetetlenné válása esetén. Amikor az elsődleges hely ismét üzembe áll, helyreállítja rajta a munkafolyamatokat.

A Site Recovery a következők replikációját képes kezelni:

- [Azure virtuális gépek replikációja Azure-régiók között](azure-to-azure-tutorial-dr-drill.md).
- Helyszíni virtuális gépek és fizikai kiszolgálók replikációja az Azure-ba, vagy egy másodlagos helyre.
Tudni, hogy több tekintse meg a dokumentációt [Itt](https://docs.microsoft.com/azure/site-recovery) .

Az Azure Site Recovery rendszeres időközönként – beleértve a új funkciók, fejlesztések a támogatási mátrixa, hibajavításokat tartalmaz, ha van ilyen szolgáltatásfrissítések tesz közzé. Annak érdekében, hogy minden a legújabb funkciók & fejlesztéseket és hibajavítások, ha van ilyen aktuális előnyeit maradni, felhasználók javasolja, hogy mindig az Azure SIte Recovery-összetevőit a legújabb verzióra frissíteni. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Az Azure Site Recovery támogatási nyilatkozattal 

> [!IMPORTANT]
> **Minden új verzióval "n" egy az Azure Site Recovery-összetevő, amely akkor szabadul fel, ajánlatból 4 alatti összes verzió "nem támogatott számít**. Ezért célszerű mindig a legújabb elérhető verzióra való frissítéséhez.

> [!IMPORTANT]
> A hivatalos támogatási frissítéseket az > N-4 N verzióra (N a legújabb verzió alatt). Ha olyan N-6, szeretné-e először frissítse a N-4, majd utána frissítse a n

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Ha jelenlegi verziója és a legújabb elérhető verzió közötti különbség nagyobb, mint 4 frissítése

1. Első lépésként frissítse a jelenleg telepített összetevő verzió say N N + 4, és folytassa a következő kompatibilis verzióját. Tegyük fel, és a jelenlegi verzió: 9,24, és a 9.16, először frissíteniük 9,24 9.20, majd.
2. Hajtsa végre az eljárást a forgatókönyvtől függően valamennyi összetevője számára.

### <a name="support-for-latest-oskernel-versions"></a>Legújabb OS-vagy kernel-verziók támogatása

> [!NOTE]
> Ha ütemezett karbantartási időszak van, és a egy újraindítás azonos részét képezi, az azt javasoljuk, először frissítse a Site Recovery-összetevők és a többi az ütemezett tevékenységek folytatásához.

1. A Kernel/operációsrendszer-verziók a frissítés előtt először ellenőrizze, hogy a célverzió Azure Site Recovery által támogatott. Azure-beli virtuális gépek dokumentációban találhatja meg az információkat [VMware virtuális gépek](vmware-physical-azure-support-matrix.md) & Hyper-V virtuális gépek
2. Tekintse meg a [szolgáltatásfrissítések](https://azure.microsoft.com/updates/?product=site-recovery) ismerje meg, melyik verzióját a Site Recovery összetevők támogatja az adott verziót, hogy frissíteni szeretné.
3. Először frissítsen a legújabb hely helyreállítása verzióra.
4. A kívánt verziók, frissítse az operációs rendszer kernelén.
5. Végezze el az újraindítást.
6. Ez biztosítja, hogy az operációs rendszer/Kernel verziója a gépeken vannak frissítve a legújabb verzióra, és hogy a forrásoldali virtuális gépen is betöltött a Site Recovery változásokat, melyek szükségesek az új verzióját.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure-beli virtuális gépek vészhelyreállítása az Azure-ba
Ebben a forgatókönyvben javasoljuk, hogy Ön [engedélyezése](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) automatikus frissítéseket. Választhat, hogy a Site Recovery a következő módokon kezelheti a frissítéseket:

- A replikáció engedélyezése lépés részeként
- Váltás a bővítményt a táron belüli beállításainak frissítése

Abban az esetben manuálisan a frissítések kezelése választotta, kövesse az alábbi lépéseket:

1. Nyissa meg az Azure Portalon, és keresse meg a "helyreállítási tárban."
2. Nyissa meg a "Replikált elemek" panel az Azure Portalon a "helyreállítási tár."
3. Kattintson a képernyő tetején a következő értesítés:
    
    *A Site Recovery replikációs ügynökének frissítése új érhető el*
    
    *Telepítéséhez kattintson ide ->*

4. Válassza ki a alkalmazni a frissítést, és kattintson a kívánt virtuális gépek **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Két helyszíni VMM-helyek között
1. A Microsoft Azure Site Recovery Provider legújabb kumulatív frissítés letöltése.
2. A kumulatív frissítések először telepítse a helyszíni VMM-kiszolgáló, amely akkor kezeli a helyreállítási helyet.
3. A helyreállítás után a hely frissült, a kumulatív frissítések telepítése a VMM-kiszolgálón, amely az elsődleges hely kezelését.

> [!NOTE]
> Ha a VMM egy magas rendelkezésre állású VMM rendszert (fürtözött VMM), győződjön meg arról, hogy a frissítés telepítését, amelyre a VMM szolgáltatás telepítve van a fürt összes csomópontjára.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Egy helyszíni VMM-hely és az Azure között
1. Töltse le a kumulatív frissítés a Microsoft Azure Site Recovery Provider.
2. A kumulatív frissítések telepítése a helyszíni VMM-kiszolgálón.
3. Telepítse a legújabb ügynök MARS-ügynök minden Hyper-V-gazdagépre.

> [!NOTE]
> Ha a VMM egy magas rendelkezésre állású VMM rendszert (fürtözött VMM), győződjön meg arról, hogy a frissítés telepítését, amelyre a VMM szolgáltatás telepítve van a fürt összes csomópontjára.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Egy helyszíni Hyper-V hely és az Azure között

1. Töltse le a kumulatív frissítés a Microsoft Azure Site Recovery Provider.
2. Telepítse a szolgáltatót a Hyper-V-kiszolgálók az Azure Site Recoveryben regisztrált minden egyes csomóponton.

> [!NOTE]
> Ha a Hyper-V gazdagép fürtözött Hyper-V-kiszolgáló, győződjön meg arról, hogy telepítse a frissítést a fürt minden csomópontján

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Egy helyszíni VMware vagy fizikai helyről az Azure között

Frissítések folytatása előtt tekintse meg [Site Recovery támogatási nyilatkozattal](#support-statement-for-azure-site-recovery) tudni, hogy a frissítési útvonalat.

1. A fenti aktuális verziója és a támogatási utasítás alapján, a frissítés telepítése először a helyi felügyeleti kiszolgálón az alábbi az irányelveket, adott [Itt](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Ez az a kiszolgálón, amelyen a konfigurációs kiszolgáló és a folyamat kiszolgálói szerepkörök.
2. Ha kibővített kiszolgálók feldolgozni, frissítse azokat melletti megadva a következő irányelvek alapján [Itt](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Ezt követően szeretné frissíteni a mobilitási szolgáltatást a egyes védett elemek, nyissa meg az Azure Portalra, és folytassa a a **védett elemek** > **replikált elemek** lapot. Válassza ki a virtuális gép ezen az oldalon. Válassza ki a **Windows Update Agent** minden virtuális géphez az oldal alján megjelenő gombra. Ez frissíti a Mobilitásiszolgáltatás-ügynök minden védett virtuális gépen.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Indítsa újra a forrásgépen a mobilitási ügynök frissítése után

Újraindítás ajánlott minden mobilitási ügynök frissítését követően győződjön meg arról, hogy az összes legutóbbi módosítások töltődnek be a forrásgépen. Azonban van **nem kötelező**. Ügynök utolsó újraindítás során és az aktuális verziója közötti különbség nagyobb, mint 4, ha újraindításra kötelező. Tekintse meg az alábbi táblázatban részletes ismertetése.

|**Utolsó újraindítás során ügynök verziója** | **A frissítés** | **Az újraindítás kötelező?**|
|---------|---------|---------|
|9.16 |  9.18 | Nem kötelező|
|9.16 | 9.19 | Nem kötelező|
| 9.16 | 9.20 | Nem kötelező
 | 9.16 | 9.21 | Igen, először frissítse a 9.20, majd indítsa újra a verziók közötti különbség 9.21 való frissítés előtt (9.16 hol történt meg az utolsó újraindítás és a cél verziójának 9.21) van > 4,

## <a name="links-to-currently-supported-update-rollups"></a>Jelenleg támogatott kumulatív mutató hivatkozások

|Update Rollup  |Szolgáltató  |Egyesített telepítő| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[35. kumulatív frissítés](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[34. kumulatív frissítés](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) -gyorsjavítást     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[33. kumulatív frissítés](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[32-es kumulatív frissítés](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[A kumulatív frissítések 31.](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[30. kumulatív frissítés](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0

## <a name="previous-update-rollups"></a>Korábbi kumulatív frissítések

- [29-es kumulatív frissítés](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [28. kumulatív frissítés](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [27. kumulatív frissítés](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [26. kumulatív frissítés](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [25. kumulatív frissítés](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [23. kumulatív frissítés](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [22-es kumulatív frissítés](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [21. kumulatív frissítés](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [20. kumulatív frissítés](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [19. kumulatív frissítés](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
