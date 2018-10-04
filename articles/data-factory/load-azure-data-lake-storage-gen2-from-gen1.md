---
title: Adatmásolás az Azure Data Lake Storage Gen1 Gen2-re (előzetes verzió) az Azure Data Factoryvel
description: Adatmásolás az Azure Data Lake Storage Gen1 Gen2-re az Azure Data Factory használatával (előzetes verzió)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: jingwang
ms.openlocfilehash: 953585ffcc5a40d9ae48055f68a1c1fa84db25cc
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249332"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-preview-with-azure-data-factory"></a>Adatmásolás az Azure Data Lake Storage Gen1 Gen2-re (előzetes verzió) az Azure Data Factoryvel

[Az Azure Data Lake Storage Gen2 (előzetes verzió)](../storage/data-lake-storage/introduction.md) ad hozzá egy protokoll a hierarchikus fájlrendszer névtér és biztonsági szolgáltatásokat az Azure Blob Storage analytics keretrendszerek csatlakozni egy tartós tárolási réteg egyszerűvé. A Data Lake Storage Gen2 (előzetes verzió), a objektum tárolási összes minőségű továbbra is egy fájlrendszer felületen előnyei hozzáadása során.

Ha jelenleg az Azure Data Lake Storage Gen1 használ, másolja az adatokat a Data Lake Storage Gen1 Gen2-re az Azure Data Factory használatával kiértékelheti az Gen2 új funkciót.

Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás az. A szolgáltatás segítségével feltölti a lake széles helyszíni adataival és a felhőalapú adatokat tárolja, és ezzel időt takaríthat az elemzési megoldások készítése során. Támogatott összekötők részletes listáját lásd a táblázat az [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

Az Azure Data Factory adatáthelyezési horizontális felskálázás, felügyelt adatok megoldást kínál. Az ADF kibővített architektúrája miatt, betöltheti az adatokat nagy adatátviteli kapacitással. További információkért lásd: [másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használható a Data Factory az adatok másolása eszköz adatokat másolni _Azure Data Lake Storage Gen1_ be _Azure Data Lake Storage Gen2_. Adatok másolása a más típusú adattárakban hasonló lépéseket követheti.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Az Azure Data Lake Storage Gen1 fiók adattal.
* Az Azure Storage-fiókot a Data Lake Storage Gen2 engedélyezve: Ha nem rendelkezik egy tárfiókot, kattintson a [Itt](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) hozhat létre egyet.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **új** > **adatok + analitika** > **adat-előállító**:
   
   ![Új adat-előállító létrehozása](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** lap, adja meg a mezőket az alábbi képen látható: 
      
   ![Új adat-előállító lap](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Név**: Adja meg az Azure data factory egy globálisan egyedi nevet. Ha a hibaüzenetet kapja "adat-előállító nevét \"LoadADLSDemo\" nem érhető el" adja meg a data Factory egy másik nevet. Például használhatja a név  _**sajátneve**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállító. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki az Azure-előfizetés, amelyben az adat-előállító létrehozásához. 
    * **Erőforráscsoport**: a legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válasszon **V2**.
    * **Hely**: válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak más helyeken / régiókban is lehetnek. 

3. Kattintson a **Létrehozás** gombra.
4. Létrehozás befejezése után nyissa meg az adat-előállítóhoz. Megjelenik a **adat-előállító** kezdőlapja a következő képen látható módon: 
   
   ![Data factory kezdőlap](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   Válassza ki a **létrehozás és Monitorozás** csempére kattintva indítsa el az adatintegrációs alkalmazás külön lapon.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2

1. Az a **Ismerkedés** lapon válassza ki a **adatok másolása** csempére, hogy az adatok másolása eszköz elindításához: 

   ![Az Adatok másolása eszköz csempéje](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Az a **tulajdonságok** adja meg azokat **CopyFromADLSGen1ToGen2** számára a **feladat neve** mezőt, és válassza **tovább**:

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Az a **forrásadattár** kattintson **+ új kapcsolat létrehozása**:

    ![Forrásadattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    Válassza ki **Azure Data Lake Storage Gen1** az összekötő-katalógusából, és válassza ki a **Folytatás**
    
    ![Forrásadatok tárolhatja az Azure Data Lake Storage Gen1 lap](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. Az a **adja meg az Azure Data Lake Storage Gen1 kapcsolat** lapon, tegye a következőket:
   1. Válassza ki a Data Lake Storage Gen1 a fiók neve.
   2. Adja meg, vagy ellenőrizheti a **bérlői**, és válassza ki a Befejezés gombra.
   3. Kattintson a **Tovább** gombra.
   
   > [!IMPORTANT]
   > Ebben az útmutatóban egy felügyelt identitás, az Azure-erőforrásokhoz használhatja a Data Lake Storage Gen1e hitelesítéséhez. Ügyeljen arra, hogy adja meg az MSI az Azure Data Lake Storage Gen1 a megfelelő engedélyeket az alábbi [ezek az utasítások](connector-azure-data-lake-store.md#managed-identity).
   
   ![Az Azure Data Lake Storage Gen1 fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
   
   4. Megjelenik egy új kapcsolat jön létre. Kattintson a **Tovább** gombra.
   
5. Az a **a bemeneti fájl vagy mappa kiválasztása** párbeszédpanelen tallózással keresse meg a mappát és fájlt, amelyet másolja át azokat. Válassza ki a mappát vagy fájlt, jelölje be **válasszon**:

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Adja meg a másolási viselkedés ellenőrzésével a **fájlok másolása a rekurzív módon** és **bináris másolat** beállítások. Válassza ki **tovább**:

    ![Adja meg a kimeneti mappa](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. Az a **célként megadott adattárba** lap, kattintson **+ új kapcsolat létrehozása**, majd válassza ki **Azure Data Lake Storage Gen2 (előzetes verzió)**, és válassza ki **Folytatás** :

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. Az a **adja meg az Azure Data Lake Storage Gen2 kapcsolat** lapon, tegye a következőket:

   1. Válassza ki a Data Lake Storage Gen2 a "Tárfiók neve" képes a fiókot a legördülő listából.
   2. Kattintson a **Tovább** gombra.
   
   ![Az Azure Data Lake Storage Gen2-fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. Az a **a kimeneti fájl vagy mappa kiválasztása** lap, adja meg **copyfromadlsgen1** , a kimeneti mappa nevét, és válassza **tovább**: 

    ![Adja meg a kimeneti mappa](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. Az a **beállítások** lapon jelölje be **tovább** , az alapértelmezett beállításokat használja.

11. Az a **összefoglalás** lapon tekintse át a beállításokat, és válassza ki **tovább**:

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. Az a **üzembe helyezés lap**válassza **figyelő** használatával figyelheti a folyamatot:

    ![Üzembe helyezés lap](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop tartalmazza a tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások:

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. A folyamat futásához társított tevékenységfuttatások megtekintéséhez jelölje ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Válassza ki, váltson vissza a folyamatfuttatások nézetre a **folyamatok** a fenti hivatkozásra. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. Figyelheti a minden egyes másolási tevékenység-végrehajtási részleteit, válassza ki a **részletei** (szemüveg képet) kapcsolat alatt **műveletek** a figyelési nézet tevékenység. Adatait is figyelheti, például a fogadó, a fájlmegosztásra, a megfelelő időtartamot a végrehajtási lépések a forráskiszolgálóról másolt adatok mennyiségét, és a használt konfigurációk:

    ![A figyelő tevékenységfuttatás részletei](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. Győződjön meg arról, hogy az adatok Data Lake Storage Gen2-fiókjába másolja.

## <a name="best-practices"></a>Ajánlott eljárások

Ha másolja a nagy volumenű adatok fájlalapú adattárból, akkor javasolt a:

- A fájlok particionálása be 10 TB-os, 30TB fileset.
- Nem indítja el a forrás és fogadó adattárakból származó szabályozás elkerülése érdekében túl sok egyidejű másolási futtatások. Egy példány futtatása és figyelése az átviteli sebességet, majd fokozatosan adjon hozzá további igény szerint.

## <a name="next-steps"></a>További lépések

* [Másolási tevékenység áttekintése](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md)