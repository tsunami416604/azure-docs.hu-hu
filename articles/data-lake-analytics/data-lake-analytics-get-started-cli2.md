---
title: "Az Azure Data Lake Analytics használatának első lépései az Azure CLI 2.0 használatával | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI 2.0-s verzióját egy Data Lake Analytics-fiók létrehozásához, egy Data Lake Analytics-feladat létrehozásához U-SQL használatával, valamint a feladat elküldéséhez. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 109460cecc4e11c729203af97c9bf1c22b90e61a
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20-preview"></a>Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései az Azure CLI 2.0-val (előzetes verzió)
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI 2.0-s verzióját Azure Data Lake Analytics-fiókok létrehozásához, Data Lake Analytics-feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok Data Lake Analytics-fiókokba való elküldéséhez. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

Az oktatóanyag során elkészít egy feladatot, amely beolvas egy tabulátorral elválasztott értékeket (TSV) tartalmazó fájlt, és azt vesszővel elválasztott értékeket (CSV) tartalmazó fájllá konvertálja. Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, használja a legördülő listákat a szakasz tetején.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI 2.0**. Lásd: [Install and configure Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (Az Azure parancssori felület telepítése és konfigurálása).
* **Engedélyezze a Data Lake Store/Analytics CLI 2.0 előzetes verzióját**. A Data Lake Store és a Data Lake Analytics CLI 2.0 felületei még csak előzetes verzióban érhetők el. Futtassa az alábbi parancsokat mindkettő engedélyezéséhez:

    ```azurecli
    az component update --add dls
    az component update --add dla 
    ```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésébe:

```azurecli
az login
```

Fel kell keresnie egy URL-címet, és ott meg kell adnia egy hitelesítő kódot.  Ezután kövesse az útmutatásokat a hitelesítő adatai megadásához.

Miután bejelentkezett, a login paranccsal listázhatja az előfizetéseit.

Egy adott előfizetés használata:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása
A feladatok futtatásához rendelkeznie kell egy Data Lake Analytics-fiókkal. A Data Lake Analytics-fiók létrehozásához az alábbiakat kell megadnia:

* **Azure-erőforráscsoport**. A Data Lake Analytics-fiókot egy Azure-erőforráscsoporton belül kell létrehoznia. Az [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként használja. Az alkalmazás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti.  
  
    Az előfizetés alá tartozó meglévő erőforráscsoportok megjelenítése:
  
    ```azurecli
    az group list 
    ```

    Új erőforráscsoport létrehozása:
  
    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **A Data Lake Analytics-fiók neve**. Minden Data Lake Analytics-fiók rendelkezik egy névvel.
* **Hely**. Használja az egyik, a Data Lake Analytics szolgáltatást támogató Azure-adatközpontot.
* **Alapértelmezett Data Lake Store-fiók:** minden Data Lake Analytics-fiókhoz tartozik egy alapértelmezett Data Lake Store-fiók.
  
    A meglévő Data Lake Store-fiók megjelenítése:

    ```azurecli
    az dls account list
    ```
  
    Új Data Lake Store-fiók létrehozása:
  
    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Egy Data Lake Analytics-fiók létrehozásához használja a következő szintaxist:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

A fiók létrehozása után az alábbi parancsok használatával listázhatja a fiókokat és jelentheti meg azok részleteit:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Adatok feltöltése a Data Lake Store-ba
Az alábbi oktatóanyagban keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban. 

Az Azure Portal felhasználói felületet biztosít bizonyos mintaadatfájlok alapértelmezett Data Lake Store-fiókba való másolásához. Ilyen adatfájl a keresési napló is. Az adatok alapértelmezett Data Lake Store-fiókba való feltöltéséhez lásd a [Forrásadatok előkészítése](data-lake-analytics-get-started-portal.md#prepare-source-data) című szakaszt.

A fájlok a CLI 2.0 segítségével való feltöltéséhez használja az alábbi parancsokat:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

A Data Lake Analytics az Azure Blob Storage-hoz is rendelkezik hozzáféréssel.  A fájlok az Azure Blob Storage-ba történő feltöltéséhez lásd: [Using the Azure CLI with Azure Storage](../storage/storage-azure-cli.md) (Az Azure parancssori felület és az Azure Storage használata).

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics-feladatok küldése
A Data Lake Analytics-feladatok nyelve a U-SQL. További információk a U-SQL-ről: [U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) és [U-SQL nyelvi referencia](http://go.microsoft.com/fwlink/?LinkId=691348).

**Data Lake Analytics-feladatparancsfájl létrehozása**

Hozzon létre egy szövegfájlt az alábbi U-SQL-parancsfájl alapján, és mentse a szövegfájlt a munkaállomáson:
  
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

Egyszerűbb relatív útvonalakat használni az alapértelmezett Data Lake Store-fiókokban tárolt fájlokhoz. De használhat abszolút elérési utakat is.  Példa:

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

A társított tárfiókokban lévő fájlok eléréséhez abszolút elérési utakat kell használnia.  A társított Azure Storage-fiókban tárolt fájlok szintaxisa:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > A nyilvános blobokkal vagy a nyilvános tárolókhoz hozzáférési jogosultságokkal rendelkező Azure Blob-tárolók használata jelenleg nem támogatott.      
  > 
  > 

**Feladatok elküldése**

Feladatok elküldéséhez használja a következő szintaxist.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Példa:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Feladatok listázása és részleteik megjelenítése**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Feladatok megszakítása**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Feladatok eredményeinek lekérése

A feladat befejezése után az alábbi parancsok segítségével listázhatja ki a kimeneti fájlokat, és töltheti le a fájlokat:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" 
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Példa:

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Következő lépések

* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* A Data Lake Analytics CLI 2.0-referenciadokumentum megtekintéséhez lásd: [Data Lake Analytics – az dla](https://docs.microsoft.com/cli/azure/dla).
* A Data Lake Store CLI 2.0-referenciadokumentum megtekintéséhez lásd: [Data Lake Store – az dls](https://docs.microsoft.com/cli/azure/dls).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
* A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).


