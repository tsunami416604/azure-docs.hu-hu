---
title: Parancsfájlműveletekkel – Azure HDInsight-fürtök testre szabása
description: Egyéni összetevők hozzáadása a Linux-alapú HDInsight-fürtök parancsfájlműveletekkel. A szkriptműveletek olyan Bash-szkriptek, amelyek segítségével a fürtkonfiguráció egyéni beállítását, vagy adjon hozzá további szolgáltatásokat és segédprogramok, mint például a Hue, Solr, vagy az R.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e655624a30332630c28cbd555dac26098adeb68b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976919"
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>Szkriptműveletek használatával Linux-alapú HDInsight-fürtök testre szabása

Nevű konfigurációs módszert biztosít a HDInsight **szkriptműveletek** , amely meghívja az egyéni szkriptek a fürt testreszabása. Ezek a parancsfájlok segítségével további összetevők és konfigurációs beállításokat módosítaná. A szkriptműveletek használható során vagy a fürt létrehozása után.

> [!IMPORTANT]  
> Szkriptműveletek használhat egy már futó fürt csak Linux-alapú HDInsight-fürtök esetén érhető el.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

A szkriptműveletek HDInsight alkalmazásként is az Azure piactéren tehetők közzé. További információ a HDInsight-alkalmazások: [közzé HDInsight-alkalmazások az Azure piactéren](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Engedélyek

A tartományhoz csatlakoztatott HDInsight-fürtöt használ, van-e két Ambari szükséges engedélyekről szkriptműveletek használata a fürt esetén:

* **AZ AMBARI. FUTTATÁS\_EGYÉNI\_PARANCS**: Az Ambari rendszergazdai szerepkör alapértelmezés szerint rendelkezik ezzel az engedéllyel.
* **A FÜRT. FUTTATÁS\_EGYÉNI\_PARANCS**: A HDInsight fürt rendszergazdája és az Ambari rendszergazda alapértelmezés szerint rendelkezik ezzel az engedéllyel.

Tartományhoz csatlakoztatott HDInsight-engedélyek való használatáról további információkért lásd: [tartományhoz csatlakoztatott HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Hozzáférés-vezérlés

Ha nem a az Azure-előfizetés rendszergazdája vagy tulajdonosa, a fióknak rendelkeznie kell legalább **közreműködői** hozzáférés az erőforráscsoporthoz, amelyet a HDInsight-fürt található.

Ezenkívül ha hoz létre egy HDInsight-fürtöt, rendelkező személy legalább **közreműködői** hozzáférést az Azure-előfizetés korábban regisztrálnia kellett az HDInsight-szolgáltatót. A szolgáltató regisztrálása akkor történik meg, ha egy olyan felhasználó, aki Közreműködői hozzáféréssel rendelkezik az előfizetéshez, először hoz létre erőforrást az előfizetésben. Az erőforrás létrehozása nélkül is elvégezhető, ha [ a REST segítségével regisztrál egy szolgáltatót](https://msdn.microsoft.com/library/azure/dn790548.aspx).

A hozzáférés-kezelés használatával kapcsolatos további információk a következő dokumentumokban találhatók:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md)

## <a name="understanding-script-actions"></a>A szkriptműveletek ismertetése

Szkriptműveletet egy HDInsight-fürt csomópontjain futó Bash-szkript. Az alábbiakban a jellemzők és szkriptműveletek funkciói.

* A HDInsight-fürt által elérhető, egy URI-t kell tárolni. A lehetséges tárolási helyek a következők:

    * Egy **Azure Data Lake Storage** fiókot, amely a HDInsight-fürt által elérhető. Az Azure Data Lake Storage használata a HDInsight további információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

        A Data Lake Storage-ban tárolt parancsfájl használatakor az URI formátuma `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]  
        > HDInsight Data Lake Storage elérésére használja a szolgáltatásnév a parancsfájl olvasási hozzáféréssel kell rendelkeznie.

    * A blob- **Azure Storage-fiók** , amely vagy az elsődleges vagy a további storage-fiókot a HDInsight-fürt számára. HDInsight hozzáférést kap mindkét említett típusú tárfiókok fürt létrehozása során.

    * Egy nyilvános fájlmegosztási szolgáltatással, például az Azure Blob, GitHub, OneDrive, Dropbox stb.

        Például az URI-k, tekintse meg a [parancsfájl művelet parancsfájlpéldákat](#example-script-action-scripts) szakaszban.

        > [!WARNING]  
        > HDInsight standard teljesítményszint az Azure Storage-fiókokat Blob csak támogatja. 

* Korlátozható **futtatása az egyes csomóponttípusok**, például átjárócsomópontokhoz vagy a munkavégző csomópontok.

* Lehet **megőrzött** vagy **az ad hoc**.

    **Megőrzött** parancsfájlok segítségével testre szabhatja a fürthöz méretezés műveletek során hozzáadott új munkavégző csomópontok. Egy megőrzött parancsfájl is vonatkozhatnak módosításokat egy másik csomópont típusa, például a fő csomópontot, a méretezési műveletek esetén.

  > [!IMPORTANT]  
  > Megőrzött Parancsfájlműveletek egyedi névvel kell rendelkeznie.

    **Az ad hoc** parancsfájlok nem rögzíti. Nem adják hozzá a fürthöz, miután a parancsfájl lefutott rendelkezik munkavégző csomópontokhoz. Ezt követően egy ad-hoc parancsfájl egy megőrzött parancsfájl előléptetése, vagy egy ad-hoc parancsfájl egy megőrzött parancsfájl lefokozása.

  > [!IMPORTANT]  
  > A fürt létrehozásakor használt szkriptműveletek automatikusan megmaradnak.
  >
  > Parancsfájlok, amelyek sikertelen nincsenek állandó, még akkor is, ha Ön kifejezetten azt jelzik, hogy azok kell lennie.

* Elfogadhatja **paraméterek** , amelyek használják a parancsfájl végrehajtása közben.

* Futtassa a **gyökér szintű jogosultságok** a fürtcsomópontokon.

* Keresztül is használható a **az Azure portal**, **Azure PowerShell-lel**, **Azure klasszikus parancssori felület**, vagy **HDInsight .NET SDK-val**

A fürt összes lett futtatott parancsfájlok előzményeket. Az előzmények akkor hasznos, ha meg kell keresnie egy parancsfájl azonosítója előléptetés vagy a lefokozási műveletekhez.

> [!IMPORTANT]  
> Nincs automatikus mód szkriptműveletet által végzett módosítások visszavonása. Vagy manuálisan a változtatásokat, vagy adjon meg egy parancsfájlt, amely megfordítja őket.

### <a name="script-action-in-the-cluster-creation-process"></a>A fürtlétrehozás a parancsfájlművelet

A fürt létrehozásakor használt szkriptműveletek némileg eltérnek parancsprogram egy meglévő fürtön futottak műveletek:

* A parancsfájl **automatikusan megőrzött**.

* A **hiba** a parancsfájl okozhat a Fürtlétrehozási folyamat sikertelen lesz.

A következő ábra szemlélteti a parancsprogram-művelet végrehajtásakor a létrehozási folyamat során:

![HDInsight-fürt testreszabása és -szakaszok a fürt létrehozásakor][img-hdi-cluster-states]

A szkript futtatása, amíg megtörténik a HDInsight konfigurálása folyamatban van. A parancsfájl a megadott csomópontok a fürtben párhuzamosan fut, és a csomópontok gyökérszintű jogosultságokkal futtatja.

> [!NOTE]  
> Például a szolgáltatások, többek között az Apache Hadoop-hez kapcsolódó szolgáltatások indítása és leállítása műveleteket hajthat végre. Ha leállítja a szolgáltatások, biztosítania kell, hogy az Ambari szolgáltatás és más Hadoop-hez kapcsolódó szolgáltatások, mielőtt a parancsfájl futtatása befejeződik. Ezek a szolgáltatások állapotának és a fürt állapotának sikeresen meghatározása, létrehozása közben van szükség.


Fürt létrehozása során egyszerre több parancsprogram-művelet is használja. Ezeket a parancsfájlokat a megadott is sorrendben kerül meghívásra.

> [!IMPORTANT]  
> A szkriptműveletek 60 perc, vagy időtúllépés belül kell végrehajtania. Fürt üzembe helyezésekor, a parancsfájl futtatása egy időben alkalmazza a többi beállítási és konfigurációs folyamat. Verseny a erőforrások – például CPU-idő vagy a hálózati sávszélesség előfordulhat, hogy a parancsfájl, a fejlesztési környezet mint befejezése hosszabb időt vesz igénybe.
>
> A parancsfájl futtatásához szükséges idő minimalizálása érdekében ne feladatok, például a szoftverletöltés és fordítás forrásból származó alkalmazások. Előre fordítsa le az alkalmazásokat, és tárolja a bináris fájlt az Azure Storage-ban.


### <a name="script-action-on-a-running-cluster"></a>Futó fürtökön parancsfájlműveletekkel

Hiba történt a parancsfájl lefutott egy már fut a fürt automatikusan nem okoz a fürt módosítása sikertelen állapotba. Miután a parancsfájl futása befejeződött, a fürt "fut" állapotba kell visszaadnia.

> [!IMPORTANT]  
> Akkor is, ha a fürt egy "fut" állapotban van, előfordulhat, hogy a hibás parancsfájl megszakítják dolog. Például egy szkriptet sikerült törölni a fürt számára szükséges fájlokat.
>
> Parancsfájlok műveletek gyökérszintű jogosultságokkal futtassa. Győződjön meg arról, hogy megértette a parancsfájl funkciója a fürthöz való alkalmazása előtt.

A parancsfájl egy fürthöz alkalmazásakor a fürt állapota **futtató** való **elfogadva**, majd **HDInsight konfigurációs**, és végül térjen  **Futó** sikeres parancsprogramok. A szkript állapotát a parancsfájlművelet előzményeinek be van jelentkezve, és ezek az információk segítségével határozza meg, hogy a parancsfájl sikeres vagy sikertelen volt. Ha például a `Get-AzureRmHDInsightScriptActionHistory` PowerShell-parancsmag segítségével egy parancsfájl állapotának megtekintése. Ez hasonló információt ad vissza a következő szöveget:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Ha a fürt létrehozása után módosította a fürt (rendszergazdai) felhasználó jelszava, műveletek lefutott-e a fürt parancsfájl sikertelen lehet. Ha bármely megőrzött Parancsfájlműveletek adott cél feldolgozó csomóponttal rendelkezik, ezek a szkriptek sikertelen lehet, amikor a fürt.

## <a name="example-script-action-scripts"></a>Példa parancsfájl művelet parancsfájlok

Parancsprogram-művelet parancsfájlok keresztül az alábbi segédprogramokat használhatja:

* Azure Portal
* Azure PowerShell
* Az Azure klasszikus parancssori felület
* HDInsight .NET SDK

HDInsight parancsprogramokkal történő telepítését a következő összetevőket a HDInsight-fürtökön biztosítja:

| Name (Név) | Szkript |
| --- | --- |
| **Azure Storage-fiók hozzáadása** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Lásd: [további tárhely hozzáadása egy HDInsight-fürt](hdinsight-hadoop-add-storage.md). |
| **A Hue telepítése** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Lásd: [telepítése és használata a Hue a HDInsight-fürtök](hdinsight-hadoop-hue-linux.md). |
| **A Presto telepítése** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Lásd: [telepítése és használata Presto a HDInsight-fürtök](hdinsight-hadoop-install-presto.md). |
| **A Solr telepítése** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Lásd: [telepítése és használata a Solr a HDInsight-fürtök](hdinsight-hadoop-solr-install-linux.md). |
| **A Giraph telepítése** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Lásd: [telepítése és használata a Giraph a HDInsight-fürtök](hdinsight-hadoop-giraph-install-linux.md). |
| **Hive-kódtárak előzetes betöltése** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Lásd: [adja hozzá a Hive-kódtárak a HDInsight-fürtök](hdinsight-hadoop-add-hive-libraries.md). |
| **Mono telepítése vagy frissítése** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Lásd: [telepítése vagy frissítése a HDInsight Mono](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Fürt létrehozása során egy parancsfájlművelettel

Ez a szakasz példákat használhatja a Parancsfájlműveletek egy HDInsight-fürt létrehozásakor különböző módjaival.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Használjon szkriptműveletet az Azure Portalról a fürt létrehozásakor

1. Indítsa el a fürt létrehozása ismertetett módon [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Fürt létrehozása során, érkezik egy __fürt összegzése__ lapot. Az a __fürt összegzése__ lapon válassza ki a __szerkesztése__ hozzárendeli a __speciális beállítások__.

    ![Speciális beállítások hivatkozása](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Az a __speciális beállítások__ szakaszban jelölje be __Szkriptműveletek__. Az a __Szkriptműveletek__ szakaszban jelölje be __+ új küldés__

    ![Egy új szkriptművelet elküldéséhez](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Használja a __válassza ki a parancsprogramot__ bejegyzésben válassza ki az előre elkészített parancsfájlt. Egyéni parancsfájl használata, válassza ki a __egyéni__ , és adja meg a __neve__ és __Bash parancsfájl URI azonosítója__ a parancsfájlt.

    ![Parancsprogram hozzáadásához válassza ki a parancsfájl formájában](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A következő táblázat ismerteti az elemeket az űrlapon:

    | Tulajdonság | Érték |
    | --- | --- |
    | Szkript kiválasztása | A saját parancsfájl használatára, válassza ki a __egyéni__. Ellenkező esetben válassza a megadott szkriptek közül. |
    | Name (Név) |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájl URI azonosítója |Adja meg a szkript URI azonosítója. |
    | A fő/feldolgozó vagy Zookeeper |Adja meg a csomópontok (**fő**, **feldolgozó**, vagy **ZooKeeper**) az a szkript futása. |
    | Paraméterek |Adja meg a paramétereket, ha a parancsfájl által igényelt. |

    Használja a __parancsfájlműveletet__ bejegyzés annak érdekében, hogy érvényesek-e a parancsfájl méretezési műveletek során.

5. Válassza ki __létrehozás__ a parancsfájl mentéséhez. Ezután __+ új küldés__ , adja meg egy másik szkriptet.

    ![Több parancsprogram-művelet](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Amikor elkészült a parancsfájlok hozzáadását, a __válassza__ gomb, majd a __tovább__ gombra kattintva térjen vissza a __fürt összegzése__ szakaszban.

3. A fürt létrehozásához válassza __létrehozás__ származó a __fürt összegzése__ kiválasztása.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Használjon szkriptműveletet az Azure Resource Manager-sablonok

A szkriptműveletek használható az Azure Resource Manager-sablonok. Egy vonatkozó példáért lásd: [ https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/ ](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Ebben a példában a parancsprogram-művelet hozzáadva a következő kód használatával:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

A sablon üzembe helyezése további információkért lásd: a következő dokumentumokat:

* [Erőforrások üzembe helyezése sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Erőforrások üzembe helyezése sablonokkal és az Azure CLI-vel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Használjon szkriptműveletet az Azure PowerShell a fürt létrehozásakor

Ebben a szakaszban használhatja a [Add-AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction) parancsmag segítségével szabhatja testre a fürthöz-szkriptek meghívása. A folytatás előtt győződjön meg arról, hogy telepítette és konfigurálta az Azure Powershellt. Egy munkaállomás HDInsight PowerShell-parancsmagok futtatásához konfigurálásával kapcsolatos további információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).

Az alábbi parancsfájl bemutatja, hogyan szkriptműveletet alkalmazni, amikor a fürthöz PowerShell használatával hoz létre:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Mielőtt a fürt létrehozása több percet is igénybe vehet.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>A HDInsight .NET SDK-t a fürt létrehozása során egy parancsfájlművelettel

A HDInsight .NET SDK-t, amely megkönnyíti a HDInsight használata a .NET-alkalmazásból klienskódtárakat biztosít. A kód minta: [a HDInsight .NET SDK használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Egy futó fürt szkriptműveletet vonatkozik

Ebben a szakaszban megtudhatja, hogyan parancsfájl műveleteket alkalmazhat a futó fürt.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Egy futó fürt szkriptműveletet alkalmazni az Azure Portalról

Az a [az Azure portal](https://portal.azure.com):

1. A bal oldali menüben válassza ki a **minden szolgáltatás**.

1. A **ANALYTICS**válassza **HDInsight-fürtök**.

1. Ekkor megnyílik az alapértelmezett nézet listából válassza ki a fürtöt.

1. Az alapértelmezett nézet a csoportban **beállítások**válassza **Szkriptműveletek**.

1. A felső részén a **Szkriptműveletek** lapon jelölje be **+ új küldés**.

    ![Parancsfájl hozzáadása egy futó fürt](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Használja a __válassza ki a parancsprogramot__ bejegyzésben válassza ki az előre elkészített parancsfájlt. Egyéni parancsfájl használata, válassza ki a __egyéni__ , és adja meg a __neve__ és __Bash parancsfájl URI azonosítója__ a parancsfájlt.

    ![Parancsprogram hozzáadásához válassza ki a parancsfájl formájában](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A következő táblázat ismerteti az elemeket az űrlapon:

    | Tulajdonság | Érték |
    | --- | --- |
    | Szkript kiválasztása | A saját parancsfájl használatára, válassza ki a __egyéni__. Ellenkező esetben válassza ki a megadott parancsfájlt. |
    | Name (Név) |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájl URI azonosítója |Adja meg a szkript URI azonosítója. |
    | A fő/feldolgozó vagy Zookeeper |Adja meg a csomópontok (**fő**, **feldolgozó**, vagy **ZooKeeper**) az a szkript futása. |
    | Paraméterek |Adja meg a paramétereket, ha a parancsfájl által igényelt. |

    Használja a __parancsfájlműveletet__ bejegyzést, hogy a parancsfájl a méretezési műveletek során mikor lesz alkalmazva.

5. Végül a **létrehozás** gombra kattintva a alkalmazni a parancsfájl a fürthöz.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Egy futó fürt szkriptműveletet alkalmazni az Azure PowerShell-lel

A folytatás előtt győződjön meg arról, hogy telepítette és konfigurálta az Azure Powershellt. Egy munkaállomás HDInsight PowerShell-parancsmagok futtatásához konfigurálásával kapcsolatos további információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).

A következő példa bemutatja, hogyan alkalmazhatja a szkriptműveletet futó fürthöz:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

A művelet befejezése után az alábbi szöveghez hasonló információt kap:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Egy futó fürt szkriptműveletet alkalmazni az Azure parancssori felületen

A folytatás előtt győződjön meg arról, hogy telepítette és konfigurálta az Azure parancssori felület. További információkért lásd: [a klasszikus Azure CLI telepítése](../cli-install-nodejs.md).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Váltson Azure Resource Manager módra, használja a következő parancsot a parancssorban:

    ```bash
    azure config mode arm
    ```

2. Használja a következő hitelesítéséhez az Azure-előfizetéshez.

    ```bash
    azure login
    ```

3. A következő paranccsal szkriptműveletet alkalmazandó futó fürt

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Ha kihagyja ezt a parancsot a paramétereket, a rendszer kéri a számukra. Ha a parancsfájl adja meg a `-u` fogad el paramétereket, megadhatja azokat használja a `-p` paraméter.

    Érvényes csomópont-típusok a következők `headnode`, `workernode`, és `zookeeper`. Ha a parancsfájl több csomóponttípusok kell alkalmazni, adja meg a elválasztva típusok egy (;). Például: `-n headnode;workernode`.

    A parancsfájl is tartalmaz, adja hozzá a `--persistOnSuccess`. Emellett megőrizheti a parancsfájl később használatával `azure hdinsight script-action persisted set`.

    A feladat befejezése után az alábbi szöveghez hasonló kimenet jelenhet meg:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>REST API-val futó fürt szkriptműveletet vonatkozik

Lásd: [szkriptműveletek futtatása a futó fürtön](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Egy futó fürt a HDInsight .NET SDK-ból szkriptműveletet vonatkozik

A .NET SDK használatával történő parancsfájlok alkalmazni a fürt egy példa: [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Előzmények megtekintése, előléptetése és lefokozása Parancsfájlműveletek

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **minden szolgáltatás**.

1. A **ANALYTICS**válassza **HDInsight-fürtök**.

1. Ekkor megnyílik az alapértelmezett nézet listából válassza ki a fürtöt.

1. Az alapértelmezett nézet a csoportban **beállítások**válassza **Szkriptműveletek**.

4. Ehhez a fürthöz-szkriptek előzményei a parancsfájl műveletek szakaszban jelenik meg. Ezen információk közé tartozik a megőrzött szkriptek listáját. Az alábbi képernyőképen látható, hogy a parancsfájl lett a Solr a fürtön futottak. A képernyőfelvételen nem jeleníti meg minden olyan megőrzött szkriptek.

    ![Parancsfájl műveletek szakaszban](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. A szkript a Tulajdonságok szakaszának jelölje ki a parancsfájl előzményeit jeleníti meg. A képernyő tetején futtassa újra a parancsfájlt, vagy léptetheti elő.

    ![Parancsfájl műveletek tulajdonságai](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Is használhatja a **...**  jobb oldalán a parancsfájl műveletek szakaszban lévő bejegyzéseket műveletek végrehajtásához.

    ![Szkriptműveletek... használat](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

| Használja a következő... | A... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |A megőrzött Parancsfájlműveletek információk lekéréséhez |
| Get-AzureRmHDInsightScriptActionHistory |A fürt vagy egy adott parancsfájl részleteinek alkalmazott szkriptműveletek előzményeit beolvasása |
| Set-AzureRmHDInsightPersistedScriptAction |Elősegíti a megőrzött parancsfájlművelet-ad-hoc parancsfájlművelet |
| Remove-AzureRmHDInsightPersistedScriptAction |Ad-hoc művelet egy megőrzött parancsfájlművelet visszaléptetése |

> [!IMPORTANT]  
> Használatával `Remove-AzureRmHDInsightPersistedScriptAction` nem vonja vissza a parancsfájl által végrehajtott műveletek. Ez a parancsmag csak a megőrzött jelző távolítja el.

Az alábbi példa parancsfájl bemutatja a parancsmagokkal történő előléptetéséhez, majd fokozza le a parancsfájlt.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-classic-cli"></a>A klasszikus Azure CLI használatával

| Használja a következő... | A... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Megőrzött Parancsfájlműveletek listájának lekéréséhez |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Az információgyűjtéshez az egy adott megőrzött parancsfájlművelet |
| `azure hdinsight script-action history list <clustername>` |A fürt a alkalmazni szkriptműveletek előzményeit beolvasása |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Egy adott szkriptművelet információk lekérése |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Elősegíti a megőrzött parancsfájlművelet-ad-hoc parancsfájlművelet |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Ad-hoc művelet egy megőrzött parancsfájlművelet visszaléptetése |

> [!IMPORTANT]  
> Használatával `azure hdinsight script-action persisted delete` nem vonja vissza a parancsfájl által végrehajtott műveletek. Ez a parancsmag csak a megőrzött jelző távolítja el.

### <a name="using-the-hdinsight-net-sdk"></a>A HDInsight .NET SDK használatával

A .NET SDK használatával parancsfájl előzményeinek lekérése a fürt egy példát, előléptetése vagy parancsfájlok lefokozása, lásd: [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Ebben a példában azt is bemutatja, hogyan telepíthet HDInsight-alkalmazásokat a .NET SDK használatával.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatást használ az ökoszisztéma formátumú körül Apache Hadoop nyílt forráskódú technológiák. A Microsoft Azure nyílt forráskódú technológiák egy általános szintű támogatást biztosít. További információkért lásd: a **támogatás hatóköre** szakaszában a [Azure támogatás – gyakori kérdések webhely](https://azure.microsoft.com/support/faq/). A HDInsight szolgáltatás egy további szintű támogatást biztosít a beépített összetevők.

A HDInsight szolgáltatásban elérhető nyílt forráskódú összetevőket két típusa van:

* **Beépített összetevők** – ezek az összetevők a HDInsight-fürtök előre telepítve vannak, és adja meg a fürt fő funkciói. Például [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager, a Hive lekérdezési nyelvet ([HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)), és a [Apache Mahout](https://mahout.apache.org/) könyvtár ebbe a kategóriába tartozik. Kiszolgálófürt-összetevők teljes listája megtalálható [a HDInsight által biztosított Hadoop-fürtverziók újdonságai](hdinsight-component-versioning.md).
* **Egyéni összetevők** -, a fürt felhasználói telepítése vagy használata az alkalmazások és szolgáltatások valamelyik összetevő a Közösségben elérhető vagy Ön által létrehozott.

> [!WARNING]  
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak. Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők annak érdekében, hogy a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Lehet, hogy a probléma megoldásához a Microsoft ügyfélszolgálatához, vagy előfordulhat, hogy megadását is végezhetnek elérhető csatornák a nyílt forráskódú technológiák, ahol található részletes szakértelmét, hogy a technológiát. Például sok, használható, például közösségi helyek vannak: [A HDInsight az MSDN-fórumokra](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Is Apache projektek rendelkeznek projekt helyek [ https://apache.org ](https://apache.org), például: [Hadoop](https://hadoop.apache.org/).

A HDInsight szolgáltatás használatához az egyéni összetevők több módot nyújt. Az azonos szintű támogatást vonatkozik, függetlenül attól, milyen egy összetevő használt vagy a fürtön telepíteni. Az alábbi lista ismerteti a leggyakoribb módja, hogy egyéni összetevők a HDInsight-fürtökön is használható:

1. Feladat küldése – a Hadoop és egyéb feladatok végrehajtása, vagy használjon egyéni összetevők küldheti el a fürtöt.

2. Fürt testreszabása – fürt létrehozása során, megadhat további beállításokat és egyéni, a fürtcsomópontokon telepített összetevőket.

3. Minták – a népszerű egyéni összetevők, a Microsoft és mások rendelkezhetnek mintákat ezeket az összetevőket hogyan használható a HDInsight-fürtökön. Ezek a minták támogatás nélkül állnak rendelkezésre.

## <a name="troubleshooting"></a>Hibaelhárítás

Az Ambari webes felhasználói felület segítségével szkriptműveletek által naplózott adatok megtekintéséhez. Ha a parancsfájl futása sikertelen, a fürt létrehozásakor, a naplók is elérhetők a fürthöz társított alapértelmezett tárfiókban. Ez a szakasz információt nyújt az mindkét ezek a beállítások használatával a naplók begyűjtéséről.

### <a name="using-the-apache-ambari-web-ui"></a>Az Apache Ambari webes Felülettel

1. A böngészőjében lépjen a https://CLUSTERNAME.azurehdinsight.net helyre. CLUSTERNAME cserélje le a HDInsight-fürt nevére.

    Amikor a rendszer kéri, adja meg a rendszergazdai fióknevet (rendszergazdai) és a jelszót a fürthöz. Előfordulhat, hogy a rendszergazdai hitelesítő adatok webes űrlapon való újbóli.

2. Az oldal tetején lévő sávon válassza a **ops** bejegyzés. A fürt Ambari végrehajtott jelenlegi és korábbi műveletek listája jelenik meg.

    ![Az Ambari webes felhasználói felület sávon kicserélhet ops kiválasztva](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Keresse meg a bejegyzéseket, amelyek **futtatása\_customscriptaction** a a **műveletek** oszlop. Ezek a bejegyzések a Parancsfájlműveletek futásakor jönnek létre.

    ![Képernyőkép a műveletek](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Az STDOUT és STDERR kimenetet megtekintéséhez válassza ki a run\customscriptaction bejegyzést, és részletes elemzést a hivatkozásokon keresztül. Ez a kimenet jön létre, amikor futtatja a szkriptet, és olyan hasznos információkat is tartalmazhat.

### <a name="access-logs-from-the-default-storage-account"></a>Az alapértelmezett tárfiókot a naplók elérése

Ha fürt létrehozása egy parancsfájl hiba miatt nem sikerül, a naplók a fürt tárfiókban őrzi meg.

* A storage-naplók esetén érhető el `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Képernyőkép a műveletek](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Ebben a könyvtárban a naplók vannak rendszerezve külön átjárócsomópontjával workernode és zookeeper-csomópontok számára. Néhány példa:

    * **Átjárócsomópont** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Munkavégző csomópont** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper-csomópont** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* A storage-fiók összes stdout és stderr a megfelelő gazdagép van feltöltve. Van egy **kimeneti -\*.txt** és **hibák –\*.txt** minden parancsfájlművelethez. A kimeneti-*.txt fájl itt van a gazdagépen futó szkript URI kapcsolatos információkat tartalmazza. A következő szöveg egy példa ezt az információt:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Akkor lehet, hogy ismételten parancsfájl művelet fürt létrehozása ezzel a névvel. Ebben az esetben hogy megkülönböztethesse a dátum mappa neve alapján a megfelelő naplók. Például a gyökérmappa-szerkezetében különböző időpontokban létrehozott fürt (sajátfürt) hasonlóan jelenik meg a következő naplófájl-bejegyzéseket:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Ha egy parancsfájl művelet fürtöt hoz létre ugyanazzal a névvel ugyanarra a napra, az egyedi előtag segítségével azonosíthatja a megfelelő naplófájlok helyét.

* Ha közel 12:00 -kor (éjfél) egy fürt létrehozása, lehetséges, hogy a naplófájlok span két napon keresztül. Ezekben az esetekben a látható ugyanazon a fürtön a két különböző mappája.

* Naplófájlok feltöltése az alapértelmezett tároló legfeljebb 5 perc, különösen nagyméretű fürtök esetén is eltarthat. Ezért ha azt szeretné, a naplók eléréséhez, ne azonnal törölje a fürt szkriptműveletet meghibásodásakor.

### <a name="ambari-watchdog"></a>Ambari-figyelő

> [!WARNING]  
> Nem található a Linux-alapú HDInsight-fürt Ambari figyelő (hdinsightwatchdog) tartozó jelszó módosítása. Ez a fiók jelszavának módosítása működésképtelenné válik új Parancsfájlműveletek futtatására a HDInsight-fürtön.

### <a name="cant-import-name-blobservice"></a>BlobService neve nem lehet importálni.

__A jelenség__: A parancsprogram-művelet sikertelen lesz. A szöveg a következő hibához hasonló Ambari megtekintheti a művelet jelenik meg:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__OK__: Ez a hiba akkor fordul elő, ha a Python Azure Storage ügyfelet frissít, amely a HDInsight-fürt részét képezi. HDInsight az Azure Storage-kliens 0.20.0 vár.

__Feloldási__: Ez a hiba elhárításához manuálisan kapcsolódni minden egyes fürt csomópont használatával `ssh` és a következő paranccsal telepítse újra a helyes tárfiók-ügyfél verziója:

```bash
sudo pip install azure-storage==0.20.0
```

Csatlakozás a fürthöz az ssh-val kapcsolatos információkért lásd: [az SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Előzmények nem jelenik meg a fürt létrehozásakor használt parancsfájlok

A fürt létrejött, 2016. március 15. előtt nem jelenhet meg a parancsfájlművelet előzményeinek bejegyzés. A fürt átméretezése hatására a szkripteket a parancsfájlművelet előzményeinek jelennek meg.

Ez alól két kivétel van:

* Ha a fürt 2015. szeptember 1. előtt hozták létre. Dátum, amikor szkriptműveletek jelentek meg. Tetszőleges fürt, ez a dátum előtt létrehozott sikerült nem használt szkriptműveletek a fürtlétrehozáshoz.

* Ha több parancsprogram-művelet használja a fürt létrehozása során, és több parancsfájl ugyanazt a nevet, vagy a ugyanazzal a névvel, ugyanilyen URI, de különböző paraméterek használt több parancsfájlt. Ezekben az esetekben a következő hibaüzenetet kapja:

    Nem lehet műveleteket új parancsfájl ütköző parancsfájl nevét a meglévő szkriptekre miatt a fürtön futottak. Fürt megadott parancsfájl nevét kell létrehozni minden egyedi legyen. Az átméretezés lefutnak a meglévő szkriptekre.

## <a name="next-steps"></a>További lépések

* [Parancsfájl művelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Telepítse és Apache Solr használata a HDInsight-fürtökön](hdinsight-hadoop-solr-install-linux.md)
* [Telepítse, és az Apache Giraph használata a HDInsight-fürtökön](hdinsight-hadoop-giraph-install-linux.md)
* [Adjon hozzá további tárterületet HDInsight-fürthöz](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fürt létrehozása során szakaszai"
