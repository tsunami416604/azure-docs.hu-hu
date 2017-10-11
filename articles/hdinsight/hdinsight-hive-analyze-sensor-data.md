---
title: "Elemezhet érzékelőadatokat a Hive és a Hadoop - Azure HDInsight használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan elemezhet érzékelőadatokat a Hive lekérdezés konzol és a HDInsight (Hadoop) együttes használatával, majd a Microsoft Excel PowerView az adatok megjelenítéséhez."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 3abb71c12b4769bebd808276f8bdd832aad22d7a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>HDInsight hadoop Hive lekérdezés konzol használata érzékelőadatok elemzése

Megtudhatja, hogyan elemezhet érzékelőadatokat a Hive lekérdezés konzol és a HDInsight (Hadoop) együttes használatával, majd a Microsoft Excelben az adatok megjelenítése Power View használatával.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtök dolgozhat. HDInsight csak érhető el a Windows korábbi, mint a HDInsight 3.4-es verziójához. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).


Ez a példa a Hive előzményadatokat feldolgozni, és azonosíthatja a problémákat, a fűtésrendszerek, a légkondicionáló rendszerek használhatja. Pontosabban azonosíthatja a rendszerek nem képesek megbízhatóan fenntartani egy megadott hőmérsékletet az alábbi feladatokat:

* A vesszővel tagolt (CSV) fájlok adataihoz lekérdezés HIVE táblák létrehozása.
* Az adatok elemzése a HIVE-lekérdezések létrehozása.
* Az adatok lekéréséhez a Microsoft Excel használatával csatlakozhat a HDInsight.
* Adatok megjelenítéséhez használja a Power View nézetet.

![Egy a megoldásarchitektúra ábrája](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Előfeltételek

* HDInsight (Hadoop)-fürtöt: lásd: [Hadoop létrehozása a HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md) fürt létrehozásáról további információt.
* A Microsoft Excel 2013

  > [!NOTE]
  > A Microsoft Excel szolgál az adatok vizuális [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [A Microsoft Hive ODBC-illesztőprogram](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>A minta futtatásához

1. Webböngészőből keresse meg a következő URL-címe: 

         https://<clustername>.azurehdinsight.net

    Cserélje le a `<clustername>` kifejezést a HDInsight-fürt nevére.

    Amikor a rendszer kéri, a rendszergazdai felhasználónevet és a fürt létesítésekor használt jelszó használatával hitelesíteni.

2. A megnyíló weblapon, kattintson a **Getting Started gyűjteménye** fülre, majd a a **mintaadatokkal megoldások** kategória, kattintson a **érzékelő adatelemzés** minta.

    ![Első lépések gyűjtemény kép](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Kövesse a megjelenő utasításokat a weblap a minta befejezéséhez.
