---
title: 'Oktatóanyag: Apache Spark feladatdefiníció létrehozása a szinapszis Studióban'
description: Oktatóanyag – az Azure szinapszis Analytics használatával Spark-feladatdefiníciók hozhatók létre, és elküldhetik azokat egy Apache Spark az Azure szinapszis Analytics-készletbe.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 38678c795b0ce7534de0ca8602c1198bc35f0e05
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206189"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Oktatóanyag: Apache Spark feladatdefiníció létrehozása a szinapszis Studióban

Ez az oktatóanyag azt mutatja be, hogyan használható az Azure szinapszis Studio Apache Spark feladatdefiníció létrehozásához, majd egy Apache Spark-készletbe való beküldéséhez.

Ez az oktatóanyag a következő feladatokat mutatja be:

* Apache Spark feladatdefiníció létrehozása a PySpark (Python)
* Apache Spark feladatdefiníció létrehozása a Sparkhoz (Scala)
* Apache Spark feladatdefiníció létrehozása a .NET Sparkhoz (C#/F #)
* Apache Spark feladatdefiníció beküldése batch-feladatokként
* Apache Spark feladatdefiníció hozzáadása a folyamathoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekilát az oktatóanyagnak, ellenőrizze, hogy megfelel-e a következő feltételeknek:

* Egy Azure szinapszis Analytics-munkaterület. Útmutatásért lásd: [Azure szinapszis Analytics-munkaterület létrehozása](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Egy Apache Spark készlet
* Egy ADLS Gen2 Storage-fiók. A használni kívánt ADLS Gen2 fájlrendszer Storage blob-adattulajdonosának kell lennie. Ha nem, manuálisan kell hozzáadnia az engedélyt.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Apache Spark feladatdefiníció létrehozása a PySpark (Python)

Ebben a szakaszban a PySpark (Python) Apache Spark feladatainak definícióját hozza létre.

1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).

2. A **WordCount.py** és a **shakespear.txt**letöltéséhez Apache Spark-feladatdefiníciók [létrehozásához](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) is megtekintheti a fájlokat. Ezután töltse fel ezeket a fájlokat az Azure Storage-ba: kattintson **az adatok**elemre, válassza a **Storage-fiókok**lehetőséget, és töltse fel a kapcsolódó fájlokat a ADLS Gen2 fájlrendszerbe. Hagyja ki ezt a lépést, ha a fájlok már az Azure Storage-ban vannak. 

     ![Python-fájl feltöltése](./media/apache-spark-job-definitions/upload-python-file.png)

3. Kattintson a hub **fejlesztése** elemre, válassza a **Spark-feladatok definíciói** elemet a bal oldali ablaktáblán, majd kattintson a... elemre. Művelet csomópont a Spark- **feladatdefiníció**mellett, majd válassza a helyi menü **új Spark-feladatok meghatározása** elemét.

     ![új definíció létrehozása a Pythonhoz](./media/apache-spark-job-definitions/create-new-definition.png)

4. Válassza a **PySpark (Python)** elemet a Apache Spark Job definition Main ablak nyelv legördülő listájában.

5. Adja meg Apache Spark-feladatdefiníció adatait. A mintaadatok másolására is lehetőség van.

     |  Tulajdonság   | Leírás   |  
     | ----- | ----- |  
     |Feladatdefiníció neve| Adja meg a Apache Spark-feladatdefiníció nevét. Ez a név bármikor frissíthető, amíg közzé nem teszi. Minta `job definition sample`|
     |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon ki egy fájlt a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. Minta `abfss://…/path/to/wordcount.py`|
     |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok. Minta `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Hivatkozási fájlok| A fő definíciós fájlban való hivatkozáshoz használt további fájlok. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. |
     |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Apache Spark-készletbe.|
     |Spark-verzió| A Apache Spark-készletet futtató Apache Spark verziója.|
     |Végrehajtók| A feladathoz megadott Apache Spark készletben megadható végrehajtók száma.|
     |Végrehajtó mérete| A feladathoz megadott Apache Spark készletben megadott végrehajtók számára használandó magok és memória száma.|  
     |Illesztőprogram mérete| A feladathoz megadott Apache Spark készletben megadott illesztőprogramhoz használandó magok és memória száma.|

     ![A Spark-feladatdefiníció értékének beállítása a Pythonhoz](./media/apache-spark-job-definitions/create-py-definition.png)

6. Válassza a **Közzététel** lehetőséget a Apache Spark feladatdefiníció mentéséhez.

     ![a-konfiguráció közzétételének meghatározása](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Apache Spark (Scala) Apache Spark feladatdefiníció létrehozása

Ebben a szakaszban létrehoz egy Apache Spark Apache Spark (Scala) feladatdefiníció.

 1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).

 2. A **WordCount. jar** és a **shakespear.txt**letöltéséhez Apache Spark-feladatdefiníciók [létrehozásához](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) megtekintheti a fájlokat. Ezután töltse fel ezeket a fájlokat az Azure Storage-ba: kattintson **az adatok**elemre, válassza a **Storage-fiókok**lehetőséget, és töltse fel a kapcsolódó fájlokat a ADLS Gen2 fájlrendszerbe. Hagyja ki ezt a lépést, ha a fájlok már az Azure Storage-ban vannak. 
 
     ![a Scala-struktúra előkészítése](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Kattintson a hub **fejlesztése** elemre, válassza a **Spark-feladatok definíciói** elemet a bal oldali ablaktáblán, majd kattintson a... elemre. Művelet csomópont a Spark- **feladatdefiníció**mellett, majd válassza a helyi menü **új Spark-feladatok meghatározása** elemét.
     ![új definíció létrehozása a Scala számára](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Válassza ki a **Spark (Scala)** elemet a Apache Spark Job definition Main ablak nyelv legördülő listájában.

 5. Adja meg Apache Spark-feladatdefiníció adatait. A mintaadatok másolására is lehetőség van.

     |  Tulajdonság   | Leírás   |  
     | ----- | ----- |  
     |Feladatdefiníció neve| Adja meg a Apache Spark-feladatdefiníció nevét. Ez a név bármikor frissíthető, amíg közzé nem teszi. Minta `job definition sample`|
     |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon ki egy JAR-fájlt a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. Minta `abfss://…/path/to/wordcount.jar`|
     |Fő osztály neve| A fő definíciós fájlban lévő teljes azonosító vagy fő osztály. Minta `WordCount`|
     |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok. Minta `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Hivatkozási fájlok| A fő definíciós fájlban való hivatkozáshoz használt további fájlok. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
     |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Apache Spark-készletbe.|
     |Spark-verzió| A Apache Spark-készletet futtató Apache Spark verziója.|
     |Végrehajtók| A feladathoz megadott Apache Spark készletben megadható végrehajtók száma.|  
     |Végrehajtó mérete| A feladathoz megadott Apache Spark készletben megadott végrehajtók számára használandó magok és memória száma.|
     |Illesztőprogram mérete| A feladathoz megadott Apache Spark készletben megadott illesztőprogramhoz használandó magok és memória száma.|

     ![A Spark-feladatdefiníció értékének beállítása a Scala számára](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. Válassza a **Közzététel** lehetőséget a Apache Spark feladatdefiníció mentéséhez.

     ![a Scala definíciójának közzététele](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Apache Spark feladatdefiníció létrehozása a .NET Sparkhoz (C#/F #)

Ebben a szakaszban létre fog hozni egy Apache Spark feladatdefiníció a .NET Sparkhoz (C#/F #).
 1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).

 2. A **wordcount.zip** és a **shakespear.txt**letöltéséhez [Apache Spark feladatdefiníció létrehozásához](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) tekintse meg a fájlok mintaképét. Ezután töltse fel ezeket a fájlokat az Azure Storage-ba: kattintson **az adatok**elemre, válassza a **Storage-fiókok**lehetőséget, és töltse fel a kapcsolódó fájlokat a ADLS Gen2 fájlrendszerbe. Hagyja ki ezt a lépést, ha a fájlok már az Azure Storage-ban vannak. 

     ![DotNet-struktúra előkészítése](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Kattintson a hub **fejlesztése** elemre, válassza a **Spark-feladatok definíciói** elemet a bal oldali ablaktáblán, majd kattintson a... elemre. Művelet csomópont a Spark- **feladatdefiníció**mellett, majd válassza a helyi menü **új Spark-feladatok meghatározása** elemét.

     ![új definíció létrehozása a DotNet számára](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Válassza a **.net Spark (C#/f #)** lehetőséget a Apache Spark Job definition főablakában található nyelv legördülő listából.

 5. Adja meg Apache Spark-feladatdefiníció adatait. A mintaadatok másolására is lehetőség van.
    
     |  Tulajdonság   | Leírás   |  
     | ----- | ----- |  
     |Feladatdefiníció neve| Adja meg a Apache Spark-feladatdefiníció nevét. Ez a név bármikor frissíthető, amíg közzé nem teszi. Minta `job definition sample`|
     |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon egy ZIP-fájlt, amely tartalmazza a .NET for Apache Spark alkalmazást (azaz a fő végrehajtható fájlt, a felhasználó által definiált függvényeket tartalmazó DLL-eket és az egyéb szükséges fájlokat) a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. Minta `abfss://…/path/to/wordcount.zip`|
     |Fő végrehajtható fájl| A fő végrehajtható fájl a fő definíciós ZIP-fájlban. Minta `WordCount`|
     |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok. Minta `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Hivatkozási fájlok| További fájlok szükségesek a munkavégző csomópontok számára a .NET Apache Spark alkalmazáshoz való végrehajtásához, amely nem szerepel a fő definíciós ZIP-fájlban (azaz függő tégelyekben, a felhasználó által definiált függvény dll-jei és más konfigurációs fájlokban). A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
     |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Apache Spark-készletbe.|
     |Spark-verzió| A Apache Spark-készletet futtató Apache Spark verziója.|
     |Végrehajtók| A feladathoz megadott Apache Spark készletben megadható végrehajtók száma.|  
     |Végrehajtó mérete| A feladathoz megadott Apache Spark készletben megadott végrehajtók számára használandó magok és memória száma.|
     |Illesztőprogram mérete| A feladathoz megadott Apache Spark készletben megadott illesztőprogramhoz használandó magok és memória száma.|

     ![A Spark-feladatdefiníció értékének beállítása a DotNet-hez](./media/apache-spark-job-definitions/create-net-definition.png)

 6. Válassza a **Közzététel** lehetőséget a Apache Spark feladatdefiníció mentéséhez.

      ![DotNet-definíció közzététele](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Apache Spark feladatdefiníció beküldése batch-feladatokként

Apache Spark feladatdefiníció létrehozása után elküldheti azt egy Apache Spark készletbe. Győződjön meg arról, hogy a ADLS Gen2 fájlrendszer Storage blob-adattulajdonosa, amellyel dolgozni szeretne. Ha nem, manuálisan kell hozzáadnia az engedélyt.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>1. forgatókönyv: Apache Spark feladatdefiníció elküldése
 1. Az Apache Spark-feladatdefiníció ablak megnyitásához kattintson rá.

      ![Az elküldéshez nyissa meg a Spark-feladatdefiníció ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Kattintson a **Küldés** ikonra a projektnek a kiválasztott Apache Spark készletbe való beküldéséhez. Az Apache Spark alkalmazás LogQuery megtekintéséhez kattintson a **Spark-figyelés URL-címe** fülre.

    ![A Spark-feladatdefiníció elküldéséhez kattintson a Küldés gombra.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A Spark beküldése párbeszédpanel](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>2. forgatókönyv: folyamatban lévő Apache Spark-feladatok megtekintése

 1. Kattintson a **figyelés**elemre, majd válassza a **Spark-alkalmazások** lehetőséget. Megtalálhatja az elküldött Apache Spark alkalmazást.

     ![Spark-alkalmazás megtekintése](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Ezután kattintson a Apache Spark alkalmazásra, a **LogQuery** ablak jelenik meg. A **LogQuery**-ből megtekintheti a feladatok végrehajtásának folyamatát.
     
     ![Spark-alkalmazás LogQuery megtekintése](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>3. forgatókönyv: kimeneti fájl keresése

 1. Kattintson **az**adatelemre, majd válassza a **Storage-fiókok**lehetőséget. Sikeres Futtatás után nyissa meg a ADLS Gen2 tárolót, és győződjön meg róla, hogy a kimenetek jönnek létre.

     ![Kimeneti fájl megtekintése](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Apache Spark feladatdefiníció hozzáadása a folyamathoz

Ebben a szakaszban egy Apache Spark feladatdefiníció hozzáadása a folyamathoz.

 1. Nyisson meg egy meglévő Apache Spark feladatdefiníció.

 2. Kattintson a jobb felső sarokban lévő ikonra Apache Spark feladatdefiníció, válassza a **meglévő folyamat**vagy az **új folyamat**elemet. További információért tekintse meg a folyamat lapját.

     ![Hozzáadás a folyamathoz](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Hozzáadás a folyamathoz](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt mutatja be, hogyan használható az Azure szinapszis Studio Apache Spark feladatdefiníció létrehozásához, majd egy Apache Spark-készletbe való beküldéséhez. Ezután az Azure szinapszis Studio használatával létrehozhat Power BI adatkészleteket, és kezelheti a Power BI adatokat.

