---
title: Az Azure-sablonok használata a HDInsight létrehozása az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Használja az Azure Resource Manager-sablonok létrehozása és használata HDInsight-fürtök az Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 5e8aa310d518ff26d2d0224797b2feb0745896d1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864906"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Hozzon létre egy HDInsight-fürtöt az Azure Data Lake Storage Gen1 Azure Resource Manager-sablon használatával
> [!div class="op_single_selector"]
> * [A Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [(Az alapértelmezett tároló) a PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [(A további tárhely) PowerShell-lel](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager használatával](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell használata egy HDInsight-fürt konfigurálása az Azure Data Lake Storage Gen1 **kiegészítő tárolóként**.

Támogatott fürttípusok Data Lake Storage Gen1, egy alapértelmezett tároló további tárfiókot is használható. Data Lake Storage Gen1 használata kiegészítő tárolóként, az alapértelmezett tárfiókot, a fürt továbbra is az Azure Storage Blobs (WASB) és az alapértelmezett tárolóba, míg az adatok kívánt továbbra is írja a fürttel kapcsolatos fájlok (például naplók, stb.) folyamatot a Data Lake Storage Gen1 fiókban tárolhatók. Data Lake Storage Gen1 használja, mint egy tárfiókot funkciója nem érinti a teljesítmény vagy a az olvasási/írási a tárolóhoz a fürtből.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight-fürt Storage használatával a Data Lake Storage Gen1

Az alábbiakban néhány HDInsight használata a Data Lake Storage Gen1 fontos szempontjai:

* Lehetőség a HDInsight-fürtök létrehozása a Data Lake Storage Gen1 hozzáféréssel rendelkező, a HDInsight 3.5-ös és 3.6-os verzió érhető el az alapértelmezett tároló.

* HDInsight-fürtök létrehozása a Data Lake Storage Gen1 hozzáférést, ha további tárhely elérhető HDInsight-verziók 3.2-es, 3.4-es, 3.5-ös és 3.6-os lehetőség.

Ez a cikk a Data Lake Storage Gen1 Hadoop-fürt kiegészítő tárolóként kiépítve. A Hadoop-fürt létrehozása a Data Lake Storage Gen1 alapértelmezett tárolóként útmutatásért lásd: [egy HDInsight-fürt létrehozása a Data Lake Storage Gen1 az Azure Portal használatával](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* **Az Azure Active Directory egyszerű szolgáltatás**. Ebben az oktatóanyagban lépések nyújtanak segítséget az egyszerű szolgáltatás létrehozása az Azure ad-ben. Azonban az Azure AD-rendszergazda létrehozhat egy egyszerű szolgáltatást kell lennie. Ha egy Azure AD-rendszergazdát, kihagyhatja ezt az előfeltételt, és folytassa az oktatóanyagot.

    **Ha nem Azure AD-rendszergazda**, nem fogjuk tudni elvégezni az egyszerű szolgáltatás létrehozásához szükséges lépéseket. Ebben az esetben az Azure AD-rendszergazda először létre kell hoznia egy egyszerű szolgáltatást a Data Lake Storage Gen1 egy HDInsight-fürt létrehozása előtt. Emellett az egyszerű szolgáltatás segítségével kell létrehozni egy tanúsítványt, ismertetett módon [egyszerű szolgáltatás létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Egy HDInsight-fürt létrehozása a Data Lake Storage Gen1
A Resource Manager-sablont, és a sablonok használatára vonatkozó Előfeltételek a githubon érhető el [az új Data Lake Storage Gen1 HDInsight Linux-fürt üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Kövesse az utasításokat, adja meg ezt a hivatkozást egy HDInsight-fürt létrehozása a Data Lake Storage Gen1 további tárolóként.

A fent említett hivatkozás webhelyen található utasításokat a PowerShell szükséges. Ezeket az utasításokat a Kezdés előtt győződjön meg arról, az Azure-fiókjába való bejelentkezést. Az asztalon nyissa meg egy új Azure PowerShell-ablakot, és adja meg az alábbi kódrészletek. Amikor a rendszer kéri, jelentkezzen be, győződjön meg arról, hogy a rendszergazdák/tulajdonossal egyik jelentkezzen be:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

A sablon üzembe helyezi ezeket erőforrástípusok:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Mintaadatok feltöltése a Data Lake Storage Gen1
A Resource Manager-sablon egy új Data Lake Storage Gen1 fiókot hoz létre, és hozzárendeli a HDInsight-fürt. A Data Lake Storage Gen1 most tölthet fel néhány adatot. Ezeket az adatokat egy HDInsight-fürtöt, amely a Data Lake Storage Gen1 fiókban lévő adatok eléréséhez a feladatok végrehajtásához az oktatóanyag későbbi részében lesz szüksége. Adatok feltöltése az utasításokért lásd: [fájl feltöltése a Data Lake Storage Gen1 fiók](data-lake-store-get-started-portal.md#uploaddata). Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

## <a name="set-relevant-acls-on-the-sample-data"></a>A mintaadatokat a megfelelő ACL-ek beállítása
Ahhoz, hogy a HDInsight-fürtön elérhető-e a mintaadatok feltöltése, biztosítania kell, hogy az Azure AD-alkalmazást, amely segítségével hozhat létre az identitás a HDInsight-fürt és a Data Lake Storage Gen1 közötti férhet hozzá a kívánt fájl vagy mappa a hozzáférés. Ehhez hajtsa végre az alábbi lépéseket.

1. Keresse meg az Azure AD-alkalmazást, amely a HDInsight-fürt és a Data Lake Storage Gen1 fiók van hozzárendelve. Kattintson az egyik módja a nevét, hogy nyissa meg a HDInsight-fürt panelén, a Resource Manager-sablon használatával létrehozott keressen a **fürt AAD-identitásával** lapra, és nézze meg a **egyszerű szolgáltatás megjelenített neve**.
2. Most adja meg az Azure AD-alkalmazáshoz való hozzáférést, amely a HDInsight-fürtből származó elérni kívánt fájl vagy mappa a. Tekintse meg a megfelelő hozzáférés-vezérlési listák beállítása a fájl vagy mappa a Data Lake Storage Gen1, [az adatok védelme az Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>A HDInsight-fürt használata a Data Lake Storage Gen1 tesztelési feladatok futtatása
Miután konfigurálta egy HDInsight-fürtöt, a fürtön, hogy a HDInsight-fürt hozzáférhet-e a Data Lake Storage Gen1 teszteléséhez tesztelési feladatok futtatásához. Ehhez egy minta Hive-feladatot, amely létrehoz egy táblát a mintaadatokkal, amelyek korábban a Data Lake Storage Gen1 fiókjába feltöltött fog Futtatás.

Ez a szakasz tartalma SSH be egy HDInsight Linux-fürt, és futtassa a mintát Hive-lekérdezést. Ha egy Windows-ügyfelet használ, javasoljuk **PuTTY**, amely letölthető a [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

A PuTTY használatával kapcsolatos további információkért lásd: [az SSH használata a Linux-alapú Hadooppal a HDInsight, a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Ha csatlakoztatva van, indítsa el a Hive-CLI az alábbi paranccsal:

   ```
   hive
   ```
2. A parancssori felületről, adja meg az alábbi utasításokat, hozzon létre egy új táblát nevű **járművek** a mintaadatok használatával a Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   A következőhöz hasonló kimenetnek kell megjelennie:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Data Lake Storage Gen1 eléréséhez HDFS-parancsok használatával
Miután konfigurálta a HDInsight-fürt használata a Data Lake Storage Gen1, a HDFS-rendszerhéjparancsokat használhatja az áruház.

Ez a szakasz tartalma SSH be egy HDInsight Linux-fürt, és futtassa a HDFS-parancsokat. Ha egy Windows-ügyfelet használ, javasoljuk **PuTTY**, amely letölthető a [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

A PuTTY használatával kapcsolatos további információkért lásd: [az SSH használata a Linux-alapú Hadooppal a HDInsight, a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

A csatlakozás után a következő HDFS-fájlrendszer parancs segítségével listázza a Data Lake Storage Gen1 fiókban lévő fájlokat.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Megjelenik a Data Lake Storage Gen1 korábban feltöltött fájl.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Is használhatja a `hdfs dfs -put` parancsot néhány fájlok feltöltése a Data Lake Storage Gen1, és hogyan `hdfs dfs -ls` ellenőrizze, hogy a fájl sikeresen feltöltve.


## <a name="next-steps"></a>További lépések
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Az Azure HDInsight-fürtök használata a Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
