---
title: Apache Spark alkalmazások figyelése a szinapszis Studio használatával
description: A szinapszis Studio használatával figyelheti Apache Spark alkalmazásait.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 4d6c50436ddf68e2610aeb10ddfaaab0a5d060f3
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387353"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>A szinapszis Studio használata a Apache Spark-alkalmazások figyelésére

Az Azure szinapszis Analytics segítségével a munkaterületen lévő Apache Spark-készleteken notebookok, feladatok és más típusú alkalmazások futtatására Apache Spark használható.

Ez a cikk ismerteti, hogyan figyelheti meg Apache Spark-alkalmazásait, így nyomon követheti a legújabb állapotot, problémákat és előrehaladást.

Ez az oktatóanyag a következő feladatokat mutatja be:

* Futó Apache Spark-alkalmazás figyelése
* Befejezett Apache Spark alkalmazás megtekintése
* Megszakított Apache Spark alkalmazás megtekintése
* A hibakeresés nem sikerült Apache Spark alkalmazás

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekilát az oktatóanyagnak, ellenőrizze, hogy megfelel-e a következő feltételeknek:

- Egy szinapszis Studio-munkaterület. Útmutatásért lásd: [a szinapszis Studio-munkaterület létrehozása](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Egy Apache Spark készlet.

## <a name="monitor-running-apache-spark-application"></a>Futó Apache Spark-alkalmazás figyelése

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások**elemet. A Apache Spark futó alkalmazások részleteinek megtekintéséhez válassza a beküldés Apache Spark alkalmazást, és tekintse meg a részleteket. Ha a Apache Spark alkalmazás még fut, nyomon követheti a folyamatot.

  ![futó feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Keresse meg a **Befejezett feladatokat**, az **állapotot**és az **összes időtartamot**.

2. A Apache Spark alkalmazás megszakítása.

3. A napló lekérdezésének frissítése.

4. Tekintse meg a diagramot.

5. Tekintse át az **összegző** információkat.

6. Keresse meg a **naplókat**. A napló adatai üresen futnak a futtatása közben.

    ![futó feladatok megtekintése](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>Befejezett Apache Spark alkalmazás megtekintése

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások**elemet. A befejezett Apache Spark alkalmazások részleteinek megtekintéséhez válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket.

  ![Befejezett feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Keresse meg a **Befejezett feladatokat**, az **állapotot**és az **összes időtartamot**.

2. A napló lekérdezésének frissítése.

3. Nyissa meg Apache Spark History Server hivatkozást a **Spark History Server**elemre kattintva.

4. Tekintse át az **összegzési** adatokat a gráf ikonjára kattintva.

5. Keresse meg a **naplókat**. A legördülő listából különböző típusú naplókat választhat, és a **naplók letöltése**lehetőségre kattintva letöltheti a napló adatait.

6. A feladatokról a generált feladatok diagramján tekinthet meg áttekintést. Alapértelmezés szerint a Graph megjeleníti az összes feladatot. Ezt a nézetet **feladattípus**alapján szűrheti.

7. Alapértelmezés szerint a **folyamatjelző** megjelenítés van kiválasztva. Az adatfolyamatot úgy is megtekintheti, ha a **Megjelenítés** legördülő listában az **olvasás** vagy **írás** lehetőséget választja.

8. A feladatok lejátszásához válassza a **Lejátszás**lehetőséget. A **Leállítás lehetőség kiválasztásával** bármikor leállíthatja a leállítást.

9. Az egér görgetésével nagyíthatja és kicsinyítheti a feladatok gráfját, vagy kiválaszthatja a **nagyítást, hogy illeszkedjen** a képernyőhöz.

10. A feladatütemezés csomópont a következő információkat jeleníti meg az egyes szakaszok esetében:

    * ID.

    * Név vagy leírás.

    * A feladat teljes száma.

    * Olvasott adatok: a bemeneti méret és a shuffle olvasási méret összege.

    * Adatírás: a kimeneti méret és a shuffle írási méret összege.

    * Végrehajtási idő: az első kísérlet kezdési időpontja és a legutóbbi kísérlet befejezési időpontja közötti idő.

    * Sorok száma: a bemeneti rekordok, a kimeneti rekordok, a shuffle olvasási rekordok és a shuffle írási rekordok összege.

    * Haladás.

     ![Befejezett feladatok megtekintése](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Kattintson a diagramra, majd a szakasz részletei megjelennek.

   ![a fázis részletei](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>Megszakított Apache Spark alkalmazás megtekintése

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások**elemet. A megszakított Apache Spark alkalmazások részleteinek megtekintéséhez válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket.

 ![megszakított feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Keresse meg a **Befejezett feladatokat**, az **állapotot**és az **összes időtartamot**.

2. Frissítse a napló lekérdezését.

3. Nyissa meg az Apache History Server hivatkozást a **Spark History Server**elemre kattintva.

4. Tekintse meg a diagramot.

5. Tekintse át az **összegző** információkat.

6. Keresse meg a **naplókat**. A legördülő listából különböző típusú naplókat választhat, és a **naplók letöltése**lehetőségre kattintva letöltheti a napló adatait.

   ![megszakított feladatok megtekintése](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>A hibakeresés nem sikerült Apache Spark alkalmazás

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások**elemet. A sikertelen Apache Spark alkalmazások részleteinek megtekintéséhez válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket.

![Sikertelen feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Keresse meg a **Befejezett feladatokat**, az **állapotot**és az **összes időtartamot**.

2. A napló lekérdezésének frissítése.

3. Nyissa meg Apache Spark History Server hivatkozást a **Spark History Server**elemre kattintva.

4. Tekintse meg a diagramot.

5. Tekintse át az **összegző** információkat.

6. Keresse meg a hiba adatait.

   ![Sikertelen feladatok adatai](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [folyamatok figyelése a szinapszis Studio használatával](how-to-monitor-pipeline-runs.md) című cikket.  
