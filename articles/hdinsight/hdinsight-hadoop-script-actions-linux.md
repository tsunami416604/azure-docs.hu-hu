---
title: Parancsfájlműveletek fejlesztése az Azure HDInsight-fürtök testreszabásához
description: Ismerje meg, hogyan bash parancsfájlok hdinsight-fürtök testreszabásához. A parancsfájlműveletek lehetővé teszik parancsfájlok futtatását a fürt létrehozása során vagy azt követően a fürt konfigurációs beállításainak módosításához vagy további szoftverek telepítéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: db37a56ffbf0cb64530f8f7af38841bac72c77d4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767550"
---
# <a name="script-action-development-with-hdinsight"></a>Parancsfájl-műveletek fejlesztése a HDInsight segítségével

Ismerje meg, hogyan szabhatja testre a HDInsight-fürtbash-parancsfájlok használatával. A parancsfájlműveletek a HDInsight testreszabásának egyik módját teszik lea fürt létrehozása során vagy azt követően.

## <a name="what-are-script-actions"></a>Mik azok a parancsfájlműveletek?

A parancsfájlműveletek olyan Bash-parancsfájlok, amelyeket az Azure a fürtcsomópontokon futtat a konfigurációs módosítások vagy a szoftverek telepítése érdekében futtat. A parancsfájl-művelet gyökérként kerül végrehajtásra, és teljes hozzáférési jogokat biztosít a fürtcsomópontokhoz.

A parancsfájlműveletek a következő módszerekkel alkalmazhatók:

| Ezzel a módszerrel parancsfájlt alkalmazhat... | A fürt létrehozása során... | Futó fürtön... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure klasszikus parancssori felület |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-sablon |✓ |&nbsp; |

A parancsfájlműveletek alkalmazásának módszereiről a [HDInsight-fürtök testreszabása parancsfájlműveletek használatával című](hdinsight-hadoop-customize-cluster-linux.md)témakörben talál további információt.

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Gyakorlati tanácsok a parancsfájlok fejlesztéséhez

Ha egy HDInsight-fürthöz egyéni parancsfájlt fejleszt ki, számos ajánlott eljárás nak kell megszokva:

