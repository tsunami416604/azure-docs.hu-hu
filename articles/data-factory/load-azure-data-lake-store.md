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
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 4446f83563293d0834f241dcca382ccf6ea99403
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>Adatok betöltése az Azure Data Lake Store Azure Data Factory használatával

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) van egy vállalati szintű kapacitású adattár a big data koncepción alapuló adatelemzési célokra. Azure Data Lake lehetővé teszi bármilyen méretű, típusú és adatfeldolgozást sebesség adatok rögzítéséhez. Az adatok rögzített a műveleti és felderítési jellegű egyetlen helyen.

Az Azure Data Factory egy olyan felhőalapú teljes körűen felügyelt adatok integrációs szolgáltatás. A szolgáltatás segítségével a data lake feltöltése a meglévő rendszerről és időt takaríthat meg az elemzési megoldásokat felépítése közben.

Az Azure Data Factory az adatok betöltését az Azure Data Lake Store a következő előnyöket nyújtja:

* **Egyszerűen állíthat be**: egy egyszerűen elsajátítható 5. lépés varázsló nem parancsfájl-szükséges.
* **Gazdag adattároló támogatási**: a helyszíni és felhőalapú adattároló széles skáláját beépített támogatása. Részletes listájáért lásd: a tábla [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok. A globális szolgáltatás meglétének biztosítja, hogy az adatok sosem hagyja el a földrajzi határ.
* **Nagy teljesítményű**: legfeljebb 1-GB/s Adatbetöltési sebesség az Azure Data Lake Store. További információkért lásd: [a másolási tevékenység](copy-activity-performance.md).

A cikkből megtudhatja, hogyan használható a Data Factory adatok másolása eszköz _adatok betöltése az Amazon S3 az Azure Data Lake Store_. Akkor is hasonló lépésekkel lehet adatok másolása az egyéb típusú adattároló.

> [!NOTE]
> További információkért lásd: [másolása adatok vagy az Azure Data Lake Store az Azure Data Factory használatával](connector-azure-data-lake-store.md).
>
> Ez a cikk az Azure Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használata lásd [másolási tevékenység során az Azure Data Factoryben az 1-es](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Azure Data Lake Store: Ha a Data Lake Store-fiók nem rendelkezik, tekintse meg a utasításait [Data Lake Store-fiók létrehozása](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3: Ez a cikk bemutatja, hogyan adatok másolása az Amazon S3. Egyéb adattárakhoz hasonló módon használhatja.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza **új** > **adatok + analitika** > **adat-előállító**:
   
   ![Hozzon létre egy új adat-előállító](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** lapján adja meg a mezőket, amelyeknek az alábbi ábrán látható: 
      
   ![Új adat-előállító lap](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Név**: Adja meg az az Azure data factory globálisan egyedi nevet. Ha a hibaüzenet "adat-előállító \"LoadADLSDemo\" nem áll rendelkezésre," írjon be egy másik nevet az adat-előállítóban. Például használhatja a név  _**saját_név**_**ADFTutorialDataFactory**. Próbálja meg újra létrehozni az adat-előállítóban. Adat-előállító összetevők elnevezési szabályait, lásd: [Data Factory elnevezési szabályok](naming-rules.md).
    * **Előfizetés**: válassza ki az Azure-előfizetéshez használandó adat-előállító létrehozása. 
    * **Erőforráscsoport**: a legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válassza ki a **hozzon létre új** lehetőséget, majd írja be az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válasszon **V2 (előzetes verzió)**.
    * **Hely**: Jelölje ki az adat-előállítóban helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattároló adat-előállító által használt más helyek és a régióban lehet. Ezekkel az áruházakkal adatok közé tartoznak a Azure Data Lake Store, Azure Storage, Azure SQL Database és így tovább.

3. Kattintson a **Létrehozás** gombra.
4. Létrehozásának befejezése után lépjen a data factory. Megjelenik a **adat-előállító** kezdőlapja a következő ábrán látható módon: 
   
   ![Data factory kezdőlap](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Válassza ki a **Szerző & figyelő** csempe az adatok integrációs alkalmazást egy külön lapján.

## <a name="load-data-into-azure-data-lake-store"></a>Adatok betöltése az Azure Data Lake Store

1. Az a **Ismerkedés** lapon jelölje be a **adatok másolása** csempe az adatok másolása eszköz: 

   ![Az Adatok másolása eszköz csempéje](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Az a **tulajdonságok** adja meg azokat **CopyFromAmazonS3ToADLS** a a **feladatnév** mezőjét, majd válassza **következő**:

    ![Tulajdonságok lap](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Az a **forrás adattár** lapon jelölje be **Amazon S3**, és válassza ki **következő**:

    ![Forrásadattár lap](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. Az a **adja meg, Amazon S3 kapcsolat** lapon esetén tegye a következőket: 
   1. Adja meg a **hozzáférési kulcs azonosító** érték.
   2. Adja meg a **titkos hívóbetű** érték.
   3. Kattintson a **Tovább** gombra.
   
   ![Adja meg az Amazon S3 fiók](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. Az a **válassza ki azt a bemeneti fájl vagy mappa** párbeszédpanelen keresse meg a mappát és a fájlt, amelyet másolja át. Válassza ki a mappát vagy fájlt, jelölje be **válasszon**, majd válassza ki **következő**:

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Az a **adatok céltár** lapon jelölje be **Azure Data Lake Store**, és válassza ki **következő**:

    ![Céladattár lap](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Válassza ki a példány viselkedést kiválasztásával a **fájlok másolása a rekurzív módon** és **bináris másolási** (mint a fájlok másolása-van) beállítások. Válassza ki **következő**:

    ![Adja meg a kimeneti mappa](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. Az a **megadása Data Lake Store kapcsolat** lapon esetén tegye a következőket: 

   1. Válassza ki a Data Lake Store a a **Data Lake Store-fiók neve**.
   2. Adja meg a szolgáltatás egyszerű adatokat: **bérlői**, **résztvevő-azonosító szolgáltatás**, és **szolgáltatás elsődleges kulcsa**.
   3. Kattintson a **Tovább** gombra.
   
   > [!IMPORTANT]
   > Ebben a bemutatóban használhat egy _egyszerű_ a Data Lake Store hitelesítéséhez. Ügyeljen arra, hogy adja meg az egyszerű szolgáltatás a megfelelő engedélyeket az Azure Data Lake Store következő [ezeket az utasításokat](connector-azure-data-lake-store.md#using-service-principal-authentication).
   
   ![Adja meg az Azure Data Lake Store-fiók](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Az a **válassza ki azt a kimeneti fájl vagy mappa** lapján adja meg **copyfroms3** a kimeneti mappa nevét, és válassza ki **következő**: 

    ![Adja meg a kimeneti mappa](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Az a **beállítások** lapon jelölje be **következő**:

    ![Beállítások lap](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Az a **összegzés** lapon tekintse át a beállításokat, és válassza ki **következő**:

    ![Összefoglaló lap](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Az a **telepítési lap**, jelölje be **figyelő** figyelheti a folyamat (feladat):

    ![Üzembe helyezés lap](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop ismerteti tevékenységfuttatási részletek megtekintése, és futtassa újra a folyamatot:

    ![Folyamatfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Válassza ki, ha tevékenység fut, amely társul a folyamat, futtassa a **nézet tevékenység fut** hivatkozásra a **műveletek** oszlop. Nincs (másolási tevékenység) csak egy tevékenység a sorban, így csak egy bejegyzés. Váltson vissza az a folyamat futó nézet, válassza ki a **folyamatok** tetején. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Minden egyes másolási tevékenység végrehajtási adatainak figyelésére, válassza ki a **részletek** hivatkozásra **műveletek** figyelési nézet tevékenységben. Részletes adatai figyelhetők adatokat fogadó, adatátvitelt, megfelelő időtartamú támadáséhoz hasonlók a forráskiszolgálóról másolt, és konfigurációkat használják:

    ![A figyelő tevékenységfuttatási részletei](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Győződjön meg arról, hogy az adatokat az Azure Data Lake Store másoljuk: 

    ![Ellenőrizze a Data Lake Store kimeneti](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>További lépések

Továbblépés a következő cikkben tájékozódhat az Azure Data Lake Store támogatása: 

> [!div class="nextstepaction"]
>[Azure Data Lake Store-összekötő](connector-azure-data-lake-store.md)