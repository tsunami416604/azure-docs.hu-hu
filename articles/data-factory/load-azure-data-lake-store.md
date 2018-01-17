---
title: "Adatok betöltése az Azure Data Lake Store Azure Data Factory használatával |} Microsoft Docs"
description: "Adatok másolása az Azure Data Lake Store az Azure Data Factory használatával"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: a684f8bcdefc8e3338fae59e56863dfc37c23f1f
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Adatok betöltése az Azure Data Lake Store Azure Data Factory használatával

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) van egy vállalati szintű kapacitású adattár a big data koncepción alapuló adatelemzési célokra. Az Azure Data Lake lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok egy helyen történő műveleti és felderítési jellegű feldolgozását.

Az Azure Data Factory egy teljes körűen felügyelt felhőalapú integrációs szolgáltatás, amely feltölti az adatokat a rendszer, és akkor értékes időt a elemzési megoldásokat felépítésekor mentése lake használható. Az alábbiakban az adatok betöltését az Azure Data Lake Store Azure Data Factory használatával legfontosabb előnyei:

* **Egyszerűen állíthat be**: 5. lépés intuitív varázsló nem parancsfájl-szükséges.
* **Gazdag adattároló támogatási**: széles választékának a helyszíni és felhőalapú adattároló beépített támogatása, tekintse meg a részletes listát [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok, és a globális szolgáltatás meglétének biztosítja az adatok sosem hagyja el a földrajzi határ
* **Nagy teljesítményű**: legfeljebb 1 GB/s Adatbetöltési sebesség az Azure Data Lake Store. További részletek a [a másolási tevékenység](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan használ az adatok a Data Factory másolása eszközzel **adatok betöltése az Amazon S3 az Azure Data Lake Store**. Adatok másolása egyéb típusú adattároló hasonló lépések követésével.

> [!NOTE]
>  Általános képességeivel kapcsolatos információk a Data Factory az adatok másolása az Azure Data Lake Store, lásd: [másolása adatok vagy az Azure Data Lake Store az Azure Data Factory használatával](connector-azure-data-lake-store.md) cikk.
>
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
* **Az Azure Data Lake Store**. Ha a Data Lake Store-fiók nem rendelkezik, tekintse meg a [Data Lake Store-fiók létrehozása](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account) cikk lépéseit követve létrehozhat egyet.
* **Amazon S3**. Ez a cikk bemutatja, hogyan adatok másolása az Amazon S3. Egyéb adattárakhoz hasonló módon használhatja.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Kattintson a **NEW** (Új) lehetőségre a bal oldali menüben, kattintson az **Data + Analytics** (Adatok + analitika) pontra, majd a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** lapján adja meg az értékeket, az alábbi képernyőfelvételen látható módon: 
      
     ![Új előállító lap](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **név.** Adjon meg egy globális egyedi nevet az adat-előállítóban. Ha a következő hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-ra), majd próbálkozzon újra a létrehozással. Lásd: [Data Factory - elnevezési szabályait](naming-rules.md) cikk az adat-előállító összetevők elnevezési szabályait.
  
            `Data factory name "LoadADLSDemo" is not available`

    * **Előfizetés.** Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
    * **Erőforráscsoport.** A legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válasszon **hozzon létre új** lehetőséget, majd írja be az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió.** Válassza ki **V2 (előzetes verzió)**.
    * **Hely.** Válassza ki az adat-előállító helyét. Csak a támogatott helyek a legördülő listában jelennek meg. Az adattároló (Azure Data Lake Store, Azure Storage, Azure SQL Database, stb.) adat-előállító által használt más helyek és régiókban is szerepelhet.

3. Kattintson a **Create** (Létrehozás) gombra.
4. Miután létrehozása befejeződött, navigáljon a data factory, és megjelenik a **adat-előállító** lapon, az ábrán látható módon. Kattintson a **Szerző & figyelő** csempe az adatok integrációs alkalmazást egy külön lapján. 
   
   ![Data factory kezdőlap](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Adatok betöltése az Azure Data Lake Store

1. A get elindított oldalon kattintson **adatok másolása** feliratú csempéjén, így az adatok másolása eszköz indítása. 

   ![Másolja az adatokat eszköz csempe](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. A a **tulajdonságok** lapon adja meg az adatok másolása eszköz **CopyFromAmazonS3ToADLS** a a **feladat neve**, és kattintson a **tovább**. 

    ![Másolja az adatokat eszköz-Tulajdonságok lap](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Az a **forrás adattár** lapon jelölje be **Amazon S3**, és kattintson a **következő**.

    ![Forrás adatokat tároló lap](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Az a **adja meg, Amazon S3 kapcsolat** lapon esetén tegye a következőket: 
    1. Adja meg a **Kulcsazonosítója hozzáférési**.
    2. Adja meg a **titkos hívóbetű**.
    3. Kattintson a **Tovább** gombra. 

        ![Adja meg az Amazon S3 fiók](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. A a **válassza ki azt a bemeneti fájl vagy mappa** párbeszédpanelen keresse meg a mappát vagy fájlt, másolja át, válassza ki, majd kattintson a kívánt **válasszon**, majd kattintson a **következő**. 

    ![Válassza ki a bemeneti fájl vagy mappa](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Az a **adatok céltár** lapon jelölje be **Azure Data Lake Store**, és kattintson a **tovább**. 

    ![A cél adatokat tároló lap](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Ezen a lapon válassza ki a példány viselkedést ellenőrzésével **fájlok másolása a rekurzív módon** és **bináris másolása** (mint a fájlok másolása-van) beállítások. Kattintson a **Tovább** gombra.

    ![Adja meg a kimeneti mappa](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Az a **megadása Data Lake Store kapcsolat** lapon esetén tegye a következőket: 

    1. Válassza ki a Data Lake Store a a **Data Lake Store-fiók neve**.
    2. Adja meg, beleértve a szolgáltatás egyszerű információkat **bérlői**, **résztvevő-azonosító szolgáltatás**, és **szolgáltatás elsődleges kulcsa**.
    3. Kattintson a **Tovább** gombra. 

    > [!IMPORTANT]
    > Ebben a bemutatóban használhat egy **egyszerű** data lake store hitelesítéséhez. Az útmutatást követve [Itt](connector-azure-data-lake-store.md#using-service-principal-authentication) , és győződjön meg arról, hogy biztosítsa a szolgáltatás egyszerű megfelelő engedélyeket az Azure Data Lake Store.

    ![Adja meg az Azure Data Lake Store-fiók](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. Az a **válassza ki azt a kimeneti fájl vagy mappa** adja meg azokat **copyfroms3**, majd kattintson a **következő**. 

    ![Adja meg a kimeneti mappa](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. Az a **beállítások** kattintson **következő**. 

    ![Beállítások lap](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Az a **összegzés** lapon tekintse át a beállításokat, és kattintson a **következő**.

    ![Összefoglaló lap](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Az a **telepítési lap**, kattintson a **figyelő** figyelheti a folyamat (feladat).

    ![Telepítés lap](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Figyelje meg, hogy a **figyelő** a bal oldali lapon automatikusan ki van jelölve. Megjelenik a tevékenységfuttatási részletek megtekintése, és futtassa újra a kimenetátirányítási mechanizmusával hivatkozások a **műveletek** oszlop. 

    ![A figyelő folyamat fut](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. A Futtatás futószalag társított tevékenység fut megtekintéséhez kattintson **nézet tevékenység fut** hivatkozásra a **műveletek** oszlop. Nincs (másolási tevékenység) csak egy tevékenység a sorban, így csak egy bejegyzés. Váltson vissza az a folyamat futó nézet, kattintson a **folyamatok** tetején. Kattintson a **frissítése** a listájának frissítése. 

    ![A figyelő tevékenység fut](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Győződjön meg arról, hogy az adatokat az Azure Data Lake Store másolódik. 

    ![Ellenőrizze a Data Lake Store kimeneti](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>További lépések

Továbblépés a következő cikkben tájékozódhat az Azure Data Lake Store támogatása: 

> [!div class="nextstepaction"]
>[Azure Data Lake Store-összekötő](connector-azure-data-lake-store.md)