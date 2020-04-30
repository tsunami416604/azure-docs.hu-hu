---
title: Rövid útmutató – szinapszis SQL-készlet létrehozása
description: Hozzon létre egy új szinapszis SQL-készletet az Azure szinapszis Analytics szinapszis munkaterülethez az útmutató lépéseit követve.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a50f203b8057dd4ea51eff1cf09fc97fb598cb3e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82096435"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Rövid útmutató: szinapszis SQL-készlet létrehozása

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez, elemzéséhez és kiszolgálásához. Az SQL-készlet A T-SQL-alapú számítási és tárolási funkciókat kínál. Miután létrehozta az SQL-készletet a szinapszis-munkaterületen, betöltheti, modellezheti, feldolgozhatja és kiszolgálhatja az elemzések beszerzését.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy SQL-készletet egy szinapszis-munkaterületen a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

[Szinapszis-munkaterület](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Új SQL-készlet létrehozása

1. A szinapszis munkaterületen, ahol az SQL-készletet létre szeretné hozni, kattintson a felső sávban található **új SQL Pool** parancsra.
![Az új SQL-készlet létrehozásához szükséges, a szinapszis munkaterület áttekintése a parancs körüli piros mezővel.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Az **alapok** lapon adja meg a következő adatokat:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **SQL-készlet neve** | Bármely érvényes név | Az SQL-készlet neve. |
    | **Teljesítményszint** | DW100c | Állítsa a legkisebb méretre a gyors üzembe helyezés költségeinek csökkentése érdekében |
    ||||
  
    ![SQL-készlet folyamat létrehozása – alapbeállítások lap.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy az SQL-készletek által használható nevekre vonatkozó korlátozások érvényesek. A nevek nem tartalmazhatnak speciális karaktereket, csak 15 karakterből állhatnak, és nem tartalmazhatnak fenntartott szavakat, és egyedinek kell lenniük a munkaterületen.

3. Kattintson a **Tovább gombra: további beállítások**.
4. Válassza a **nincs** lehetőséget, ha az SQL-készletet adatnélkül szeretné kiépíteni. Hagyja bejelölve az alapértelmezett rendezést.
![SQL-készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Kattintson az **Áttekintés + létrehozás** elemre.
6. Győződjön meg arról, hogy a korábban megadott adatok alapján a részletek helyesek, majd kattintson a **Létrehozás**gombra.
![SQL-készlet létrehozása folyamat – felülvizsgálati beállítások lap.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. Ekkor elindul az erőforrás-kiépítési folyamat.
 ![SQL-készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. A kiépítés befejezése után lépjen vissza a munkaterületre, majd egy új bejegyzést fog látni az újonnan létrehozott SQL-készlethez.
 ![SQL-készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi lépéseket követve törölheti az SQL-készletet a munkaterületről.
> [!WARNING]
> Egy SQL-készlet törlésével a rendszer eltávolítja az elemzési motort és a törölt SQL-készlet adatbázisában tárolt adatok a munkaterületről. Többé nem lehet csatlakozni az SQL-készlethez, és az SQL-készletbe beolvasott vagy írt összes lekérdezés, folyamat és jegyzetfüzet többé nem fog működni.

Ha törölni szeretné az SQL-készletet, hajtsa végre a következő lépéseket:

1. Navigáljon az SQL-készletek panelre a munkaterület panelen
1. Válassza ki a törölni kívánt SQL-készletet (ebben az esetben **contosoedw**)
1. Jelölje ki, majd nyomja le a **delete**billentyűt.
1. Erősítse meg a törlést, majd nyomja le a **delete (Törlés** ) gombot.
 ![SQL Pool – áttekintés – a törlés megerősítésének kiemelése.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Ha a folyamat sikeresen befejeződik, az SQL-készlet többé nem jelenik meg a munkaterület erőforrásaiban.

Az SQL-készlet létrehozása után elérhető lesz a munkaterületen az adatok betöltéséhez, a streamek feldolgozásához, a tótól való olvasáshoz stb.

## <a name="next-steps"></a>További lépések

- Lásd [: rövid útmutató: Apache Spark-készlet létrehozása a szinapszis Studióban webes eszközök használatával](quickstart-apache-spark-notebook.md).
- Lásd [: rövid útmutató: Apache Spark készlet létrehozása a Azure Portal használatával](quickstart-create-apache-spark-pool.md).
