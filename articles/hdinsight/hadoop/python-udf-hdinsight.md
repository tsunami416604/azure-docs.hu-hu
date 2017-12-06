---
title: "Python UDF az Apache Hive és a sertésfelmérés – az Azure HDInsight |} Microsoft Docs"
description: "Ismerje meg, Python felhasználó definiált függvény (UDF) a Hive és a Pig használata a Hdinsightban, a Hadoop technológiai területekre az Azure-on."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/05/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 002072c8eac37ffb1548b44627ec08e941c96a1d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Használjon Python felhasználói függvény (UDF), a Hive és a Pig a Hdinsightban

Útmutató Apache Hive és az Azure hdinsight Hadoop Pig Python felhasználói függvény (UDF) használhat.

## <a name="python"></a>A HDInsight Python

A HDInsight 3.0-s és újabb verziók alapértelmezés szerint telepítve van a Python2.7. Apache Hive használható ebben a Python adatfolyam feldolgozásra. Adatfolyam feldolgozása mechanizmusok adatok átadására a Hive és a UDF között használja az STDOUT és STDIN.

HDInsight is Jython, amely a Java nyelven írt Python megvalósítása. Jython közvetlenül a Java virtuálisgép fut, és nem használja a streaming. Jython a javasolt Python értelmező esetén a Pig használata a Python.

