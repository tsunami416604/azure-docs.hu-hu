---
title: Áttelepítés a Windows-alapú HDInsight a Linux-alapú HDInsight – Azure
description: Ismerje meg, hogyan át egy Windows-alapú HDInsight-fürtöt Linux-alapú HDInsight-fürthöz.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: jasonh
ms.openlocfilehash: cb2ca9ac3be0034f5a90add58249a2c2043975d0
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094084"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Windows-alapú HDInsight-fürtök áttelepítése Linux-alapú fürtre

Ez a dokumentum részletesen HDInsight, a Windows és Linux közötti különbségeket. A meglévő számítási feladatok migrálása a Linux-alapú fürt útmutatást is biztosít.

Bár a Windows-alapú HDInsight a Hadoop felhőbeli használata egyszerű megoldást kínál, szükség lehet egy Linux-alapú fürtbe történő áttelepítéséhez. Ha például a Linux-alapú eszközök és technológiák, amelyek szükségesek a megoldás előnyeit. A Hadoop-ökoszisztéma sok-sok dolog Linux-alapú rendszereken lettek kifejlesztve, és nem lehet elérhető a Windows-alapú HDInsight segítségével. Számos könyveket, videók és más képzési anyagokat azt feltételezik, hogy használ egy Linux rendszert és a hadoop együttes használata során.

