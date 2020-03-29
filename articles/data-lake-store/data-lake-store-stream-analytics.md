---
title: Adatok streamelése a Stream Analytics szolgáltatásból az Azure Data Lake Storage Gen1 szolgáltatásba | Microsoft dokumentumok
description: Adatok streamelése az Azure Data Lake Storage Gen1 szolgáltatásba az Azure Stream Analytics segítségével
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60194855"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Adatok streamelése az Azure Storage Blobból az Azure Data Lake Storage Gen1 szolgáltatásba az Azure Stream Analytics használatával
Ebben a cikkben megtudhatja, hogyan használhatja az Azure Data Lake Storage Gen1-et kimenetként egy Azure Stream Analytics-feladathoz. Ez a cikk bemutatja egy egyszerű forgatókönyv, amely beolvassa az adatokat egy Azure Storage blob (bemenet), és írja az adatokat a Data Lake Storage Gen1 (kimenet).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure Storage-fiók.** Egy blob tároló ebből a fiókból a Stream Analytics-feladat adatok beviteléhez. Ebben az oktatóanyagban tegyük fel, hogy van egy storage-fiók nevű **storageforasa** és egy tároló a fiókban nevű **storageforasacontainer**. Miután létrehozta a tárolót, töltsön fel egy mintaadatfájlt. 
  
* **A Data Lake Storage Gen1 fiók**. Kövesse az [Azure Data Lake Storage Gen1 használatának első](data-lake-store-get-started-portal.md)lépéseit az Azure Portal használatával című útmutatóban. Tegyük fel, hogy van egy Data Lake Storage Gen1 fiókja, a **myadlsg1.** 

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
Először hozzon létre egy Stream Analytics-feladatot, amely bemeneti forrást és kimeneti célt tartalmaz. Ebben az oktatóanyagban a forrás egy Azure blob tároló, és a cél a Data Lake Storage Gen1.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. A bal oldali ablaktáblában kattintson a **Stream Analytics-feladatok**elemre, majd a **Hozzáadás**gombra.

    ![Stream Analytics-feladat létrehozása](./media/data-lake-store-stream-analytics/create.job.png "Stream Analytics-feladat létrehozása")

    > [!NOTE]
    > Győződjön meg arról, hogy a feladat létrehozása ugyanabban a régióban, mint a tárfiók, vagy ha további költségeket az adatok régiók közötti áthelyezése.
    >

## <a name="create-a-blob-input-for-the-job"></a>Blob-bemenet létrehozása a feladathoz

1. Nyissa meg a Stream Analytics-feladat lapját, a bal oldali ablaktáblában kattintson a **Bemenetek** fülre, majd a **Hozzáadás**gombra.

    ![Bemenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.input.1.png "Bemenet hozzáadása a feladathoz")

2. Az **Új beviteli** panelen adja meg a következő értékeket.

    ![Bemenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.input.2.png "Bemenet hozzáadása a feladathoz")

   * A **Bemeneti alias**mezőbe írja be a feladat bemenetének egyedi nevét.
   * A **Forrástípus mezőben**válassza **az Adatfolyam**lehetőséget.
   * A **Forrás csoportban**válassza a **Blob storage**lehetőséget.
   * **Előfizetés esetén**válassza a Blob storage használata az **aktuális előfizetésből**lehetőséget.
   * A **Storage-fiók,** válassza ki a tárfiókot, amely az előfeltételek részeként létrehozott. 
   * A **Tároló csoportban**válassza ki a kijelölt tárfiókban létrehozott tárolót.
   * **Az Esemény szerializálási formátuma mezőben**válassza a **CSV**lehetőséget.
   * A **Határoló jel**csoportban válassza a **tab (Lap) lehetőséget.**
   * A **kódoláshoz**válassza az **UTF-8**lehetőséget.

     Kattintson **a Létrehozás gombra.** A portál most hozzáadja a bemenetet, és teszteli a kapcsolatot.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Data Lake Storage Gen1 kimenet létrehozása a feladathoz

1. Nyissa meg a Stream Analytics-feladat **lapját,** kattintson a Kimenetek fülre, a **Hozzáadás**gombra, és válassza a **Data Lake Storage Gen1 lehetőséget.**

    ![Kimenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.output.1.png "Kimenet hozzáadása a feladathoz")

