---
title: Az Apache Hive- és Apache Pig - az Azure HDInsight UDF Python
description: Ismerje meg a Python felhasználói definiált függvények (UDF) az Apache Hive-és Apache Pig használata a HDInsight, az Apache Hadoop-technológiák az Azure-ban.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 02/27/2018
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 92221e5aaebbaebb2af17ea211e38a3665a2b04f
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652473"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Használható Python felhasználói függvények (UDF) az Apache Hive és a HDInsight Apache Pig

Ismerje meg, hogyan használható a Python felhasználó által definiált függvények (UDF) az Apache Hive- és Apache Hadoop on Azure HDInsight az Apache Pig.

## <a name="python"></a>Python a HDInsight

A HDInsight 3.0-s és újabb verziók alapértelmezés szerint telepítve van a Python2.7. Az Apache Hive használható Python ezen verziójával a streamfeldolgozáshoz. Stream-feldolgozás használja az STDOUT és az STDIN adatok átadására a Hive és a UDF között.

HDInsight Jython, amely egy Python-implementációja Java nyelven írt is tartalmaz. Jython közvetlenül a Java virtuális gépen fut, és nem használja a streaming. Jython javasolt a Python-fordítóra akkor, ha a Python és a Pig együttes használatával.

> [!WARNING]  
> A jelen dokumentumban leírt lépések hajtsa végre a következő előfeltételek: 
>
> * A Python-szkriptek hoz létre a helyi fejlesztési környezetbe.
> * Fel kell töltenie a parancsfájlokat a HDInsight segítségével a `scp` parancsot egy helyi Bash vagy a megadott PowerShell-parancsfájlt.
>
> Ha használni szeretné a [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) előzetes dolgozunk a HDInsight, majd be kell:
>
> * Hozzon létre a parancsfájlokat a cloud shell környezeten belül.
> * Használat `scp` HDInsight, a cloud shellben a fájlok feltöltéséről.
> * Használat `ssh` a HDInsight csatlakozhat, és futtassa a példák a cloud shellben.

## <a name="hivepython"></a>Az Apache Hive UDF-ben

Python is használható a Hive a HiveQL használatával egy UDF `TRANSFORM` utasítást. Ha például a következő HiveQL meghívja a `hiveudf.py` az a fürt alapértelmezett Azure Storage-fiókban tárolt fájlhoz.

**Linux-alapú HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**Windows-alapú HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]  
> Windows-alapú HDInsight-fürtökön a `USING` záradékának meg kell adnia python.exe teljes elérési útja.

Itt látható az ebben a példában leírása:

1. A `add file` hozzáadja a fájl elején utasítás a `hiveudf.py` fájlt az elosztott gyorsítótáras, hogy a fürt összes csomópontja által elérhető legyen.
2. A `SELECT TRANSFORM ... USING` utasítás kiválasztja az adatokat a `hivesampletable`. Továbbítja továbbá az clientid devicemake és devicemodel értékeket a `hiveudf.py` parancsfájlt.
3. A `AS` záradék által visszaadott mezők leírását `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>A hiveudf.py fájl létrehozása


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

1. Az adatok egy sor olvasható STDIN.
2. A záró soremelés karaktert el lett távolítva `string.strip(line, "\n ")`.
3. Adatfolyam-feldolgozó visszamehet egyetlen sor minden érték közötti lapon karakterrel az összes értékeket tartalmazza. Ezért `string.split(line, "\t")` minden lapját, csak a mezők visszaadása a bemeneti felosztása is használható.
4. Feldolgozás befejeződése után a kimenet kell megírni, hogy az STDOUT egyetlen vonal, egy lap, a mezők között. Például: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. A `while` hurok ismétlődik, amíg nem `line` olvasható.

A parancsfájl kimenete a bemeneti értékek összefűzésével `devicemake` és `devicemodel`, és a egy kivonatot az összefűzött érték.

Lásd: [példák futó](#running) futtatására ebben a példában a HDInsight-fürtön.

## <a name="pigpython"></a>Az Apache Pig UDF-ben

Egy Python-szkriptet egy UDF-ben, a Pig keresztül lehet használni a `GENERATE` utasítást. A parancsfájl Jython vagy C Python használatával futtathatja.

* Jython a JVM fut, és a Pig natív módon lehet hívni.
* C Python egy külső folyamatban, így a JVM a Pig származó adatok küldése a parancsfájlt egy Python-folyamatokhoz. A Python-szkript a kimenetét a rendszer újra üzembe a Pig küldi el.

Adja meg a Python-fordítóra `register` amikor hivatkozik a Python-szkriptet. Az alábbi példák parancsfájlok regisztrálja, a Pig `myfuncs`:

* **Jython használandó**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Használható a C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Jython használatakor a pig_jython fájl elérési útja lehet-e, vagy helyi elérési utat, vagy a WASB: / / elérési útja. C Python használata esetén a csomópont, amely a Pig-feladat elküldéséhez használja a helyi fájlrendszerben egy fájlt kell hivatkoznia.

Egyszer korábbi regisztráció, a Pig Latin ebben a példában megegyezik is:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Itt látható az ebben a példában leírása:

1. Az első sor betölti a mintaadatfájl `sample.log` be `LOGS`. Azt is meghatározza, minden rekord egy `chararray`.
2. A következő sorban kiszűri a bármely null értékeket, tárolásához, a művelet eredménye `LOG`.
3. Ezután azt ismétel a rekordok `LOG` és `GENERATE` meghívni a `create_structure` tölti be a Python/Jython szkriptnek metódus `myfuncs`. `LINE` a függvény az aktuális rekordot adnak át szolgál.
4. Végül a kimenetek a STDOUT vannak kiírt használatával a `DUMP` parancsot. Ez a parancs megjeleníti az eredményeket, a művelet befejezése után.

### <a name="create-the-pigudfpy-file"></a>A pigudf.py fájl létrehozása

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

## <a name="running"></a>Töltse fel, és futtassa a példákat

> [!IMPORTANT]  
> A **SSH** csak a lépések a Linux-alapú HDInsight-fürtökkel. A **PowerShell** lépések Linux vagy Windows-alapú HDInsight-fürttel működik, de egy Windows-ügyfél szükséges.

### <a name="ssh"></a>SSH

SSH használatával kapcsolatos további információkért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Használat `scp` a fájlok másolása a HDInsight-fürthöz. Ha például a következő parancs másolja a fájlokat nevű fürtben **sajátfürt**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Az SSH használatával csatlakozhat a fürthöz.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Az SSH-munkamenetből adjon hozzá a korábban feltöltött a fürt tárolóhelyét WASB python-fájlokat.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

A fájlok feltöltése után a következő lépések segítségével a Hive és Pig-feladatok futtatásához.

#### <a name="use-the-hive-udf"></a>Az UDF-ben a Hive használata

1. Ha csatlakozni szeretne, Hive, a következő paranccsal:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Ez a parancs elindítja a Beeline-ügyfél.

2. Írja be a következő lekérdezést, a `0: jdbc:hive2://headnodehost:10001/>` parancssorba:

   ```hive
   add file wasb:///hiveudf.py;
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

4. A Beeline kilép, használja a következő parancsot:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Az UDF-ben a Pig használata

1. Ha csatlakozni szeretne a pig, a következő paranccsal:

    ```bash
    pig
    ```

2. Adja meg a következő utasításokat a `grunt>` parancssorba:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
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
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Ez a feladat befejezése után megtekintheti az ugyanazzal a hibaüzenettel, ha korábban már futtatta a szkriptet Jython használatával.

### <a name="powershell-upload-the-files"></a>PowerShell: A fájlok feltöltése

A HDInsight-kiszolgáló a fájlok feltöltése a PowerShell használatával is. A Python-fájlok feltöltéséhez használja a következő szkriptet:

> [!IMPORTANT]   
> A jelen szakaszban ismertetett lépések az Azure PowerShell-lel. További információ az Azure PowerShell-lel: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Módosítsa a `C:\path\to` a fejlesztési környezetet a fájlok elérési útja értéket.

Ez a szkript lekéri a HDInsight-fürthöz, majd kinyeri a fiók és az alapértelmezett tárfiók kulcsa, és feltölti a fájlokat a tároló gyökérkönyvtárába.

> [!NOTE]  
> Fájlok feltöltése a további információkért lásd: a [Upload data for HDInsight az Apache Hadoop-feladatok](../hdinsight-upload-data.md) dokumentumot.

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: Az UDF-ben a Hive használata

PowerShell is használható távolról a Hive-lekérdezések futtatásához. A következő PowerShell-parancsfájl használatával futtathat Hive-lekérdezéseket használó **hiveudf.py** parancsfájlt:

> [!IMPORTANT]  
> Előtt fut, a parancsfájl kérni fogja, a HTTPs vagy rendszergazdai fiók adatainak a HDInsight-fürt számára.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

A kimenet a **Hive** feladat a következő példához hasonlóan kell megjelennie:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>A Pig (Jython)

PowerShell is használható a Pig Latin-feladatok futtatásához. A Pig Latin használó feladatok futtatásához a **pigudf.py** parancsfájl, használja a következő PowerShell-parancsfájlt:

> [!NOTE]  
> Távolról küldjön el egy PowerShell-lel feladat, nincs lehetőség használható a C Python értelmezőként.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

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
