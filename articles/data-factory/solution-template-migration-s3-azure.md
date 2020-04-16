---
title: Adatok áttelepítése az Amazon S3-ról az Azure Data Lake Storage Gen2 szolgáltatásba
description: Ismerje meg, hogyan használhat megoldássablont az Amazon S3-ból származó adatok áttelepítéséhez egy külső vezérlőtábla használatával az AWS S3 és az Azure Data Factory partíciólistájának tárolására.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414763"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Adatok áttelepítése az Amazon S3-ról az Azure Data Lake Storage Gen2 szolgáltatásba

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A sablonok segítségével több száz millió fájlt tartalmazó petabájtnyi adatot telepíthet át az Amazon S3-ról az Azure Data Lake Storage Gen2-re. 

 > [!NOTE]
 > Ha kis adatmennyiséget szeretne másolni az AWS S3-ból az Azure-ba (például kevesebb, mint 10 TB), hatékonyabb és egyszerűbb az [Azure Data Factory Copy Data eszköz](copy-data-tool.md)használata. A cikkben ismertetett sablon több, mint amire szüksége van.

## <a name="about-the-solution-templates"></a>A megoldássablonok – beamelyek

Az adatpartíció különösen 10 TB-nál több adat áttelepítésekor ajánlott. Az adatok particionálásához használja ki az "előtag" beállítást az Amazon S3 mappáinak és fájljainak név szerinti szűréséhez, majd minden ADF másolási feladat egyszerre egy partíciót másolhat. A jobb átviteli rendszer érdekében egyidejűleg több ADF-másolási feladatot is futtathat.

Az adatáttelepítéshez általában egyszeri előzményadat-áttelepítésre van szükség, valamint rendszeres időközönként szinkronizálni kell az AWS S3-ról az Azure-ra történő módosításokat. Az alábbiakban két sablon található, ahol az egyik sablon az egyszeri előzményadatok áttelepítését, a másik pedig az AWS S3-ról az Azure-ra történő módosítások szinkronizálását ismerteti.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>A sablon áttelepíti a korábbi adatokat az Amazon S3-ról az Azure Data Lake Storage Gen2-re

Ez a sablon (*sablon neve: áttelepíteni a korábbi adatokat AWS S3 az Azure Data Lake Storage Gen2*) feltételezi, hogy írt egy partíciólistát egy külső vezérlőtábla az Azure SQL Database.This template ( template name: migrate historical data from AWS S3 to Azure Data Lake Storage Gen2 ) assumes that you have written a partition list in an external control table in Azure SQL Database. Így *keresési* tevékenységet fog használni a partíciólista lekéréséhez a külső vezérlőtáblából, minden partíció felett iterálni, és minden Egyes ADF másolási feladat másolása partícióval egyszerre. Miután minden másolási feladat befejeződött, *a Tárolt eljárás* tevékenység et használ a vezérlőtábla egyes partíciói másolási állapotának frissítéséhez.

A sablon öt tevékenységet tartalmaz:
- **A keresés** lekéri azokat a partíciókat, amelyek nem lettek átmásolva az Azure Data Lake Storage Gen2-be egy külső vezérlőtáblából. A tábla neve *s3_partition_control_table,* és a táblából adatokat betöltő lekérdezés *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure = 0"*.
- **ForEach** lekéri a partíciólistát a *lookup* tevékenységből, és minden partíciót az *TriggerCopy tevékenységhez.* Beállíthatja, hogy a *batchCount* egyszerre több ADF másolási feladatot futtasson. Van meg 2 ebben a sablonban.
- **Az ExecutePipeline** végrehajtja a *CopyFolderPartitionFromS3* folyamatot. Az ok, amiért létrehozunk egy másik folyamatot, hogy minden másolási feladat egy partíciót, mert ez megkönnyíti a sikertelen másolási feladat újratöltése, hogy az adott partíció t a AWS S3 újra. Az összes többi más partíciót betöltő másolási feladatoknem érinti.
- **Másolja** az egyes partíciókat az AWS S3-ról az Azure Data Lake Storage Gen2 szolgáltatásba.
- **Az SqlServerStoredProcedure** frissíti a vezérlőtábla egyes partícióinak másolásának állapotát.

A sablon két paramétert tartalmaz:
- **AWS_S3_bucketName** a gyűjtőneve az AWS S3-on, ahonnan adatokat szeretne áttelepíteni. Ha az AWS S3 több gyűjtőjéből szeretne adatokat áttelepíteni, hozzáadhat még egy oszlopot a külső vezérlőtáblához az egyes partíciók gyűjtőnevének tárolásához, és frissítheti a folyamatot, hogy ennek megfelelően adatokat olvasson le az oszlopból.
- **Azure_Storage_fileSystem** a fileSystem neve az Azure Data Lake Storage Gen2, ahol adatokat szeretne áttelepíteni.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Ahhoz, hogy a sablon csak az Amazon S3-ról az Azure Data Lake Storage Gen2-re másolja a módosított fájlokat

