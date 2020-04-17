---
title: Értékelési/áttelepítési eszköz hozzáadása az Azure Áttelepítése szolgáltatásban
description: Bemutatja, hogyan hozhat létre egy Azure Migrate projektet, és hogyan adhat hozzá értékelési/áttelepítési eszközt.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 48bdea31d17ea1ddf0b983af962dce30b22d8dcf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537729"
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
3. A **Projekt részletei**területen adja meg a projekt nevét és földrajzi elhelyezkedését, amelyben a projektet létre szeretné hozni.  Tekintse át a támogatott földrajzi területeket [az állami](migrate-support-matrix.md#supported-geographies-public-cloud) és [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)számára.

    ![Azure Migrate projekt létrehozása](./media/how-to-add-tool-first-time/migrate-project.png)

    - A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. A tényleges áttelepítéshez bármelyik célrégiót kiválaszthatja.
    - Ha egy adott régión belül kell telepítenie egy projektet egy földrajzi területen, a következő API-val hozzon létre egy projektet. Adja meg az előfizetés azonosítóját, az erőforráscsoport nevét és a projekt nevét a helynel együtt. Tekintse át a földrajzi /régiók [at állami](migrate-support-matrix.md#supported-geographies-public-cloud) és [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government).

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
