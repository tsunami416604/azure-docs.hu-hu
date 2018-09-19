---
title: Ismerkedés az Azure Data Lake Storage Gen1 PowerShell-lel |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 fiók létrehozása és alapszintű műveletek végrehajtása az Azure PowerShell használatával
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 3bdbc7066a0a87a58c284c3824ee1713f41e40e0
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126394"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Ismerkedés az Azure Data Lake Storage Gen1 Azure PowerShell-lel
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Ismerje meg, hogyan használhatja az Azure Powershellt Azure Data Lake Storage Gen1 fiók létrehozása és alapszintű műveleteket, mint például mappák létrehozása, és feltöltése adatfájlok le, a fiók törlése stb. További információ a Data Lake Storage Gen1: [a Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="authentication"></a>Hitelesítés
Ez a cikk egy egyszerűbb hitelesítési módszert használ a Data Lake Storage Gen1 ahol kéri, adja meg az Azure-fiók hitelesítő adatait. A Data Lake Storage Gen1 fiók- és rendszer a bejelentkezett felhasználó hozzáférési szintjét, majd szolgáltatás hozzáférési szintet. Előfordulhatnak azonban olyan egyéb megközelítések is lehet hitelesíteni a Data Lake Storage Gen1, amelyek **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatban a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](data-lake-store-authenticate-using-active-directory.md) című témakörben talál útmutatást és további tudnivalókat.

## <a name="create-a-data-lake-storage-gen1-account"></a>Hozzon létre egy Data Lake Storage Gen1 fiókot
1. Nyisson meg egy új Windows PowerShell-ablakot az asztalon. Adja meg a következő kódrészletet az Azure-fiókjába való bejelentkezéshez, az előfizetés beállításához és a Data Lake Storage Gen1-szolgáltató regisztrálásához. Amikor a rendszer kéri, jelentkezzen be, győződjön meg arról, hogy a rendszergazdák/tulajdonossal egyik jelentkezzen be:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Egy Data Lake Storage Gen1 fiókkal társítva az Azure-erőforráscsoport. Először hozzon létre egy Azure-erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure-erőforráscsoport létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure-erőforráscsoport létrehozása")
3. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott név csak kisbetűket és számokat tartalmazhat.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Hozzon létre egy Data Lake Storage Gen1 fiókot](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "hozzon létre egy Data Lake Storage Gen1 fiókot")
4. Ellenőrizze, hogy a fiók létrehozása sikeres volt-e.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    A parancsmag kimeneti értéke **True** (Igaz) kell, hogy legyen.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Könyvtárstruktúrák létrehozása a Data Lake Storage Gen1 fiók
Könyvtárak a Data Lake Storage Gen1-fiókjában adatok kezelésére és tárolására is létrehozhat.

1. Adjon meg egy gyökérkönyvtárat.

        $myrootdir = "/"
2. Hozzon létre egy új könyvtárat **mynewdirectory** néven a megadott gyökérkönyvtárban.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Ellenőrizze, hogy az új könyvtár létrehozása sikeres volt-e.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    A következő képernyőképen láthatóhoz hasonló kimenetnek kell megjelennie:

    ![A könyvtár ellenőrzése](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "A könyvtár ellenőrzése")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Adatok feltöltése a Data Lake Storage Gen1 fiókba
Feltöltheti az adatokat a Data Lake Storage Gen1 közvetlenül, gyökérszinten, vagy a fiókon belül létrehozott könyvtárba. A jelen szakaszban szereplő kódrészletek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott könyvtárba (**mynewdirectory**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Átnevezése, letöltése és a Data Lake Storage Gen1 fiókból adatok törlése
Fájlok átnevezéséhez használja a következő parancsot:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Fájlok letöltéséhez használja a következő parancsot:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Fájlok törléséhez használja a következő parancsot:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Ha a rendszer rákérdez, írja be az **Y** karaktert az elem törléséhez. Ha több fájlt kíván törölni, megadhatja az összes elérési utat, vesszővel elválasztva.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>A Data Lake Storage Gen1 fiók törlése
Az alábbi parancs segítségével törölheti a Data Lake Storage Gen1 fiók.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>További lépések
* [Teljesítmény-finomhangolási útmutató a PowerShell használata az Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
