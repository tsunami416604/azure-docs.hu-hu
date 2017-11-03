---
title: "Egy Azure virtuális gép replikálása egy másik Azure-régiót (előzetes verzió)"
description: "A gyors üzembe helyezés lépései replikálni egy Azure-régió, egy Azure virtuális gép egy másik régióban."
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 369ffed823bc76ee4273d7866935c0ddc7ffa515
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="replicate-an-azure-vm-to-another-azure-region-preview"></a>Egy Azure virtuális gép replikálása egy másik Azure-régiót (előzetes verzió)

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás által az üzleti alkalmazások, és az elérhető futtató tervezett és nem tervezett leállások során hozzájárul az üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégiát. A Site Recovery koordinálja a vész-helyreállítási a helyi gép és az Azure virtuális gépek (VM), beleértve a replikáció, feladatátvétel és helyreállítás, valamint a kezelésére.

A gyors üzembe helyezési útmutató egy másik Azure-régió, egy Azure virtuális gép replikálása.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="enable-replication-for-the-azure-vm"></a>Azure virtuális gép replikációjának engedélyezése

1. Az Azure portálon kattintson **virtuális gépek**, és válassza ki a replikálni kívánt virtuális Gépet.

2. A **beállítások**, kattintson a **vész-helyreállítási (előzetes verzió)**.
3. A **katasztrófa utáni helyreállítás konfigurálása** > **cél régió** , amelyhez replikálni fogja a cél régiót.
4. A gyors üzembe helyezés elfogadja az alapértelmezett beállításokat.
5. Kattintson a **engedélyezze a replikálást**. Ekkor elindul egy feladat a virtuális gép replikációjának engedélyezéséhez.

    ![Replikáció engedélyezése](media/azure-to-azure-quickstart/enable-replication1.png)



## <a name="verify-settings"></a>Ellenőrizze a beállításokat

A replikációs feladat befejeződését követően a replikáció állapotának, replikációs beállítások beállításainak módosítása, és a telepítés tesztelésére.

1. A virtuális gép menüben kattintson a **vész-helyreállítási (előzetes verzió)**.
2. Ellenőrizheti a replikációs állapot, a létrehozott helyreállítási pontok és a forrás és cél régiók a térképen.

   ![Replikáció állapota](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A virtuális gép az elsődleges régióban leállítja a replikáló replikációját letiltása:

- Az adatforrás-replikációs beállítások automatikusan is törlődnek.
- Leállítja a Site Recovery szolgáltatás számlázása a virtuális gép is.

Állítsa le a replikációt az alábbiak szerint:

1. Válassza ki a virtuális Gépet.
2. A **vész-helyreállítási (előzetes verzió)**, kattintson a **további**.
3. Kattintson a **tiltsa le a replikációt**.

   ![Tiltsa le a replikációt](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, az egyetlen virtuális gép távoli másodlagos régióba replikálja.

> [!div class="nextstepaction"]
> [Vész-helyreállítási konfigurálása az Azure virtuális gépeken](azure-to-azure-tutorial-enable-replication.md)