> [!WARNING]
> A jelen dokumentumban leírt lépések hajtsa végre a következő előfeltételek: 
>
> * A helyi fejlesztési környezet a Python parancsfájlok hoz létre.
> * A parancsfájlok HDInsight segítségével töltse fel a `scp` parancsot a helyi Bash munkamenet, vagy a megadott PowerShell-parancsfájlt.
>
> Ha szeretné használni a [Azure Cloud rendszerhéj (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) preview működéséhez a hdinsight eszközzel, majd be kell:
>
> * Hozzon létre a parancsfájlok a felhő rendszerhéj környezeten belül.
> * Használjon `scp` HDInsight a felhő rendszerhéjból a fájlok feltöltéséhez.
> * Használjon `ssh` a felhő rendszerhéjból HDInsight csatlakozni, és futtassa a példákat.

## <a name="hivepython"></a>Hive UDF-ben

Python változtatás nélkül használhatók a HiveQL a Hive a egy UDF `TRANSFORM` utasítást. Például a következő HiveQL meghívja a `hiveudf.py` a fürt az alapértelmezett Azure Storage-fiókban tárolt fájl.

**Linux-alapú HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**Windows-alapú HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Windows-alapú HDInsight-fürtök a `USING` záradék python.exe teljes elérési útja kell megadnia.

Ez a példa funkciója:

1. A `add file` a fájl elején utasítás hozzáadja a `hiveudf.py` az elosztott gyorsítótáras, így a fürt összes csomópontja által elérhető fájlt.
2. A `SELECT TRANSFORM ... USING` utasítás kiválasztja az adatokat a `hivesampletable`. A clientid, devicemake és devicemodel értékeket is átadja a `hiveudf.py` parancsfájl.
3. A `AS` záradék által visszaadott mezőket ismerteti `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>A hiveudf.py fájl létrehozása


A fejlesztési környezetet hozzon létre egy szövegfájlt nevű `hiveudf.py`. A fájl tartalmát az alábbira használata:

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

Ezt a parancsfájlt a következő műveleteket hajtja végre:

1. Egy adatsort STDIN olvasni.
2. A záró soremelés karaktert segítségével távolítja el `string.strip(line, "\n ")`.
3. Az adatfolyam feldolgozása során egy sorba tabulátor minden érték közötti minden a értékeket tartalmaz. Ezért `string.split(line, "\t")` segítségével ossza fel a bemenet minden egyes lapját, csak a mezőket ad vissza.
4. Ha feldolgozása befejeződött, a kimenetet kell írni STDOUT sortörés, egy lap a mezők között. Például: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. A `while` hurok ismétlődik, amíg nem `line` olvasható.

Parancsfájl eredménye a bemeneti érték a összefűzése `devicemake` és `devicemodel`, és a összefűzött érték kivonatát.

Lásd: [fut a példák](#running) ebben a példában a HDInsight-fürt futtatására.

## <a name="pigpython"></a>Pig UDF-ben

A Python-parancsfájl egy UDF a Pig keresztül használható a `GENERATE` utasítást. A parancsfájl Jython vagy C Python segítségével is futtathatja.

* Jython a JVM-et futtatja, és a Pig natív módon kell meghívni.
* C Python egy külső folyamatban, így a Pig a JVM-et a adatait egy Python folyamatban futó parancsfájl által kiküldött. A Python-parancsfájl újra üzembe a Pig zajlik.

Adja meg a Python értelmező `register` való hivatkozáskor a Python-parancsfájl. Az alábbi példák mint a Pig parancsfájlok regisztrálása `myfuncs`:

* **Jython használandó**:`register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python használandó**:`register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Jython használata esetén a fájl elérési útját pig_jython lehet-e, vagy helyi elérési utat, vagy a WASB: / / elérési út. C Python használata esetén egy fájlt a helyi fájlrendszerben, a csomópont, Ön által használt elküldeni a Pig feladatot kell hivatkoznia.

Egyszer túli regisztráció, a Pig Latin ehhez a példához megegyezik a is:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Ez a példa funkciója:

1. Az első sor betölti a mintaadatfájlokat `sample.log` történő `LOGS`. Mint minden rekordot is meghatározza egy `chararray`.
2. A következő sorban kiszűri a null értékeket, tárolja a művelet eredményét `LOG`.
3. A következő megismétli a blobban található rekordok keresztül `LOG` , és `GENERATE` meghívni a `create_structure` tölti be a Python/Jython parancsfájlban szereplő `myfuncs`. `LINE`a függvény az aktuális rekord átadására szolgál.
4. Végezetül a kimenetek vannak kiírt STDOUT való használata a `DUMP` parancsot. A parancs megjeleníti az eredményeket, a művelet befejeződése után.

### <a name="create-the-pigudfpy-file"></a>A pigudf.py fájl létrehozása

A fejlesztési környezetet hozzon létre egy szövegfájlt nevű `pigudf.py`. A fájl tartalmát az alábbira használata:

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

A Pig Latin a példában a `LINE` bemeneti egy chararray típusúként van definiálva, mert nincs a bemeneti konzisztens séma. A Python-parancsfájl az adatok átalakítja a kimeneti konzisztens marad.

1. A `@outputSchema` nyilatkozat meghatározása a Pig számára visszaadott adatok formátuma. Ebben az esetben van egy **adatok tulajdonságcsomag**, amely a Pig adattípus. A csomagban van, amelyek mindegyike chararray (karakterláncok), a következő mezőket tartalmazza:

   * dátum - naplófájlbejegyzést létrehozásának dátuma
   * idő - naplófájlbejegyzést létrehozásának időpontja
   * osztálynév - az osztály nevét a bejegyzés lett létrehozva
   * szint – a naplózási szint
   * Részletek - részletes adatait a naplóbejegyzés

2. Ezt követően a `def create_structure(input)` azt a Pig kapott sor elemek függvényt.

3. A példaadatokat `sample.log`, leginkább megfelel-e a dátum, idő, osztálynév szint, és részletességi séma. Azonban tartalmaz néhány kezdődő sorok `*java.lang.Exception*`. Ezek a sorok felel meg a sémának módosítani kell. A `if` utasítás ellenőrzi azok számára, majd a bemeneti adatok áthelyezése massages a `*java.lang.Exception*` karakterlánc végén, mihamarabb elérhetővé tenni az adatok beágyazott a várt kimeneti sémával.

4. Ezt követően a `split` parancs segítségével az adatok, az első négy karaktereket. A kimeneti hozzá van rendelve, a `date`, `time`, `classname`, `level`, és `detail`.

5. Végül az értékek Pig visszatér.

Amikor adatokat küld vissza a Pig, a rendelkezik konzisztens marad a `@outputSchema` utasítást.

## <a name="running"></a>Töltse fel, és futtassa a példák

> [!IMPORTANT]
> A **SSH** csak a lépések egy Linux-alapú HDInsight-fürthöz. A **PowerShell** lépéseket egy Linux vagy a Windows-alapú HDInsight-fürthöz működik, de a Windows-ügyfél szükséges.

### <a name="ssh"></a>SSH

További információ az SSH használatával, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Használjon `scp` átmásolni a fájlokat a HDInsight-fürthöz. Például a következő parancsot a másolja a fájlokat egy fürt nevű **sajátfürt**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. SSH használatával csatlakozhat a fürthöz.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    További információ: [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Az SSH-munkamenetet a python-fájlok a fürt WASB tárolóhelyét korábban feltöltött hozzá.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

A fájlok feltöltése után az alábbi lépések segítségével a Hive és a Pig feladatok futtatása.

#### <a name="use-the-hive-udf"></a>Az UDF-ben a Hive használata

1. Hive kapcsolódni a következő paranccsal:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    A Beeline ügyfél indítja el.

2. Adja meg a következő lekérdezés: a `0: jdbc:hive2://headnodehost:10001/>` parancssorba:

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Után utolsó sora bevitelét, a feladat elindul. Ha a feladat befejeződik, azt kimenetet visszaadja a következőhöz hasonló:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Beeline kilép, használja a következő parancsot:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>A Pig UDF használata

1. Ha csatlakozni szeretne a pig, a következő paranccsal:

    ```bash
    pig
    ```

2. Adja meg az alábbi utasításokat a `grunt>` parancssorba:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Írja be a következő parancsot, miután a feladat elindul. Ha a feladat befejeződik, az-kimenet visszaadása hasonló a következő adatokat:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Használjon `quit` kattintva lépjen ki a Grunt rendszerhéjat, és a következők segítségével szerkesztheti a pigudf.py fájlt a helyi fájlrendszerben:

    ```bash
    nano pigudf.py
    ```

5. Egyszer-szerkesztőben, állítsa vissza ezt a következő sor eltávolításával a `#` karakter eltávolítása a sor elejére:

    ```bash
    #from pig_util import outputSchema
    ```

    Ezt a sort a Python-parancsfájl használata helyett Jython C Python módosítja. A változás nem lett végrehajtva, ha a **Ctrl + X** a szerkesztőből való kilépéshez. Válassza ki **Y**, majd **Enter** menti a módosításokat.

6. Használja a `pig` indítsa újra a rendszerhéj parancsot. Miután a a `grunt>` kérni, használja a következő a Python-parancsfájl használata a C Python értelmező futtatásához.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Miután a feladat befejeződik, megtekintheti az azonos kimenethez, ha korábban már futtatta a parancsfájl segítségével történő Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: A fájl feltöltése

PowerShell segítségével töltse fel a fájlokat a HDInsight-kiszolgálóra. A következő parancsfájl használata a Python fájlok feltöltéséhez:

> [!IMPORTANT] 
> A jelen szakaszban szereplő lépéseket használhatja az Azure Powershellt. További információ az Azure PowerShell használatával, lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Módosítsa a `C:\path\to` értéket a fejlesztési környezetet a fájlok elérési útját.

Ezt a parancsfájlt a HDInsight-fürthöz adatainak beolvasása majd kibontja a fiók és az alapértelmezett tárfiók kulcsa, és feltölti a fájlok a tároló, a legfelső szintű.

> [!NOTE]
> Fájlok feltöltésével kapcsolatos további információkért lásd: a [feltölteni az adatokat a HDInsight Hadoop-feladatok](../hdinsight-upload-data.md) dokumentum.

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: A Hive UDF használata

PowerShell távolról ugyanúgy futtathatják a Hive-lekérdezéseket is használható. A következő PowerShell-parancsfájl segítségével használó Hive-lekérdezések futtatása **hiveudf.py** parancsfájlt:

> [!IMPORTANT]
> Előtt fut, a parancsprogram kéri a HTTPs/rendszergazdai fiók adatait a HDInsight-fürthöz.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

A kimenet a **Hive** feladatot az alábbi példához hasonlóan kell megjelennie:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell is használható a Pig Latin feladatok futtatásához. A Pig Latin feladat által használt futtatásához a **pigudf.py** parancsfájl, a következő PowerShell-parancsfájl:

> [!NOTE]
> Ha távolról elküld egy PowerShell-lel feladatot, nincs lehetőség a értelmező C Python használandó.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

A kimenet a **Pig** feladat a következő adatok hasonlóan kell megjelennie:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="errors-when-running-jobs"></a>Ha a futó feladatok hibák

A hive-feladatokban futtatásakor találkozhat az alábbihoz hasonló hiba:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ez a probléma a sorvégződések a Python-fájl okozhatja. Az CRLF a sor befejezési, de a Linux-alkalmazások általában számos Windows szerkesztők alapértelmezett LF várt.

A következő PowerShell-utasítások segítségével a CR karakter eltávolítása előtt a fájl feltöltése a HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-parancsfájlok

A példa a példák futtatásához használt PowerShell-parancsfájlok tartalmazniuk jeleníti meg a feladat kimenetének hiba megjegyzésként sorra. Ha nem Ön a feladat várható kimenete, állítsa vissza a következő sort, és tekintse meg, ha a hiba adataival kapcsolatos problémát jelez.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

A hibával kapcsolatos információk (STDERR) és a feladat (STDOUT) eredménye is bekerül a HDInsight-tárolóba.

| A feladat... | Nézze meg ezeket a fájlokat a blob a tárolóban |
| --- | --- |
| Hive |/ HivePython/stderr<p>/ HivePython/stdout |
| Pig |/ PigPython/stderr<p>/ PigPython/stdout |

## <a name="next"></a>Következő lépések

Ha alapértelmezés szerint nem biztosított Python-modulok betöltése szüksége, tekintse meg [központi telepítése egy modul Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Más használatának módjai sertésfelmérés, struktúra, és a MapReduce használatával kapcsolatos további tudnivalókért lásd a következő dokumentumokat:

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [Use MapReduce with HDInsight](hdinsight-use-mapreduce.md)
