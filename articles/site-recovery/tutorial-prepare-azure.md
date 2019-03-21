---
title: Az Azure előkészítése helyszíni gépek vészhelyreállításához az Azure Site Recoveryvel | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni gépek vészhelyreállításához az Azure Site Recovery használatával.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/03/2019
ms.author: mayg
ms.custom: MVC
ms.openlocfilehash: 5168fc28952631f00c2415d6bc171a130dc85dfd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838561"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Azure-erőforrások előkészítése helyszíni gépek vészhelyreállításához

 Az [Azure Site Recovery](site-recovery-overview.md) működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag az első rész abban a sorozatban, amely bemutatja, hogyan állíthat be vészhelyreállítást helyszíni virtuális gépekhez. Lényeges szempont, hogy helyszíni VMware virtuális gépek, Hyper-V virtuális gépek vagy fizikai kiszolgálók számára kíván védelmet biztosítani.

> [!NOTE]
> Az oktatóanyagokat úgy terveztük meg, hogy az adott forgatókönyvhöz a legegyszerűbb üzembehelyezési utat mutassák be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért lásd a **vonatkozó forgatókönyvet ismertető** szakaszt.

A cikk bemutatja, hogyan készítse elő az Azure-összetevőket, amikor helyszíni virtuális gépeket (Hyper-V vagy VMware) vagy Windows/Linux rendszerű fizikai kiszolgálókat szeretne replikálni az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikálási engedélyekkel.
> * Recovery Services-tároló létrehozása. A tároló virtuális gépek és más replikációs összetevők metaadatait és konfigurációs adatait tárolja.
> * Azure-hálózat beállítása. Amikor a feladatátvétel után Azure-beli virtuális gépek jönnek létre, ehhez az Azure-hálózathoz csatlakoznak.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Storage-fiók írni.
- Felügyelt lemezre írni.

A feladatok végrehajtásához az Ön fiókjának rendelkeznie kell a Virtuális gépek közreműködője beépített szerepkörrel. Emellett a fiókhoz hozzá kell rendelni a Site Recovery-közreműködő beépített szerepkört is, a Site Recovery-műveletek tárolókban való kezeléséhez.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Az Azure Portalon kattintson a **+ erőforrás létrehozása**, és keressen a piactéren **a Recovery services**.
2. Kattintson a **Backup és Site Recovery (OMS)**, a Backup és Site Recovery oldalon kattintson **létrehozás**. 
1. A **Recovery Services-tároló** > **neve**, adjon meg egy, a tárolót azonosító rövid nevet. Ehhez az oktatóanyag-sorozathoz a **ContosoVMVault** nevet használjuk.
2. A **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben az oktatóanyagban használunk **contosoRG**.
3. A **hely**, válassza ki a régiót, amelyben a tároló kell lennie. a **Nyugat-Európa** beállítást használjuk.
4. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.

   ![Új tároló létrehozása](./media/tutorial-prepare-azure/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Ha az Azure virtuális gépek a feladatátvételt követően létrejönnek a felügyelt lemezekről, ehhez a hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget.
2. Hagyja kiválasztva a **Resource Manager** üzemi modellt.
3. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Ebben az oktatóanyagban a **ContosoASRnet** nevet használjuk.
4. Adja meg, melyik erőforráscsoportban hozza létre a hálózatot. A **contosoRG** meglévő erőforráscsoportot használjuk.
5. A **Címtartomány** mezőben adja meg a hálózat tartományát: **10.0.0.0/24**. Ebben a hálózatban nem használunk alhálózatot.
6. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
7. A **Hely** mezőben válassza a **Nyugat-Európa** lehetőséget. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
8. Meghagyjuk az alapszintű DDoS Protection alapértelmezett beállításait hálózati szolgáltatásvégpont nélkül.
9. Kattintson a **Create** (Létrehozás) gombra.

   ![Virtuális hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

   A virtuális hálózat néhány másodperc alatt létrejön. A létrehozást követően megjelenik az Azure Portal irányítópultján.

## <a name="useful-links"></a>Hasznos hivatkozások

- Az Azure Networks [ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).
- [Ismerje meg](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) felügyelt lemezeket.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyszíni VMware-infrastruktúra előkészítése az Azure-ba irányuló vészhelyreállításhoz](tutorial-prepare-on-premises-vmware.md)
