---
title: Adatok betöltése az Azure Data Lake tárolási Gen2 (előzetes) az Azure Data Factoryvel
description: Azure Data Factory használatával adatait átmásolhatja Azure Data Lake tárolási Gen2 (előzetes verzió)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 961c8dea4dbb6b6600d10b75e84a9a84c34c329b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036185"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-preview-with-azure-data-factory"></a>Adatok betöltése az Azure Data Lake tárolási Gen2 előzetes az Azure Data Factoryvel

[Az Azure Data Lake tárolási Gen2 előzetes](../storage/data-lake-storage/introduction.md) rendelkező hierarchikus fájlrendszer névtér és biztonsági szolgáltatásokat protokoll ad hozzá Azure Blob Storage Ez megkönnyíti a tartós tárolási réteg elemzési keretrendszerek csatlakozni. A Data Lake tárolási Gen2 (előzetes verzió), miközben megpróbálta felvenni a következő előnyöket egy fájl rendszer felületet maradnak objektum tároló összes minőség.

Az Azure Data Factory egy olyan felhőalapú teljes körűen felügyelt adatok integrációs szolgáltatás. A szolgáltatás segítségével a lake helyszíni széles skáláját származó adatokkal feltölteni, és a felhőalapú adatokat tárolja, és időt takaríthat meg az elemzési megoldásokat felépítése közben. Támogatott összekötők részletes listájáért lásd: a tábla [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).

