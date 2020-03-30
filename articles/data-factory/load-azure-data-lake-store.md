---
title: Adatok betöltése az Azure Data Lake Storage Gen1 szolgáltatásba
description: Adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásba az Azure Data Lake Storage Gen1 szolgáltatásba
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 1325910877d1e030b3bf4114e16d0f81ecea8cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443977"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Adatok betöltése az Azure Data Lake Storage Gen1 szolgáltatásba az Azure Data Factory használatával

[Az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (korábbi nevén Azure Data Lake Store) egy nagyvállalati szintű, nagyméretű tárház big data-elemzési számítási feladatokhoz. A Data Lake Storage Gen1 segítségével bármilyen méretű, típusú és betöltési sebességű adatokat rögzíthet. Az adatok at egy helyen rögzítik a működési és feltáró elemzések.

Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatás segítségével feltöltheti a tó adatokat a meglévő rendszer, és időt takaríthat meg, amikor az elemzési megoldások.

Az Azure Data Factory a következő előnyöket kínálja az adatok Data Lake Storage Gen1-be való betöltéséhez:

* **Könnyen beállítható:** Intuitív, 5 lépéses varázsló parancsfájlok nélkül.
* **Bővített adattár-támogatás:** Beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez. A részletes listát a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)táblázatában található.
* **Biztonságos és megfelelő**: Az adatok átvitele HTTPS-en vagy ExpressRoute-on keresztül történik. A globális szolgáltatásjelenlét biztosítja, hogy az adatok soha nem hagyja el a földrajzi határt.
* **Nagy teljesítmény:** Akár 1 GB/s adatbetöltési sebesség a Data Lake Storage Gen1-be. További információt a [Tevékenységteljesítmény másolása](copy-activity-performance.md)című témakörben talál.

Ez a cikk bemutatja, hogyan tölthet be adatokat az _Amazon S3-ból a Data Lake Storage Gen1 alkalmazásba_a Data Factory Copy Data eszközzel. Hasonló lépéseket követve más típusú adattárakból is másolhat adatokat.

