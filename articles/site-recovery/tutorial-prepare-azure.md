---
title: Erőforrások létrehozása az Azure Site Recoveryvel való használatra | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni gépek replikálásához az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 95d6673acaf3cbac2098ac7ae30114696f477045
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212789"
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Azure-erőforrások előkészítése helyszíni gépek replikálásához

 Az [Azure Site Recovery](site-recovery-overview.md) működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Az oktatóanyag bemutatja, hogyan készítse elő az Azure-összetevőket, amikor helyszíni virtuális gépeket (Hyper-V vagy VMware) vagy Windows/Linux rendszert futtató fizikai kiszolgálókat szeretne replikálni az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikálási engedélyekkel.
> * Hozzon létre egy Azure-tárfiókot. A replikált adatok itt lesznek tárolva.
> * Recovery Services-tároló létrehozása.
> * Azure-hálózat beállítása. Amikor a feladatátvétel után Azure-beli virtuális gépek jönnek létre, ehhez az Azure-hálózathoz csatlakoznak.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás a kiválasztott tárfiókra.

A feladatok végrehajtásához az Ön fiókjának rendelkeznie kell a Virtuális gépek közreműködője beépített szerepkörrel. Emellett a fiókhoz hozzá kell rendelni a Site Recovery-közreműködő beépített szerepkört is, a Site Recovery-műveletek tárolókban való kezeléséhez.

## <a name="create-a-storage-account"></a>Create a storage account

A replikált gépek rendszerképeit egy Azure-tároló tartalmazza. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során.

1. Az [Azure Portal](https://portal.azure.com) menüjében válassza az **Új** > **Tárolás** > **Tárfiók** lehetőséget.
2. A **Tárfiók létrehozása** területen írja be a fiók nevét. Ezekben az oktatóanyagokban a **contosovmsacct1910171607** nevet használjuk. A kiválasztott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter közé kell esnie, továbbá csak számokat és kisbetűket tartalmazhat.
3. Az **Üzemi modell** mezőben válassza a **Resource Manager** lehetőséget.
4. A **Fiók típusa** mezőben válassza az **Általános célú** lehetőséget. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget. Ne válasszon blob-tárolót.
5. A **Replikáció** mezőben válassza az alapértelmezett **Írásvédett georedundáns tárolás** értéket a tárhely-redundanciához.
6. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyikben az új tárfiókot létre szeretné hozni.
7. Az **Erőforráscsoport** mezőben adjon meg egy új erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ezekben az oktatóanyagokban használja a **ContosoRG** nevet.
8. A **Hely** mezőben válassza ki a tárfiók földrajzi helyét. A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. Ezekben az oktatóanyagokban használja a **Nyugat-Európa** régiót.

   ![Create a storage account](media/tutorial-prepare-azure/create-storageacct.png)

9. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

## <a name="create-a-vault"></a>Tároló létrehozása

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Monitoring és felügyelet** > **Backup és Site Recovery** lehetőséget.
2. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet. Ehhez az oktatóanyag-sorozathoz a **ContosoVMVault** nevet használjuk.
3. Az **Erőforráscsoport** mezőben válassza ki a **contosoRG** nevű meglévő erőforráscsoportot.
4. A **Hely** mezőben adja meg a **Nyugat-Európa** nevű Azure-régiót, amelyet ezekben az oktatóanyagokban használunk.
5. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.

   ![Új tároló létrehozása](./media/tutorial-prepare-azure/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Amikor a feladatátvétel után Azure-beli virtuális gépek jönnek létre a tárolóból, ehhez az Azure-hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget.
2. Hagyja kiválasztva a **Resource Manager** üzemi modellt. A Resource Manager az előnyben részesített üzemi modell. Ezután hajtsa végre az alábbi lépéseket:

   a. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Használja a **ContosoASRnet** nevet.

   b. Az **Erőforráscsoport** mezőben használja a meglévő **contosoRG** erőforráscsoportot.

   c. A **Címtartomány** mezőben adja meg a hálózat címtartományát: **10.0.0.0/24**.

   d. Ebben az oktatóanyagban nincs szüksége alhálózatra.

   e. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.

   f. A **Hely** mezőben válassza a **Nyugat-Európa** lehetőséget. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.

3. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

   A virtuális hálózat néhány másodperc alatt létrejön. A létrehozást követően megjelenik az Azure Portal irányítópultján.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyszíni VMware-infrastruktúra előkészítése az Azure-ba irányuló vészhelyreállításhoz](tutorial-prepare-on-premises-vmware.md)