Az Azure Data Factory ajánlatok egy kibővített felügyelt adatok mozgása megoldás figyelésekor AzCopy, a parancssori adatok átvitele segédprogram. A kibővíthető architecture az ADF, mert azt fogadására képes adatokat a nagy átviteli sebességgel. További információkért lásd: [a másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használja a Data Factory adatok másolása eszközt az adatok betöltése _Amazon Web Services S3 szolgáltatás_ történő _Azure Data Lake tárolási Gen2_. Akkor is hasonló lépésekkel lehet adatok másolása az egyéb típusú adattároló.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Az Azure Storage-fiókkal, és engedélyezve van a Data Lake tárolási Gen2: Ha a tárfiók nincs, kattintson a [Itt](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) kattintva létrehozhat egyet.
* Egy adatokat tartalmazó S3 gyűjtő AWS fiókot: Ez a cikk bemutatja, hogyan adatok másolása az Amazon S3. Egyéb adattárakhoz hasonló módon használhatja.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza **új** > **adatok + analitika** > **adat-előállító**:
   
   ![Új adat-előállító létrehozása](./media/load-azure-data-lake-storage-gen2/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** lapján adja meg a mezőket, amelyeknek az alábbi ábrán látható: 
      
   ![Új adat-előállító lap](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Név**: Adja meg az az Azure data factory globálisan egyedi nevet. Ha a hibaüzenet "adat-előállító \"LoadADLSDemo\" nem áll rendelkezésre," írjon be egy másik nevet az adat-előállítóban. Például használhatja a név  _**saját_név**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállítóban. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki az Azure-előfizetéshez használandó adat-előállító létrehozása. 
    * **Erőforráscsoport**: a legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **hozzon létre új** lehetőséget, majd írja be az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válasszon **V2(Preview)**.
    * **Hely**: Jelölje ki az adat-előállítóban helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattároló adat-előállító által használt más helyek és a régióban lehet. 

3. Kattintson a **Létrehozás** gombra.
4. Létrehozásának befejezése után lépjen a data factory. Megjelenik a **adat-előállító** kezdőlapja a következő ábrán látható módon: 
   
   ![Data factory kezdőlap](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Válassza ki a **Szerző & figyelő** csempe az adatok integrációs alkalmazást egy külön lapján.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake tárolási Gen2

1. Az a **Ismerkedés** lapon jelölje be a **adatok másolása** csempe az adatok másolása eszköz: 

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Az a **tulajdonságok** adja meg azokat **CopyFromAmazonS3ToADLS** a a **feladatnév** mezőjét, majd válassza **következő**:

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Az a **forrás adattár** kattintson **+ új kapcsolat létrehozása**:

    ![Forrásadattár lap](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Válassza ki **Amazon S3** összekötő gyűjteménye, és válassza ki a **Folytatás**
    
    ![Forrás adatokat tároló s3 lap](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Az a **adja meg, Amazon S3 kapcsolat** lapon esetén tegye a következőket:
   1. Adja meg a **hozzáférési kulcs azonosító** érték.
   2. Adja meg a **titkos hívóbetű** érték.
   3. Kattintson a **tesztkapcsolat** ellenőrzése a beállításokat, majd válassza ki **Befejezés**.
   
   ![Adja meg az Amazon S3 fiók](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   
   4. Látni fogja az új kapcsolat jön létre. Kattintson a **Tovább** gombra.
   
5. Az a **válassza ki azt a bemeneti fájl vagy mappa** párbeszédpanelen keresse meg a mappát és a fájlt, amelyet másolja át. Válassza ki a mappát vagy fájlt, jelölje be **válasszon**:

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Adja meg a másolási viselkedését ellenőrzésével a **fájlok másolása a rekurzív módon** és **bináris másolási** beállítások. Válassza ki **következő**:

    ![Adja meg a kimeneti mappa](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. A a **adatok céltár** lapján kattintson **+ új kapcsolat létrehozása**, majd válassza ki **Azure Data Lake tárolási Gen2 (előzetes verzió)**, és válassza ki **Folytatás** :

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Az a **adja meg Azure Data Lake tárolási kapcsolat** lapon esetén tegye a következőket:

   1. Válassza ki a Data Lake tárolási Gen2 képes a fiókot a "Tárfiók neve" a legördülő listából.
   2. Kattintson a **Tovább** gombra.
   
   ![Adja meg az Azure Data Lake Store-fiók](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Az a **válassza ki azt a kimeneti fájl vagy mappa** lapján adja meg **copyfroms3** a kimeneti mappa nevét, és válassza ki **következő**: 

    ![Adja meg a kimeneti mappa](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Az a **beállítások** lapon jelölje be **következő** az alapértelmezett beállításokkal:

    ![Beállítások lap](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Az a **összegzés** lapon tekintse át a beállításokat, és válassza ki **következő**:

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Az a **telepítési lap**, jelölje be **figyelő** figyelheti a folyamatot:

    ![Üzembe helyezés lap](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop ismerteti tevékenységfuttatási részletek megtekintése, és futtassa újra a folyamatot:

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Válassza ki, ha tevékenység fut, amely társul a folyamat, futtassa a **nézet tevékenység fut** hivatkozásra a **műveletek** oszlop. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Váltson vissza az a folyamat futó nézet, válassza ki a **folyamatok** tetején. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Minden egyes másolási tevékenység végrehajtási adatainak figyelésére, válassza ki a **részletek** (jelölőnégyzetet kép) hivatkozásra kattintva **műveletek** figyelési nézet tevékenységben. Részletes adatai figyelhetők adatokat fogadó, adatátvitelt, megfelelő időtartamú támadáséhoz hasonlók a forráskiszolgálóról másolt, és konfigurációkat használják:

    ![A figyelő tevékenységfuttatási részletei](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Győződjön meg arról, hogy az adatok másolásakor a Data Lake tárolási Gen2 fiókjába.

## <a name="best-practice"></a>Ajánlott eljárás

Ha másolja a nagy méretű fájl alapú adattár-adatainak javasoltak a:

- A fájlok partícióazonosító be 10 TB-os 20TB fileset számára.
- Nem indítható el, túl sok egyidejű példány fut a forrás- és fogadó adattárolókhoz szabályozás elkerülése érdekében. Egy példány futtatása és a teljesítményt figyelő elindítása, majd fokozatosan adja hozzá több igény szerint.

## <a name="next-steps"></a>További lépések

* [Tevékenység – áttekintés](copy-activity-overview.md)
* [Az Azure Data Lake tárolási Gen2 összekötő](connector-azure-data-lake-storage.md)