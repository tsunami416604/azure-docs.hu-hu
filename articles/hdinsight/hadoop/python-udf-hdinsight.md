---
title: Az Apache Hive- és Apache Pig - az Azure HDInsight UDF Python
description: Ismerje meg a Python felhasználói definiált függvények (UDF) az Apache Hive-és Apache Pig használata a HDInsight, az Apache Hadoop-technológiák az Azure-ban.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: adcfb308bbbc8e3de456c4e7a71c543f988db02a
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497992"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Használható Python felhasználói függvények (UDF) az Apache Hive és a HDInsight Apache Pig

Ismerje meg, hogyan használható a Python felhasználó által definiált függvények (UDF) az Apache Hive- és Apache Hadoop on Azure HDInsight az Apache Pig.

## <a name="python"></a>Python on HDInsight

A HDInsight 3.0-s és újabb verziók alapértelmezés szerint telepítve van a Python2.7. Az Apache Hive használható Python ezen verziójával a streamfeldolgozáshoz. Stream-feldolgozás használja az STDOUT és az STDIN adatok átadására a Hive és a UDF között.

HDInsight Jython, amely egy Python-implementációja Java nyelven írt is tartalmaz. Jython közvetlenül a Java virtuális gépen fut, és nem használja a streaming. Jython javasolt a Python-fordítóra akkor, ha a Python és a Pig együttes használatával.

## <a name="prerequisites"></a>Előfeltételek