> [!NOTE]
> HDInsight-fürtök operációs rendszer Ubuntu hosszú távú támogatási (LTS) használata a fürt csomópontjain. A HDInsight, a rendelkezésre álló Ubuntu verzióját és egyéb összetevő is verzióinformációk kapcsolatos tudnivalókat lásd: [HDInsight összetevő verziók](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Áttelepítési feladatok

Az áttelepítés általános munkafolyamata a következőképpen történik.

![Migrálási munkafolyamat diagramja](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Olvassa el ezt a dokumentumot, megismerheti a migráció során esetleg szükséges módosításokat minden szakasza.

2. Hozzon létre egy Linux-alapú fürt egy tesztelési és minőség garancia környezetben. Egy Linux-alapú fürtök létrehozásával kapcsolatos további információkért lásd: [a HDInsight-fürtök létrehozása Linux-alapú](hdinsight-hadoop-provision-linux-clusters.md).

3. Másolja a meglévő feladatokat, adatok, forrásként és fogadóként az új környezetre.

4. Hajtsa végre az ellenőrzési tesztelés célja annak győződjön meg arról, hogy a feladatok az új fürtön a várt módon működik-e.

Miután ellenőrizte, hogy minden a várt módon működik, az áttelepítés tervezze. A leállás ideje alatt hajtsa végre a következő műveleteket:

1. Készítsen biztonsági másolatot a fürtcsomópontokon helyileg tárolt átmeneti adatok. Például, ha közvetlenül a központi csomóponton tárolt adatokat.

2. A Windows-alapú fürt törlése.

3. Hozzon létre egy Linux-alapú fürtöt az ugyanazon alapértelmezett adattár, amely a Windows-alapú fürt használja. A Linux-alapú fürt továbbra is a meglévő éles adataival végzett munkát.

4. Biztonsági másolatot készített az átmeneti adatok importálása.

5. Indítási feladatok/folytatni a használatával az új fürtön.

### <a name="copy-data-to-the-test-environment"></a>Adatok másolása a tesztkörnyezetben

Másolja az adatokat és a feladatok számos módszer áll rendelkezésre, azonban a jelen szakaszban ismertetett két a legegyszerűbb módszer áll közvetlenül helyezze át a fájlokat egy tesztfürt.

#### <a name="hdfs-copy"></a>HDFS-példány

A következő lépések használatával adatokat másol az éles fürtöt a vizsgálatot fürthöz. Ezek a lépések használják a `hdfs dfs` segédprogram, amely a HDInsight részét képezi.

1. Keresse meg a tárolási fiók és az alapértelmezett tároló a meglévő fürt adatai. Az alábbi példa Powershellt használ, ezek az információk lekéréséhez:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Létrehozhat egy tesztkörnyezetet, kövesse a Linux-alapú fürtök HDInsight a dokumentumban leírt lépéseket. A fürt létrehozása előtt állítsa le, és ehelyett válassza **opcionális konfigurációs**.

3. Válassza ki a választható konfiguráció szakaszt **a társított Tárfiókokban**.

4. Válassza ki **tárkulcs hozzáadása**, és amikor a rendszer kéri, válassza ki a tárfiókot, az 1. lépésben a PowerShell-parancsfájl által visszaadott. Kattintson a **kiválasztása** az egyes szakaszokban. Végül hozza létre a fürtön.

5. Ha a fürt létrejött, csatlakozhat hozzá **SSH.** További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Az SSH-munkamenetből a következő parancs használatával másolja a fájlokat a társított storage-fiókból az új alapértelmezett tárfiók. Cserélje le a tároló a PowerShell által visszaadott tároló információkkal. Cserélje le __fiók__ fiókja néven. Cserélje le az adatok elérési útja egy adat-fájl elérési útját.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Ha az adatokat tartalmazó a mappastruktúrát nem létezik a tesztkörnyezetben, hozhat létre a következő paranccsal:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    A `-p` kapcsoló lehetővé teszi, hogy az elérési út minden címtár létrehozása.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Az Azure Storage-blobok közötti közvetlen másolása

Azt is megteheti, előfordulhat, hogy szeretné használni a `Start-AzureStorageBlobCopy` másolhat át blobokat több tárfiók HDInsight-on kívül az Azure PowerShell-parancsmagot. További információkért tekintse meg a kezelése az Azure PowerShell az Azure Storage Azure-Blobok szakaszában.

## <a name="client-side-technologies"></a>Ügyféloldali technológiák

Ügyféloldali technológiák, például [Azure PowerShell-parancsmagok](/powershell/azureps-cmdlets-docs), [Azure CLI-vel](../cli-install-nodejs.md), vagy a [.NET SDK a Hadoophoz](https://hadoopsdk.codeplex.com/) Linux-alapú fürtök működni. Ezek a technológiák válik a REST API-k, amelyek azonosak mindkét fürt operációs rendszerének típusa.

## <a name="server-side-technologies"></a>Kiszolgálóoldali technológiák

A következő tábla áttelepítése kiszolgálóoldali összetevőivel, amelyek Windows-specifikus nyújt útmutatást.

| Ha ezt a technológiát használ... | Ez a művelet eltarthat... |
| --- | --- |
| **PowerShell** (kiszolgálóoldali parancsprogramok, beleértve a fürt létrehozása során használt Szkriptműveletek) |Újraírási, Bash-szkriptek. Szkriptműveletek, lásd: [testreszabása Linux-alapú HDInsight parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md) és [parancsfájlművelet-alapú fejlesztés a Linux-alapú HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **Az Azure CLI** (kiszolgálóoldali parancsprogramokat) |Az Azure CLI a Linux rendszeren érhető el, amíg azt nem olyan előre telepített a HDInsight-fürt átjárócsomópontjaiból a. Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Azure CLI 2.0 használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **.NET-összetevők** |Linux-alapú HDInsight segítségével a támogatott .NET [Mono](https://mono-project.com). További információkért lásd: [át .NET – megoldások a Linux-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **A Win32-összetevők és egyéb csak Windows technológiák** |Útmutatás az összetevő vagy technológiai függ. Előfordulhat, hogy egy olyanra, amely kompatibilis a Linux találja. Ha nem, ha egy másik megoldás, vagy ha újraírási ezt az összetevőt. |

> [!IMPORTANT]
> A HDInsight kezelése az SDK nem teljes mértékben kompatibilis a Mono. Ne használja a HDInsight-fürtön üzembe helyezett megoldások részeként.

## <a name="cluster-creation"></a>Fürt létrehozása

Ez a szakasz a fürt létrehozásakor különbségek ismertetése.

### <a name="ssh-user"></a>SSH felhasználói

Linux-alapú HDInsight-fürtök használata a **Secure Shell (SSH)** protokoll, a fürtcsomópontokon távoli elérést biztosíthat. Ellentétben a távoli asztal a Windows-alapú fürtök esetén a legtöbb SSH-ügyfél nem biztosítanak egy grafikus felhasználói felület. Ehelyett SSH-ügyfelet adjon meg egy parancssort, amely lehetővé teszi, hogy a fürtön futtasson parancsokat. Egyes ügyfelek (például [MobaXterm](http://mobaxterm.mobatek.net/)) adjon meg egy grafikus rendszer fájltallózó egy távoli parancssori mellett.

Fürt létrehozása során, és meg kell adnia egy SSH-felhasználót vagy egy **jelszó** vagy **nyilvános kulcsú tanúsítvány** a hitelesítéshez.

Azt javasoljuk, nyilvános kulcsú tanúsítvány, mivel biztonságosabb, mint a jelszó használatával. Tanúsítványalapú hitelesítés működik egy aláírt nyilvános/titkos kulcspár generálása, akkor a nyilvános kulcsot biztosít, a fürt létrehozásakor. Az SSH-val kiszolgálóhoz való csatlakozáshoz, akkor a titkos kulcs az ügyfél a kapcsolat hitelesítést biztosít.

További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Fürt testreszabása

**Szkriptműveletek** használt Linux-alapú fürtök kell megírni a Bash-szkript. Linux-alapú fürtök parancsfájlműveletekkel az alatt vagy fürt létrehozása után. További információkért lásd: [testreszabása Linux-alapú HDInsight parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md) és [parancsfájlművelet-alapú fejlesztés a Linux-alapú HDInsight](hdinsight-hadoop-script-actions-linux.md).

Egy másik testreszabási szolgáltatás **bootstrap**. A Windows-fürtök esetén ez a funkció lehetővé teszi, hogy a Hive segítségével megadhatja a helyét, további kódtárak pedig használatra. Fürt létrehozása után ezek a kódtárak érhetők el automatikusan a Hive-lekérdezések használata nélkül segítségével `ADD JAR`.

A rendszerindítási szolgáltatás Linux-alapú fürtök esetén nem nyújt ez a funkció. Ehelyett használjon szkriptműveletet részletes ismertetését lásd: [hozzáadása Hive-kódtárak a fürt létrehozásakor](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtuális hálózatok

Windows-alapú HDInsight-fürtök a klasszikus virtuális hálózatok csak munkahelyi Linux-alapú HDInsight-fürtök használatához a Resource Manager virtuális hálózatok. Ha a Linux-HDInsight-fürt csatlakoznia kell a klasszikus virtuális hálózat rendelkezik erőforrások, tekintse meg [egy klasszikus virtuális hálózat összekapcsolása egy Resource Manager virtuális hálózatot](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

A konfigurációs követelményekről további információkért lásd: a [HDInsight kiterjesztése funkciókat a Virtual Network használatával](hdinsight-extend-hadoop-virtual-network.md) dokumentumot.

## <a name="management-and-monitoring"></a>Kezelés és monitorozás

A webes Windows-alapú HDInsight, például a Feladatelőzményeket és a Yarn felhasználói felületén, a használt számos ambarival érhető el. Az Ambari Hive-nézet emellett biztosítja a webböngésző használatával Hive-lekérdezések futtatásához. Az Ambari webes felhasználói felület érhető el, a Linux-alapú fürtök https://CLUSTERNAME.azurehdinsight.net.

Az Ambari használatával kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [Az Ambari webes](hdinsight-hadoop-manage-ambari.md)
* [Az Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari-riasztások

Az Ambari van riasztási rendszer, amely eláruljuk, a fürt kapcsolatos lehetséges problémákat. Az Ambari webes Kezelőfelületen piros, sárga vagy bejegyzésként riasztások jelennek meg, azonban az is lekérhet őket a REST API-n keresztül.

> [!IMPORTANT]
> Ambari riasztások jelzi, hogy *előfordulhat, hogy* a probléma nem az, hogy ott *van* probléma. Például jelenhet meg a riasztást nem hiveserver2-n keresztül érhető el, hogy, annak ellenére, hogy elérhető-e megfelelően.
>
> Sok riasztást szolgáltatás időközönkénti lekérdezéseket vannak megvalósítva, és a egy megadott időkereten belül választ vár. A riasztás nem feltétlenül jelenti azt, hogy a szolgáltatás leállt, ezért most, hogy azt nem adott vissza eredményt, a várt időn belül.

## <a name="file-system-locations"></a>Fájlrendszerbeli helyeken

A Linux-fürt fájlrendszer működnek, mint a Windows-alapú HDInsight-fürtök van leírva. Az alábbi táblázat segítségével keresse meg a gyakran használt fájlok.

| Kell keresése... | ... |
| --- | --- |
| Konfiguráció |`/etc`. Például: `/etc/hadoop/conf/core-site.xml` |
| Naplófájlok |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Nincsenek a két címtár található itt, amely az aktuális HDP verzió és `current`. A `current` directory fájlok és mappák száma verzió könyvtárában található szimbolikus hivatkozásokat tartalmaz. A `current` directory kerül a HDP fájlokhoz fér hozzá a szám megváltozik a verzió óta, a HDP kényelmes módja verzióra frissült. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Általánosságban elmondható Ha ismeri a fájl nevét, használhatja a következő parancsot egy SSH-munkamenetből található a fájl elérési útja:

    find / -name FILENAME 2>/dev/null

A fájl nevét a helyettesítő karakterek is használhatja. Ha például `find / -name *streaming*.jar 2>/dev/null` visszaküldi az elérési út részeként a fájl nevét a "streaming" szót tartalmazó jar fájlok.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig és MapReduce

A Pig és MapReduce számítási feladatok Linux-alapú fürtökön hasonlóak. Azonban Linux-alapú HDInsight-fürtök hozható létre Hadoop-, Hive és Pig újabb verzióit. Ezek verzióeltérései vezethet be, hogyan változásai a meglévő megoldásokat függvényt. További információ a HDInsight részét képező összetevők verzióját, lásd: [HDInsight összetevők verziószámozása](hdinsight-component-versioning.md).

Linux-alapú HDInsight nem nyújt a távoli asztali funkcióit. Ehelyett az SSH segítségével távolról kapcsolódni a fürt átjárócsomópontjaiból. További információkért tekintse meg a következő dokumentumokat:

* [A Hive használata az ssh-val](hdinsight-hadoop-use-hive-ssh.md)
* [A Pig használata az ssh-val](hadoop/apache-hadoop-use-pig-ssh.md)
* [A MapReduce használata a az ssh-val](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Ha egy külső Hive-metaadattár használ, készítsen biztonsági másolatot a metaadattár használata a Linux-alapú HDInsight előtt. Linux-alapú HDInsight Hive, előfordulhat, hogy inkompatibilitásokat újabb verzióival történő érhető el a korábbi verziók által létrehozott metaadattárak.

Az alábbi ábra a Hive számítási feladatok migrálása nyújt útmutatást.

| A Windows-alapú, használni... | A Linux-alapú... |
| --- | --- |
| **Hive-szerkesztő** |[Az Ambari Hive nézete](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` Tez engedélyezése |Tez a helyzet a Linux-alapú fürtök alapértelmezett-végrehajtó motor már nincs szükség a set utasítás. |
| C# felhasználó által definiált függvények | Információ a Linux-alapú HDInsight-összetevők C# ellenőrzése: [át .NET – megoldások a Linux-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-fájlokat vagy parancsprogramokat a kiszolgálón, egy Hive-feladat részeként meghívása |Bash-szkriptek használata |
| `hive` a távoli asztal parancsot |Használat [Beeline](hadoop/apache-hadoop-use-hive-beeline.md) vagy [SSH-munkamenetből történő Hive](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| A Windows-alapú, használni... | A Linux-alapú... |
| --- | --- |
| C# felhasználó által definiált függvények | Információ a Linux-alapú HDInsight-összetevők C# ellenőrzése: [át .NET – megoldások a Linux-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-fájlokat vagy parancsprogramokat a kiszolgálón egy Pig feladat részeként meghívása |Bash-szkriptek használata |

### <a name="mapreduce"></a>MapReduce

| A Windows-alapú, használni... | A Linux-alapú... |
| --- | --- |
| C#-hozzárendelést és nyomáscsökkentő összetevők | Információ a Linux-alapú HDInsight-összetevők C# ellenőrzése: [át .NET – megoldások a Linux-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD-fájlokat vagy parancsprogramokat a kiszolgálón, egy Hive-feladat részeként meghívása |Bash-szkriptek használata |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Ha egy külső Oozie-metaadattár használ, készítsen biztonsági másolatot a metaadattár használata a Linux-alapú HDInsight előtt. Linux-alapú HDInsight egy Oozie, amelynek kompatibilitási problémák lehet újabb verziója érhető el a korábbi verziók által létrehozott metaadattárak.

Az Oozie-munkafolyamatok rendszerhéj műveletek engedélyezése. Rendszerhéj-műveletek az operációs rendszer alapértelmezett rendszerhéját használja a parancssori parancsok futtatásához. Ha a Windows felület támaszkodó Oozie-munkafolyamatok, meg kell felülírnia, munkafolyamatot, amelyet a Linux-rendszerhéj-környezetet (Bash) támaszkodik. Az Oozie használata a rendszerhéj műveletek további információkért lásd: [Oozie rendszerhéj műveleti bővítmény](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Ha egy munkafolyamatot, amely a C#-alkalmazás használ, ellenőrizze ezeket az alkalmazásokat a Linux-környezet. További információkért lásd: [át .NET – megoldások a Linux-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| A Windows-alapú, használni... | A Linux-alapú... |
| --- | --- |
| A Storm irányítópultja |A Storm irányítópultjának nem érhető el. Lásd: [futó Linux-alapú HDInsight üzembe helyezése és kezelése a Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md) módjaival topológiák elküldése |
| A Storm felhasználói felület |A Storm felhasználói felület érhető el: https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studióval való létrehozásához, üzembe helyezése és kezelése a C# vagy hibrid topológiák |A Visual Studio létrehozása, üzembe helyezése és kezelése a C# (SCP.NET) vagy a Linux-alapú Storm on HDInsight hibrid topológiák is használható. Csak használható 10/28/2016 után létrehozott fürtökkel. |

## <a name="hbase"></a>HBase

Linux-alapú fürtökön a znode szülő a hbase-hez van `/hbase-unsecure`. Ezt az értéket a konfigurációban a Java-ügyfelek bármelyike natív HBase Java API-t használó alkalmazásokat.

Lásd: [Java-alapú HBase-alkalmazás létrehozása](hdinsight-hbase-build-java-maven.md) egy példa-ügyfél, amely beállítja ezt az értéket.

## <a name="spark"></a>Spark

A Spark-fürtök az előzetes verzióban elérhető a Windows-fürtök voltak. Spark általánosan elérhető Linux-alapú fürtök csak érhető el. Nincs előzetes Windows-alapú Spark-fürtök áttelepítési útvonal egy kiadási Linux-alapú Spark-fürthöz.

## <a name="known-issues"></a>Ismert problémák

### <a name="azure-data-factory-custom-net-activities"></a>Az Azure Data Factory egyéni .NET-tevékenységek

Az Azure Data Factory egyéni .NET-tevékenységek jelenleg nem támogatottak a Linux-alapú HDInsight-fürtökön. Ehelyett használjon az alábbi módszerek közül az egyéni tevékenységek végrehajtására az ADF folyamat részeként.

* .NET-tevékenységek végrehajtása az Azure Batch-készletben. Tekintse meg a használatát az Azure Batch szolgáltatás szakaszában társított [egyéni tevékenységek használata Azure Data Factory-folyamatban](../data-factory/transform-data-using-dotnet-custom-activity.md)
* A tevékenység egy MapReduce-tevékenység végrehajtása. További információkért lásd: [MapReduce-programok meghívása a Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Sorvégződések

Windows-alapú rendszereken sorvégződések általában használni a CRLF, a Linux-alapú rendszerek használata LF Karakterrel. Módosítsa a meglévő adat-előállítók és fogyasztók LF dolgozni szeretne.

Például az Azure PowerShell-lel történő lekérdezés egy Windows-alapú fürt a HDInsight adatokat ad vissza a CRLF. A Linux-alapú fürtökkel ugyanabból a lekérdezés LF Karakterrel adja vissza. Ellenőrizze, hogy ha a sor vége a megoldással problémát okoz, a Linux-alapú fürt-ba való migrálás előtt.

Mindig használja a sor a fürtcsomópontokon futó parancsprogramok befejezési LF Karakterrel. Ha CRLF használ, előfordulhat, hogy hibába ütközik, amikor futtatja a parancsfájlokat egy Linux-alapú fürt.

Ha a parancsfájlok nem tartalmazzák a beágyazott a CR karakter karakterláncok, tömegesen módosítása a sorvégződések, az alábbi módszerek egyikével:

* **A fürthöz való feltöltéséhez**: a következő PowerShell-utasítások használatával módosítja a sorvégződések CRLF LF a parancsfájlt a fürtbe való feltöltéséhez.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **A fürthöz feltöltése után**: módosítsa a parancsfájlt a következő parancsot egy SSH-munkamenetből a Linux-alapú fürt használatával.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan hozhat létre Linux-alapú HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md)
* [Az SSH használata a HDInsight csatlakozni](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Linux-alapú fürt kezelése az Ambari](hdinsight-hadoop-manage-ambari.md)
