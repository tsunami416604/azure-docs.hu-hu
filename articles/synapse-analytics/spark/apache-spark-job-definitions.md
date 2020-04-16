---
title: 'Oktatóanyag – Azure Synapse Analytics: Spark-feladatdefiníció a Synapse számára'
description: Oktatóanyag – az Azure Synapse Analytics használatával spark-feladatdefiníciókat hozhat létre, és küldje el őket egy Synapse Spark-készletbe.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422969"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Oktatóanyag: Az Azure Synapse Analytics használatával Apache Spark-feladatdefiníciókat hozhat létre a Synapse Spark-készletekhez

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Synapse Analytics spark-feladatdefiníciók létrehozásához, és küldje el őket egy Synapse Spark-készletbe. A beépülő modult többféleképpen is használhatja:

* Dolgozzon ki és küldjön el egy Spark-feladatdefiníciót egy Synapse Spark-készleten.
* A feladat részleteinek megtekintése a beküldés után.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Dolgozzon ki és küldjön el egy Spark-feladatdefiníciót egy Synapse Spark-készleten.
> * A feladat részleteinek megtekintése a beküldés után.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Synapse Analytics munkaterület. További információt az [Azure Synapse Analytics-munkaterület létrehozása című témakörben talál.](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)

## <a name="get-started"></a>Bevezetés

A Spark-feladatdefiníció elküldése előtt a storage blob-adatok tulajdonosa kell lennie a dolgozni kívánt ADLS Gen2 fájlrendszernek. Ha nem, manuálisan kell hozzáadnia az engedélyt.

### <a name="scenario-1-add-permission"></a>1. forgatókönyv: Engedély hozzáadása

