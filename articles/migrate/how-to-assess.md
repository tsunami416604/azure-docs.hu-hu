---
title: Assessment Tools hozzáadása a Azure Migrate
description: Megtudhatja, hogyan adhat hozzá értékelési eszközöket a Azure Migrateban.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185914"
---
# <a name="add-assessment-tools"></a>Felmérőeszközök hozzáadása

Ez a cikk azt ismerteti, hogyan adhat hozzá értékelési eszközöket a [Azure Migrateban](migrate-overview.md).

Azure Migrate az Azure-ba való áttelepítéshez és az Azure-ba való Migrálás eszközeinek központja. Azure Migrate eszközöket, valamint más eszközöket és független szoftvergyártói ajánlatokat is tartalmaz.

Ha szeretne felvenni egy értékelési eszközt, és még nem rendelkezik Azure Migrate-projekttel, kövesse ezt a [cikket](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Eszköz kiválasztása

Ha a kiértékeléshez nem Azure Migrate eszközt választ, kezdje a licenc beszerzésével, vagy regisztráljon az ingyenes próbaverzióra az eszköz házirendjének megfelelően. Az eszközökhöz lehetőség van a Azure Migratehoz való kapcsolódásra. Az eszköz Azure Migrate való összekapcsolásához kövesse az utasításokat és a dokumentációt. [További](migrate-services-overview.md) információ az eszközökről.


## <a name="select-an-assessment-scenario"></a>Értékelési forgatókönyv kiválasztása

1. A Azure Migrate projektben kattintson az **Áttekintés**elemre.
2. Válassza ki a használni kívánt értékelési forgatókönyvet:

    - Az Azure-ba való áttelepítéshez szükséges gépek és munkaterhelések felderítéséhez és értékeléséhez válassza a **kiszolgálók felmérése és áttelepítése**lehetőséget.
    - A helyszíni SQL-gépek kiértékeléséhez válassza az **adatbázisok felmérése és áttelepíteni**lehetőséget.
    - A helyszíni webalkalmazások értékeléséhez válassza a **webalkalmazások értékelése és áttelepíteni**lehetőséget.

    ![Értékelési forgatókönyv](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Kiszolgáló-értékelési eszköz kiválasztása 

1. Kattintson **a kiszolgálók felmérése és migrálása**elemre.
2. Ha **Azure Migrate-kiszolgálókat**, ha még nem adott meg értékelési eszközt, az **értékelési eszközök**területen jelölje be **az értékelési eszköz hozzáadásához kattintson ide**. Ha már hozzáadott értékelési eszközöket, a **további értékelési eszközök hozzáadása**lapon válassza a **módosítás**lehetőséget.

    > [!NOTE]
    > Ha egy másik projekthez kell navigálnia, a **Azure Migrate-kiszolgálók**területen kattintson a **következő gombra** **egy másik áttelepítési projekt részleteinek megtekintéséhez**.

3. A **Azure Migrate**területen válassza ki a használni kívánt értékelési eszközt.

    - Ha Azure Migrate kiszolgáló-értékelést használ, közvetlenül a Azure Migrate projektben állíthatja be, futtathatja és tekintheti meg az értékeléseket.
    - Ha más értékelési eszközt használ, navigáljon a webhelyhez megadott hivatkozáshoz, és futtassa az értékelést az általuk megadott utasításoknak megfelelően.


## <a name="select-a-database-assessment-tool"></a>Adatbázis-értékelő eszköz kiválasztása

1. Kattintson **az adatbázisok felmérése és migrálása** elemre.
2. Az **adatbázisok**területen kattintson az **eszközök hozzáadása**elemre.
3. Az eszköz hozzáadása > **válassza az értékelési eszköz**lehetőséget, válassza ki az adatbázis értékeléséhez használni kívánt eszközt.

## <a name="select-a-web-app-assessment-tool"></a>Webalkalmazás-értékelő eszköz kiválasztása

1. Kattintson **a webalkalmazások felmérése és migrálása**elemre.
2. Kövesse a Azure App Service áttelepítési eszközre mutató hivatkozást. Használja az áttelepítési eszközt a következőre:

    - **Alkalmazások online értékelése**: a Azure app Service Migration Assistant használatával a nyilvános URL-címmel rendelkező alkalmazásokat is felhasználhatja.
    - **.Net/php**: belső .net-és PHP-alkalmazások esetén letöltheti és futtathatja a Migration Assistant.



## <a name="next-steps"></a>További lépések

A [VMware](tutorial-prepare-vmware.md) virtuális gépek, a [Hyper-V](tutorial-prepare-hyper-v.md)vagy a [fizikai kiszolgálók](tutorial-prepare-physical.md) Azure Migrate Server Assessment használatával történő értékelésének kipróbálása
