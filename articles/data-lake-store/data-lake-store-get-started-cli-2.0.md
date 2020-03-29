---
title: Az Azure Data Lake Storage Gen1 használatának megkezdéséhez használja az Azure CLI-t | Microsoft dokumentumok
description: Az Azure CLI használatával hozzon létre egy Data Lake Storage Gen1 fiókot, és hajtson végre alapvető műveleteket
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60885347"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Ismerkedés az Azure Data Lake Store-val az Azure CLI használatával

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Ismerje meg, hogyan használhatja az Azure CLI-t egy Azure Data Lake Storage Gen1 fiók létrehozásához, és alapvető műveleteket hajthat végre, például mappákat hozhat létre, adatokat tölthet fel és tölthet le, törölheti fiókját stb. A Data Lake Storage Gen1 szolgáltatásról a [Data Lake Storage Gen1 áttekintése című](data-lake-store-overview.md)témakörben olvashat bővebben.

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. A szolgáltatás macOS, Linux és Windows rendszereken használható. További információt az [Azure CLI áttekintése](https://docs.microsoft.com/cli/azure)című témakörben talál. Az Azure Data [Lake Storage Gen1 CLI referencia](https://docs.microsoft.com/cli/azure/dls) a parancsok és szintaxis teljes listáját is megnézheti.


## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** – Az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) című témakörben talál utasításokat.

## <a name="authentication"></a>Hitelesítés

Ez a cikk egy egyszerűbb hitelesítési megközelítést használ a Data Lake Storage Gen1, ahol bejelentkezik, mint egy végfelhasználó. A Data Lake Storage Gen1 fiókhoz és a fájlrendszerhez való hozzáférési szintet ezután a bejelentkezett felhasználó hozzáférési szintje szabályozza. Vannak azonban más megközelítések is a Data Lake Storage Gen1 hitelesítéséhez, amelyek a **végfelhasználói hitelesítés** vagy **a szolgáltatás-szolgáltatás hitelesítés.** A hitelesítéssel kapcsolatban a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](data-lake-store-authenticate-using-active-directory.md) című témakörben talál útmutatást és további tudnivalókat.


## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

1. Jelentkezzen be az Azure-előfizetésébe.

    ```azurecli
    az login
    ```

    Kap egy kódot a következő lépésben való használatra. Egy webböngészőben nyissa meg a https://aka.ms/devicelogin oldalt, és gépelje be a kódot a hitelesítéshez. A rendszer kéri a hitelesítési adatokkal való bejelentkezést.

2. Bejelentkezés után az ablakban megjelenő listában találhatók a fiókhoz társított Azure-előfizetések. Az alábbi paranccsal használhat egy adott előfizetést.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 fiók létrehozása

1. Hozzon létre egy új erőforráscsoportot. Az alábbi parancsban adja meg a használni kívánt paraméterértékeket. Ha a hely neve tartalmaz szóközöket, használjon idézőjeleket. Például: „USA 2. keleti régiója”. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Hozza létre a Data Lake Storage Gen1 fiókot.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Mappák létrehozása Data Lake Storage Gen1 fiókban

Az Azure Data Lake Storage Gen1 fiók alatt mappákat hozhat létre az adatok kezeléséhez és tárolásához. A következő paranccsal hozzon létre egy **mynewfolder** nevű mappát a Data Lake Storage Gen1 fiók gyökerében.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> A `--folder` paraméter gondoskodik arról, hogy a parancs egy mappát hozzon létre. Ha ez a paraméter nincs jelen, a parancs létrehoz egy mynewfolder nevű üres fájlt a Data Lake Storage Gen1 fiók gyökerében.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Adatok feltöltése Data Lake Storage Gen1 fiókba

Az adatokat közvetlenül a gyökérszinten vagy a fiókon belül létrehozott mappába töltheti fel a Data Lake Storage Gen1-be. Az alábbi kódtöredékek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott mappába (**mynewfolder**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Célként adja meg a teljes elérési utat, beleértve a fájlnevet is.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Fájlok listázása Data Lake Storage Gen1 fiókban

A következő paranccsal a fájlokat egy Data Lake Storage Gen1 fiókban listázhat.

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

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Adatok átnevezése, letöltése és törlése Data Lake Storage Gen1 fiókból 

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

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Engedélyek és Hozzáférés-hozzáférés-hozzáférés-engedélyek kezelése Data Lake Storage Gen1 fiókhoz

Ebben a szakaszban megtudhatja, hogyan kezelheti az ACL-ek és engedélyek az Azure CLI használatával. Az ACL-ek Azure Data Lake Storage Gen1-ben való megvalósításának részletes témaköre: [Access control in Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

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
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 fiók törlése
A következő paranccsal törölheti a Data Lake Storage Gen1 fiókot.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)
