---
title: Áttelepítési eszközök hozzáadása a Azure Migrate
description: Megtudhatja, hogyan adhat hozzá áttelepítési eszközöket a Azure Migrate.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: b6ac8ed64d3f12783cc16f0428874a19d027adf9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109807"
---
# <a name="add-migration-tools"></a>Migrálási eszközök hozzáadása

Ez a cikk bemutatja, hogyan adhat hozzá áttelepítési eszközöket a [Azure Migrate](./migrate-services-overview.md).

- Ha szeretne hozzáadni egy áttelepítési eszközt, és még nem állított be Azure Migrate projektet, kövesse ezt a [cikket](how-to-add-tool-first-time.md).
- Ha egy ISV-eszközt adott hozzá az áttelepítéshez, [kövesse a lépéseket](prepare-isv-movere.md), hogy felkészítse az eszközt.

## <a name="select-a-migration-scenario"></a>Áttelepítési forgatókönyv kiválasztása

1. A Azure Migrate projektben kattintson az **Áttekintés**elemre.
2. Válassza ki a használni kívánt áttelepítési forgatókönyvet:

    - Ha gépeket és számítási feladatokat szeretne áttelepíteni az Azure-ba, válassza a **kiszolgálók felmérése és áttelepíteni**lehetőséget.
    - A helyszíni SQL Server-adatbázisok átköltöztetéséhez válassza az **adatbázisok felmérése és áttelepíteni**lehetőséget.
    - A helyszíni webalkalmazások átköltöztetéséhez válassza a **webalkalmazások felmérése és áttelepíteni**lehetőséget.
    - Ha nagy mennyiségű helyszíni adatátvitelt szeretne áttelepíteni az Azure-ba offline módban, válassza **az Data Box megrendelése**lehetőséget.

    ![Értékelési forgatókönyv](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Kiszolgáló áttelepítési eszközének kiválasztása

1. Kattintson **a kiszolgálók felmérése és migrálása**elemre.
2. Ha még nem adott hozzá áttelepítési eszközöket a **Azure Migrate-kiszolgálókon**, az **áttelepítési eszközök**területen válassza az áttelepítési **eszköz hozzáadásához kattintson ide**. Ha már hozzáadott áttelepítési eszközöket, a **további áttelepítési eszközök hozzáadása**lapon válassza a **módosítás**lehetőséget.

    > [!NOTE]
    > Ha egy másik projekthez kell navigálnia, a **Azure Migrate-kiszolgálók**területen kattintson a **következő gombra** **egy másik áttelepítési projekt részleteinek megtekintéséhez**.

3. A **Azure Migrate**területen válassza ki a használni kívánt áttelepítési eszközt.
    - Ha Azure Migrate kiszolgáló áttelepítését használja, beállíthatja és futtathatja az áttelepítést közvetlenül a Azure Migrate projektben.
    - Ha harmadik féltől származó értékelési eszközt használ, navigáljon az ISV-hoz megadott hivatkozáshoz, és futtassa az áttelepítést az általuk megadott utasításoknak megfelelően.

## <a name="select-a-database-migration-tool"></a>Adatbázis-áttelepítési eszköz kiválasztása

1. Kattintson **az adatbázisok felmérése és migrálása** elemre.
2. Az **adatbázisok**területen kattintson az **eszközök hozzáadása**elemre.
3. Az eszköz hozzáadása > **válassza az áttelepítési eszköz**lehetőséget, majd válassza ki az adatbázis áttelepítéséhez használni kívánt eszközt.

## <a name="select-a-web-app-migration-tool"></a>Webalkalmazás-áttelepítési eszköz kiválasztása

1. Kattintson **a webalkalmazások felmérése és migrálása**elemre.
2. Kövesse a Azure App Service áttelepítési eszközre mutató hivatkozást. Használja az áttelepítési eszközt a következőre:

    - **Alkalmazások online értékelése**: a Azure app Service Migration Assistant használatával a nyilvános URL-címmel rendelkező alkalmazásokat felhasználhatja és áttelepítheti az interneten.
    - **.Net/php**: belső .net-és PHP-alkalmazások esetén letöltheti és futtathatja a Migration Assistant.

## <a name="order-an-azure-data-box"></a>Azure Data Box megrendelése

Nagy mennyiségű adatok Azure-ba történő átmásolásához rendeljen egy Azure-adatmezőt az offline adatforgalomhoz.

1. Kattintson **a Data Box megrendelése**lehetőségre.
2. A **Azure Data Box kiválasztása**lapon adja meg az előfizetését. 
3. Az átvitel az Azure-ba lesz importálva. Határozza meg az adatforrást és az Azure-régió célhelyét.

## <a name="next-steps"></a>Következő lépések

A [Hyper-V](tutorial-migrate-hyper-v.md) -vagy [VMware](tutorial-migrate-vmware.md) -alapú virtuális gépekre Azure Migrate kiszolgáló áttelepítését használó áttelepítés kipróbálása.
