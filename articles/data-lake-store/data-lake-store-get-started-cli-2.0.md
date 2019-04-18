---
title: Az Azure CLI használatával az Azure Data Lake Storage Gen1 használatának első lépései |} A Microsoft Docs
description: Az Azure CLI használatával hozzon létre egy Data Lake Storage Gen1 fiókot, és alapszintű műveletek végrehajtása
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 9431cc7fa12b86371ce6b2325aca8e13d264442e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880576"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Azure CLI-vel az Azure Data Lake Store használatának első lépései

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Útmutató az Azure CLI használatával az Azure Data Lake Storage Gen1 fiók létrehozása és alapszintű műveleteket, mint például mappák létrehozása, és feltöltése adatfájlok le, a fiók törlése stb. További információ a Data Lake Storage Gen1: [a Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md).

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. A szolgáltatás macOS, Linux és Windows rendszereken használható. További információkért lásd: [áttekintése az Azure CLI](https://docs.microsoft.com/cli/azure). Ezenkívül megnézheti a [Azure Data Lake Storage Gen1 CLI referencia](https://docs.microsoft.com/cli/azure/dls) teljes listáját a parancsokat és szintaxist.


## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure CLI** – lásd: [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) útmutatást.

## <a name="authentication"></a>Authentication

Ez a cikk egy egyszerűbb hitelesítési módszert használ a Data Lake Storage Gen1 hol jelentkezik be végfelhasználóként. A hozzáférési szintet a Data Lake Storage Gen1 fiók és egy fájlrendszert, majd szolgáltatás a bejelentkezett felhasználó hozzáférési szintjét. Előfordulhatnak azonban olyan egyéb megközelítések is lehet hitelesíteni a Data Lake Storage Gen1, amelyek **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatban a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](data-lake-store-authenticate-using-active-directory.md) című témakörben talál útmutatást és további tudnivalókat.


## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

1. Jelentkezzen be az Azure-előfizetésébe.

    ```azurecli
    az login
    ```

    Kap egy kódot a következő lépésben való használatra. Nyissa meg a webböngésző használata https://aka.ms/devicelogin , és írja be a kódot a hitelesítéshez. A rendszer kéri a hitelesítési adatokkal való bejelentkezést.

2. Bejelentkezés után az ablakban megjelenő listában találhatók a fiókhoz társított Azure-előfizetések. Az alábbi paranccsal használhat egy adott előfizetést.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Az Azure Data Lake Storage Gen1 fiók létrehozása

1. Hozzon létre egy új erőforráscsoportot. Az alábbi parancsban adja meg a használni kívánt paraméterértékeket. Ha a hely neve tartalmaz szóközöket, használjon idézőjeleket. Például: „USA 2. keleti régiója”. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. A Data Lake Storage Gen1 fiók létrehozásához.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Mappák létrehozása a Data Lake Storage Gen1-fiókban

Az adatok kezelésére és tárolására az Azure Data Lake Storage Gen1-fiókjában mappákat hozhat létre. A következő paranccsal hozzon létre egy nevű **mynewfolder** a Data Lake Storage Gen1 fiók gyökérmappájában.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> A `--folder` paraméter gondoskodik arról, hogy a parancs egy mappát hozzon létre. Ha ez a paraméter nem található, a parancs létrehozza a Data Lake Storage Gen1 fiók gyökérmappájában mynewfolder nevű üres fájlt.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Adatok feltöltése a Data Lake Storage Gen1-fiókhoz

Feltöltheti az adatokat a Data Lake Storage Gen1 közvetlenül, gyökérszinten, vagy a fiókon belül létrehozott mappába. Az alábbi kódtöredékek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott mappába (**mynewfolder**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Célként adja meg a teljes elérési utat, beleértve a fájlnevet is.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Fájlok listázása a Data Lake Storage Gen1-fiókban

A következő paranccsal listázhatja a fájlok egy Data Lake Storage Gen1-fiókban.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

A kimenet az alábbihoz hasonló lesz:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Átnevezése, letöltése és adatot törölni egy Data Lake Storage Gen1 fiók 

* **Fájlok átnevezéséhez** használja az alábbi parancsot:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Fájlok letöltéséhez** használja az alábbi parancsot: Ügyeljen arra, hogy a megadott cél elérési útja egy létező hely legyen.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > A parancs létrehozza a célmappát, ha az nem létezik.
    > 
    >

* **Fájlok törléséhez** használja az alábbi parancsot:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Ha egyetlen paranccsal szeretné törölni a **mynewfolder** nevű mappát és a **vehicle1_09142014_copy.csv** nevű fájlt, használja a --recurse paramétert

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Egy Data Lake Storage Gen1 fiókhoz tartozó engedélyek és hozzáférés-vezérlési listák használata

Ebben a szakaszban, megtudhatja, hogyan kezelheti a hozzáférés-vezérlési listák és az engedélyek az Azure CLI használatával. A hozzáférés-vezérlési listák hogyan találhatók meg az Azure Data Lake Storage Gen1 részletes tárgyalását lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* **Egy fájl/mappa tulajdonosának frissítését** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Egy fájl/mappa engedélyeinek frissítését** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Adott elérési úthoz tartozó hozzáférés-vezérlési listák beolvasását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    A kimenet az alábbihoz hasonló lesz:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Egy hozzáférés-vezérlési listához tartozó bejegyzés beállítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Egy hozzáférés-vezérlési listához tartozó bejegyzés eltávolítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Egy alapértelmezett teljes hozzáférés-vezérlési lista eltávolítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Egy nem alapértelmezett teljes hozzáférés-vezérlési lista eltávolítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Törölheti a Data Lake Storage Gen1
A következő paranccsal törölheti a Data Lake Storage Gen1.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
