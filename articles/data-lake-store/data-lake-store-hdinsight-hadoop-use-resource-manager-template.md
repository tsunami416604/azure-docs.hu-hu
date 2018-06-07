---
title: Azure-sablonok segítségével hozza létre a HDInsight és a Data Lake Store |} Microsoft Docs
description: Használja az Azure Resource Manager-sablonok létrehozása és használata a HDInsight-fürtök az Azure Data Lake Store
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
ms.openlocfilehash: 7f49b643550b1888e1b0a4a6a57bbe4d428c65ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625780"
---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>HDInsight-fürtök létrehozása a Data Lake Store Azure Resource Manager-sablonnal
> [!div class="op_single_selector"]
> * [A Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell használatával (az alapértelmezett tároló)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [(Tárhely) a PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használatával](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell használata a HDInsight-fürtök konfigurálása az Azure Data Lake Store **további tárolóként**.

Támogatott fürttípusok Data Lake Store alapértelmezett tároló vagy további tárfiókot kell használni. Ha a Data Lake Store további tárterületet, az alapértelmezett tárfiókot, a fürt továbbra is Azure Storage Blobs (WASB), és a fürt kapcsolatos (például naplói, stb.) továbbra is írja a fájlt, alapértelmezett tárolására, amíg a Data Lake Store-fiók tárolhatja az adatokat, hogy fel szeretné dolgoztatni. További tárhely fiókként használatával a Data Lake Store nem befolyásolja a teljesítményt vagy olvasására vagy írására a tárhelyet a fürtből történő alkalmazásának képességét.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Data Lake Store használata a HDInsight-fürt tárolására

Az alábbiakban a HDInsight a Data Lake Store használatára vonatkozó szempontokat:

* A HDInsight-fürtök létrehozása a Data Lake Store-hozzáféréssel rendelkező áll rendelkezésre a HDInsight 3.5-ös és 3.6, alapértelmezett tárolási lehetőséget.

* A HDInsight-fürtök létrehozása a Data Lake Store elérését, további tárhely áll rendelkezésre a HDInsight-verziókról 3.2-es, 3.4, 3.5-ös és 3.6 lehetőséget.

Ebben a cikkben azt a Data Lake Store a Hadoop fürtök a további tárolóként kell kiépíteni. A Hadoop-fürt létrehozása a Data Lake Store alapértelmezett tárolóként, lásd: [HDInsight-fürtök létrehozása az Azure portál használatával a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* **Az Azure Active Directory szolgáltatás egyszerű**. Ez az oktatóanyag lépéseit ad útmutatást az egyszerű szolgáltatás létrehozása az Azure ad-ben. Azonban az Azure AD a rendszergazda létrehozhat egy egyszerű szolgáltatást kell lennie. Ha az Azure AD-rendszergazdaként, hagyja ki ezt az előfeltételt, és az oktatóanyag folytatásához.

    **Ha nem az Azure AD-rendszergazda**, nem fog tudni egy egyszerű szolgáltatásnév létrehozásához szükséges lépéseket. Ebben az esetben az Azure AD-rendszergazda először létre kell hoznia egy egyszerű szolgáltatást a Data Lake Store egy HDInsight-fürt létrehozása előtt. Emellett az egyszerű szolgáltatás segítségével kell létrehozni egy tanúsítványt, részben ismertetett módon [hozzon létre egy egyszerű tanúsítvány](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>HDInsight-fürtök létrehozása az Azure Data Lake Store
A Resource Manager-sablon, és a sablon használatára vonatkozó Előfeltételek legyenek elérhetők a Githubon: [az új Data Lake Store HDInsight Linux fürt központi telepítése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Kövesse az utasításokat, ez a hivatkozás elérhető HDInsight-fürtök létrehozása az Azure Data Lake Store további tárolóként.

A fenti hivatkozás található útmutatás PowerShell szükséges. Mielőtt elkezdené a ezeket az utasításokat, győződjön meg arról az Azure-fiókjába való bejelentkezés. Az asztalon nyisson meg egy új Azure PowerShell-ablakot, és adja meg az alábbi kódtöredékek. Győződjön meg arról, hogy az előfizetés rendszergazdájaként/tulajdonosaként jelentkezik be, amikor a rendszer erre kéri:

```
# Log in to your Azure account
Connect-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Az Azure Data Lake Store mintaadatok feltöltése
A Resource Manager-sablon létrehoz egy új Data Lake Store-fiókot, és a HDInsight-fürthöz társítja azt. A Data Lake Store most tölthet fel néhány adatot. Feladatok futtatása az adatok a Data Lake Store-ban elérhető HDInsight-fürtök az oktatóanyag későbbi részében szüksége lesz az adatokat. Hogyan tölthet fel adatokat, lásd: [-fájl feltöltése a Data Lake store](data-lake-store-get-started-portal.md#uploaddata). Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

## <a name="set-relevant-acls-on-the-sample-data"></a>A mintaadatok vonatkozó hozzáférés-vezérlési listák beállítása
Annak biztosításához, a feltöltött mintaadatok érhető el a HDInsight-fürt, akkor biztosítania kell, hogy az Azure AD-alkalmazás, amely a HDInsight-fürt és a Data Lake Store között identitás hozzáfér a fájl vagy mappa elérésére tett kísérlet létrehozására szolgál. Ehhez hajtsa végre a következő lépéseket.

1. Keresse meg a HDInsight-fürt és a Data Lake Store társított Azure AD-alkalmazást. Egyirányú kikeresni a következő a neve, amely a Resource Manager sablonnal létrehozott HDInsight fürt panel megnyitásához kattintson a **fürt AAD-identitása** lapot, és keresse meg a értékének **egyszerű szolgáltatás megjelenített neve**.
2. Most biztosítanak az Azure AD-alkalmazáshoz való hozzáférés a mappára vagy fájlra, amelyet a HDInsight-fürt eléréséhez. A megfelelő hozzáférés-vezérlési beállítani a fájl vagy mappa a Data Lake Store, lásd: [adatok védelme az Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>A HDInsight-fürt használata a Data Lake Store tesztet-feladatok futtatása
Miután konfigurálta a HDInsight-fürtöt, a teszt feladatok ellenőrzéséhez, hogy a HDInsight-fürt hozzáférhet-e a Data Lake Store a fürtön is futtathatja. Ehhez az szükséges, azt egy minta Hive táblát hoz létre a Data Lake Store korábban feltöltött megadott mintaadatokat használja feladat elindul.

Ebben a szakaszban fogja SSH egy HDInsight Linux-fürt, és futtassa a minta Hive-lekérdezések. Ha egy Windows ügyfél használ, azt javasoljuk, **PuTTY**, amely letölthető [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

A PuTTY használatával kapcsolatos további információkért lásd: [SSH használata a HDInsight Windows Linux-alapú Hadooppal ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. A csatlakozás után a következő parancs használatával indítsa el a Hive CLI:

   ```
   hive
   ```
2. A parancssori felület használatával adja meg az alábbi állításokat annak nevű új tábla létrehozása **járművekről gyűjtött** által megadott mintaadatokat használja a Data Lake Store-ban:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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


## <a name="access-data-lake-store-using-hdfs-commands"></a>Hozzáférés Data Lake Store HDFS parancs használatával
Miután konfigurálta a Data Lake Store használata a HDInsight-fürthöz, a HDFS felületparancsokat használhatja az áruház eléréséhez szükséges.

Ebben a szakaszban fogja SSH egy HDInsight Linux a fürt és a HDFS parancsokat. Ha egy Windows ügyfél használ, azt javasoljuk, **PuTTY**, amely letölthető [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

A PuTTY használatával kapcsolatos további információkért lásd: [SSH használata a HDInsight Windows Linux-alapú Hadooppal ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

A csatlakozás után a következő HDFS filesystem parancs segítségével a Data Lake Store található fájlok listázása.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Megjelenik a korábban a Data Lake Store-bA feltöltött fájl.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

Használhatja a `hdfs dfs -put` parancs egyes fájlok feltöltése a Data Lake Store, és ezután `hdfs dfs -ls` ellenőrzése, hogy a fájlok sikeresen feltöltve.


## <a name="next-steps"></a>További lépések
* [Adatok másolása az Azure Storage Blobs Data Lake Store-bA](data-lake-store-copy-data-wasb-distcp.md)
* [Használjon Data Lake Store az Azure HDInsight-fürtök](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
