---
title: Ismerkedés az Azure Data Lake Storage Gen1 használatával – PowerShell | Microsoft dokumentumok
description: Azure PowerShell használatával azure Data Lake Storage Gen1-fiókot hozhat létre, és alapvető műveleteket hajthat végre.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 42ddab6991b418af3e41da9966cdab69ded87461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837889"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Ismerkedés az Azure Data Lake Storage Gen1 használatával az Azure PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Megtudhatja, hogy az Azure PowerShell használatával hogyan hozhat létre azure Data Lake Storage Gen1-fiókot, és hogyan hajthat végre olyan alapvető műveleteket, mint például mappák létrehozása, adatfájlok feltöltése és letöltése, fiók törlése stb. A Data Lake Storage Gen1 szolgáltatásról a [Data Lake Storage Gen1 áttekintése című](data-lake-store-overview.md)témakörben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="authentication"></a>Hitelesítés

Ez a cikk egy egyszerűbb hitelesítési megközelítést használ a Data Lake Storage Gen1 használatával, ahol a rendszer kéri az Azure-fiók hitelesítő adatainak megadását. A Data Lake Storage Gen1 fiókhoz és a fájlrendszerhez való hozzáférési szintet ezután a bejelentkezett felhasználó hozzáférési szintje szabályozza. Vannak azonban más megközelítések a Data Lake Storage Gen1 hitelesítésére, amelyek a végfelhasználói hitelesítés vagy a szolgáltatás-szolgáltatás hitelesítés. A hitelesítéssel kapcsolatban a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](data-lake-store-authenticate-using-active-directory.md) című témakörben talál útmutatást és további tudnivalókat.

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása

1. Nyisson meg egy új Windows PowerShell-ablakot az asztalon. Adja meg a következő kódrészletet az Azure-fiókjába való bejelentkezéshez, állítsa be az előfizetést, és regisztrálja a Data Lake Storage Gen1 szolgáltatót. Amikor a rendszer kéri a bejelentkezést, jelentkezzen be az előfizetés egyik rendszergazdájaként/tulajdonosaként:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. A Data Lake Storage Gen1 fiók egy Azure-erőforráscsoport társított. Először hozzon létre egy erőforráscsoportot.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Azure-erőforráscsoport létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure-erőforráscsoport létrehozása")

1. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott név csak kisbetűket és számokat tartalmazhat.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Data Lake Storage Gen1-fiók létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Data Lake Storage Gen1-fiók létrehozása")

1. Ellenőrizze, hogy a fiók létrehozása sikeres volt-e.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    A parancsmag kimeneti értéke **True** (Igaz) kell, hogy legyen.

## <a name="create-directory-structures"></a>Könyvtárstruktúrák létrehozása

Könyvtárakat hozhat létre a Data Lake Storage Gen1 fiók ban az adatok kezeléséhez és tárolásához.

1. Adjon meg egy gyökérkönyvtárat.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Hozzon létre egy új könyvtárat **mynewdirectory** néven a megadott gyökérkönyvtárban.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Ellenőrizze, hogy az új könyvtár létrehozása sikeres volt-e.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    A következő képernyőképen láthatóhoz hasonló kimenetnek kell megjelennie:

    ![A könyvtár ellenőrzése](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "A könyvtár ellenőrzése")

## <a name="upload-data"></a>Adatok feltöltése

Az adatokat közvetlenül a gyökérszinten, vagy a fiókon belül létrehozott könyvtárba töltheti fel a Data Lake Storage Gen1-be. A jelen szakaszban szereplő kódrészletek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott könyvtárba (**mynewdirectory**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Adatok átnevezése, letöltése és törlése

Fájlok átnevezéséhez használja a következő parancsot:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Fájlok letöltéséhez használja a következő parancsot:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Fájlok törléséhez használja a következő parancsot:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Ha a rendszer rákérdez, írja be az **Y** karaktert az elem törléséhez. Ha több fájlt kíván törölni, megadhatja az összes elérési utat, vesszővel elválasztva.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Fiók törlése

A következő paranccsal törölheti a Data Lake Storage Gen1 fiókját.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>További lépések

* [Teljesítményhangolási útmutató a PowerShell azure Data Lake Storage Gen1 használatával kapcsolatban](data-lake-store-performance-tuning-powershell.md)
* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)
