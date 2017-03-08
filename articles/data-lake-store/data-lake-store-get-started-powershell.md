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
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: ebe8724d73769eb55e40b8af2056880a5a4007ce
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Az Azure Data Lake Store használatának első lépései az Azure PowerShell használatával
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ismerje meg, hogyan hozhat létre Azure Data Lake Store-fiókot az Azure PowerShell használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake Store-ról: [Overview of Data Lake Store](data-lake-store-overview.md) (A Data Lake Store áttekintése).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="authentication"></a>Authentication
Ez a cikk egy egyszerűbb, a Data Lake Store-ral történő hitelesítési módszert használ, ahol meg kell adnia az Azure-fiókjához tartozó hitelesítő adatokat. Ezután a Data Lake Store-fiókhoz és a fájlrendszerhez való hozzáférés szintje a bejelentkezett felhasználó hozzáférési szintjétől függ. Azonban a Data Lake Store-ral más módokon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatos útmutatást és további információkat a [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Hitelesítés a Data Lake Store-ral az Azure Active Directoryt használva).

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store-fiók létrehozása
1. Nyisson meg egy új Windows PowerShell ablakot, és adja meg az alábbi kódrészletet az Azure-fiókba való bejelentkezéshez, az előfizetés beállításához és a Data Lake Store-szolgáltató regisztrálásához. Győződjön meg arról, hogy az előfizetés rendszergazdájaként/tulajdonosaként jelentkezik be, amikor a rendszer erre kéri:

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

    A kimenet értéke **True** (Igaz) kell, hogy legyen.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Könyvtárstruktúrák létrehozása az Azure Data Lake Store-ban
Az Azure Data Lake Store-fiókjában könyvtárakat hozhat létre az adatok kezelésére és tárolására.

1. Adjon meg egy gyökérkönyvtárat.

        $myrootdir = "/"
2. Hozzon létre egy új könyvtárat **mynewdirectory** néven a megadott gyökérkönyvtárban.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Ellenőrizze, hogy az új könyvtár létrehozása sikeres volt-e.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![A könyvtár ellenőrzése](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "A könyvtár ellenőrzése")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Fájlok feltöltése az Azure Data Lake Store-ba
Az adatait feltöltheti közvetlenül a Data Lake Store-ba gyökérszinten, vagy a fiókon belül létrehozott könyvtárba. Az alábbi részletek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott könyvtárba (**mynewdirectory**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata)\.

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

## <a name="performance-guidance-while-using-powershell"></a>Teljesítménnyel kapcsolatos útmutató a PowerShell használata során

Alább azon legfontosabb beállítások láthatók, amelyek megfelelő hangolásával a legjobb teljesítmény érhető el, miközben a PowerShellt használja a Data Lake Store-ral való munkavégzés során:

| Tulajdonság            | Alapértelmezett | Leírás |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ez a paraméter lehetővé teszi a párhuzamos szálak számának megadását az egyes fájlok fel- vagy letöltéséhez. Ez a szám a fájlonként maximálisan lefoglalható szálakat jelenti, azonban a forgatókönyvtől függően előfordulhat, hogy kevesebb szálat kap (ha például, egy 1 KB-os fájlt tölt fel, akkor is csak egy szálat kap, ha 20-at kér).  |
| ConcurrentFileCount | 10      | Ez a paraméter kifejezetten a mappák fel- és letöltéséhez kapcsolódik. Ez a paraméter határozza meg az egyidejűleg fel- vagy letölthető fájlok számát. Ez a szám az adott időpontban egyidejűleg fel- vagy letölthető fájlok maximális számát jelenti, azonban a forgatókönyvtől függően előfordulhat, hogy kisebb egyidejűséget kap (ha például két fájlt tölt fel, akkor is csak két egyidejű fájlfeltöltést kap, ha 15-öt kér). |

**Példa**

Ezzel a paranccsal fájlokat tölthet le az Azure Data Lake Store-ból a felhasználó helyi lemezére fájlonként 20 szálat és 100 egyidejű fájlt használva.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>Hogyan határozhatom meg a paraméterek számára beállítandó értéket?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: A teljes szálszám meghatározása** – Kezdje a használni kívánt szálak teljes számának kiszámításával. Általánosságban 6 szálat használjon minden egyes fizikai maghoz.

        Total thread count = total physical cores * 6

    **Példa**

    Tételezzük fel, hogy egy 16 maggal rendelkező D14 VM-en futtatja a PowerShell-parancsokat.

        Total thread count = 16 cores * 6 = 96 threads


