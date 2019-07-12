---
title: Áttelepítési eszközök hozzáadása az Azure Migrate
description: Áttelepítési eszközök hozzáadása az Azure Migrate hubban módját ismerteti.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811868"
---
# <a name="add-migration-tools"></a>Áttelepítési eszközök hozzáadása

Ez a cikk bemutatja, hogyan adhat hozzá az áttelepítési eszközök [Azure Migrate](migrate-overview.md).

Az Azure Migrate elemzésekhez és migráláshoz hub eszközöket biztosít az Azure-bA. Ez magában foglalja natív eszközök más Azure-szolgáltatásokat, és a külső független szoftverszállítók (ISV) szállító ajánlatok által biztosított eszközökkel.

Ha egy migrációs eszköz hozzá szeretné adni, és az Azure Migrate-projekt mentése még nem állított be, kövesse ezt [cikk](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Egy független Szoftverszállító eszköz kiválasztása

Ha úgy dönt, egy [ISV eszköz](migrate-services-overview.md#isv-integration) az áttelepítéshez, első lépésként licenc, vagy regisztráljon egy ingyenes próbaverzióra, az ISV-szabályzatának megfelelően. Minden egyes eszköz csatlakozni az Azure Migrate lehetőség van. Az eszköz telepítésére, és kövesse az eszköz utasítások és a dokumentáció az Azure Migrate az eszköz munkaterület csatlakozni. 

## <a name="select-a-migration-scenario"></a>Válassza ki az áttelepítési forgatókönyvben

1. Az Azure Migrate-projektben kattintson **áttekintése**.
2. Válassza ki a használni kívánt áttelepítési forgatókönyvben:

    - A gépek és a számítási feladatok migrálása az Azure-ba, válassza ki a **felmérési és a kiszolgálók áttelepítése**.
    - A helyszíni SQL gépek migrálása, jelölje be **felmérési és migrálhat adatbázisokat**.
    - Áttelepíteni a helyszíni webalkalmazásokat, válassza ki a **felmérési és a web Apps alkalmazások áttelepítése a**.
    - Nagy mennyiségű, a helyszíni adatok migrálása Azure-bA offline módban, jelölje be **megrendelési, egy Data Box**.

    ![Értékelés forgatókönyv](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Válassza ki a kiszolgáló-áttelepítési eszköz

1. Kattintson a **értékelni és Migrálni a kiszolgálók**.
2. A **Azure Migrate - kiszolgálók**, ha az áttelepítési eszközök még, még nem vett alatt **áttelepítési eszközök**válassza **Ide kattintva hozzáadhat egyet egy migrációs eszköz**. Ha már hozzáadott felhőmigrálási eszközök, a **adjon hozzá további áttelepítési eszközök**válassza **módosítás**.

    > [!NOTE]
    > Ha szeretné-e egy másik projekthez, navigáljon a **Azure Migrate - kiszolgálók**mellett található **különböző migrate projekt részleteit lásd**, kattintson a **kattintson ide**.

3. A **Azure Migrate**, válassza ki a használni kívánt áttelepítési eszköz.
    - Ha használja az Azure Migrate-kiszolgáló áttelepítése, állítsa be a, és futtassa a migrálási közvetlenül az Azure Migrate-projektben.
    - Ha egy külső frissítésfelmérő eszközt használ, lépjen a független Szoftvergyártók számára megadott hivatkozás, és végezze el az áttelepítést az utasításoknak nyújtanak.

## <a name="select-a-database-migration-tool"></a>Válassza ki az adatbázis-migrálási eszköz

1. Kattintson a **felmérési és migrálhat adatbázisokat**
2. A **adatbázisok**, kattintson a **eszközök hozzáadása**.
3. Az eszköz hozzáadása > **válassza áttelepítési eszköz**, válassza ki az adatbázis migrálása a használni kívánt eszközt.

## <a name="select-a-web-app-migration-tool"></a>Válassza ki a web app áttelepítési eszköz

1. Kattintson a **felmérési és a web Apps alkalmazások áttelepítése a**.
2. Kövesse a hivatkozást a Migrálási eszközben, az Azure App Service. Az áttelepítési eszköz használata:

    - **Mérje fel az alkalmazások online**: Mérje fel, és a egy nyilvános URL-cím online, alkalmazások migrálása a használatával az Azure App Service Migration Assistant.
    - **.NET/PHP**: Belső .NET és a PHP-alkalmazások esetén töltse le, és futtassa az áttelepítési Segéd.

## <a name="order-an-azure-data-box"></a>Az Azure Data Box sorrend

Nagy mennyiségű adat áttelepítése az Azure-ba, az Azure DAta Box offline adatátvitel rendezheti.

1. Kattintson a **megrendelési, egy Data Box**.
2. A **válassza ki az Azure Data Box**, válassza ki az előfizetést. 
3. Az átvitel lesz az importálás az Azure-bA. Adja meg az adatforráshoz, és az adatokat az Azure-régióban célját.

## <a name="next-steps"></a>További lépések

Próbálja ki az Azure Migrate-áttelepítés használatával az áttelepítés [Hyper-V](tutorial-migrate-hyper-v.md) vagy [VMware](tutorial-migrate-vmware.md) virtuális gépeket.
