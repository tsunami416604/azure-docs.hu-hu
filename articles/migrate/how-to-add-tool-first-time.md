---
title: Felmérési/áttelepítési eszköz hozzáadása Azure Migrate
description: Útmutató Azure Migrate projekt létrehozásához és egy Assessment/Migration eszköz hozzáadásához.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185939"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Felmérési/migrálási eszköz hozzáadása első alkalommal

Ez a cikk azt ismerteti, hogyan adhat hozzá értékelést vagy áttelepítési eszközt egy [Azure Migrate](migrate-overview.md) projekthez az első alkalommal.  
Azure Migrate egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint az egyéb eszközökhöz és a független szoftvergyártók (ISV) [ajánlatokhoz](migrate-services-overview.md#isv-integration) . 

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
3. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. 

    ![Azure Migrate projekt létrehozása](./media/how-to-add-tool-first-time/migrate-project.png)

    Ezen földrajzi területek bármelyikében létrehozhat egy Azure Migrate projektet.

   **Régiócsoport** | **Tárolási hely régiója**
    --- | ---
    Ázsia   | Délkelet-Ázsia vagy Kelet-Ázsia
    Európa | Észak-Európa vagy Nyugat-Európa
    Japán  | Kelet-japán vagy Nyugat-Japán
    Egyesült Királyság | Egyesült Királyság déli régiója vagy Egyesült Királyság nyugati régiója
    Egyesült Államok | USA középső régiója vagy USA 2. nyugati régiója
    Kanada | Közép-Kanada
    India  | Közép-India vagy Dél-India
    Ausztrália | Délkelet-Ausztrália

    A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. A tényleges áttelepítéshez kiválaszthatja a kívánt régiót.

    Ha az áttelepítési projekt és a hozzá tartozó erőforrások (az előfizetéshez tartozó szabályzatok) üzembe helyezéséhez egy földrajzon belül szeretne megadni egy adott régiót, az Azure-erőforrások csak egy adott Azure-régióba történő telepítését engedélyezheti, az alábbi API-t használhatja hozzon létre egy Áttelepítő projektet. Adja meg az előfizetés AZONOSÍTÓját, az erőforráscsoport nevét, az áttelepítési projekt nevét és helyét (a táblázatban említett összes Azure-régiót, ahol a Azure Migrate telepítve van).

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Kattintson a **tovább**gombra, és adjon hozzá egy Assessment vagy áttelepítési eszközt.

    > [!NOTE]
    > Projekt létrehozásakor legalább egy értékelési vagy áttelepítési eszközt hozzá kell adnia.

5. Az **értékelés kiválasztása eszközben**adjon hozzá egy Assessment eszközt. Ha nincs szüksége értékelési eszközre, válassza **a kiértékelési eszköz hozzáadásának mellőzése most** > **tovább**lehetőséget. 
2. Az **áttelepítési eszköz kiválasztása lapon**szükség szerint adjon hozzá egy áttelepítési eszközt. Ha jelenleg nincs szüksége áttelepítési eszközre, válassza **az áttelepítési eszköz hozzáadásának mellőzése most > a** **tovább**lehetőséget.
3. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd kattintson az **eszközök hozzáadása**elemre.

A projekt létrehozása után további eszközöket választhat a kiszolgálók és munkaterhelések, adatbázisok és webalkalmazások értékeléséhez és áttelepítéséhez.

## <a name="create-additional-projects"></a>További projektek létrehozása

Bizonyos esetekben előfordulhat, hogy további Azure Migrate projekteket kell létrehoznia. Ha például különböző földrajzi területeken vannak adatközpontok, vagy más földrajzi helyen kell tárolnia a metaadatokat. Hozzon létre egy további projektet a következőképpen:

1. A jelenlegi Azure Migrate projektben kattintson a **kiszolgálók** vagy **adatbázisok**elemre.
2. A jobb felső sarokban kattintson a jelenlegi projekt neve melletti **módosítás** elemre.
3. A **Beállítások**területen válassza az **új projekt létrehozásához kattintson ide**.
4. Hozzon létre egy új projektet, és adjon hozzá egy új eszközt az előző eljárásban leírtak szerint.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan adhat hozzá további [értékelési](how-to-assess.md) és [áttelepítési](how-to-migrate.md) eszközöket. 
