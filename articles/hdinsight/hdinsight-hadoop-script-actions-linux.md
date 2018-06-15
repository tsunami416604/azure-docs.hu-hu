---
title: Parancsfájl-művelet fejlesztése a Linux-alapú HDInsight - Azure |} Microsoft Docs
description: Megtudhatja, hogyan Bash parancsfájlok használata a Linux-alapú HDInsight-fürtök testreszabása. A HDInsight a parancsfájl művelet a szolgáltatás lehetővé teszi a parancsfájlok futtatása közben, vagy a fürt létrehozása után. Parancsfájlok segítségével fürt konfigurációs beállításokat módosítaná, vagy további szoftvereket telepíteni.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: d5df67021e997df3a6344701f50be4871a11386d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406030"
---
# <a name="script-action-development-with-hdinsight"></a>Parancsfájl művelet fejlesztése a Hdinsighttal

Ismerje meg, hogyan szabhatja testre a Bash parancsfájlok használata a HDInsight-fürthöz. A Parancsfájlműveletek olyan testreszabhatja a HDInsight alatt vagy a fürt létrehozása után.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>Mik azok a Parancsfájlműveletek

A Parancsfájlműveletek olyan Bash parancsfájlok futó Azure a fürtcsomópontokon ellenőrizze konfigurációs módosításokat, vagy a szoftver telepítése. A parancsfájlművelet legfelső szintű végrehajtásakor, és a fürtcsomópontok teljes körű hozzáférési jogosultságokat biztosít.

A Parancsfájlműveletek az alábbi eljárások alkalmazhatók:

| Ezt a módszert használja egy parancsfájl alkalmazandó... | Során fürt létrehozása... | Egy futó fürtön... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure CLI 1.0 |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Az Azure Resource Manager-sablon |✓ |&nbsp; |

