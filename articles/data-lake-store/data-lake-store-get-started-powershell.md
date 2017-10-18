---
title: "Az Azure Data Lake Store használatának első lépései PowerShell használatával | Microsoft Docs"
description: "Data Lake Store-fiók létrehozása és alapszintű műveletek végrehajtása az Azure PowerShell használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 3428c3852f156df850f4b9e8833d15f4ae32729d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Az Azure Data Lake Store használatának első lépései az Azure PowerShell használatával
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

Ismerje meg, hogyan hozhat létre Azure Data Lake Store-fiókot az Azure PowerShell használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake Store-ról: [Overview of Data Lake Store](data-lake-store-overview.md) (A Data Lake Store áttekintése).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="authentication"></a>Authentication
Ez a cikk egy egyszerűbb, a Data Lake Store-ral történő hitelesítési módszert használ, ahol meg kell adnia az Azure-fiókjához tartozó hitelesítő adatokat. Ezután a Data Lake Store-fiókhoz és a fájlrendszerhez való hozzáférés szintje a bejelentkezett felhasználó hozzáférési szintjétől függ. Azonban a Data Lake Store-ral más módokon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. Útmutatás a hitelesítéshez és további tudnivalók a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](data-lake-store-authenticate-using-active-directory.md) című témakörben.

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store-fiók létrehozása
1. Nyisson meg egy új Windows PowerShell-ablakot az asztalon. Írja be az alábbi kódrészletet az Azure-fiókba való bejelentkezéshez, az előfizetés beállításához és a Data Lake Store-szolgáltató regisztrálásához. Győződjön meg arról, hogy az előfizetés rendszergazdájaként/tulajdonosaként jelentkezik be, amikor a rendszer erre kéri:

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

    ![Azure-erőforráscsoport létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure-erőforráscsoport létrehozása")
3. Hozzon létre egy Azure Data Lake Store-fiókot. A megadott név csak kisbetűket és számokat tartalmazhat.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake Store-fiók létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Azure Data Lake Store-fiók létrehozása")
4. Ellenőrizze, hogy a fiók létrehozása sikeres volt-e.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    A parancsmag kimeneti értéke **True** (Igaz) kell, hogy legyen.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Könyvtárstruktúrák létrehozása az Azure Data Lake Store-ban
Az Azure Data Lake Store-fiókjában könyvtárakat hozhat létre az adatok kezelésére és tárolására.

1. Adjon meg egy gyökérkönyvtárat.

        $myrootdir = "/"
2. Hozzon létre egy új könyvtárat **mynewdirectory** néven a megadott gyökérkönyvtárban.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Ellenőrizze, hogy az új könyvtár létrehozása sikeres volt-e.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    A következő képernyőképen láthatóhoz hasonló kimenetnek kell megjelennie:

    ![A könyvtár ellenőrzése](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "A könyvtár ellenőrzése")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Fájlok feltöltése az Azure Data Lake Store-ba
Az adatait feltöltheti közvetlenül a Data Lake Store-ba gyökérszinten, vagy a fiókon belül létrehozott könyvtárba. A jelen szakaszban szereplő kódrészletek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott könyvtárba (**mynewdirectory**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>A Data Lake Store-ban lévő adatok átnevezése, letöltése és törlése
Fájlok átnevezéséhez használja a következő parancsot:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Fájlok letöltéséhez használja a következő parancsot:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Fájlok törléséhez használja a következő parancsot:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Ha a rendszer rákérdez, írja be az **Y** karaktert az elem törléséhez. Ha több fájlt kíván törölni, megadhatja az összes elérési utat, vesszővel elválasztva.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Az Azure Data Lake Store-fiók törlése
Az alábbi parancs segítségével törölheti a Data Lake Store-fiókját.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>Következő lépések
* [Teljesítmény-finomhangolási útmutató az Azure Data Lake Store-ral használt PowerShellhez](data-lake-store-performance-tuning-powershell.md)
* [Az Azure Data Lake Store használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)