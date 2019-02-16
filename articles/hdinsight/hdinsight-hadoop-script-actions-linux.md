---
title: Parancsfájlművelet-alapú fejlesztés a Linux-alapú HDInsight – Azure
description: 'Útmutató: Linux-alapú HDInsight-fürtök testre szabása a Bash-szkriptek használatával. HDInsight parancsfájl műveletet jellemzője lehetővé teszi, hogy a parancsfájlok futtatása közben, vagy a fürt létrehozása után. Parancsprogramok segítségével fürt konfigurációs beállításokat módosítaná, vagy további szoftverek telepíthetők.'
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 9711e9bf94619b8cd003972d74f3bca4c45894bf
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312083"
---
# <a name="script-action-development-with-hdinsight"></a>Parancsfájlművelet-alapú fejlesztés a HDInsight

Ismerje meg, hogyan szabhatja testre a Bash-szkriptek használata a HDInsight-fürt. A szkriptműveletek olyan testreszabhatja a HDInsight fürt létrehozás során vagy után.

> [!IMPORTANT]  
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>Mik azok a Parancsfájlműveletek

A szkriptműveletek olyan Bash-szkriptek, amelyek az Azure-szolgáltatásokat a fürt csomópontjai a konfigurációs módosítások végrehajtása vagy a szoftver telepítése. Szkriptműveletet legfelső szintű hajtja végre, és a fürtcsomópontok teljes körű hozzáférési jogosultságokat biztosít.

A Parancsfájlműveletek az alábbi módszerek alkalmazhatók:

| Ezt a módszert használja a alkalmazni egy parancsfájl... | Fürtlétrehozás során... | Egy futó fürtön... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Az Azure klasszikus parancssori felület |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-sablon |✓ |&nbsp; |

Ezek a módszerek használatával a alkalmazni szkriptműveletek további információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Ajánlott eljárások a parancsfájl-fejlesztés

Egy HDInsight-fürthöz tartozó egyéni parancsfájl fejlesztésekor van néhány ajánlott eljárásokat, és tartsa szem előtt:

