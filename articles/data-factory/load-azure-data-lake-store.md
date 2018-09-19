---
title: Adatok betöltése az Azure Data Lake Storage Gen1 Azure Data Factory használatával |} A Microsoft Docs
description: Az Azure Data Factory használata adatok másolása az Azure Data Lake Storage Gen1
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 7984d156c1f8d1c29bda57ae39991876341ea0ac
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127244"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Adatok betöltése az Azure Data Lake Storage Gen1 Azure Data Factory használatával

[Az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (korábbi nevén Azure Data Lake Store) van egy vállalati szintű kapacitású adattár a big Data típusú adatok adatelemzési számítási feladatokhoz. Data Lake Storage Gen1 lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok rögzítéséhez. Az adatok egy egy helyen történő műveleti és felderítési jellegű van rögzítve.

Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás az. A szolgáltatás segítségével feltölti a a data lake a meglévő rendszerről és időt takaríthat meg az elemzési megoldások készítése során.

Az Azure Data Factory a Data Lake Storage Gen1 adatok betöltését az alábbi előnyöket biztosítja:

* **Könnyű beállítás**: 5-lépés egy intuitív varázslóval telepítheti nem parancsfájl-szükséges.
* **Sokrétű támogatás adattároló**: a helyszíni és felhőalapú adattárak számos beépített támogatása. Részletes listáját, tekintse meg a fenti táblázat [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok. A szolgáltatás globális jelenlét biztosítja, hogy az adatok a földrajzi határ sosem hagyja el.
* **Nagy teljesítményű**: legfeljebb 1 GB/s Adatbetöltési sebességét, a Data Lake Storage Gen1. További információkért lásd: [másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használható a Data Factory az adatok másolása eszköz _adatok betöltése az Amazon S3 az Data Lake Storage Gen1_. Adatok másolása a más típusú adattárakban hasonló lépéseket követheti.

> [!NOTE]
> További információkért lásd: [másolása az Azure Data Factory használatával adatokat, illetve a Data Lake Storage Gen1](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Data Lake Storage Gen1 fiók: Ha a Data Lake Storage Gen1 fiók nem rendelkezik, tekintse meg a következő témakör utasításait [hozzon létre egy Data Lake Storage Gen1 fiókot](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Az Amazon S3: Ez a cikk bemutatja, hogyan másolhat adatokat egy Amazon S3-ból. Hasonló lépéseket követve más adattárakban is használhatja.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **erőforrás létrehozása** > **Analytics** > **adat-előállító**:
   
   ![Új adat-előállító létrehozása](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** lap, adja meg a mezőket az alábbi képen látható: 
      
   ![Új adat-előállító lap](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Név**: Adja meg az Azure data factory egy globálisan egyedi nevet. Ha a hibaüzenetet kapja "adat-előállító nevét \"LoadADLSG1Demo\" nem érhető el" adja meg a data Factory egy másik nevet. Például használhatja a név  _**sajátneve**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállító. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki az Azure-előfizetés, amelyben az adat-előállító létrehozásához. 
    * **Erőforráscsoport**: a legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válasszon **V2**.
    * **Hely**: válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak más helyeken / régiókban is lehetnek. Ezek adattárak közé tartozik az Azure Data Lake Storage Gen1, az Azure Storage, Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. Létrehozás befejezése után nyissa meg az adat-előállítóhoz. Megjelenik a **adat-előállító** kezdőlapja a következő képen látható módon: 
   
   ![Data factory kezdőlap](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Válassza ki a **létrehozás és Monitorozás** csempére kattintva indítsa el az adatintegrációs alkalmazás külön lapon.

## <a name="load-data-into-data-lake-storage-gen1"></a>Adatok betöltése az Data Lake Storage Gen1

1. Az a **Ismerkedés** lapon válassza ki a **adatok másolása** csempére, hogy az adatok másolása eszköz elindításához: 

   ![Az Adatok másolása eszköz csempéje](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Az a **tulajdonságok** adja meg azokat **CopyFromAmazonS3ToADLS** számára a **feladat neve** mezőt, és válassza **tovább**:

    ![Tulajdonságok lap](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Az a **forrásadattár** kattintson **+ új kapcsolat létrehozása**:

    ![Forrásadattár lap](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Válassza ki **Amazon S3**, és válassza ki **Folytatás**
    
    ![Forrásadattár s3 lap](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Az a **adja meg az Amazon S3-kapcsolat** lapon, tegye a következőket: 
   1. Adja meg a **elérési kulcs Azonosítóját** értéket.
   2. Adja meg a **titkos elérési kulcsát** értéket.
   3. Válassza a **Finish** (Befejezés) elemet.
   
   ![Az Amazon S3-fiók megadása](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Megjelenik egy új kapcsolatot. Kattintson a **Tovább** gombra.
   
   ![Az Amazon S3-fiók megadása](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Az a **a bemeneti fájl vagy mappa kiválasztása** párbeszédpanelen tallózással keresse meg a mappát és fájlt, amelyet másolja át azokat. Válassza ki a mappát vagy fájlt, jelölje be **válasszon**, majd válassza ki **tovább**:

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Válassza ki a másolási viselkedés kiválasztásával a **fájlok másolása a rekurzív módon** és **bináris másolat** (másolja a fájlokat,-van) lehetőségeket. Válassza ki **tovább**:

    ![Adja meg a kimeneti mappa](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Az a **célként megadott adattárba** lap, kattintson **+ új kapcsolat létrehozása**, majd válassza ki **Azure Data Lake Storage Gen1**, és válassza ki **Folytatás**:

    ![Céladattár lap](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Az a **új társított szolgáltatás (Azure Data Lake Storage Gen1)** lapon, tegye a következőket: 

   1. Válassza ki a Data Lake Storage Gen1 fiókját a **Data Lake Store-fiók neve**.
   2. Adja meg a **bérlői**, és válassza ki a Befejezés gombra.
   3. Kattintson a **Tovább** gombra.
   
   > [!IMPORTANT]
   > Ebben a bemutatóban használhat egy _felügyeltszolgáltatás-identitás_ a Data Lake Storage Gen1 fiók hitelesítéséhez. Ügyeljen arra, hogy adja meg az MSI a Data Lake Storage Gen1 a megfelelő engedélyeket az alábbi [ezek az utasítások](connector-azure-data-lake-store.md#using-managed-service-identity-authentication).
   
   ![Adja meg a Data Lake Storage Gen1 fiók](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Az a **a kimeneti fájl vagy mappa kiválasztása** lap, adja meg **copyfroms3** , a kimeneti mappa nevét, és válassza **tovább**: 

    ![Adja meg a kimeneti mappa](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Az a **beállítások** lapon jelölje be **tovább**:

    ![Beállítások lap](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Az a **összefoglalás** lapon tekintse át a beállításokat, és válassza ki **tovább**:

    ![Összefoglaló lap](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Az a **üzembe helyezés lap**válassza **figyelő** a folyamat (feladat) figyeléséhez:

    ![Üzembe helyezés lap](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop tartalmazza a tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások:

    ![Folyamatfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. A folyamat futásához társított tevékenységfuttatások megtekintéséhez jelölje ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Válassza ki, váltson vissza a folyamatfuttatások nézetre a **folyamatok** a fenti hivatkozásra. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Figyelheti a minden egyes másolási tevékenység-végrehajtási részleteit, válassza ki a **részletei** mellett kapcsolni **műveletek** a figyelési nézet tevékenység. Adatait is figyelheti, például a fogadó, a fájlmegosztásra, a megfelelő időtartamot a végrehajtási lépések a forráskiszolgálóról másolt adatok mennyiségét, és a használt konfigurációk:

    ![A figyelő tevékenységfuttatás részletei](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Győződjön meg arról, hogy az adatokat másolja ki a Data Lake Storage Gen1 figyelembe: 

    ![Data Lake Storage Gen1 kimenet ellenőrzése](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>További lépések

Lépjen a Data Lake Storage Gen1 támogatásával kapcsolatos további információt a következő cikket: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-összekötő](connector-azure-data-lake-store.md)
