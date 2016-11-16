---
title: "Az Azure Data Lake Analytics használatának első lépései az Azure parancssori felülettel | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja az Azure parancssori felületet egy Data Lake Store-fiók létrehozásához, egy Data Lake Analytics-feladat létrehozásához U-SQL használatával, valamint a feladat elküldéséhez. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 651021d4-4591-4c48-b1ef-3ebc4606d66d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8b38c62ae1a60728d08643990238e2cc69cb6447


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-commandline-interface-cli"></a>Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései az Azure parancssori felülettel
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure parancssori felületet Azure Data Lake Analytics-fiókok létrehozásához, Data Lake Analytics-feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok Data Lake Analytics-fiókokba való elküldéséhez. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

Az oktatóanyag során elkészít egy feladatot, amely beolvas egy tabulátorral elválasztott értékeket (TSV) tartalmazó fájlt, és azt vesszővel elválasztott értékeket (CSV) tartalmazó fájllá konvertálja. Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, kattintson a szakasz tetején található fülekre.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure parancssori felület (CLI)**. Lásd: [Install and configure Azure CLI](../xplat-cli-install.md) (Az Azure parancssori felület telepítése és konfigurálása).
  * Töltse le és telepítse az **előzetes kiadású** [Azure parancssori felületi eszközöket](https://github.com/MicrosoftBigData/AzureDataLake/releases) a bemutató elvégzéséhez.
* A **hitelesítésnél** használja az alábbi parancsot:
  
        azure login
    További információ a munkahelyi vagy iskolai fiók segítségével történő hitelesítésről: [Connect to an Azure subscription from the Azure CLI](../xplat-cli-connect.md) (Csatlakozás Azure-előfizetéshez az Azure parancssori felületről).
* **Váltson Azure Resource Manager módra** az alábbi paranccsal:
  
        azure config mode arm

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása
A feladatok futtatásához rendelkeznie kell egy Data Lake Analytics-fiókkal. A Data Lake Analytics-fiók létrehozásához az alábbiakat kell megadnia:

* **Azure-erőforráscsoport:** a Data Lake Analytics-fiókot egy Azure-erőforráscsoporton belül kell létrehoznia. Az [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként használja. Az alkalmazás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti.  
  
    Az erőforráscsoportok felsorolása az előfizetésben:
  
        azure group list 
  
    Új erőforráscsoport létrehozása:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **A Data Lake Analytics-fiók neve**
* **Hely:** az egyik, a Data Lake Analytics szolgáltatást támogató Azure-adatközpont.
* **Alapértelmezett Data Lake-fiók:** minden Data Lake Analytics-fiókhoz tartozik egy alapértelmezett Data Lake-fiók.
  
    A meglévő Data Lake-fiók megjelenítése:
  
        azure datalake store account list
  
    Új Data Lake-fiók létrehozása:
  
        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"
  
  > [!NOTE]
  > A Data Lake-fiók neve csak kisbetűket és számokat tartalmazhat.
  > 
  > 

**Data Lake Analytics-fiók létrehozása**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"            

![Data Lake Analytics-fiók megjelenítése](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> A Data Lake Analytics-fiók neve csak kisbetűket és számokat tartalmazhat.
> 
> 

## <a name="upload-data-to-data-lake-store"></a>Adatok feltöltése a Data Lake Store-ba
Az oktatóanyag során keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban. 

Az Azure portál felhasználói felületet biztosít bizonyos mintaadatfájlok az alapértelmezett Data Lake-fiókba való másolásához. Ilyen adatfájl a keresési napló is. Az adatok alapértelmezett Data Lake Store-fiókba való feltöltéséhez lásd a [Forrásadatok előkészítése](data-lake-analytics-get-started-portal.md#prepare-source-data) című szakaszt.

A fájlok parancssori felülettel való feltöltéséhez használja az alábbi parancsot:

      azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
      azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

A Data Lake Analytics az Azure Blob Storage-hoz is rendelkezik hozzáféréssel.  A fájlok az Azure Blob Storage-ba történő feltöltéséhez lásd: [Using the Azure CLI with Azure Storage](../storage/storage-azure-cli.md) (Az Azure parancssori felület és az Azure Storage használata).

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics-feladatok küldése
A Data Lake Analytics-feladatok nyelve a U-SQL. További információk a U-SQL-ről: [U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) és [U-SQL nyelvi referencia](http://go.microsoft.com/fwlink/?LinkId=691348).

**Data Lake Analytics-feladatparancsfájl létrehozása**

* Hozzon létre egy szövegfájlt az alábbi U-SQL-parancsfájl alapján, és mentse a szövegfájlt a munkaállomáson:
  
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
  
    Ne módosítsa az elérési utat, kivéve, ha átmásolja a forrásfájlt egy másik helyre.  A Data Lake Analytics létrehozza a kimeneti mappát, ha az még nem létezik.
  
    Egyszerűbb relatív útvonalakat használni az alapértelmezett Data Lake-fiókokban tárolt fájlokhoz. De használhat abszolút elérési utakat is.  Példa: 
  
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    A társított tárfiókokban lévő fájlok eléréséhez abszolút elérési utakat kell használnia.  A társított Azure Storage-fiókban tárolt fájlok szintaxisa:
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > A nyilvános blobokkal vagy a nyilvános tárolókhoz hozzáférési jogosultságokkal rendelkező Azure Blob-tárolók használata jelenleg nem támogatott.      
  > 
  > 

**Feladat küldése**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


A következő parancsokkal sorolhatja fel a feladatokat, lekérhet feladatadatokat és visszavonhat feladatokat:

```
azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
azure datalake analytics job list "<Data Lake Analytics Account Name>"
azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"
```

A feladat befejezése után az alábbi parancsmagok segítségével listázhatja ki a fájlt, és letöltheti a fájlt:

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Lásd még:
* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure portállal).
* A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).




<!--HONumber=Nov16_HO2-->


