---
title: Python UDF Apache Hive-val és Apache Pig-el – Azure HDInsight
description: Ismerje meg, hogyan használhatja a Python-felhasználó által definiált függvényeket (UDF) az Apache Hive-ből és az Apache Pig-ből a HDInsightban, az Apache Hadoop technológiai veremben az Azure-ban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 201bb40e5024442587f5508886da7e844f35be40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74148404"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Python-felhasználó által definiált függvények (UDF) használata Apache Hive-val és Apache Pig-rel a HDInsightban

Ismerje meg, hogyan használhatja a Python-felhasználó által definiált függvényeket (UDF) az Apache Hive-vel és az Apache Pig-lel az Apache Hadoop ban az Azure HDInsight-on.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python a HDInsight-on

A Python2.7 alapértelmezés szerint telepítve van a HDInsight 3.0-s és újabb verzióira. Az Apache Hive a Python ezen verziójával használható streamfeldolgozáshoz. Stream feldolgozás a STDOUT és STDIN adatok átad a Hive és az UDF között.

A HDInsight tartalmazza a Jythont is, amely egy Java nyelven írt Python-implementáció. A Jython közvetlenül a Java virtuális gépen fut, és nem használja a streamelést. A Jython az ajánlott Python-értelmező a Python és a Pig használatakor.

## <a name="prerequisites"></a>Előfeltételek

