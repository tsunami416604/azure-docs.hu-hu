---
title: Rövid útmutató – szinapszis SQL-készlet létrehozása (előzetes verzió) a szinapszis Studio használatával
description: Hozzon létre egy új szinapszis SQL-készletet a szinapszis Studio használatával az útmutató lépéseinek követésével.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f93eb55b888c58ad111bd67b2011ba9c996b16bb
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960265"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-synapse-studio"></a>Rövid útmutató: szinapszis SQL-készlet létrehozása (előzetes verzió) a szinapszis Studio használatával

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez és elemzéséhez. Az SQL-készlet A T-SQL-alapú számítási és tárolási funkciókat kínál. Miután létrehozta az SQL-készletet a szinapszis munkaterületen, az adatmennyiség betölthető, modellezhető, feldolgozható és leküldhető a gyorsabb elemzési elemzéshez.

Ez a rövid útmutató ismerteti, hogyan hozhat létre SQL-készletet egy szinapszis-munkaterületen a szinapszis Studio használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Szinapszis-munkaterület](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigáljon a szinapszis munkaterületre

1. Navigáljon ahhoz a szinapszis-munkaterülethez, ahol az SQL-készletet hozza létre, majd írja be a szolgáltatás nevét (vagy az erőforrás nevét közvetlenül) a keresősávba.
![Azure Portal keresési sáv, amelyben szinapszis-munkaterületek vannak beírva.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. A munkaterületek listájából írja be a megnyitni kívánt munkaterület nevét (vagy annak egy részét). Ebben a példában egy **contosoanalytics**nevű munkaterületet fogunk használni.
![A contoso nevet tartalmazó szinapszis-munkaterületek listázása.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>A Synapse Studio indítása

1. A munkaterület áttekintés területén válassza a **szinapszis Studio elindítása** lehetőséget, hogy megnyissa azt a helyet, ahol az SQL-készlet létre lesz hozva. Írja be a szolgáltatás nevét vagy az erőforrás nevét közvetlenül a keresősávba.
![Azure Portal szinapszis-munkaterület áttekintése](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>SQL-készlet létrehozása a szinapszis Studióban

1. A szinapszis Studio kezdőlapján navigáljon a **felügyeleti hubhoz** a bal oldali navigációs sávon a **kezelés** ikonra kattintva.
![Szinapszis Studio kezdőlapja kiemelve a felügyeleti központ szakaszával.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. A felügyeleti központban navigáljon az **SQL-készletek** szakaszhoz, és tekintse meg a munkaterületen elérhető SQL-készletek aktuális listáját.
![Szinapszis Studio Management hub és SQL-készletek navigáció kiválasztva](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Válassza az **+ új** parancs lehetőséget, az új SQL-készlet létrehozása varázsló pedig megjelenik. 
![A szinapszis Studio Management hub SQL-készletek listázása.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Az **alapok** lapon adja meg a következő adatokat:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **SQL-készlet neve** | contosoedw | Ez az a név, amelyet az SQL-készlet fog tartalmazni. |
    | **Teljesítményszint** | DW100c | Állítsa be ezt a legkisebb méretre a rövid útmutató költségeinek csökkentése érdekében |

    ![SQL-készletek létrehozási folyamat – alapbeállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy az SQL-készletek által használható nevekre vonatkozó korlátozások érvényesek. A nevek nem tartalmazhatnak speciális karaktereket, csak 15 karakterből állhatnak, és nem tartalmazhatnak fenntartott szavakat, és egyedinek kell lenniük a munkaterületen.

4. A következő lapon a **További beállítások**területen válassza a **nincs** lehetőséget, ha az SQL-készletet szeretné kiépíteni az információk nélkül. Az alapértelmezett rendezést hagyja kiválasztva.
![SQL-készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Most nem adunk hozzá címkéket, ezért válassza a **következő: felülvizsgálat + létrehozás**lehetőséget.

1. A **felülvizsgálat + létrehozás** lapon győződjön meg arról, hogy a korábban beírt adatok alapján a részletek helyesek, majd kattintson a **Létrehozás**gombra. 
![SQL-készlet létrehozása folyamat – felülvizsgálati beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Ekkor elindul az erőforrás-kiépítési folyamat.

1. A kiépítés befejezése után lépjen vissza a munkaterületre, majd egy új bejegyzést fog látni az újonnan létrehozott SQL-készlethez.
 ![SQL-készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Az SQL-készlet létrehozása után a munkaterület elérhető lesz az adatok betöltéséhez, a streamek feldolgozásához, a tótól való olvasáshoz stb.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>SQL-készletek tisztítása a szinapszis Studio használatával    

Az alábbi lépések végrehajtásával törölheti az SQL-készletet a munkaterületről a szinapszis Studio használatával.
> [!WARNING]
> Ha töröl egy SQL-készletet, a rendszer eltávolítja az elemzési motort a munkaterületről. Többé nem lesz lehetséges a készlethez való kapcsolódás, és az SQL-készletet használó összes lekérdezés, folyamat és szkript többé nem fog működni.

Ha törölni szeretné az SQL-készletet, tegye a következőket:

1. Navigáljon az SQL-készletekhez a szinapszis Studióban, a felügyeleti központban.
1. Válassza ki a törölni kívánt SQL-készletben lévő három pontot (ebben az esetben **contosoedw**) az SQL-készlethez tartozó parancsok megjelenítéséhez: az ![ SQL-készletek listázása a legutóbb létrehozott készlettel.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Nyomja le a **delete**billentyűt.
1. Erősítse meg a törlést, majd nyomja le a **delete (Törlés** ) gombot.
 ![Megerősítő párbeszédpanel a kiválasztott SQL-készlet törléséhez.](media/quickstart-create-sql-pool/create-sql-pool-studio-29.png)
1. Ha a folyamat sikeresen befejeződik, az SQL-készlet többé nem jelenik meg a munkaterület erőforrásaiban.

## <a name="next-steps"></a>További lépések 
- Tekintse meg a rövid útmutató [: Apache Spark jegyzetfüzet létrehozása](quickstart-apache-spark-notebook.md)című témakört.
- Lásd [: gyors útmutató: SZINAPSZIS SQL-készlet létrehozása a Azure Portal használatával](quickstart-create-sql-pool-portal.md).