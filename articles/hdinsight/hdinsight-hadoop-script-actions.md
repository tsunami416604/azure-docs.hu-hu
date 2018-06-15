---
title: Parancsfájl-művelet fejlesztése a HDInsight - Azure |} Microsoft Docs
description: Ismerje meg a parancsfájlművelet Hadoop-fürtök testreszabása. Parancsfájl művelet fut a Hadoop-fürthöz további szoftvereket telepíteni, vagy módosítsa a fürt telepített alkalmazások használható.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 921da2db8e235e17611788cae7e976597bd76703
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271615"
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>A HDInsight-Windows-alapú fürtök parancsfájlművelet-parancsfájlok fejlesztése
A HDInsight parancsfájlművelet parancsfájlok írásának ismertetése. Parancsfájlművelet-parancsfájlok használatával kapcsolatos információkért lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster.md). A Linux-alapú HDInsight-fürtök írt ugyanazon cikk, lásd: [parancsfájlművelet fejlesztése parancsfájlok a HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Az ebben a dokumentumban csak a lépések Windows-alapú HDInsight-fürtök. HDInsight csak érhető el a Windows korábbi, mint a HDInsight 3.4-es verziójához. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A Parancsfájlműveletek használata a Linux-alapú fürtökön információkért lásd: [parancsfájl-művelet fejlesztése a HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



Parancsfájl művelet fut a Hadoop-fürthöz további szoftvereket telepíteni, vagy módosítsa a fürt telepített alkalmazások használható. A Parancsfájlműveletek olyan parancsfájlok, futtassa a fürtcsomópontokon, a HDInsight-fürtök telepítésekor, és a fürt csomópontjai HDInsight konfigurálásának befejezése után végrehajtás. A parancsfájlművelet hajtja végre a fiók rendszergazdai jogosultságokkal, és teljes hozzáférési jogosultsága ahhoz, hogy a fürt csomópontjai biztosít. Az egyes fürtökön megadható Parancsfájlműveletek hajthatnak végre, amely a megadott sorrendben listáját.

> [!NOTE]
> Ha a következő hibaüzenet:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: A kifejezés "Save-HDIFile" nem ismerhető fel egy parancsmag, a függvény, a parancsfájl vagy a futtatható program nevét. Ellenőrizze a helyesírást, a név, vagy ha egy elérési út része, ellenőrizze, hogy az elérési út helyességét, és próbálkozzon újra.
> Mivel az nem tartozik a segédmódszereket van.  Lásd: [segédmódszereket egyéni parancsfájlok](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Mintaszkriptek
A HDInsight-fürtök létrehozása a Windows operációs rendszeren, a parancsfájl művelete Azure PowerShell-parancsfájlt. A következő parancsfájlt a hely konfigurációs fájljainak konfigurálásához végrehajtott minta:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

A parancsfájl fogadja el a négy paraméter, a konfigurációs fájl nevét, a tulajdonságot kívánja módosítani, az értéket be szeretné állítani, és egy leírást. Példa:

    hive-site.xml hive.metastore.client.socket.timeout 90

Ezek a paraméterek értéke hive.metastore.client.socket.timeout 90 a hive-site.xml fájlban.  Az alapértelmezett értéke 60 másodperc.

A parancsfájlpéldát is találhatók [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight több parancsfájlok további összetevők telepíthetők a HDInsight-fürtök biztosítja:

| Name (Név) | Szkript |
| --- | --- |
| **Spark telepítése** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Lásd: [telepítése és használata a HDInsight Spark-fürtök][hdinsight-install-spark]. |
| **R telepítéséhez** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Lásd: [telepítése és használata R HDInsight-fürtök] [a hdinsight-r-parancsfájl]. |
| **Solr telepítése** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Lásd: [telepítése és használata Solr a HDInsight-fürtök](hdinsight-hadoop-solr-install.md). |
| - **Giraph telepítése** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Lásd: [telepítése és használata Giraph a HDInsight-fürtök](hdinsight-hadoop-giraph-install.md). |

Parancsfájlművelet is telepíthető, az Azure-portálon az Azure PowerShell vagy a HDInsight .NET SDK használatával.  További információkért lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet][hdinsight-cluster-customize].

> [!NOTE]
> A minta parancsfájlok csak a HDInsight-fürt verziószáma 3.1-es vagy újabb működik. A HDInsight-fürt verziókról további információkért lásd: [HDInsight-fürt verziókról](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Egyéni parancsfájlok segítő módszerei
Parancsfájl művelet segítő módszereket segédprogramok egyéni parancsfájlok írása közben használható. Ezek a módszerek definiált [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1), és a használatával a következő minta parancsfájlokat is szerepelhet:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Ez a parancsfájl által biztosított segítő módszerek a következők:

| Segédmetódus | Leírás |
| --- | --- |
| **Save-HDIFile** |Töltse le a fájlt a a megadott egységes erőforrás-azonosító (URI) a helyi lemezen, amely az Azure virtuális gép csomópontot a fürthöz rendelt helyre. |
| **Expand-HDIZippedFile** |Bontsa ki a tömörített fájlt. |
| **Invoke-HDICmdScript** |Futtassa a parancsfájlt a cmd.exe. |
| **Write-HDILog** |Kimeneti írása egy parancsfájl műveletéhez használt egyéni parancsfájl. |
| **Get-Services** |Ha a parancsfájl végrehajtása a gépen futó szolgáltatásokat listájának lekérése. |
| **Get-Service** |Az adott szolgáltatás nevű bemeneti adatként, részletes információ egy adott szolgáltatáshoz (a szolgáltatás neve, folyamatazonosító, állapot, stb.) Ha a parancsfájl végrehajtása a gépen. |
| **Get-HDIServices** |HDInsight services fut a számítógépen, ahol a parancsfájl végrehajtása listájának lekérése. |
| **Get-HDIService** |Az adott HDInsight szolgáltatásnévvel bemeneti adatként, részletes információ egy adott szolgáltatáshoz (a szolgáltatás neve, folyamatazonosító, állapot, stb.) Ha a parancsfájl végrehajtása a gépen. |
| **Get-ServicesRunning** |Futó szolgáltatásokat a számítógépen a parancsfájl végrehajtása ahol listájának lekérése. |
| **Get-ServiceRunning** |Ellenőrizze, hogy egy adott szolgáltatáshoz (a neve szerint) fut a számítógépen ahol a parancsfájl végrehajtása. |
| **Get-HDIServicesRunning** |HDInsight services fut a számítógépen, ahol a parancsfájl végrehajtása listájának lekérése. |
| **Get-HDIServiceRunning** |Ellenőrizze, hogy egy adott HDInsight-szolgáltatás (név) a számítógépen. Ha a parancsfájl végrehajtása. |
| **Get-HDIHadoopVersion** |A verziója telepítve azon a számítógépen, ahol a parancsfájl végrehajtása Hadoop beolvasása. |
| **Test-IsHDIHeadNode** |Ellenőrizze, hogy a számítógépen, ahol a parancsfájl végrehajtása egy átjárócsomóponttal. |
| **Test-IsActiveHDIHeadNode** |Ellenőrizze, hogy a számítógépen, ahol a parancsfájl végrehajtása az aktív központi csomópont. |
| **Test-IsHDIDataNode** |Ellenőrizze, hogy a számítógépen, ahol a parancsfájl végrehajtása egy adatcsomóponton. |
| **Edit-HDIConfigFile** |A konfigurációs fájlok hive-site.xml, a core-site.xml, a hdfs-site.xml, a mapred-site.xml vagy a yarn-site.xml szerkesztéséhez. |

## <a name="best-practices-for-script-development"></a>Parancsfájl fejlesztési ajánlott eljárásai
A HDInsight-fürtök egyéni parancsfájl fejlesztésekor van több bevált gyakorlatokat, amelyekkel tartsa szem előtt:

* A Hadoop-verziójának ellenőrzése

    Csak a HDInsight (Hadoop 2.4) 3.1-es verzióját vagy újabb támogatás parancsfájlművelet használatával egyéni összetevőinek telepítése egy fürt. Az egyéni parancsfájlt kell használnia a **Get-HDIHadoopVersion** segédmetódus Hadoop verziójának más feladatok végrehajtása a parancsfájl a folytatása előtt.
* Tartalmaznak egy stabil parancsfájl erőforrások

    Felhasználók győződjön meg arról, hogy a parancsfájlok és egyéb összetevők szerepel a testreszabás, a fürt teljes élettartama alatt a fürt elérhetők maradnak, és, hogy a fájlok verzióinak ne változtassa meg az az időtartam. Ezeket az erőforrásokat szükség, ha a fürtben található csomópontok különösen szükség. Az ajánlott eljárás, hogy töltse le és archiválni egy tárfiókot, amely a felhasználó a tartalmát. Ez a fiók lehet az alapértelmezett tárfiókot, vagy időpontban a központi telepítés testreszabott fürt megadott további tárfiókok.
    Fürt minták a Spark és R testreszabott, ha a dokumentáció, például van egy helyi példányát a tárfiókban lévő erőforrások: https://hdiconfigactions.blob.core.windows.net/.
* Győződjön meg arról, hogy a fürt testreszabási parancsfájl idempotent

    Várt kell, hogy a csomópontok egy HDInsight-fürt a fürt élettartama alatt vannak lemezképet. Amikor a fürt rendszerképének a fürt testreszabási parancsfájl futtatása. Ezt a parancsfájlt kell megtervezni, abban az értelemben, hogy szerepkörpéldány rendszerképét, akkor a parancsfájl győződjön meg arról, hogy a fürt küld vissza a ugyanaz az idempotent testreszabott állapotát az imént, miután a parancsfájl lefutott, a fürt kezdetben létrehozásának első alkalommal kell. Például ha egyéni parancsfájl telepítették az alkalmazást D:\AppLocation első futtassa, és minden ezt követő futtatáskor, különösen, akkor a parancsfájl ellenőrizze, hogy a az alkalmazás D:\AppLocation helyen van, a parancsfájlban szereplő lépések más folytatása előtt.
* Az optimális helyen egyéni összetevők telepítéséhez

    Ha a fürtcsomópontok vannak lemezképet, a C:\ meghajtó-erőforrás és a D:\ rendszermeghajtón is újraformázza, adatokat és alkalmazásokat, amelyek adott meghajtókon telepítette elvesztését eredményezi. Ez a veszteség is előfordulhat, ha egy Azure virtuális gép (VM) csomópontot, amely a fürt része leáll, és olyan új csomópont cseréli le. Összetevők a D:\ meghajtóra, vagy a fürt C:\apps helyen telepíthető. A C:\ meghajtón más helyeken vannak fenntartva. Adja meg a helyet Ha alkalmazások vagy a könyvtárak a fürt testreszabási parancsfájl telepíteni.
* Magas rendelkezésre állásának a fürt-architektúra

    HDInsight felépítésű egy aktív-passzív a magas rendelkezésre állás érdekében egy átjárócsomóponttal van aktív módot (ahol a HDInsight-szolgáltatás fut) és a más átjárócsomópont (mely a hdinsight szolgáltatások nem futnak) készenléti kiszolgálói módban van. A csomópontok aktív és passzív módban vált, ha a HDInsight szolgáltatások megszakadnak. A parancsfájlművelet kiszolgálók telepítését mindkét központi csomópont a magas rendelkezésre állású használata esetén vegye figyelembe, hogy a HDInsight feladatátvételi mechanizmus nem tudja, hogy automatikusan áthelyezze a feladatokat a felhasználók által telepített szolgáltatások. HDInsight központi csomópontokra, amelyeket várhatóan magas rendelkezésre állású legyen, felhasználók által telepített szolgáltatások vagy a saját feladatátvételi mechanizmus, ha az aktív-passzív módban van vagy kell aktív-aktív módban.

    Egy HDInsight-parancsfájlművelet parancs mindkét központi csomópontján fut, amikor az átjárócsomópont szerepkör van megadva értékként a *ClusterRoleCollection* paraméter. Egyéni parancsfájl tervezésekor ügyeljen, hogy, hogy a telepítő tisztában-e a parancsfájlt. A problémák, ahol ugyanazok a szolgáltatások telepítve, és mindkét központi csomópont elindult és azok egymással versengő végül nem kell futtatnia. Is vegye figyelembe, hogy adatok nem vesztek el során, különösen, tehát parancsfájlművelet keresztül telepített szoftverek ezek az események rugalmasak lehetnek. Alkalmazások úgy kell megtervezni, magas rendelkezésre állású adatait, amely van osztva sok csomópontjai között. Akár 1/5 egy fürt csomópontja egyidejűleg lemezképet is lehet.
* Az Azure Blob storage használata egyéni összetevők konfigurálása

    Az egyéni összetevők, amelyek telepítése a fürtcsomópontokon előfordulhat, hogy rendelkezik a Hadoop elosztott fájlrendszerrel (HDFS) tárolót használjanak alapértelmezett konfigurációval. Módosítania kell a konfigurációt használja helyette az Azure Blob Storage tárolóban. A fürt lemezkép alaphelyzetbe a HDFS fájlrendszer formázott lekérdezi és elveszítik az ott tárolt adatokat. Azure Blob storage használatával helyette biztosítja, hogy az adatok őrződnek meg.

## <a name="common-usage-patterns"></a>Gyakori használati minták
Ez a szakasz néhány gyakori használati mintái, amelyek a saját egyéni parancsfájl írásakor mutatjuk be végrehajtási nyújt útmutatást.

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása
Gyakran a parancsfájl művelet fejlesztési, úgy érzi, hogy a környezeti változók megadása szükséges. Például legvalószínűbb forgatókönyv esetén bináris egy külső webhelyről töltheti le, telepítse azt a fürt, és adja hozzá a "PATH" környezeti változó a telepítés helyét. Az alábbi kódrészletben bemutatja, hogyan környezeti változókat beállítani az egyéni parancsprogramok futtatására.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

A jelen nyilatkozat beállítja a környezeti változó **MDS_RUNNER_CUSTOM_CLUSTER** értékre a "true", továbbá beállítja ezt a változót kell gépre kiterjedő hatóköre. Fontos, hogy a környezeti változók a megfelelő hatókörben – számítógép vagy felhasználó van beállítva. Tekintse meg a [Itt] [ 1] környezeti változók beállításával kapcsolatos további információt.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Hozzáférés az egyéni parancsfájlok tároló helyekhez
A fürt testreszabásához használt parancsfájlok kell bármelyik kell az alapértelmezett tárfiókot, a fürt vagy egy nyilvános csak olvasható tároló bármely más tárfiók. Ha a parancsfájl máshol található erőforrásokhoz fér hozzá az erőforrásokat nyilvánosan olvashatónak kell lennie. Például előfordulhat, hogy szeretne hozzáférni egy fájlhoz, és mentse a SaveFile-HDI-paranccsal.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Ebben a példában, győződjön meg arról, hogy a tároló `somecontainer` tárfiókban `somestorageaccount` nyilvánosan elérhető. Ellenkező esetben a parancsfájl "Nem található" kivételt okoz, és sikertelen lesz.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Az Add-AzureRmHDInsightScriptAction parancsmagnak paraméterekkel
Az Add-AzureRmHDInsightScriptAction parancsmag több paraméterekkel, magában foglalja a parancsfájl az összes paraméter értékeként karakterlánc formázni kell. Példa:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

vagy

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Sikertelen fürttelepítés kivétel throw
Ha pontosan értesítés szeretné, hogy a fürt testreszabási sikertelen volt a vártnak, fontos, hogy kivételt jelez, és a fürt létrehozása sikertelen. Például előfordulhat, hogy szeretne feldolgozni egy fájlt, ha létezik, és kezelni a hiba eset, ha a fájl nem létezik. Ez biztosítja, hogy a parancsfájl szabályosan kilép, és a fürt állapota megfelelően van azonosítva. A következő példa bemutatja, hogyan ennek érdekében a következő kódrészletet:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Ezt a kódrészletet a Ha a fájl nem létezett, vezet, olyan állapotban, ahol a parancsfájl ténylegesen kilép szabályosan a hibaüzenet a következő nyomtatás után, és a fürt eléri futó állapotban, feltéve, hogy a "sikeres" fürt szabásának fejeződött be. Pontosan értesíti arról, hogy a fürt lényegében testreszabási Ha nem sikerült egy hiányzó fájlok miatt várt módon, hogy jobban megfelelő kivételt jelez, és a fürt testreszabási lépés sikertelen lesz. Ennek eléréséhez kell használnia a következő minta kódrészletet.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Ellenőrzőlista a üzembe helyezéséhez egy parancsfájlművelet
Azt a parancsfájlok telepítendő előkészítésekor tartott lépései a következők:

1. Helyezze el az egyéni parancsfájlok, amely elérhető a fürt csomópontjai a telepítés során helyen tartalmazó fájlokat. Ez az alapértelmezett vagy a fürtöt tartalmazó környezetben, vagy bármely más nyilvánosan elérhető tároló időpontjában megadott további tárfiókok lehet.
2. Győződjön meg arról, hogy azok végrehajtási idempotently, így a parancsfájl hajtható végre több alkalommal ugyanazon a csomóponton parancsfájlok ellenőrzést hozzáadása.
3. Használja a `Write-Output` Azure PowerShell-parancsmag segítségével STDOUT, valamint az STDERR dokumentumokat nyomtassanak azokon. Ne használjon `Write-Host`.
4. Használjon például egy ideiglenes mappába `$env:TEMP`, hogy a parancsfájlok által használt a letöltött fájlt, majd eltávolítással parancsfájlok rendelkezik végrehajtása után.
5. Csak a D:\ vagy C:\apps egyéni szoftver telepítése. A C: meghajtón más helyeken nem használható, azok le foglalva. A C: meghajtón a C:\apps mappán kívüli fájlok telepítése azt eredményezheti, a telepítő hibákat reimages a csomópont alatt.
6. Abban az esetben, ha az operációs rendszer szintű beállításokat vagy Hadoop szolgáltatás konfigurációs fájlok módosultak, érdemes lehet indítsa újra a HDInsight-szolgáltatásokat, így kiválaszthatja a bármely az operációs rendszer szintű beállításokat, például a parancsfájlokban beállított környezeti változókat.

## <a name="debug-custom-scripts"></a>Egyéni parancsfájlok hibakeresése
A parancsfájl hibanaplókat más kimeneti az alapértelmezett tárfiókot, ahol létrehozták a fürthöz megadott együtt tárolják. A naplók tárolódnak a nevű tábla *u < \cluster-name-fragment >< \time-stamp > setuplog*. Ezek a rekordok, az összes, amelyen a parancsprogram lefut a fürtben lévő csomópontok (átjárócsomópont és feldolgozó csomópontokat) összesített naplók.

Ellenőrizze a naplókat, egyszerűen a HDInsight Tools for Visual Studio használandó. Az eszközök telepítése, lásd: [első lépések a Visual Studio Hadoop tools for HDInsight használatával](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**A részletek a naplóban a Visual Studio használatával**

1. Nyissa meg a Visual Studiót.
2. Kattintson a **nézet**, és kattintson a **Server Explorer**.
3. Kattintson a jobb gombbal az "Azure", kattintson a Csatlakozás gombra **Microsoft Azure-előfizetések**, és írja be a hitelesítő adatait.
4. Bontsa ki a **tárolási**, bontsa ki a rendszer az alapértelmezett fájlrendszert használja az Azure storage-fiók, **táblák**, majd kattintson duplán a tábla neve.

Akkor is távoli az STDOUT és az STDERR megjelenítéséhez egyéni parancsfájlok a fürt csomópontjai. A naplók minden egyes csomóponton csak ahhoz a csomóponthoz, és be vannak jelentkezve **C:\HDInsightLogs\DeploymentAgent.log**. Ezekben a naplófájlokban rögzíti az egyéni parancsfájl az összes kimenetének. A külső parancsfájl művelet egy példa napló részlet így néz ki:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Ez a napló az egyszerű, hogy a külső parancsfájl művelet lett végrehajtva a HEADNODE0 nevű virtuális Gépre, és, hogy a kivételek fordultak elő a végrehajtás során is.

Abban az esetben, ha végrehajtási hiba történik, a kimeneti leíró azt is ez a naplófájl tartalmazza. Ezek a naplók található információk is segíthetnek megoldani az esetleg felmerülő problémákat parancsfájl.

## <a name="see-also"></a>Lásd még
* [Parancsfájlművelet HDInsight-fürtök testreszabása][hdinsight-cluster-customize]
* [Telepítse, és válassza a Spark on HDInsight-fürtök][hdinsight-install-spark]
* [Telepítheti és használhatja a HDInsight-fürtök Solr](hdinsight-hadoop-solr-install.md).
* [Telepítheti és használhatja a HDInsight-fürtök Giraph](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
