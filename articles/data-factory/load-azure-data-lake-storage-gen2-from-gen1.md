---
title: Adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásból a Gen2 szolgáltatásba
description: Adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásból a Gen2 szolgáltatásba az Azure Data Data Factory használatával
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
ms.openlocfilehash: 81f8577592f1d53627bc09a2f9ace8c060ad4660
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668851"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Adatok másolása az Azure Data Lake Storage Gen1-ről a Gen2 szolgáltatásba az Azure Data Factory segítségével

Az Azure Data Lake Storage Gen2 az [Azure Blob storage-ba](../storage/blobs/storage-blobs-introduction.md)beépített big data-elemzési képességek összeskészlete. Segítségével kapcsolatba léphet az adatokkal a fájlrendszer és az objektumtárolási paradigmák használatával.

Ha jelenleg az Azure Data Lake Storage Gen1-et használja, kiértékelheti az Azure Data Lake Storage Gen2-t, ha adatokat másol a Data Lake Storage Gen1-ről a Gen2-be az Azure Data Factory használatával.

Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatás segítségével feltöltheti a tó a helyszíni és felhőalapú adattárak gazdag készletéből származó adatokkal, és időt takaríthat meg az elemzési megoldások létrehozásakor. A támogatott összekötők listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)táblázatában láthatja.

Az Azure Data Factory kibővített, felügyelt adatmozgatási megoldást kínál. A Data Factory kibővített architektúrája miatt nagy átviteli sebességgel képes adatokat betöltésre. További információt a [Tevékenység teljesítményének másolása](copy-activity-performance.md)című témakörben talál.

