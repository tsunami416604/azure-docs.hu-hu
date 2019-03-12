---
title: Stream a Stream Analyticsből származó adatokat az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Adatok streamelése az Azure Data Lake Storage Gen1 Azure Stream Analytics használatával
services: data-lake-store,stream-analytics
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: nitinme
ms.openlocfilehash: e52cf99e3bdd1960ca361ccde50d85dfaa2209c0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544920"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Stream-adatokat az Azure Storage-Blobból az Azure Data Lake Storage Gen1 Azure Stream Analytics használatával
Ebben a cikkben, megtudhatja, hogyan használható az Azure Data Lake Storage Gen1 kimenetként Azure Stream Analytics-feladat. Ez a cikk bemutatja egy egyszerű forgatókönyvet, amely adatokat olvas be egy Azure Storage-blobot (bemenet) és az adatokat ír az Data Lake Storage Gen1 (kimenet).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-fiók** Ebből a fiókból egy blobtárolót a bemeneti adatokat a Stream Analytics-feladat fogja használni. Ebben az oktatóanyagban fel, hogy egy nevű tárfiókot **storageforasa** és a egy tárolót a fiókon belül **storageforasacontainer**. Miután létrehozta a tárolót, töltse fel egy minta adatfájl azt. 
  
* **A Data Lake Storage Gen1 fiók**. Kövesse az utasításokat, [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md). Tegyük fel, hogy a Data Lake Storage Gen1 fiókkal, amelynek neve **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
Hozzon létre egy Stream Analytics-feladatot, amely tartalmaz egy bemeneti forrás- és egy kimeneti megkezdése. Ebben az oktatóanyagban a forrás egy Azure blob-tároló és a cél a Data Lake Storage Gen1.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali ablaktáblában kattintson **Stream Analytics-feladatok**, és kattintson a **Hozzáadás**.

    ![Hozzon létre egy Stream Analytics-feladat](./media/data-lake-store-stream-analytics/create.job.png "Stream Analytics-feladat létrehozása")

    > [!NOTE]
    > Ellenőrizze, hogy feladatot hoz létre ugyanabban a régióban a storage-fiókot, vagy további díj az adatok régiók közötti mozgatása számolunk.
    >

## <a name="create-a-blob-input-for-the-job"></a>Hozzon létre egy Blob bemeneti a feladathoz

1. Nyissa meg az oldalon a Stream Analytics-feladat, a bal oldali ablaktáblában kattintson a **bemenetek** fülre, majd **Hozzáadás**.

    ![Adjon hozzá egy a feladat bemenetének](./media/data-lake-store-stream-analytics/create.input.1.png "a feladat beviteli mód hozzáadása")

2. Az a **új bemenet** panelen adja meg a következő értékeket.

    ![Adjon hozzá egy a feladat bemenetének](./media/data-lake-store-stream-analytics/create.input.2.png "a feladat beviteli mód hozzáadása")

    * A **bemeneti alias**, adjon meg egy egyedi nevet a feladat bemeneti.
    * A **adatforrástípust**válassza **adatfolyam**.
    * A **forrás**válassza **a Blob storage-**.
    * A **előfizetés**válassza **a blob storage aktuális előfizetésből**.
    * A **tárfiók**, válassza ki a létrehozott tárfiók az Előfeltételek részeként. 
    * A **tároló**, válassza ki a tárolót, amelyet a kiválasztott tárfiók.
    * A **eseményszerializációs formátum**válassza **CSV**.
    * A **elválasztó**válassza **lapon**.
    * A **kódolás**válassza **UTF-8**.

    Kattintson a **Create** (Létrehozás) gombra. A portál most hozzáadja a bemeneti, és azt a kapcsolat tesztelése.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Hozzon létre egy Data Lake Storage Gen1 a feladat kimenete

1. Nyissa meg a Stream Analytics-feladat oldalán kattintson a **kimenetek** lapra, majd **Hozzáadás**, és válassza ki **Data Lake Storage Gen1**.

    ![Kimenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.output.1.png "kimenet hozzáadása a feladathoz")

