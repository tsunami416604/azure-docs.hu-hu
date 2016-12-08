---
title: "A Data Lake Store használatának első lépései a platformfüggetlen parancssori felülettel | Microsoft Docs"
description: "Data Lake Store-fiók létrehozása és alapszintű műveletek végrehajtása az Azure platformfüggetlen parancssorával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/21/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: c157da7bf53e2d0762624e8e71e56e956db04a24
ms.openlocfilehash: 236563a8814640391130ba53886c5cebfea67a8f


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Az Azure Data Lake Store használatának első lépései az Azure parancssori felülettel
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

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Data Lake Store-fiókot az Azure parancssori felület használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake Store-ról: [Overview of Data Lake Store](data-lake-store-overview.md) (A Data Lake Store áttekintése).

Az Azure parancssori felület implementálva van a Node.js-ben. Használható bármilyen platformon, amely támogatja a Node.js-t, beleértve a Windows, Mac és Linux platformokat. Az Azure parancssori felület nyílt forráskódú. A forráskód felügyelete a GitHubon: <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Ez a cikk csak az Azure parancssori felület Data Lake Store-ral való használatát tárgyalja. Általános útmutató az Azure CLI használatáról: [Az Azure parancssori felület használata][azure-command-line-tools].

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure parancssori felület** – A telepítésre és konfigurálásra vonatkozó információkért lásd: [Install and configure the Azure CLI](../xplat-cli-install.md) (Az Azure parancssori felület telepítése és konfigurálása). Győződjön meg róla, hogy újraindította a számítógépét a parancssori felület telepítése után.

## <a name="authentication"></a>Authentication
Ez a cikk egy egyszerűbb hitelesítési módszert használ a Data Lake Store-ral, ahol Ön végfelhasználóként jelentkezik be. Ezután a Data Lake Store-fiókhoz és a fájlrendszerhez való hozzáférés szintje a bejelentkezett felhasználó hozzáférési szintjétől függ. Azonban a Data Lake Store-ral más módokon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatos útmutatást és további információkat a [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Hitelesítés a Data Lake Store-ral az Azure Active Directoryt használva).

## <a name="login-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe
1. Kövesse a [Csatlakozás Azure-előfizetéshez az Azure parancssori felületéről (Azure CLI)](../xplat-cli-connect.md) című dokumentumban leírt lépéseket, és csatlakozzon az előfizetéséhez az `azure login` módszerrel.
2. Az `azure account list` paranccsal sorolja fel a fiókjához társított előfizetéseket.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    A fenti kimenetben jelenleg az **Azure-sub-1** van engedélyezve, és van egy másik előfizetés, az **Azure-sub-2**. 
3. Válassza ki a használni kívánt előfizetést. Ha az Azure-sub-2 előfizetés alapján szeretne dolgozni, használja az `azure account set` parancsot.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store-fiók létrehozása
Nyisson meg egy parancssor, rendszerhéjat vagy terminálalapú munkamenetet, és futtassa a következő parancsokat.

1. Váltson Azure Resource Manager módra az alábbi paranccsal:
   
        azure config mode arm
2. Hozzon létre egy új erőforráscsoportot. Az alábbi parancsban adja meg a használni kívánt paraméterértékeket.
   
        azure group create <resourceGroup> <location>
   
    Ha a hely neve tartalmaz szóközöket, használjon idézőjeleket. Például: „USA 2. keleti régiója”.
3. Hozza létre a Data Lake Store-fiókot.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Mappák létrehozása a Data Lake Store-fiókban
Mappákat hozhat létre az Azure Data Lake Store-fiókjában az adatok kezelése és tárolása céljából. Az alábbi parancs segítségével hozzon létre egy „mynewfolder” nevű mappát a Data Lake Store gyökérmappájában.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Példa:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Adatok feltöltése a Data Lake Store-ba
Az adatait feltöltheti közvetlenül a Data Lake Store-ba gyökérszinten, vagy egy, a fiókon belül létrehozott mappába. Az alábbi kódtöredékek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott mappába (**mynewfolder**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (például C:\sampledata)\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Példa:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Fájlok listázása a Data Lake Store-ban
Az alábbi parancs segítségével kilistázhatja a Data Lake Store-fiók fájljait.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Példa:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

A kimenet az alábbihoz hasonló lesz:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>A Data Lake Store-ban lévő adatok átnevezése, letöltése és törlése
* **Fájlok átnevezéséhez** használja az alábbi parancsot:
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    Példa:
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **Fájlok letöltéséhez** használja az alábbi parancsot: Ügyeljen arra, hogy a megadott cél elérési útja egy létező hely legyen.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    Példa:
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **Fájlok törléséhez** használja az alábbi parancsot:
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    Példa:
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    Ha a rendszer rákérdez, írja be az **Y** karaktert az elem törléséhez.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Mappa hozzáférés-vezérlési listájának megtekintése a Data Lake Store-ban
Az alábbi parancs segítségével megtekintheti egy Data Lake Store-mappa hozzáférés-vezérlési listáit. A jelenlegi kiadásban az hozzáférés-vezérlési listák csak a Data Lake Store gyökerében állíthatók be. Így az alábbi útvonal-paraméter mindig a gyökér (/) lesz.

    azure datalake store permissions show <dataLakeStoreName> <path>

Példa:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Data Lake Store-fiók törlése
Az alábbi parancs segítségével törölheti a Data Lake Store-fiókját.

    azure datalake store account delete <dataLakeStoreAccountName>

Példa:

    azure datalake store account delete mynewdatalakestore

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Nov16_HO4-->