> [!NOTE]
> További információ: [Adatok másolása a Data Lake Storage Gen1 szolgáltatásba az Azure Data Factory használatával című témakörben.](connector-azure-data-lake-store.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Data Lake Storage Gen1 fiók: Ha nem rendelkezik Data Lake Storage Gen1 fiókkal, olvassa el a [Data Lake Storage Gen1 fiók létrehozása című témakörutasításait.](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)
* Amazon S3: Ez a cikk bemutatja, hogyan másolhat adatokat az Amazon S3-ról. Más adattárakat is használhat hasonló lépések végrehajtásával.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **Erőforrás-elemzési** > **Analytics** > **adatgyár**létrehozása lehetőséget:
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lapon adja meg az alábbi képen látható mezők értékeit: 
      
   ![Új adat-előállító lap](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Név**: Adjon meg egy globálisan egyedi nevet az Azure-adat-előállító. Ha a következő hibaüzenet jelenik meg: "Data factory name LoadADLSG1Demo is not available" (A Data factory name LoadADLSG1Demo (Data factory name LoadADLSG1Demo) (Data factory name LoadADLSG1Demo) (Data factory name LoadADLSG1Demo) (Data factory name \"LoadADLSG1Demo)\" (Data factory Például használhatja a _**nevét a nevét**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállítót. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetést, amelyben létre szeretné hozni az adat-előállító. 
    * **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2**lehetőséget.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak más helyeken és régiókban is lehetnek. Ezek az adattárak közé tartozik az Azure Data Lake Storage Gen1, az Azure Storage, az Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatgyárat. A **Data Factory** kezdőlapja az alábbi képen látható módon jelenik meg: 
   
   ![Data factory kezdőlap](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-data-lake-storage-gen1"></a>Adatok betöltése a Data Lake Storage Gen1 szolgáltatásba

1. Az **Első lépések** lapon válassza az **Adatok másolása** csempét az Adatok másolása eszköz elindításához: 

   ![Az Adatok másolása eszköz csempéje](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. A **Tulajdonságok** lapon adja meg a **CopyFromAmazonS3ToADLS értéket** a **Tevékenység neve** mezőhöz, és válassza a **Tovább**gombot:

    ![Tulajdonságok lap](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. A **Forrásadattár** lapon kattintson a **+ Új kapcsolat létrehozása gombra:**

    ![Forrásadattár lap](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Válassza az **Amazon S3**lehetőséget, majd a **Continue (Folytatás) lehetőséget.**
    
    ![Forrásadattár s3 lapja](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Az **Amazon S3 kapcsolat megadása** lapon tegye a következő lépéseket: 
   1. Adja meg a **hozzáférési kulcs azonosítóértékét.**
   2. Adja meg a **titkos hozzáférési kulcs** értékét.
   3. Válassza a **Finish** (Befejezés) elemet.
   
      ![Amazon S3 fiók megadása](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Új kapcsolatot fog látni. Válassza a **Tovább lehetőséget.**
   
   ![Amazon S3 fiók megadása](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. **A bemeneti fájl vagy mappa kiválasztása** lapon tallózzon az átmásolni kívánt mappához és fájlhoz. Jelölje ki a mappát/fájlt, válassza **a Választás**lehetőséget, majd a **Tovább**gombot.

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. A másolási viselkedést a **rekurzív fájlok másolása** és a **bináris másolás** (fájlok másolása is) beállítás kiválasztásával válassza. Válassza a **Tovább lehetőséget:**

    ![Kimeneti mappa megadása](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. A **Céladattár** lapon kattintson a **+ Új kapcsolat elemre**, majd válassza az Azure Data Lake Storage **Gen1**lehetőséget, és válassza a **Folytatás**lehetőséget:

    ![Céladattár lap](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Az **új csatolt szolgáltatás (Azure Data Lake Storage Gen1)** lapon tegye a következő lépéseket: 

   1. Válassza ki a Data Lake Storage Gen1 fiókját a **Data Lake Store-fiók nevéhez.**
   2. Adja meg a **bérlőt**, és válassza a Befejezés gombot.
   3. Válassza a **Tovább lehetőséget.**
   
   > [!IMPORTANT]
   > Ebben a forgatókönyvben az Azure-erőforrások felügyelt identitását használja a Data Lake Storage Gen1 fiók hitelesítéséhez. Ügyeljen arra, hogy az MSI a megfelelő engedélyeket a Data Lake Storage Gen1 az [alábbi utasításokat](connector-azure-data-lake-store.md#managed-identity)követve .
   
   ![Data Lake Storage Gen1 fiók megadása](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. A **Kimeneti fájl vagy mappa kiválasztása** lapon írja be a **copyfroms3** parancsot kimeneti mappanévként, és válassza a **Tovább**gombot: 

    ![Kimeneti mappa megadása](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. A **Beállítások** lapon válassza a **Tovább**lehetőséget:

    ![Beállítások lap](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Az **Összegzés** lapon tekintse át a beállításokat, és válassza a **Tovább**gombot:

    ![Összefoglaló lap](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. A **Telepítés lapon**válassza a **Figyelő** lehetőséget a folyamat (feladat) figyeléséhez:

    ![Üzembe helyezés lap](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **Műveletek** oszlop hivatkozásokat tartalmaz a tevékenységfuttatás részleteinek megtekintéséhez és a folyamat újbóli futtatásához:

    ![Folyamatfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez válassza a Műveletek **oszlop** **Tevékenységfuttatások megtekintése** hivatkozását. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamatfuttatási nézetre, válassza a **folyamatkapcsolatok** közötti kapcsolatot a tetején. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a **Részletek hivatkozást** a **műveletek** a tevékenységfigyelési nézetben. Figyelheti a részleteket, például a forrásból a fogadóba másolt adatok mennyiségét, az adatátviteli áteresztőát, a megfelelő időtartamú végrehajtási lépéseket és a használt konfigurációkat:

    ![Tevékenységfuttatás részleteinek figyelése](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Ellenőrizze, hogy az adatok átvannak-e másolva a Data Lake Storage Gen1 fiókjába: 

    ![A Data Lake Storage Gen1 kimenet ének ellenőrzése](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>További lépések

A Data Lake Storage Gen1 támogatásáról a következő cikkre való továbbjutásért: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1 összekötő](connector-azure-data-lake-store.md)