* [A cél az Apache Hadoop-verzió](#bPS1)
* [Cél operációsrendszer-verzió](#bps10)
* [Adja meg a parancsfájl stabil hivatkozását](#bPS2)
* [Előre lefordított erőforrások használata](#bPS4)
* [Győződjön meg arról, hogy a fürt testreszabása szkript idempotens](#bPS3)
* [Magas rendelkezésre állásának garantálása érdekében a fürt architektúra](#bPS5)
* [Az Azure Blob storage használata az egyéni összetevők konfigurálása](#bPS6)
* [Az STDOUT és STDERR információ írása](#bPS7)
* [ASCII-LF sorvégződések fájlok mentése](#bps8)
* [Újrapróbálkozási logika átmeneti hibák használata](#bps9)

> [!IMPORTANT]  
> Szkriptműveletek 60 percen belül kell végeznie, vagy a folyamat sikertelen lesz. Csomópont üzembe helyezésekor, a parancsfájl futtatása egy időben alkalmazza a többi beállítási és konfigurációs folyamat. Verseny a erőforrások – például CPU-idő vagy a hálózati sávszélesség előfordulhat, hogy a parancsfájl, a fejlesztési környezet mint befejezése hosszabb időt vesz igénybe.

### <a name="bPS1"></a>A cél az Apache Hadoop-verzió

Különböző verzióit a HDInsight Hadoop-szolgáltatásokhoz és a telepített összetevők különböző verziója van. A szkript egy adott verzióját egy szolgáltatás vagy összetevő vár, ha csak használjon a szkriptet a HDInsight, amely tartalmazza a szükséges összetevők verziójával. A HDInsight használatával mellékelt összetevő verziókról információkat a [HDInsight összetevők verziószámozása](hdinsight-component-versioning.md) dokumentumot.

### <a name="bps10"></a> Cél az operációsrendszer-verzió

Linux-alapú HDInsight az Ubuntu Linux-disztribúció alapján történik. HDInsight különböző verzióinak különböző verzióit Ubuntu, előfordulhat, hogy a parancsfájl viselkedésének módosítása támaszkodnak. A HDInsight 3.4-es és korábbi például Ubuntu verziók Upstart használó alapul. 3.5-ös és újabb verziók Ubuntu 16.04, amely Systemd használ alapul. Systemd és Upstart támaszkodnak különböző parancsokat, és így is dolgozhat a parancsfájlt kell írni.

Egy másik fontos HDInsight 3.4-es és 3.5-ös közötti különbség az, hogy `JAVA_HOME` Java 8 most mutat.

Az operációsrendszer-verzió használatával ellenőrizheti `lsb_release`. A következő kód bemutatja, hogyan határozza meg, ha a parancsfájl futása, az Ubuntu 14-es vagy 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
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

A teljes szkript, amely tartalmazza, ezek a kódrészletek annak https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

A HDInsight által használt Ubuntu verzióját, lásd: a [HDInsight összetevő verziószáma](hdinsight-component-versioning.md) dokumentumot.

Systemd és Upstart közötti különbségek ismertetése: [Upstart felhasználók Systemd](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Adja meg a parancsfájl stabil hivatkozását

A szkript és a kapcsolódó erőforrások mindvégig biztosítani kell a fürt élettartama során. Ezek az erőforrások szükség, ha új csomópontokat ad hozzá a fürthöz méretezési műveletek során.

Az ajánlott eljárás, hogy töltse le, és archiválja mindent az Azure Storage-fiókot, az előfizetéséhez.

> [!IMPORTANT]  
> A storage-fiókot használni más storage-fiók a fürt vagy egy nyilvános, írásvédett tárolót az alapértelmezett tárfiókot kell lennie.

Ha például a Microsoft által biztosított minták vannak tárolva a [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) storage-fiókot. Ez a hely egy nyilvános, írásvédett tárolót a HDInsight csapata tartja karban.

### <a name="bPS4"></a>Előre lefordított erőforrások használata

A parancsfájl futtatásához szükséges idő csökkentése érdekében kerülje az olyan műveleteket, amelyek a forráskód erőforrások fordítása. Például előre fordítsa le erőforrásokat, és tárolja őket egy Azure Storage-fiók blobot a HDInsight ugyanabban az adatközpontban található.

### <a name="bPS3"></a>Győződjön meg arról, hogy a fürt testreszabása szkript idempotens

Parancsfájlok idempotensnek kell lenniük. Ha a parancsfájl több alkalommal fut, kell visszaadnia a fürt állapot minden alkalommal.

Ha például egy parancsfájlt, amely módosítja a konfigurációs fájlok nem kell hozzáadnia ismétlődő bejegyzéseket Ha több alkalommal futott.

### <a name="bPS5"></a>Magas rendelkezésre állásának garantálása érdekében a fürt architektúra

Linux-alapú HDInsight-fürtök, adja meg, amelyek aktív-e a fürtön belül két fő csomópont, és szkriptműveletek mindkét csomópont fut. Ha az összetevők telepítése csak egy fő csomópontot, az összetevők telepítésének a két fő csomópontra.

> [!IMPORTANT]  
> A HDInsight része a szolgáltatást tervezték, hogy igény szerint, a két fő csomópont közötti feladatátvételt. Ez a funkció nincs kibővítve a Parancsfájlműveletek segítségével telepítve egyéni összetevők. Ha egyéni összetevők magas rendelkezésre állásra van szüksége, meg kell valósítani a saját adatátvételi mechanizmus.

### <a name="bPS6"></a>Az Azure Blob storage használata az egyéni összetevők konfigurálása

A fürtön telepítendő összetevők lehet alapértelmezett konfigurációja a tárolót az Apache Hadoop elosztott fájlrendszer (HDFS) használja. HDInsight az alapértelmezett tárolóként használ az Azure Storage vagy a Data Lake Storage. Mindkettő biztosít a HDFS-kompatibilis rendszerekben, amely az adatok továbbra is fennáll, akkor is, ha a fürt törlődik. Előfordulhat, hogy kell telepítenie a WASB vagy az ADL használja a HDFS helyett összetevők konfigurálása.

A legtöbb műveletet nem kell megadnia a fájlrendszerben. Ha például a következő másol a giraph-examples.jar fájl a helyi fájlrendszerben fürttároló:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

Ebben a példában a `hdfs` parancs átlátható módon használja az alapértelmezett fürttárolóhoz. Egyes műveletek esetében szükség lehet az URI-t adja meg. Ha például `adl:///example/jars` a Data Lake Storage Gen1 vagy `wasb:///example/jars` Azure Storage-hoz.

### <a name="bPS7"></a>Az STDOUT és STDERR információ írása

HDInsight-naplók STDOUT és STDERR írt parancsprogram kimenete. Megtekintheti az Ambari webes felhasználói felületen ezt az információt.

> [!NOTE]  
> Az Apache Ambari csak akkor használható, ha a fürt sikeresen létrejött. Ha használ egy parancsfájl művelet során a fürt létrehozása és a létrehozás sikertelen, tekintse meg a hibaelhárítási szakaszt [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) férnek hozzá a naplózott információk egyéb módjaira vonatkozóan.

A legtöbb segédprogramok és telepítőcsomagok már adatokat írni az STDOUT és STDERR, azonban előfordulhat, hogy szeretne további naplózás hozzáadása. Az STDOUT szöveg küldeni, használja a `echo`. Példa:

```bash
echo "Getting ready to install Foo"
```

Alapértelmezés szerint `echo` STDOUT küld a karakterláncot. Irányítani STDERR, adjon hozzá `>&2` előtt `echo`. Példa:

```bash
>&2 echo "An error occurred installing Foo"
```

Ez átirányítja a STDERRBEN (2) STDOUT inkább írt adatok. I/o-átirányítás további információkért lásd: [ https://www.tldp.org/LDP/abs/html/io-redirection.html ](https://www.tldp.org/LDP/abs/html/io-redirection.html).

A szkriptműveletek által naplózott adatok megtekintésekor további információkért lásd: [testreszabása HDInsight-fürtök szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> ASCII-LF sorvégződések fájlok mentése

Bash-szkriptek sorok LF megszakította az ASCII formátumban kell tárolni. UTF-8 tárolódnak, vagy CRLF használja, mint a sor vége fájlok a következő hibaüzenettel meghiúsulhat:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Újrapróbálkozási logika átmeneti hibák használata

Fájlok telepítése apt-get paranccsal, vagy más műveletek, amelyek az interneten keresztül adatokat továbbítson csomagok letöltése esetén a művelet átmeneti hálózati hibák miatt sikertelen lehet. A távoli erőforrás való kommunikáció közben lehet például egy biztonsági mentési csomópont-ba irányuló feladatátvétel folyamatban.

Ahhoz, hogy a parancsfájl rugalmas az átmeneti hibákra, újrapróbálkozási logikát valósíthat meg. A következő függvény bemutatja, hogyan újrapróbálkozási algoritmussal. Újra megpróbálja a műveletet háromszor mielőtt hibát jelentene.

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

Az alábbi példák bemutatják, hogyan használja ezt a funkciót.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Egyéni parancsfájlok segédmetódusokat

Parancsprogram-művelet segédmetódusokat olyan segédprogramok, amelyet használhat egyéni parancsfájlok írása közben. Ezek a metódusok tárolják a [ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) parancsfájlt. A következő használatával töltse le és használja őket a szkript részeként:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

A következő segítőket használható a parancsfájlban:

| Segítő használati | Leírás |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Egy fájlt tölt le a forrás URI-t, a fájl megadott elérési útja. Alapértelmezés szerint azt a meglévő fájl felülírásának mellőzése. |
| `untar_file TARFILE DESTDIR` |Tar fájlt (használatával `-xf`) a cél könyvtárba. |
| `test_is_headnode` |Ha futtatunk egy átjárócsomóponthoz, lépjen vissza az 1. más esetekben 0. |
| `test_is_datanode` |Ha az aktuális csomópont egy adatcsomópont (munkavégző), a visszatérési érték egy 1. más esetekben 0. |
| `test_is_first_datanode` |Ha az aktuális csomópont az első adatok (munkavégző) csomópont (elnevezett workernode0) adja vissza egy 1. más esetekben 0. |
| `get_headnodes` |Ezután az átjárócsomópontokról teljesen minősített neveinek visszaadása a fürtben. Nevek vesszővel tagolt. Hiba történt a üres karakterláncot ad vissza. |
| `get_primary_headnode` |Lekérdezi az elsődleges átjárócsomóponton teljesen minősített tartománynevét. Hiba történt a üres karakterláncot ad vissza. |
| `get_secondary_headnode` |Lekérdezi a másodlagos átjárócsomóponthoz teljesen minősített tartománynevét. Hiba történt a üres karakterláncot ad vissza. |
| `get_primary_headnode_number` |Az elsődleges átjárócsomóponthoz numerikus utótagot kapja meg. Hiba történt a üres karakterláncot ad vissza. |
| `get_secondary_headnode_number` |A másodlagos átjárócsomóponthoz, numerikus utótagot kapja meg. Hiba történt a üres karakterláncot ad vissza. |

## <a name="commonusage"></a>Gyakori használati minták

Ez a szakasz útmutatást nyújt az megvalósítása a használt használati mintáit, amelyek a saját egyéni parancsfájlok írása közben mutatjuk be.

### <a name="passing-parameters-to-a-script"></a>A parancsfájl paraméterek átadása

Bizonyos esetekben a parancsfájl paramétereit lehet szükség. Ha például szükség lehet a rendszergazdai jelszót a fürthöz az Ambari REST API használata esetén.

A parancsfájlnak átadott paraméterek nevezzük *pozícióparaméterek*, és hozzá vannak rendelve `$1` az első paraméter `$2` a második, és így az. `$0` magát a parancsfájlt nevét tartalmazza.

Paraméterek közé kell tenni félidézőjelet ('), a parancsfájl átadott értékeket. Ez biztosítja, hogy az átadott érték szövegkonstans számít.

### <a name="setting-environment-variables"></a>Környezeti változók beállítása

Egy környezeti változó beállítása a következő utasítást hajt végre:

    VARIABLENAME=value

VARIABLENAME helyére annak a változónak a nevét. A változó elérésére, `$VARIABLENAME`. Például szeretne hozzárendelni egy jelszó nevű környezeti változóban Helyzetbeállító paraméter által megadott érték, akkor használja a következő utasítást:

    PASSWORD=$1

Ezt követően a hozzáférés, az adatokat aztán használhatja `$PASSWORD`.

A környezeti változók a parancsfájl belül csak a parancsfájl hatókörén belül léteznek. Bizonyos esetekben előfordulhat, hogy hozzá kell rendszerszintű környezeti változókat a parancsfájl befejezése után megőrzi. Adja hozzá a rendszerszintű környezeti változókat, adja hozzá a változó `/etc/environment`. Ha például a következő utasítást ad hozzá `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Az egyéni parancsprogramok tárolására helyeken való hozzáférés

Egy fürt testreszabására szolgáló parancsfájlok mappájába kell menteni a következő helyen:

* Egy __Azure Storage-fiók__ társítva a fürthöz.

* Egy __további tárfiókot__ a fürthöz társított.

* A __nyilvánosan olvasható URI__. Például egy URL-címe a OneDrive, Dropbox vagy egyéb üzemeltetési szolgáltatás fájl tárolt adatokat.

* Egy __Azure Data Lake-tárfiókra__ társítva a HDInsight-fürt. Az Azure Data Lake Storage használata a HDInsight további információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > HDInsight Data Lake Storage elérésére használja a szolgáltatásnév a parancsfájl olvasási hozzáféréssel kell rendelkeznie.

A parancsfájlhoz használt erőforrásokhoz is nyilvánosan elérhető kell lennie.

Fájlok tárolására az Azure Storage-fiók vagy az Azure Data Lake Storage gyors hozzáférést, mint belül az Azure-hálózatot is biztosít.

> [!NOTE]  
> A mutató hivatkozás a szkript URI-formátum a használt szolgáltatástól függően eltérő. A HDInsight-fürthöz társított storage-fiókok esetében használjon `wasb://` vagy `wasbs://`. Nyilvánosan olvasható URI-k használata `http://` vagy `https://`. Használja a Data Lake Storage `adl://`.

### <a name="checking-the-operating-system-version"></a>Az operációs rendszer verziójának ellenőrzése

HDInsight különböző verzióit Ubuntu adott verzióinak támaszkodnak. Előfordulhat, hogy a parancsfájlban ellenőrizze az operációsrendszer-verziók közötti különbségeket. Ha például szükség lehet bináris vannak kötve, az Ubuntu verzióját telepítse.

Az operációs rendszer verziójának ellenőrzéséhez használja a `lsb_release`. Például az alábbi parancsfájl bemutatja, hogyan adott tar fájlra az operációs rendszer verziójától függően:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Ellenőrzőlista a központi telepítése egy parancsprogram-művelet

Íme a lépéseket hajtsa végre a megfelelő telepítését egy parancsfájl elkészítése közben:

* Helyezze el az olyan helyen, amely elérhető a fürt csomópontok üzembe helyezése során az egyéni parancsfájlok tartalmazó fájlokat. Ha például a a fürt alapértelmezett tárolója. Fájlok nyilvánosan olvasható üzemeltetési szolgáltatásokat is tárolhatók.
* Ellenőrizze, hogy a szkript idempotens. Ez lehetővé teszi a parancsfájl hajtható végre több alkalommal ugyanazon a csomóponton.
* Egy ideiglenes könyvtár ügynökszámítógépen használatával mindig a letöltött fájlokat, a parancsfájlok által használt, majd eltávolítással parancsfájlok rendelkezik végrehajtása után.
* Ha operációs rendszer szintű beállításokat vagy a Hadoop szolgáltatás konfigurációs fájlok megváltoztak, érdemes a HDInsight-szolgáltatások újraindítása.

## <a name="runScriptAction"></a>A parancsprogram-művelet futtatása

A Parancsfájlműveletek segítségével testre szabhatja a HDInsight-fürtök a következő módszerekkel:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-sablonok
* A HDInsight .NET SDK-t.

Az egyes módszerek használatáról további információkért lásd: [szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Egyéni parancsfájl-minták

A Microsoft biztosít mintaszkriptek összetevők telepítéséhez egy HDInsight-fürtön. Tekintse meg az alábbi hivatkozások további példa parancsfájl műveletek.

* [Telepítse, és a Hue használata a HDInsight-fürtökön](hdinsight-hadoop-hue-linux.md)
* [Telepítse és Apache Solr használata a HDInsight-fürtökön](hdinsight-hadoop-solr-install-linux.md)
* [Telepítse, és az Apache Giraph használata a HDInsight-fürtökön](hdinsight-hadoop-giraph-install-linux.md)
* [Telepítené vagy frissítené a Mono a HDInsight-fürtökön](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Hibaelhárítás

Kialakítása a parancsfájlok használata során felmerülő hibák a következők:

**Hiba**: `$'\r': command not found`. Néha követ `syntax error: unexpected end of file`.

*Ok*: Ezt a hibát az okozza, ha a sorokat egy parancsfájlban CRLF végződhet. UNIX rendszerek várhatóan csak LF, a sor vége.

Ez a probléma leggyakrabban esetén a szkript egy Windows-környezetben létrehozott egy közös sor számára a Windows számos szövegszerkesztő befejezési CRLF-jébe.

*Megoldás*: Ha a szövegszerkesztőben lehetőség, a sor vége válassza ki Unix formátumban vagy LF Karakterrel. Módosítsa a CRLF-LF UNIX operációs rendszeren is felhasználhatja a következő parancsokat:

> [!NOTE]  
> A következő parancsok pedig nagyjából, annak, hogy azok a CRLF sorvégződések kell módosítani a LF Karakterrel. Válasszon ki egy, a rendszer a segédprogramok alapján.

| Parancs | Megjegyzések |
| --- | --- |
| `unix2dos -b INFILE` |Az eredeti fájl van mentve a. BAK bővítmény |
| `tr -d '\r' < INFILE > OUTFILE` |EREDMÉNYFÁJL tartalmaz csak LF végződések javítása verzió |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Közvetlenül módosítja a fájlt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |EREDMÉNYFÁJL csak LF végződések javítása verzió tartalmazza. |

**Hiba**: `line 1: #!/usr/bin/env: No such file or directory`.

*Ok*: Ez a hiba akkor fordul elő, ha a parancsfájl az UTF-8 és a egy bájt rendelés Mark (AJ) néven lett mentve.

*Megoldás*: Mentse a fájlt, mint ASCII vagy UTF-8 AJ nélkül. Hozzon létre egy fájlt az Anyagjegyzék nélkül a Linux és Unix rendszeren is felhasználhatja a következő parancsot:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Cserélje le `INFILE` tartalmazó a AJ-fájllal. `OUTFILE` egy új fájl neve, amely tartalmazza a parancsfájl az Anyagjegyzék nélkül kell lennie.

## <a name="seeAlso"></a>Következő lépések

* Ismerje meg, hogyan [testreszabása HDInsight-fürtök szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)
* Használja a [HDInsight .NET SDK-referenciában](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) további információ a HDInsight kezelése .NET-alkalmazások létrehozása
* Használja a [HDInsight REST API-val](https://msdn.microsoft.com/library/azure/mt622197.aspx) a REST használata a HDInsight-fürtökön felügyeleti műveletek végrehajtásához.
