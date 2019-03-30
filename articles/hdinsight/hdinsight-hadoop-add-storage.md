---
title: A HDInsight további Azure storage-fiókok hozzáadása
description: Ismerje meg, további Azure storage-fiókok hozzáadása egy meglévő HDInsight-fürthöz.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: hrasheed
ms.openlocfilehash: 373851c406d95a2e458c017cb311bd5cc4e5b30f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664290"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>A HDInsight további tárfiókok hozzáadása

Ismerje meg, hogyan szkriptműveletek használatával adjon hozzá további az Azure storage *fiókok* a HDInsight. A jelen dokumentumban leírt lépések hozzáadása a storage *fiók* meglévő Linux-alapú HDInsight-fürtre. Ez a cikk vonatkozik, és *fiókok* (nem az alapértelmezett fürt tárfiók), és nincs további tárhely például [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) és [Azure Data Lake Storage Gen2 ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> A jelen dokumentumban lévő információk további tárhely hozzáadása egy fürt létrehozása után van. Információk a storage-fiókok hozzáadása a fürt létrehozása során: [fürtök beállítása a HDInsight az Apache Hadoop, az Apache Spark, az Apache Kafka és további](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Előfeltételek

* A HDInsight Hadoop-fürt. Lásd: [HDInsight Linux első lépések](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Tárfiók neve és kulcsa. Lásd: [kezelése a tárfiók-beállítások az Azure Portalon](../storage/common/storage-account-manage.md).
* [Megfelelően kisbetűsek fürtnév](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Ha a PowerShell használatával kell a AZ modul.  Lásd: [Azure PowerShell áttekintése](https://docs.microsoft.com/powershell/azure/overview).
* Ha még nem telepítette az Azure CLI-vel, tekintse meg [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Ha a bash vagy egy windows parancssori ablakba, is kell **jq**, egy parancssori JSON feldolgozó.  Lásd: [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/). Találhat bash on Ubuntu on Windows 10 [Linux telepítési útmutató a Windows 10-es Windows-alrendszer](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Működés

Ez a szkript a következő paramétereket fogadja:

* __Az Azure storage-fiók neve__: A HDInsight-fürt hozzáadása a tárfiók neve. A szkript futtatása után HDInsight olvashat és írhat a tárfiókban tárolt adatokat.

* __Az Azure storage-fiókkulcs__: Egy kulcs, amely engedélyezi a hozzáférést a tárfiókhoz.

* __-p__ (nem kötelező): Ha meg van adva, a kulcs nem titkosított, és egyszerű szövegként a core-site.xml fájlban tárolja.

A feldolgozás során a szkript a következő műveleteket hajtja végre:

* A tárfiók már létezik a core-site.xml a fürt konfigurációját, ha a parancsfájl kilép, és nincs több teendő történik.

* Ellenőrzi, hogy a tárfiók létezik, és a kulcs segítségével érhetők el.

* Titkosítja a kulcsot, a fürt hitelesítő adatok használatával.

* A storage-fiók hozzáadása a core-site.xml fájlt.

* Leállítja és újraindítja a [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), és [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) szolgáltatások. Ezek a szolgáltatások indítása és leállítása lehetővé teszi, hogy az új tárfiókot használni őket.

> [!WARNING]  
> A HDInsight-fürt, mint egy másik helyen lévő tárfiókok használata nem támogatott.

## <a name="the-script"></a>A parancsfájl

__Parancsfájl helye__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Követelmények__:  A parancsfájl a alkalmazni kell a __Átjárócsomópontokhoz__. Nem kell megjelölni, ez a szkript __megőrzött__, ahogy azt közvetlenül frissíti a fürt Ambari konfigurációját.

## <a name="to-use-the-script"></a>A parancsfájl használata

Ez a szkript az Azure PowerShell, Azure CLI-vel vagy az Azure Portalon is használható.

### <a name="powershell"></a>PowerShell

Használatával [elküldése AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Cserélje le `CLUSTERNAME`, `ACCOUNTNAME`, és `ACCOUNTKEY` megfelelő értékeivel.

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

### <a name="azure-cli"></a>Azure CLI

Használatával [az hdinsight-parancsfájlművelet végrehajtása](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Cserélje le `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, és `ACCOUNTKEY` megfelelő értékeivel.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure Portal

Lásd: [szkriptműveletet vonatkozik egy futó fürt](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Ismert problémák

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Storage-fiókok nem jelenik meg az Azure Portalon vagy az eszközök

A HDInsight-fürt az Azure Portalon megtekintésekor kiválasztása a __Tárfiókok__ bejegyzés alatt __tulajdonságok__ nem jelennek meg a parancsprogram-művelet során hozzáadott tárfiókok. Az Azure PowerShell és az Azure CLI-vel nem jelennek meg a további tárfiókot vagy.

A storage-adatokat nem jelenik meg, mert a parancsfájl csak módosítja a core-site.xml a fürt konfigurációját. Ezeket az információkat nem használja az Azure felügyeleti API-k használatával fürtinformációkat lekérésekor.

Az Ambari REST API használatával hozzá a fürthöz, a parancsfájl tárfiók-információ megtekintéséhez. A következő parancsok használatával lekérheti a fürt ezt az információt:

### <a name="powershell"></a>PowerShell

Cserélje le `CLUSTERNAME` megfelelően kisbetűsek fürt nevét. Először határozza meg a konfigurációs verzióját használja az alábbi parancs beírásával:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Cserélje le `ACCOUNTNAME` a tényleges nevét. Ezután cserélje le `4` tényleges config verziója szolgáltatást, és adja meg a parancsot. Amikor a rendszer kéri, adja meg a fürt bejelentkezési jelszava.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>A bash
Cserélje le `myCluster` megfelelően kisbetűsek fürt nevét.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Cserélje le `myAccount` a tényleges nevét. Ezután cserélje le `4` tényleges config verziója szolgáltatást, és adja meg a parancsot:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Cserélje le `CLUSTERNAME` mindkét szkriptek a fürt megfelelően kisbetűsek néven. Először határozza meg a konfigurációs verzióját használja az alábbi parancs beírásával:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Cserélje le `ACCOUNTNAME` a tényleges nevét. Ezután cserélje le `4` tényleges config verziója szolgáltatást, és adja meg a parancsot:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 Ez a parancs által visszaadott információ jelenik meg az alábbi szöveghez hasonló:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ez a szöveg, amelyek egy titkosított kulcs, amely a tárfiók eléréséhez használatos.

### <a name="unable-to-access-storage-after-changing-key"></a>Nem fér hozzá a tárolási kulcs módosítása után

Ha módosítja a tárfiók kulcsát, HDInsight már nem tud hozzáférni a tárfiókhoz. HDInsight a core-site.xml kulcs gyorsítótárazott másolatának a fürt használja. Ezt a gyorsítótárazott példányt frissíteni kell, hogy az új kulcs egyezik.

Parancsfájl művelet ismételt futtatása does __nem__ frissíteni a kulcsot, mert a parancsfájl ellenőrzi, hogy a storage-fiókhoz tartozó bejegyzés már létezik. Ha már létezik egy bejegyzést, azt nem végezze el a módosításokat.

A probléma megkerüléséhez, el kell távolítania a meglévő bejegyzést a tárfiókhoz. Az alábbi lépések segítségével, meglévő bejegyzés eltávolításához:

1. Egy webböngészőben nyissa meg az Ambari webes felhasználói Felületet, a HDInsight-fürt számára. Az URI-ja `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.

    Amikor a rendszer kéri, adja meg a HTTP-bejelentkezési felhasználónév és jelszó a fürt számára.

2. Az oldal bal oldalán a szolgáltatások listájából válassza ki a __HDFS__. Válassza ki a __Configs__ fülre az oldal közepén.

3. Az a __szűrése...__  mezőben adja meg a egy értéke __fs.azure.account__. Ez a lettek hozzáadva a fürt minden további tárfiókok bejegyzést ad vissza. Két típusú bejegyzéseket; __keyprovider__ és __kulcs__. A kulcsnév részeként a tárfiók nevét is tartalmaz.

    A következő nevű tárfiókok példa tételt __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Miután azonosította, hogy a kulcsokat a tárfiók, el kell távolítania, használja a piros "-" ikont jobb oldalán a bejegyzés törli-e. Ezután a __mentése__ gombra a módosítások mentéséhez.

5. Módosítások mentése után a parancsfájl művelettel a storage-fiók és az új kulcs-érték hozzáadása a fürthöz.

### <a name="poor-performance"></a>Gyenge teljesítményt

Ha a tárfiók más régióban, mint a HDInsight-fürt, gyenge teljesítményt tapasztalhat. Egy másik régióban lévő adatok elérése elküldi a hálózati forgalom, a regionális Azure adatközponton kívül és késés megjelentetni nyilvános interneten keresztül.

### <a name="additional-charges"></a>További díjak

Ha a tárfiók más régióban, mint a HDInsight-fürt, az Azure számlázását a előfordulhat, hogy figyelje meg a további kimenő forgalom költségeit. Egy kimenő forgalmi díjat adatok kikerül egy regionális adatközpont esetén is alkalmazva lesz. A díj akkor is, ha a forgalom egy másik Azure-adatközpontban egy másik régióban lévő szánt van alkalmazva.

## <a name="next-steps"></a>További lépések

Megtanulhatta, hogyan adhat hozzá további tárfiókok meglévő HDInsight-fürtre. Parancsfájlműveletekkel további információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)