Ezekkel a módszerekkel Parancsfájlműveletek alkalmazandó további információkért lásd: [testreszabása HDInsight-fürtök Parancsfájlműveletek segítségével](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Parancsfájl fejlesztési ajánlott eljárásai

A HDInsight-fürtök egyéni parancsfájl fejlesztésekor van több bevált gyakorlatokat, amelyekkel tartsa szem előtt:

* [A cél a Hadoop-verzió](#bPS1)
* [A cél az operációs rendszer verziója](#bps10)
* [Tartalmaznak egy stabil parancsfájl erőforrások](#bPS2)
* [Előre lefordított erőforrások használata](#bPS4)
* [Győződjön meg arról, hogy a fürt testreszabási parancsfájl idempotent](#bPS3)
* [Magas rendelkezésre állásának a fürt-architektúra](#bPS5)
* [Az Azure Blob storage használata egyéni összetevők konfigurálása](#bPS6)
* [Kapcsolatos adatokat ír az STDOUT és az STDERR](#bPS7)
* [ASCII LF sorvégződések rendelkező fájlok mentése](#bps8)
* [Használja az újrapróbálkozási logika átmeneti hibák elhárítása](#bps9)

> [!IMPORTANT]
> A Parancsfájlműveletek 60 percen belül kell végrehajtani, vagy a folyamat sikertelen lesz. Csomópont telepítése során, a parancsfájl fut egyidejűleg más beállítás és konfiguráció folyamatok. Erőforrások, például a CPU-idő vagy a hálózati sávszélesség erőforrásokért való versengés okozhat a parancsfájl a befejezéshez, mint a fejlesztési környezetet az hosszabb időt vesz igénybe.

### <a name="bPS1"></a>A cél a Hadoop-verzió

Különböző verzióit a HDInsight Hadoop-szolgáltatások és telepített összetevők különböző verziói működnek. Ha a parancsfájl egy szolgáltatás vagy összetevő adott verziójának vár, csak akkor ajánlott a parancsfájl, amely tartalmazza a szükséges összetevők HDInsight verziójával. HDInsight használatával mellékelt összetevő verzióin információt a [HDInsight-összetevők verziószámozása](hdinsight-component-versioning.md) dokumentum.

### <a name="bps10"></a> A cél az operációs rendszer verziója

Linux-alapú HDInsight az Ubuntu Linux terjesztési alapul. HDInsight különböző verziói különböző verzióinak Ubuntu, módosíthatja a parancsfájl működését támaszkodnak. HDInsight 3.4 és korábbi például Ubuntu verziók Upstart használó alapul. 3.5-ös vagy újabb verzió Ubuntu 16.04, amely használja Systemd alapulnak. Systemd és Upstart támaszkodnak különböző parancsok, így a parancsfájlt is együttműködve kell írni.

HDInsight 3.4 és 3.5-ös közötti másik fontos különbség, hogy `JAVA_HOME` Java 8 most mutat.

Az operációs rendszer verzióját használatával ellenőrizheti `lsb_release`. A következő kód bemutatja, hogyan állapítható meg, ha a parancsfájl Ubuntu 14 vagy 16 fut:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

A teljes parancsfájl, amely tartalmazza ezeket a kódtöredékek található https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Ubuntu, a HDInsight által használt verziója: a [HDInsight összetevő verziószáma](hdinsight-component-versioning.md) dokumentum.

Systemd és Upstart közötti különbségek ismertetése: [Systemd Upstart felhasználók](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Tartalmaznak egy stabil parancsfájl erőforrások

A parancsfájl és a kapcsolódó erőforrások a fürt élettartama során rendelkezésre kell állnia. Ezeket az erőforrásokat szükség, ha új csomópontokat ad hozzá a fürthöz skálázás műveletek során.

Az ajánlott eljárás, hogy töltse le és archiválni mindent az Azure Storage-fiók előfizetéséhez.

> [!IMPORTANT]
> A használt tárfiók más storage-fiók a fürt vagy egy nyilvános, csak olvasható tároló alapértelmezett tárfiókot kell lennie.

Például a Microsoft által biztosított minták tárolódnak a [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) tárfiók. Ez a hely egy olyan nyilvános, csak olvasható tároló, a HDInsight csapat által fenntartott.

### <a name="bPS4"></a>Előre lefordított erőforrások használata

A parancsfájl futtatásához szükséges idő csökkentése érdekében ne erőforrások a forráskód fordítási műveletek. Például előre lefordítani az erőforrásokat, és tárolja őket egy Azure Storage-fiók BLOB a HDInsight azonos adatközpontba.

### <a name="bPS3"></a>Győződjön meg arról, hogy a fürt testreszabási parancsfájl idempotent

Parancsfájlok idempotent kell lennie. Ha a parancsfájl több alkalommal fut, akkor térjen vissza a fürt olyan állapotban minden alkalommal.

Például egy parancsfájlt, amely módosítja a konfigurációs fájlok ne adja hozzá duplikált bejegyzéseket Ha több alkalommal lefutott.

### <a name="bPS5"></a>Magas rendelkezésre állásának a fürt-architektúra

Linux-alapú HDInsight-fürtök meg két átjárócsomópontokkal aktív, a fürtön belül, és Parancsfájlműveletek mindkét csomópontján futtatni. Ha az összetevők telepítése csak egy átjárócsomópont fognak, az összetevők telepítésének mindkét központi csomópontján.

> [!IMPORTANT]
> HDInsight részeként szolgáltatást tervezték, hogy igény szerint, a két központi csomópontok közötti feladatátvételt. Ez a funkció nincs kibővítve a Parancsfájlműveletek segítségével egyéni összetevőit. Ha magas rendelkezésre állású egyéni összetevők, meg kell valósítani a saját feladatátvételi mechanizmus.

### <a name="bPS6"></a>Az Azure Blob storage használata egyéni összetevők konfigurálása

A fürt telepített összetevők előfordulhat olyan alapértelmezett konfigurációt, amely a Hadoop elosztott fájlrendszerrel (HDFS) tárolót használ. HDInsight használja, mint az alapértelmezett tároló Azure Storage vagy a Data Lake Store. Mindkét egy HDFS kompatibilis fájlrendszert biztosít, amely az adatok továbbra is fennáll, akkor is, ha a fürtök törlése. Szükség lehet az összetevők telepítését a WASB vagy ADL HDFS helyett beállításához.

A legtöbb műveleteket nem kell megadnia a fájlrendszerben. Például a következő másolja át a giraph-examples.jar fájlt a helyi fájlrendszer fürtre tárhelyre:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

Ebben a példában a `hdfs` parancs átlátható módon használja az alapértelmezett fürttároló. Egyes műveletek esetében meg kell adnia az URI azonosító. Például `adl:///example/jars` a Data Lake Store vagy `wasb:///example/jars` az Azure Storage.

### <a name="bPS7"></a>Kapcsolatos adatokat ír az STDOUT és az STDERR

HDInsight naplózza az STDOUT és az STDERR írt parancsfájl kimenete. Ezt az információt az Ambari webes felhasználói felület használatával tekintheti meg.

> [!NOTE]
> Ambari csak akkor használható, ha a fürt sikeresen létrehozva. Ha Ön egy parancsfájlművelettel és a fürt létrehozása, létrehozása sikertelen, tekintse meg a hibaelhárítási rész [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) az egyéb módjai férnek hozzá a naplózott információk.

A legtöbb segédprogramok és telepítőcsomagok már kapcsolatos adatokat ír az STDOUT és az STDERR, azonban előfordulhat, hogy hozzáadandó további naplózás. Szöveg STDOUT küldeni, használja a `echo`. Példa:

```bash
echo "Getting ready to install Foo"
```

Alapértelmezés szerint `echo` STDOUT elküldi a karakterláncot. Az STDERR irányítani, vegye fel `>&2` előtt `echo`. Példa:

```bash
>&2 echo "An error occurred installing Foo"
```

Ez a STDERR (2) az STDOUT helyette írt adatok irányítja át. IO átirányítással kapcsolatos további információkért lásd: [ http://www.tldp.org/LDP/abs/html/io-redirection.html ](http://www.tldp.org/LDP/abs/html/io-redirection.html).

A Parancsfájlműveletek által naplózott adatok megtekintéséhez használatos időkategóriát további információkért lásd: [testreszabása HDInsight-fürtök parancsfájlművelet használatával](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> ASCII LF sorvégződések rendelkező fájlok mentése

Bash parancsfájlok megszakította a LF vonallal ASCII formátumban kell tárolni. A következő hiba miatt sikertelen lehet a fájlokat, UTF-8 tárolják, vagy a sor befejezési CRLF használja:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Használja az újrapróbálkozási logika átmeneti hibák elhárítása

Fájlok apt get vagy egyéb adatot az interneten keresztül továbbítani művelettel csomagok telepítése letöltése a művelet átmeneti hálózati hibák miatt sikertelenek lehetnek. A távoli erőforrás kommunikációra például folyamatban van egy biztonsági mentési csomópont feladatátvételét lehet.

Ahhoz, hogy a parancsfájl rugalmas átmeneti hibáinak, újrapróbálkozási logikát is létrehozható. A következő függvény végrehajtása újrapróbálkozási logika mutatja be. Újbóli próbálkozások háromszor a műveletet.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Az alábbi példák bemutatják, hogyan lehet a funkció használatához.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Egyéni parancsfájlok segítő módszerei

Parancsfájl művelet segítő módszereket segédprogramok egyéni parancsfájlok írása közben használható. Ezek a módszerek tartalmazza a[ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) parancsfájl. Használja a következő letölteni és telepíteni azokat a parancsfájl részeként:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

A következő segítő a parancsfájlt használhatja:

| Segítő kihasználtsága | Leírás |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |A fájl a forrás URI letölti a fájl megadott elérési útja. Alapértelmezés szerint azt a meglévő fájl felülírásának nem. |
| `untar_file TARFILE DESTDIR` |Kibontja a bont (használatával `-xf`) a cél könyvtárba. |
| `test_is_headnode` |Ha a fürt átjárócsomópontjához, visszatérési 1; itt futott Ellenkező esetben 0. |
| `test_is_datanode` |Ha az aktuális csomópont adatcsomóponton (munkavégző), a visszatérési érték egy 1. Ellenkező esetben 0. |
| `test_is_first_datanode` |Ha az aktuális csomópont az első adatok (munkavégző) csomópontra (elnevezett workernode0) adja vissza egy 1. Ellenkező esetben 0. |
| `get_headnodes` |Térjen vissza a headnodes teljesen minősített tartományneve a fürtben. Neveket vesszővel tagolt. Üres karakterlánc hibát akkor adja vissza. |
| `get_primary_headnode` |Lekérdezi az elsődleges headnode teljesen minősített tartományneve. Üres karakterlánc hibát akkor adja vissza. |
| `get_secondary_headnode` |Lekérdezi a másodlagos headnode teljesen minősített tartományneve. Üres karakterlánc hibát akkor adja vissza. |
| `get_primary_headnode_number` |A numerikus utótagját, az elsődleges headnode lekérdezi. Üres karakterlánc hibát akkor adja vissza. |
| `get_secondary_headnode_number` |A numerikus utótagját, a másodlagos headnode lekérdezi. Üres karakterlánc hibát akkor adja vissza. |

## <a name="commonusage"></a>Gyakori használati minták

Ez a szakasz néhány gyakori használati mintái, amelyek a saját egyéni parancsfájl írásakor mutatjuk be végrehajtási nyújt útmutatást.

### <a name="passing-parameters-to-a-script"></a>A parancsfájl paraméterek átadása

Bizonyos esetekben a parancsfájl paramétereit lehet szükség. Például szükség lehet a rendszergazdai jelszó a fürt az Ambari REST API használata esetén.

A parancsfájlnak átadott paraméterek nevezik *pozícióparaméterek*, és a hozzárendelt `$1` az első paraméter `$2` a második, és így-on. `$0` a parancsfájl önmagában nevét tartalmazza.

Értékek átadott paraméterek közé kell tenni szimpla idézőjelben ('), a parancsfájl. Ezzel biztosítja, hogy az átadott értéknek literálként van-e kezelni.

### <a name="setting-environment-variables"></a>Környezeti változók beállítása

Egy környezeti változó beállítása hajtja végre a következő utasítást:

    VARIABLENAME=value

VARIABLENAME esetén annak a változónak a nevét. A változó elérésére, `$VARIABLENAME`. Például egy jelszó nevű környezeti változó pozicionális paraméter által megadott értéket hozzárendelni használhatja a következő utasítást:

    PASSWORD=$1

További információk hozzáférésének aztán használhatja `$PASSWORD`.

Környezeti változók a parancsfájl belül csak a parancsfájl hatókörén belül található. Egyes esetekben szükség lehet rendszerszintű környezeti változókat, amelyek megmaradnak a parancsfájl hozzáadása. Adja hozzá a rendszerszintű környezeti változókat, vegye fel a változó `/etc/environment`. Például a következő utasítás létrehozza `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Hozzáférés az egyéni parancsfájlok tároló helyekhez

Egy fürt testreszabásához használt parancsfájlok kell tárolni az alábbi helyek egyikét:

* Egy __Azure Storage-fiók__ , amely kapcsolódik a fürthöz.

* Egy __további tárfiók__ a fürthöz rendelt.

* A __nyilvánosan olvasható URI__. Például egy URL-címe a onedrive-on, Dropbox vagy más szolgáltatást tartalmazó fájl tárolt adatokat.

* Egy __Azure Data Lake Store-fiók__ a HDInsight-fürthöz társított. Az Azure Data Lake Store és a HDInsight együttes használatával további információkért lásd: [HDInsight-fürtök létrehozása a Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > A HDInsight a Data Lake Store elérésére használt egyszerű a parancsfájl olvasási hozzáféréssel kell rendelkeznie.

A parancsfájl által használt erőforrások is nyilvánosan hozzáférhetőnek kell lenniük.

A fájlok tárolására az Azure Storage-fiók vagy az Azure Data Lake Store biztosítja a gyors hozzáférést, mind az Azure-hálózatot.

> [!NOTE]
> A mutató hivatkozás a parancsfájl URI-formátum eltér attól függően, hogy a szolgáltatás használt. A HDInsight-fürthöz társított tárfiókokat, használjon `wasb://` vagy `wasbs://`. Nyilvánosan olvasható URI-k használata `http://` vagy `https://`. A Data Lake Store, használjon `adl://`.

### <a name="checking-the-operating-system-version"></a>Az operációs rendszer verziójának ellenőrzése

HDInsight különböző verzióinak Ubuntu verzióról támaszkodnak. Előfordulhat, hogy ki kell választani az parancsfájlban operációsrendszer-verziók közötti különbséget. Szükség lehet például Ubuntu verziója kötődik bináris telepítéséhez.

Az operációs rendszer verzióját ellenőrzéséhez használja `lsb_release`. Például az alábbi parancsfájl bemutatja, hogyan kell hivatkoznia, egy adott bont fájlt az operációs rendszer verziójától függően:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Ellenőrzőlista a üzembe helyezéséhez egy parancsfájlművelet

Parancsfájl telepítendő elkészítésekor az alábbiakban a lépéseket hajtsa végre a megfelelő:

* Helyezze el az egyéni parancsfájlok, amely elérhető a fürt csomópontjai a telepítés során helyen tartalmazó fájlokat. Például az alapértelmezett a fürt tárolóhelyét. Fájlok nyilvánosan olvasható üzemeltetési szolgáltatásokat is tárolhatja.
* Győződjön meg arról, hogy a parancsfájl az idempotent. Így lehetővé teszi, hogy a parancsfájl hajthatnak végre több alkalommal ugyanazon a csomóponton.
* Egy ideiglenes könyvtár /tmp használatával a letöltött fájlok, parancsfájlok által használt, majd eltávolítással parancsfájlok rendelkezik végrehajtása után.
* Ha az operációs rendszer szintű beállításokat és a Hadoop szolgáltatás konfigurációs fájlokat, módosulnak, érdemes lehet HDInsight a szolgáltatások újraindítására.

## <a name="runScriptAction"></a>A parancsfájlművelet futtatása

A Parancsfájlműveletek segítségével testre szabhatja a HDInsight-fürtök az alábbi módszerekkel:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-sablonok
* A HDInsight .NET SDK.

További információ az egyes módszerek használatával, lásd: [parancsfájlművelet használata](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Egyéni parancsfájl-minták

A Microsoft mintaparancsfájlok összetevőinek telepítése HDInsight-fürtöt biztosít. Tekintse meg a következő hivatkozások további példa Parancsfájlműveletek.

* [Telepítheti és használhatja a HDInsight-fürtök Hue](hdinsight-hadoop-hue-linux.md)
* [Telepítheti és használhatja a HDInsight-fürtök Solr](hdinsight-hadoop-solr-install-linux.md)
* [Telepítheti és használhatja a HDInsight-fürtök Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Telepítéséhez vagy frissítéséhez monó a HDInsight-fürtökön](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Hibaelhárítás

Szert parancsfájlok használata során felmerülő hibák a következők:

**Hiba**: `$'\r': command not found`. Egyes esetekben követ `syntax error: unexpected end of file`.

*OK*: ezt a hibát az okozza, ha egy parancsfájlban a sorok CRLF végződik. UNIX rendszerek, a sor befejezési csak LF várt.

A probléma leggyakrabban esetén a parancsfájl állította össze a Windows-környezetben, mert CRLF egy közös sor a Windows sok szöveg szerkesztők befejezési.

*Megoldási*: Ha egy beállítást a szövegszerkesztőben, Unix-formátum vagy kiválasztása LF a sor befejezése. A CRLF átállítása egy LF UNIX operációs rendszeren is használhat a következő parancsokat:

> [!NOTE]
> Az alábbi parancsokat is nagyjából megfelel, abban, hogy LF CRLF sorvégződések módosítsa azokat. Válasszon ki egy, a rendszer segédprogramok alapján.

| Parancs | Megjegyzések |
| --- | --- |
| `unix2dos -b INFILE` |Az eredeti fájl biztonsági mentése a egy. BAK bővítmény |
| `tr -d '\r' < INFILE > OUTFILE` |EREDMÉNYFÁJL csak LF végződések javítása rendelkező verzióját tartalmazza. |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | A fájlt közvetlenül módosítja |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |EREDMÉNYFÁJL csak LF végződések javítása rendelkező verzióját tartalmazza. |

**Hiba**: `line 1: #!/usr/bin/env: No such file or directory`.

*OK*: A hiba akkor fordul elő, ha a parancsfájl UTF-8 a bájt rendelés be van jelölve (AJ) néven lett mentve.

*Megoldási*: mentse a fájlt, ASCII vagy UTF-8 AJ nélkül. Az Anyagjegyzék nélkül fájlt létrehozni a Linux és Unix rendszeren is használhat a következő parancsot:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Cserélje le `INFILE` az Anyagjegyzék tartalmazó fájl. `OUTFILE` egy új fájlnevet, amely tartalmazza a parancsfájl az Anyagjegyzék nélkül kell lennie.

## <a name="seeAlso"></a>Következő lépések

* Megtudhatja, hogyan [testreszabása HDInsight-fürtök parancsfájlművelet használatával](hdinsight-hadoop-customize-cluster-linux.md)
* Használja a [HDInsight .NET SDK referenciáit](https://msdn.microsoft.com/library/mt271028.aspx) tudhat meg többet kezelése a HDInsight .NET-alkalmazások létrehozása
* Használja a [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) a többi használata a HDInsight-fürtökön felügyeleti műveletek elvégzéséhez.
