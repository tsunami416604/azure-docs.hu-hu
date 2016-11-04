---
title: Az Azure Data Lake Analytics használatának első lépései az Azure portállal | Microsoft Docs
description: 'Ebből a cikkből megtudhatja, hogyan használhatja az Azure portált egy Data Lake Analytics-fiók létrehozásához, egy Data Lake Analytics-feladat létrehozásához U-SQL használatával, valamint a feladat elküldéséhez. '
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései az Azure portállal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure Portalt Azure Data Lake Analytics-fiókok létrehozásához, Data Lake Analytics-feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok Data Lake Analytics-fiókokba való elküldéséhez. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

Az oktatóanyag során elkészít egy feladatot, amely beolvas egy tabulátorral elválasztott értékeket (TSV) tartalmazó fájlt, és azt vesszővel elválasztott értékeket (CSV) tartalmazó fájllá konvertálja. Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, kattintson a szakasz tetején található fülekre. Ha az első feladatot sikeresen befejezte, elkezdhet összetettebb adatátalakításokat írni a U-SQL-lel.

## Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## Data Lake Analytics-fiók létrehozása
A feladatok futtatásához rendelkeznie kell egy Data Lake Analytics-fiókkal.

Minden Data Lake Analytics-fiók egy [Azure Data Lake Store]()-fióktól függ.  Ezt a fiókot kezeli a rendszer alapértelmezett Data Lake Store-fiókként.  A Data Lake Store-fiókot létrehozhatja előre, vagy a Data Lake Analytics-fiók létrehozásakor. Ebben az oktatóanyagban a Data Lake Store-fiókot a Data Lake Analytics-fiókkal együtt fogja létrehozni.

**Data Lake Analytics-fiók létrehozása**

