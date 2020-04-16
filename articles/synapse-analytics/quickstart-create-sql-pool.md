---
title: Rövid útmutató – Szinapszis SQL-készlet létrehozása
description: Hozzon létre egy új Synapse SQL-készletet egy Azure Synapse Analytics Synapse Workspace számára az útmutató lépéseit követve.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b3cd4ed280529efbde32fb89e6bac20640940fff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423907"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Rövid útmutató: Szinapszis SQL-készlet létrehozása

Az Azure Synapse Analytics különböző elemzési motorokat kínál az adatok betöltéséhez, átalakításához, modellezéséhez, elemzéséhez és kiszolgálásához. Az SQL-készlet T-SQL alapú számítási és tárolási lehetőségeket kínál. Miután létrehozott egy SQL-készletet a Szinapszis-munkaterületen, az adatok betölthetők, modellezhetők, feldolgozhatók és szolgálhatók az elemzések megszerzéséhez.

Ebben a rövid útmutatóban megtudhatja, hogy hozzon létre egy SQL-készlet egy synapse-munkaterület az Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot a kezdés előtt.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Előfeltételek

[Szinapszis munkaterület](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Bejelentkezés az [Azure Portalra](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Új SQL-készlet létrehozása

1. A Synapse munkaterületen, ahol létre szeretné hozni az SQL-készletet, kattintson az **Új SQL-készlet** parancs a felső sávon.
![A Synapse-munkaterület áttekintése egy piros mezővel a parancs körül egy új SQL-készlet létrehozásához.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Írja be a következő részleteket az **Alapok** lapon:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **SQL-készlet neve** | Bármely érvényes név | Az SQL-készlet neve. |
    | **Teljesítményszint** | DW100c | Állítsa a legkisebb méretre a rövid útmutató költségeinek csökkentéséhez |
    ||||
  
    ![SQL-készlet létrehozása folyamat - alapjai lapon.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy az SQL-készletek által használható nevekre vonatkozóan speciális korlátozások vonatkoznak. A nevek nem tartalmazhatnak speciális karaktereket, legfeljebb 15 karakterből állhatatosak, nem tartalmazhatnak fenntartott szavakat, és nem tartalmazhatnak egyedi karaktereket a munkaterületen.

3. Kattintson **a Tovább gombra: További beállítások**.
4. Válassza a **Nincs** lehetőséget az SQL-készlet adatok nélküli kiépítéséhez. Hagyja kijelölve az alapértelmezett illesztést.
![SQL készlet létrehozása folyamat - további beállítások lapon.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Kattintson az **Áttekintés + létrehozás** elemre.
6. Győződjön meg arról, hogy a részletek a korábban megadottadatok alapján helyesnek tűnnek, majd kattintson a **Létrehozás gombra.**
![SQL készlet létrehozása folyamat - ellenőrzés beállítások lap.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. Ezen a ponton az erőforrás-létesítési folyamat elindul.
 ![SQL-készlet létrehozása folyamat - erőforrás-kiépítés.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. A kiépítés befejezése után a munkaterületre való visszanavigálás új bejegyzést jelenít meg az újonnan létrehozott SQL-készlethez.
 ![SQL-készlet létrehozása folyamat - erőforrás-kiépítés.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi lépéseket követve törölje az SQL-készletet a munkaterületről.
> [!WARNING]
> Az SQL-készlet törlése eltávolítja az elemzési motort és a törölt SQL-készlet adatbázisában tárolt adatokat a munkaterületről. A továbbiakban nem lehet csatlakozni az SQL-készlethez, és az SQL-készletbe olvasott vagy beolvasott összes lekérdezés, folyamat és jegyzetfüzet a továbbiakban nem fog működni.

Ha törölni szeretné az SQL-készletet, hajtsa végre az alábbi lépéseket:

1. Keresse meg az SQL-készletek panelt a munkaterület panelen
1. Válassza ki a törölni kívánt SQL-készletet (ebben az esetben **contosoedw)**
1. Jelölje ki, és nyomja le a **delete billentyűt.**
1. Erősítse meg a törlést, és nyomja meg a **Törlés** gombot.
 ![SQL-készlet áttekintése – a törlés megerősítésének kiemelése.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Ha a folyamat sikeresen befejeződik, az SQL-készlet a továbbiakban nem jelenik meg a munkaterületi erőforrásokban.

Az SQL-készlet létrehozása után a munkaterületen lesz elérhető az adatok betöltéséhez, az adatfolyamok feldolgozásához, a tóból való olvasáshoz stb.

## <a name="next-steps"></a>További lépések

- Lásd: [Rövid útmutató: Hozzon létre egy Apache Spark-készletet a Synapse Studio-ban webes eszközök használatával.](spark/apache-spark-notebook-create-spark-use-sql.md)
- Lásd: [Rövid útmutató: Hozzon létre egy Apache Spark-készletet az Azure Portalon.](quickstart-create-apache-spark-pool.md)