* [Az Apache Hadoop verzió célzása](#bPS1)
* [Az operációs rendszer verziójának megcélzása](#bps10)
* [Stabil hivatkozások biztosítása parancsfájl-erőforrásokhoz](#bPS2)
* [Előre lefordított erőforrások használata](#bPS4)
* [Annak ellenőrzése, hogy a fürt testreszabási parancsfájlja idempotens-e](#bPS3)
* [A fürtarchitektúra magas rendelkezésre állásának biztosítása](#bPS5)
* [Az egyéni összetevők konfigurálása az Azure Blob storage használatára](#bPS6)
* [Információk írása az STDOUT és az STDERR számára](#bPS7)
* [Fájlok mentése ASCII-ként LF sorvégződéssel](#bps8)
* [Az újrapróbálkozási logika használata az átmeneti hibák helyreállítása érdekében](#bps9)

> [!IMPORTANT]  
> A parancsfájlműveleteknek 60 percen belül be kell fejeződniük, különben a folyamat sikertelen lesz. A csomópont-kiépítés során a parancsfájl más telepítési és konfigurációs folyamatokkal egyidejűleg fut. Az erőforrásokért, például a processzoridőért vagy a hálózati sávszélességért folyó verseny miatt a parancsfájl befejezése hosszabb időt vehet igénybe, mint a fejlesztői környezetben.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Az Apache Hadoop verzió célzása

A HDInsight különböző verziói a Hadoop-szolgáltatások és -összetevők különböző verzióival rendelkeznek. Ha a parancsfájl egy szolgáltatás vagy összetevő egy adott verzióját várja, csak a HDInsight a szükséges összetevőket tartalmazó verziójával használja a parancsfájlt. A HDInsight részét képező összetevő-verziókról a [HDInsight-összetevő verziószámozási](hdinsight-component-versioning.md) dokumentumsegítségével talál információt.

### <a name="checking-the-operating-system-version"></a>Az operációs rendszer verziójának ellenőrzése

A HDInsight különböző verziói az Ubuntu egyes verzióira támaszkodnak. Előfordulhat, hogy különbségek vannak az operációs rendszer verziói között, amelyeket ellenőriznie kell a parancsfájlban. Előfordulhat például, hogy telepítenie kell egy bináris fájlt, amely az Ubuntu verziójához van kötve.

Az operációs rendszer verziójának ellenőrzéséhez használja a használatát. `lsb_release` A következő parancsfájl például bemutatja, hogyan hivatkozzon egy adott kátrányfájlra az operációs rendszer verziójától függően:

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

### <a name="target-the-operating-system-version"></a><a name="bps10"></a>Az operációs rendszer verziójának megcélzása

A HDInsight az Ubuntu Linux disztribúción alapul. A HDInsight különböző verziói az Ubuntu különböző verzióira támaszkodnak, ami megváltoztathatja a parancsfájl működését. A HDInsight 3.4-es és korábbi verziói például az Upstart ot használó Ubuntu-verziókon alapulnak. A 3.5-ös vagy újabb verziók az Ubuntu 16.04-en alapulnak, amely a Systemd-et használja. A Systemd és az Upstart különböző parancsokra támaszkodik, ezért a parancsfájlt úgy kell megírni, hogy mindkettővel működjön.

Egy másik fontos különbség a HDInsight 3.4 és a 3.5 között, hogy `JAVA_HOME` most a Java 8-ra mutat. A következő kód bemutatja, hogyan állapítható meg, hogy a szkript ubuntu 14-en vagy 16-on fut-e:

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

A kódrészleteket tartalmazó teljes parancsfájl a https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.shkövetkező helyen található: .

Az Ubuntu HDInsight által használt verzióját lásd a [HDInsight összetevő](hdinsight-component-versioning.md) verziódokumentumában.

A Systemd és az Upstart közötti különbségeket a [Rendszerindítás kezdő felhasználók számára című](https://wiki.ubuntu.com/SystemdForUpstartUsers)témakörben olvassa el.

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Stabil hivatkozások biztosítása parancsfájl-erőforrásokhoz

A parancsfájlnak és a kapcsolódó erőforrásoknak a fürt teljes élettartama alatt elérhetőnek kell maradniuk. Ezekre az erőforrásokra akkor van szükség, ha a méretezési műveletek során új csomópontokat adhozzá a fürthöz.

Az ajánlott eljárás az, hogy töltse le és archiválja mindent egy Azure Storage-fiók az előfizetésben.

> [!IMPORTANT]  
> A használt tárfióknak a fürt alapértelmezett tárfiókjának vagy bármely más tárfiók nyilvános, csak olvasható tárolójának kell lennie.

Például a Microsoft által biztosított minták [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) a tárfiókban tárolódnak. Ez a hely egy nyilvános, csak olvasható tároló, amelyet a HDInsight-csapat tart fenn.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Előre lefordított erőforrások használata

A parancsfájl futtatásához szükséges idő csökkentése érdekében kerülje az erőforrásokat forráskódból fordító műveleteket. Például előre lefordítani az erőforrásokat, és tárolja őket egy Azure Storage-fiók blob ugyanabban az adatközpontban, mint a HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Annak ellenőrzése, hogy a fürt testreszabási parancsfájlja idempotens-e

A parancsfájlok nak idempotensnek kell lenniük. Ha a parancsfájl többször fut, a fürtnek minden alkalommal ugyanabba az állapotba kell visszatérnie.

Ha például egy parancsfájl módosítja a konfigurációs fájlokat, nem adhat hozzá ismétlődő bejegyzéseket, ha az többször is futott.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>A fürtarchitektúra magas rendelkezésre állásának biztosítása

A Linux-alapú HDInsight-fürtök két, a fürtön belül aktív főcsomópontot biztosítanak, és a parancsfájlműveletek mindkét csomóponton futnak. Ha a telepített összetevők csak egy főcsomópontot várnak, ne telepítse az összetevőket mindkét főcsomópontra.

> [!IMPORTANT]  
> A HDInsight részeként nyújtott szolgáltatások célja, hogy szükség szerint feladatátvételt hajtson elő a két főcsomópont között. Ez a funkció nem érhető el a parancsfájlműveleteken keresztül telepített egyéni összetevőkre. Ha az egyéni összetevők magas rendelkezésre állásra van szüksége, saját feladatátvételi mechanizmust kell megvalósítania.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Az egyéni összetevők konfigurálása az Azure Blob storage használatára

Előfordulhat, hogy a fürtre telepített összetevők alapértelmezett konfigurációja Apache Hadoop Distributed File System (HDFS) tárolót használ. A HDInsight az Azure Storage vagy a Data Lake Storage alapértelmezett tárolóként használja. Mindkettő hdfs-kompatibilis fájlrendszert biztosít, amely akkor is megőrzi az adatokat, ha a fürt et törlik. Előfordulhat, hogy a telepített összetevőket úgy kell konfigurálnia, hogy a HDFS helyett WASB vagy ADL-t használjanak.

A legtöbb művelethez nem kell megadnia a fájlrendszert. A következő adatok például a hadoop-common.jar fájlt a helyi fájlrendszerből a fürttárolóba másolja:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

Ebben a példában a `hdfs` parancs transzparens módon használja az alapértelmezett fürttárolót. Egyes műveletek esetén előfordulhat, hogy meg kell adnia az URI-t. Például `adl:///example/jars` az Azure Data Lake `abfs:///example/jars` Storage Gen1, a `wasb:///example/jars` Data Lake Storage Gen2 vagy az Azure Storage esetében.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Információk írása az STDOUT és az STDERR számára

A HDInsight naplózza az STDOUT és az STDERR rendszerbe írt parancsfájlkimenetet. Ezeket az információkat az Ambari webes felhasználói felületén tekintheti meg.

> [!NOTE]  
> Az Apache Ambari csak akkor érhető el, ha a fürt sikeresen létrejött. Ha parancsfájlműveletet használ a fürt létrehozása során, és a létrehozás sikertelen, olvassa el a [parancsfájlműveletek elhárítása című](./troubleshoot-script-action.md) témakört a naplózott adatok elérésének egyéb módjairól.

A legtöbb segédprogram és telepítőcsomag már tartalmaz adatokat az STDOUT és az STDERR számára, azonban további naplózást is hozzáadhat. Ha szöveget szeretne küldeni az `echo`STDOUT-nak, használja a használatát. Például:

```bash
echo "Getting ready to install Foo"
```

Alapértelmezés szerint `echo` elküldi a karakterláncot az STDOUT-nak. Az STDERR-hez való `>&2` `echo`átirányításhoz adja hozzá a before . Például:

```bash
>&2 echo "An error occurred installing Foo"
```

Ez átirányítja az STDOUT-nak írt információkat az STDERR (2) helyett. Az IO átirányításáról további [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html)információt a .

A parancsfájlműveletek által naplózott információk megtekintéséről a [Parancsfájlműveletek elhárítása című](./troubleshoot-script-action.md)témakörben talál további információt.

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a>Fájlok mentése ASCII-ként LF sorvégződéssel

A Bash parancsfájlokat ASCII formátumban kell tárolni, a sorokat pedig az LF szüntesse meg. Az UTF-8 néven tárolt fájlok, illetve a CRLF használata sorvégződésként a következő hibával sikertelen lehet:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a>Az újrapróbálkozási logika használata az átmeneti hibák helyreállítása érdekében

Fájlok letöltésekor, csomagok telepítésekor apt-get használatával, vagy más, az interneten keresztül adatokat továbbító műveletek esetén a művelet átmeneti hálózati hibák miatt sikertelen lehet. Előfordulhat például, hogy a távoli erőforrás, amellyel kommunikál, egy biztonsági csomópontnak való átadás folyamatában van.

Ahhoz, hogy a parancsfájl rugalmas az átmeneti hibák, valósíthatja meg újrapróbálkozási logika. A következő függvény bemutatja, hogyan valósítható meg újrapróbálkozási logika. A műveletet háromszor újrapróbálkozik, mielőtt meghibásodna.

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

Az alábbi példák bemutatják, hogyan kell használni ezt a funkciót.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Segítő módszerek egyéni parancsfájlokhoz

A parancsfájlműveleteket segítő módszerek olyan segédprogramok, amelyeket egyéni parancsfájlok írásakor használhat. Ezek a módszerek [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) a parancsfájlban találhatók. Az alábbiak segítségével töltheti le és használhatja őket a parancsfájl részeként:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

A parancsfájlban használható alábbi segítők:

| Segítő használata | Leírás |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Letölt egy fájlt a forrás URI-ról a megadott fájl elérési útjára. Alapértelmezés szerint nem írja felül a meglévő fájlokat. |
| `untar_file TARFILE DESTDIR` |Kátrányfájlt (használatával) `-xf`bont ki a célkönyvtárba. |
| `test_is_headnode` |Ha fürtfőcsomóponton fut, 1-es visszaad. ellenkező esetben 0. |
| `test_is_datanode` |Ha az aktuális csomópont adat (dolgozó) csomópont, akkor 1-es adata; ellenkező esetben 0. |
| `test_is_first_datanode` |Ha az aktuális csomópont az első adat (dolgozó) csomópont (workernode0) 1-es visszaad; ellenkező esetben 0. |
| `get_headnodes` |Adja vissza a fürtben lévő headnodes teljesen minősített tartománynevét. A nevek vesszővel vannak elválasztva. Hiba esetén egy üres karakterlánc ot ad vissza. |
| `get_primary_headnode` |Leveszi az elsődleges csomópont teljesen minősített tartománynevét. Hiba esetén egy üres karakterlánc ot ad vissza. |
| `get_secondary_headnode` |Leveszi a másodlagos csomópont teljesen minősített tartománynevét. Hiba esetén egy üres karakterlánc ot ad vissza. |
| `get_primary_headnode_number` |Leveszi az elsődleges fejcsomópont numerikus utótagot. Hiba esetén egy üres karakterlánc ot ad vissza. |
| `get_secondary_headnode_number` |Leveszi a másodlagos csomópont numerikus utótagot. Hiba esetén egy üres karakterlánc ot ad vissza. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Gyakori használati minták

Ez a szakasz útmutatást nyújt a közös használati minták, amelyek előfordulhat, hogy a saját egyéni parancsfájl írása közben.

### <a name="passing-parameters-to-a-script"></a>Paraméterek átadása parancsfájlhoz

Bizonyos esetekben előfordulhat, hogy a parancsfájl paramétereket igényel. Előfordulhat például, hogy az Ambari REST API használatakor szüksége lehet a fürt rendszergazdai jelszavára.

A parancsfájlnak átadott *paramétereket pozícióparamétereknek nevezzük,* `$1` és az első `$2` paraméterhez, a másodikhoz és a so-on-hoz vannak rendelve. `$0`tartalmazza magának a szkriptnek a nevét.

A parancsfájlnak paraméterekként átadott értékeket egyszeres idézőjelekkel (') kell mellékelve. Ezzel biztosítja, hogy az átadott érték konstansként legyen kezelve.

### <a name="setting-environment-variables"></a>Környezeti változók beállítása

A környezeti változó beállítását a következő utasítás hajtja végre:

    VARIABLENAME=value

Ahol a VÁLTOZÓNÉV a változó neve. A változó eléréséhez `$VARIABLENAME`használja a használatát. Ha például egy pozícióparaméter által megadott értéket rendel a PASSWORD nevű környezeti változóhoz, a következő utasítást kell használnia:

    PASSWORD=$1

Ezt követően az információhoz való későbbi hozzáférés használható `$PASSWORD`.

A parancsfájlon belül beállított környezeti változók csak a parancsfájl hatókörén belül léteznek. Bizonyos esetekben előfordulhat, hogy rendszerszintű környezeti változókat kell hozzáadnia, amelyek a parancsfájl befejezése után is megmaradnak. Rendszerszintű környezeti változók hozzáadásához adja hozzá `/etc/environment`a változót a hoz. Például a következő `HADOOP_CONF_DIR`utasítás hozzáteszi:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Hozzáférés azokhoz a helyekhez, ahol az egyéni parancsfájlok tárolódnak

A fürt testreszabásához használt parancsfájlokat a következő helyek egyikén kell tárolni:

* A fürthöz társított __Azure Storage-fiók.__

* A fürthöz társított __további tárfiók.__

* Nyilvánosan __olvasható URI__. Például a OneDrive-on, a Dropboxban vagy más fájltároló szolgáltatásban tárolt adatok URL-címe.

* Egy __Azure Data Lake Storage-fiók,__ amely a HDInsight-fürthöz van társítva. Az Azure Data Lake Storage HDInsight szolgáltatással való használatáról további információt a [Gyorsútmutató: Fürtök beállítása a HDInsightban című témakörben talál.](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)

    > [!NOTE]  
    > A szolgáltatás egyszerű HDInsight a Data Lake Storage eléréséhez használt olvasási hozzáférést kell biztosítania a parancsfájlhoz.

A parancsfájl által használt erőforrásoknak is nyilvánosan elérhetőnek kell lenniük.

A fájlok tárolása egy Azure Storage-fiókban vagy az Azure Data Lake Storage gyors hozzáférést biztosít, mivel mind az Azure-hálózaton belül.

> [!NOTE]  
> A parancsfájlra való hivatkozáshoz használt URI-formátum a használt szolgáltatástól függően eltérő lehet. A HDInsight-fürthöz társított tárfiókok esetén használja `wasb://` a vagyat. `wasbs://` Nyilvánosan olvasható URI-k `http://` esetén `https://`használja a vagy . A Data Lake `adl://`Storage használatához használja a használatát.

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Parancsfájlművelet telepítésének ellenőrzőlistája

A parancsfájl okának előkészítése során az alábbi lépéseket kell tenni:

* Helyezze az egyéni parancsfájlokat tartalmazó fájlokat olyan helyre, amely a fürtcsomópontok számára elérhető a telepítés során. Például a fürt alapértelmezett tárolója. A fájlok nyilvánosan olvasható tárhelyszolgáltatásokban is tárolhatók.
* Ellenőrizze, hogy a parancsfájl idempotens-e. Ez lehetővé teszi, hogy a parancsfájl végrehajtása többször ugyanazon a csomóponton.
* A parancsfájlok által használt letöltött fájlok megtartásához használjon ideiglenes fájlkönyvtárat /tmp, majd a parancsfájlok végrehajtása után törölje őket.
* Ha az operációs rendszer szintű beállítások vagy a Hadoop szolgáltatás konfigurációs fájljai megváltoznak, érdemes újraindítani a HDInsight-szolgáltatásokat.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Parancsfájl-művelet futtatása

Parancsfájlműveletek segítségével testreszabhatja a HDInsight-fürtöket a következő módszerekkel:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-sablonok
* A HDInsight .NET SDK.

Az egyes módszerek használatáról a [Parancsfájl-műveletek használata](hdinsight-hadoop-customize-cluster-linux.md)című témakörben talál további információt.

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Egyéni parancsfájlminták

A Microsoft mintaparancsfájlokat biztosít az összetevők HDInsight-fürtre való telepítéséhez. Lásd: [Hue telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-hue-linux.md) példaparancsfájl-műveletként.

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi hibák rakhatnak át a kifejlesztett parancsfájlok használatakor:

**Hiba** `$'\r': command not found`: . Néha követi `syntax error: unexpected end of file`.

*Ok*: Ez a hiba akkor keletkezik, ha a parancsfájl sorai CRLF-re végződnek. Unix rendszerek várják csak LF, mint a vonal vége.

Ez a probléma leggyakrabban akkor fordul elő, ha a parancsfájl Windows-környezetben készült, mivel a CRLF a Windows számos szövegszerkesztőjének közös vonala.

*Megoldás:* Ha ez egy lehetőség a szövegszerkesztőben, válassza a Unix formátumot vagy az LF lehetőséget a sor befejezéséhez. Unix rendszeren a következő parancsok segítségével a CRLF lf-re is módosítható:

> [!NOTE]  
> A következő parancsok nagyjából egyenértékűek, mivel a CRLF sorvégződéseket LF-re kell módosítaniuk. Válasszon egyet a rendszeren elérhető segédprogramok alapján.

| Parancs | Megjegyzések |
| --- | --- |
| `unix2dos -b INFILE` |Az eredeti fájlról biztonsági másolatot kell tenni egy . BAK kiterjesztés |
| `tr -d '\r' < INFILE > OUTFILE` |Outfile tartalmaz egy változatot csak LF végződések |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Közvetlenül módosítja a fájlt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |Az OUTFILE olyan verziót tartalmaz, amelycsak LF végződésekkel van elvégezve. |

**Hiba** `line 1: #!/usr/bin/env: No such file or directory`: .

*Ok*: Ez a hiba akkor fordul elő, amikor a parancsfájlutf-8 néven lett mentve bájtrendelés-jellel (AJ).

*Megoldás*: Mentse a fájlt ASCII vagy UTF-8 néven anyagjegyzék nélkül. Linux vagy Unix rendszeren a következő parancs segítségével is létrehozhat egy fájlt az anyagjegyzék nélkül:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Cserélje `INFILE` le az anyagjegyzéket tartalmazó fájlra. `OUTFILE`kell lennie egy új fájlnevet, amely tartalmazza a parancsfájl nélkül anyagjegyzék.

## <a name="next-steps"></a><a name="seeAlso"></a>További lépések

* A [HDInsight-fürtök testreszabása parancsfájlművelettel](hdinsight-hadoop-customize-cluster-linux.md)
* A [HDInsight .NET SDK hivatkozásának használatával](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) további információt nyújt a HDInsightot kezelő .NET-alkalmazások létrehozásáról
* A [HDInsight REST API-val](https://msdn.microsoft.com/library/azure/mt622197.aspx) megtudhatja, hogyan használhatja a REST-et a HDInsight-fürtök felügyeleti műveletek végrehajtására.
