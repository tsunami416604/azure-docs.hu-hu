---
title: Felmérési/áttelepítési eszköz hozzáadása Azure Migrate
description: Útmutató Azure Migrate projekt létrehozásához és egy Assessment/Migration eszköz hozzáadásához.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: a94e3cc18f46c457d6ed54ef88c62adefb07c5b9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102531"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Felmérési/migrálási eszköz hozzáadása első alkalommal

Ez a cikk azt ismerteti, hogyan adhat hozzá értékelést vagy áttelepítési eszközt egy [Azure Migrate](./migrate-services-overview.md) projekthez az első alkalommal.  
Azure Migrate egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint az egyéb eszközökhöz és a független szoftvergyártók (ISV) [ajánlatokhoz](migrate-services-overview.md#isv-integration) . 

## <a name="check-permissions-to-create-project"></a>A projekt létrehozásához szükséges engedélyek keresése

Ha még nem hozott létre Azure Migrate projektet, ellenőrizze, hogy rendelkezik-e a megfelelő engedélyekkel.

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A hozzáférés engedélyezése területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez. Közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

## <a name="create-a-project-and-add-a-tool"></a>Projekt létrehozása és eszköz hozzáadása

Hozzon létre egy új Azure Migrate projektet egy Azure-előfizetésben, és adjon hozzá egy eszközt.

- Azure Migrate-projekttel a rendszer az Ön által értékelt vagy áttelepíteni kívánt környezetből gyűjtött felderítési, értékelési és áttelepítési metaadatok tárolására szolgál. 
- Egy projektben nyomon követheti a felderített eszközöket, és megszervezheti az értékelést és az áttelepítést.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.

    ![Azure Migrate beállítása](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Az **Áttekintés** területen kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.
4. A **kiszolgálók felderítése, felmérése és migrálása**területen kattintson a **kiszolgálók felmérése és migrálása**elemre.

    ![Kiszolgálók felderítése és értékelése](./media/how-to-add-tool-first-time/assess-migrate.png)

1. A **Kiszolgálók felderítése, értékelése és migrálása** területen kattintson az **Eszközök hozzáadása** lehetőségre.
2. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
3. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet.  Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

    ![Azure Migrate projekt létrehozása](./media/how-to-add-tool-first-time/migrate-project.png)

    - A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. A tényleges áttelepítéshez kiválaszthatja a kívánt régiót.
    - Ha egy adott régión belül kell telepítenie egy projektet, a következő API-val kell létrehoznia egy projektet. Adja meg az előfizetés AZONOSÍTÓját, az erőforráscsoport nevét és a projekt nevét, valamint a helyet. Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)földrajzi területeit/régióit.

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Kattintson a **tovább**gombra, és adjon hozzá egy Assessment vagy áttelepítési eszközt.

    > [!NOTE]
    > Projekt létrehozásakor legalább egy értékelési vagy áttelepítési eszközt hozzá kell adnia.

5. Az **értékelés kiválasztása eszközben**adjon hozzá egy Assessment eszközt. Ha nincs szüksége értékelési eszközre, válassza **a kiértékelési eszköz hozzáadásának mellőzése most**  >  **tovább**lehetőséget. 
2. Az **áttelepítési eszköz kiválasztása lapon**szükség szerint adjon hozzá egy áttelepítési eszközt. Ha jelenleg nincs szüksége áttelepítési eszközre, válassza a következőre **az áttelepítési eszköz hozzáadásának mellőzése**lehetőséget  >  **Next**.
3. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd kattintson az **eszközök hozzáadása**elemre.

A projekt létrehozása után további eszközöket is választhat a kiszolgálók és munkaterhelések, adatbázisok és webalkalmazások értékeléséhez és áttelepítéséhez.

## <a name="create-additional-projects"></a>További projektek létrehozása

Bizonyos esetekben előfordulhat, hogy további Azure Migrate projekteket kell létrehoznia. Ha például különböző földrajzi területeken vannak adatközpontok, vagy más földrajzi helyen kell tárolnia a metaadatokat. Hozzon létre egy további projektet a következőképpen:

1. A jelenlegi Azure Migrate projektben kattintson a **kiszolgálók** vagy **adatbázisok**elemre.
2. A jobb felső sarokban kattintson a jelenlegi projekt neve melletti **módosítás** elemre.
3. A **Beállítások**területen válassza az **új projekt létrehozásához kattintson ide**.
4. Hozzon létre egy új projektet, és adjon hozzá egy új eszközt az előző eljárásban leírtak szerint.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)vagy [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).
- Ha ISV-eszközt adott hozzá, vagy átvezeti az eszközt, [tekintse át](prepare-isv-movere.md) az eszköz Azure Migrate való összekapcsolásának előkészítéséhez szükséges lépéseket.
- Ismerje meg, hogyan adhat hozzá további [értékelési](how-to-assess.md) és [áttelepítési](how-to-migrate.md) eszközöket. 