1. Jelentkezzen be az új [klasszikus Azure portálra](https://portal.azure.com).
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Lake Analytics** elemre.
3. Írja be, vagy válassza ki az alábbiakat:
   
    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **Név:** Nevezze el az Analytics-fiókot.
   * **Data Lake Store:** Minden Data Lake Analytics-fiókhoz tartozik egy függőségként kezelt Data Lake Store-fiók. A Data Lake Analytics-fióknak és a függő Data Lake Store-fióknak ugyanabban az Azure-adatközpontban kell lennie. Az útmutatást követve hozzon létre egy új Data Lake Store-fiókot, vagy válasszon ki egy már meglévőt.
   * **Előfizetés:** Válassza ki az Analytics-fiókhoz használt Azure-előfizetést.
   * **Erőforráscsoport**. Válasszon ki egy meglévő Azure-erőforráscsoportot, vagy hozzon létre egy újat. Az Azure Resource Manager (ARM) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként használja. További információk: [Azure Resource Manager overview](../resource-group-overview.md) (Az Azure Resource Manager áttekintése). 
   * **Hely**. Válasszon egy Azure-adatközpontot az Azure Data Lake Analytics-fiókhoz. 
4. Válassza **A kezdőpulton rögzít** lehetőséget. Ez szükséges az oktatóanyag lépéseinek követéséhez.
5. Kattintson a **Létrehozás** gombra. Ezzel továbblép a kezdőpult portálra. A kezdőpulton megjelenik egy új csempe „Az Azure Data Lake Analytics telepítése” címkével. A Data Lake Analytics-fiók létrehozása igénybe vehet pár másodpercet. Ha a fiók kész, a portál egy új panelen nyitja meg a fiókot.
   
    ![Azure Data Lake Analytics portál panel](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

Egy Data Lake Analytics-fiók létrehozása után hozzáadhat további Data Lake Store-fiókokat és Azure Storage-fiókokat is. Útmutatásért lásd: [Manage Data lake Analytics account data sources](data-lake-analytics-manage-use-portal.md#manage-account-data-sources) (A Data Lake Analytics-fiók adatforrásainak kezelése).

## Forrásadatok előkészítése
Az oktatóanyag során keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban. 

Az Azure portál felhasználói felületet biztosít bizonyos mintaadatfájlok az alapértelmezett Data Lake-fiókba való másolásához. Ilyen adatfájl a keresési napló is.

**Mintaadatfájlok másolása**

1. Az Azure Portalon kattintson a bal felső sarokban található **Microsoft Azure** elemre.
2. Kattintson a Data Lake Analytics-fiókja nevével ellátott csempére.  Ezt a rendszer a fiók létrehozásakor tűzi ki.
   Ha a fiók nem látható ezen a felületen, a fiók megnyitásához tekintse meg a [Data Lake Analytics-fiók megnyitása a portálról](data-lake-analytics-manage-use-portal.md#access-adla-account) című szakaszt..
3. Bontsa ki az **Alapvető erőforrások** panelt, majd kattintson a **Mintafeladatok kezelése** lehetőségre. Ekkor megnyílik egy másik, **Mintafeladatok** nevű panel.
4. Kattintson a **Mintaadatok másolása** lehetőségre, majd kattintson megerősítésként az **OK** gombra.
5. Kattintson a harangot ábrázoló **Értesítés** ikonra. Megjelenik egy értesítés a következő szöveggel: **A mintaadatok frissítése befejeződött**. Az értesítési panel bezárásához kattintson a panelen kívülre.
6. A Data Lake Analytics-fiók paneljén kattintson a felül található **Adatkezelő** elemre. 
   
    ![Azure Data Lake Analytics: Adatkezelő gomb](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)
   
    Két panel nyílik meg. Az egyik az **Adatkezelő**, a másik az alapértelmezett Data Lake Store-fiók.
7. Az alapértelmezett Data Lake Store-fiók panelen kattintson a **Minták** elemre a mappa kibontásához, majd kattintson az **Adatok** elemre a mappa kibontásához. A következő fájlokat és mappákat láthatja:
   
   * AmbulanceData/
   * AdsLog.tsv
   * SearchLog.tsv
   * version.txt
   * WebLog.log
     
     Az oktatóanyag során a SearchLog.tsv fájlt fogja használni.

A gyakorlatban vagy arra programozza az alkalmazást, hogy a társított tárfiókokba írja az adatokat, vagy arra, hogy feltöltse az adatokat. Fájlok feltöltése: [Adatok feltöltése a Data Lake Store-ba](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) és [Adatok feltöltése a Blob Storage-ba](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

## Data Lake Analytics-feladatok létrehozása és küldése
Miután előkészítette a forrásadatokat, nekiláthat a U-SQL-parancsfájl elkészítésének.  

**Feladat küldése**

1. A portál Data Lake Analytics-fiók paneljén kattintson az **Új feladat** lehetőségre. 
   
    ![Azure Data Lake Analytics: Új feladat gomb](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)
   
    Ha nem látja a panelt, tekintse meg a [Data Lake Analytics-fiók megnyitása a portálról](data-lake-analytics-manage-use-portal.md#access-adla-account) című szakaszt..
2. Adja meg a **Feladat nevét**, és az alábbi U-SQL-parancsfájlt:
   
    ![Azure Data Lake Analytics U-SQL-feladatok létrehozása](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)
   
        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
   
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();
   
    Ez a U-SQL-parancsfájl beolvassa a forrásadatfájlt az **Extractors.Tsv()** segítségével, majd létrehoz egy csv-fájlt az **Outputters.Csv()** használatával. 
   
    Ne módosítsa az elérési utat, kivéve, ha átmásolja a forrásfájlt egy másik helyre.  A Data Lake Analytics létrehozza a kimeneti mappát, ha az még nem létezik.  Ebben az esetben egyszerű, relatív útvonalakat használunk.  
   
    Egyszerűbb relatív útvonalakat használni az alapértelmezett Data Lake-fiókokban tárolt fájlokhoz. De használhat abszolút elérési utakat is.  Példa: 
   
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    További információk a U-SQL nyelvről: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével). és [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348) (U-SQL nyelvi referencia).

1. Kattintson felül a **Feladat elküldése** lehetőségre. Megjelenik egy új Feladat részletei panel. A címsoron a feladat állapota látható.   
2. Várjon, amíg a feladat állapota **Sikeres** nem lesz. Ha a feladat kész, a portál a feladat részleteit egy új panelen nyitja meg:
   
    ![Azure Data Lake Analytics-feladat részletei](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-completed.png)
   
    Az előző képernyőképen látható, hogy a feladat befejezése az Elküldött állapottól a Befejezve állapotig hozzávetőlegesen 1,5 percet vett igénybe.
   
    Amennyiben a feladat nem sikerült, tekintse meg a [Data Lake Analytics-feladatok figyelése és hibaelhárítása](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorials.md) című szakaszt.
3. A **Feladat részletei** panelen kattintson a feladat nevére a **SearchLog-from-Data-Lake.csv** alatt. Megtekintheti a kimeneti fájl előnézetét, illetve letöltheti, átnevezheti és törölheti azt.
   
    ![Azure Data Lake Analytics-feladat kimeneti fájljának tulajdonságai](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)
4. A kimeneti fájl megtekintéséhez kattintson az **Előnézet** lehetőségre.
   
    ![Azure Data Lake Analytics-feladat kimeneti fájljának előnézete](./media/data-lake-analytics-get-started-portal/data-lake-analytics-job-output-preview.png)

## Lásd még:
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure portállal).
* A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).
* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* A diagnosztikai információk naplózása: [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Az Azure Data Lake Analytics diagnosztikai naplóinak elérése).

<!--HONumber=Sep16_HO4-->


