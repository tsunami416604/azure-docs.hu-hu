---
title: Rövid útmutató Apache Spark-készlet létrehozása (előzetes verzió)
description: Hozzon létre egy új Apache Spark készletet az Azure szinapszis Analytics-munkaterülethez az útmutató lépéseit követve.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0fd8a379927396f2ae44d74c2d968d48fbd039c1
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096367"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Rövid útmutató: új Apache Spark-készlet létrehozása (előzetes verzió)

A szinapszis Analytics számos analitikai motorral segíti az adatai betöltését, átalakítását, modellezését, elemzését és kiszolgálását. Egy Apache Spark-készlet nyílt forráskódú big data számítási képességekkel rendelkezik. Miután létrehozta a Apache Spark-készletet a szinapszis munkaterületen, az adatgyűjtést, a modellezést, a feldolgozást és a kiszolgált elemzések beszerzésére is képes.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Apache Spark készletet egy szinapszis-munkaterületen a Azure Portal használatával.

> [!IMPORTANT]
> A Spark-példányok számlázása percenként történik, függetlenül attól, hogy használja-e őket. A használata után állítsa le a Spark-példányt, vagy állítson be egy rövid időkorlátot. További információkért lásd a cikk **Az erőforrások eltávolítása** című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https:/azure.microsoft.com/free/)
- [Szinapszis Analytics-munkaterület](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [Azure Portalba](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Új Apache Spark-készlet létrehozása

1. A szinapszis munkaterületen, ahol a Apache Spark készletet szeretné létrehozni, kattintson az **új Apache Spark készlet**elemre.
![A szinapszis munkaterület áttekintése, amely egy új Apache Spark készlet létrehozásához szükséges vörös mezővel rendelkezik a parancs körül](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Az **alapok** lapon adja meg a következő adatokat:

    |Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Apache Spark készlet neve** | Egy érvényes készlet neve | A Apache Spark-készlet neve. |
    | **Csomópont mérete** | Kicsi (4 vCPU/32 GB) | Állítsa be ezt a legkisebb méretre a rövid útmutató költségeinek csökkentése érdekében |
    | **Automatikus méretezés** | Engedélyezve | Hagyja meg ezt az alapértelmezett beállítást |
    | **Csomópontok száma** | 3 - 40 | Hagyja meg ezt az alapértelmezett beállítást |
    ||||

    ![Apache Spark készlet létrehozása folyamat – alapismeretek lap.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy a Apache Spark-készletek által használható nevekre vonatkozó korlátozások érvényesek. A név csak betűket vagy számokat tartalmazhat, legfeljebb 15 karakterből állhat, betűvel kell kezdődnie, nem tartalmazhat fenntartott szavakat, és egyedinek kell lennie a munkaterületen.

3. Kattintson a **Tovább gombra: további beállítások** , és tekintse át az alapértelmezett beállításokat. Ne módosítsa az alapértelmezett beállításokat.
![Apache Spark készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Kattintson a **Tovább gombra: címkék**. Ne adjon hozzá címkéket.
![Apache Spark készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Kattintson az **Áttekintés + létrehozás** elemre.

6. Győződjön meg arról, hogy a korábban megadott adatok alapján a részletek helyesek, majd kattintson a **Létrehozás**gombra.
![Apache Spark készlet létrehozása folyamat – felülvizsgálati beállítások lap.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. Ezen a ponton az erőforrás-kiépítési folyamat elindul, ami azt jelzi, hogy a teljes ![Apache Spark készlet folyamat létrehozása – erőforrás kiépítés.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. A kiépítés befejezése után lépjen vissza a munkaterületre, majd megjelenik egy új bejegyzés az újonnan létrehozott Apache Spark-készlethez.
 ![Apache Spark készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. Ezen a ponton nincsenek futó erőforrások, a Spark díjszabása nélkül létrehozott metaadatokat a létrehozni kívánt Spark-példányokkal kapcsolatban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi lépések végrehajtásával törölje a Apache Spark-készletet a munkaterületről.
> [!WARNING]
> Apache Spark-készlet törlése eltávolítja az elemzési motort a munkaterületről. Többé nem lesz lehetséges a készlethez való kapcsolódás, és a Apache Spark készletet használó összes lekérdezés, folyamat és jegyzetfüzet többé nem fog működni.

Ha törölni szeretné a Apache Spark-készletet, tegye a következőket:

1. Navigáljon a munkaterületen a Apache Spark készletek panelre.
2. Válassza ki a törölni kívánt Apache-készletet (ebben az esetben **contosospark**)
3. Nyomja le a **delete**billentyűt.
 ![Apache Spark készletek felsorolása a legutóbb létrehozott készlettel.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Erősítse meg a törlést, majd nyomja le a **delete (Törlés** ) gombot.
 ![Megerősítő párbeszédpanel a kiválasztott Apache Spark-készlet törléséhez.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Ha a folyamat sikeresen befejeződik, a Apache Spark-készlet többé nem jelenik meg a munkaterület erőforrásaiban.

Az SQL-készlet létrehozása után a munkaterület elérhetővé válik az adatok betöltéséhez, az adatfolyamok feldolgozásához, a tótól való olvasáshoz és így tovább.

## <a name="next-steps"></a>További lépések

- Tekintse meg a rövid útmutató [: Apache Spark jegyzetfüzet létrehozása](quickstart-apache-spark-notebook.md)című témakört.
- Lásd [: gyors útmutató: SZINAPSZIS SQL-készlet létrehozása a Azure Portal használatával](quickstart-create-sql-pool.md).
