---
title: Azure-erőforrások előkészítése helyszíni gépek vészhelyreállításához
description: Ismerje meg, hogyan készítheti elő az Azure a helyszíni Hyper-V virtuális gépek vészhelyreállítása az Azure Site Recovery használatával
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 29189a5919a01fcb897758fb64ca9e84b9381fb6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410908"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Azure-erőforrások előkészítése helyszíni gépek vészhelyreállításához

 [Az Azure Site Recovery](site-recovery-overview.md) segít az üzletmenet-folytonossági és vészhelyreállítási helyreállítási (BCDR) tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag egy sorozat, amely ismerteti, hogyan lehet a helyszíni Hyper-V virtuális gépek vészhelyreállításának beállítása az első olyan.

> [!NOTE]
> A Microsoft tervezési oktatóanyagok esetén a legegyszerűbb telepítési út megjelenítéséhez. Ezekben az oktatóanyagokban használja az alapértelmezett beállításokat, amikor csak lehetséges, és ne jelenjen meg az összes lehetséges beállítások és elérési út. "További információ a hogyan" című szakasz mindegyik megfelelő forgatókönyvhöz.

Ez az oktatóanyag bemutatja, hogyan készítse elő az Azure-összetevőket, amikor a helyszíni virtuális gépeket (Hyper-V) replikálhat az Azure-bA. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikálási engedélyekkel.
> * Hozzon létre egy Azure storage-fiókot, amely az adott replikált gépek rendszerképeit tárolja.
> * Hozzon létre egy Recovery Services-tároló, amely tárolja a virtuális gépek és egyéb replikációs összetevő metaadatai és információkat.
> * Azure-hálózat beállítása. Azure virtuális gépek a feladatátvételt követően létrejönnek, amikor csatlakoznak a hálózathoz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in"></a>Bejelentkezés

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha az imént létrehozott ingyenes Azure-fiókra, Ön az előfizetés rendszergazdájának. Ha Ön nem a rendszergazda, a szükséges engedélyeket a rendszergazdával együttműködve. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás a kiválasztott tárfiókra.

A feladatok végrehajtásához, a fiók rendel a virtuális gépek Közreműködője beépített szerepkörrel. A Site Recovery-műveletek tárolókban kezeléséhez, a fiók rendel a Site Recovery-közreműködő beépített szerepkört.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A replikált gépek rendszerképeit egy Azure-tároló tartalmazza. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során. A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.

1. Az a [az Azure portal](https://portal.azure.com) menüjében válassza **erőforrás létrehozása** > **tárolási** > **tárfiók – blob, fájl, tábla, üzenetsor** .
2. A **Tárfiók létrehozása** területen írja be a fiók nevét.  Kiválasztott Azure-on belül egyedinek kell lennie, lehet 3 – 24 karakter hosszú, és csak kisbetűket és számokat használni. A jelen oktatóanyag esetében használja **contosovmsacct1910171607**.
3. Az **Üzemi modell** mezőben válassza a **Resource Manager** lehetőséget.
4. A **fióktípus**válassza **Storage (általános célú v1)**. Ne válasszon blob-tárolót.
5. A **Replikáció** mezőben válassza az alapértelmezett **Írásvédett georedundáns tárolás** értéket a tárhely-redundanciához. Hagyja üresen a letiltott fejléccé biztonságos átvitelre van szükség.
6. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget. Ezután **hozzáférési szint**, válassza ki az alapértelmezett beállítás a **gyors**.
7. A **előfizetés**, válassza ki az előfizetést, amelyben szeretné létrehozni az új tárfiókot.
8. Az **Erőforráscsoport** mezőben adjon meg egy új erőforráscsoportot. Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure erőforrások üzembe helyezése és felügyelt. A jelen oktatóanyag esetében használja **ContosoRG**.
9. A **hely**, válassza ki a tárfiók földrajzi helyét. A jelen oktatóanyag esetében használja **Nyugat-Európa**.
10. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

   ![Tárfiók létrehozása](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**, és a Recovery Services keressen rá az Azure piactéren.
2. Válassza ki **Backup és Site Recovery (OMS)**. A következő a **Backup és Site Recovery** lapon jelölje be **létrehozás**.
1. A **Recovery services-tároló > név**, adjon meg egy, a tárolót azonosító rövid nevet. Ehhez az oktatóanyaghoz használja a **ContosoVMVault** nevet.
2. A **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. A jelen oktatóanyag esetében használja **contosoRG**.
3. A **hely**, válassza ki a régiót, ahol a tárolóban kell lennie. A jelen oktatóanyag esetében használja **Nyugat-Európa**.
4. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.

![Új tároló létrehozása](./media/tutorial-prepare-azure/new-vault-settings.png)

Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Amikor a feladatátvétel után Azure-beli virtuális gépek jönnek létre a tárolóból, ehhez az Azure-hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget. Hagyja üresen a Resource Manager üzemi kiválasztva.
2. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. A jelen oktatóanyag esetében használja **ContosoASRnet**.
3. Adja meg az erőforráscsoportot, amelyben létre kívánja hozni a hálózatot. A jelen oktatóanyag esetében használja a meglévő erőforráscsoportot **contosoRG**.
4. A **címtartomány**, adja meg **10.0.0.0/24** a tartomány a hálózathoz. Ezen a hálózaton nincs alhálózat van.
5. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
6. A **hely**, válassza a **Nyugat-Európa**. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
7. Hagyja bejelölve az alapértelmezett beállításokat, az alapszintű DDoS elleni védelem nincs szolgáltatásvégpont a hálózaton.
8. Kattintson a **Létrehozás** gombra.

![Virtuális hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

A virtuális hálózat néhány másodperc alatt létrejön. A létrehozást követően láthatja az Azure portal irányítópultján.

## <a name="useful-links"></a>Hasznos hivatkozások

További információ:
- [Azure-hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [A felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítás az Azure-bA a helyszíni Hyper-V infrastruktúra előkészítése](hyper-v-prepare-on-premises-tutorial.md)