Ez a cikk bemutatja, hogyan használhatja a Data Factory adatmásolási eszközt az Azure Data Lake Storage Gen1 adatainak az Azure Data Lake Storage Gen2-be történő másolásához. Hasonló lépéseket követve más típusú adattárakból is másolhat adatokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Azure Data Lake Storage Gen1 fiók adatokkal benne.
* Azure Storage-fiók a Data Lake Storage Gen2 engedélyezve van. Ha nem rendelkezik Tárfiókkal, [hozzon létre egy fiókot.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **Erőforrás létrehozása** > **adatok + Analytics** > **Data Factory parancsot.**
   
   ![Adatgyár kiválasztása az Új ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **Új adatgyár** lapon adja meg az alábbi képen látható mezők értékeit: 
      
   ![Új adatgyári lap](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Név**: Adjon meg egy globálisan egyedi nevet az Azure-adat-előállító. Ha a következő hibaüzenet jelenik meg: "Data factory name LoadADLSDemo is not available" (A Data factory name LoadADLSDemo(Data factory name LoadADLSDemo) (Data factory name LoadADLSDemo) (Data factory name LoadADLSDemo) (Data factory name LoadADLSDemo) \"\" (Data factory name LoadADLS Például: _**sajátneve**_**ADFTutorialDataFactory**. Hozza létre újra az adatgyárat. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: Válassza ki az Azure-előfizetést, amelyben létre szeretné hozni az adat-előállító. 
    * **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot a legördülő listából. Az **Új létrehozása** lehetőséget is bejelölheti, és megadhatja egy erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
    * **Verzió**: Válassza a **V2**lehetőséget.
    * **Hely**: Válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak más helyeken és régiókban is lehetnek. 

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatgyárat. A **Data Factory** kezdőlapja az alábbi képen látható módon jelenik meg: 
   
   ![Data factory kezdőlap](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. A **Szerzői & figyelése** csempével külön lapon indíthatja el az Adatintegrációs alkalmazást.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2-be

1. Az **Első lépések** lapon válassza az **Adatok másolása** csempét az adatmásolási eszköz elindításához. 

   ![Adateszköz csempéjéhez másolva](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. A **Tulajdonságok** lapon adja meg a **CopyFromADLSGen1ToGen2** értéket a **Tevékenység neve** mezőhöz. Válassza a **Tovább lehetőséget.**

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. A **Forrásadattár** lapon válassza a **+ Új kapcsolat létrehozása**lehetőséget.

    ![Forrásadattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Válassza az **Azure Data Lake Storage Gen1** elemet az összekötők katalógusában, majd a **Folytatás** gombot.
    
    ![Forrásadattár Az Azure Data Lake Storage Gen1 lapja](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Az **Azure Data Lake Storage Gen1 szolgáltatás ának megadása lapon** hajtsa végre az alábbi lépéseket:

   a. Válassza ki a Data Lake Storage Gen1-et a fiók nevéhez, és adja meg vagy érvényesítse a **bérlőt.**
  
   b. A beállítások érvényesítéséhez válassza a **Kapcsolat tesztelése** lehetőséget. Ezután kattintson a **Befejezés** gombra.
  
   c. Láthatja, hogy új kapcsolat jött létre. Válassza a **Tovább lehetőséget.**
   
   > [!IMPORTANT]
   > Ebben az átjárási, az Azure-erőforrások felügyelt identitás használatával hitelesíti az Azure Data Lake Storage Gen1. A felügyelt identitás megfelelő engedélyeket az Azure Data Lake Storage Gen1, kövesse [az alábbi utasításokat.](connector-azure-data-lake-store.md#managed-identity)
   
   ![Azure Data Lake Storage Gen1 fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. A **A Bemeneti fájl vagy mappa kiválasztása** lapon keresse meg azt a mappát és fájlt, amelyet át szeretne másolni. Jelölje ki a mappát vagy fájlt, és válassza **a Választás gombot.**

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Adja meg a másolási viselkedést a **rekurzív fájlok másolása** és a **bináris másolás** idomulása beállítás kiválasztásával. Válassza a **Tovább lehetőséget.**

    ![Kimeneti mappa megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. A **Cél adattár** lapon válassza **a + Új kapcsolat** > létrehozása**az Azure Data Lake Storage Gen2** > **Continue lehetőséget.**

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Az **Azure Data Lake Storage Gen2 kapcsolatának megadása** lapon hajtsa végre az alábbi lépéseket:

   a. Válassza ki a Data Lake Storage Gen2 képes fiókot a **Storage-fiók név** legördülő listájából.
   
   b. Kattintson a **Befejezés** gombra a kapcsolat létrehozásához. Ezután válassza a **Tovább**gombot.
   
   ![Azure Data Lake Storage Gen2 fiók megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. A **Kimeneti fájl vagy mappa kiválasztása** lapon adja meg a **copyfromadlsgen1 parancsot** kimeneti mappanévként, és válassza a **Tovább**gombot. A Data Factory létrehozza a megfelelő Azure Data Lake Storage Gen2 fájlrendszert és almappákat a másolás során, ha azok nem léteznek.

    ![Kimeneti mappa megadása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. A **Beállítások** lapon kattintson a **Tovább** gombra az alapértelmezett beállítások használatához.

12. Az **Összegzés** lapon tekintse át a beállításokat, és válassza a **Tovább gombot.**

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. A **Központi telepítés lapon**válassza a **Figyelő** lehetőséget a folyamat figyeléséhez.

    ![Üzembe helyezés lap](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók.

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez válassza a Műveletek **oszlop** **Tevékenységfuttatások megtekintése** hivatkozását. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamatfuttatási nézetre, válassza a **folyamatkapcsolatok** közötti kapcsolatot a tetején. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a **Részletek** hivatkozást (szemüvegkép) a **Műveletek csoportban** a tevékenységfigyelési nézetben. Figyelheti a részleteket, például a forrásból a fogadóba másolt adatok mennyiségét, az adatátviteli áteresztőképest, a megfelelő időtartamú végrehajtási lépéseket és a használt konfigurációkat.

    ![Tevékenységfuttatás részleteinek figyelése](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Ellenőrizze, hogy az adatok másolása az Azure Data Lake Storage Gen2 fiókba.

## <a name="best-practices"></a>Ajánlott eljárások

Az Azure Data Lake Storage Gen1-ről az Azure Data Lake Storage Gen2-re való frissítés értékeléséről általában az [Azure Data Lake Storage Gen2 szolgáltatásról a Big Data Lake Storage Gen1 szolgáltatásról az Azure Data Lake Storage Gen2 szolgáltatásra való frissítése.](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) A következő szakaszok gyakorlati tanácsok a Data Factory használatával a Data Lake Storage Gen1 a Data Lake Storage Gen2 adatfrissítéshez.

### <a name="data-partition-for-historical-data-copy"></a>Adatpartíció az előzményadatok másolásához

- Ha a Data Lake Storage Gen1 teljes adatmérete kevesebb, mint 30 TB, és a fájlok száma kevesebb, mint 1 millió, az összes adatot egyetlen másolási tevékenység futtatása után másolhatja.
- Ha nagyobb mennyiségű adatot szeretne másolni, vagy rugalmasan szeretné kezelni az adatáttelepítést kötegekben, és egy adott időkereten belül el végezheti őket, particionálja az adatokat. Particionálás is csökkenti a váratlan probléma kockázatát.

A koncepció igazolásával ellenőrizheti a végpontok között megoldást, és tesztelheti a másolási átviteli szintet a környezetben. A koncepció legfontosabb lépései: 

1. Hozzon létre egy Data Factory-folyamatot egyetlen másolási tevékenységgel, és több TB-nyi adatot másoljon a Data Lake Storage Gen1-ről a Data Lake Storage Gen2-re, hogy másolatot kapjon a teljesítmény alapkonfigurációjából. Kezdje [az adatintegrációs egységekkel (DIUs)](copy-activity-performance-features.md#data-integration-units) 128-ként. 
2. Az 1. 
3. (Nem kötelező) Hozzon létre egy vezérlőtáblát, és határozza meg az áttelepítendő fájlok particionálásához szükséges fájlszűrőt. A fájlok particionálásának módja a következő: 

    - Particionálás mappanév vagy mappanév szerint helyettesítő szűrővel. Ezt a módszert javasoljuk.
    - Particionálás a fájl utolsó módosítási idejével.

### <a name="network-bandwidth-and-storage-io"></a>Hálózati sávszélesség és tárolási I/O 

Szabályozhatja a Data Factory másolási feladatok egyidejűségét, amelyek adatokat olvasnak a Data Lake Storage Gen1-ből, és adatokat írhatnak a Data Lake Storage Gen2-nek. Ily módon kezelheti a tárolási I/O használatát, hogy elkerülje a Data Lake Storage Gen1 normál üzleti munkájának befolyásolását az áttelepítés során.

### <a name="permissions"></a>Engedélyek 

A Data Factory, a [Data Lake Storage Gen1 összekötő](connector-azure-data-lake-store.md) támogatja az Azure-erőforrás-hitelesítések egyszerű és felügyelt identitás. A [Data Lake Storage Gen2 összekötő](connector-azure-data-lake-storage.md) támogatja a fiókkulcsot, az egyszerű szolgáltatást és a felügyelt identitást az Azure-erőforrás-hitelesítések. Ahhoz, hogy a Data Factory képes legyen navigálni és másolni az összes szükséges fájlt vagy hozzáférés-vezérlési listát (ACL), adjon elég magas engedélyeket az Ön által megadott fiókhoz az összes fájl eléréséhez, olvasásához vagy írásához, és ha úgy dönt. Adjon meg egy rendszergazdai vagy tulajdonosi szerepkört az áttelepítési időszakban. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 acl-jainak megőrzése

Ha a Data Lake Storage Gen1-ről a Data Lake Storage Gen2-re való frissítéskor az ACL-eket adatfájlokkal együtt szeretné replikálni, olvassa el az [ACL-ek megőrzése a Data Lake Storage Gen1 szolgáltatásból című témakört.](connector-azure-data-lake-storage.md#preserve-acls) 

### <a name="incremental-copy"></a>Növekményes másolat 

Több féle módszerrel csak a Data Lake Storage Gen1 új vagy frissített fájljait töltheti be:

- Új vagy frissített fájlok betöltése a particionált mappa vagy fájlnév szerint. Egy példa a /2019/05/13/*.
- Új vagy frissített fájlok betöltése a LastModifiedDate segítségével.
- Azonosítsa az új vagy frissített fájlokat bármely külső gyártótól származó eszköz vagy megoldás segítségével. Ezután adja át a fájl vagy mappa nevét a Data Factory folyamatparaméteren vagy egy táblán vagy fájlon keresztül. 

A növekményes terhelés megfelelő gyakorisága az Azure Data Lake Storage Gen1-ben található fájlok teljes számától és a minden alkalommal betöltendő új vagy frissített fájlok mennyiségétől függ. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tevékenység másolása – áttekintés](copy-activity-overview.md)
> [Az Azure Data Lake Storage Gen1 összekötő](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 összekötő](connector-azure-data-lake-storage.md)