1. Nyissa meg a [Microsoft Azure-t,](https://ms.portal.azure.com)majd nyissa meg a Storage-fiókot.

2. Kattintson **a Tárolók**elemre, majd hozzon létre egy **fájlrendszert.** Ebben az oktatóanyagban a következőt használjuk: `sparkjob`.

    ![Kattintson a Küldés gombra a spark-feladat definíciójának elküldéséhez](./media/apache-spark-job-definitions/open-azure-container.png)

    ![A Szikraküldés párbeszédpanel](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Nyissa `sparkjob`meg a **Hozzáférés-vezérlés (IAM) menüt,** majd kattintson a **Hozzáadás** gombra, és válassza **a Szerepkör-hozzárendelés hozzáadása**lehetőséget.

    ![Kattintson a Küldés gombra a spark-feladat definíciójának elküldéséhez](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Kattintson a Küldés gombra a spark-feladat definíciójának elküldéséhez](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Kattintson **a Szerepkör-hozzárendelések gombra**, adja meg a felhasználónevét, majd ellenőrizze a felhasználói szerepkört.

    ![Kattintson a Küldés gombra a spark-feladat definíciójának elküldéséhez](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>2. forgatókönyv: Mappastruktúra előkészítése

A Spark-feladatdefiníció elküldése előtt az egyik feladat, amelyet el kell végeznie, hogy fájlokat töltsön fel az ADLS Gen2-be, és ott készítse elő a mappastruktúrát. A Synapse Studio tárolócsomópontját használjuk a fájlok tárolására.

1. Nyissa meg [az Azure Synapse Analytics szolgáltatást.](https://web.azuresynapse.net/)

2. Kattintson **az Adatok**menü **Tárfiókok parancsára,** és töltse fel a megfelelő fájlokat az ADLS Gen2 fájlrendszerbe elemre. A Scala, a Java, a .NET és a Python támogatása. Ez az oktatóanyag az ábrán szereplő példát bemutatóként használja, és tetszés szerint módosíthatja a projekt szerkezetét.

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Spark-feladatdefiníció létrehozása

1. Nyissa meg [az Azure Synapse Analytics](https://web.azuresynapse.net/)szolgáltatást, és válassza a **Fejlesztés**lehetőséget.

2. Válassza ki a **Spark-feladatdefiníciókat** a bal oldali ablaktáblából.

3. Kattintson a "Spark-feladatdefiníciók" jobb oldalán található **Műveletek** csomópontra.

     ![Új spark-feladatdefiníció létrehozása](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. A **Műveletek** legördülő listából válassza az **Új Spark feladatdefiníciót.**

     ![Új spark-feladatdefiníció létrehozása](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. Az Új Spark feladatdefiníciós ablakban válassza ki a nyelvet, majd adja meg a következő információkat:  

   * Válassza a **Nyelv lehetőséget** **Szikraként(Scala)**.

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Feladatdefiníció neve| Adja meg a Spark-feladatdefiníció nevét.  Ebben az oktatóanyagban a következőt használjuk: `job definition sample`. Ez a név bármikor frissíthető a közzétételig.|  
    |Fő definíciós fájl| A feladathoz használt fő fájl. Jelöljön ki egy JAR-fájlt a tárhelyről. A **Fájl feltöltése** lehetőséget választva feltöltheti a fájlt egy tárfiókba. |
    |Fő osztály neve| A teljes mértékben minősített azonosító vagy a fő definíciós fájlban lévő fő osztály.|
    |Parancssori argumentumok| Nem kötelező argumentumok a feladathoz.|
    |Referenciafájlok| A fő definíciós fájlban hivatkozásra használt további fájlok. A **Fájl feltöltése** lehetőséget választva feltöltheti a fájlt egy tárfiókba.|
    |Spark medence| A feladat elküldésre kerül a kiválasztott Spark-készletbe.|
    |Spark-verzió| Spark, amely a Spark-készlet fut.|
    |Végrehajtók| A feladathoz a megadott Spark-készletben megadandó végrehajtók száma.|
    |Végrehajtó mérete| A feladathoz megadott Spark-készletben megadott végrehajtókhoz használandó magok és memória száma.|  
    |Illesztőprogram mérete| A feladathoz megadott Spark-készletben megadott illesztőprogramhoz használandó magok és memória száma.|

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Válassza a **Nyelv lehetőséget** **PySpark(Python)** néven.

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Feladatdefiníció neve| Adja meg a Spark-feladatdefiníció nevét.  Ebben az oktatóanyagban a következőt használjuk: `job definition sample`. Ez a név bármikor frissíthető a közzétételig.|  
    |Fő definíciós fájl| A feladathoz használt fő fájl. Jelöljön ki egy PY-fájlt a tárhelyről. A **Fájl feltöltése** lehetőséget választva feltöltheti a fájlt egy tárfiókba.|
    |Parancssori argumentumok| Nem kötelező argumentumok a feladathoz.|
    |Referenciafájlok| A fő definíciós fájlban hivatkozásra használt további fájlok. A **Fájl feltöltése** lehetőséget választva feltöltheti a fájlt egy tárfiókba.|
    |Spark medence| A feladat elküldésre kerül a kiválasztott Spark-készletbe.|
    |Spark-verzió| Spark, amely a Spark-készlet fut.|
    |Végrehajtók| A feladathoz a megadott Spark-készletben megadandó végrehajtók száma.|
    |Végrehajtó mérete| A feladathoz megadott Spark-készletben megadott végrehajtókhoz használandó magok és memória száma.|  
    |Illesztőprogram mérete| A feladathoz megadott Spark-készletben megadott illesztőprogramhoz használandó magok és memória száma.|

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/create-py-definition.png)

   * Válassza **a Nyelv** lehetőséget **.NET Spark(C#/F#) néven.**

    |  Tulajdonság   | Leírás   |  
    | ----- | ----- |  
    |Feladatdefiníció neve| Adja meg a Spark-feladatdefiníció nevét.  Ebben az oktatóanyagban a következőt használjuk: `job definition sample`. Ez a név bármikor frissíthető a közzétételig.|  
    |Fő definíciós fájl| A feladathoz használt fő fájl. Jelöljön ki egy ZIP-fájlt, amely tartalmazza a .NET for Spark alkalmazást (azaz a fő végrehajtható fájlt, a felhasználó által definiált függvényeket tartalmazó DLL-eket és más szükséges fájlokat) a tárolóból. A **Fájl feltöltése** lehetőséget választva feltöltheti a fájlt egy tárfiókba.|
    |Fő végrehajtható fájl| A fő végrehajtható fájl a fő meghatározás ZIP fájlt.|
    |Parancssori argumentumok| Nem kötelező argumentumok a feladathoz.|
    |Referenciafájlok| A feldolgozócsomópontok számára a .NET for Spark alkalmazás végrehajtásához szükséges további fájlok, amelyek nem szerepelnek a fő definíciós ZIP-fájlban (azaz függő üvegek, további felhasználó által definiált függvény DLL-ek és más konfigurációs fájlok). A **Fájl feltöltése** lehetőséget választva feltöltheti a fájlt egy tárfiókba.|
    |Spark medence| A feladat elküldésre kerül a kiválasztott Spark-készletbe.|
    |Spark-verzió| Spark, amely a Spark-készlet fut.|
    |Végrehajtók| A feladathoz a megadott Spark-készletben megadandó végrehajtók száma.|
    |Végrehajtó mérete| A feladathoz megadott Spark-készletben megadott végrehajtókhoz használandó magok és memória száma.|  
    |Illesztőprogram mérete| A feladathoz megadott Spark-készletben megadott illesztőprogramhoz használandó magok és memória száma.|

    ![A Spark-feladatdefiníció értékének beállítása](./media/apache-spark-job-definitions/create-net-definition.png)

6. Válassza **a Közzététel** lehetőséget a Spark-feladatdefiníció mentéséhez.

    ![Spark-feladatdefiníció közzététele](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Spark-feladatdefiníció küldése

A Spark-feladatdefiníció létrehozása után elküldheti azt egy Synapse Spark-készletbe. Győződjön meg arról, hogy az **Első lépések** részben végigment, mielőtt mintákat próbálna ki ebben a részben.

### <a name="scenario-1-submit-spark-job-definition"></a>1. forgatókönyv: Spark-feladatdefiníció küldése

1. Megnyithat egy szikraprojekt-definíciós ablakot, kattintson rá.

      ![A küldéshez nyitott szikrafeladat-definíció ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Kattintson **a Küldés** ikonra, ha el szeretné küldeni a projektet a kiválasztott Spark-készletbe. A **Spark figyelési URL-címe** fülre kattintva megtekintheti a Spark-alkalmazás LogQueryjét.

    ![Kattintson a Küldés gombra a spark-feladat definíciójának elküldéséhez](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A Szikraküldés párbeszédpanel](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>2. forgatókönyv: A Spark-feladat futó állapotának megtekintése

1. Kattintson **a Figyelő**gombra, majd válassza a **Spark-alkalmazások** lehetőséget. A benyújtott Spark-alkalmazás megtalálható.

    ![Spark-alkalmazás megtekintése](./media/apache-spark-job-definitions/view-spark-application.png)

2. Ezután kattintson a Spark alkalmazás, **LogQuery** ablak jelenik meg. A feladat végrehajtási folyamatát a **LogQuery**programból tekintheti meg.

    ![Spark-alkalmazás LogQuery megtekintése](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>3. forgatókönyv: A kimeneti fájl ellenőrzése

 1. Kattintson **az Adatok**menüre, majd válassza **a Tárfiókok lehetőséget.** Sikeres futtatás után az ADLS Gen2 tárolóra léphet, és ellenőrizheti a kimenetek létrejöttét.

    ![Kimeneti fájl megtekintése](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Synapse Analytics spark-feladatdefiníciók létrehozásához, és küldje el őket egy Synapse Spark-készletbe. Ezután az Azure Synapse Analytics segítségével hozhat létre Power BI-adatkészleteket és kezelheti a Power BI-adatokat. 

- [Csatlakozás adatokhoz a Power BI Desktopban](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Vizualizáció a Power BI használatával](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