Ez a sablon (*sablon neve: másolás különbözeti adatok At AWS S3 az Azure Data Lake Storage Gen2*) használja LastModifiedTime minden fájl másolni az új vagy frissített fájlokat csak AWS S3 az Azure-ba. Ne feledje, ha a fájlokat vagy mappákat már időszeleteléssel particionálták az AWS S3 fájl- vagy mappanevének részeként (például /yyyy/mm/dd/file.csv), akkor az [oktatóanyagba](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) az új fájlok növekményes betöltésének hatékonyabb megközelítését kaphatja meg. Ez a sablon feltételezi, hogy az Azure SQL Database egy külső vezérlőtáblájában írt egy partíciólistát. Így *keresési* tevékenységet fog használni a partíciólista lekéréséhez a külső vezérlőtáblából, minden partíció felett iterálni, és minden Egyes ADF másolási feladat másolása partícióval egyszerre. Amikor minden másolási feladat elkezdi másolni a fájlokat az AWS S3-ból, a LastModifiedTime tulajdonságra támaszkodik, hogy csak az új vagy frissített fájlokat azonosítsa és másolja. Miután minden másolási feladat befejeződött, *a Tárolt eljárás* tevékenység et használ a vezérlőtábla egyes partíciói másolási állapotának frissítéséhez.

A sablon hét tevékenységet tartalmaz:
- **A keresés** lekéri a partíciókat egy külső vezérlőtáblából. A tábla neve *s3_partition_delta_control_table,* és a táblából adatokat betöltő lekérdezés *"select distinct PartitionPrefix from s3_partition_delta_control_table"*.
- **ForEach** lekéri a partíciólistát a *lookup* tevékenységből, és minden partíciót a *TriggerDeltaCopy tevékenységhez.* Beállíthatja, hogy a *batchCount* egyszerre több ADF másolási feladatot futtasson. Van meg 2 ebben a sablonban.
- **Az ExecutePipeline** végrehajtja a *DeltaCopyFolderPartitionFromS3* folyamatot. Az ok, amiért létrehozunk egy másik folyamatot, hogy minden másolási feladat egy partíciót, mert ez megkönnyíti a sikertelen másolási feladat újratöltése, hogy az adott partíció t a AWS S3 újra. Az összes többi más partíciót betöltő másolási feladatoknem érinti.
- **A keresés** lekéri az utolsó másolási feladat futási idejét a külső vezérlőtáblából, így az új vagy frissített fájlok azonosíthatók a LastModifiedTime segítségével. A tábla neve *s3_partition_delta_control_table,* és a táblából adatokat betöltő lekérdezés *"select max(JobRunTime) as LastModifiedTime s3_partition_delta_control_table ahol PartitionPrefix = "@{pipeline().parameters.prefixStr}" és SuccessOrFailure = 1"*.
- **Másolja** az új vagy módosított fájlokat csak az egyes partíciók AWS S3 az Azure Data Lake Storage Gen2. A *modifiedDatetimeStart* tulajdonsága az utolsó másolási feladat futási idejére van beállítva. A *modifiedDatetimeEnd* tulajdonsága az aktuális másolási feladat futási idejére van beállítva. Ne feledje, hogy az idő az UTC időzónára vonatkozik.
- **Az SqlServerStoredProcedure** frissíti az egyes partíciók másolásának és a vezérlőtáblában a futási idő másolásának állapotát, ha az sikeres. A SuccessOrFailure oszlopa 1-re van állítva.
- **Az SqlServerStoredProcedure** frissíti az egyes partíciók másolásának és a vezérlőtáblában a futási idő másolásának állapotát, ha az sikertelen. A SuccessOrFailure oszlopa 0-ra van állítva.

A sablon két paramétert tartalmaz:
- **AWS_S3_bucketName** a gyűjtőneve az AWS S3-on, ahonnan adatokat szeretne áttelepíteni. Ha az AWS S3 több gyűjtőjéből szeretne adatokat áttelepíteni, hozzáadhat még egy oszlopot a külső vezérlőtáblához az egyes partíciók gyűjtőnevének tárolásához, és frissítheti a folyamatot, hogy ennek megfelelően adatokat olvasson le az oszlopból.
- **Azure_Storage_fileSystem** a fileSystem neve az Azure Data Lake Storage Gen2, ahol adatokat szeretne áttelepíteni.

## <a name="how-to-use-these-two-solution-templates"></a>A két megoldássablon használata

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>A sablon áttelepíti a korábbi adatokat az Amazon S3-ról az Azure Data Lake Storage Gen2-re 

