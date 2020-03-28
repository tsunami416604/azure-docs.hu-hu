---
title: Az Azure előkészítése a helyszíni vészhelyreállításra az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni gépek vészhelyreállításához az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ba5ecc29edbcd69324500e87add846e4395ce0a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067570"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Az Azure előkészítése a helyszíni vészhelyreállításra az Azure-ba

Ez a cikk ismerteti, hogyan készítse elő az Azure-erőforrások és -összetevők, hogy az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával beállítható a helyszíni VMware virtuális gépek, hyper-vm-ek vagy Windows/Linux fizikai kiszolgálók vészutáni helyreállítása az Azure-ba.

Ez az oktatóanyag az első rész abban a sorozatban, amely bemutatja, hogyan állíthat be vészhelyreállítást helyszíni virtuális gépekhez. 


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikációs engedélyekkel.
> * Recovery Services-tároló létrehozása. A tároló virtuális gépek és más replikációs összetevők metaadatait és konfigurációs adatait tárolja.
> * Hozzon létre egy Azure virtuális hálózat (VNet). Amikor az Azure-beli virtuális gépek feladatátvétel után jönnek létre, csatlakoznak ehhez a hálózathoz.

> [!NOTE]
> Oktatóanyagok megmutatja a legegyszerűbb telepítési útvonalat egy forgatókönyv. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a webhely-helyreállítási tartalomjegyzék Útmutató szakaszában található cikket.

## <a name="before-you-start"></a>Előkészületek

- Tekintse át a [VMware,](vmware-azure-architecture.md) [a Hyper-V](hyper-v-azure-architecture.md)és a fizikai [kiszolgálóvész-helyreállítás](physical-azure-architecture.md) architektúráját.
- A [VMware](vmware-azure-common-questions.md) és a [Hyper-V](hyper-v-azure-common-questions.md) gyakori kérdéseinek elolvasása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené. Ezután jelentkezzen be az [Azure Portalra.](https://portal.azure.com)


## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha most hozta létre az ingyenes Azure-fiókját, ön az előfizetés rendszergazdája, és rendelkezik a szükséges engedélyekkel. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írjon egy Azure-tárfiókba.
- Írjon egy Azure felügyelt lemezre.

A feladatok végrehajtásához az Ön fiókjának rendelkeznie kell a Virtuális gépek közreműködője beépített szerepkörrel. Emellett a webhely-helyreállítási műveletek kezelése a tárolóban, a fiók kell rendelni a Site Recovery Közreműködő beépített szerepkört.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Az Azure Portal menüben válassza az **Erőforrás létrehozása**lehetőséget, és keressen a Piactér **helyreállítási célokra parancsban.**
2. A keresési eredmények között válassza a **Biztonsági másolat és webhely-helyreállítás** elemet, majd a Biztonsági másolat és webhely helyreállítása lapon kattintson a **Létrehozás gombra.** 
3. A **Helyreállítási szolgáltatások létrehozása tárolólapon** válassza az **Előfizetés**lehetőséget. **A Contoso-előfizetést**használjuk.
4. Az **Erőforrás csoportban**jelöljön ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben a bemutatóban **contosoRG -t**használunk.
5. A **Tároló neve mezőbe**írja be a tároló azonosítására szolgáló rövid nevet. Ehhez az oktatóanyag-sorozathoz a **ContosoVMVault** nevet használjuk.
6. A **régióban**válassza ki azt a régiót, amelyben a tárolónak lennie kell lennie. a **Nyugat-Európa** beállítást használjuk.
7. Válassza az **Áttekintés + létrehozás** lehetőséget.

   ![Új tároló létrehozása](./media/tutorial-prepare-azure/new-vault-settings.png)

   Az új tároló most megjelenik az **Irányítópult minden** > **erőforrás**és a fő Helyreállítási **szolgáltatások tárolók** lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

A helyszíni gépek replikálódnak az Azure által felügyelt lemezekre. Feladatátvétel esetén az Azure virtuális gépek ezekből a felügyelt lemezekből jönnek létre, és csatlakoznak az ebben az eljárásban megadott Azure-hálózathoz.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget.
2. Az **Erőforrás-kezelő** kijelölését jelölje ki telepítési modellként.
3. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Ebben az oktatóanyagban a **ContosoASRnet** nevet használjuk.
4. A **Címmezőbe**írja be a virtuális hálózat címtartományát cdr jelöléssel. **10.1.0.0/24-et**használunk.
5. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
6. Adja meg azt az **erőforráscsoportot,** amelyben a hálózat létrejön. A **contosoRG** meglévő erőforráscsoportot használjuk.
7. A **Hely területen**válassza ki ugyanazt a régiót, amelyben a Recovery Services-tároló jött létre. A mi bemutató ez **Nyugat-Európa**. A hálózatnak ugyanabban a régióban kell lennie, mint a tárolónak.
8. A **Cím tartományban**adja meg a hálózat tartományát. **10.1.0.0/24-et**használunk, és nem alhálózatot használunk.
9. Az alapvető DDoS-védelem alapértelmezett beállításait hagyjuk meg, szolgáltatásvégpont vagy tűzfal nélkül a hálózaton.
9. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

A virtuális hálózat néhány másodperc alatt létrejön. Létrehozása után, látni fogja az Azure Portal irányítópultján.




## <a name="next-steps"></a>További lépések

- A VMware vész-helyreállítási, [előkészíti a helyszíni VMware-infrastruktúra.](tutorial-prepare-on-premises-vmware.md)
- A Hyper-V vész-helyreállítási, [előkészíti a helyszíni Hyper-V-kiszolgálók](hyper-v-prepare-on-premises-tutorial.md).
- A kiszolgáló fizikai vészhelyreállítása érdekében [állítsa be a konfigurációs kiszolgálót és a forráskörnyezetet](physical-azure-disaster-recovery.md)
- Az Azure Networks [ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).
- További információ a felügyelt [lemezekről.](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)
