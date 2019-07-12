---
title: Egy értékelés vagy migrálási eszköz hozzáadásához először az Azure Migrate |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre Azure Migrate-projektet, és adjon hozzá egy értékelés vagy migrálási eszköz.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812024"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Egy értékelés vagy migrálási eszköz hozzáadása az első alkalommal

Ez a cikk bemutatja, hogyan adhat hozzá egy értékelés vagy migrálási eszköz egy [Azure Migrate](migrate-overview.md) projekt első alkalommal.  
Az Azure Migrate felderítési, felmérési és a helyszíni alkalmazások és a számítási feladatok és a privát és nyilvános felhőbeli VM-eken, az Azure-ba való migrálásának nyomon a központi agyhoz biztosít. A hub Azure Migrate eszközöket biztosít a felmérés és migrálás, valamint a külső, a független szoftverszállító (ISV) [ajánlatok](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Hozzon létre egy projektet, és a egy eszköz hozzáadása

Egy új Azure Migrate-projektet az Azure-előfizetés beállítása, és a egy eszköz hozzáadásához.

- Azure Migrate-projekt felderítése, a felmérési és a gyűjtött a környezet felmérése, vagy áttelepítése áttelepítési metaadatok tárolására szolgál. 
- Egy projektet a felderített eszközök nyomon követése, és koordinálhatja az elemzésekhez és migráláshoz.

1. Az Azure Portal > **minden szolgáltatás**, keressen **Azure Migrate**.
2. A **szolgáltatások**válassza **Azure Migrate**.

    ![Az Azure Migrate beállítása](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. A **áttekintése**, kattintson a **felmérési és a kiszolgálók áttelepítése**.
4. A **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **felmérési és a kiszolgálók áttelepítése**.

    ![Felderítés és értékelés kiszolgálók](./media/how-to-add-tool-first-time/assess-migrate.png)

1. A **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **eszközök hozzáadása**.
2. A **Migrate projekt**, válassza ki az Azure-előfizetés, és ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
3. A **Project Details**, adja meg a projekt nevét, és a földrajzi hely, amelyben a projekt létrehozásához szeretne. 

    ![Az Azure Migrate-projekt létrehozása](./media/how-to-add-tool-first-time/migrate-project.png)

    Létrehozhat egy Azure Migrate-projekt bármely alábbi földrajzi területek számára.

    **Régiócsoport** | **Tárolási hely régió**
    --- | ---
    Ázsia | Délkelet-Ázsia és Kelet-Ázsia
    Európa | Dél-Európában és Nyugat-Európa
    Egyesült Királyság | Egyesült Királyság déli régiója vagy az Egyesült Királyság nyugati régiója
    Egyesült Államok | USA középső RÉGIÓJA és USA 2. nyugati

    A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. Kiválaszthatja, hogy az áttelepítés tényleges bármely célrégióban.

4. Kattintson a **tovább**, és a egy értékelés vagy migrálási eszköz hozzáadásához.

    > [!NOTE]
    > Egy projekt létrehozásakor hozzá kell legalább egy értékelés vagy migrálási eszközt.

5. A **válassza frissítésfelmérő eszköz**, adjon hozzá egy frissítésfelmérő eszköz. Ha már nincs szüksége a frissítésfelmérő eszközt, jelölje be **kihagyása a frissítésfelmérő eszköz felvétele most** > **tovább**. 
2. A **válassza áttelepítési eszköz**, adjon hozzá egy migrációs eszköz szükség szerint. Ha egy áttelepítési eszköz most már nincs szüksége, válassza ki a **egy migrációs eszköz felvétele most kihagyása** > **tovább**.
3. A **tekintse át + eszközök hozzáadása**, tekintse át a beállításokat, és kattintson a **eszközök hozzáadása**.

A projekt létrehozása után kiválaszthatja a további eszközök elemzésekhez és migráláshoz kiszolgálókon és a számítási feladatok, adatbázisok és webalkalmazásokat.

## <a name="create-additional-projects"></a>További projektek létrehozása

Bizonyos esetekben szükség lehet további Azure Migrate-projekt létrehozása. Például ha az adatközpontok különböző földrajzi területeken rendelkezik, vagy egy másik földrajzi metaadatokat tárolni kívánt. Hozzon létre egy további projektet a következőképpen:

1. A jelenlegi Azure Migrate projektben kattintson **kiszolgálók** vagy **adatbázisok**.
2. Kattintson a jobb felső sarokban, **módosítása** az aktuális projekt neve mellett.
3. A **beállítások**válassza **Ide kattintva hozzon létre egy új projektet**.
4. Hozzon létre egy új projektet, és adjon hozzá egy új eszköz, az előző eljárásban leírtak szerint.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan adhat hozzá további [értékelés](how-to-assess.md) és [áttelepítési](how-to-migrate.md) eszközök. 
