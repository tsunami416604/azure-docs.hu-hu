---
title: Parancsfájlművelet-alapú HDInsight - Azure-fejlesztés |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a Script Action Hadoop-fürtöket. Parancsfájlművelet használható egy fürtöt a telepített alkalmazások konfigurációjának módosítása vagy a Hadoop-fürtön futó további szoftverek telepíthetők.
services: hdinsight
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 8b00661e1561b4aa93be26994b20e33feac97ff6
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952368"
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>A HDInsight Windows-alapú fürtök Parancsfájlműveletekkel-parancsfájlok fejlesztése
Útmutató a HDInsight Script Action parancsfájlokat írhat. A Script Action parancsfájlok segítségével további információkért lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster.md). Ugyanahhoz a cikkhez írt Linux-alapú HDInsight-fürtök esetén, lásd: [Szkriptművelet fejlesztése HDInsight-parancsfájlok](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak Windows-alapú HDInsight-fürtök esetében működik. HDInsight csak akkor használható a Windows-verziók alacsonyabb, mint a HDInsight 3.4-es. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). Információk a Linux-alapú fürtök parancsfájlműveletekkel: [Szkriptművelet fejlesztése a HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



Parancsfájlművelet használható egy fürtöt a telepített alkalmazások konfigurációjának módosítása vagy a Hadoop-fürtön futó további szoftverek telepíthetők. A Parancsfájlműveletek olyan parancsfájlok, amelyek futtatását a fürt csomópontjai a HDInsight-fürtök, és a végrehajtás után a fürtben található csomópontok HDInsight konfigurálásának befejezéséhez. Szkriptműveletet hajtja végre a fiók rendszergazdai jogosultságokkal, és a fürtcsomópontok teljes körű hozzáférési jogosultságokat biztosít. Ha mindegyik fürthöz kell végrehajtani, amely a megadott sorrendben szkriptműveletek listája adható meg.

> [!NOTE]
> Ha a következő hibaüzenetet kapja:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: A kifejezés "Save-HDIFile" nem ismerhető fel egy parancsmag, a függvény, a parancsfájl vagy a működtethető program nevét. Ellenőrizze a helyesírást, a neve, vagy ha egy elérési út része, ellenőrizze, hogy az elérési út helyes, és próbálkozzon újra.
> Ez azért, mert nem tartozik a segédmetódusokat.  Lásd: [egyéni parancsfájlok segédmetódusokat](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Mintaszkriptek
A HDInsight-fürtök létrehozása Windows operációs rendszeren, a Script Action az Azure PowerShell-szkript. A következő parancsfájl egy mintát, a hely konfigurációs fájlok konfigurálása:

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

A parancsfájlnak, négy paramétert, a konfigurációs fájl neve, a módosítandó, a következőt kívánja beállítani, érték tulajdonság és egy leírást. Példa:

    hive-site.xml hive.metastore.client.socket.timeout 90

Ezek a paraméterek értékét hive.metastore.client.socket.timeout 90-re a hive-site.xml fájlban.  Az alapértelmezett értéke 60 másodperc.

Ez a példaszkript is található [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight további összetevők telepíthetők a HDInsight-fürtök számos szkript biztosítja:

| Name (Név) | Szkript |
| --- | --- |
| **A Spark telepítése** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Lásd: [telepítése és használata a Spark on HDInsight-fürtök][hdinsight-install-spark]. |
| **Az R telepítése** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Lásd: [telepítése és az R használata a HDInsight-fürtökön](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **A Solr telepítése** | `https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1`. Lásd: [telepítése és használata a Solr a HDInsight-fürtök](hdinsight-hadoop-solr-install.md). |
| **A Giraph telepítése** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Lásd: [telepítése és használata a Giraph a HDInsight-fürtök](hdinsight-hadoop-giraph-install.md). |
| **Hive-kódtárak előzetes betöltése** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Lásd: [adja hozzá a Hive-kódtárak a HDInsight-fürtök](hdinsight-hadoop-add-hive-libraries.md) |


Parancsfájlművelet is telepíthető, az Azure Portalon, az Azure PowerShell-lel vagy a HDInsight .NET SDK használatával.  További információkért lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel][hdinsight-cluster-customize].

> [!NOTE]
> Mintaszkriptek használata csak a HDInsight-fürt verziója 3.1-es vagy újabb. A HDInsight fürt verziókról további információkért lásd: [HDInsight fürtverziók](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Egyéni parancsfájlok segédmetódusokat
Parancsprogram-művelet segédmetódusokat olyan segédprogramok, amelyet használhat egyéni parancsfájlok írása közben. Ezek a metódusok vannak meghatározva [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1), és a parancsfájlok a következő minta használatával szerepelhet:

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

Ez a szkript által biztosított segítő módszerek a következők:

| Segédmetódus | Leírás |
| --- | --- |
| **Save-HDIFile** |Töltse le a fájlt, a megadott egységes erőforrás-azonosító (URI) egy olyan helyre, amely kapcsolódik az Azure-beli Virtuálisgép-csomóponttal, a fürt helyi lemezen. |
| **Expand-HDIZippedFile** |Bontsa ki a tömörített fájlt. |
| **Invoke-HDICmdScript** |Futtassa a parancsfájlt a cmd.exe. |
| **Write-HDILog** |Kiírhatja a kimenetet szkriptműveletet használt egyéni parancsfájl. |
| **Get-Services** |Ha a parancsfájl végrehajtása a gépen futó szolgáltatások listájának beolvasása. |
| **Get-Service** |A bemenetként megadott szolgáltatás nevét, az adott szolgáltatásokhoz részletes információkhoz juthat (a szolgáltatás neve, a folyamat azonosítója, állam stb.) a számítógépen, ahol a szkriptet hajt végre. |
| **Get-HDIServices** |A számítógépen futó, ahol a szkript végrehajtása HDInsight-szolgáltatások listájának beolvasása. |
| **Get-HDIService** |Az adott HDInsight szolgáltatásnévvel bemenetként, egy adott szolgáltatáshoz részletes információkhoz juthat (a szolgáltatás neve, a folyamat azonosítója, állam stb.) a számítógépen, ahol a szkriptet hajt végre. |
| **Get-ServicesRunning** |Futó szolgáltatások azon a számítógépen, a parancsfájl végrehajtása listájának beolvasása. |
| **Get-ServiceRunning** |Ellenőrizze, hogy egy adott szolgáltatáshoz (név alapján) fut-e a számítógépen, a parancsfájl végrehajtása. |
| **Get-HDIServicesRunning** |A számítógépen futó, ahol a szkript végrehajtása HDInsight-szolgáltatások listájának beolvasása. |
| **Get-HDIServiceRunning** |Ellenőrizze, hogy egy adott HDInsight szolgáltatáshoz (név alapján) fut-e a számítógépen, a parancsfájl végrehajtása. |
| **Get-HDIHadoopVersion** |Hadoop telepítve azon a számítógépen, ahol a parancsfájl végrehajtása verziójának beszerzéséhez. |
| **Test-IsHDIHeadNode** |Ellenőrizze, hogy a számítógépen, ahol a parancsfájl végrehajtása-e a fő csomópontot. |
| **Test-IsActiveHDIHeadNode** |Ellenőrizze, hogy a számítógépen, ahol a parancsfájl végrehajtása-e az aktív fő csomópont. |
| **Test-IsHDIDataNode** |Ellenőrizze, hogy a számítógépen, ahol a parancsfájl végrehajtása-e az adatok csomópont. |
| **Edit-HDIConfigFile** |A hive-site.xml konfigurációs fájlokat, a core-site.xml, a hdfs-site.xml, a mapred-site.xml vagy a yarn-site.xml szerkesztéséhez. |

## <a name="best-practices-for-script-development"></a>Ajánlott eljárások a parancsfájl-fejlesztés
Egy HDInsight-fürthöz tartozó egyéni parancsfájl fejlesztésekor van néhány ajánlott eljárásokat, és tartsa szem előtt:

* A Hadoop-verziójának ellenőrzése

    Csak a HDInsight (Hadoop 2.4) 3.1-es verzióját vagy újabb támogatási Szkriptműveletek használatával egyéni összetevők telepíthetők a fürtön. Az egyéni szkriptet kell használnia a **Get-HDIHadoopVersion** segédmetódus Hadoop verziójának és egyéb feladatok végrehajtása a szkriptet a továbblépés előtt.
* Adja meg a parancsfájl stabil hivatkozását

    Felhasználók gondoskodnia kell, hogy az összes parancsfájl és más összetevőket, használja a fürt testreszabása a fürt élettartama során elérhetők maradnak, és, hogy a fájlok verzióinak ne módosítsa az időtartam számára. Ezeket az erőforrásokat szükség, ha a fürtben található csomópontok rendszerképének alaphelyzetbe állítása szükséges. Az ajánlott eljárás, hogy töltse le, és archiválja mindent egy tárfiókot, amelyet a felhasználó szabályozza. Ez a fiók lehet az alapértelmezett tárfiókot, vagy a megadott üzembe helyezési testre szabott fürt időpontjában további tárfiókokat.
    A Spark-és R testreszabott fürt minták a dokumentációját, például van egy helyi példányát az erőforrásokat a tárfiók a megadott: https://hdiconfigactions.blob.core.windows.net/.
* Győződjön meg arról, hogy a fürt testreszabása szkript idempotens

    Vannak rendszerképét meg alaphelyzetbe, a fürt élettartama során állítják egy HDInsight-fürt csomópontjainak, hogy kell várt. A fürt testreszabása szkript fut le, amikor a fürt rendszerképének. Ez a szkript idempotens abban az értelemben, hogy esetén rendszerképének alaphelyzetbe állítása, a parancsfájl biztosítania kell, hogy a fürt ugyanazt a visszaadott állapot, amely csak akkor, ha a parancsfájl lefutott, az első alkalommal, amikor a fürt eredeti létrehozása volt testre szabott lennie kell megtervezni. Például ha egy egyéni parancsfájl az első futtatáskor D:\AppLocation az alkalmazást telepítette, majd minden egyes későbbi futtatásakor, esetén rendszerképének alaphelyzetbe állítása, a parancsfájl ellenőrizze-e a az alkalmazás D:\AppLocation helyen van, mielőtt továbblépne más lépéseit a parancsfájl.
* Egyéni összetevők optimális helyre fogja telepíteni

    Ha a fürtcsomópontok vannak rendszerképének a C:\ meghajtóra-erőforrás és a D:\ rendszermeghajtó is újraformázza, így az adatok és az alkalmazásokat, amelyek korábban telepítette az adott meghajtókat. Ez akkor is előfordulhat, ha egy Azure virtuális gép (VM) csomópontot, a fürt részét képező leáll, és a egy új csomópont helyébe. Összetevők a D:\ meghajtóra, vagy a fürtön a C:\apps helyen telepíthető. A C:\ meghajtón található más helyeken vannak fenntartva. Adja meg a helyet, alkalmazások és a könyvtárakat a fürt testreszabása szkriptet az telepíteni.
* Magas rendelkezésre állásának garantálása érdekében a fürt architektúra

    HDInsight van egy aktív – passzív architektúra magas rendelkezésre állás érdekében egyik fő csomópontja aktív módban (ahol a HDInsight-szolgáltatások futnak) és a fő csomópont (mely a HDInsight a szolgáltatások nem futnak) készenléti üzemmódban van. A csomópontok váltson aktív és passzív üzemmódban, ha HDInsight-szolgáltatások megszakadnak. Szolgáltatások telepítése a magas rendelkezésre állás érdekében két fő csomópontra szkriptműveletet használata esetén vegye figyelembe, hogy a HDInsight adatátvételi mechanizmus nem tudja automatikusan átadja a feladatokat a felhasználók által telepített szolgáltatások. Így által telepített szolgáltatásokhoz a HDInsight fő csomópont, amely várhatóan magas rendelkezésre állású kell, vagy saját adatátvételi mechanizmus, ha az aktív-passzív módban van, vagy aktív-aktív módban kell.

    Ha az átjárócsomópont szerepkör van megadva értékként a két fő csomópont futtat egy HDInsight-parancsfájlművelet parancs a *ClusterRoleCollection* paraméter. Így egyéni parancsfájl tervezésekor győződjön meg arról, hogy a parancsfájl figyelembe ezt a beállítást. Meg nem hibákba ütközne, ahol ugyanazok a szolgáltatások vannak telepítve, és mindkét a fő csomópont elindult és azok egymással versengő megtörténhet. Emellett vegye figyelembe, hogy történik adatvesztés rendszerképének alaphelyzetbe állítása, így parancsfájlművelet-n keresztül telepített szoftvereket is ellenáll az ilyen események. Alkalmazások kell kialakítani, hogy a munka sok csomóponton oszlik, magas rendelkezésre állású adatokkal. Akár 1/5, a fürt csomópontjai is lehet rendszerképét alaphelyzetbe állítják egyszerre.
* Az Azure Blob storage használata az egyéni összetevők konfigurálása

    Az egyéni összetevők, a fürtcsomópontokon telepített alapértelmezett konfigurációja a Hadoop elosztott fájlrendszer (HDFS) storage használata lehet. Módosítania kell a konfigurációt, használja helyette az Azure Blob storage. Egy fürt rendszerképeit alaphelyzetbe állítani a HDFS-fájlrendszer lekérdezi formázva, és Ön elveszítik az ott tárolt adatokat. Az Azure Blob storage inkább biztosítja, hogy az adatok őrződnek meg.

## <a name="common-usage-patterns"></a>Gyakori használati minták
Ez a szakasz útmutatást nyújt az megvalósítása a használt használati mintáit, amelyek a saját egyéni parancsfájlok írása közben mutatjuk be.

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása
Gyakran a parancsfájlművelet-alapú fejlesztés, úgy gondolja, hogy szükséges környezeti változókat. Például egy legvalószínűbb forgatókönyv akkor, ha egy külső helyről töltse le a bináris, telepítse azt a fürt és a hely, amelyre telepítve van, a "PATH" környezeti változó hozzáadása. Az alábbi kódrészlet bemutatja, hogyan állítsa be a környezeti változókat az egyéni szkript.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Ez az utasítás on értékűre állítja a környezeti változó **MDS_RUNNER_CUSTOM_CLUSTER** értéke "true", továbbá csoportok ezt a változót kell gépre kiterjedő hatókörét. Fontos, hogy a környezeti változók vannak beállítva a megfelelő hatókörben – számítógép vagy felhasználó. Tekintse meg [Itt] [ 1] környezeti változók beállításával kapcsolatos további információt.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Az egyéni parancsprogramok tárolására helyeken való hozzáférés
Egy fürt testreszabására szolgáló parancsfájlokat kell egyaránt lehet az alapértelmezett tárfiókot, a fürt vagy egy nyilvános olvasási tárolóban, a többi storage-fiók. Ha a parancsfájl máshol található erőforrásokhoz fér hozzá az erőforrásokat kell nyilvánosan is olvasható. Például előfordulhat, hogy szeretné elérni egy fájlt, és mentse a SaveFile-HDI-parancs segítségével.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Ebben a példában győződjön meg arról, hogy a tároló `somecontainer` tárfiókban `somestorageaccount` nyilvánosan elérhető-e. Ellenkező esetben a parancsfájl egy "Nem található" kivételt jelez, és sikertelen lesz.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Az Add-AzureRmHDInsightScriptAction parancsmag adja át a paramétereket
Az Add-AzureRmHDInsightScriptAction parancsmag több paraméterek adhatók át, a parancsfájl az összes paramétert tartalmazó karakterlánc értékének kell. Példa:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

vagy

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Kivétel esetén a meghibásodott fürt throw
Ha azt szeretné pontosan értesülni szeretne a tény, hogy a fürt testreszabása nem sikerült megfelelően működik, fontos, hogy kivételt, és a fürt létrehozása sikertelen. Például előfordulhat, hogy szeretné feldolgozni egy fájlt, ha létezik, és kezelni a hibaesetét, ahol a fájl nem létezik. Ez biztosítja, hogy a parancsfájl szabályosan kilép, és a fürt állapota megfelelően ismert. Az alábbi kódrészlet egy példán, hogyan lehet ezt elérni:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Ebben a kódrészletben Ha a fájl nem létezik, vezet, egy állapot, ahol a parancsfájl valóban kilép szabályosan a hibaüzenet nyomtatás után, és a fürt eléri a futó állapotba kerül, feltéve, hogy a "sikeres" fürt személyre szabásának fejeződött be. Ha azt szeretné, pontosan értesítést kapjon arról, hogy a fürt testreszabása lényegében nem sikerült egy hiányzó fájlok miatt a várt módon, hogy jobban megfelelő kivételt, és a fürt testreszabása lépés sikertelen lesz. Ennek érdekében inkább a következő minta kódrészletet kell használnia.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Ellenőrzőlista a központi telepítése egy parancsprogram-művelet
Ezek a szkriptek üzembe helyezéséhez előkészítésekor meggyőződtünk lépései a következők:

1. Helyezze el az olyan helyen, amely elérhető a fürt csomópontok üzembe helyezése során az egyéni parancsfájlok tartalmazó fájlokat. Ez az alapértelmezett vagy a további tárfiókok megadott időpontjában fürttelepítés, vagy bármely más nyilvánosan elérhető-e a tároló lehet.
2. Adjon hozzá ellenőrzéseket, parancsfájlok, győződjön meg arról, hogy azok idempotently, hajtsa végre, hogy a parancsfájl hajtható végre több alkalommal ugyanazon a csomóponton.
3. Használja a `Write-Output` STDOUT, valamint az STDERR nyomtatni az Azure PowerShell-parancsmagot. Ne használjon `Write-Host`.
4. Használjon egy ideiglenes mappába, például `$env:TEMP`, hogy tartsa meg a letöltött fájlt a parancsfájlok által használt, majd eltávolítással parancsfájlok rendelkezik végrehajtása után.
5. Csak a D:\ vagy C:\apps, egyéni szoftverek telepítését. A C: meghajtón található más helyekről kell nem használható, mivel azok le foglalva. Kívül esik a C:\apps mappát a C: meghajtón lévő fájlok telepítése eredményezhet a fürtbeállítási hibákhoz különbséglemezt a csomópont alatt.
6. Abban az esetben, ha az operációs rendszer szintű beállításokat vagy a Hadoop szolgáltatás konfigurációs fájlok megváltoztak, érdemes lehet, hogy indítsa újra a HDInsight szolgáltatásokat, így azok minden olyan operációsrendszer-szintű beállítások, például a környezeti változók beállítása a szkriptekben bejelentkeztél.

## <a name="debug-custom-scripts"></a>Egyéni parancsfájlok hibakeresése
A parancsfájl hibanaplóit tárolódnak, az alapértelmezett tárfiókot, a fürt a létrehozáskor megadott más kimeneti együtt. A naplók vannak tárolva egy tábla nevét *u < \cluster-name-fragment >< \time-stamp > setuplog*. Ezek a rekordok az összes, amelyen a parancsfájl a fürtben futó csomópontok (fő csomópontot és munkavégző csomópontok) rendelkező összesített naplókat.

Egyszerűen tekintse meg a naplókat, hogy HDInsight Tools for Visual Studio használata. Az eszközök telepítése, lásd: [Visual Studio Hadoop tools for HDInsight használatának első lépései](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**A Visual Studio használatával a naplóban**

1. Nyissa meg a Visual Studiót.
2. Kattintson a **nézet**, és kattintson a **Server Explorer**.
3. Kattintson a jobb gombbal az "Azure", kattintson a csatlakozás **a Microsoft Azure-előfizetések**, majd adja meg a hitelesítő adatait.
4. Bontsa ki a **tárolási**, bontsa ki az Azure storage-fiókot használja az alapértelmezett fájlrendszerként, **táblák**, majd kattintson duplán a tábla neve.

Akkor is távolról is megtekintheti az STDOUT és az STDERR egyéni szkriptek a fürt csomópontjai. A naplók minden egyes csomóponton csak ahhoz a csomóponthoz, és be vannak jelentkezve **C:\HDInsightLogs\DeploymentAgent.log**. Ezek a naplófájlok az egyéni szkriptet minden kimenő adatait rögzíti. Egy példa log kódrészlet egy Spark-szkript művelet így néz ki:

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


Ez a napló nincs bejelölve, hogy a Spark-szkript művelet lett végrehajtva a HEADNODE0 nevű virtuális gépre, és hogy nem tartalmaznak kivételeket a végrehajtás során fordultak elő.

Abban az esetben, ha végrehajtási hiba történik, az azt leíró kimeneti is ez a naplófájl tartalmazza. Ezek a naplók a információk hasznosak, ha esetleg felmerülő problémákat parancsprogram-hibakeresés kell lennie.

## <a name="see-also"></a>Lásd még
* [Szkriptműveletek használatával HDInsight-fürtök testre szabása][hdinsight-cluster-customize]
* [Spark telepítése és használata HDInsight-fürtökön][hdinsight-install-spark]
* [Soir telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-solr-install.md).
* [Giraph telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
