---
title: Az Azure előkészítése helyszíni gépek vészhelyreállításához az Azure Site Recoveryvel | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni gépek vészhelyreállításához az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da71857e84b27b9e9a063d707f75fdf33e5d6a96
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159009"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Azure-erőforrások előkészítése helyszíni gépek vészhelyreállításához

 Az [Azure Site Recovery](site-recovery-overview.md) működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag az első rész abban a sorozatban, amely bemutatja, hogyan állíthat be vészhelyreállítást helyszíni virtuális gépekhez. Lényeges szempont, hogy helyszíni VMware virtuális gépek, Hyper-V virtuális gépek vagy fizikai kiszolgálók számára kíván védelmet biztosítani.

> [!NOTE]
> Az oktatóanyagokat úgy terveztük meg, hogy az adott forgatókönyvhöz a legegyszerűbb üzembehelyezési utat mutassák be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért lásd a **vonatkozó forgatókönyvet ismertető** szakaszt.

A cikk bemutatja, hogyan készítse elő az Azure-összetevőket, amikor helyszíni virtuális gépeket (Hyper-V vagy VMware) vagy Windows/Linux rendszerű fizikai kiszolgálókat szeretne replikálni az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikálási engedélyekkel.
> * Hozzon létre egy Azure-tárfiókot. Ez a replikált gépek rendszerképeit tárolja.
> * Recovery Services-tároló létrehozása. A tároló virtuális gépek és más replikációs összetevők metaadatait és konfigurációs adatait tárolja.
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

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A replikált gépek rendszerképeit egy Azure-tároló tartalmazza. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során. A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie. Ebben az oktatóanyagban a Nyugat-Európa régiót használjuk.

1. Az [Azure Portal](https://portal.azure.com) menüjében válassza az **Erőforrás létrehozása** > **Tárolás** > **Tárfiók – blob, fájl, tábla, üzenetsor** elemet.
2. A **Tárfiók létrehozása** területen írja be a fiók nevét. Ezekben az oktatóanyagokban a **contosovmsacct1910171607** nevet használjuk. A kiválasztott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter közé kell esnie, továbbá csak számokat és kisbetűket tartalmazhat.
3. Az **Üzemi modell** mezőben válassza a **Resource Manager** lehetőséget.
4. A **Fiók típusa** mezőben válassza a **Storage (általános célú v1)** lehetőséget. Ne válasszon blob-tárolót.
5. A **Replikáció** mezőben válassza az alapértelmezett **Írásvédett georedundáns tárolás** értéket a tárhely-redundanciához. A **Biztonságos átvitelre van szükség** beállítást hagyja **Letiltva** állapotban.
6. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget, a **Hozzáférési szint** mezőben pedig az alapértelmezett **Gyakran használt adatok** beállítást.
7. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyikben az új tárfiókot létre szeretné hozni.
8. Az **Erőforráscsoport** mezőben adjon meg egy új erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ezekben az oktatóanyagokban a **ContosoRG** nevet használjuk.
9. A **Hely** mezőben válassza ki a tárfiók földrajzi helyét. 

   ![Tárfiók létrehozása](media/tutorial-prepare-azure/create-storageacct.png)

9. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

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

Amikor a feladatátvétel után Azure-beli virtuális gépek jönnek létre a tárolóból, ehhez az Azure-hálózathoz csatlakoznak.

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
- Az Azure Storage típusainak [ismertetése](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts).
- [További információk](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs#read-access-geo-redundant-storage) a tárhely-redundanciáról és a tárhely [biztonságos átviteléről](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer).



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyszíni VMware-infrastruktúra előkészítése az Azure-ba irányuló vészhelyreállításhoz](tutorial-prepare-on-premises-vmware.md)
