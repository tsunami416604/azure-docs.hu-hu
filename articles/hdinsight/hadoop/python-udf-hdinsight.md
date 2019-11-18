---
title: Python UDF Apache Hive és Apache Pig – Azure HDInsight
description: Ismerje meg, hogyan használható a Python felhasználói függvények (UDF) a Apache Hive és az Apache Pig HDInsight, az Azure-beli Apache Hadoop Technology stack használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148404"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Python-felhasználó által definiált függvények (UDF) használata Apache Hive és Apache Pig használatával a HDInsight-ben

Ismerje meg, hogyan használhatók a Python felhasználó által definiált függvények (UDF) a Apache Hive és az Apache Pig használatával Apache Hadoop az Azure HDInsight.

## <a name="python"></a>Python a HDInsight-on

A Python 2.7 alapértelmezés szerint telepítve van a HDInsight 3,0-es és újabb verzióiban. A Apache Hive a Python ezen verziójával használható a stream feldolgozásához. Az adatfolyam-feldolgozás az STDOUT és a STDIN használatával továbbítja az adatátvitelt a kaptár és az UDF között.

A HDInsight a Jython is tartalmazza, amely egy Java-ban írt python-implementáció. A Jython közvetlenül a Java virtuális gép fut, és nem használja az adatfolyamot. A Jython a Python és a Pig használata esetén ajánlott Python-tolmács.

## <a name="prerequisites"></a>Előfeltételek

