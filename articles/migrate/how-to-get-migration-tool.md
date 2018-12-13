---
title: Gépek migrálása értékelés után az Azure Migrate |} A Microsoft Docs
description: Ismerteti, hogyan lehet áttelepíteni javaslatokat beolvasni az Azure Migrate szolgáltatással értékelés futtatása után gépek.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 068b55e40afc96dbcfae26c8bf7da8a1b9ea349a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249777"
---
# <a name="migrate-machines-after-assessment"></a>Gépek migrálása értékelés után


[Az Azure Migrate](migrate-overview.md) felméri a helyszíni gépeket, ellenőrizze, hogy azok még az Azure-ba való migrálásra alkalmas, és a gép futtatása az Azure-ban a méretezési és becsléseket biztosít. Jelenleg az Azure Migrate csak felméri a gépek migrálásra. Maga a migrálás jelenleg más Azure-szolgáltatások használatával történik.

Ez a cikk azt ismerteti, hogyan telepheti egy áttelepítési eszköz egy migrálási felmérést futtatását követően.

> [!NOTE]
> Az áttelepítési eszköz javaslat nem érhető el az Azure Government szolgáltatásban.

## <a name="migration-tool-suggestion"></a>Áttelepítési eszköz javaslat

Az áttelepítési eszközök vonatkozó javaslatokat kérhet, kell tennie a helyszíni környezet mélyebb felderítés. A részletes felderítés telepítse az ügynököket a helyszíni gépek végezhető el.  

1. Az Azure Migrate-projekt létrehozása a helyszíni gépek felderítéséhez és hozzon létre egy migrálási felmérést. [További információk](tutorial-assessment-vmware.md).
2. Töltse le és telepítse az Azure Migrate-ügynökök minden helyszíni gépre, amelynek meg szeretné tekinteni a javasolt áttelepítési módszer. [A következő eljárással](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization) az ügynökök telepítéséhez.
2. Azonosítsa a helyszíni gépek lift-and-shift-migrálás megfelelő. Ezek azok a virtuális gépek, nem szükséges módosítania kellene a rajtuk futó alkalmazások, és mivel telepíthetők át.
3. Lift-and-shift-áttelepítés javasoljuk, hogy az Azure Site Recovery használatával. [További információk](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Azt is megteheti külső eszközöket, amelyek támogatják az áttelepítés az Azure-ban is használhatja.
4. Ha rendelkezik a helyszíni gépek nem megfelelők lift-and-shift-áttelepítés, azt jelenti, ha adott alkalmazás helyett egy teljes virtuális Gépet áttelepíteni kívánt használhatja más áttelepítési eszközök. Például, javasoljuk, hogy a [Azure Database Migration service](https://azure.microsoft.com/campaigns/database-migration/) Ha szeretne áttelepíteni a helyszíni adatbázisok ilyen egy SQL Server, MySQL, vagy az Oracle az Azure-bA.


## <a name="review-suggested-migration-methods"></a>Tekintse át a javasolt áttelepítési módszereinek

1. Ehhez előbb a javasolt áttelepítési módszer, kell az Azure Migrate-projekt létrehozása a helyszíni gépek felderítéséhez és migrálási felmérést végezhet. [További információk](tutorial-assessment-vmware.md).
2. Az értékelés létrehozása után megtekintheti azokat a Projekt > **áttekintése** > **irányítópult**. Kattintson a **készenléti állapot értékelése**

    ![Készenléti állapot értékelése](./media/tutorial-assessment-vmware/assessment-report.png)  

3. A **ajánlott eszköz**, tekintse át a javaslatok a migráláshoz használható eszközöket.

    ![Ajánlott eszköz](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