2. Az a **új kimenet** panelen adja meg a következő értékeket.

    ![Kimenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.output.2.png "kimenet hozzáadása a feladathoz")

    * A **kimeneti alias**, adjon meg egy egyedi nevet a feladat kimenetének. Ez az a lekérdezés kimenete a Data Lake Storage Gen1 fiók lekérdezésekben használt rövid nevét.
    * A Data Lake Storage Gen1 fiókhoz való hozzáférés engedélyezésére kéri. Kattintson a **engedélyezése**.

3. Az a **új kimenet** panelen továbbra is a következő értékeket adja meg.

    ![Kimenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.output.3.png "kimenet hozzáadása a feladathoz")

    * A **fióknév**, válassza ki a Data Lake Storage Gen1 fiókot, hogy már létrehozott, ahol azt szeretné, hogy a feladat kimeneti kell küldeni.
    * A **elérési út előtagmintája**, adjon meg egy fájlelérési utat a fájlokat a megadott Data Lake Storage Gen1 fiókban írásához használt.
    * A **dátumformátum**, ha azon előtag elérési útja dátumtoken használta, kiválaszthatja a dátumformátum, amelyben a fájlok vannak rendszerezve.
    * A **időformátum**, ha korábban egy idő-token azon előtag elérési útja, adja meg az időformátum, amelyben a fájlok vannak rendszerezve.
    * A **eseményszerializációs formátum**válassza **CSV**.
    * A **elválasztó**válassza **lapon**.
    * A **kódolás**válassza **UTF-8**.
    
    Kattintson a **Create** (Létrehozás) gombra. A portál most ad hozzá a kimenetet, és azt a kapcsolat tesztelése.
    
## <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

1. Stream Analytics-feladat futtatása, futtatnia kell a lekérdezés a **lekérdezés** fülre. A jelen oktatóanyag esetében futtathatja a mintalekérdezést a helyőrzőket cserélje le a feladat bemeneti és kimeneti alias, az alábbi képernyőfelvételen látható módon.

    ![Lekérdezés futtatása](./media/data-lake-store-stream-analytics/run.query.png "lekérdezés futtatása")

2. Kattintson a **mentése** a képernyőn, majd a felső a **áttekintése** lapra, majd **Start**. A párbeszédpanelen válassza ki a **egyéni idő**, majd állítsa be az aktuális dátum és idő.

    ![Feladat idő beállítása](./media/data-lake-store-stream-analytics/run.query.2.png "feladat idő beállítása")

    Kattintson a **Start** elindítani a feladatot. Ez akár is igénybe vehet néhány percre beállítva indítsa el a feladatot.

3. Aktiválja a feladatot, az adatok a blobból választja ki, másolja egy Mintaadat-fájlt a blobtárolóba. Megjelenik a származó mintaadatfájlokat a [Azure Data Lake Git-tárház](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Ebben az oktatóanyagban most másolja a fájlt **vehicle1_09142014.csv**. Használhatja például a különböző ügyfelek részére, [Azure Storage Explorer](https://storageexplorer.com/), az adatok feltöltése a blob-tárolóba.

4. Az a **áttekintése** lap **figyelés**, tekintse meg az adatok feldolgozásának módja.

    ![A figyelő feladat](./media/data-lake-store-stream-analytics/run.query.3.png "figyelő feladat")

5. Végül ellenőrizheti, hogy a feladat kimeneti adatokat érhető el a Data Lake Storage Gen1 fiókban. 

    ![Kimenet ellenőrzése](./media/data-lake-store-stream-analytics/run.query.4.png "kimenet ellenőrzése")

    Az adatkezelő panelen figyelje meg, hogy a kimenet íródik a mappaelérési utat a Data Lake Storage Gen1 megadott kimeneti beállításai (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Lásd még
* [Hozzon létre egy HDInsight-fürt használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