* **A HDInsight Hadoop-fürt**. Lásd: [HDInsight Linux első lépések](apache-hadoop-linux-tutorial-get-started.md).
* **Egy SSH-ügyfél**. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).
* A [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme) a fürtök elsődleges tárhelyeként. Ez akkor lehet wasb: / / az Azure Storage esetében abfs: / / az Azure Data Lake Storage Gen2 vagy adl: / / az Azure Data Lake Storage Gen1. Ha az Azure Storage vagy a Data Lake Storage Gen2 engedélyezve van a biztonságos átvitel, az URI a wasbs lesz: / / vagy abfss: / /, illetve lásd még a [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md).
* **Tárolási konfiguráció lehetséges módosítása.**  Lásd: [tárolási konfigurációt](#storage-configuration) a tárfiók típusának használatakor `BlobStorage`.
* Választható.  Ha tervezi a PowerShell-lel, szüksége lesz a [AZ modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) telepítve.

> [!NOTE]  
> Az ebben a cikkben használt tárfiók volt az Azure Storage [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md) engedélyezve van, és így `wasbs` a cikk használja.

## <a name="storage-configuration"></a>Tároló konfigurálása
Semmit nem kell, ha a használt tárfiók típusú `Storage (general purpose v1)` vagy `StorageV2 (general purpose v2)`.  Ebben a cikkben a folyamat legalább állítja elő a kimeneti `/tezstaging`.  Alapértelmezett konfigurációja a hadoop tartalmazni fogja `/tezstaging` a a `fs.azure.page.blob.dir` konfigurációs változó `core-site.xml` szolgáltatás `HDFS`.  Ez a konfiguráció hatására a kimeneti könyvtárba kell a lapblobokat, melyek a tárfióktípus nem támogatott `BlobStorage`.  Használandó `BlobStorage` ebben a cikkben eltávolítása `/tezstaging` származó a `fs.azure.page.blob.dir` konfigurációs változó.  A konfiguráció elérhető a [Ambari felhasználói felületén](../hdinsight-hadoop-manage-ambari.md).  Ellenkező esetben kapja meg a hibaüzenet: `Page blob is not supported for this account type.`

> [!WARNING]  
> A jelen dokumentumban leírt lépések hajtsa végre a következő előfeltételek:  
>
> * A Python-szkriptek hoz létre a helyi fejlesztési környezetbe.
> * Fel kell töltenie a parancsfájlokat a HDInsight segítségével a `scp` parancsot vagy a megadott PowerShell-parancsfájlt.
>
> Ha használni szeretné a [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) HDInsight dolgozni, majd a következőket kell tennie:
>
> * Hozzon létre a parancsfájlokat a cloud shell környezeten belül.
> * Használat `scp` HDInsight, a cloud shellben a fájlok feltöltéséről.
> * Használat `ssh` a HDInsight csatlakozhat, és futtassa a példák a cloud shellben.

## <a name="hivepython"></a>Apache Hive UDF

Python is használható a Hive a HiveQL használatával egy UDF `TRANSFORM` utasítást. Ha például a következő HiveQL meghívja a `hiveudf.py` az a fürt alapértelmezett Azure Storage-fiókban tárolt fájlhoz.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Itt látható az ebben a példában leírása:

1. A `add file` hozzáadja a fájl elején utasítás a `hiveudf.py` fájlt az elosztott gyorsítótáras, hogy a fürt összes csomópontja által elérhető legyen.
2. A `SELECT TRANSFORM ... USING` utasítás kiválasztja az adatokat a `hivesampletable`. Továbbítja továbbá az clientid devicemake és devicemodel értékeket a `hiveudf.py` parancsfájlt.
3. A `AS` záradék által visszaadott mezők leírását `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Fájl létrehozása

A fejlesztési környezetet hozzon létre egy szövegfájlt nevű `hiveudf.py`. Használja a fájl tartalmát a következő kódot:

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

1. Az adatok egy sort olvas STDIN.
2. A záró soremelés karaktert el lett távolítva `string.strip(line, "\n ")`.
3. Adatfolyam-feldolgozó visszamehet egyetlen sor minden érték közötti lapon karakterrel az összes értékeket tartalmazza. Ezért `string.split(line, "\t")` minden lapját, csak a mezők visszaadása a bemeneti felosztása is használható.
4. Feldolgozás befejeződése után a kimenet kell megírni, hogy az STDOUT egyetlen vonal, egy lap, a mezők között. Például: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. A `while` hurok ismétlődik, amíg nem `line` olvasható.

A parancsfájl kimenete a bemeneti értékek összefűzésével `devicemake` és `devicemodel`, és a egy kivonatot az összefűzött érték.

### <a name="upload-file-shell"></a>Töltse fel a fájlt (felület)
Cserélje le az alábbi parancsokat, `sshuser` Ha különböző tényleges felhasználónévvel.  Cserélje le `mycluster` tényleges fürt nevét.  Győződjön meg arról, a munkakönyvtárban történik, ahol a fájl megtalálható-e.

1. Használat `scp` a fájlok másolása a HDInsight-fürthöz. Szerkesztheti, és adja meg az alábbi parancsot:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Az SSH használatával csatlakozhat a fürthöz.  Szerkesztheti, és adja meg az alábbi parancsot:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből adjon hozzá a korábban feltöltött a fürt tárolóhelyét python-fájlokat.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Hive-UDF (felület) használata

1. Ha csatlakozni szeretne, Hive, nyissa meg az SSH-munkamenet következő parancsát használja:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Ez a parancs elindítja a Beeline-ügyfél.

2. Írja be a következő lekérdezést, a `0: jdbc:hive2://headnodehost:10001/>` parancssorba:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Miután megadta az utolsó sort, a feladat indításának. A feladat befejezése után, kimenetet ad vissza a következő példához hasonló:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. A Beeline kilép, adja meg a következő parancsot:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Töltse fel a fájlt (PowerShell)

> [!IMPORTANT]  
> Ezek a PowerShell-parancsfájlok nem fog működni, ha [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md) engedélyezve van.  Rendszerhéj-parancsokkal, vagy tiltsa le a biztonságos átvitel.

PowerShell is használható távolról a Hive-lekérdezések futtatásához. Győződjön meg, hogy a munkakönyvtárban where `hiveudf.py` található.  A következő PowerShell-parancsfájl használatával, amely egy Hive-lekérdezések futtatásához a `hiveudf.py` parancsfájlt:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

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
> Fájlok feltöltése a további információkért lásd: a [Upload data for HDInsight az Apache Hadoop-feladatok](../hdinsight-upload-data.md) dokumentumot.


#### <a name="use-hive-udf"></a>Use Hive UDF-ben


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

A kimenet a **Hive** feladat a következő példához hasonlóan kell megjelennie:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Apache Pig UDF

Egy Python-szkriptet egy UDF-ben, a Pig keresztül lehet használni a `GENERATE` utasítást. A parancsfájl Jython vagy C Python használatával futtathatja.

* Jython a JVM fut, és a Pig natív módon lehet hívni.
* C Python egy külső folyamatban, így a JVM a Pig származó adatok küldése a parancsfájlt egy Python-folyamatokhoz. A Python-szkript a kimenetét a rendszer újra üzembe a Pig küldi el.

Adja meg a Python-fordítóra `register` amikor hivatkozik a Python-szkriptet. Az alábbi példák parancsfájlok regisztrálja, a Pig `myfuncs`:

* **Jython használandó**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Használható a C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Jython használatakor a pig_jython fájl elérési útja lehet-e, vagy helyi elérési utat, vagy egy WASBS: / / elérési útja. C Python használata esetén a csomópont, amely a Pig-feladat elküldéséhez használja a helyi fájlrendszerben egy fájlt kell hivatkoznia.

Egyszer korábbi regisztráció, a Pig Latin ebben a példában megegyezik is:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Itt látható az ebben a példában leírása:

1. Az első sor betölti a mintaadatfájl `sample.log` be `LOGS`. Azt is meghatározza, minden rekord egy `chararray`.
2. A következő sorban kiszűri a bármely null értékeket, tárolásához, a művelet eredménye `LOG`.
3. Ezután azt ismétel a rekordok `LOG` és `GENERATE` meghívni a `create_structure` tölti be a Python/Jython szkriptnek metódus `myfuncs`. `LINE` a függvény az aktuális rekordot adnak át szolgál.
4. Végül a kimenetek a STDOUT vannak kiírt használatával a `DUMP` parancsot. Ez a parancs megjeleníti az eredményeket, a művelet befejezése után.

### <a name="create-file"></a>Fájl létrehozása

A fejlesztési környezetet hozzon létre egy szövegfájlt nevű `pigudf.py`. Használja a fájl tartalmát a következő kódot:

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

A Pig Latin a példában a `LINE` bemenet egy chararray típusúként van definiálva, mert nincs a bemeneti konzisztens séma. A Python-szkriptet az adatok alakítja a kimeneti konzisztens sémát.

1. A `@outputSchema` utasítás, amely a Pig visszaadott adatok formátumát határozza meg. Ebben az esetben van egy **adatok tulajdonságcsomag**, amely a Pig adattípus. A tulajdonságcsomag, amelyek mindegyike chararray (karakterlánc), a következő mezőket tartalmazza:

   * dátum – a naplóbejegyzés dátuma
   * idő – a a naplóbejegyzés létrehozásának ideje
   * osztálynév - az osztály nevét a bejegyzés lett létrehozva
   * szint – a naplózási szint
   * Részletek – részletes adatainak a naplóbejegyzés

2. Ezután a `def create_structure(input)` határozza meg a függvényt, amely a Pig továbbítja a sor-elemek.

3. A példaadatokat `sample.log`, leginkább megfelel-e a dátum, idő, osztályneve, szint, és részletes információkat talál a sémát. Azonban néhány kezdődő sorokat tartalmaz `*java.lang.Exception*`. Ezek a sorok módosítani kell, hogy felel meg a sémának. A `if` utasítás ellenőrzi azok számára, majd a bemeneti adatok áthelyezése massages a `*java.lang.Exception*` karakterlánc végén, így az adatok beágyazott a várt kimeneti sémával.

4. Ezután a `split` parancs felosztani az adatokat az első négy területet karakter használható. A kimenet van hozzárendelve `date`, `time`, `classname`, `level`, és `detail`.

5. Végül az értékek visszakerülnek a Pig.

Amikor az adatok visszakerülnek a Pig, a konzisztens sémák rendelkezik a `@outputSchema` utasítást.



### <a name="upload-file-shell"></a>Töltse fel a fájlt (felület)

Cserélje le az alábbi parancsokat, `sshuser` Ha különböző tényleges felhasználónévvel.  Cserélje le `mycluster` tényleges fürt nevét.  Győződjön meg arról, a munkakönyvtárban történik, ahol a fájl megtalálható-e.

1. Használat `scp` a fájlok másolása a HDInsight-fürthöz. Szerkesztheti, és adja meg az alábbi parancsot:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Az SSH használatával csatlakozhat a fürthöz.  Szerkesztheti, és adja meg az alábbi parancsot:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Az SSH-munkamenetből adjon hozzá a korábban feltöltött a fürt tárolóhelyét python-fájlokat.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>A Pig UDF (felület) használata

1. Ha csatlakozni szeretne a pig, nyissa meg az SSH-munkamenet következő parancsát használja:

    ```bash
    pig
    ```

2. Adja meg a következő utasításokat a `grunt>` parancssorba:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Miután megadta a következő sort, a feladat indításának. A feladat befejezése után, hasonló kimenetet ad vissza a következő adatokat:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Használjon `quit` való kilépéshez a Grunt rendszerhéjat, majd használja a helyi fájlrendszerben pigudf.py fájl szerkesztése a következő:

    ```bash
    nano pigudf.py
    ```

5. Egyszer a szerkesztőben, állítsa vissza ezt a következő sor eltávolításával a `#` karakter, a sor elejére:

    ```bash
    #from pig_util import outputSchema
    ```

    Ez a sor Jython helyett C Python használata a Python-szkript módosítja. A változás nem lett végrehajtva, ha az **Ctrl + X** a szerkesztőből való kilépéshez. Válassza ki **Y**, majd **Enter** menti a módosításokat.

6. Használja a `pig` paranccsal indítsa újra a rendszerhéjat. Ha már a `grunt>` kéri, használja az alábbi parancsot a Python-szkript használata a C Python-fordítóra futtatásához.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Ez a feladat befejezése után megtekintheti az ugyanazzal a hibaüzenettel, ha korábban már futtatta a szkriptet Jython használatával.


### <a name="upload-file-powershell"></a>Töltse fel a fájlt (PowerShell)

> [!IMPORTANT]  
> Ezek a PowerShell-parancsfájlok nem fog működni, ha [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md) engedélyezve van.  Rendszerhéj-parancsokkal, vagy tiltsa le a biztonságos átvitel.

PowerShell is használható távolról a Hive-lekérdezések futtatásához. Győződjön meg, hogy a munkakönyvtárban where `pigudf.py` található.  A következő PowerShell-parancsfájl használatával, amely egy Hive-lekérdezések futtatásához a `pigudf.py` parancsfájlt:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

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

### <a name="use-pig-udf-powershell"></a>A Pig használata UDF (PowerShell)

> [!NOTE]  
> Távolról küldjön el egy PowerShell-lel feladat, nincs lehetőség használható a C Python értelmezőként.

PowerShell is használható a Pig Latin-feladatok futtatásához. A Pig Latin használó feladatok futtatásához a `pigudf.py` parancsfájl, használja a következő PowerShell-parancsfájlt:

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

A kimenet a **Pig** feladat a következő adatokhoz hasonlóan kell megjelennie:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="errors-when-running-jobs"></a>Ha a futó feladatok hibák

Amikor futtatja a hive-feladatokban, találkozhat az alábbi szöveghez hasonló hibát:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ez a probléma oka a sorvégződések a Python-fájlt a. A CRLF karakterrel, a sor vége, de a Linux-alkalmazások általában számos Windows szerkesztők alapértelmezett várhatóan LF Karakterrel.

A következő PowerShell-utasítások segítségével a CR karakter eltávolítása előtt a fájl feltöltése a HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-parancsprogramok

Mind a példa PowerShell-parancsfájlok futtatásához a példákban használt tartalmaz, amely megjeleníti a feladat hibakimenet megjegyzéssel ellátott vonal. Ha nem jelennek meg a feladat várható kimenete, állítsa vissza a következő sort, és tekintse meg, ha a hiba adatait kapcsolatos problémát jelez.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

A hiba adatait (STDERR) és a feladat (STDOUT) eredménye is rögzíti a HDInsight-tárolóba.

| A feladathoz... | Tekintse meg ezeket a fájlokat a blob-tárolóban |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Következő lépések

Ha alapértelmezés szerint nem biztosított Python-modulok betöltése van szüksége, tekintse meg [modul üzembe helyezése az Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Más használati módjai a Pig-alapú, Hive, és a MapReduce használatával kapcsolatos további információkért lásd az alábbi dokumentumokat:

* [Az Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hdinsight-use-mapreduce.md)
