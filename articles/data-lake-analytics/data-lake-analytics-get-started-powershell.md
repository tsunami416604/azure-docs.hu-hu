<properties 
   pageTitle="Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel | Azure" 
   description="Ebből a cikkből megtudhatja, hogyan használhatja az Azure PowerShellt egy Data Lake Store-fiók létrehozásához, egy Data Lake Analytics-feladat létrehozásához U-SQL használatával, valamint a feladat elküldéséhez. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure PowerShellt Azure Data Lake Analytics-fiókok létrehozásához, Data Lake Analytics-feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok elküldéséhez Data Lake Analytics-fiókokba. További információk a Data Lake Analyticsről: [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md).

Az oktatóanyag során elkészít egy feladatot, amely beolvas egy tabulátorral elválasztott értékeket (TSV) tartalmazó fájlt, és azt vesszővel elválasztott értékeket (CSV) tartalmazó fájllá konvertálja. Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, kattintson a szakasz tetején található fülekre.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

##Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
- **Munkaállomás Azure PowerShell-lel**. Lásd: [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása).
    
##Data Lake Analytics-fiók létrehozása

A feladatok futtatásához rendelkeznie kell egy Data Lake Analytics-fiókkal. A Data Lake Analytics-fiók létrehozásához az alábbiakat kell megadnia:

- **Azure-erőforráscsoport:** a Data Lake Analytics-fiókot egy Azure-erőforráscsoporton belül kell létrehoznia. Az [Azure Resource Manager](../resource-group-overview.md) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként használja. Az alkalmazás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti.  

    Az erőforráscsoportok felsorolása az előfizetésben:
    
        Get-AzureRmResourceGroup
    
    Új erőforráscsoport létrehozása:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **A Data Lake Analytics-fiók neve**
- **Hely:** az egyik, a Data Lake Analytics szolgáltatást támogató Azure-adatközpont.
- **Alapértelmezett Data Lake-fiók:** minden Data Lake Analytics-fiókhoz tartozik egy alapértelmezett Data Lake-fiók.

    Új Data Lake-fiók létrehozása:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] A Data Lake-fiók neve csak kisbetűket és számokat tartalmazhat.



**Data Lake Analytics-fiók létrehozása**

1. Nyissa meg a PowerShell ISE-t a Windows-munkaállomásról.
2. Futtassa a következő parancsfájlt:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##Adatok feltöltése a Data Lake-be

Az oktatóanyag során keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban. 

A rendszer átmásolt egy mintául szolgáló keresési naplófájlt a nyilvános Azure Blob-tárolóba. Az alábbi PowerShell-parancsfájllal letöltheti a fájlt a munkaállomására, majd feltöltheti a fájlt a Data Lake Analytics-fiókja alapértelmezett Data Lake Store-fiókjába.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Az alábbi PowerShell-parancsfájl bemutatja, hogyan kérheti le a Data Lake Analytics-fiókjához tartozó alapértelmezett Data Lake Store nevét.


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

>[AZURE.NOTE] Az Azure Portal felhasználói felületet biztosít a mintaadatfájlok alapértelmezett Data Lake Store-fiókba való másolásához. Útmutatásért lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

A Data Lake Analytics az Azure Blob Storage-hoz is rendelkezik hozzáféréssel.  A fájlok az Azure Blob Storage-ba történő feltöltéséhez lásd: [Using Azure PowerShell with Azure Storage](../storage/storage-powershell-guide-full.md) (Az Azure PowerShell és az Azure Storage használata).

##Data Lake Analytics-feladatok küldése

A Data Lake Analytics-feladatok nyelve a U-SQL. További információk a U-SQL-ről: [U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) és [U-SQL nyelvi referencia](http://go.microsoft.com/fwlink/?LinkId=691348).

**Data Lake Analytics-feladatparancsfájl létrehozása**

- Hozzon létre egy szövegfájlt az alábbi U-SQL-parancsfájl alapján, és mentse a szövegfájlt a munkaállomáson:

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

    >[AZURE.NOTE] A nyilvános blobokkal vagy a nyilvános tárolókhoz hozzáférési jogosultságokkal rendelkező Azure Blob-tárolók használata jelenleg nem támogatott.    
    
    
**Feladat küldése**

1. Nyissa meg a PowerShell ISE-t a Windows-munkaállomásról.
2. Futtassa a következő parancsfájlt:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
                        
        While (($t = Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId).State -ne "Ended"){
            Write-Host "Job status: "$t.State"..."
            Start-Sleep -seconds 5
        }
        
        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    A parancsfájlban a U-SQL-parancsfájl helye: c:\tutorials\data-lake-analytics\copyFile.usql. Frissítse a fájl elérési útját ennek megfelelően.
 
A feladat befejezése után az alábbi parancsmagok segítségével listázhatja ki a fájlt, és letöltheti a fájlt:
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## Lásd még:

- Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
- Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
- Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
- A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
- Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure portállal).
- A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).




<!--HONumber=sep16_HO1-->


