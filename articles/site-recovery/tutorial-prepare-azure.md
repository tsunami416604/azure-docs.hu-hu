---
title: Az Azure előkészítése helyszíni gépek vészhelyreállításához az Azure Site Recoveryvel | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni gépek vészhelyreállításához az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3d2b22fc507b209a96870daa8bf12ea9ab60a466
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617413"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Azure-erőforrások előkészítése helyszíni gépek vészhelyreállításához

Ez a cikk azt ismerteti, hogyan készíti elő az Azure-erőforrások és -összetevők, hogy állíthat be vészhelyreállítást helyszíni VMware virtuális gépek, Hyper-V virtuális gépek vagy Windows/Linux fizikai kiszolgálók Azure-ba, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

Ez az oktatóanyag az első rész abban a sorozatban, amely bemutatja, hogyan állíthat be vészhelyreállítást helyszíni virtuális gépekhez. 


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikálási engedélyekkel.
> * Recovery Services-tároló létrehozása. A tároló virtuális gépek és más replikációs összetevők metaadatait és konfigurációs adatait tárolja.
> * Állítsa be egy Azure virtuális hálózaton (VNet). Azure virtuális gépek a feladatátvételt követően létrejönnek, amikor csatlakoznak a hálózathoz.

> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a cikk az útmutató szakaszban a Site Recovery a tartalom.

## <a name="before-you-start"></a>Előkészületek

- Az architektúra áttekintése a [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md), és [fizikai kiszolgáló](physical-azure-architecture.md) vész-helyreállítási.
- Olvassa el a gyakori kérdések a [VMware](vmware-azure-common-questions.md) és Hyper-V(hyper-v-azure-common-questions.md)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt. Ezután lépjen be a [az Azure portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha az imént létrehozott ingyenes Azure-fiókját, Ön az előfizetés rendszergazdája, és rendelkezik a szükséges engedélyekkel. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írni az Azure storage-fiók.
- Írni az Azure-beli felügyelt lemezt.

A feladatok végrehajtásához az Ön fiókjának rendelkeznie kell a Virtuális gépek közreműködője beépített szerepkörrel. Emellett a fiókhoz hozzá kell rendelni a Site Recovery-közreműködő beépített szerepkört is, a Site Recovery-műveletek tárolókban való kezeléséhez.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Az Azure Portalon kattintson a **+ erőforrás létrehozása**, és keressen a piactéren **helyreállítási**.
2. Kattintson a **Backup és Site Recovery (OMS)**, a Backup és Site Recovery oldalon kattintson **létrehozás**. 
1. A **Recovery Services-tároló** > **neve**, adjon meg egy, a tárolót azonosító rövid nevet. Ehhez az oktatóanyag-sorozathoz a **ContosoVMVault** nevet használjuk.
2. A **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben az oktatóanyagban használunk **contosoRG**.
3. A **hely**, válassza ki a régiót, amelyben a tároló kell lennie. a **Nyugat-Európa** beállítást használjuk.
4. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.

   ![Új tároló létrehozása](./media/tutorial-prepare-azure/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

A helyszíni gépeket az Azure-bA replikálja a rendszer felügyelt lemezeket. Feladatátvétel esetén az Azure virtuális gépek a felügyelt lemezek alapján létrehozott, és adja meg, ha ebben az eljárásban az Azure-hálózathoz csatlakozik.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget.
2. Tartsa **Resource Manager** kiválasztott lehetőséget üzemi modellként.
3. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Ebben az oktatóanyagban a **ContosoASRnet** nevet használjuk.
4. Adja meg, melyik erőforráscsoportban hozza létre a hálózatot. A **contosoRG** meglévő erőforráscsoportot használjuk.
5. A **címtartomány**, adja meg a tartományt a hálózaton. Használunk **10.1.0.0/24**, és nem használja az alhálózatot.
6. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
7. A **hely**, válassza az ugyanabban a régióban, amelyre a Recovery Services-tároló létrehozása. A jelen oktatóanyagban rendelkezik **Nyugat-Európa**. A hálózat és a tárolónak ugyanabban a régióban kell lennie.
8. Meghagyjuk az alapszintű DDoS Protection alapértelmezett beállításait hálózati szolgáltatásvégpont nélkül.
9. Kattintson a **Create** (Létrehozás) gombra.

   ![Virtuális hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

A virtuális hálózat néhány másodperc alatt létrejön. A létrehozást követően megjelenik az Azure Portal irányítópultján.




## <a name="next-steps"></a>További lépések

- VMware-vészhelyreállítás [a helyszíni VMware-infrastruktúra előkészítése](tutorial-prepare-on-premises-vmware.md).
- Hyper-V vész-helyreállítási [a helyszíni Hyper-V-kiszolgálók előkészítése](hyper-v-prepare-on-premises-tutorial.md).
- Fizikai kiszolgáló vész-helyreállítási [a konfigurációs kiszolgáló és a forrás-környezet beállítása](physical-azure-disaster-recovery.md)
- Az Azure Networks [ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).
- [Ismerje meg](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) felügyelt lemezeket.
