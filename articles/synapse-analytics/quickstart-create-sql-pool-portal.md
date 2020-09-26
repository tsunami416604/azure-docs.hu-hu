---
title: 'Rövid útmutató: szinapszis SQL-készlet létrehozása (előzetes verzió) a Azure Portal használatával'
description: Az útmutató lépéseinek követésével hozzon létre egy új szinapszis SQL-készletet a Azure Portal használatával.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c89bd01cb2e6947df5ed192d66e0dbae08cf66d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260631"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-the-azure-portal"></a>Rövid útmutató: szinapszis SQL-készlet létrehozása (előzetes verzió) a Azure Portal használatával

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez és elemzéséhez. Az SQL-készlet A T-SQL-alapú számítási és tárolási funkciókat kínál. Miután létrehozta az SQL-készletet a szinapszis munkaterületen, az adatmennyiség betölthető, modellezhető, feldolgozható és leküldhető a gyorsabb elemzési elemzéshez.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre SQL-készletet egy szinapszis-munkaterületen a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Szinapszis-munkaterület](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigáljon a szinapszis munkaterületre

1. Navigáljon ahhoz a szinapszis-munkaterülethez, ahol az SQL-készletet hozza létre, majd írja be a szolgáltatás nevét (vagy az erőforrás nevét közvetlenül) a keresősávba.
![Azure Portal keresési sáv, amely szinapszis-munkaterületeket írt ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) be. 
1. A munkaterületek listájából írja be a megnyitni kívánt munkaterület nevét (vagy annak egy részét). Ebben a példában egy **contosoanalytics**nevű munkaterületet fogunk használni.
![A contoso nevet tartalmazó szinapszis-munkaterületek listázása.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-sql-pool"></a>Új SQL-készlet létrehozása

1. A szinapszis munkaterületen, ahol az SQL-készletet létre szeretné hozni, válassza a felső sávban az **új SQL Pool** parancsot.
![Az új SQL-készlet létrehozásához szükséges, a szinapszis munkaterület áttekintése a parancs körüli piros mezővel.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Az **alapok** lapon adja meg a következő adatokat:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **SQL-készlet neve** | Bármely érvényes név | Az SQL-készlet neve. |
    | **Teljesítményszint** | DW100c | Állítsa a legkisebb méretre a gyors üzembe helyezés költségeinek csökkentése érdekében |

  
    ![SQL-készlet folyamat létrehozása – alapbeállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy az SQL-készletek által használható nevekre vonatkozó korlátozások érvényesek. A nevek nem tartalmazhatnak speciális karaktereket, csak 15 karakterből állhatnak, és nem tartalmazhatnak fenntartott szavakat, és egyedinek kell lenniük a munkaterületen.

3. Válassza a **Tovább: További beállítások** lehetőséget.
4. Válassza a **nincs** lehetőséget, ha az SQL-készletet adatnélkül szeretné kiépíteni. Hagyja bejelölve az alapértelmezett rendezést.
![SQL-készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Győződjön meg arról, hogy a korábban megadott adatok alapján a részletek helyesek. Kattintson a **Létrehozás** gombra.
![SQL-készlet létrehozása folyamat – felülvizsgálati beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. Ekkor elindul az erőforrás-kiépítési folyamat.
 ![SQL-készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. A kiépítés befejezése után lépjen vissza a munkaterületre, majd egy új bejegyzést fog látni az újonnan létrehozott SQL-készlethez.
 ![SQL-készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-sql-pool/create-sql-pool-portal-07.png)


Az SQL-készlet létrehozása után elérhető lesz a munkaterületen az adatok betöltéséhez, a streamek feldolgozásához, a tótól való olvasáshoz stb.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi lépéseket követve törölheti az SQL-készletet a munkaterületről.
> [!WARNING]
> Egy SQL-készlet törlésével a rendszer eltávolítja az elemzési motort és a törölt SQL-készlet adatbázisában tárolt adatok a munkaterületről. Többé nem lehet csatlakozni az SQL-készlethez, és az SQL-készletbe beolvasott vagy írt összes lekérdezés, folyamat és jegyzetfüzet többé nem fog működni.

Ha törölni szeretné az SQL-készletet, hajtsa végre a következő lépéseket:

1. Navigáljon az SQL-készletek panelre a munkaterület panelen
1. Válassza ki a törölni kívánt SQL-készletet (ebben az esetben **contosowdw**)
1. A kijelölés után nyomja le a **delete** billentyűt.
1. Erősítse meg a törlést, **majd nyomja le a DELETE gomb** ![ SQL Pool (Törlés megerősítése) elemet.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Ha a folyamat sikeresen befejeződik, az SQL-készlet többé nem jelenik meg a munkaterület erőforrásaiban.

## <a name="next-steps"></a>Következő lépések

- Lásd [: rövid útmutató: Apache Spark-készlet létrehozása a szinapszis Studióban webes eszközök használatával](quickstart-apache-spark-notebook.md).
- Lásd [: rövid útmutató: Apache Spark készlet létrehozása a Azure Portal használatával](quickstart-create-apache-spark-pool-portal.md).
