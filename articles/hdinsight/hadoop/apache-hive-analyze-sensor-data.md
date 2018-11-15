---
title: Az Apache Hive- és Apache Hadoop – Azure HDInsight érzékelőadatok elemzése
description: Ismerje meg, hogyan elemezhet érzékelőadatokat a HDInsight (Hadoop) az Apache Hive-lekérdezés konzol használatával, majd a PowerView-Microsoft Excelben megjeleníthetők.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c3e4ab9dc03afe1c4a19e738804e6400b0830291
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634413"
---
# <a name="analyze-sensor-data-using-the-apache-hive-query-console-on-apache-hadoop-in-hdinsight"></a>Érzékelőadatok elemzése a HDInsight az Apache Hadoop az Apache Hive-lekérdezés konzol segítségével

Ismerje meg, hogyan elemezhet érzékelőadatokat a Hive-lekérdezés konzol segítségével a HDInsight (az Apache Hadoop), majd a Power View használatával a Microsoft Excelben megjeleníthetők.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtökkel működik. HDInsight csak akkor használható a Windows-verziók alacsonyabb, mint a HDInsight 3.4-es. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).


Ebben a példában a Hive dolgozza fel a korábbi adatok alapján, és azonosíthatja a problémákat, a fűtésrendszerek, a légkondicionáló rendszerek használata. Rendszerek azonosításához, amelyek nem képesek megbízhatóan fenntartani egy megadott hőmérsékletet az alábbi feladatok végrehajtásával:

* Hozzon létre a HIVE-táblák vesszővel tagolt (CSV) fájlban tárolt adatokat lekérdezni.
* Hozzon létre az adatok elemzése a HIVE-lekérdezéseket.
* Elemzett adatok lekéréséhez, a Microsoft Excel használatával csatlakozhat a HDInsight.
* Megjelenítheti az adatokat, használja a Power View.

![Egy a megoldásarchitektúra ábrája](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight (Hadoop) fürtön: lásd: [Hadoop-fürtök létrehozása a HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) fürt létrehozásával kapcsolatos információk.
* A Microsoft Excel 2013-hoz

  > [!NOTE]
  > A Microsoft Excel szolgál az adatvizualizáció jövőjét [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [A Microsoft Hive ODBC-illesztő](https://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>A minta futtatása

1. A webböngészőben lépjen a következő URL-cím: 

         https://<clustername>.azurehdinsight.net

    Cserélje le a `<clustername>` kifejezést a HDInsight-fürt nevére.

    Amikor a rendszer kéri, a rendszergazdai felhasználónevet és jelszót a fürt üzembe helyezésekor használt használatával hitelesíteni.

2. A webes megnyíló lapon kattintson a **Getting Started katalógus** lapon, majd a a **mintaadatokkal megoldások** kategória, kattintson a **elemzése** minta.

    ![Első lépések a katalóguslemezt](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Kövesse a megjelenő utasításokat a weblapon a minta befejezéséhez.
