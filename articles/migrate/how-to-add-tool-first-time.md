---
title: Értékelési/áttelepítési eszköz hozzáadása az Azure Áttelepítése szolgáltatásban
description: Bemutatja, hogyan hozhat létre egy Azure Migrate projektet, és hogyan adhat hozzá értékelési/áttelepítési eszközt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185939"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Felmérési/migrálási eszköz hozzáadása első alkalommal

Ez a cikk ismerteti, hogyan adhat hozzá egy értékelési vagy áttelepítési eszközt egy [Azure Migrate](migrate-overview.md) projekthez első alkalommal.  
Az Azure Migrate központi központot biztosít a helyszíni alkalmazások és számítási feladatok, valamint a magán-/nyilvános felhőalapú virtuális gépek Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ azure-áttelepítési eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint más eszközöket és független szoftverszállítói (ISV) [ajánlatokat.](migrate-services-overview.md#isv-integration) 

## <a name="create-a-project-and-add-a-tool"></a>Projekt létrehozása és eszköz hozzáadása

Állítson be egy új Azure Migrate projektet egy Azure-előfizetésben, és adjon hozzá egy eszközt.

- Az Azure Migrate projekt a felderítési, értékelési és áttelepítési metaadatok tárolására szolgál, amelyeket a felmérhető vagy migráló környezetből gyűjtött. 
- A projektben nyomon követheti a felderített eszközöket, és koordinálhatja az értékelést és az áttelepítést.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.

    ![Az Azure Migrate beállítása](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Az **Áttekintés** területen kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.
4. A **Kiszolgálók felderítése, felmérése és áttelepítése csoportban**kattintson **a Kiszolgálók felmassza és áttelepítése**elemre.

    ![Kiszolgálók felfedezése és értékelése](./media/how-to-add-tool-first-time/assess-migrate.png)

1. A **Kiszolgálók felderítése, értékelése és migrálása** területen kattintson az **Eszközök hozzáadása** lehetőségre.
2. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
3. A **Projekt részletei**területen adja meg a projekt nevét és földrajzi elhelyezkedését, amelyben a projektet létre szeretné hozni. 

    ![Azure Migrate projekt létrehozása](./media/how-to-add-tool-first-time/migrate-project.png)

    Az Azure Áttelepítési projekt et ezek a földrajzi területek bármelyikén létrehozhatja.

   **Földrajz** | **Tárolási hely régiója**
    --- | ---
    Ázsia   | Délkelet-Ázsia vagy Kelet-Ázsia
    Európa | Észak-Európa vagy Nyugat-Európa
    Japán  | Kelet-Japán vagy Nyugat-Japán
    Egyesült Királyság | Egyesült Királyság déli vagy egyesült királyságbeli nyugati
    Egyesült Államok | USA középső vagy usa nyugati régiója 2
    Kanada | Közép-Kanada
    India  | India Közép vagy India Dél
    Ausztrália | Ausztrália Délkelet

    A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. A tényleges áttelepítéshez bármelyik célrégiót kiválaszthatja.

    Ha egy adott régiót szeretne megadni egy földrajzi területen belül az áttelepítési projekt és a kapcsolódó erőforrások üzembe helyezéséhez (az előfizetésben szereplő házirend-korlátozások lehetővé tehetik az Azure-erőforrások csak egy adott Azure-régióra történő üzembe helyezését), használhatja az alábbi API-t hozzon létre egy áttelepítési projektet. Adja meg az előfizetés-azonosítót, az erőforráscsoport nevét, a Projekt nevének áttelepítése a helynel együtt (a táblázatban említett bármely Azure-régió, ahol az Azure Migrate telepítve van.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Kattintson **a Tovább**gombra, és vegyen fel egy értékelési vagy áttelepítési eszközt.

    > [!NOTE]
    > Projekt létrehozásakor legalább egy értékelési vagy áttelepítési eszközt hozzá kell adnia.

5. Az **Értékelési eszköz kiválasztása eszközben**adjon hozzá egy értékelési eszközt. Ha nincs szüksége értékelési **eszközre,** > válassza az Értékelési eszköz hozzáadása lehetőséget most a**Tovább gombra.** 
2. Az **Áttelepítés kiválasztása eszközben**szükség szerint adjon hozzá egy áttelepítési eszközt. Ha most nincs szüksége áttelepítési **eszközre, válassza az Áttelepítési eszköz hozzáadása lehetőséget most** > **tovább**.
3. Az **Ellenőrzés + eszközök hozzáadása párbeszédpanelen**tekintse át a beállításokat, és kattintson az Eszközök **hozzáadása**gombra.

A projekt létrehozása után további eszközöket választhat a kiszolgálók és munkaterhelések, adatbázisok és webalkalmazások értékeléséhez és áttelepítéséhez.

## <a name="create-additional-projects"></a>További projektek létrehozása

Bizonyos körülmények között előfordulhat, hogy további Azure Migrate-projekteket kell létrehoznia. Ha például különböző földrajzi területeken rendelkezik adatközpontokkal, vagy más földrajzi területen kell tárolnia a metaadatokat. Hozzon létre egy további projektet az alábbiak szerint:

1. Az aktuális Azure Áttelepítés projektben kattintson a **Kiszolgálók** vagy **adatbázisok**elemre.
2. A jobb felső sarokban kattintson a **Módosítás** gombra az aktuális projektneve mellett.
3. Új **Settings**projekt **létrehozásához**kattintson ide lehetőséget.
4. Hozzon létre egy új projektet, és vegyen fel egy új eszközt az előző eljárásban leírtak szerint.

## <a name="next-steps"></a>További lépések

További információ további [értékelési](how-to-assess.md) és [áttelepítési](how-to-migrate.md) eszközök hozzáadásáról. 