* **Hadoop-fürt a HDInsighton.** Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](apache-hadoop-linux-tutorial-get-started.md)
* **Egy SSH-ügyfél**. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)
* A fürtök [elsődleges tárolóURI-séma.](../hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez az `wasb://` Azure Storage, `abfs://` az Azure Data Lake Storage Gen2 vagy adl:// az Azure Data Lake Storage Gen1 esetében lenne. Ha az Azure Storage biztonságos átvitele engedélyezve van, az URI wasbs:// lesz.  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).
* **Lehetséges változás a tárolási konfigurációra.**  Lásd: [Tárolási konfiguráció,](#storage-configuration) `BlobStorage`ha tárfiók-típusú.
* Választható.  Ha a PowerShell használatát tervezi, telepítenie kell az [AZ modult.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

> [!NOTE]  
> A cikkben használt tárfiók az [secure transfer](../../storage/common/storage-require-secure-transfer.md) Azure Storage `wasbs` biztonságos átvitel engedélyezve van, és így a cikk egészében.

## <a name="storage-configuration"></a>Tároló konfigurálása

Nincs szükség műveletre, ha a `Storage (general purpose v1)` használt `StorageV2 (general purpose v2)`tárfiók fajta vagy .  A folyamat ebben a cikkben `/tezstaging`fog kimenetet legalább .  Az alapértelmezett `/tezstaging` hadoop-konfiguráció `fs.azure.page.blob.dir` a szolgáltatás `core-site.xml` `HDFS`konfigurációs változójában jelenik meg.  Ez a konfiguráció hatására a címtár kimenete lapblobok, amelyek `BlobStorage`nem támogatottak a tárfiók-típusú.  A `BlobStorage` cikkhez való `/tezstaging` használathoz `fs.azure.page.blob.dir` távolítsa el a konfigurációs változót.  A konfiguráció az [Ambari felhasználói felületről](../hdinsight-hadoop-manage-ambari.md)érhető el.  Ellenkező esetben a következő hibaüzenet jelenik meg:`Page blob is not supported for this account type.`

> [!WARNING]  
> A jelen dokumentum lépései a következő feltételezéseket követik:  
>
> * A Python-parancsfájlokat a helyi fejlesztési környezetben hozza létre.
> * A parancsfájlokat a HDInsightba `scp` a parancs vagy a megadott PowerShell-parancsfájl használatával töltheti fel.
>
> Ha az Azure [Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) használatával szeretne dolgozni a HDInsight szolgáltatással, akkor a következőket kell tennie:
>
> * Hozza létre a parancsfájlokat a felhőhéj-környezetben.
> * A `scp` felhőalapú rendszerhéjból a HDInsightba töltheti fel a fájlokat.
> * Használja `ssh` a felhőshell csatlakozni HDInsight és a példák futtatásához.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python használható UDF-ként a Hive a HiveQL `TRANSFORM` utasításon keresztül. Például a következő HiveQL `hiveudf.py` meghívja a fürt alapértelmezett Azure Storage-fiókjában tárolt fájlt.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

A példa a következőket teszi:

1. A `add file` fájl elején lévő utasítás `hiveudf.py` hozzáadja a fájlt az elosztott gyorsítótárhoz, így a fürt összes csomópontja számára elérhető.
2. Az `SELECT TRANSFORM ... USING` utasítás adatokat `hivesampletable`választ ki a ból. Azt is átadja a clientid, devicemake és `hiveudf.py` devicemodel értékeket a parancsfájlba.
3. A `AS` záradék a rendszerből `hiveudf.py`visszaadott mezőket írja le.

<a name="streamingpy"></a>

### <a name="create-file"></a>Fájl létrehozása

A fejlesztői környezetben hozzon `hiveudf.py`létre egy szövegfájlt. A fájl tartalmaként a következő kódot használja:

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

Ez a parancsfájl a következő műveleteket hajtja végre:

1. Beolvas egy adatsort az STDIN-ből.
2. A záró newline karakter `string.strip(line, "\n ")`a használatával törlődik.
3. Az adatfolyam-feldolgozás során egyetlen sor tartalmazza az egyes értékek között tabulátorkaraktert tartalmazó összes értéket. Így `string.split(line, "\t")` lehet használni, hogy osztja a bemenet minden lapon, visszacsak a mezőket.
4. Ha a feldolgozás befejeződött, a kimenetet egyetlen sorként kell írni az STDOUT-ba, az egyes mezők között egy laplal. Például: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. A `while` hurok addig `line` ismétlődik, amíg nem olvasható.

A parancsfájl kimenete az összefont `devicemake` `devicemodel`érték és a bemeneti érték bemeneti értékeinek összefűzése.

### <a name="upload-file-shell"></a>Fájl feltöltése (rendszerhéj)

Az alábbi parancsokban `sshuser` cserélje le a tényleges felhasználónevet, ha más.  Cserélje `mycluster` le a tényleges fürtnévre.  Győződjön meg arról, hogy a munkakönyvtár a fájl helye.

1. A `scp` fájlokat a HDInsight-fürtbe másolhatja. Edit és adja meg az alábbi parancsot:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Az SSH segítségével csatlakozzon a fürthöz.  Edit és adja meg az alábbi parancsot:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből adja hozzá a korábban feltöltött python-fájlokat a fürt tárolójába.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Hive UDF (rendszerhéj) használata

1. A Hive-hoz való csatlakozáshoz használja a következő parancsot a megnyitott SSH-munkamenetből:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Ez a parancs elindítja a Beeline ügyfelet.

2. Írja be a `0: jdbc:hive2://headnodehost:10001/>` következő lekérdezést a kérdésben:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Az utolsó sor megadása után a feladatnak el kell kezdődnie. A feladat befejezése után a következő példához hasonló kimenetet ad vissza:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. A Beeline-ból való kilépéshez írja be a következő parancsot:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Fájl feltöltése (PowerShell)

A PowerShell a Hive-lekérdezések távoli futtatására is használható. Győződjön meg arról, hogy a munkakönyvtár a hely. `hiveudf.py`  A következő PowerShell-parancsfájl használatával futtasson `hiveudf.py` egy Hive-lekérdezést, amely a parancsfájlt használja:

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
> A fájlok feltöltésével kapcsolatos további információkért tekintse meg az [Apache Hadoop-feladatok feltöltési adatait a HDInsight-dokumentumban.](../hdinsight-upload-data.md)

#### <a name="use-hive-udf"></a>Hive UDF használata

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

A Hive-feladat kimenetének a következő példához hasonlóan kell **megjelennie:**

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache Sertés UDF

A Python-parancsfájl udf-ként használható a `GENERATE` Pig-ből a utasításon keresztül. Futtathatja a parancsfájlt a Jython vagy a C Python használatával.

* Jython fut a JVM, és natívan lehet hívni a Pig.
* C Python egy külső folyamat, így a Pig a JVM-en lévő adatokat küldi ki a python-folyamatban futó parancsfájlnak. A Python-parancsfájl kimenete visszakerül a Pig-be.

A Python-értelmező `register` megadásához használja a Python-parancsfájlhivatkozáshoz. A következő példák regisztrálja `myfuncs`scriptek Pig mint:

* **A Jython használata:**`register '/path/to/pigudf.py' using jython as myfuncs;`
* **A C Python használata:**`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> A Jython használatakor a pig_jython fájl elérési útja lehet helyi vagy WASBS:// elérési út. A C Python használatakor azonban hivatkoznia kell egy fájlra a Pig-feladat elküldéséhez használt csomópont helyi fájlrendszerében.

Miután a korábbi regisztráció, a Pig Latin ebben a példában ugyanaz mind:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

A példa a következőket teszi:

1. Az első sor betölti `sample.log` a `LOGS`mintaadatfájlt a rendszerbe. Azt is meghatározza, hogy `chararray`minden rekord a .
2. A következő sor kiszűri a null értékeket, `LOG`és a művelet eredményét a ba tárolja.
3. Ezután `LOG` iterálja a rekordokat, `GENERATE` és `create_structure` a Python/Jython parancsfájlban található `myfuncs`metódus meghívására használja. `LINE`az aktuális rekord függvénynek való átadására szolgál.
4. Végül a kimenetek a `DUMP` parancs használatával kerülnek az STDOUT-ba. Ez a parancs a művelet befejezése után jeleníti meg az eredményeket.

### <a name="create-file"></a>Fájl létrehozása

A fejlesztői környezetben hozzon `pigudf.py`létre egy szövegfájlt. A fájl tartalmaként a következő kódot használja:

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

A Pig Latin példában a `LINE` bemenet ichararray ként van definiálva, mert nincs konzisztens séma a bemenethez. A Python-parancsfájl az adatokat egy konzisztens kimeneti sémává alakítja.

1. Az `@outputSchema` utasítás a Pig-nek visszaadott adatok formátumát határozza meg. Ebben az esetben ez egy **adatcsomag**, amely egy Pig adattípus. A táska a következő mezőket tartalmazza, amelyek mindegyike chararray (karakterlánc):

   * dátum - a naplóbejegyzés létrehozásának dátuma
   * idő - a naplóbejegyzés létrehozásának időpontja
   * osztálynév - az az osztálynév, amelyhez a bejegyzés készült
   * szint - a napló szintje
   * részletek - részletes részletek a naplóbejegyzéshez

2. Ezután `def create_structure(input)` határozza meg azt a függvényt, amelynek a Pig átadja a sorokat.

3. A példaadatok `sample.log`( , . . . ) többnyire megfelelnek a dátum-, idő-, osztálynév,szint- és részletsémának. Azonban tartalmaz néhány sort, hogy `*java.lang.Exception*`kezdődik . Ezeket a sorokat módosítani kell, hogy megfeleljenek a sémának. A `if` kimutatás ellenőrzi azokat, majd masszírozza a bemeneti adatokat a `*java.lang.Exception*` karakterlánc végére, így az adatok összhangban a várt kimeneti séma.

4. Ezután `split` a parancs az adatok felosztására szolgál az első négy szóközkarakternél. A kimenet hozzá `date`van `time` `classname`rendelve a , , , , `level`, és `detail`.

5. Végül az értékek visszakerülnek a Pig-be.

Amikor az adatokat visszaadja a Pig, azt egy egységes `@outputSchema` séma az utasításban meghatározott.

### <a name="upload-file-shell"></a>Fájl feltöltése (rendszerhéj)

Az alábbi parancsokban `sshuser` cserélje le a tényleges felhasználónevet, ha más.  Cserélje `mycluster` le a tényleges fürtnévre.  Győződjön meg arról, hogy a munkakönyvtár a fájl helye.

1. A `scp` fájlokat a HDInsight-fürtbe másolhatja. Edit és adja meg az alábbi parancsot:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Az SSH segítségével csatlakozzon a fürthöz.  Edit és adja meg az alábbi parancsot:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből adja hozzá a korábban feltöltött python-fájlokat a fürt tárolójába.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Sertés UDF (héj) használata

1. A sertéshez való csatlakozáshoz használja a következő parancsot a megnyitott SSH-munkamenetből:

    ```bash
    pig
    ```

2. Írja be a következő `grunt>` utasításokat a kérdésre:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. A következő sor megadása után a feladatnak el kell kezdődnie. A feladat befejezése után a következő adatokhoz hasonló kimenetet ad vissza:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. A `quit` morgásrendszerből való kilépéshez, majd az alábbiak hoz a pigudf.py fájl szerkesztéséhez a helyi fájlrendszeren:

    ```bash
    nano pigudf.py
    ```

5. Miután a szerkesztő, uncomment a következő `#` sort eltávolításával a karakter elejéről a sor:

    ```bash
    #from pig_util import outputSchema
    ```

    Ez a sor módosítja a Python-szkriptet, hogy a Jython helyett a C Pythonnal működjön. A módosítás után a **Ctrl+X** billentyűkombinációval lépjen ki a szerkesztőből. A módosítások mentéséhez válassza az **Y**, majd az **Enter lehetőséget.**

6. A `pig` parancs segítségével indítsa újra a rendszerhéjat. Miután a `grunt>` parancssorba ért, a következő használatával futtassa a Python-parancsfájlt a C Python-értelmező használatával.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Miután ez a feladat befejeződött, ugyanazt a kimenetet kell látnia, mint amikor korábban futtatta a parancsfájlt a Jython használatával.

### <a name="upload-file-powershell"></a>Fájl feltöltése (PowerShell)

A PowerShell a Hive-lekérdezések távoli futtatására is használható. Győződjön meg arról, hogy a munkakönyvtár a hely. `pigudf.py`  A következő PowerShell-parancsfájl használatával futtasson `pigudf.py` egy Hive-lekérdezést, amely a parancsfájlt használja:

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

### <a name="use-pig-udf-powershell"></a>A Pig UDF (PowerShell) használata

> [!NOTE]  
> PowerShell használatával távolról küldött el egy feladatot, nem lehet a C Pythont értelmezőként használni.

A PowerShell a Pig Latin-feladatok futtatására is használható. A `pigudf.py` parancsfájlt használó Pig Latin-feladat futtatásához használja a következő PowerShell-parancsfájlt:

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

A **Pig** feladat kimenetének a következő adatokhoz hasonlóan kell megjelennie:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a><a name="troubleshooting"></a>hibaelhárítással

### <a name="errors-when-running-jobs"></a>Hibák feladatok futtatásakor

A struktúrafeladat futtatásakor a következő höz hasonló hibaüzenet jelenhet meg:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ezt a problémát a Python-fájl sorvégződései okozhatják. Sok Windows-szerkesztő alapértelmezés szerint a CRLF-et használja sorvégeként, de a Linux alkalmazások általában LF-et várnak.

A következő PowerShell-utasításokkal távolíthatja el a CR-karaktereket, mielőtt feltöltené a fájlt a HDInsightba:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-parancsprogramok

Mindkét példa PowerShell-parancsfájlok futtatásához használt példák tartalmaznak egy megjegyzéssor, amely megjeleníti a feladat hibakimenetét. Ha nem látja a feladat várt kimenetét, ne fűzze ki a következő sort, és nézze meg, hogy a hibainformációk problémát jeleznek-e.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

A hibainformációk (STDERR) és a feladat eredménye (STDOUT) is naplózva vannak a HDInsight-tárolóba.

| Ezért a munkáért... | Tekintse meg ezeket a fájlokat a blob tárolóban |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>További lépések

Ha olyan Python-modulokat kell betöltenie, amelyek alapértelmezés szerint nincsenek megadva, olvassa el [a Modul üzembe helyezése az Azure HDInsight szolgáltatásba.](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)

A Pig, Hive használatának és a MapReduce használatának további módjairól az alábbi dokumentumokban olvashat:

* [Az Apache Hive használata a HDInsight segítségével](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hdinsight-use-mapreduce.md)
