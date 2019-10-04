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
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068974"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Adatok másolása az Azure Data Lake Storage Gen1 Gen2-re az Azure Data Factoryvel

Az Azure Data Lake Storage Gen2 dedikált beépített big data-elemzési képességeket [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Használhatja kommunikáljon az adatok a mindkét fájl rendszer és a objektum tárolási paradigmákat használatával.

Ha jelenleg használja az Azure Data Lake Storage Gen1, kiértékelheti az Azure Data Lake Storage Gen2 által adatmásolás a Data Lake Storage Gen1 Gen2-re az Azure Data Factory használatával.

Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás az. A szolgáltatás segítségével feltölti a lake széles helyszíni adataival és felhőalapú adatokat tárolja, és időt, amikor az elemzési megoldásokat hozhat létre. Támogatott összekötők listája, tekintse meg a fenti táblázat [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

Az Azure Data Factory adatáthelyezési horizontális felskálázás, felügyelt adatok megoldást kínál. Az adat-előállító kibővített architektúrát, mert azt betöltheti az adatokat nagy adatátviteli kapacitással. További információkért lásd: [másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használható a Data Factory másolás eszköz adatokat másol az Azure Data Lake Storage Gen1 az Azure Data Lake Storage Gen2. Adatok másolása a más típusú adattárakban hasonló lépéseket követheti.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Az Azure Data Lake Storage Gen1 fiók adattal.
* Az Azure Storage-fiók a Data Lake Storage Gen2 engedélyezve Ha egy Storage-fiók nem rendelkezik [hozzon létre egy fiókot](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **erőforrás létrehozása** > **adatok + analitika** > **adat-előállító**.
   
   ![Data Factory kiválasztása az új panelen](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az a **új adat-előállító** lap, adja meg a mezőket az alábbi képen látható: 
      
   ![Új adat-előállító lap](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Név**: Adja meg az Azure data factory egy globálisan egyedi nevet. Ha a hibaüzenetet kapja "adat-előállító nevét \"LoadADLSDemo\" nem érhető el" adja meg a data Factory egy másik nevet. Például: _**sajátneve**_ **ADFTutorialDataFactory**. Hozza létre újra az adat-előállítóban. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetés, amelyben az adat-előállító létrehozásához. 
    * **Erőforráscsoport**: A legördülő listából válasszon ki egy meglévő erőforráscsoportot. Is kiválaszthat a **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket. 
    * **Verzió**: Válassza ki **V2**.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A data factory által használt adattárakban más helyeken / régiókban is lehetnek. 

3. Kattintson a **Létrehozás** gombra.
4. Létrehozás befejezése után nyissa meg az adat-előállítóhoz. Megjelenik a **adat-előállító** kezdőlapja a következő képen látható módon: 
   
   ![Data factory kezdőlap](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Válassza ki a **létrehozás és Monitorozás** csempére kattintva indítsa el az adatintegrációs alkalmazás külön lapon.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2

1. Az a **Ismerkedés** lapon válassza ki a **adatok másolása** csempére, hogy az adatok másolása eszköz elindításához. 

   ![Adatok másolása eszköz csempéje](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Az a **tulajdonságok** adja meg azokat **CopyFromADLSGen1ToGen2** számára a **feladat neve** mező. Kattintson a **Tovább** gombra.

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Az a **forrásadattár** lapon jelölje be **+ új kapcsolat létrehozása**.

    ![Forrásadattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Válassza ki **Azure Data Lake Storage Gen1** az összekötő-katalógusából, és válassza ki a **Folytatás**.
    
    ![Forrásadatok tárolhatja az Azure Data Lake Storage Gen1 lap](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Az a **adja meg az Azure Data Lake Storage Gen1 kapcsolat** lapon, kövesse az alábbi lépéseket:

   a. Válassza ki a Data Lake Storage Gen1 a fiók neve, és adja meg, vagy ellenőrizheti a **bérlői**.
  
   b. Válassza ki **kapcsolat tesztelése** beállítások érvényesítése. Ezután kattintson a **Befejezés** gombra.
  
   c. Láthatja, hogy létrejött-e egy új kapcsolatot. Kattintson a **Tovább** gombra.
   
   > [!IMPORTANT]
   > Ebben az útmutatóban egy felügyelt identitás, az Azure-erőforrásokhoz használhatja az Azure Data Lake Storage Gen1 hitelesítéséhez. Adja meg a felügyelt identitás a megfelelő engedélyeket az Azure Data Lake Storage Gen1, hajtsa végre a [ezek az utasítások](connector-azure-data-lake-store.md#managed-identity).
   
   ![Az Azure Data Lake Storage Gen1 fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Az a **a bemeneti fájl vagy mappa kiválasztása** párbeszédpanelen tallózással keresse meg a mappát és fájlt, amelyet másolja át azokat. Válassza ki a mappát vagy fájlt, és válassza ki **válasszon**.

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Adja meg a másolási viselkedés kiválasztásával a **fájlok másolása a rekurzív módon** és **bináris másolat** beállítások. Kattintson a **Tovább** gombra.

    ![Adja meg a kimeneti mappa](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Az a **célként megadott adattárba** lapon jelölje be **+ új kapcsolat létrehozása** > **Azure Data Lake Storage Gen2**  >   **Továbbra is**.

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Az a **adja meg az Azure Data Lake Storage Gen2 kapcsolat** lapon, kövesse az alábbi lépéseket:

   a. Válassza ki a Data Lake Storage Gen2 képes a fiókját a **tárfióknevet** legördülő listából.
   
   b. Válassza ki **Befejezés** a kapcsolat létrehozásához. Ezután kattintson a **Tovább** gombra.
   
   ![Az Azure Data Lake Storage Gen2-fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Az a **a kimeneti fájl vagy mappa kiválasztása** lap, adja meg **copyfromadlsgen1** , a kimeneti mappa nevét, és válassza **tovább**. A Data Factory hoz létre a megfelelő Azure Data Lake Storage Gen2-fájlrendszer és almappák másolása során, ha azok nem léteznek.

    ![Adja meg a kimeneti mappa](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Az a **beállítások** lapon jelölje be **tovább** , az alapértelmezett beállításokat használja.

12. Az a **összefoglalás** lapon tekintse át a beállításokat, és válassza ki **tovább**.

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Az a **üzembe helyezés lap**válassza **figyelő** folyamat monitorozásához.

    ![Üzembe helyezés lap](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók.

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. A folyamat futásához társított tevékenységfuttatások megtekintéséhez jelölje ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Válassza ki, váltson vissza a folyamatfuttatások nézetre a **folyamatok** a fenti hivatkozásra. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Figyelheti a minden egyes másolási tevékenység-végrehajtási részleteit, válassza ki a **részletei** (szemüveg képet) kapcsolat alatt **műveletek** a figyelési nézet tevékenység. Például a fogadó, a fájlmegosztásra, a megfelelő időtartamot a végrehajtási lépések a forráskiszolgálóról másolt adatok mennyiségét, és a használt konfigurációk adatait is figyelheti.

    ![A figyelő tevékenységfuttatás részletei](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Győződjön meg arról, hogy az adatokat másolja ki az Azure Data Lake Storage Gen2-fiókba.

## <a name="best-practices"></a>Ajánlott eljárások

Annak ellenőrzéséhez, hogy a frissítés az Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2-re általában, lásd: [a big data-elemzési megoldások az Azure Data Lake Storage Gen1 frissítése az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-upgrade.md). Az alábbi szakaszok az ajánlott eljárások adat-előállító a Data Lake Storage Gen2-re a Data Lake Storage Gen1 adatok frissítésnél vezetnek be.

### <a name="data-partition-for-historical-data-copy"></a>Adatok partícióra másolásához a korábbi adatok alapján

- Ha az összesített adatok mérete, a Data Lake Storage Gen1 kevesebb mint 30 TB, és a fájlok száma kisebb, mint 1 millió, másolja az összes adat egyetlen másolási tevékenység futtatása.
- Ha egy nagyobb mennyiségű adatot másolni, vagy azt szeretné, hogy rugalmasan kötegekben adatok áttelepítésének kezelése, és győződjön meg azok a teljes vagy adott időszakon belül, az adatok particionálásához. A particionálás is Védettebb lesz minden olyan váratlan probléma.

A koncepció igazolása segítségével ellenőrizze a teljes körű megoldást, és a példány átviteli sebesség tesztelése a környezetben. Proof-of-concept fő lépést: 

1. Hozzon létre egy Data Factory-folyamatot egy másolási tevékenységgel több TB-osra bővül adatokat másol a Data Lake Storage Gen1 a Data Lake Storage Gen2 másolási teljesítmény alapvető. Kezdje [(DIUs) integrációs adategységek](copy-activity-performance.md#data-integration-units) mint 128. 
2. Az 1. lépésben kap másolási teljesítmény alapján, kiszámítása a becsült ideje a teljes adat az áttelepítéshez szükséges. 
3. (Nem kötelező) Vezérlő tábla létrehozása, és adja meg a fájl szűrő az áttelepítendő fájlok particionálásához. A partíció a fájlok módja: 

    - A partíció mappanév vagy egy helyettesítő karaktert tartalmazó szűrő a mappa neve. Azt javasoljuk, hogy ez a módszer.
    - Partíció által egy fájlt az utolsó módosítás időpontja.

### <a name="network-bandwidth-and-storage-io"></a>Hálózati sávszélességet és a tárolási I/O 

A Data Factory másolási feladatokat, amely adatokat olvas a Data Lake Storage Gen1 és adatokat írni a Data Lake Storage Gen2 egyidejűségi szabályozhatja. Ezzel a módszerrel a használatát az, hogy a tárolási i/o elkerülése érdekében az áttelepítés során a normál üzleti munkát a Data Lake Storage Gen1 érintő kezelheti.

### <a name="permissions"></a>Engedélyek 

A Data Factory a [Data Lake Storage Gen1 összekötő](connector-azure-data-lake-store.md) támogatja a szolgáltatás az Azure-erőforrás hitelesítések egyszerű és felügyelt identitás. A [Data Lake Storage Gen2 összekötő](connector-azure-data-lake-storage.md) támogatja a fiók a kulcs egyszerű szolgáltatás és az Azure-erőforrás hitelesítések felügyelt identitás. Szeretné, hogy a Data Factory keresse meg és másolja az összes fájlt, vagy hozzáférés-vezérlési listák (ACL), magas elérését, olvassa el, vagy minden fájl írása és ACL-ek beállítása, ha úgy dönt, hogy a megadott fióknak megfelelő engedélyeket kell, biztosítson. Az áttelepítés során egy felügyelő vagy a tulajdonos szerepkör biztosítania. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Hozzáférés-vezérlési listák a Data Lake Storage Gen1 megőrzése

Ha meg szeretné replikálni a hozzáférés-vezérlési listák data fájlokkal együtt, amikor Data Lake Storage Gen2-re frissíti a Data Lake Storage Gen1, [megőrzése hozzáférés-vezérlési listák a Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>A növekményes másolási 

Több megközelítés közül csak az új vagy frissített fájlokat betölteni a Data Lake Storage Gen1 használhatja:

- Töltse be új vagy frissített fájlok idő particionált fájl vagy mappa neve. Ilyen például, és a 2019/05/13 / *.
- Új vagy frissített fájlok LastModifiedDate betölteni.
- Bármilyen külső eszköz vagy megoldás új vagy frissített fájlok azonosításához. A paraméter vagy táblázat vagy a fájl akkor továbbítja a fájl vagy mappa nevét a Data Factory-folyamatot. 

Növekményes betöltés ehhez a megfelelő gyakoriságát az Azure Data Lake Storage Gen1 lévő fájlok teljes száma, és be kell tölteni minden alkalommal új vagy frissített fájlok mennyisége függ. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Másolási tevékenység áttekintése](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 összekötő](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md)