---
title: "Telepítse át a gépeket értékelése után az Azure áttelepítése |} Microsoft Docs"
description: "Ismerteti, hogyan történő áttelepítésére vonatkozó javaslatokat kérhet felmérés elvégzéséhez az Azure áttelepítése szolgáltatással futtatása után gépek."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: e6e32e9bd2384987a1d0315bfbef913c46fc5dbb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-machines-after-assessment"></a>Gépek migrálása értékelés után


[Az Azure áttelepítése](migrate-overview.md) értékelésére ellenőrizze, hogy azok alkalmas áttelepítése az Azure-ba, és biztosít az Azure-beli a gép méretezési és költségű becsléseket a helyszíni gépeket. Jelenleg Azure áttelepítése csak értékelésére gépek áttelepítésre. Az áttelepítés maga jelenleg más Azure-szolgáltatások használatával történik.

A cikkből megtudhatja, hogyan kérhet egy áttelepítési eszköz javaslatokat a áttelepítési értékelése futtatását követően.

## <a name="migration-tool-suggestion"></a>Áttelepítési eszköz javaslat

Az áttelepítési eszközök vonatkozó javaslatokat kérhet, kell tennie a részletes felderítés a helyszíni környezet. A részletes felderítési ügynökök telepítése a helyszíni gépeken végezhető el.  

1. Hozzon létre egy Azure áttelepítése projektet, a helyszíni gépeket felderítését és hozzon létre egy áttelepítési assessment. [További információk](tutorial-assessment-vmware.md).
2. Töltse le és telepítse az Azure áttelepítése ügynökök minden a helyi számítógépen, amelynek meg szeretné tekinteni a javasolt áttelepítési módszer. [A következő eljárással](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping) az ügynökök telepítéséhez.
2. Azonosítsa a növekedési és shift áttelepítési alkalmas a helyszíni gépeket. Ezek azok a rajtuk futó alkalmazások módosításait nem feltétlenül szükséges, amely áttelepíthető, mert a virtuális gépeket.
3. A növekedési és shift áttelepítési javasoljuk, hogy Azure Site Recovery segítségével. [További információk](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Alternatív megoldásként eszközeivel külső, amely támogatja az áttelepítést, az Azure-bA.
4. Ha a helyszíni gépeket, amelyek nem megfelelő a növekedési és shift áttelepítésre, ez azt jelenti, ha szeretne áttelepíteni az adott alkalmazást, hanem az egész virtuális gép használhatja más áttelepítési eszközök. Például, javasoljuk, hogy a [Azure adatbázis áttelepítési szolgáltatás](https://azure.microsoft.com/campaigns/database-migration/) Ha szeretne áttelepíteni a helyszíni adatbázisok ilyen egy SQL Server, MySQL vagy Oracle az Azure-bA.


## <a name="review-suggested-migration-methods"></a>Javasolt áttelepítési módszereinek áttekintése

1. Előtt javasolt áttelepítési metódus beszerzéséhez Azure áttelepítése projekt létrehozása, a helyszíni gépeket, és futtassa a áttelepítési értékelése szüksége. [További információk](tutorial-assessment-vmware.md).
2. Az értékelés létrehozása után tekintse meg a Projekt > **áttekintése** > **irányítópult**. Kattintson a **Assessment Readiness**

    ![Assessment readiness](./media/tutorial-assessment-vmware/assessment-report.png)  

3. A **javasolt eszköz**, tekintse át a javaslatokat áttelepítésre használható eszközöket.

    ![Javasolt eszköz](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>Következő lépések

[További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
