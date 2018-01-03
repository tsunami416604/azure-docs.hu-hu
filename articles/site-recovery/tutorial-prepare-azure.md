---
title: "Erőforrások használatra létrehozása az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Ismerje meg, hogyan készíti elő az Azure a helyszíni gépeket az Azure Site Recovery szolgáltatással a replikációhoz."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 71d740107eb2082e3f112941e1d4abd715d25807
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>A helyszíni gépeket replikálása Azure-erőforrások előkészítése

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás által az üzleti alkalmazások, és az elérhető futtató tervezett és nem tervezett leállások során hozzájárul az üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégiát. A Site Recovery koordinálja a vész-helyreállítási a helyi gép és az Azure virtuális gépek (VM), beleértve a replikáció, feladatátvétel és helyreállítás, valamint a kezelésére.

Az oktatóanyag bemutatja, hogyan készíti elő az Azure összetevők, ha meg szeretné a helyszíni virtuális gépek (Hyper-V vagy VMware), vagy windowsos/Linuxos fizikai kiszolgálók replikálása Azure-bA. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ellenőrizze a fiók rendelkezik-e replikációs engedélyek
> * Hozzon létre egy Azure-tárfiókot.
> * Állítsa be az Azure-hálózatot. Azure virtuális gépek a feladatátvételt követően létrehozott, azok van csatolva az Azure-hálózatot.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="verify-account-permissions"></a>Fiók engedélyek ellenőrzése

Ha az imént létrehozott az ingyenes Azure-fiókjával, majd a rendszergazdaként az előfizetéséhez. Ha nem Ön az előfizetés rendszergazdája, dolgozni a rendszergazdához, és rendelje hozzá a szükséges engedélyekkel. Ahhoz, hogy egy új virtuális gép replikálását, rendelkeznie kell:

- Virtuális gép létrehozása a kijelölt erőforráscsoportban engedély
- Engedéllyel a kiválasztott virtuális hálózatban lévő virtuális gép létrehozása
- A kiválasztott tárolási fiók írásához engedély

A "Virtuális gép közreműködő" beépített szerepkör tartalmazza azokat az engedélyeket. Azure Site Recovery-műveleteket kezelése engedéllyel is kell. A "Hely helyreállítási közreműködői" szerepkör minden, a Site Recovery-műveleteket a Recovery Services-tároló felügyeletéhez szükséges engedéllyel rendelkezik.

## <a name="create-a-storage-account"></a>Create a storage account

Lemezképek replikált gép az Azure storage tartanak. Azure virtuális gépek jönnek létre a tárból, amikor a rendszer átadja a helyszíni Azure.

1. Az a [Azure-portálon](https://portal.azure.com) menüben kattintson a **új** -> **tárolási** -> **tárfiók**.
2. Adja meg a tárfiók nevét. Ezek az oktatóanyagok az nevét használjuk **contosovmsacct1910171607**. A névnek kell Azure belül egyedieknek kell, és csak kisbetűket és számokat 3 és 24 karakter közötti lehet.
3. Használja a **erőforrás-kezelő** üzembe helyezési modellben.
4. Válassza ki **általános célú** > **szabványos**.
5. Válassza ki az alapértelmezett **RA-GRS** az adattároló redundanciája, amely.
6. Válassza ki az előfizetést, amelyikben az új tárfiókot létre szeretné hozni.
7. Adjon meg egy új erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ezek az oktatóanyagok nevét használjuk **ContosoRG**.
8. Válassza ki a tárfiók földrajzi helyét. A tárfiók és a Recovery Services-tárolónak ugyanabban a régióban kell lennie. Ezek az oktatóanyagok használjuk a **Nyugat-Európában** régióban.

   ![Hozzon létre storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.

## <a name="create-a-vault"></a>Tároló létrehozása

1. Az Azure portál menüjében kattintson **új** > **figyelés & felügyeleti** >
    **biztonsági mentés és helyreállítás**.
2. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ebben az oktatóanyagban használjuk **ContosoVMVault**.
3. Válassza ki a meglévő erőforráscsoportot **contosoRG**.
4. Adja meg az Azure-régió **Nyugat-Európában**, hogy ez az oktatóanyag készlet használunk.
5. Gyors hozzáférést a tárolóhoz az irányítópultról, kattintson a **rögzítés az irányítópulton** > **létrehozása**.

   ![Új tároló](./media/tutorial-prepare-azure/new-vault-settings.png)

   Az új tároló megjelenik a **irányítópult** > **összes erőforrás**, majd a fő **Recovery Services-tárolók** lap.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Azure virtuális gépek a feladatátvételt követően létrehozásakor a tárolási, azok ehhez a hálózathoz van csatlakoztatva.

1. A a [Azure-portálon](https://portal.azure.com) menüben kattintson a **új** > **hálózati** >
    **virtuális hálózat**
2. Hagyja **erőforrás-kezelő** kiválasztott lehetőséget üzemi modellként. Erőforrás-kezelő egy, az előnyben részesített telepítési modell.
   - Adja meg a hálózati nevet adja. A név az Azure erőforráscsoporton belül egyedinek kell lennie. Azt a nevet fogja használni **ContosoASRnet**
   - A meglévő erőforráscsoport használata **contosoRG**.
   - Adja meg a hálózati cím tartománya: 10.0.0.0/24.
   - Ebben az oktatóanyagban egy alhálózat nem szükséges.
   - Válassza ki az előfizetést, a hálózat létrehozásához.
   - Válassza ki azt a helyet **Nyugat-Európában**. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
3. Kattintson a **Create** (Létrehozás) gombra.

   ![hálózat létrehozása](media/tutorial-prepare-azure/create-network.png)

   A virtuális hálózat létrehozásához néhány másodpercet vesz igénybe. Miután létrehozott látható az Azure portál Irányítópultjára.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyszíni VMware-infrastruktúra előkészítése az Azure-bA katasztrófa utáni helyreállítás](tutorial-prepare-on-premises-vmware.md)