* **2. lépés: A PerFileThreadCount kiszámítása** – A PerFileThreadCount számítását fájlok mérete alapján végezzük. A 2,5 GB-nál kisebb fájlok esetén ezt a paramétert nem kell módosítani, mert az alapértelmezett érték (10) elegendő. A 2,5 GB-nál nagyobb fájlok esetén az első 2,5 GB-nál 10 szálat kell használnia alapként, és a fájlméret minden további 256 MB-os növekedése után 1 szálat kell hozzáadni. Ha olyan mappát másol, amelyben nagyon eltérő méretű fájlok találhatók, érdemes hasonló fájlméret alapján csoportosítani a fájlokat. Az eltérő fájlméretek az optimálisnál kisebb teljesítménnyel járhatnak. Ha nem lehetséges a hasonló fájlméret alapján történő csoportosítás, akkor a PerFileThreadCount értékét a legnagyobb fájlméret alapján kell beállítani.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Példa**

    Ha feltételezzük, hogy 100 fájllal rendelkezik, amelyek mérete 1 és 10 GB között változik, akkor a 10 GB-os fájlt, mint legnagyobb méretű fájlt használjuk a számításban, ami az alábbiakban látható.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **3. lépés: A ConcurrentFilecount kiszámítása** – Használja a teljes szálszámot és a PerFileThreadCount értékét a ConcurrentFileCount kiszámításához az alábbi egyenlet alapján.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Példa**

    Az eddig használt példaértékek alapján

        96 = 40 * ConcurrentFileCount

    Tehát a **ConcurrentFileCount** értéke **2,4**, amelyet kerekíthetünk **2**-re.

### <a name="further-tuning"></a>További hangolás

Elképzelhető, hogy további hangolásra lesz szüksége, mert különböző fájlméretekkel kell dolgoznia. A fenti számítás jól működik, ha az összes, vagy legalábbis a legtöbb fájl nagyobb méretű, és közelebb van a 10 GB-os mérethez. Ha ehelyett sok különböző, nagyon eltérő méretű fájllal rendelkezik, akkor csökkentheti a PerFileThreadCount értékét. A PerFileThreadCount értékének csökkentésével, növelhetjük a ConcurrentFileCount értékét. Ha tehát feltételezzük, hogy a legtöbb fájlunk kisebb, az 5 GB-os tartományba eső mérettel rendelkezik, akkor újra végrehajthatjuk a számítást:

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

A **ConcurrentFileCount** értéke így 96/20, ami 4,8-del egyenlő, kerekítve pedig **4** lesz.

A beállítások hangolását a **PerFileThreadCount** értékének növelésével vagy csökkentésével folytathatja a fájlméretek eloszlásától függően.

### <a name="limitation"></a>Korlátozás

* **A fájlok száma kisebb, mint a ConcurrentFileCount értéke**: Ha a feltölteni kívánt fájlok száma kisebb a **ConcurrentFileCount** kiszámított értékének, akkor csökkentse a **ConcurrentFileCount** értékét úgy, hogy az egyenlő legyen a fájlok számával. A fennmaradó szálakat a **PerFileThreadCount** értékének a növelésére használhatja.

* **Túl sok szál**: Ha túlságosan megnöveli a szálszámot a fürt méretének növelése nélkül, az rosszabb teljesítménnyel járhat. Versengési problémák adódhatnak, ha kontextusváltás történik a CPU-n.

* **Elégtelen egyidejűség**: Ha az egyidejűség nem elégséges, lehet, hogy túl kicsi a fürt. A fürtben növelheti a csomópontok számát, ami több egyidejűséget biztosít.

* **Szabályozási hibák**: Elképzelhető, hogy szabályozási hibákat tapasztal, ha az egyidejűség túl magas. Ha szabályozás hibák merülnek fel, csökkentse az egyidejűséget vagy lépjen kapcsolatba velünk.

## <a name="next-steps"></a>Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)


