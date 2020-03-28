---
title: Az Azure előkészítése a Hyper-V vészhelyreállításra az Azure Site Recovery segítségével
description: Megtudhatja, hogy miként készítheti elő az Azure-t a helyszíni Hyper-V virtuális gépek vészutáni helyreállítására az Azure Site Recovery használatával
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ef623b95e104b485c6bfc8b2f489afeca436d81e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084176"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Az Azure-erőforrások előkészítése a Hyper-V vészhelyreállításhoz

 [Az Azure Site Recovery](site-recovery-overview.md) segít az üzletmenet folytonosságát és a vész-helyreállítási (BCDR) azáltal, hogy az üzleti alkalmazások fut nak a tervezett és nem tervezett kimaradások során. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag az első egy sorozatban, amely leírja, hogyan lehet beállítani a vész-helyreállítási helyszíni Hyper-V virtuális gépek.

> [!NOTE]
> Úgy tervezünk oktatóanyagokat, hogy egy forgatókönyv legegyszerűbb telepítési útvonalát jelenítsük meg. Ezek az oktatóanyagok az alapértelmezett beállításokat használják, amikor csak lehetséges, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. További információkért tekintse meg a "Hogyan" szakasz minden megfelelő forgatókönyv.

Ez az oktatóanyag bemutatja, hogyan készítheti elő az Azure-összetevőket, ha helyszíni virtuális gépeket (Hyper-V) szeretne replikálni az Azure-ba. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikálási engedélyekkel.
> * Hozzon létre egy Azure-tárfiókot, amely replikált gépek lemezképeit tárolja.
> * Hozzon létre egy Helyreállítási szolgáltatások tárolóját, amely a virtuális gépek és más replikációs összetevők metaadatait és konfigurációs adatait tárolja.
> * Azure-hálózat beállítása. Amikor az Azure-beli virtuális gépek feladatátvétel után jönnek létre, csatlakoznak ehhez a hálózathoz.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.

## <a name="sign-in"></a>Bejelentkezés

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha csak létrehozott egy ingyenes Azure-fiókot, ön az adott előfizetés rendszergazdája. Ha nem Ön a rendszergazda, a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás a kiválasztott tárfiókra.

A feladatok elvégzéséhez a fiók hoz kell a virtuális gép közreműködői beépített szerepkört. A webhely-helyreállítási műveletek trezorban való kezeléséhez a fiókhoz hozzá kell rendelnia a Site Recovery Contributor beépített szerepkört.

## <a name="create-a-storage-account"></a>Create a storage account

A replikált gépek rendszerképeit egy Azure-tároló tartalmazza. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során. A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.

1. Az [Azure Portal menüben](https://portal.azure.com) válassza az Erőforrás >  > **tárterület-fiók létrehozása - blob, fájl, tábla, várólista** **lehetőséget.****Storage**
2. A **Tárfiók létrehozása** területen írja be a fiók nevét.  A választott névnek egyedinek kell lennie az Azure-on belül, 3 és 24 karakter között kell lennie, és csak kisbetűket és számokat kell használnia. Ebben az oktatóanyagban használja **a contosovmsacct1910171607**.
3. Az **Üzemi modell** mezőben válassza a **Resource Manager** lehetőséget.
4. A **Fiók-típusú területen**válassza **a Storage (általános célú v1)** lehetőséget. Ne válasszon blob-tárolót.
5. A **Replikáció** mezőben válassza az alapértelmezett **Írásvédett georedundáns tárolás** értéket a tárhely-redundanciához. Hagyja letiltva a Biztonságos átvitel szükséges beállítását.
6. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget. Ezután az **Access rétegben**válassza a **Gyakori elérés**alapértelmezett beállítást.
7. Az **Előfizetés**alkalmazásban válassza ki azt az előfizetést, amelyben létre szeretné hozni az új tárfiókot.
8. Az **Erőforráscsoport** mezőben adjon meg egy új erőforráscsoportot. Az Azure-erőforráscsoport egy logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése. Ebben az oktatóanyagban használja **a ContosoRG.**
9. A **Hely területen**válassza ki a tárfiók földrajzi helyét. Ebben az oktatóanyagban használja **nyugat-európai**.
10. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

   ![Create a storage account](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Az Azure Portalon válassza a **+Hozzon létre egy erőforrást,** majd keressen az Azure Marketplace helyreállítási szolgáltatások.
2. Válassza **a Biztonsági másolat és a Hely helyreállítása (OMS) lehetőséget.** Ezután a **Biztonsági másolat és webhely-helyreállítás** lapon válassza a **Létrehozás gombot.**
1. A **Helyreállítási szolgáltatások tárolója > név**mezőbe írjon be egy rövid nevet a tároló azonosításához. Ehhez az oktatóanyaghoz használja a **ContosoVMVault** nevet.
2. Az **Erőforrás csoportban**jelöljön ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben az oktatóanyagban használja **a contosoRG**.
3. A **Hely területen**válassza ki azt a régiót, ahol a tárolónak lennie kell lennie. Ebben az oktatóanyagban használja **nyugat-európai**.
4. Ha gyorsan el szeretné érni a tárolót az irányítópultról, válassza a **Rögzítés az irányítópult** > **létrehozása lehetőséget.**

![Új tároló létrehozása](./media/tutorial-prepare-azure/new-vault-settings.png)

Az új tároló megjelenik az **Irányítópult minden** > **erőforrás**és a fő Helyreállítási **szolgáltatások tárolók** lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Amikor a feladatátvétel után Azure-beli virtuális gépek jönnek létre a tárolóból, ehhez az Azure-hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget. Hagyja kiválasztva a Resource Manager üzemi modellt.
2. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Ebben az oktatóanyagban használja a **ContosoASRnet .**
3. Adja meg azt az erőforráscsoportot, amelyben a hálózatot létre szeretné hozni. Ebben az oktatóanyagban használja a meglévő erőforráscsoport **contosoRG.**
4. A **Cím tartományban**adja meg a **10.0.0.0/24** értéket a hálózat tartományaként. Ehhez a hálózathoz nincs alhálózat.
5. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
6. A **Hely területen**válassza **nyugat-európai**lehetőséget. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
7. Hagyja meg az alapvető DDoS-védelem alapértelmezett beállításait, és ne található szolgáltatásvégpont a hálózaton.
8. Kattintson a **Létrehozás** gombra.

![Virtuális hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

A virtuális hálózat néhány másodperc alatt létrejön. Létrehozása után, látni fogja az Azure Portal irányítópultján.

## <a name="useful-links"></a>Hasznos hivatkozások

Ismerkedjen meg a következőkkel:
- [Azure-hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyszíni Hyper-V infrastruktúra előkészítése az Azure-ba való vészhelyreállításhoz](hyper-v-prepare-on-premises-tutorial.md)
