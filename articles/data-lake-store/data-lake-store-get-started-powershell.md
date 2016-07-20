<properties
   pageTitle="Bevezetés a Data Lake Store használatába | Azure"
   description="Data Lake Store-fiók létrehozása és alapszintű műveletek végrehajtása az Azure PowerShell használatával"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Az Azure Data Lake Store használatának első lépései az Azure PowerShell használatával

> [AZURE.SELECTOR]
- [Portál](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Ismerje meg, hogyan hozhat létre Azure Data Lake Store-fiókot az Azure PowerShell használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake Store-ról: [Overview of Data Lake Store](data-lake-store-overview.md) (A Data Lake Store áttekintése).

## Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
- **Az Azure-előfizetés engedélyezése** a Data Lake Store nyilvános előzetes verziójához. Lásd az [utasításokat](data-lake-store-get-started-portal.md#signup).


##Az Azure PowerShell 1.0-s vagy újabb verziójának telepítése

Tekintse meg a [Using Azure PowerShell with Azure Storage](../powershell-azure-resource-manager.md#prerequisites) (Az Azure PowerShell és az Azure Resource Manager együttes használata) című cikk Előfeltételek szakaszát.

## Azure Data Lake Store-fiók létrehozása

1. Nyisson meg egy új Azure PowerShell ablakot, és adja meg az alábbi kódrészletet az Azure-fiókba való bejelentkezéshez, az előfizetés beállításához és a Data Lake Store-szolgáltató regisztrálásához. Győződjön meg arról, hogy az előfizetés rendszergazdájaként/tulajdonosaként jelentkezik be, amikor a rendszer erre kéri:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Az Azure Data Lake Store-fiókok egy Azure-erőforráscsoporthoz vannak társítva. Először hozzon létre egy Azure-erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure-erőforráscsoport létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")

2. Hozzon létre egy Azure Data Lake Store-fiókot. A megadott név csak kisbetűket és számokat tartalmazhat.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake Store-fiók létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")

3. Ellenőrizze, hogy a fiók létrehozása sikeres volt-e.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    A kimenet értéke **True** (Igaz) kell, hogy legyen.

## Könyvtárstruktúrák létrehozása az Azure Data Lake Store-ban

Az Azure Data Lake Store-fiókjában könyvtárakat hozhat létre az adatok kezelésére és tárolására.

1. Adjon meg egy gyökérkönyvtárat.

        $myrootdir = "/"

2. Hozzon létre egy új könyvtárat **mynewdirectory** néven a megadott gyökérkönyvtárban.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Ellenőrizze, hogy az új könyvtár létrehozása sikeres volt-e.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![A könyvtár ellenőrzése](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")


## Fájlok feltöltése az Azure Data Lake Store-ba

Az adatait feltöltheti közvetlenül a Data Lake Store-ba gyökérszinten, vagy a fiókon belül létrehozott könyvtárba. Az alábbi részletek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott könyvtárba (**mynewdirectory**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## A Data Lake Store-ban lévő adatok átnevezése, letöltése és törlése

Fájlok átnevezéséhez használja a következő parancsot:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Fájlok letöltéséhez használja a következő parancsot:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Fájlok törléséhez használja a következő parancsot:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Ha a rendszer rákérdez, írja be az **Y** karaktert az elem törléséhez. Ha több fájlt kíván törölni, megadhatja az összes elérési utat, vesszővel elválasztva.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Az Azure Data Lake Store-fiók törlése

Az alábbi parancs segítségével törölheti a Data Lake Store-fiókját.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.


## További lépések

- [Secure data in Data Lake Store (Az adatok védelme a Data Lake Store-ban)](data-lake-store-secure-data.md)
- [Use Azure Data Lake Analytics with Data Lake Store (Az Azure Data Lake Analytics használata a Data Lake Store-ral)](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store (Az Azure HDInsight használata a Data Lake Store-ral)](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


