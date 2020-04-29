---
title: 'Oktatóanyag – Azure szinapszis Analytics: Spark-feladatdefiníció a Szinapszishoz'
description: Oktatóanyag – az Azure szinapszis Analytics használatával Spark-feladatdefiníciók hozhatók létre, és elküldhetik azokat egy szinapszis Spark-készletbe.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422969"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Oktatóanyag: az Azure szinapszis Analytics használata Apache Spark-feladatdefiníciók létrehozásához a szinapszis Spark-készletekhez

Ez az oktatóanyag azt mutatja be, hogyan használható az Azure szinapszis Analytics a Spark-feladatdefiníciók létrehozásához, majd egy szinapszis Spark-készletbe való beküldéséhez. A beépülő modult többféleképpen is használhatja:

* Kifejlesztheti és elküldheti a Spark-feladatok definícióját egy szinapszis Spark-készleten.
* A beküldést követően megtekintheti a feladatok részleteit.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Kifejlesztheti és elküldheti a Spark-feladatok definícióját egy szinapszis Spark-készleten.
> * A beküldést követően megtekintheti a feladatok részleteit.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure szinapszis Analytics-munkaterület. Útmutatásért lásd: [Azure szinapszis Analytics-munkaterület létrehozása](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Bevezetés

A Spark-feladatdefiníció elküldése előtt meg kell adni a ADLS Gen2 fájlrendszer tárolási blob-adattulajdonosát, amellyel dolgozni szeretne. Ha nem, manuálisan kell hozzáadnia az engedélyt.

### <a name="scenario-1-add-permission"></a>1. forgatókönyv: engedély hozzáadása

1. Nyissa meg [Microsoft Azure](https://ms.portal.azure.com), majd nyissa meg a Storage-fiókot.

2. Kattintson a **tárolók**elemre, majd hozzon létre egy **fájlrendszert**. Ebben az oktatóanyagban a következőt használjuk: `sparkjob`.

    ![A Spark-feladatdefiníció elküldéséhez kattintson a Küldés gombra.](./media/apache-spark-job-definitions/open-azure-container.png)

    ![A Spark beküldése párbeszédpanel](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. `sparkjob`Nyissa meg a következőt:, kattintson **Access Control (iam)**, majd a **Hozzáadás** gombra, és válassza a **szerepkör-hozzárendelés hozzáadása**elemet.

    ![A Spark-feladatdefiníció elküldéséhez kattintson a Küldés gombra.](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![A Spark-feladatdefiníció elküldéséhez kattintson a Küldés gombra.](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Kattintson a **szerepkör-hozzárendelések**, a bemeneti Felhasználónév, majd a felhasználói szerepkör ellenőrzése elemre.

    ![A Spark-feladatdefiníció elküldéséhez kattintson a Küldés gombra.](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>2. forgatókönyv: a mappa szerkezetének előkészítése

A Spark-feladatdefiníció elküldése előtt az egyik feladatnak meg kell tennie a fájlok feltöltését ADLS Gen2 és a mappastruktúrát előkészítéséhez. A fájlok tárolására a szinapszis Studióban használjuk a Storage csomópontot.

1. Nyissa meg az [Azure szinapszis Analytics szolgáltatást](https://web.azuresynapse.net/).

2. Kattintson az **adatok**elemre, válassza a **Storage-fiókok**lehetőséget, majd töltse fel a megfelelő fájlokat a ADLS Gen2 fájlrendszerbe. A Scala, a Java, a .NET és a Python is támogatott. Ez az oktatóanyag az ábrán látható példát használja bemutatóként, a projekt szerkezetét igény szerint módosíthatja.

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Spark-feladatdefiníció létrehozása

1. Nyissa meg az [Azure szinapszis Analytics szolgáltatást](https://web.azuresynapse.net/), és válassza a **fejlesztés**lehetőséget.

2. Válassza ki a **Spark-feladatok definícióit** a bal oldali ablaktáblán.

3. Kattintson a "Spark-feladatok definíciója" jobb oldalán található **műveletek** csomópontra.

     ![Új Spark-feladatdefiníció létrehozása](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. A **műveletek** legördülő listában válassza az **új Spark-feladatdefiníció** elemet.

     ![Új Spark-feladatdefiníció létrehozása](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. Az új Spark-feladatdefiníció ablakban válassza a Language (nyelv) lehetőséget, majd adja meg a következő adatokat:  

   * Válassza a **Language** as **Spark (Scala)** lehetőséget.

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Feladatdefiníció neve| Adja meg a Spark-feladatdefiníció nevét.  Ebben az oktatóanyagban a következőt használjuk: `job definition sample`. Ez a név bármikor frissíthető, amíg közzé nem teszi.|  
    |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon ki egy JAR-fájlt a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. |
    |Fő osztály neve| A fő definíciós fájlban lévő teljes azonosító vagy fő osztály.|
    |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok.|
    |Hivatkozási fájlok| A fő definíciós fájlban való hivatkozáshoz használt további fájlok. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
    |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Spark-készletbe.|
    |Spark-verzió| A Spark-készletet futtató Spark verziója.|
    |Végrehajtók| A feladathoz megadott Spark-készletben megadható végrehajtók száma.|
    |Végrehajtó mérete| A feladathoz megadott Spark-készletben megadott végrehajtók számára felhasználható magok és memória száma.|  
    |Illesztőprogram mérete| A megadott Spark-készletben a feladathoz adott illesztőprogramhoz használandó magok és memória száma.|

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Válassza a **Language** as **PySpark (Python)** lehetőséget.

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Feladatdefiníció neve| Adja meg a Spark-feladatdefiníció nevét.  Ebben az oktatóanyagban a következőt használjuk: `job definition sample`. Ez a név bármikor frissíthető, amíg közzé nem teszi.|  
    |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon ki egy fájlt a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
    |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok.|
    |Hivatkozási fájlok| A fő definíciós fájlban való hivatkozáshoz használt további fájlok. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
    |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Spark-készletbe.|
    |Spark-verzió| A Spark-készletet futtató Spark verziója.|
    |Végrehajtók| A feladathoz megadott Spark-készletben megadható végrehajtók száma.|
    |Végrehajtó mérete| A feladathoz megadott Spark-készletben megadott végrehajtók számára felhasználható magok és memória száma.|  
    |Illesztőprogram mérete| A megadott Spark-készletben a feladathoz adott illesztőprogramhoz használandó magok és memória száma.|

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/create-py-definition.png)

   * Válassza a **Language** as **.net Spark (C#/f #)** lehetőséget.

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Feladatdefiníció neve| Adja meg a Spark-feladatdefiníció nevét.  Ebben az oktatóanyagban a következőt használjuk: `job definition sample`. Ez a név bármikor frissíthető, amíg közzé nem teszi.|  
    |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon ki egy ZIP-fájlt, amely tartalmazza a .NET for Spark alkalmazást (azaz a fő végrehajtható fájlt, a felhasználó által definiált függvényeket tartalmazó DLL-eket és az egyéb szükséges fájlokat) a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
    |Fő végrehajtható fájl| A fő végrehajtható fájl a fő definíciós ZIP-fájlban.|
    |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok.|
    |Hivatkozási fájlok| További fájlok szükségesek a munkavégző csomópontok számára a .NET for Spark-alkalmazás végrehajtásához, amely nem szerepel a fő definíciós ZIP-fájlban (azaz függő tégelyekben, a felhasználó által definiált függvény dll-jei és más konfigurációs fájlokban). A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
    |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Spark-készletbe.|
    |Spark-verzió| A Spark-készletet futtató Spark verziója.|
    |Végrehajtók| A feladathoz megadott Spark-készletben megadható végrehajtók száma.|
    |Végrehajtó mérete| A feladathoz megadott Spark-készletben megadott végrehajtók számára felhasználható magok és memória száma.|  
    |Illesztőprogram mérete| A megadott Spark-készletben a feladathoz adott illesztőprogramhoz használandó magok és memória száma.|

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/create-net-definition.png)

6. Válassza a **Közzététel** lehetőséget a Spark-feladatdefiníció mentéséhez.

    ![Spark-feladatdefiníció közzététele](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Spark-feladatdefiníció beküldése

A Spark-feladatdefiníció létrehozása után elküldheti azt egy szinapszis Spark-készletbe. Az ebben a részben szereplő minták kipróbálása előtt győződjön meg arról, hogy elvégezte a **Get-Started** lépéseit.

### <a name="scenario-1-submit-spark-job-definition"></a>1. forgatókönyv: Spark-feladatdefiníció beküldése

1. A Spark-feladatdefiníció ablak megnyitásához kattintson rá.

      ![Az elküldéshez nyissa meg a Spark-feladatdefiníció ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Kattintson a **Küldés** ikonra a projektnek a kiválasztott Spark-készletbe való beküldéséhez. A Spark **-figyelési URL-cím** lapra kattintva megtekintheti a Spark-alkalmazás LogQuery.

    ![A Spark-feladatdefiníció elküldéséhez kattintson a Küldés gombra.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A Spark beküldése párbeszédpanel](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>2. forgatókönyv: a futó Spark-feladatok megtekintése folyamatban

1. Kattintson a **figyelés**elemre, majd válassza a **Spark-alkalmazások** lehetőséget. Megtalálhatja az elküldött Spark-alkalmazást.

    ![Spark-alkalmazás megtekintése](./media/apache-spark-job-definitions/view-spark-application.png)

2. Ezután kattintson a Spark-alkalmazásra, a **LogQuery** ablak jelenik meg. A **LogQuery**-ből megtekintheti a feladatok végrehajtásának folyamatát.

    ![Spark-alkalmazás LogQuery megtekintése](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>3. forgatókönyv: kimeneti fájl keresése

 1. Kattintson **az**adatelemre, majd válassza a **Storage-fiókok**lehetőséget. Sikeres Futtatás után nyissa meg a ADLS Gen2 tárolót, és győződjön meg róla, hogy a kimenetek jönnek létre.

    ![Kimeneti fájl megtekintése](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt mutatja be, hogyan használható az Azure szinapszis Analytics a Spark-feladatdefiníciók létrehozásához, majd egy szinapszis Spark-készletbe való beküldéséhez. Ezután az Azure szinapszis Analytics használatával létrehozhat Power BI adatkészleteket, és kezelheti a Power BI adatokat. 

- [Kapcsolódás Power BI Desktop-beli adatkapcsolathoz](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Vizualizáció a Power BI használatával](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
