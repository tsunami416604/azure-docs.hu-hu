---
title: "A Data Lake Store a Stream Analytics adatok folyamatos átviteléhez |} Microsoft Docs"
description: "Az adatfolyam adatok az Azure Data Lake Store Azure Stream Analytics segítségével"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/01/2017
ms.author: nitinme
ms.openlocfilehash: 35b737cf5b53f0ad0dbe4a50772fdcaa2e14ca5e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Adatok streamelése az Azure Storage-blobokból a Data Lake Store-ba az Azure Stream Analytics használatával
Ebben a cikkben, megtudhatja, hogyan használható az Azure Data Lake Store kimenetként egy Azure Stream Analytics-feladat. Ez a cikk bemutatja egy olyan egyszerű forgatókönyvet, amely egy Azure Storage-blobba (bemeneti) olvassa be az adatokat, és írja az adatokat Data Lake Store (kimenet).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-fiók** A blob-tároló ebből a fiókból használandó adja meg a Stream Analytics-feladat adatokat. Az oktatóanyag során feltételezzük, hogy a tárfiók neve **storageforasa** és a tároló, a fiókon belül nevű **storageforasacontainer**. Miután létrehozta a tárolót, egy minta adatfájl feltöltése rá. 
  
* **Azure Data Lake Store-fiók**. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait. Tegyük fel, egy Data Lake Store-fiók neve van **asadatalakestore**. 

## <a name="create-a-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása
Indítsa el a Stream Analytics-feladat, amely tartalmaz egy bemeneti forrás- és egy kimeneti létrehozásával. Ebben az oktatóanyagban a forrás álljon az Azure blob-tároló, és a cél nem Data Lake Store.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali ablaktáblán kattintson a **Stream Analytics-feladatok**, és kattintson a **Hozzáadás**.

    ![A Stream Analytics-feladat létrehozása](./media/data-lake-store-stream-analytics/create.job.png "a Stream Analytics-feladat létrehozása")

    > [!NOTE]
    > Ellenőrizze, hogy a tárfiók ugyanabban a régióban feladatot hoz létre, vagy akkor merülnek fel, amelyekre az adatok régiók közötti áthelyezése további költség nélkül.
    >

## <a name="create-a-blob-input-for-the-job"></a>Egy Blob bemeneti a feladat létrehozása

1. Nyissa meg a Stream Analytics-feladat, a bal oldali ablaktáblán kattintson a **bemenetek** fülre, majd **Hozzáadás**.

    ![Adja hozzá a projekthez bemeneti](./media/data-lake-store-stream-analytics/create.input.1.png "hozzáadása a projekthez bemeneti")

2. Az a **új bemeneti** panelen adja meg a következő értékeket.

    ![Adja hozzá a projekthez bemeneti](./media/data-lake-store-stream-analytics/create.input.2.png "hozzáadása a projekthez bemeneti")

    * A **bemeneti alias**, adja meg a feladat, adjon meg egyedi nevet.
    * A **adatforrástípust**, jelölje be **adatfolyam**.
    * A **forrás**, jelölje be **Blob-tároló**.
    * A **előfizetés**, jelölje be **használja a jelenlegi előfizetés blob-tároló**.
    * A **tárfiók**, válassza ki a létrehozott tárfiók az Előfeltételek részeként. 
    * A **tároló**, válassza ki a tárolóhoz, amelybe a kiválasztott tárolási fiók létrehozott.
    * A **esemény szerializálási formátum**, jelölje be **CSV**.
    * A **elválasztó**, jelölje be **lapon**.
    * A **kódolás**, jelölje be **UTF-8**.

    Kattintson a **Create** (Létrehozás) gombra. A portál most hozzáadja a bemeneti, és ellenőrzi a hozzá való csatlakozást.


## <a name="create-a-data-lake-store-output-for-the-job"></a>A Data Lake Store kimeneti a feladat létrehozása

1. Nyissa meg a lap a Stream Analytics-feladat, kattintson a **kimenetek** fülre, majd **Hozzáadás**.

    ![Adja hozzá a feladatot egy kimeneti](./media/data-lake-store-stream-analytics/create.output.1.png "kimenetnek hozzáadása a projekthez")

