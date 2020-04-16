---
title: Gyorsindítás Apache Spark-készlet létrehozása (előzetes verzió)
description: Hozzon létre egy új Apache Spark-készletet egy Azure Synapse Analytics-munkaterülethez az útmutató lépéseit követve.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424432"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Rövid útmutató: Új Apache Spark-készlet létrehozása (előzetes verzió)

A Synapse Analytics különböző elemzési motorokat kínál az adatok betöltéséhez, átalakításához, modellezéséhez, elemzéséhez és kiszolgálásához. Az Apache Spark-készlet nyílt forráskódú big data számítási lehetőségeket kínál. Miután létrehozott egy Apache Spark-készletet a Szinapszis-munkaterületen, az adatok betölthetők, modellezhetők, feldolgozhatók és szolgálhatók az elemzések megszerzéséhez.

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre apache Spark-készletet egy Synapse-munkaterületen az Azure Portal használatával.

> [!IMPORTANT]
> A Spark-példányok számlázása percenként van értékelve, függetlenül attól, hogy használja-e őket, vagy sem. Győződjön meg róla, hogy leállítja a Spark-példányt, miután befejezte a használatát, vagy állítson be egy rövid időtúltöltést. További információkért lásd a cikk **Az erőforrások eltávolítása** című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot a kezdés előtt.](https:/azure.microsoft.com/free/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https:/azure.microsoft.com/free/)
- [Synapse Analytics munkaterület](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Bejelentkezés az [Azure Portalra](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Új Apache Spark-készlet létrehozása

1. A Synapse munkaterületen, ahol létre szeretné hozni az Apache Spark-készletet, kattintson az **Új Apache Spark-készlet**elemre.
![A Synapse munkaterület áttekintése egy piros dobozzal a parancs körül egy új Apache Spark-készlet létrehozásához](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Írja be a következő részleteket az **Alapok** lapon:

    |Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Apache Spark-készlet neve** | Érvényes készletnév | Ez az a név, amely az Apache Spark készlet lesz. |
    | **Csomópont mérete** | Kicsi (4 vCPU / 32 GB) | Állítsa be ezt a legkisebb méretre a rövid útmutató költségeinek csökkentése érdekében |
    | **Automatikus méretezés** | Engedélyezve | Hagyja meg ezt az alapértelmezett beállítást |
    | **Csomópontok száma** | 3 - 40 | Hagyja meg ezt az alapértelmezett beállítást |
    ||||

    ![Az Apache Spark-készlet folyamatot hoz létre – az alapok lapja.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy az Apache Spark-készletek által használható nevekre vonatkozóan speciális korlátozások vonatkoznak. A nevek csak betűket vagy számokat tartalmazhatnak, legfeljebb 15 karakterből állhatnak, betűvel kell kezdődniük, nem tartalmazhatnak fenntartott szavakat, és egyedinek kell lenniük a munkaterületen.

3. Kattintson a **Tovább gombra: további beállítások,** és tekintse át az alapértelmezett beállításokat. Ne módosítsa az alapértelmezett beállításokat.
![Az Apache Spark-készlet folyamatot hoz létre - további beállítások lapon.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Kattintson a **Tovább gombra: címkék**. Ne adjon hozzá címkéket.
![Az Apache Spark-készlet folyamatot hoz létre - további beállítások lapon.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Kattintson az **Áttekintés + létrehozás** elemre.

6. Győződjön meg arról, hogy a részletek a korábban megadottadatok alapján helyesnek tűnnek, majd kattintson a **Létrehozás gombra.**
![Apache Spark-készlet létrehozása flow - ellenőrzés beállítások lap.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. Ezen a ponton az erőforrás-létesítési folyamat elindul, ![jelezve, ha befejeződött az Apache Spark-készlet létrehozása folyamat - erőforrás-kiépítés.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. A kiépítés befejezése után a munkaterületre való visszanavigálás egy új bejegyzést jelenít meg az újonnan létrehozott Apache Spark-készlethez.
 ![Az Apache Spark-készlet folyamat - erőforrás-kiépítés létrehozása.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. Ezen a ponton nincsenek futó erőforrások, nincsenek díjak a Spark, létrehozott metaadatokat a Spark-példányok létrehozni kívánt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi lépéseket követve törölje az Apache Spark-készletet a munkaterületről.
> [!WARNING]
> Az Apache Spark-készlet törlésével eltávolítja az elemzési motort a munkaterületről. A továbbiakban nem lehet csatlakozni a készlethez, és az Apache Spark-készletet használó összes lekérdezés, folyamat és jegyzetfüzet már nem fog működni.

Ha törölni szeretné az Apache Spark-készletet, tegye a következőket:

1. Keresse meg az Apache Spark készletek panel a munkaterületen.
2. Válassza ki a törölni kívánt Apache-készletet (ebben az esetben **contosospark)**
3. Nyomja **le a delete billentyűt.**
 ![Az Apache Spark-készletek listája, a legutóbb létrehozott készlet kiválasztásával.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Erősítse meg a törlést, és nyomja meg a **Törlés** gombot.
 ![Megerősítés párbeszédablak a kijelölt Apache Spark-készlet törléséhez.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Ha a folyamat sikeresen befejeződik, az Apache Spark-készlet már nem jelenik meg a munkaterületi erőforrásokban.

Az SQL-készlet létrehozása után a munkaterületen elérhető az adatok betöltéséhez, az adatfolyamok feldolgozásához, a tóból való olvasáshoz és így.

## <a name="next-steps"></a>További lépések

- Lásd: [Rövid útmutató: Hozzon létre egy Apache Spark-készletet a Synapse Studio-ban webes eszközök használatával.](spark/apache-spark-notebook-create-spark-use-sql.md)
- Lásd: [Rövid útmutató: Hozzon létre egy synapse SQL-készletet az Azure Portalon keresztül.](quickstart-create-sql-pool.md)
