---
title: Adatok betöltése az Azure Data Lake Storage Gen2-be
description: Az Adatmásolás Azure Data Factory használatával Azure Data Lake Storage Gen2
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
ms.openlocfilehash: ff43173823919f25f85d32996b77265a8a5864c3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926826"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ba beépített, Big Data Analytics szolgáltatáshoz rendelt képességek összessége. Lehetővé teszi az adatkapcsolatot a fájlrendszer és az objektum tárolási paradigmáinak használatával.

A Azure Data Factory (ADF) egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatással feltöltheti a tavat a helyszíni és felhőalapú adattárakból származó adatokkal, és időt takaríthat meg az elemzési megoldások kiépítésekor. A támogatott összekötők részletes listáját a [támogatott adattárakkal](copy-activity-overview.md#supported-data-stores-and-formats)foglalkozó táblázatban tekintheti meg.

A Azure Data Factory kibővíthető, felügyelt adatátviteli megoldást kínál. Az ADF kibővített architektúrája miatt az adatok nagy adatátviteli sebességgel is beolvashatók. Részletekért lásd: [másolási tevékenység teljesítménye](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan tölthetők be a Data Factory Adatok másolása eszköz az _Amazon Web Services S3 szolgáltatásból_ _Azure Data Lake Storage Gen2ba_való adatok betöltéséhez. Az adatok más típusú adattárakból történő másolásához hasonló lépéseket kell követnie.

>[!TIP]
>Az adatok Azure Data Lake Storage Gen1ból a Gen2 való másolásához tekintse meg [ezt a konkrét bemutatót](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Azure Storage-fiók Data Lake Storage Gen2 engedélyezve: Ha nincs Storage-fiókja, [hozzon létre egy fiókot](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-fiók egy olyan S3 gyűjtővel, amely tartalmaz egy adatforrást: Ez a cikk bemutatja, hogyan másolhat adatok az Amazon S3-ból. A hasonló lépéseket követve más adattárakat is használhat.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása** > **adatok és Analitika** > **Data Factory**:
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **új adat-előállító** lapon adja meg az alábbi képen látható mezők értékét: 
      
   ![Új adat-előállító lap](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Név**: adjon meg egy globálisan egyedi nevet az Azure-beli adatgyár számára. Ha "az adatfeldolgozó neve \"LoadADLSDemo\" nem érhető el" hibaüzenet jelenik meg, adjon meg egy másik nevet az adatelőállító számára. Használhatja például a _**sajátneve**_ **ADFTutorialDataFactory**nevet. Próbálkozzon újra az adatelőállító létrehozásával. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. 
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válassza a **v2**elemet.
    * **Hely**: válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Refactory által használt adattárak más helyszíneken és régiókban is lehetnek. 

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatait a gyárban. Megjelenik a **Data Factory** kezdőlapja, ahogy az a következő képen látható: 
   
   ![Data factory kezdőlap](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2-be

1. Az **első lépések** lapon válassza ki a **adatok másolása** csempét a adatok másolása eszköz elindításához: 

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. A **Tulajdonságok** lapon adja meg a **CopyFromAmazonS3ToADLS** a **feladat neve** mezőben, majd válassza a **Next (tovább**) gombot:

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. A **forrás adattár** lapon kattintson az **+ új kapcsolatok létrehozása**lehetőségre:

    ![Forrásadattár lap](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Válassza ki az **Amazon S3** elemet az összekötő-katalógusból, és válassza a **Folytatás** lehetőséget.
    
    ![Forrás adattár S3 oldal](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Az **Amazon S3-kapcsolatok meghatározása** oldalon hajtsa végre a következő lépéseket:

   1. Itt adhatja meg a **hozzáférési kulcs azonosítójának** értékét.
   2. A **titkos hozzáférési kulcs** értékének megadása.
   3. Kattintson a **Kapcsolat tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Befejezés** lehetőséget.
   4. Ekkor megjelenik egy új, a létrehozott kapcsolatok. Kattintson a **Tovább** gombra.
   
      ![Amazon S3-fiók meghatározása](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. **A bemeneti fájl vagy mappa kiválasztása** lapon tallózzon az átmásolni kívánt mappához és fájlhoz. Válassza ki a mappát/fájlt, majd válassza a **kiválasztás**lehetőséget:

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Adja meg a másolási viselkedést a **Fájlok másolása rekurzív módon** és a **Bináris másolat** beállítások bejelölésével. Kattintson a **Tovább** gombra:

    ![Kimeneti mappa meghatározása](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. A **cél adattár** lapon kattintson az **+ új kapcsolatok létrehozása**lehetőségre, majd válassza a **Azure Data Lake Storage Gen2**lehetőséget, majd a **Folytatás**:

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Az **Azure Data Lake Storage-kapcsolatok meghatározása** oldalon hajtsa végre a következő lépéseket:

   1. Válassza ki a Data Lake Storage Gen2 képes fiókot a "Storage-fiók neve" legördülő listából.
   2. Kattintson a **Befejezés** gombra a kapcsolat létrehozásához. Ezután kattintson a **Tovább** gombra.
   
   ![Azure Data Lake Storage Gen2 fiók meghatározása](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. A **kimeneti fájl vagy mappa kiválasztása** lapon adja meg a **copyfroms3** a kimeneti mappa neveként, majd kattintson a **tovább**gombra. Az ADF a másolás során létrehozza a megfelelő ADLS Gen2 fájlrendszert és almappákat, ha az nem létezik.

    ![Kimeneti mappa meghatározása](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. A **Beállítások** lapon válassza a **tovább** lehetőséget az alapértelmezett beállítások használatához:

    ![Beállítások lap](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Next (tovább) gombra**:

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. A folyamat figyeléséhez az **üzembe helyezés lapon**válassza a **figyelő** elemet:

    ![Üzembe helyezés lap](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop a tevékenység futtatási részleteinek megtekintésére és a folyamat újrafuttatására mutató hivatkozásokat tartalmaz:

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Ha meg szeretné tekinteni a folyamat futtatásához társított tevékenység-futtatásokat, válassza a **műveletek** oszlop **Megtekintés tevékenység futtatása** hivatkozását. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamat futási nézetére, válassza a felül található **folyamatok** hivatkozást. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a tevékenységek figyelése nézet **műveletek** területén található **részletek** hivatkozást (szemüvegek képe). A forrásról a fogadóra másolt adatok mennyiségét, az adatátvitelt, a végrehajtási lépéseket és a megfelelő időtartamot, valamint a használt konfigurációkat is figyelheti:

    ![Tevékenység-futtatási részletek figyelése](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Ellenőrizze, hogy a rendszer átmásolta-e az adatait a Data Lake Storage Gen2-fiókjába.

## <a name="next-steps"></a>Következő lépések

* [Másolási tevékenység – áttekintés](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md)
