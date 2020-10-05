---
title: 'Rövid útmutató: Apache Spark-készlet létrehozása (előzetes verzió) a szinapszis Studio használatával'
description: Hozzon létre egy új Apache Spark készletet a szinapszis Studióval a jelen útmutató lépéseinek követésével.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 3/19/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: cdf9a1f211e889146c9a6986eeea7502d3177057
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260835"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-using-synapse-studio"></a>Rövid útmutató: Apache Spark-készlet létrehozása (előzetes verzió) a szinapszis Studio használatával

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez, elemzéséhez és kiszolgálásához. A Apache Spark Pool nyílt forráskódú big data számítási funkciókat kínál. Miután létrehozta a Apache Spark-készletet a szinapszis munkaterületen, az adatgyűjtést, a modellezést, a feldolgozást és a kiszolgált elemzések beszerzésére is képes.  

Ez a rövid útmutató ismerteti, hogyan hozhat létre Apache Spark-készletet egy szinapszis-munkaterületen a szinapszis Studio használatával.

> [!IMPORTANT]
> A Spark-példányok számlázása percenként történik, függetlenül attól, hogy használja-e őket. A használata után állítsa le a Spark-példányt, vagy állítson be egy rövid időkorlátot. További információkért lásd a cikk **Az erőforrások eltávolítása** című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Szinapszis-munkaterület](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigáljon a szinapszis munkaterületre

1. Navigáljon ahhoz a szinapszis-munkaterülethez, ahol a Apache Spark-készletet hozza létre, majd írja be a szolgáltatás nevét (vagy az erőforrás nevét közvetlenül) a keresősávba.
![Azure Portal keresési sáv, amelyben szinapszis-munkaterületek vannak beírva.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. A munkaterületek listájából írja be a megnyitni kívánt munkaterület nevét (vagy annak egy részét). Ebben a példában egy **contosoanalytics**nevű munkaterületet fogunk használni.
![A contoso nevet tartalmazó szinapszis-munkaterületek listázása.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>A Synapse Studio indítása 

1. A munkaterület áttekintés területén válassza a **szinapszis Studio elindítása** lehetőséget a Apache Spark-készlet létrehozási helyének megnyitásához. Írja be a szolgáltatás nevét vagy az erőforrás nevét közvetlenül a keresősávba.
![Azure Portal szinapszis-munkaterület áttekintése](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>A Apache Spark-készlet létrehozása a szinapszis Studióban

1. A szinapszis Studio kezdőlapján navigáljon a **felügyeleti hubhoz** a bal oldali navigációs sávon a **kezelés** ikonra kattintva.
![Szinapszis Studio kezdőlapja kiemelve a felügyeleti központ szakaszával.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. A felügyeleti központban keresse meg a **Apache Spark készletek** szakaszt a munkaterületen elérhető Apache Spark készletek aktuális listájának megtekintéséhez.
![Szinapszis Studio Management hub Apache Spark-készletekkel navigáció kiválasztva](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Válassza az **+ új** lehetőséget, és megjelenik az új Apache Spark készlet létrehozása varázsló. 
![A a szinapszis Studio felügyeleti központja a Spark-készletek listáját tartalmazza.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-23.png)

1. Az **alapok** lapon adja meg a következő adatokat:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Apache Spark készlet neve** | contosospark | A Apache Spark-készlet neve. |
    | **Csomópont mérete** | Kicsi (4 vCPU/32 GB) | Állítsa be ezt a legkisebb méretre a rövid útmutató költségeinek csökkentése érdekében |
    | **Automatikus méretezés** | Disabled (Letiltva) | Ebben a rövid útmutatóban nem szükséges az autoscale méretezés |
    | **Csomópontok száma** | 8 | Kis méret használata a rövid útmutató költségeinek korlátozásához|
    
    ![Szinapszis Studio új Apache Spark készlet űrlapja.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy a Apache Spark-készletek által használható nevekre vonatkozó korlátozások érvényesek. A név csak betűket vagy számokat tartalmazhat, legfeljebb 15 karakterből állhat, betűvel kell kezdődnie, nem tartalmazhat fenntartott szavakat, és egyedinek kell lennie a munkaterületen.

1. A következő lapon (további beállítások) hagyja meg az összes alapértelmezett értéket, majd nyomja meg a **felülvizsgálat + létrehozás** (nem adunk hozzá címkéket).
 ![Szinapszis Studio új Apache Spark készlet űrlapja.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-25.png)

1. Most nem adunk hozzá címkéket, ezért válassza a **felülvizsgálat + létrehozás**lehetőséget.

1. A **felülvizsgálat + létrehozás** lapon győződjön meg arról, hogy a korábban beírt adatok alapján a részletek helyesek, majd kattintson a **Létrehozás**gombra. 
 ![Szinapszis Studio új Apache Spark készlet űrlapja.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. A Apache Spark készlet elindítja a kiépítési folyamatot.
![Szinapszis Studio új Apache Spark készlet űrlapja.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-27.png)

1. Miután a kiépítés befejeződött, megjelenik az új Apache Spark készlet a listában.
![Szinapszis Studio új Apache Spark készlet űrlapja.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Apache Spark készlet erőforrásainak tisztítása a szinapszis Studio használatával

Az alábbi lépések végrehajtásával törölje a Apache Spark-készletet a munkaterületről a szinapszis Studio használatával.
> [!WARNING]
> A Spark-készlet törlése eltávolítja az elemzési motort a munkaterületről. Többé nem lesz lehetséges a készlethez való kapcsolódás, és a Spark-készletet használó összes lekérdezés, folyamat és jegyzetfüzet többé nem fog működni.

Ha törölni szeretné a Apache Spark-készletet, tegye a következőket:

1. Navigáljon a Apache Spark készletekhez a szinapszis Studióban, a felügyeleti központban.
1. Válassza ki a törölni kívánt Apache-készlet melletti három pontot (ebben az esetben **contosospark**) a Apache Spark-készlet parancsainak megjelenítéséhez.
![Apache Spark készletek felsorolása a legutóbb létrehozott készlettel.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)
1. Nyomja le a **delete**billentyűt.
1. Erősítse meg a törlést, majd nyomja le a **delete (Törlés** ) gombot.
 ![Megerősítő párbeszédpanel a kiválasztott Apache Spark-készlet törléséhez.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-30.png)
1. Ha a folyamat sikeresen befejeződik, a Apache Spark-készlet többé nem jelenik meg a munkaterület erőforrásaiban. 

## <a name="next-steps"></a>További lépések

- Lásd [: rövid útmutató: Apache Spark-készlet létrehozása a szinapszis Studióban webes eszközök használatával](quickstart-apache-spark-notebook.md).
- Lásd [: rövid útmutató: Apache Spark készlet létrehozása a Azure Portal használatával](quickstart-create-apache-spark-pool-portal.md).
