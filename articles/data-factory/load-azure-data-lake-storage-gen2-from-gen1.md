---
title: Adatok másolása az Azure Data Lake Storage Gen1 Gen2-re az Azure Data Factoryvel
description: Adatmásolás az Azure Data Lake Storage Gen1 Gen2-re az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d6e09ec1f070f9ee0f4162524e4bd80d1f81adc3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560643"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Adatok másolása az Azure Data Lake Storage Gen1 Gen2-re az Azure Data Factoryvel

Az Azure Data Lake Storage Gen2 egy olyan dedikált big data-analitika, beépített képességei [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Lehetővé teszi az adatok használata mindkét fájl rendszer és a objektum tárolási paradigmákat csatoló.

Ha jelenleg az Azure Data Lake Storage Gen1 használ, másolja az adatokat a Data Lake Storage Gen1 Gen2-re az Azure Data Factory használatával kiértékelheti az Gen2 új funkciót.

Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás az. A szolgáltatás segítségével feltölti a lake széles helyszíni adataival és a felhőalapú adatokat tárolja, és ezzel időt takaríthat az elemzési megoldások készítése során. Támogatott összekötők részletes listáját lásd a táblázat az [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

Az Azure Data Factory adatáthelyezési horizontális felskálázás, felügyelt adatok megoldást kínál. Az ADF kibővített architektúrája miatt, betöltheti az adatokat nagy adatátviteli kapacitással. További információkért lásd: [másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használható a Data Factory az adatok másolása eszköz adatokat másolni _Azure Data Lake Storage Gen1_ be _Azure Data Lake Storage Gen2_. Adatok másolása a más típusú adattárakban hasonló lépéseket követheti.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Az Azure Data Lake Storage Gen1 fiók adattal.
* Azure Storage-fiókjába Data Lake Storage Gen2 engedélyezve: Ha egy Storage-fiók nem rendelkezik [hozzon létre egy fiókot](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **erőforrás létrehozása** > **adatok + analitika** > **adat-előállító**:
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az a **új adat-előállító** lap, adja meg a mezőket az alábbi képen látható: 
      
   ![Új adat-előállító lap](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Név**: Adja meg az Azure data factory egy globálisan egyedi nevet. Ha a hibaüzenetet kapja "adat-előállító nevét \"LoadADLSDemo\" nem érhető el" adja meg a data Factory egy másik nevet. Például használhatja a név  _**sajátneve**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállító. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetés, amelyben az adat-előállító létrehozásához. 
    * **Erőforráscsoport**: A legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza ki **V2**.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak más helyeken / régiókban is lehetnek. 

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
   1. Válassza ki a Data Lake Storage Gen1 a fiók neve, és adja meg, vagy ellenőrizheti a **bérlői**.
   2. Kattintson a **kapcsolat tesztelése** ellenőrzése a beállításokat, majd válassza ki **Befejezés**.
   3. Megjelenik egy új kapcsolat jön létre. Kattintson a **Tovább** gombra.
   
   > [!IMPORTANT]
   > Ebben az útmutatóban egy felügyelt identitás, az Azure-erőforrásokhoz használhatja a Data Lake Storage Gen1 hitelesítéséhez. Ügyeljen arra, hogy adja meg az MSI az Azure Data Lake Storage Gen1 a megfelelő engedélyeket az alábbi [ezek az utasítások](connector-azure-data-lake-store.md#managed-identity).
   
   ![Az Azure Data Lake Storage Gen1 fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. Az a **a bemeneti fájl vagy mappa kiválasztása** párbeszédpanelen tallózással keresse meg a mappát és fájlt, amelyet másolja át azokat. Válassza ki a mappát vagy fájlt, jelölje be **válasszon**:

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Adja meg a másolási viselkedés ellenőrzésével a **fájlok másolása a rekurzív módon** és **bináris másolat** beállítások. Válassza ki **tovább**:

    ![Adja meg a kimeneti mappa](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. Az a **célként megadott adattárba** lap, kattintson **+ új kapcsolat létrehozása**, majd válassza ki **Azure Data Lake Storage Gen2**, és válassza ki **Folytatás**:

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. Az a **adja meg az Azure Data Lake Storage Gen2 kapcsolat** lapon, tegye a következőket:

   1. Válassza ki a Data Lake Storage Gen2 a "Tárfiók neve" képes a fiókot a legördülő listából.
   2. Válassza ki **Befejezés** a kapcsolat létrehozásához. Ezután kattintson a **Tovább** gombra.
   
   ![Az Azure Data Lake Storage Gen2-fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. Az a **a kimeneti fájl vagy mappa kiválasztása** lap, adja meg **copyfromadlsgen1** , a kimeneti mappa nevét, és válassza **tovább**. Az ADF hoz létre a megfelelő ADLS Gen2-fájlrendszer és almappák másolása során, ha még nem létezik.

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

Annak ellenőrzéséhez, hogy a frissítés az Azure Data Lake Storage (ADLS) Gen1 Gen2-re általában, tekintse meg [a big data-elemzési megoldások az Azure Data Lake Storage Gen1 frissítése az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-upgrade.md). Az alábbi szakaszok az ADF használatával adatok verzióról Gen1 Gen2-re vonatkozó ajánlott eljárások vezetnek be.

### <a name="data-partition-for-historical-data-copy"></a>Adatok partícióra másolásához a korábbi adatok alapján

- Ha az ADLS Gen1 a tárolt adatok mérete kisebb, mint **30TB** és a fájlok száma kisebb, mint **1 millió**, minden adatokat másolhat egy másolási tevékenység futtatási.
- Ha az adatok másolása nagyobb méretű, vagy azt szeretné, a rugalmasságot, és kötegekben adatok áttelepítésének kezelése, és azok egy adott időzítési időkereten belül fejeződött be, akkor javasolt az adatok particionálásához, ebben az esetben azt is is kockázatának csökkentése érdekében minden nem várt iss UE.

A PoC (a koncepció igazolása) erősen ajánlott annak érdekében, hogy ellenőrizze a teljes körű megoldást, és a példány átviteli sebesség tesztelése a környezetben. Megvalósíthatósági vizsgálat állapotát fő lépést: 

1. Hozzon létre egy ADF folyamatot egy másolási tevékenységgel az ADLS Gen1 több TB-osra bővül az adatok másolása másolási teljesítmény alapvető, kezdve az ADLS Gen2 [(DIUs) integrációs adategységek](copy-activity-performance.md#data-integration-units) mint 128. 
2. #1. lépésben kap másolási teljesítmény alapján, kiszámíthatja az összes adatát áttelepítés szükséges becsült idő. 
3. (Nem kötelező) Vezérlő tábla létrehozása, és adja meg a fájl szűrő az áttelepítendő fájlok particionálásához. A módszer particionálja a fájlokat, a következőket: 

    - A (javasolt) helyettesítő karaktert tartalmazó szűrő dokumentumtárolási mappa- vagy mappanév 
    - Utolsó módosítás időpontja a fájl segítségével 

### <a name="network-bandwidth-and-storage-io"></a>Hálózati sávszélességet és a tárolási I/O 

Az egyidejűség az ADF másolási feladatokat, amely adatokat olvas az ADLS Gen1 és adatokat írni az ADLS Gen2, szabályozhatja, hogy a tárolási i/o-használatának kezelheti annak érdekében, hogy nem érinti a normál üzleti munkát, az ADLS Gen1 az áttelepítés során.

### <a name="permissions"></a>Engedélyek 

A Data Factoryban [ADLS Gen1 összekötő](connector-azure-data-lake-store.md) egyszerű szolgáltatás és a felügyelt identitást támogatja az Azure-erőforrás-hitelesítés; [ADLS Gen2 összekötő](connector-azure-data-lake-storage.md) támogatja a fiókkulcs, egyszerű szolgáltatás és a felügyelt identitást a Azure-erőforrás hitelesítéséről. Navigálhat a Data Factory és az összes fájl/ACL-EK és győződjön meg arról, magas a fiók megfelelő engedélyeket biztosítson másolási való hozzáférés/olvasási/írási megadja az összes fájlt, és ACL-ek beállítása, ha úgy dönt, hogy. Javasoljuk, biztosítania super-felhasználók vagy tulajdonosi szerepkört, az áttelepítés során. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Hozzáférés-vezérlési listák a Data Lake Storage Gen1 megőrzése

Ha meg szeretné replikálni a hozzáférés-vezérlési listák data fájlokkal együtt, a Data Lake Storage Gen1 Gen2-re történő frissítés során, olvassa el [megőrzése hozzáférés-vezérlési listák a Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>A növekményes másolási 

Több megközelítés közül csak az új vagy frissített fájlok betöltése az ADLS Gen1 használható:

- Új vagy frissített fájlok betöltése idő particionált fájl vagy mappa neve, pl./2019/05/13 / *;
- Új vagy frissített fájlok LastModifiedDate; betöltése
- Új vagy frissített fájlok azonosítása-3. fél eszköz/megoldással, majd a fájl vagy mappa neve át ADF folyamat paraméter vagy egy tábla és fájl használatával.  

Növekményes betöltés ehhez a megfelelő gyakoriságát ADLS Gen1 lévő fájlok teljes száma és a kötetet, nem tölthető be minden alkalommal új vagy frissített fájl függ.  

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Másolási tevékenység áttekintése](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 összekötő](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md)