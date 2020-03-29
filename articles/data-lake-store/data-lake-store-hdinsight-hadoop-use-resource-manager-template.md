---
title: Azure-sablonok használatával hdinsightot hozhat létre az Azure Data Lake Storage Gen1 szolgáltatással | Microsoft dokumentumok
description: HDInsight-fürtök létrehozása és használata az Azure Data Lake Storage Gen1 használatával Azure Resource Manager-sablonok használatával
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161373"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>HDInsight-fürt létrehozása az Azure Data Lake Storage Gen1 használatával az Azure Resource Manager-sablon használatával
> [!div class="op_single_selector"]
> * [A portál használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell használata (alapértelmezett tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [A PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Az Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Megtudhatja, hogy az Azure PowerShell használatával miként konfigurálhat hdinsight-fürtöt az Azure Data Lake Storage Gen1 szolgáltatással **további tárhelyként.**

Támogatott fürttípusok esetén a Data Lake Storage Gen1 alapértelmezett tárolóként vagy további tárfiókként használható. Ha a Data Lake Storage Gen1-et további tárolóként használja, a fürtök alapértelmezett tárfiókja továbbra is az Azure Storage Blobs (WASB) lesz, és a fürthöz kapcsolódó fájlok (például naplók stb.) továbbra is az alapértelmezett tárolóba kerülnek, míg a kívánt adatok folyamat tárolható a Data Lake Storage Gen1 fiókban. A Data Lake Storage Gen1 használata további tárfiókként nem befolyásolja a teljesítményt, és nem képes írni/olvasni a fürtből a tárolóba.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>A Data Lake Storage Gen1 használata a HDInsight-fürttároláshoz

Az alábbiakban a HDInsight data lake storage gen1 szolgáltatással való használatának néhány fontos szempontját olvashatja:

* A HDInsight-fürtök létrehozása a Data Lake Storage Gen1 alapértelmezett tárolóként a HDInsight 3.5-ös és 3.6-os verziójához érhető el.

* HdInsight-fürtök et hozhat létre a Data Lake Storage Gen1 szolgáltatáshoz való hozzáféréssel, mivel további tárhely érhető el a HDInsight 3.2-es, 3.4-es, 3.5-ös és 3.6-os verziójához.

Ebben a cikkben egy Hadoop-fürtöt létesítünk a Data Lake Storage Gen1 további tárolóként. Ha a Data Lake Storage Gen1 alapértelmezett tárolóként való létrehozásáról a [HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással az Azure Portal használatával című](data-lake-store-hdinsight-hadoop-use-portal.md)témakörben olvashat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* **Az Azure Active Directory szolgáltatásnév .** Ebben az oktatóanyagban az egyszerű szolgáltatás létrehozása az Azure AD-ben. Azonban egy Azure AD-rendszergazdai kell lennie, hogy hozzon létre egy egyszerű szolgáltatás. Ha Ön Az Azure AD-rendszergazda, kihagyhatja ezt az előfeltételt, és folytassa az oktatóanyag.

    **Ha nem Azure AD-rendszergazda,** nem fogja tudni végrehajtani az egyszerű szolgáltatás létrehozásához szükséges lépéseket. Ebben az esetben az Azure AD-rendszergazdának először létre kell hoznia egy egyszerű szolgáltatásszolgáltatást, mielőtt hdinsight-fürtöt hozhat létre a Data Lake Storage Gen1 használatával. Emellett a szolgáltatásnév kell létrehozni egy tanúsítvány, ahogy azt a [Szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)című részen leírtak szerint.

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással
Az Erőforrás-kezelő sablon és a sablon használatának előfeltételei a GitHubon érhetők el [a HDInsight Linux-fürt telepítése kor az új Data Lake Storage Gen1 szolgáltatással.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) Kövesse az ezen a hivatkozáson található utasításokat egy HDInsight-fürt létrehozásához a Data Lake Storage Gen1 további tárolóként.

A fent említett hivatkozáson található utasítások hoz a PowerShell. Mielőtt elkezdené ezeket az utasításokat, győződjön meg róla, hogy jelentkezzen be az Azure-fiókjába. Az asztalról nyisson meg egy új Azure PowerShell-ablakot, és írja be a következő kódrészleteket. Amikor a rendszer kéri a bejelentkezést, jelentkezzen be az előfizetés egyik rendszergazdájaként/tulajdonosaként:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

A sablon a következő erőforrástípusokat telepíti:

* [Microsoft.DataLakeStore/fiókok](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/fürtök](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Mintaadatok feltöltése a Data Lake Storage Gen1 szolgáltatásba
Az Erőforrás-kezelő sablon létrehoz egy új Data Lake Storage Gen1 fiókot, és társítja azt a HDInsight-fürthöz. Most fel kell töltenie néhány mintaadatot a Data Lake Storage Gen1-be. Ezekre az adatokra később az oktatóanyag ban kell futtatni a feladatokat egy HDInsight-fürtből, amely a Data Lake Storage Gen1 fiók adataihoz fér hozzá. Az adatok feltöltésével kapcsolatos tudnivalókért olvassa el [a Fájl feltöltése a Data Lake Storage Gen1 fiókba .for](data-lake-store-get-started-portal.md#uploaddata) Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

## <a name="set-relevant-acls-on-the-sample-data"></a>A megfelelő ACL-ek beállítása a mintaadatokon
Győződjön meg arról, hogy a minta adatok feltöltött elérhető a HDInsight-fürt, győződjön meg arról, hogy az Azure AD-alkalmazás, amely a HDInsight-fürt és a Data Lake Storage Gen1 közötti identitás létrehozásához használt hozzáférést biztosít a fájl/mappa próbál hozzáférni. Ehhez hajtsa végre az alábbi lépéseket.

1. Keresse meg a HDInsight-fürthöz társított Azure AD-alkalmazás nevét és a Data Lake Storage Gen1-fiókot. A név megkeresésének egyik módja az Erőforrás-kezelő sablon nal létrehozott HDInsight-fürtpanel megnyitása, a **Fürt AAD-identitás** fülre kattintva, és megkeresheti az **egyszerű szolgáltatás megjelenítési nevének**értékét.
2. Most hozzáférést biztosít az Azure AD-alkalmazás a fájl/mappa, amely a HDInsight-fürtről elérni kívánt. A Data Lake Storage Gen1 fájljában/mappájában a megfelelő ACL-ek beállításához olvassa el [az Adatok védelme a Data Lake Storage Gen1 alkalmazásban című témakört.](data-lake-store-secure-data.md#filepermissions)

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Tesztelési feladatok futtatása a HDInsight-fürtön a Data Lake Storage Gen1 használatához
Miután konfigurált egy HDInsight-fürtöt, futtathat tesztfeladatokat a fürtön annak teszteléséhez, hogy a HDInsight-fürt hozzáférhet-e a Data Lake Storage Gen1 szolgáltatáshoz. Ehhez egy minta Hive-feladatot futtatunk, amely létrehoz egy táblát a Data Lake Storage Gen1-fiókjába korábban feltöltött mintaadatok használatával.

Ebben a szakaszban lesz SSH egy HDInsight Linux-fürt, és futtassa a minta Hive-lekérdezést. Ha Windows-ügyfelet használ, javasoljuk a **PuTTY**használatát, [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)amely letölthető a alkalmazásból.

A PuTTY használatával kapcsolatos további tudnivalókért olvassa el [az SSH használata Linux alapú Hadoop használatával a WINDOWS HDInsight szolgáltatásában című témakört.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)

1. A csatlakozás után indítsa el a Hive CLI-t a következő paranccsal:

   ```
   hive
   ```
2. A CLI használatával adja meg a következő állításokat egy **járművek** nevű új tábla létrehozásához a Data Lake Storage Gen1 mintaadatainak használatával:

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


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Hozzáférés a Data Lake Storage Gen1 szolgáltatáshoz HDFS-parancsokkal
Miután konfigurálta a HDInsight-fürtet a Data Lake Storage Gen1 használatára, a HDFS rendszerhéj-parancsokkal elérheti az áruházat.

Ebben a szakaszban lesz SSH egy HDInsight Linux-fürt, és futtassa a HDFS-parancsokat. Ha Windows-ügyfelet használ, javasoljuk a **PuTTY**használatát, [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)amely letölthető a alkalmazásból.

A PuTTY használatával kapcsolatos további tudnivalókért olvassa el [az SSH használata Linux alapú Hadoop használatával a WINDOWS HDInsight szolgáltatásában című témakört.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)

A csatlakozás után a következő HDFS fájlrendszer paranccsal sorolja fel a fájlokat a Data Lake Storage Gen1 fiókban.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Ez tartalmazza a Data Lake Storage Gen1-nek korábban feltöltött fájlt.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

A paranccsal `hdfs dfs -put` is feltölthet néhány fájlt a Data `hdfs dfs -ls` Lake Storage Gen1 szolgáltatásba, majd ellenőrizheti, hogy a fájlok feltöltése sikeresen megtörtént-e.


## <a name="next-steps"></a>További lépések
* [Adatok másolása az Azure Storage Blobs szolgáltatásból a Data Lake Storage Gen1 szolgáltatásba](data-lake-store-copy-data-wasb-distcp.md)
* [A Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