* **Hadoop-fürt a HDInsight-on**. Lásd: Ismerkedés [a HDInsight Linux rendszeren](apache-hadoop-linux-tutorial-get-started.md).
* **Egy SSH-ügyfél**. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).
* A fürtök elsődleges tárolójának [URI-sémája](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ez `wasb://` az Azure Storage-hoz, `abfs://` a Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2 vagy adl://. Ha a biztonságos átvitel engedélyezve van az Azure Storage-hoz, az URI wasbs://lesz.  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).
* **A tárolási konfiguráció lehetséges módosítása.**  Tekintse meg a [tárolási konfigurációt](#storage-configuration) , ha a Storage-fiók típusa `BlobStorage`.
* Választható.  Ha a PowerShell használatát tervezi, az az [modult](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) kell telepítenie.

> [!NOTE]  
> A cikkben használt Storage-fiók az Azure [Storage szolgáltatás engedélyezve van, és](../../storage/common/storage-require-secure-transfer.md) így `wasbs` a cikk egészében használatos.

## <a name="storage-configuration"></a>Tároló konfigurálása

Nincs szükség beavatkozásra, ha a használt Storage-fiók típusa `Storage (general purpose v1)` vagy `StorageV2 (general purpose v2)`.  A cikkben szereplő folyamat legalább `/tezstaging`kimenetet eredményez.  Az alapértelmezett Hadoop-konfiguráció `/tezstaging` fog szerepelni a `core-site.xml` for Service `HDFS``fs.azure.page.blob.dir` konfigurációs változójában.  Ez a konfiguráció azt eredményezi, hogy a könyvtár Blobok lesznek, amelyek nem támogatottak a Storage-fiók `BlobStorage`.  Ha a cikkhez `BlobStorage` szeretne használni, távolítsa el `/tezstaging` a `fs.azure.page.blob.dir` konfigurációs változóból.  A konfiguráció a [Ambari felhasználói felületéről](../hdinsight-hadoop-manage-ambari.md)érhető el.  Ellenkező esetben a következő hibaüzenet jelenik meg: `Page blob is not supported for this account type.`

> [!WARNING]  
> A jelen dokumentumban ismertetett lépések a következő feltételezéseket teszik:  
>
> * A Python-szkripteket a helyi fejlesztési környezetben hozza létre.
> * A szkripteket a `scp` parancs vagy a megadott PowerShell-szkript használatával tölti fel a HDInsight.
>
> Ha a [Azure Cloud shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) használatával szeretné használni a HDInsight-t, akkor a következőket kell tennie:
>
> * Hozza létre a parancsfájlokat a Cloud Shell-környezetben.
> * A `scp` használatával töltse fel a fájlokat a Cloud shellből a HDInsight-be.
> * A Cloud Shell `ssh` használatával csatlakozhat a HDInsight, és futtathatja a példákat.

## <a name="hivepython"></a>UDF Apache Hive

A Python a kaptárból UDF-ként is használható a HiveQL `TRANSFORM` utasítással. A következő HiveQL például meghívja a fürt alapértelmezett Azure Storage-fiókjában tárolt `hiveudf.py` fájlt.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

A példa a következőképpen működik:

1. A fájl elején található `add file` utasítás hozzáadja a `hiveudf.py` fájlt az elosztott gyorsítótárhoz, így a fürt összes csomópontja számára elérhetővé válik.
2. Az `SELECT TRANSFORM ... USING` utasítás kiválasztja az adatok `hivesampletable`. Emellett a ClientID, a devicemake és a devicemodel értékeket is átadja a `hiveudf.py`-parancsfájlnak.
3. A `AS` záradék a `hiveudf.py`által visszaadott mezőket ismerteti.

<a name="streamingpy"></a>

### <a name="create-file"></a>Fájl létrehozása

A fejlesztői környezetben hozzon létre egy `hiveudf.py`nevű szövegfájlt. Használja a következő kódot a fájl tartalmához:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Ez a szkript a következő műveleteket hajtja végre:

1. Adatsorokat olvas be az STDIN-ből.
2. A rendszer eltávolítja a záró sortörési karaktert a `string.strip(line, "\n ")`használatával.
3. Az adatfolyam-feldolgozás során a rendszer egyetlen sort tartalmaz az egyes értékek közötti Tab karakterrel rendelkező összes értékre. Így `string.split(line, "\t")` a bemenetek felosztásához az egyes lapokon, csak a mezőket adja vissza.
4. A feldolgozás befejezésekor a kimenetet egyetlen sorba kell írni, és az egyes mezők között egy fület kell megadnia. Például: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. A `while` hurok addig ismétlődik, amíg nincs `line` beolvasva.

A szkript kimenete a `devicemake` és `devicemodel`bemeneti értékeinek összefűzése, valamint az összefűzött érték kivonata.

### <a name="upload-file-shell"></a>Fájl feltöltése (rendszerhéj)

Az alábbi parancsokban cserélje le a `sshuser`t a tényleges felhasználónévvel, ha más.  Cserélje le a `mycluster`t a tényleges fürt nevére.  Győződjön meg arról, hogy a munkakönyvtár a fájl helye.

1. A `scp` segítségével másolja a fájlokat a HDInsight-fürtbe. Szerkessze és írja be az alábbi parancsot:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Használja az SSH-t a fürthöz való kapcsolódáshoz.  Szerkessze és írja be az alábbi parancsot:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből adja hozzá a korábban feltöltött Python-fájlokat a fürt tárterületéhez.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>A méhkas UDF (Shell) használata

1. A Kaptárhoz való kapcsolódáshoz használja a következő parancsot az Open SSH-munkamenetből:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Ez a parancs elindítja a Beeline-ügyfelet.

2. Adja meg a következő lekérdezést a `0: jdbc:hive2://headnodehost:10001/>` parancssorban:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Az utolsó sor megadása után a feladattípusnak indulnia kell. Miután a feladatok befejeződik, az a következő példához hasonló kimenetet ad vissza:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. A Beeline kilépéséhez írja be a következő parancsot:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Fájl feltöltése (PowerShell)

A PowerShell használható a kaptár-lekérdezések távoli futtatására is. Győződjön meg arról, hogy a munkakönyvtár a `hiveudf.py` található.  A következő PowerShell-szkripttel futtathat egy `hiveudf.py` parancsfájlt használó kaptár-lekérdezést:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> A fájlok feltöltésével kapcsolatos további információkért tekintse meg az [adatok feltöltése Apache Hadoop feladatok számára a HDInsight](../hdinsight-upload-data.md) dokumentumban.

#### <a name="use-hive-udf"></a>A kaptár UDF használata

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A **kaptár** -feladatokhoz tartozó kimenetnek az alábbi példához hasonlóan kell megjelennie:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="pigpython"></a>Apache Pig UDF

A Python-szkriptek a Pig-ből származó UDF-ként is használhatók a `GENERATE` utasításban. A szkriptet a Jython vagy a C Python használatával is futtathatja.

* A Jython a JVM fut, és natív módon hívható meg a Pig-től.
* A C Python egy külső folyamat, így a JVM lévő Pig-ből származó adatok a Python-folyamatban futó parancsfájlba kerülnek. A Python-szkript kimenetét a rendszer visszaküldi a Pig-nek.

A Python-tolmács megadásához használja a `register` a Python-szkriptre való hivatkozáskor. Az alábbi példákban a Pig `myfuncs`:

* **A Jython használata**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **A C Python használata**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> A Jython használatakor a pig_jython-fájl elérési útja lehet helyi elérési út vagy WASBS://elérési út is. A C Python használatakor azonban hivatkoznia kell egy fájlra annak a csomópontnak a helyi fájlrendszerén, amelyet a Pig feladat elküldéséhez használ.

A korábbi regisztráció után a példában szereplő latin a következőhöz hasonló:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

A példa a következőképpen működik:

1. Az első sor betölti a minta adatfájlt, `sample.log` a `LOGS`ba. Emellett az egyes rekordokat `chararray`ként határozza meg.
2. A következő sor kiszűri a null értékeket, és a művelet eredményét `LOG`ba tárolja.
3. Ezután megismétli a `LOG` lévő rekordokat, és a `GENERATE` használatával meghívja a `create_structure` metódust, amely a Python/Jython parancsfájlban szerepel `myfuncs`ként betöltve. `LINE` az aktuális rekord függvénybe való továbbítására szolgál.
4. Végül a kimenetek az STDOUT-ra kerülnek a `DUMP` parancs használatával. Ez a parancs a művelet befejeződése után jeleníti meg az eredményeket.

### <a name="create-file"></a>Fájl létrehozása

A fejlesztői környezetben hozzon létre egy `pigudf.py`nevű szövegfájlt. Használja a következő kódot a fájl tartalmához:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

A Pig Latin példában a `LINE` bemenet chararray van definiálva, mert nincs konzisztens séma a bemenethez. A Python-szkript átalakítja az adatokat egy konzisztens sémára a kimenethez.

1. A `@outputSchema` utasítás meghatározza a Pig-nek visszaadott adatok formátumát. Ebben az esetben ez egy **adattáska**, amely egy Pig-adattípus. A táska a következő mezőket tartalmazza, amelyek mindegyike chararray (karakterlánc):

   * dátum – a naplóbejegyzés létrehozásának dátuma
   * idő – a naplóbejegyzés létrehozásának időpontja
   * osztálynév – a bejegyzést létrehozó osztály neve
   * szint – a naplózási szint
   * Részletek – részletes részletek a napló bejegyzéséhez

2. Ezután a `def create_structure(input)` meghatározza azt a függvényt, amelyet a Pig a sorokra továbbít.

3. A példában szereplő adatok (`sample.log`) többnyire a Date, az Time, az osztálynév, a Level és a detail sémának felelnek meg. Ez azonban néhány olyan sort tartalmaz, amelyek `*java.lang.Exception*`kal kezdődnek. Ezeket a sorokat módosítani kell, hogy egyezzenek a sémával. A `if` utasítás ezeket a fájlokat ellenőrzi, majd a bemeneti adatokat a `*java.lang.Exception*` sztring végére helyezi át, és az adatokat összhangba hozza a várt kimeneti sémával.

4. Ezután a `split` parancs az adatfelosztást használja az első négy szóköz karakternél. A kimenet `date`, `time`, `classname`, `level`és `detail`hoz van rendelve.

5. Végül a rendszer visszaadja az értékeket a Pig-nek.

Ha a rendszer visszaküldi az adatokhoz a malacot, a `@outputSchema` utasításban meghatározott konzisztens sémával rendelkezik.

### <a name="upload-file-shell"></a>Fájl feltöltése (rendszerhéj)

Az alábbi parancsokban cserélje le a `sshuser`t a tényleges felhasználónévvel, ha más.  Cserélje le a `mycluster`t a tényleges fürt nevére.  Győződjön meg arról, hogy a munkakönyvtár a fájl helye.

1. A `scp` segítségével másolja a fájlokat a HDInsight-fürtbe. Szerkessze és írja be az alábbi parancsot:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Használja az SSH-t a fürthöz való kapcsolódáshoz.  Szerkessze és írja be az alábbi parancsot:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből adja hozzá a korábban feltöltött Python-fájlokat a fürt tárterületéhez.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>A Pig UDF (Shell) használata

1. A Pig-hez való kapcsolódáshoz használja a következő parancsot az Open SSH-munkamenetből:

    ```bash
    pig
    ```

2. Adja meg a következő utasításokat a `grunt>` parancssorban:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. A következő sor beírása után a feladattípusnak indulnia kell. Miután a feladatok befejeződik, az a következő adatokhoz hasonló kimenetet ad vissza:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. A `quit` használatával lépjen ki a morog-rendszerhéjból, majd a következő paranccsal szerkessze a pigudf.py-fájlt a helyi fájlrendszerben:

    ```bash
    nano pigudf.py
    ```

5. A szerkesztőben a következő sor megjegyzésének törlésével törölje a `#` karaktert a sor elejéről:

    ```bash
    #from pig_util import outputSchema
    ```

    Ez a sor módosítja a Python-szkriptet, hogy a C Python használatával működjön a Jython helyett. A módosítás megtörténte után a **CTRL + X** billentyűkombinációval lépjen ki a szerkesztőből. Válassza az **Y**lehetőséget, majd az **ENTER billentyűt** a módosítások mentéséhez.

6. A `pig` parancs használatával indítsa újra a rendszerhéjat. Ha a `grunt>` promptot használja, a következő paranccsal futtassa a Python-szkriptet a C Python-értelmező használatával.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    A feladatok befejezése után ugyanazt a kimenetet kell látnia, mint amikor a szkriptet korábban a Jython használatával futtatta.

### <a name="upload-file-powershell"></a>Fájl feltöltése (PowerShell)

A PowerShell használható a kaptár-lekérdezések távoli futtatására is. Győződjön meg arról, hogy a munkakönyvtár a `pigudf.py` található.  A következő PowerShell-szkripttel futtathat egy `pigudf.py` parancsfájlt használó kaptár-lekérdezést:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>A Pig UDF használata (PowerShell)

> [!NOTE]  
> Amikor a PowerShell használatával távolról küld el egy feladatot, nem lehet a C Pythont használni tolmácsként.

A PowerShellt a Pig Latin feladatok futtatására is használhatja. A `pigudf.py` parancsfájlt használó Pig Latin-feladatok futtatásához használja a következő PowerShell-parancsfájlt:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A **Pig** -feladatokhoz tartozó kimenetnek a következő adatokhoz hasonlóan kell megjelennie:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="errors-when-running-jobs"></a>Hibák a feladatok futtatásakor

A kaptár-feladatok futtatásakor az alábbi szöveghez hasonló hibaüzenet jelenhet meg:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ezt a problémát a Python-fájlban végződő sorok okozzák. Számos Windows-szerkesztő alapértelmezés szerint a CRLF-t használja, de a Linux-alkalmazások általában a TT-t várnak.

A következő PowerShell-utasítások segítségével távolítsa el a CR-karaktereket, mielőtt feltölti a fájlt a HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-parancsfájlok

A példák futtatásához használt PowerShell-szkriptek közül mindkettő tartalmaz egy kommentált sort, amely a feladatokhoz tartozó hibaüzenetet jeleníti meg. Ha nem látja a feladathoz várt kimenetet, adja meg a következő sor megjegyzését, és ellenőrizze, hogy a hiba információi hibát jeleznek-e.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

A rendszer a hiba adatait (STDERR) és a feladatok eredményét (STDOUT) is naplózza a HDInsight-tárolóban.

| Ehhez a feladatokhoz... | Tekintse meg ezeket a fájlokat a blob-tárolóban |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Következő lépések

Ha olyan Python-modulokat kell betölteni, amelyek alapértelmezés szerint nem találhatók meg, tekintse meg [a modul üzembe helyezése az Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)című témakört.

A Pig, a kaptár és a MapReduce használatával kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hdinsight-use-mapreduce.md)
