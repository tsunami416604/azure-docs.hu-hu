---
title: "Az Azure Data Lake Analytics használatának első lépései az Azure CLI 2.0 használatával | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI 2.0-s verzióját egy Data Lake Analytics-fiók létrehozásához, egy Data Lake Analytics-feladat létrehozásához U-SQL használatával, valamint a feladat elküldéséhez. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: jgao
ms.openlocfilehash: fe2b84aac718ff5eddd4d73b5dc2120362952c1e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>Az Azure Data Lake Analytics használatának első lépései az Azure parancssori felületének 2.0-s verziójával
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Az oktatóanyaggal egy olyan feladatot fog elkészíteni, amely beolvas egy tabulátorral elválasztott értékeket tartalmazó fájlt (TSV), majd konvertálja azt egy vesszővel elválasztott értékeket tartalmazó fájllá (CSV). Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, használja a legördülő listákat a szakasz tetején.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI 2.0**. Lásd: [Install and configure Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (Az Azure parancssori felület telepítése és konfigurálása).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésébe:

```
azurecli
az login
```

Fel kell keresnie egy URL-címet, és ott meg kell adnia egy hitelesítő kódot.  Ezután kövesse az útmutatásokat a hitelesítő adatai megadásához.

Miután bejelentkezett, a login paranccsal listázhatja az előfizetéseit.

Egy adott előfizetés használata:

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása
A feladatok futtatásához rendelkeznie kell egy Data Lake Analytics-fiókkal. A Data Lake Analytics-fiók létrehozásához az alábbiakat kell megadnia:

* **Azure-erőforráscsoport**. A Data Lake Analytics-fiókot egy Azure-erőforráscsoportban kell létrehoznia. Az [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként használja. Az alkalmazás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti.  

Az előfizetés alá tartozó meglévő erőforráscsoportok megjelenítése:

```
az group list
```

Új erőforráscsoport létrehozása:

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **A Data Lake Analytics-fiók neve**. Minden Data Lake Analytics-fiók rendelkezik egy névvel.
* **Hely**. Használja az egyik, a Data Lake Analytics szolgáltatást támogató Azure-adatközpontot.
* **Alapértelmezett Data Lake Store-fiók:** minden Data Lake Analytics-fiókhoz tartozik egy alapértelmezett Data Lake Store-fiók.

A meglévő Data Lake Store-fiók megjelenítése:

```
az dls account list
```

Új Data Lake Store-fiók létrehozása:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Egy Data Lake Analytics-fiók létrehozásához használja a következő szintaxist:

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

A fiók létrehozása után az alábbi parancsok használatával listázhatja a fiókokat és jelentheti meg azok részleteit:

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Adatok feltöltése a Data Lake Store-ba
Az alábbi oktatóanyagban keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban.

Az Azure Portal felhasználói felületet biztosít bizonyos mintaadatfájlok alapértelmezett Data Lake Store-fiókba való másolásához. Ilyen adatfájl a keresési napló is. Az adatok alapértelmezett Data Lake Store-fiókba való feltöltéséhez lásd a [Forrásadatok előkészítése](data-lake-analytics-get-started-portal.md) című szakaszt.

A fájloknak a parancssori felület 2.0-s verziójával történő feltöltéséhez használja az alábbi parancsokat:

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

A Data Lake Analytics az Azure Blob Storage-hoz is rendelkezik hozzáféréssel.  A fájlok az Azure Blob Storage-ba történő feltöltéséhez lásd: [Using the Azure CLI with Azure Storage](../storage/common/storage-azure-cli.md) (Az Azure parancssori felület és az Azure Storage használata).

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics-feladatok küldése
A Data Lake Analytics-feladatok nyelve a U-SQL. A U-SQL-ről további tudnivalók [A U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) című cikkben és [a U-SQL nyelvvel foglalkozó segédanyagban](http://go.microsoft.com/fwlink/?LinkId=691348) találhatók.

**Data Lake Analytics-feladatparancsfájl létrehozása**

Hozzon létre egy szövegfájlt az alábbi U-SQL-parancsfájl alapján, és mentse a szövegfájlt a munkaállomáson:

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Ez a U-SQL-parancsfájl beolvassa a forrásadatfájlt az **Extractors.Tsv()** segítségével, majd létrehoz egy csv-fájlt az **Outputters.Csv()** használatával.

Ne módosítsa az elérési utat, kivéve, ha átmásolja a forrásfájlt egy másik helyre.  A Data Lake Analytics létrehozza a kimeneti mappát, ha az még nem létezik.

Egyszerűbb relatív útvonalakat használni az alapértelmezett Data Lake Store-fiókokban tárolt fájlokhoz. De használhat abszolút elérési utakat is.  Példa:

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

A társított tárfiókokban lévő fájlok eléréséhez abszolút elérési utakat kell használnia.  A társított Azure Storage-fiókban tárolt fájlok szintaxisa:

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> A nyilvános blobokat tartalmazó Azure Blob-tárolók nem támogatottak.      
> A nyilvános tárolókat tartalmazó Azure Blob-tárolók nem támogatottak.      
>

**Feladatok elküldése**

Feladatok elküldéséhez használja a következő szintaxist.

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Példa:

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Feladatok listázása és részleteik megjelenítése**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Feladatok megszakítása**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Feladatok eredményeinek lekérése

A feladat befejezése után az alábbi parancsok segítségével listázhatja ki a kimeneti fájlokat, és töltheti le a fájlokat:

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Példa:

```
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="pipelines-and-recurrences"></a>Folyamatok és ismétlődések

**Folyamatok és ismétlődések adatainak lekérése**

A korábban elküldött feladatok folyamatadatait az `az dla job pipeline` paranccsal tekintheti meg.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

A korábban elküldött feladatok ismétlődési adatait az `az dla job recurrence` paranccsal tekintheti meg.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Következő lépések

* A Data Lake Analytics parancssori felületének 2.0-s verziójával foglalkozó referenciadokumentum megtekintéséhez lásd: [Data Lake Analytics](https://docs.microsoft.com/cli/azure/dla).
* A Data Lake Store parancssori felületének 2.0-s verziójával foglalkozó referenciadokumentum megtekintéséhez lásd: [Data Lake Store](https://docs.microsoft.com/cli/azure/dls).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