1. Hozzon létre egy vezérlőtáblát az Azure SQL Database-ben az AWS S3 partíciólistájának tárolásához. 

    > [!NOTE]
    > A tábla neve s3_partition_control_table.
    > A vezérlőtábla sémája partitionprefix és SuccessOrFailure, ahol PartitionPrefix az előtag az S3 a mappák és fájlok szűréséhez az Amazon S3 név szerint, és SuccessOrFailure az állapota az egyes partíciók másolása: 0 azt jelenti, hogy ez a partíció nem másolt az Azure-ba, és 1 azt jelenti, hogy ez a partíció sikeresen átmásolta az Azure-ba.
    > A vezérlőtáblában 5 partíció van definiálva, és az egyes partíciók másolásának alapértelmezett állapota 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Hozzon létre egy tárolt eljárást ugyanazon az Azure SQL-adatbázison a vezérlőtáblához. 

    > [!NOTE]
    > A tárolt eljárás neve sp_update_partition_success. Az SqlServerStoredProcedure tevékenység hívja meg az ADF-folyamatban.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Nyissa meg a Korábbi adatok áttelepítése az **AWS S3-ból az Azure Data Lake Storage Gen2** sablonba. Adja meg a kapcsolatokat a külső vezérlőtáblába, az AWS S3 adatforrás-tárolóba és az Azure Data Lake Storage Gen2-t a céltárolóba. Ne feledje, hogy a külső vezérlőtábla és a tárolt eljárás ugyanarra a kapcsolatra hivatkozik.

    ![Új kapcsolat létrehozása](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Válassza **a Sablon használata lehetőséget.**

    ![Sablon használata](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. A következő példában látható módon a 2 folyamat és 3 adatkészlet jött létre:

    ![A folyamat áttekintése](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.

    ![Kattintson a **Debug**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. A következő példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Ahhoz, hogy a sablon csak az Amazon S3-ról az Azure Data Lake Storage Gen2-re másolja a módosított fájlokat

1. Hozzon létre egy vezérlőtáblát az Azure SQL Database-ben az AWS S3 partíciólistájának tárolásához. 

    > [!NOTE]
    > A tábla neve s3_partition_delta_control_table.
    > A vezérlőtábla sémája a PartitionPrefix, a JobRunTime és a SuccessOrFailure, ahol a PartitionPrefix az S3 előtag az Amazon S3-ban lévő mappák és fájlok név szerinti szűrésére, a JobRunTime a datetime érték a másolási feladatok futtatásakor, a SuccessOrFailure pedig az egyes partíciók másolásának állapota: 0 azt jelenti, hogy ez a partíció nem lett átmásolva az Azure-ba, és 1 azt jelenti, hogy ez a partíció sikeresen átlett másolva az Azure-ba.
    > A vezérlőtáblában 5 partíció van definiálva. A JobRunTime alapértelmezett értéke lehet az egyszeri előzményadat-áttelepítés indítása. Az ADF másolási tevékenysége az AWS S3-on lévő azon fájlokat másolja, amelyeket utoljára módosítottak ezen időpont után. Az egyes partíciók másolásának alapértelmezett állapota 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Hozzon létre egy tárolt eljárást ugyanazon az Azure SQL-adatbázison a vezérlőtáblához. 

    > [!NOTE]
    > A tárolt eljárás neve sp_insert_partition_JobRunTime_success. Az SqlServerStoredProcedure tevékenység hívja meg az ADF-folyamatban.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Nyissa meg a **különbözeti adatok másolása az AWS S3-ból az Azure Data Lake Storage Gen2** sablonba. Adja meg a kapcsolatokat a külső vezérlőtáblába, az AWS S3 adatforrás-tárolóba és az Azure Data Lake Storage Gen2-t a céltárolóba. Ne feledje, hogy a külső vezérlőtábla és a tárolt eljárás ugyanarra a kapcsolatra hivatkozik.

    ![Új kapcsolat létrehozása](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Válassza **a Sablon használata lehetőséget.**

    ![Sablon használata](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. A következő példában látható módon a 2 folyamat és 3 adatkészlet jött létre:

    ![A folyamat áttekintése](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.

    ![Kattintson a **Debug**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. A következő példához hasonló eredmények jelennek meg:

    ![Az eredmény áttekintése](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. A vezérlőtábla eredményeit a *"select * from s3_partition_delta_control_table"* lekérdezéssel is ellenőrizheti, a kimenet a következő példához hasonló:

    ![Az eredmény áttekintése](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>További lépések

- [Fájlok másolása több tárolóból](solution-template-copy-files-multiple-containers.md)
- [Fájlok áthelyezése](solution-template-move-files.md)