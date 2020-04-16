---
title: Adatok betöltése az Azure Data Lake Storage Gen2-be
description: Adatok másolása az Azure Data Lake Storage Gen2 szolgáltatásba az Azure Data Lake Storage Gen2 szolgáltatásba
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 96674f059e9cbc21c5c8c64eff8c94c810c4aa32
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417776"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Adatok betöltése az Azure Data Lake Storage Gen2 szolgáltatásba az Azure Data Factory segítségével

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az Azure Data Lake Storage Gen2 az [Azure Blob storage-ba](../storage/blobs/storage-blobs-introduction.md)épített big data-elemzési képességek összeskészlete. Ez lehetővé teszi, hogy interfész az adatok segítségével mind a fájlrendszer és az objektum tárolási paradigmák.

Az Azure Data Factory (ADF) egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatás segítségével feltöltheti a tó a helyszíni és felhőalapú adattárak gazdag készletéből származó adatokkal, és időt takaríthat meg az elemzési megoldások létrehozásakor. A támogatott összekötők részletes listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)táblázatában láthatja.

Az Azure Data Factory kibővített, felügyelt adatmozgatási megoldást kínál. Az ADF kibővített architektúrája miatt nagy átviteli sebességgel képes adatokat beadni. További információt a [Tevékenységteljesítmény másolása](copy-activity-performance.md)című témakörben talál.

Ebből a cikkből megtudhatja, hogy a Data Factory Copy Data eszközzel hogyan tölthet be adatokat az _Amazon Web Services S3 szolgáltatásból_ az Azure Data Lake _Storage Gen2_szolgáltatásba. Hasonló lépéseket követve más típusú adattárakból is másolhat adatokat.

>[!TIP]
>Az Azure Data Lake Storage Gen1 gen1-ből a Gen2-be történő adatok másolásához tekintse meg [ezt a konkrét forgatókönyvet.](load-azure-data-lake-storage-gen2-from-gen1.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Azure Storage-fiók data lake storage gen2 engedélyezve: Ha nem rendelkezik tárfiókkal, [hozzon létre egy fiókot.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)
* AWS-fiók egy S3 gyűjtővel, amely adatokat tartalmaz: Ez a cikk bemutatja, hogyan másolhat adatokat az Amazon S3-ról. Más adattárakat is használhat hasonló lépések végrehajtásával.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az > **Erőforrás-adatok létrehozása + Analytics** > **Data Factory** **lehetőséget:**
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lapon adja meg az alábbi képen látható mezők értékeit: 
      
   ![Új adat-előállító lap](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Név**: Adjon meg egy globálisan egyedi nevet az Azure-adat-előállító. Ha a következő hibaüzenet jelenik meg: "Data factory name LoadADLSDemo is not available" (A Data factory name LoadADLSDemo(Data factory name LoadADLSDemo) (Data factory name LoadADLSDemo) (Data factory name LoadADLSDemo) (Data factory name LoadADLSDemo) \"\" (Data factory name LoadADLS Például használhatja a _**nevét a nevét**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállítót. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetést, amelyben létre szeretné hozni az adat-előállító. 
    * **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2**lehetőséget.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak más helyeken és régiókban is lehetnek. 

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatgyárat. A **Data Factory** kezdőlapja az alábbi képen látható módon jelenik meg: 
   
   ![Data factory kezdőlap](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2-be

1. Az **Első lépések** lapon válassza az **Adatok másolása** csempét az Adatok másolása eszköz elindításához: 

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. A **Tulajdonságok** lapon adja meg a **CopyFromAmazonS3ToADLS értéket** a **Tevékenység neve** mezőhöz, és válassza a **Tovább**gombot:

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. A **Forrásadattár** lapon kattintson a **+ Új kapcsolat létrehozása gombra:**

    ![Forrásadattár lap](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Válassza az **Amazon S3** elemet az összekötőgalériából, és válassza a **Continue (Folytatás) lehetőséget.**
    
    ![Forrásadattár s3 lapja](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Az **Amazon S3 kapcsolat megadása** lapon tegye a következő lépéseket:

   1. Adja meg a **hozzáférési kulcs azonosítóértékét.**
   2. Adja meg a **titkos hozzáférési kulcs** értékét.
   3. Kattintson a **Kapcsolat tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Befejezés** lehetőséget.
   4. Látni fogja, hogy egy új kapcsolat jön létre. Kattintson a **Tovább** gombra.
   
      ![Amazon S3 fiók megadása](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. **A bemeneti fájl vagy mappa kiválasztása** lapon tallózzon az átmásolni kívánt mappához és fájlhoz. Jelölje ki a mappát/fájlt, és válassza **a Választás lehetőséget:**

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Adja meg a másolási viselkedést a **Fájlok másolása rekurzív módon** és a **Bináris másolat** beállítások bejelölésével. Válassza a **Tovább lehetőséget:**

    ![Kimeneti mappa megadása](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. A **Céladattár** lapon kattintson a **+ Új kapcsolat elemre**, majd válassza az Azure Data Lake Storage **Gen2**lehetőséget, és válassza a **Folytatás**lehetőséget:

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Az **Azure Data Lake Storage-tároló megadása lapban** tegye a következő lépéseket:

   1. Válassza ki a Data Lake Storage Gen2 képes fiókot a "Tárfiók neve" legördülő listából.
   2. Kattintson a **Befejezés** gombra a kapcsolat létrehozásához. Ezután válassza a **Tovább**gombot.
   
   ![Azure Data Lake Storage Gen2 fiók megadása](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. A **Kimeneti fájl vagy mappa kiválasztása** lapon írja be a **copyfroms3** parancsot kimeneti mappanévként, és válassza a **Tovább**gombot. Az ADF létrehozza a megfelelő ADLS Gen2 fájlrendszert és almappákat a másolás során, ha az nem létezik.

    ![Kimeneti mappa megadása](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. A **Beállítások** lapon válassza a **Tovább** gombot az alapértelmezett beállítások használatához:

    ![Beállítások lap](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Az **Összegzés** lapon tekintse át a beállításokat, és válassza a **Tovább**gombot:

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. A **Telepítés lapon**válassza a **Figyelő** lehetőséget a folyamat figyeléséhez:

    ![Üzembe helyezés lap](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **Műveletek** oszlop hivatkozásokat tartalmaz a tevékenységfuttatás részleteinek megtekintéséhez és a folyamat újbóli futtatásához:

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez válassza a Műveletek **oszlop** **Tevékenységfuttatások megtekintése** hivatkozását. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamatfuttatási nézetre, válassza a **folyamatkapcsolatok** közötti kapcsolatot a tetején. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a **Részletek** hivatkozást (szemüvegkép) a **Műveletek csoportban** a tevékenységfigyelési nézetben. Figyelheti a részleteket, például a forrásból a fogadóba másolt adatok mennyiségét, az adatátviteli áteresztőát, a megfelelő időtartamú végrehajtási lépéseket és a használt konfigurációkat:

    ![Tevékenységfuttatás részleteinek figyelése](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Ellenőrizze, hogy az adatok másolása a Data Lake Storage Gen2 fiókjába.

## <a name="next-steps"></a>További lépések

* [Tevékenység másolása – áttekintés](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md)