2. Az a **új kimeneti** panelen adja meg a következő értékeket.

    ![Adja hozzá a feladatot egy kimeneti](./media/data-lake-store-stream-analytics/create.output.2.png "kimenetnek hozzáadása a projekthez")

    * A **kimeneti alias**, adjon meg egy egyedi nevet a feladat kimenetére. Ez az a lekérdezés kimenete a Data Lake Store a lekérdezésekben használt rövid nevét.
    * A **gyűjtése**, jelölje be **Data Lake Store**.
    * A rendszer kéri az Data Lake Store-fiókba való hozzáférés engedélyezésére. Kattintson a **engedélyezik**.

3. Az a **új kimeneti** panelen adja meg a következő értékek továbbra is.

    ![Adja hozzá a feladatot egy kimeneti](./media/data-lake-store-stream-analytics/create.output.3.png "kimenetnek hozzáadása a projekthez")

    * A **fióknév**, válassza ki a már létrehozott hol kívánja-e a feladatot, kimeneti küldendő Data Lake Store-fiókot.
    * A **elérési út előtag mintája**, adja meg a megadott Data Lake Store-fiók található a fájl írásához használt elérési út.
    * A **dátumformátum**, ha dátum-tokent használt előtag elérési, válassza a dátumformátum, amelyben a fájlok vannak rendezve.
    * A **időformátum**, idő jogkivonat a előtag elérési út esetén adja meg az időformátum, amelyben a fájlok vannak rendezve.
    * A **esemény szerializálási formátum**, jelölje be **CSV**.
    * A **elválasztó**, jelölje be **lapon**.
    * A **kódolás**, jelölje be **UTF-8**.
    
    Kattintson a **Create** (Létrehozás) gombra. A portál most hozzáadja a kimeneti, és ellenőrzi a hozzá való csatlakozást.
    
## <a name="run-the-stream-analytics-job"></a>A Stream Analytics-feladat futtatása

1. A Stream Analytics-feladat futtatásához egy lekérdezést kell futtatnia a **lekérdezés** fülre. Ebben az oktatóanyagban futtathatja a mintalekérdezést azáltal, hogy a helyőrzőket a feladat bemeneti és kimeneti aliasok, az alábbi képernyőfelvételen látható módon.

    ![Lekérdezés](./media/data-lake-store-stream-analytics/run.query.png "lekérdezés futtatása")

2. Kattintson a **mentése** a képernyőt, majd a felső a **áttekintése** lapra, majd **Start**. A párbeszédpanelen válassza ki **egyéni idő**, és utána állítsa be az aktuális dátumot és időt.

    ![Feladat idő beállítása](./media/data-lake-store-stream-analytics/run.query.2.png "feladat idő beállítása")

    Kattintson a **Start** elindítja a feladatot. Azt is eltarthat néhány percig indítsa el a feladatot.

3. Válassza ki az adatokat a blobból a feladat elindítása, másolja egy minta adatfájl a blob-tároló. Beszerezheti a mintaadatfájlokat a [Azure Data Lake Git-tárház](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Ebben az oktatóanyagban most másolja a fájlt **vehicle1_09142014.csv**. Használhatja például a különböző ügyfelek [Azure Tártallózó](http://storageexplorer.com/), feltölteni az adatokat egy blob-tárolóba.

4. Az a **áttekintése** lap **figyelés**, tekintse meg az adatok feldolgozásának módja.

    ![A figyelő feladat](./media/data-lake-store-stream-analytics/run.query.3.png "figyelő feladat")

5. Végül ellenőrizheti, hogy a feladat kimeneti adatok érhető el a Data Lake Store-fiókban. 

    ![Ellenőrizze a kimeneti](./media/data-lake-store-stream-analytics/run.query.4.png "kimeneti ellenőrzése")

    A Data Explorer ablaktáblában figyelje meg, hogy a kimeneti íródik a Data Lake Store megadott egyik mappaelérési útvonalára kimeneti beállításai (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Lásd még:
* [Data Lake Store használatára HDInsight-fürtök létrehozása](data-lake-store-hdinsight-hadoop-use-portal.md)