2. Az **Új kimeneti** panelen adja meg a következő értékeket.

    ![Kimenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.output.2.png "Kimenet hozzáadása a feladathoz")

    * A **Kimenetalias mezőbe**írja be a feladat kimenetének egyedi nevét. Ez egy rövid név, amely a lekérdezések kimenetének a Data Lake Storage Gen1 fiókra irányítására szolgál.
    * A rendszer kérni fogja a Data Lake Storage Gen1 fiókhoz való hozzáférést. Kattintson **az Engedélyezés gombra.**

3. Az **Új kimeneti** panelen adja meg a következő értékeket.

    ![Kimenet hozzáadása a feladathoz](./media/data-lake-store-stream-analytics/create.output.3.png "Kimenet hozzáadása a feladathoz")

   * A **Fiók neve,** válassza ki a Data Lake Storage Gen1 fiókot már létrehozott, ahol azt szeretné, hogy a feladat kimenetét kell küldeni.
   * Az **Elérési út előtag minta,** adja meg a fájl elérési útját a megadott Data Lake Storage Gen1 fiókban írt fájlokat.
   * A **Dátum formátumban,** ha dátumtokent használt az előtag elérési útján, kiválaszthatja azt a dátumformátumot, amelyben a fájlok rendszerezve vannak.
   * Az **Idő formátumban,** ha időjogkivonatot használt az előtag elérési útján, adja meg a fájlok rendszerezésének időformátumát.
   * **Az Esemény szerializálási formátuma mezőben**válassza a **CSV**lehetőséget.
   * A **Határoló jel**csoportban válassza a **tab (Lap) lehetőséget.**
   * A **kódoláshoz**válassza az **UTF-8**lehetőséget.
    
     Kattintson **a Létrehozás gombra.** A portál most hozzáadja a kimenetet, és teszteli a kapcsolatot.
    
## <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

1. A Stream Analytics-feladat futtatásához a **Lekérdezés** lapról kell futtatnia egy lekérdezést. Ebben az oktatóanyagban futtathatja a mintalekérdezést úgy, hogy a helyőrzőket a feladat bemeneti és kimeneti aliasaival helyettesíti, ahogy az az alábbi képernyőfelvételen látható.

    ![A lekérdezés futtatása](./media/data-lake-store-stream-analytics/run.query.png "A lekérdezés futtatása")

2. Kattintson a képernyő felső részén a **Mentés** **gombra,** majd az Áttekintés lapon kattintson a **Start**gombra. A párbeszédpanelen válassza az **Egyéni időpont**lehetőséget, majd adja meg az aktuális dátumot és időpontot.

    ![Feladat idejének beállítása](./media/data-lake-store-stream-analytics/run.query.2.png "Feladat idejének beállítása")

    A feladat elindításához kattintson az **Indítás** gombra. Akár pár percig is eltarthat, amíg elindítod a munkát.

3. A blobból származó adatok kiválasztásához indítsa el a feladatot, másolja a mintaadatfájlt a blobtárolóba. Beszerezhet egy mintaadatfájlt az [Azure Data Lake Git repository-ból.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt) Ehhez a bemutatóhoz másoljuk a **fájlt vehicle1_09142014.csv**. Különböző ügyfelek, például az [Azure Storage Explorer](https://storageexplorer.com/)használatával adatokat tölthet fel egy blobtárolóba.

4. Az **Áttekintés** lap **Figyelés**csoportjában tekintse meg az adatok feldolgozásának módját.

    ![Feladat monitorozása](./media/data-lake-store-stream-analytics/run.query.3.png "Feladat monitorozása")

5. Végül ellenőrizheti, hogy a feladat kimeneti adatok elérhetők-e a Data Lake Storage Gen1 fiókban. 

    ![Kimenet ellenőrzése](./media/data-lake-store-stream-analytics/run.query.4.png "Kimenet ellenőrzése")

    Az Adatkezelő ablaktáblán figyelje meg, hogy a kimenet a Data Lake Storage`streamanalytics/job/output/{date}/{time}`Gen1 kimeneti beállításaiban ( ) megadott mappaelérési útra van írva.  

## <a name="see-also"></a>Lásd még
* [HDInsight-fürt létrehozása a Data Lake Storage Gen1 használatához](data-lake-store-hdinsight-hadoop-use-portal.md)
