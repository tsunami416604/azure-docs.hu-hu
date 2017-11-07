---
title: "A Parancsfájlműveletek - Azure HDInsight-fürtök testreszabása |} Microsoft Docs"
description: "Linux-alapú HDInsight-fürtök Parancsfájlműveletek segítségével egyéni összetevők hozzáadása. A Parancsfájlműveletek olyan Bash parancsfájlok, amelyek segítségével testre szabhatja a fürtkonfiguráció, vagy adja hozzá a további szolgáltatások és segédprogramok Hue, Solr vagy R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: larryfr
ms.openlocfilehash: f166158d09cd867718acecc6c97ce16b839f49bd
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>A Parancsfájlműveletek Linux-alapú HDInsight-fürtök testreszabása

A HDInsight lehetővé nevű konfigurációs beállítás **parancsfájlművelet** , amely elindítja az egyéni parancsfájlok, amelyek a fürt testreszabása. További összetevők és konfigurációs beállításokat módosítaná, ezek a parancsfájlok segítségével. A Parancsfájlműveletek használható során vagy a fürt létrehozása után.

> [!IMPORTANT]
> A Parancsfájlműveletek használhatja egy már futó fürt Linux-alapú HDInsight-fürtök csak érhető el.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

A Parancsfájlműveletek is tehetők közzé az Azure piactéren HDInsight-alkalmazásokat. E dokumentumban szereplő példák némelyike bemutatják, hogyan telepíthet a HDInsight-alkalmazások parancsfájl művelet parancsokat a PowerShell és a .NET SDK használatával. További információk a HDInsight-alkalmazások: [közzé HDInsight-alkalmazások az Azure piactéren](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Engedélyek

Ha egy tartományhoz csatlakozó HDInsight-fürtöt használ, nincsenek két Ambari szükséges engedélyek a fürt integrált parancsfájl-műveletek használata esetén:

* **AMBARI. Futtatás\_egyéni\_parancs**: az Ambari rendszergazdai szerepkör rendelkezik ezzel az engedéllyel, alapértelmezés szerint.
* **A FÜRT. Futtatás\_egyéni\_parancs**: a HDInsight fürt rendszergazdája és az Ambari rendszergazda alapértelmezés szerint rendelkezik ilyen engedéllyel.

Tartományhoz csatlakozó HDInsight engedélyeket munkáról bővebben lásd: [tartományhoz HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Hozzáférés-vezérlés

Ha nem az Azure-előfizetése rendszergazdája vagy tulajdonosa, a fióknak rendelkeznie kell legalább **közreműködő** az erőforráscsoport, amely tartalmazza a HDInsight-fürthöz való hozzáférés.

Emellett ha hoz létre HDInsight-fürtöt, valaki legalább **közreműködő** az Azure-előfizetés hozzáférést kell már korábban regisztrált a HDInsight-szolgáltatója. A szolgáltató regisztrálása akkor történik meg, ha egy olyan felhasználó, aki Közreműködői hozzáféréssel rendelkezik az előfizetéshez, először hoz létre erőforrást az előfizetésben. Az erőforrás létrehozása nélkül is elvégezhető, ha [ a REST segítségével regisztrál egy szolgáltatót](https://msdn.microsoft.com/library/azure/dn790548.aspx).

A hozzáférés-kezelés használatával kapcsolatos további információk a következő dokumentumokban találhatók:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../active-directory/role-based-access-control-what-is.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>A Parancsfájlműveletek ismertetése

A parancsfájlművelet Bash parancsfájlok mutató URI-t biztosító, de a paraméterek. A parancsfájl a HDInsight-fürt csomópontján fut. Jellemzőit és Parancsfájlműveletek jellemzői a következők:

* A HDInsight-fürt által elérhető URI kell tárolni. A lehetséges tárolási helyek a következők:

    * Egy **Azure Data Lake Store** fiókkal, amely a HDInsight-fürt által elérhető. Azure Data Lake Store és a HDInsight együttes használatáról további információért lásd: [HDInsight-fürtök létrehozása a Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        A Data Lake Store-ban tárolt parancsfájl használata esetén az URI-formátum van `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > A HDInsight a Data Lake Store elérésére használt egyszerű a parancsfájl olvasási hozzáféréssel kell rendelkeznie.

    * A blob egy **Azure Storage-fiók** , amely vagy az elsődleges vagy további tárfiók a HDInsight-fürthöz. A HDInsight hozzáférést kap mindkét fenti támadástípus tárfiókok fürt létrehozása során.

    * Egy nyilvános fájlmegosztási szolgáltatással, például az Azure Blob, a GitHub, a onedrive vállalati verzió, a Dropbox, a stb.

        Például URI, tekintse meg a [parancsfájl művelet parancsfájlpéldákat](#example-script-action-scripts) szakasz.

        > [!WARNING]
        > Csak a HDInsight támogatja __általános célú__ Azure Storage-fiókokat. Jelenleg nem támogatja a __Blob-tároló__ fiók típusa.

* Korlátozható **futhat az egyes csomóponttípusok**, például átjárócsomópontokkal vagy munkavégző csomópontokhoz.

  > [!NOTE]
  > HDInsight prémium használata esetén adja meg, hogy a parancsfájl az élcsomóponthoz kell használni.

* Lehet **megőrzött** vagy **alkalmi**.

    **Megőrzött** parancsfájlok munkavégző csomópontokhoz felvenni a fürtbe, a parancsfájl futtatása után is vonatkozik. Például, amikor a fürt vertikális felskálázásával.

    Egy megőrzött parancsfájl típusú csomópont egy másik, például egy átjárócsomóponttal módosításokat is jelentkezhet.

  > [!IMPORTANT]
  > A megőrzött Parancsfájlműveletek egy egyedi névvel kell rendelkeznie.

    **Az ad hoc** parancsfájlok nem maradnak meg. Rendszer nem alkalmazza azokat a fürtbe felvenni, miután a parancsfájl lefutott rendelkezik munkavégző csomópontokhoz. Ezt követően egy ad hoc parancsfájl egy megőrzött parancsfájl előléptetéséhez, vagy egy ad hoc parancsfájl megőrzött parancsfájl fokozni.

  > [!IMPORTANT]
  > Fürt létrehozása során használt Parancsfájlműveletek automatikusan megmaradnak.
  >
  > Parancsfájlok, amelyek nem sikertelen tárolt, még akkor is, ha kifejezetten Megadja, hogy azok kell lennie.

* Fogadhat **paraméterek** használt a parancsfájl végrehajtása közben.
* Futtassa a **gyökér szintű jogosultságokkal** a fürtcsomópontokon.
* Keresztül is használható a **Azure-portálon**, **Azure PowerShell**, **Azure CLI**, vagy **HDInsight .NET SDK**

A fürt megtart lett futtatott összes parancsfájl előzményeit. Az előzmények akkor hasznos, ha a parancsfájl-azonosító található a(z) előléptetés vagy a lefokozás műveletek kell.

> [!IMPORTANT]
> Nincs automatikus mód egy parancsfájl művelet által végrehajtott módosítások visszavonásához. Vagy manuálisan a változtatásokat, vagy adjon meg egy parancsfájlt, amely visszavonja őket.


### <a name="script-action-in-the-cluster-creation-process"></a>A Fürtlétrehozási folyamat parancsfájlművelet

Fürt létrehozása során használt Parancsfájlműveletek némileg eltérnek a műveletek egy meglévő fürthöz futtatott parancsfájl:

* A parancsfájl **automatikusan megőrzött**.
* A **hiba** a parancsfájl azt okozhatja, a Fürtlétrehozási folyamat sikertelen lesz.

A következő ábra szemlélteti a parancsfájl művelet végrehajtásakor a létrehozási folyamat során:

![A HDInsight fürt testreszabási és fürt létrehozása során szakaszai][img-hdi-cluster-states]

A parancsprogram lefut, amíg a HDInsight konfigurálása. Ebben a szakaszban a parancsfájl a megadott csomópontok a fürt párhuzamosan fut, és a csomópontokon legfelső szintű jogosultságokkal fut.

> [!NOTE]
> Mivel a parancsfájl a fürtcsomópontokon gyökér szintű jogosultságokkal fut, mint például a szolgáltatások, beleértve a Hadoop-kapcsolatos szolgáltatások indítása és leállítása műveleteket hajthat végre. Ha leállítja a szolgáltatások, bizonyosodjon meg, hogy az Ambari szolgáltatás és más Hadoop kapcsolatos szolgáltatások működik, és mielőtt a parancsfájl befejezése után történik. Ezek a szolgáltatások szükségesek sikeresen határozza meg a rendszerállapot és a fürt állapotának azt létrehozása közben.


Fürt létrehozása során is használhat több Parancsfájlműveletek egyszerre. A parancsfájlokat a megadott volt sorrendben hívják.

> [!IMPORTANT]
> Parancsfájlműveletek 60 perc, vagy időtúllépés belül kell végrehajtani. Fürt telepítése során, a parancsfájl fut egyidejűleg más beállítás és konfiguráció folyamatok. Erőforrások, például a CPU-idő vagy a hálózati sávszélesség erőforrásokért való versengés okozhat a parancsfájl a befejezéshez, mint a fejlesztési környezetet az hosszabb időt vesz igénybe.
>
> A parancsfájl futtatásához szükséges idő minimalizálása érdekében ne feladatokat, mint a letöltés és forrásból származó alkalmazások összeállítására. Előre lefordítani az alkalmazások és a bináris fájlt az Azure Storage tárolja.


### <a name="script-action-on-a-running-cluster"></a>Egy futó fürtön parancsfájlművelet

Egy hiba történt egy parancsfájlt a fürt létrehozása során használt műveletek egy már futó fürtön futtatott parancsfájl eltérően automatikusan indítják el a fürt módosítása sikertelen állapotba. Miután befejeződött egy parancsfájl, a fürt "fut" állapotba kell visszaadnia.

> [!IMPORTANT]
> Akkor is, ha a fürt "fut" állapotú, előfordulhat, hogy a sikertelen parancsfájlt megszakítják dolgot. Például egy parancsfájlt a fürt számára szükséges fájlok sikerült törölni.
>
> Parancsfájlok műveletek futtassa legfelső szintű jogosultságokkal, ezért meg kell győződnie arról, hogy tudomásul veszi a parancsfájl funkciója mielőtt telepítené azt a fürtöt.

Egy parancsfájl fürtre alkalmazásakor a fürt állapota megosztottról **futtató** való **elfogadott**, majd **HDInsight konfigurációs**, és végül biztonsági  **Futó** sikeres parancsfájlok. A parancsfájl állapota a parancsfájlművelet előzményeinek van bejelentkezve, és ezen információk használatával határozza meg, hogy a parancsfájl sikeres vagy sikertelen volt. Például a `Get-AzureRmHDInsightScriptActionHistory` PowerShell-parancsmag segítségével egy parancsfájl állapotának megtekintése. Olyan információkat ad vissza az alábbihoz hasonló:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> A fürt felhasználói (rendszergazda) jelszavát megváltoztatta a fürt létrehozása után, ha parancsfájlt a fürt lefutott műveletek sikertelenek lehetnek. Ha bármely a megőrzött Parancsfájlműveletek adott cél munkavégző csomópontokhoz, ezek a parancsfájlok nem tud a fürt méretezni.

## <a name="example-script-action-scripts"></a>Példa parancsfájl művelet parancsfájlok

Parancsfájl művelet parancsfájlok segítségével az alábbi segédprogramokat használhatja:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight parancsprogramokat a HDInsight-fürtök az alábbi összetevők telepítése itt:

| Név | Szkript |
| --- | --- |
| **Egy Azure Storage-fiók hozzáadása** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxaddstorageaccountv01/Add-Storage-Account-v01.SH. Lásd: [további tárhelyet adhat a HDInsight-fürtök](hdinsight-hadoop-add-storage.md). |
| **Hue telepítése** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-hue-uber-v02.SH. Lásd: [telepítése és használata a HDInsight Hue-fürtök](hdinsight-hadoop-hue-linux.md). |
| **Presto telepítése** |https://RAW.githubusercontent.com/hdinsight/presto-hdinsight/Master/installpresto.SH. Lásd: [telepítése és használata Presto a HDInsight-fürtök](hdinsight-hadoop-install-presto.md). |
| **Solr telepítése** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxsolrconfigactionv01/solr-Installer-v01.SH. Lásd: [telepítése és használata Solr a HDInsight-fürtök](hdinsight-hadoop-solr-install-linux.md). |
| **Giraph telepítése** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxgiraphconfigactionv01/giraph-Installer-v01.SH. Lásd: [telepítése és használata Giraph a HDInsight-fürtök](hdinsight-hadoop-giraph-install-linux.md). |
| **Hive-könyvtárakhoz előzetes betöltése** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxsetupcustomhivelibsv01/Setup-customhivelibs-v01.SH. Lásd: [kódtárak hozzáadása Hive HDInsight-fürtök](hdinsight-hadoop-add-hive-libraries.md). |
| **Mono telepítése vagy frissítése** | https://hdiconfigactions.BLOB.Core.Windows.NET/Install-mono/Install-mono.bash. Lásd: [telepítése vagy frissítése a HDInsight monó](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Egy parancsfájlművelettel fürt létrehozása során

Ez a szakasz a különböző módszereket, Parancsfájlműveletek létrehozása a HDInsight-fürtök esetén a példákat.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Egy parancsfájlművelettel során fürt létrehozása az Azure-portálon

1. Indítsa el a fürt létrehozása a részben ismertetett módon [Hadoop létrehozása a HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md). Állítsa le, ha eléri a __fürt összefoglaló__ szakasz.

2. Az a __fürt összefoglaló__ szakaszban jelölje be a __szerkesztése__ hivatkozás a __speciális beállítások__.

    ![Speciális beállítások hivatkozása](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Az a __speciális beállítások__ szakaszban jelölje be __parancsfájl-műveletek__. Az a __parancsfájl-műveletek__ szakaszban jelölje be __+ Submit újdonságai__

    ![Egy új parancsfájlművelet](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Használja a __válassza ki a parancsprogramot__ bejegyzés egy előre elkészített parancsfájl kiválasztásához. Egyéni parancsfájl használata, válassza ki a __egyéni__ és adja meg a __neve__ és __Bash parancsfájlok URI__ a parancsfájlt.

    ![A parancsprogramok hozzáadásához válassza ki a parancsfájl formájában](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A következő táblázat ismerteti az elemeket az űrlapon:

    | Tulajdonság | Érték |
    | --- | --- |
    | Válassza ki a parancsprogramot | A saját parancsfájl használatához válassza __egyéni__. Ellenkező esetben válasszon egyet a megadott parancsprogramok. |
    | Név |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájlok URI |Adja meg az URI-t a parancsfájlt, amelyet a fürt testreszabásához. |
    | HEAD/munkavégző/Zookeeper |Adja meg a csomópontok (**Head**, **munkavégző**, vagy **ZooKeeper**) a a testreszabási parancsfájl futtatásához. |
    | Paraméterek |Adja meg a paraméterek, ha a parancsfájl által igényelt. |

    Használja a __parancsfájlműveletet__ bejegyzést, hogy a parancsfájl skálázás műveletek során van alkalmazva.

5. Válassza ki __létrehozása__ a parancsfájl mentése. Ezután __+ új küldési__ egy másik parancsfájl hozzáadásához.

    ![Több parancsprogram-műveletek](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Amikor elkészült a parancsfájlok hozzáadását, használja a __válassza__ gomb, majd a __tovább__ gombra kattintva visszatérhet a __fürt összefoglaló__ szakasz.

3. A fürt létrehozásához válassza __létrehozása__ a a __fürt összefoglaló__ kijelölés.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Egy parancsfájlművelettel Azure Resource Manager-sablonok alapján

A Parancsfájlműveletek Azure Resource Manager sablonokban használható. Egy vonatkozó példáért lásd: [https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/](https://azure.microsoft.com/en-us/resources/templates/hdinsight-linux-run-script-action/).

Ebben a példában a parancsfájlművelet hozzáadása a következő kódot:

    "scriptActions": [
        {
            "name": "setenvironmentvariable",
            "uri": "[parameters('scriptActionUri')]",
            "parameters": "headnode"
        }
    ]

Egy sablon telepítésének módjáról további információkért lásd a következő dokumentumokat:

* [Erőforrások sablonok és Azure PowerShell telepítése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Erőforrások sablonok és az Azure parancssori felület telepítése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Egy parancsfájlművelettel Azure PowerShell a fürt létrehozása során

Ebben a szakaszban használhatja a [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) parancsmag meghívni a parancsfájlokat egy fürt testreszabásához. A folytatás előtt győződjön meg arról, hogy telepítette és konfigurálta az Azure PowerShell. A munkaállomás HDInsight PowerShell-parancsmagok futtatásához konfigurálásával kapcsolatos további információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).

Az alábbi parancsfájl bemutatja, hogyan alkalmazza a parancsfájlművelet, ha a PowerShell egy fürtöt hoz létre:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

A fürt létrehozása előtt több percet is igénybe vehet.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Egy parancsfájlművelettel a HDInsight .NET SDK-ból a fürt létrehozása során

A HDInsight .NET SDK biztosít, amely megkönnyíti a HDInsight használata a .NET-alkalmazás klienskódtárak segítségével. Kódminta, lásd: [fürtök létrehozása Linux-alapú hdinsight .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>A parancsfájlművelet alkalmazása egy működő fürthöz

Ebben a szakaszban megtudhatja, hogyan Parancsfájlműveletek alkalmazandó működő fürthöz.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>A parancsfájlművelet alkalmazása egy működő fürthöz az Azure-portálon

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a HDInsight-fürthöz.

2. A HDInsight-fürt áttekintése, válassza ki a **Parancsfájlműveletek** csempére.

    ![Parancsfájl műveletek csempe](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Igény szerint kiválaszthatja **összes beállítás** , és válassza **Parancsfájlműveletek** beállítások szakaszából.

3. Válassza ki a parancsfájl-műveletek szakasz elején **új küldési**.

    ![Parancsfájl hozzáadása egy működő fürthöz](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Használja a __válassza ki a parancsprogramot__ bejegyzés egy előre elkészített parancsfájl kiválasztásához. Egyéni parancsfájl használata, válassza ki a __egyéni__ és adja meg a __neve__ és __Bash parancsfájlok URI__ a parancsfájlt.

    ![A parancsprogramok hozzáadásához válassza ki a parancsfájl formájában](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A következő táblázat ismerteti az elemeket az űrlapon:

    | Tulajdonság | Érték |
    | --- | --- |
    | Válassza ki a parancsprogramot | A saját parancsfájl használatához válassza __egyéni__. Ellenkező esetben válassza ki a megadott parancsfájlt. |
    | Név |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájlok URI |Adja meg az URI-t a parancsfájlt, amelyet a fürt testreszabásához. |
    | HEAD/munkavégző/Zookeeper |Adja meg a csomópontok (**Head**, **munkavégző**, vagy **ZooKeeper**) a a testreszabási parancsfájl futtatásához. |
    | Paraméterek |Adja meg a paraméterek, ha a parancsfájl által igényelt. |

    Használja a __parancsfájlműveletet__ bejegyzést győződjön meg arról, hogy a parancsfájl skálázás műveletek során alkalmazzák.

5. Végül a **létrehozása** gombra a parancsfájl alkalmazása a fürtön.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>A parancsfájlművelet alkalmazása egy működő fürthöz az Azure PowerShell

A folytatás előtt győződjön meg arról, hogy telepítette és konfigurálta az Azure PowerShell. A munkaállomás HDInsight PowerShell-parancsmagok futtatásához konfigurálásával kapcsolatos további információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).

A következő példa bemutatja, hogyan alkalmazza a parancsfájlművelet futó fürtre:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Amint a művelet befejeződik, az alábbihoz hasonló információkat kapni:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Egy működő fürthöz az Azure parancssori felületen a parancsfájlművelet alkalmazása

A folytatás előtt győződjön meg arról, hogy telepítette és konfigurálta az Azure parancssori felület. További információkért lásd: [az Azure parancssori felület telepítése](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Váltson Azure Resource Manager módra, használja a következő parancsot a parancssorból:

        azure config mode arm

2. Használja a következő hitelesítéséhez az Azure-előfizetéshez.

        azure login

3. A következő paranccsal egy parancsfájlművelet alkalmazandó működő fürthöz

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Ha nincs megadva ez a parancs paramétereit, kéri azokat. Ha a parancsfájl adja meg a `-u` fogad el paramétert, is megadhat használatával a `-p` paraméter.

    Érvénytelen csomópont típusok `headnode`, `workernode`, és `zookeeper`. Ha a parancsfájl több csomóponttípusok alkalmazandó, adja meg a típusokat, szóközzel elválasztva a ";". Például: `-n headnode;workernode`.

    A parancsfájl megőrizni, vegye fel a `--persistOnSuccess`. Akkor is is megmaradnak a parancsfájl később használatával `azure hdinsight script-action persisted set`.

    Ha a feladat befejeződik, a kimenet az alábbihoz hasonló jelenhet meg:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>REST API használatával futó fürthöz egy parancsfájl műveletének alkalmazása

Lásd: [Parancsfájlműveletek futtatása a futó fürtön](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>A parancsfájlművelet alkalmazása futó fürt a HDInsight .NET SDK-ból

A .NET SDK használatával parancsfájlok alkalmazása a fürt egy példát, lásd: [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Előzményeinek megtekintése, előléptetése és lefokozása Parancsfájlműveletek

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a HDInsight-fürthöz.

2. A HDInsight-fürt áttekintése, válassza ki a **Parancsfájlműveletek** csempére.

    ![Parancsfájl műveletek csempe](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Igény szerint kiválaszthatja **összes beállítás** , és válassza **Parancsfájlműveletek** beállítások szakaszából.

4. Ehhez a fürthöz parancsfájlok előzményeit jelenik meg a parancsprogram-műveletek szakaszban. Ezen információk közé tartozik a megőrzött parancsfájlok listáját. Az alábbi képernyőképen látható, hogy a parancsfájl lett Solr futtatta-e a fürtön. A képernyőfelvételen nem szerepelnek a megőrzött parancsfájlokat.

    ![Parancsfájl műveletek szakasz](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Ehhez a parancsprogramhoz a Tulajdonságok szakaszának parancsfájl kijelölésével előzményeit jeleníti meg. A képernyő tetején futtassa újra a parancsfájlt, vagy előléptetheti.

    ![Parancsfájl műveletek tulajdonságai](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Használhatja a **...**  jobb oldalán a parancsfájl-műveletek szakasz bejegyzések műveletek elvégzéséhez.

    ![Parancsfájl-műveletek... kihasználtsága](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

| Használja a következő... | A... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |A megőrzött Parancsfájlműveletek információk beolvasása |
| Get-AzureRmHDInsightScriptActionHistory |A fürt, vagy egy adott parancsfájl részletei alkalmazott Parancsfájlműveletek előzményeit beolvasása |
| Set-AzureRmHDInsightPersistedScriptAction |Elősegíti a az ad hoc parancsfájlművelet egy megőrzött parancsfájl művelet |
| Remove-AzureRmHDInsightPersistedScriptAction |Úgy fokozza le a megőrzött parancsfájlművelet alkalmi művelet |

> [!IMPORTANT]
> Használatával `Remove-AzureRmHDInsightPersistedScriptAction` nem vonja vissza a parancsfájl által végrehajtott műveletekről. Ez a parancsmag csak eltávolítja a megőrzött jelzőt.

Az alábbi példa parancsfájl bemutatja, a parancsmagok használatával lépteti elő, akkor a parancsfájl fokozni.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

| Használja a következő... | A... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |A megőrzött Parancsfájlműveletek listájának beolvasása |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Egy adott megőrzött parancsfájlművelet információk beolvasása |
| `azure hdinsight script-action history list <clustername>` |A fürt alkalmazott Parancsfájlműveletek előzményeit beolvasása |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Egy adott parancsfájlművelet információk beolvasása |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Elősegíti a az ad hoc parancsfájlművelet egy megőrzött parancsfájl művelet |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Úgy fokozza le a megőrzött parancsfájlművelet alkalmi művelet |

> [!IMPORTANT]
> Használatával `azure hdinsight script-action persisted delete` nem vonja vissza a parancsfájl által végrehajtott műveletekről. Ez a parancsmag csak eltávolítja a megőrzött jelzőt.

### <a name="using-the-hdinsight-net-sdk"></a>A HDInsight .NET SDK használatával

A .NET SDK használatával parancsfájl előzményeinek lekérése a fürt egy példát, előléptetni vagy parancsfájlok fokozni, lásd: [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Ez a példa is mutatja be a .NET SDK használatával HDInsight-alkalmazások telepítéséhez.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftvereket támogatása

A Microsoft Azure HDInsight szolgáltatás egy nyílt forráskódú technológiák formátumú-e körül Hadoop ökoszisztémájának használja. A Microsoft Azure támogatási általános szintű nyílt forráskódú technológiák nyújt. További információkért lásd: a **támogatási hatókör** szakasza a [Azure támogatás – gyakori kérdések webhely](https://azure.microsoft.com/support/faq/). A HDInsight-szolgáltatás egy további szintű támogatást biztosít a beépített összetevők.

A HDInsight szolgáltatásban elérhető nyílt forráskódú összetevőinek két típusa van:

* **A beépített összetevők** -ezeket az összetevőket a HDInsight-fürtök előre telepített, és adja meg a fürt alapvető funkcióit. Például YARN erőforrás-kezelő, a Hive lekérdezési nyelv (HiveQL) és a Mahout könyvtárban a kategóriába tartoznak. A kiszolgálófürt-összetevők teljes listáját a érhető [What's new in HDInsight által biztosított Hadoop-fürt verziók](hdinsight-component-versioning.md).
* **Egyéni összetevők** -Ön, mint a felhasználó a fürt telepítése vagy használata előtt a munkaterhelésnek valamelyik összetevő a közösségi érhető el, vagy Ön által létrehozott.

> [!WARNING]
> A HDInsight-fürt összetevői teljes mértékben támogatottak. Microsoft Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők kapnak minden üzleti szempontból ésszerű támogatási segítséget nyújtanak a probléma további hibaelhárításához. Lehet, hogy a Microsoft támogatási szolgálatához tudja megoldani a problémát, vagy azok megkérheti, hogy a nyílt forráskódú technológiák, ahol a részletes segítséget, hogy a technológiát található elérhető csatorna kódolása. Például nincsenek sok közösségi webhelyek használható, például: [MSDN fórum hdinsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Is Apache projektek rendelkezik projekt helyek [http://apache.org](http://apache.org), például: [Hadoop](http://hadoop.apache.org/).

A HDInsight-szolgáltatás többféleképpen is egyéni összetevőket használnak. Az azonos szintű támogatást hogyan összetevőt használja, és a fürtön telepítve függetlenül érvényes. Az alábbi lista ismerteti a leggyakoribb módon, hogy a HDInsight-fürtök egyéni összetevők használható:

1. Feladat elküldése - Hadoop és egyéb feladatok végrehajtása, vagy használjon egyéni összetevők küldheti el a fürtöt.

2. Fürt testreszabási - fürt létrehozása során megadhatja további beállításokat és egyéni a fürtcsomópontokon telepített összetevőket.

3. Minták – a népszerű egyéni összetevők, a Microsoft és a mások által biztosított mintákat ezeket az összetevőket hogyan használható a HDInsight-fürtök. Ezeket a mintákat támogatás nélkül van megadva.

## <a name="troubleshooting"></a>Hibaelhárítás

Ambari webes felhasználói felület segítségével Parancsfájlműveletek által naplózott információk megtekintése. Ha a parancsfájl a fürt létrehozása során nem sikerül, a naplókat is elérhetők az alapértelmezett tárfiókot, a fürthöz rendelt. Ebben a szakaszban megtudhatja hogyan lehet lekérni a naplók segítségével mindkét ezeket a beállításokat.

### <a name="using-the-ambari-web-ui"></a>Az Ambari webes felhasználói felület használatával

1. A böngészőben navigáljon https://CLUSTERNAME.azurehdinsight.net. CLUSTERNAME cserélje le a HDInsight-fürt nevét.

    Amikor a rendszer kéri, írja be a rendszergazdai fiók neve (rendszergazda) és a jelszót a fürt. Előfordulhat, hogy a rendszergazdai hitelesítő adatok webes űrlapon újbóli.

2. A sáv a lap tetején, válassza ki a **ops** bejegyzés. A fürt Ambari keresztül végrehajtott jelenlegi és korábbi műveletek listája jelenik meg.

    ![Ambari webes felhasználói felület sávon kicserélhet kijelölt ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. A bejegyzéseket található **futtatása\_customscriptaction** a a **műveletek** oszlop. Ezek a bejegyzések a Parancsfájlműveletek futásakor jönnek létre.

    ![Képernyőkép a műveletek](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Az STDOUT és az STDERR kimeneti megtekintéséhez válassza ki a run\customscriptaction bejegyzést, és a hivatkozások részletezésének. A kimenet jön létre, amikor a parancsprogram lefut, és hasznos információt tartalmazhat.

### <a name="access-logs-from-the-default-storage-account"></a>Az alapértelmezett tárfiók a belépési naplók

Ha a fürt létrehozása a parancsfájl hibája miatt nem sikerül, a naplók a fürt tárfiókot kell tartani.

* A tárolási naplófájljai érhetők el `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Képernyőkép a műveletek](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Az ebben a könyvtárban a naplók vannak rendszerezve külön-külön headnode, workernode és zookeeper csomópontok. Néhány példa:

    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Munkavégző csomópont** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper csomópont** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Stdout és az stderr a megfelelő fogadó feltöltése a tárfiókba. Van egy **kimeneti -\*.txt** és **hibák -\*.txt** parancsfájl műveleteket. A kimeneti-*.txt fájlt a gazdagépen futó kapott a parancsfájl URI információt tartalmaz. Példa:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Akkor lehet, hogy ismételten hozzon létre egy parancsfájl művelet fürt ugyanazzal a névvel. Ilyen esetben különbözteti meg a megfelelő naplók dátum mappa neve alapján. Például egy fürt (sajátfürt) különböző időpontokban létrehozott mappaszerkezetet hasonlóan jelenik meg a következő naplófájl-bejegyzéseket:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Ha a parancsfájl művelet fürt létrehozása ezzel a névvel ugyanarra a napra esnek, az egyedi előtag segítségével azonosítsa a megfelelő naplófájlok helyét.

* 12:00 -kor (éjfél) mellett a fürt létrehozása, akkor lehetséges, hogy a naplófájlok span két napon keresztül. Ebben az esetben tekintse meg az ugyanabban a fürtben két külön dátummal mappáiban.

* Fájlok feltöltése az alapértelmezett tároló legfeljebb 5 perc, különösen olyan nagyméretű fürtök vehet igénybe. Igen ha el szeretne érni a naplókat, nem azonnal törölnie kell a fürt Ha egy parancsfájl művelet sikertelen.

### <a name="ambari-watchdog"></a>Ambari figyelő

> [!WARNING]
> Ne módosítsa a jelszót az Ambari figyelő (hdinsightwatchdog) a Linux-alapú HDInsight-fürt számára. A fiókhoz tartozó jelszó módosítása megsérti a HDInsight-fürt új Parancsfájlműveletek futási képességét.

### <a name="cant-import-name-blobservice"></a>BlobService neve nem lehet importálni.

__A jelenség__: A parancsfájl művelet meghiúsul. Az alábbihoz hasonló szöveg jelenik meg a műveletet az Ambari megtekintésekor:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__OK__: Ez a hiba akkor fordul elő, ha a Python Azure Storage ügyfelet frissít, amely tartalmazza a HDInsight-fürthöz. A HDInsight az Azure Storage ügyfél 0.20.0 vár.

__Megoldási__: Ez a hiba elhárításához manuálisan kapcsolódás minden fürt csomópont `ssh` és telepítse újra a megfelelő tárolási ügyfél verzióját a következő paranccsal:

```
sudo pip install azure-storage==0.20.0
```

A fürthöz az SSH a további információkért lásd: [az SSH a Hdinsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Előzmények nem jelenik meg a fürt létrehozása során használt parancsfájlok

Ha a fürt létrehozása előtt 2016. március 15-én nem jelenhet meg a parancsfájlművelet előzményeinek bejegyzése. A fürt átméretezése azt eredményezi, a parancsfájlok jelennek meg a parancsfájlművelet előzményeinek.

Ez alól két kivétel van:

* Ha a fürt 2015. szeptember 1. előtt létrehozott. Ez a dátum esetén a Parancsfájlműveletek lett bevezetve. Ez a dátum előtt létrehozott fürthöz sikerült nem használta a Parancsfájlműveletek a fürt létrehozásához.

* Ha több Parancsfájlműveletek fürt létrehozása során használt, és több parancsfájl több parancsfájl ugyanazt a nevet vagy a ugyanazzal a névvel, ugyanilyen URI, de más paraméterekkel használja. Ebben az esetben a következő hibaüzenetet kapja:

    Nem lehet műveleteket új parancsfájl lefutott, meglévő parancsfájlokban egymással ütköző parancsfájl név miatt ezen a fürtön. Fürt elérhető parancsfájl nevét kell létrehozni minden egyedi lehet. Meglévő parancsfájlok megbízhatóságához ablakhoz.

## <a name="next-steps"></a>Következő lépések

* [Parancsfájl művelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Telepítheti és használhatja a HDInsight-fürtök Solr](hdinsight-hadoop-solr-install-linux.md)
* [Telepítheti és használhatja a HDInsight-fürtök Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [További tárhely hozzáadása egy HDInsight-fürt](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fürt létrehozása során szakaszból"
