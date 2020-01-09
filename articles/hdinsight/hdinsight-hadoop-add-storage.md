---
title: További Azure Storage-fiókok hozzáadása a HDInsight-hez
description: Ismerje meg, hogyan adhat hozzá további Azure Storage-fiókokat egy meglévő HDInsight-fürthöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 86b9230dbdca82c5599c1839fd64bd3df4725051
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435572"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>További Storage-fiókok hozzáadása a HDInsight-hez

Megtudhatja, hogyan használhat parancsfájl-műveleteket további Azure Storage- *fiókok* HDInsight való hozzáadásához. A jelen dokumentum lépései egy meglévő Linux-alapú HDInsight-fürthöz adhatnak hozzá Storage- *fiókot* . Ez a cikk a Storage- *fiókokra* vonatkozik (nem az alapértelmezett fürtlemez-fiókra), és nem a további tárterület, például a [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) és a [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> A jelen dokumentumban található információk további Storage-fiók (ok) egy fürthöz való hozzáadását ismertetik a létrehozása után. A Storage-fiókok fürt létrehozása során történő hozzáadásával kapcsolatos információkért lásd: [fürtök beállítása a HDInsight-ben Apache Hadoop, Apache Spark, Apache Kafka és sok más](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Előfeltételek

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* A Storage-fiók neve és kulcsa. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md).
* A [fürt nevének megfelelő betokozása](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Ha a PowerShellt használja, szüksége lesz az az modulra.  Lásd: [Azure PowerShell áttekintése](https://docs.microsoft.com/powershell/azure/overview).
* Ha még nem telepítette az Azure CLI-t, tekintse meg az [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)című témakört.
* Ha bash-t vagy Windows-parancssort használ, **jQ**, parancssori JSON-processzorra is szüksége lesz.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). A bash on Ubuntu on Windows 10 lásd: [Windows alrendszer a Linux-telepítési útmutató a Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)rendszerhez.

## <a name="how-it-works"></a>Működési elv

A szkript a következő paramétereket veszi figyelembe:

* __Azure Storage-fiók neve__: a HDInsight-fürthöz hozzáadandó Storage-fiók neve. A szkript futtatása után a HDInsight képes olvasni és írni az ebben a Storage-fiókban tárolt adatfájlokat.

* __Azure Storage-fiók kulcsa__: olyan kulcs, amely hozzáférést biztosít a Storage-fiókhoz.

* __-p__ (nem kötelező): Ha meg van adva, a kulcs nincs titkosítva, és a Core-site. xml fájlban tárolja egyszerű szövegként.

A feldolgozás során a parancsfájl a következő műveleteket hajtja végre:

* Ha a Storage-fiók már létezik a fürt Core-site. XML konfigurációjában, a parancsfájl kilép, és nem hajt végre további műveletet.

* Ellenőrzi, hogy a Storage-fiók létezik-e, és hogy a kulcs használatával érhető-e el.

* Titkosítja a kulcsot a fürt hitelesítő adatainak használatával.

* Hozzáadja a Storage-fiókot a Core-site. xml fájlhoz.

* Leállítja és újraindítja az [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop fonalat](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)és [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) szolgáltatásokat. A szolgáltatások leállítása és elindítása lehetővé teszi, hogy az új Storage-fiókot használják.

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen lévő Storage-fiók használata nem támogatott.

## <a name="the-script"></a>A parancsfájl

__Parancsfájl helye__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Követelmények__: a szkriptet a __fő csomópontokon__kell alkalmazni. A parancsfájlt nem kell megőrzöttként megjelölni __, mert közvetlenül__frissíti a fürt Ambari-konfigurációját.

## <a name="to-use-the-script"></a>A szkript használata

Ezt a szkriptet a Azure PowerShell, az Azure CLI vagy a Azure Portal használhatja.

### <a name="powershell"></a>PowerShell

A [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction)használata. Cserélje le a `CLUSTERNAME`, `ACCOUNTNAME`és `ACCOUNTKEY` értékeket a megfelelő értékekre.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az [az hdinsight script-Action Execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)paranccsal.  Cserélje le a `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`és `ACCOUNTKEY` értékeket a megfelelő értékekre.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure portál

Lásd: [parancsfájl-művelet alkalmazása futó fürtre](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Ismert problémák

### <a name="storage-firewall"></a>Tárolási tűzfal

Ha úgy dönt, hogy védi a Storage-fiókot a **tűzfalakkal és a virtuális hálózatokkal** kapcsolatos korlátozásokkal a **kiválasztott hálózatokon**, ügyeljen arra, hogy a kivételt engedélyezze a **megbízható Microsoft-szolgáltatások számára** ... így a HDInsight hozzáférhet a Storage-fiókhoz.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Azure Portal vagy eszközök nem jelennek meg a Storage-fiókok között

Ha a HDInsight-fürtöt a Azure Portal tekinti meg, akkor a __Tulajdonságok__ területen a __Storage-fiókok__ bejegyzés nem jeleníti meg a parancsfájl-művelettel hozzáadott tárolási fiókokat. Azure PowerShell és az Azure CLI nem jeleníti meg a további Storage-fiókot sem.

A tárolási adatok nem jelennek meg, mert a parancsfájl csak a fürt Core-site. XML konfigurációját módosítja. Ez az információ nem használatos a fürt adatainak Azure felügyeleti API-k használatával történő beolvasásakor.

Ha meg szeretné tekinteni a fürthöz az ezzel a parancsfájllal hozzáadott Storage-fiók adatait, használja a Ambari REST API. A fürthöz tartozó információk beolvasásához használja a következő parancsokat:

### <a name="powershell"></a>PowerShell

Cserélje le a `CLUSTERNAME`t a megfelelő tokozású fürt nevére. Cserélje le a `ACCOUNTNAME`t a tényleges nevekre. Ha a rendszer kéri, adja meg a fürt bejelentkezési jelszavát.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Cserélje le a `CLUSTERNAME`t a megfelelő tokozású fürt nevére. Cserélje le a `PASSWORD`t a fürt rendszergazdai jelszavára. Cserélje le a `STORAGEACCOUNT`t a tényleges Storage-fiók nevére.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Cserélje le a `CLUSTERNAME`t a megfelelő tokozású fürt nevére mindkét parancsfájlban. Először azonosítsa a szolgáltatás konfigurációs verzióját a használatban az alábbi parancs beírásával:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Cserélje le a `ACCOUNTNAME`t a tényleges Storage-fiók nevére. Ezután cserélje le `4`t a tényleges szolgáltatás konfigurációjának verziójára, és írja be a parancsot:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

A parancs által visszaadott adatok az alábbi szöveghez hasonlóan jelennek meg:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ez a szöveg egy titkosított kulcs példája, amely a Storage-fiók elérésére szolgál.

### <a name="unable-to-access-storage-after-changing-key"></a>Nem lehet hozzáférni a tárolóhoz a kulcs módosítása után

Ha megváltoztatja egy Storage-fiók kulcsát, a HDInsight már nem fér hozzá a Storage-fiókhoz. A HDInsight a kulcs gyorsítótárazott példányát használja a Core-site. xml fájlban a fürthöz. Ezt a gyorsítótárazott másolatot frissíteni kell, hogy az megfeleljen az új kulcsnak.

A parancsfájl-művelet ismételt futtatása __nem__ frissíti a kulcsot, mivel a parancsfájl ellenőrzi, hogy van-e már létező bejegyzés a Storage-fiókhoz. Ha egy bejegyzés már létezik, nem végez módosítást.

A probléma megkerüléséhez el kell távolítania a meglévő bejegyzést a Storage-fiókhoz. A meglévő bejegyzés eltávolításához kövesse az alábbi lépéseket:

> [!IMPORTANT]  
> A fürthöz csatolt elsődleges Storage-fiók tárolási kulcsának elforgatása nem támogatott.

1. A böngészőben nyissa meg a Ambari webes felhasználói felületét a HDInsight-fürthöz. Az URI `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.

    Ha a rendszer kéri, adja meg a fürthöz tartozó HTTP-bejelentkezési felhasználót és jelszót.

2. A lap bal oldalán található szolgáltatások listájából válassza a __HDFS__lehetőséget. Ezután válassza az oldal közepén található __konfigurációk__ lapot.

3. A __Filter...__ mezőben adja meg az __FS. Azure. Account__értéket. Ez a függvény a fürthöz hozzáadott további Storage-fiókok bejegyzéseinek visszaadása. Két típusú bejegyzés létezik; __a__ kulcstartó és a __kulcs__. Mindkettő tartalmazza a Storage-fiók nevét a kulcs neve részeként.

    A következő példa egy __mystorage__nevű Storage-fiókra vonatkozó bejegyzéseket mutat be:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Miután azonosította az eltávolítandó Storage-fiókhoz tartozó kulcsokat, a törléshez használja a "-" ikont a bejegyzés jobb oldalán. Ezután mentse a módosításokat a __Save (Mentés__ ) gombra kattintva.

5. A módosítások mentése után a parancsfájl művelettel adja hozzá a Storage-fiókot és az új kulcs értékét a fürthöz.

### <a name="poor-performance"></a>Gyenge teljesítmény

Ha a Storage-fiók a HDInsight-fürttől eltérő régióban található, a teljesítmény gyenge lehet. Az adatok egy másik régióban való elérése a regionális Azure-adatközponton és a nyilvános interneten kívüli hálózati forgalmat küld, amely a késést is képes bevezetni.

### <a name="additional-charges"></a>További díjak

Ha a Storage-fiók a HDInsight-fürttől eltérő régióban található, az Azure-számlázás további kimenő költségeire is figyelmeztetheti. Ha az adatközpont elhagyja a helyi adatközpontot, a kimenő forgalom díját is alkalmazza. Ez a díj akkor is alkalmazható, ha a forgalmat egy másik régióban lévő Azure-adatközpontra szánják.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan adhat hozzá további Storage-fiókokat egy meglévő HDInsight-fürthöz. További információ a parancsfájl-műveletekről: [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-művelet használatával](hdinsight-hadoop-customize-cluster-linux.md)
