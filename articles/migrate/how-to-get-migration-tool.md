---
title: "Telepítse át a gépeket értékelése után az Azure áttelepítése |} Microsoft Docs"
description: "Ismerteti, hogyan történő áttelepítésére vonatkozó javaslatokat kérhet felmérés elvégzéséhez az Azure áttelepítése szolgáltatással futtatása után gépek."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 809c6e85-0928-45e2-a7c7-6824d860e134
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: d633f140635ba184642a2af999efcde845f3ec31
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="migrate-machines-after-assessment"></a>Telepítse át a gépek értékelése után


[Az Azure áttelepítése](migrate-overview.md) értékelésére ellenőrizze, hogy azok alkalmas áttelepítése az Azure-ba, és biztosít az Azure-beli a gép méretezési és költségű becsléseket a helyszíni gépeket. Jelenleg Azure áttelepítése csak értékelésére gépek áttelepítésre. Az áttelepítés maga jelenleg más Azure-szolgáltatások használatával történik.

A cikkből megtudhatja, hogyan kérhet egy áttelepítési eszköz javaslatokat a áttelepítési értékelése futtatását követően.

## <a name="migration-methods"></a>Áttelepítési módszereinek

Azure áttelepítése használatával értékelését követően ez már javasolt:

1. Hozzon létre egy Azure áttelepítése projektet, a helyszíni gépeket felderítését és futtassa a áttelepítési értékelése. [További információk](tutorial-assessment-vmware.md).
2. Töltse le és telepítse az Azure áttelepítése ügynökök minden a helyi számítógépen, amelynek meg szeretné tekinteni a javasolt áttelepítési módszer. [A következő eljárással](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping) az ügynökök telepítéséhez.
2. Azonosítsa a növekedési és shift áttelepítési alkalmas a helyszíni gépeket. Ezek azok a rajtuk futó alkalmazások módosításait nem feltétlenül szükséges, amely áttelepíthető, mert a virtuális gépeket.
3. A növekedési és shift áttelepítési javasoljuk, hogy Azure Site Recovery segítségével. [További információk](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Alternatív megoldásként eszközeivel 3. fél, amely támogatja az áttelepítést, az Azure-bA.
4. Ha a helyszíni gépeket, amelyek nem a növekedési és shift áttelepítésre alkalmas például ha az áttelepítést szeretné az egész virtuális gép helyett az adott alkalmazást, más áttelepítési eszközök is használhatja. Például, javasoljuk, hogy a [Azure adatbázis áttelepítési szolgáltatás](https://azure.microsoft.com/campaigns/database-migration/) Ha szeretne áttelepíteni a helyszíni adatbázisok ilyen egy SQL Server, MySQL vagy Oracle az Azure-bA.


## <a name="review-suggested-migration-methods"></a>Javasolt áttelepítési módszereinek áttekintése

1. Előtt javasolt áttelepítési metódus beszerzéséhez Azure áttelepítése projekt létrehozása, a helyszíni gépeket, és futtassa a áttelepítési értékelése szüksége. [További információk](tutorial-assessment-vmware.md).
2. Az értékelés létrehozása után tekintse meg a Projekt > **áttekintése** > **irányítópult**. Kattintson a **Assessment Readiness**

    ![Assessment readiness](./media/tutorial-assessment-vmware/assessment-report.png)  

3. A **javasolt eszköz**, tekintse át a javaslatokat áttelepítésre használható eszközöket.

    ![Javasolt eszköz](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>Következő lépések

[További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
