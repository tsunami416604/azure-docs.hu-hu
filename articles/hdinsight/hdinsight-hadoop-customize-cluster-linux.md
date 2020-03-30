---
title: Az Azure HDInsight-fürtök testreszabása parancsfájlműveletek használatával
description: Parancsfájlműveletek használatával egyéni összetevőket adhat hozzá a HDInsight-fürtökhöz. A parancsfájlműveletek Bash-parancsfájlok, amelyek a fürtkonfiguráció testreszabására, illetve további szolgáltatások és segédprogramok, például hue, Solr vagy R hozzáadására használhatók.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272499"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Az Azure HDInsight-fürtök testreszabása parancsfájlműveletek használatával

Az Azure HDInsight egy **parancsfájlműveleteknek** nevezett konfigurációs módszert biztosít, amely egyéni parancsfájlokat hív meg a fürt testreszabásához. Ezek a parancsfájlok további összetevők telepítésére és a konfigurációs beállítások módosítására szolgálnak. A parancsfájlműveletek a fürt létrehozása során vagy azt követően is használhatók.

Parancsfájl-műveletek is közzétehető az Azure Marketplace HDInsight-alkalmazásként. A HDInsight-alkalmazásokról további információt a [HDInsight-alkalmazások közzététele az Azure Piactéren című témakörben talál.](hdinsight-apps-publish-applications.md)

## <a name="permissions"></a>Engedélyek

A tartományhoz csatlakozó HDInsight-fürt höz két Apache Ambari-engedély szükséges, ha parancsfájlműveleteket használ a fürthöz:

* **AMBARI, ez az. FUTTASSA\_AZ EGYÉNI\_PARANCSOT**. Az Ambari rendszergazdai szerepkör alapértelmezés szerint rendelkezik ezzel az engedéllyel.
* **CLUSTER-BEN. FUTTASSA\_AZ EGYÉNI\_PARANCSOT**. Mind a HDInsight fürtfelügyelő, mind az Ambari rendszergazda rendelkezik ezzel az engedéllyel.

A tartományhoz csatlakozó HDInsight engedélyeinek kezeléséről a [HDInsight-fürtök kezelése vállalati biztonsági csomaggal](./domain-joined/apache-domain-joined-manage.md)című témakörben talál további információt.

## <a name="access-control"></a>Hozzáférés-vezérlés

Ha nem Ön az Azure-előfizetés rendszergazdája vagy tulajdonosa, a fióknak legalább közreműködői hozzáféréssel kell rendelkeznie a HDInsight-fürtöt tartalmazó erőforráscsoporthoz.

Ha létrehoz egy HDInsight-fürtöt, az Azure-előfizetéshez legalább közreműködői hozzáféréssel rendelkező személynek korábban regisztrálnia kell a HDInsight szolgáltatóját. A szolgáltató regisztrálása akkor történik meg, ha egy olyan felhasználó, aki Közreműködői hozzáféréssel rendelkezik az előfizetéshez, először hoz létre erőforrást az előfizetésben. Erőforrás létrehozása nélkül is elvégezhető, ha [a REST használatával regisztrál szolgáltatót.](https://msdn.microsoft.com/library/azure/dn790548.aspx)

További információ a hozzáférés-kezelés kezeléséről:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Parancsfájlműveletek ismertetése

A parancsfájlművelet a Bash parancsfájl, amely egy HDInsight-fürt csomópontjain fut. A parancsfájl-műveletek jellemzői és jellemzői a következők:

* A HDInsight-fürtről elérhető URI-n kell tárolni. A lehetséges tárolóhelyek a következők:

    * Rendszeres fürtök esetén:

      * ADLS Gen1: A szolgáltatás egyszerű HDInsight a Data Lake Storage eléréséhez kell olvasni a parancsfájlhoz való hozzáférés. A Data Lake Storage Gen1-ben tárolt `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`parancsfájlok URI-formátuma a .

      * Egy blob egy Azure Storage-fiókban, amely vagy a HDInsight-fürt elsődleges vagy további tárfiók. A HDInsight mindkét ilyen típusú tárfiókhoz hozzáférést kap a fürt létrehozása során.

        > [!IMPORTANT]  
        > Ne forgassa el a tárolási kulcsot ezen az Azure Storage-fiókon, mert a további parancsfájl-műveletek sikertelenek lesznek.

      * A http:// elérési utakon elérhető nyilvános fájlmegosztó szolgáltatás. Ilyenek például az Azure Blob, a GitHub, a OneDrive. Például az URI-k, [lásd: Példa parancsfájlok művelet parancsfájlok](#example-script-action-scripts).

     * Esp-vel rendelkező fürtök esetén a wasb:// vagy wasbs:// vagy http[s]:// URI-k támogatottak.

* Csak bizonyos csomóponttípusokon futtatható. Ilyenek például a főcsomópontok vagy a feldolgozói csomópontok.

* Lehet tartósan vagy ad hoc.

    A megőrzött parancsfájlműveleteknek egyedi névvel kell rendelkezniük. A megőrzött parancsfájlok a fürthöz méretezési műveleteken keresztül hozzáadott új munkavégző csomópontok testreszabására szolgálnak. A megőrzött parancsfájl egy másik csomóponttípusra is alkalmazhat módosításokat méretezési műveletek esetén. Erre példa a főcsomópont.

    Az ad hoc parancsfájlok nem maradnak meg. A fürt létrehozása során használt parancsfájlműveletek automatikusan megmaradnak. Ezek nem lesznek alkalmazva a fürthöz a parancsfájl futtatása után hozzáadott munkavégző csomópontokra. Ezután előléptethet egy ad hoc parancsfájlt egy megőrzött parancsfájlba, vagy lefokozhat egy megőrzött parancsfájlt egy ad hoc parancsfájlra. A sikertelen parancsfájlok nem maradnak meg, még akkor sem, ha kifejezetten jelzi, hogy azoknak kellene lenniük.

* A parancsfájl által a végrehajtás során használt paramétereket is elfogadhatja.

* Root-szintű jogosultságokkal futva a fürtcsomópontokon.

* Az Azure Portalon, az Azure PowerShellen, az Azure CLI-n vagy a HDInsight .NET SDK-n keresztül használható.

A fürt megőrzi az összes futtatott parancsfájl előzményeit. Az előzmények segítenek, ha meg kell találnia egy parancsfájl azonosítóját előléptetési vagy lefokozási műveletekhez.

> [!IMPORTANT]  
> A parancsfájl-művelet által végrehajtott módosításokat nem lehet automatikusan visszavonni. Manuálisan sztornírozzák a módosításokat, vagy olyan parancsfájlt adnak meg, amely megfordítja azokat.

### <a name="script-action-in-the-cluster-creation-process"></a>Parancsfájl-művelet a fürt létrehozási folyamatában

A fürt létrehozása során használt parancsfájlműveletek némileg eltérnek a meglévő fürtön futó parancsfájlműveletektől:

* A parancsfájl automatikusan megmarad.

* A parancsfájl hibája a fürt létrehozási folyamatának sikertelensedését okozhatja.

Az alábbi ábra azt szemlélteti, hogy a parancsfájl-művelet mikor fut le a létrehozási folyamat során:

![A HDInsight-fürt testreszabása és szakaszai a fürt létrehozása során][img-hdi-cluster-states]

A parancsfájl a HDInsight konfigurálása közben fut. A parancsfájl párhuzamosan fut a fürt összes megadott csomópontján. Root jogosultságokkal fut a csomópontokon.

Olyan műveleteket hajthat végre, mint a szolgáltatások leállítása és indítása, beleértve az Apache Hadoop-hoz kapcsolódó szolgáltatásokat is. Ha leállítja a szolgáltatásokat, győződjön meg arról, hogy az Ambari szolgáltatás és más Hadoop-szolgáltatások futnak, mielőtt a parancsfájl befejeződik. Ezek a szolgáltatások szükségesek a fürt állapotának és állapotának sikeres meghatározásához a létrehozás ideje alatt.

A fürt létrehozása során egyszerre több parancsfájlműveletet is használhat. Ezeket a parancsfájlokat a megadott sorrendben hívja meg a parancsfájlok.

> [!IMPORTANT]  
> A parancsfájlműveleteknek 60 percen belül be kell fejeződniük, különben időbeli megtelnek. A fürt kiépítése során a parancsfájl más beállítási és konfigurációs folyamatokkal egyidejűleg fut. Az erőforrásokért, például a processzoridőért vagy a hálózati sávszélességért való verseny miatt a parancsfájl befejezése hosszabb időt vehet igénybe, mint a fejlesztői környezetben.
>
> A parancsfájl futtatásához szükséges idő minimalizálása érdekében kerülje az olyan feladatokat, mint az alkalmazások letöltése és összeállítása a forrásból. Előre lefordítani alkalmazásokat, és tárolja a bináris az Azure Storage.Precompile applications and store the binary in Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Parancsfájlművelet futó fürtön

A már futó fürtön futó parancsfájl meghibásodása nem okozza automatikusan a fürt meghibásodott állapotba váltását. A parancsfájl befejezése után a fürtnek vissza kell térnie egy futó állapotba. Még akkor is, ha a fürt egy futó állapot, a sikertelen parancsfájl lehet, hogy megszakadt a dolgokat. Előfordulhat például, hogy egy parancsfájl törli a fürt által szükséges fájlokat.

A parancsfájlok műveletek gyökérjogosultságokkal futnak. Győződjön meg arról, hogy megértette, mit csinál egy parancsfájl, mielőtt alkalmazza azt a fürtre.

Amikor parancsfájlt alkalmaz egy fürtre, a fürt állapota **Futásról** **Elfogadva**állapotra változik. Ezután a **HDInsight-konfigurációra** változik, és végül vissza a sikeres parancsfájlok **futtatásához.** A parancsfájl állapota bevan jelentkezve a parancsfájl műveletelőzményeibe. Ez az információ jelzi, hogy a parancsfájl sikeres vagy sikertelen volt. Például a `Get-AzHDInsightScriptActionHistory` PowerShell-parancsmag egy parancsfájl állapotát mutatja. A következő szöveghez hasonló információkat ad vissza:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Ha módosítja a fürt felhasználóját, rendszergazda, jelszó létrehozása után, parancsfájlműveletek futnak a fürthöz, előfordulhat, hogy nem sikerül. Ha olyan megőrzött parancsfájlműveleteket, amelyek a feldolgozócsomópontokat célozzák meg, ezek a parancsfájlok sikertelenek lehetnek a fürt méretezésekénél.

## <a name="example-script-action-scripts"></a>Példa parancsfájl-műveletparancsfájlokra

A parancsfájlok műveletparancsfájljai a következő segédprogramokon keresztül használhatók:

* Azure portál
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

A HDInsight parancsfájlokat biztosít a következő összetevők HDInsight-fürtökön való telepítéséhez:

| Név | Parancsfájl |
| --- | --- |
| Azure Storage-fiók hozzáadása |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Lásd: [További tárfiókok hozzáadása a HDInsighthoz.](hdinsight-hadoop-add-storage.md) |
| Színárnyalat telepítése |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Lásd: [Hue telepítése és használata HDInsight Hadoop-fürtökön.](hdinsight-hadoop-hue-linux.md) |
| Hive-könyvtárak előzetes betöltése |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Lásd: [Egyéni Apache Hive-könyvtárak hozzáadása a HDInsight-fürt létrehozásakor.](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="script-action-during-cluster-creation"></a>Parancsfájl-művelet a fürt létrehozása során

Ez a szakasz bemutatja, hogy a HDInsight-fürt létrehozásakor milyen különböző módokon használhatja a parancsfájlműveleteket.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Parancsfájl-művelet használata az Azure Portalról való fürtlétrehozása során

1. Az Azure Portal használatával indítsa el [a fürt létrehozását a HDInsightban linuxalapú fürtök létrehozása című részben leírtak szerint.](hdinsight-hadoop-create-linux-clusters-portal.md) A **Konfiguráció + árképzés** lapon válassza a **+ Parancsfájl hozzáadása művelet lehetőséget.**

    ![Az Azure Portal fürtparancsfájl-művelete](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Előre elkészített __parancsfájl kiválasztása parancsfájl-bejegyzés kiválasztásához__ használja a Parancsfájl-bejegyzés kiválasztása lehetőséget. Egyéni parancsfájl használatához válassza az __Egyéni__lehetőséget. Ezután adja meg a __név__ és a __Bash parancsfájl URI__ a parancsfájlhoz.

    ![Parancsfájl hozzáadása a kijelölt parancsfájlűrlaphoz](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Az alábbi táblázat az űrlap elemeit ismerteti:

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl kijelölése | A saját parancsfájl használatához válassza az __Egyéni__lehetőséget. Ellenkező esetben válassza ki a megadott parancsfájlok egyikét. |
    | Név |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájl URI |Adja meg a parancsfájl URI-ját. |
    | Fej/Dolgozó/Zookeeper |Adja meg azokat a csomópontokat, amelyeken a parancsfájl fut: **Head**, **Worker**vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha a parancsfájl megköveteli. |

    A __Parancsfájl megőrzése műveletbejegyzés__ segítségével győződjön meg arról, hogy a parancsfájl alkalmazása méretezési műveletek során történik.

1. A parancsfájl mentéséhez válassza a __Létrehozás__ gombot. Ezután __használhatja + Új küldése__ egy másik szkript hozzáadásához.

    ![HDInsight többparancsfájlos műveletek](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Ha befejezte a parancsfájlok hozzáadását, visszatér a **Konfiguráció + árképzés** lapra.

1. Végezze el a fennmaradó fürtlétrehozási lépéseket a szokásos módon.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Parancsfájl-művelet használata az Azure Resource Manager-sablonokból

Parancsfájl-műveletek azure Resource Manager-sablonokkal használható. Például lásd: [HDInsight Linux-fürt létrehozása és parancsfájlművelet futtatása.](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)

Ebben a példában a parancsfájlművelet a következő kód dal kerül hozzáadásra:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

További információ a sablonok üzembe helyezéséről:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Parancsfájl-művelet használata az Azure PowerShellből való fürtlétrehozása során

Ebben a szakaszban az [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) parancsmag használatával parancsfájlokat hívhat meg a fürt testreszabásához. Mielőtt elkezdené, győződjön meg arról, hogy telepíti és konfigurálja az Azure PowerShellt. A PowerShell-parancsok használatához az [AZ modulra](https://docs.microsoft.com/powershell/azure/overview)van szükség.

A következő parancsfájl bemutatja, hogyan alkalmazhat parancsfájlműveletet, amikor fürtöt hoz létre a PowerShell használatával:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

A fürt létrehozása több percig is eltarthat.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Parancsfájl-művelet használata a HDInsight .NET SDK fürtből történő létrehozása során

A HDInsight .NET SDK olyan ügyfélkódtárakat biztosít, amelyek megkönnyítik a HDInsight használatát egy .NET alkalmazásból. A kódmintáról a [Parancsfájlműveletek témakörben van.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions)

## <a name="script-action-to-a-running-cluster"></a>Parancsfájlművelet futó fürthöz

Ez a szakasz azt ismerteti, hogyan alkalmazhat parancsfájlműveleteket egy futó fürtre.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Parancsfájl-művelet alkalmazása egy futó fürtre az Azure Portalról

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg a fürtöt.

1. Az alapértelmezett nézet **Beállítások**területén válassza a **Parancsfájlműveletek**lehetőséget.

1. A **Parancsfájl-műveletek** lap tetején válassza a **+ Submit new**lehetőséget.

    ![Parancsfájl hozzáadása futó fürthöz](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Előre elkészített __parancsfájl kiválasztása parancsfájl-bejegyzés kiválasztásához__ használja a Parancsfájl-bejegyzés kiválasztása lehetőséget. Egyéni parancsfájl használatához válassza az __Egyéni__lehetőséget. Ezután adja meg a __név__ és a __Bash parancsfájl URI__ a parancsfájlhoz.

    ![Parancsfájl hozzáadása a kijelölt parancsfájlűrlaphoz](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Az alábbi táblázat az űrlap elemeit ismerteti:

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl kijelölése | Ha saját parancsfájlt szeretne használni, válassza az __egyéni__lehetőséget. Ellenkező esetben jelöljön ki egy megadott parancsfájlt. |
    | Név |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájl URI |Adja meg a parancsfájl URI-ját. |
    | Vezető/dolgozó/Állattartó |Adja meg azokat a csomópontokat, amelyeken a parancsfájl fut: **Head**, **Worker**vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha a parancsfájl megköveteli. |

    A __Parancsfájl megőrzése műveletbejegyzés__ segítségével győződjön meg arról, hogy a parancsfájl a méretezési műveletek során van alkalmazva.

1. Végül kattintson a **Létrehozás** gombra, ha a parancsfájlt a fürtre szeretné alkalmazni.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Parancsfájlművelet alkalmazása egy futó fürtre az Azure PowerShellből

A PowerShell-parancsok használatához az [AZ modulra](https://docs.microsoft.com/powershell/azure/overview)van szükség. A következő példa bemutatja, hogyan alkalmazhat parancsfájlműveletet egy futó fürtre:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

A művelet befejezése után a következő szöveghez hasonló információkat kap:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Parancsfájlművelet alkalmazása egy futó fürtre az Azure CLI-ből

Mielőtt elkezdené, győződjön meg arról, hogy telepíti és konfigurálja az Azure CLI.Before you start, make sure you install and configure the Azure CLI. Győződjön meg arról, hogy a legújabb verzióval rendelkezik. További információ: [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Hitelesítse magát Azure-előfizetésében:

    ```azurecli
    az login
    ```

1. Parancsfájlművelet alkalmazása futó fürtre:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Az érvényes `headnode` `workernode`szerepkörök a , `zookeepernode`, , `edgenode`. Ha a parancsfájlt több csomóponttípusra kell alkalmazni, válassza el a szerepköröket egy szóközvel. Például: `--roles headnode workernode`.

    A parancsfájl megőrzéséhez `--persist-on-success`adja hozzá a ot. A parancsfájlt később is `az hdinsight script-action promote`megőrizheti a használatával.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Parancsfájlművelet alkalmazása futó fürtre a REST API használatával

Lásd: [FürtREST API az Azure HDInsightban.](https://msdn.microsoft.com/library/azure/mt668441.aspx)

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Parancsfájlművelet alkalmazása futó fürtre a HDInsight .NET SDK-ból

A .NET SDK használatával parancsfájlokat alkalmazhat egy fürtre, olvassa el a [Parancsfájl-művelet alkalmazása egy futó Linux-alapú HDInsight-fürtre című témakört.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

## <a name="view-history-and-promote-and-demote-script-actions"></a>Előzmények megtekintése, valamint parancsfájlműveletek előléptetése és lefokozása

### <a name="the-azure-portal"></a>Az Azure Portal

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg a fürtöt.

1. Az alapértelmezett nézet **Beállítások**területén válassza a **Parancsfájlműveletek**lehetőséget.

1. A fürt parancsfájljainak előzményei megjelennek a parancsfájlműveletek szakaszban. Ez az információ tartalmazza a megőrzött parancsfájlok listáját. A következő képernyőkép azt mutatja, hogy a Solr parancsfájl fut ezen a fürtön. A képernyőkép nem jelenít meg megőrzött parancsfájlokat.

    ![A portálparancsfájl-műveletek beküldik az előzményeket](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Jelöljön ki egy parancsfájlt az előzmények közül a parancsfájl **Tulajdonságok** szakaszának megjelenítéséhez. A képernyő felső részén újrafuttathatja a parancsfájlt, vagy előléptetheti azt.

    ![A parancsfájlműveletek tulajdonságai elősegítik](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. A parancsfájlműveletek szakasz bejegyzéseitől **...** jobbra lévő három pontot is kijelölheti a műveletek végrehajtásához.

    ![A megőrzött parancsfájlműveletek törlése](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Parancsmag | Függvény |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |A megőrzött parancsfájlműveletekre vonatkozó információk lekérése. Ez a parancsmag nem vonja vissza a parancsfájl által végrehajtott műveleteket, csak a megőrzött jelzőt távolítja el.|
| `Get-AzHDInsightScriptActionHistory` |A fürtre alkalmazott parancsfájl-műveletek előzményeinek lekérése vagy egy adott parancsfájl részletei. |
| `Set-AzHDInsightPersistedScriptAction` |Ad hoc parancsfájl-művelet előléptetése egy megőrzött parancsfájlműveletre. |
| `Remove-AzHDInsightPersistedScriptAction` |A megőrzött parancsfájlművelet lefokozása ad hoc műveletre. |

A következő példaparancsfájl bemutatja a parancsmagok használatát egy parancsfájl előléptetésére, majd lefokozására.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Parancs | Leírás |
| --- | --- |
| [az hdinsight parancsfájlművelet törlése](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Törli a fürt megadott, nem őrzött parancsfájlműveletét. Ez a parancs nem vonja vissza a parancsfájl által végrehajtott műveleteket, csak a megőrzött jelzőt távolítja el.|
|[az hdinsight parancsfájlművelet végrehajtása](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Parancsfájlműveletek végrehajtása a megadott HDInsight-fürtön.|
| [az hdinsight parancsfájl-műveletlista](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |A megadott fürt összes megőrzött parancsfájlműveletének listája. |
|[az hdinsight script-action list-execution-history](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Felsorolja az összes parancsfájl végrehajtási előzményeit a megadott fürthöz.|
|[az hdinsight script-action előléptetése](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|A megadott ad-hoc parancsfájl-végrehajtást egy megőrzött parancsfájllá lépteti elő.|
|[az hdinsight script-action show-execution-details](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Lekéri a parancsfájl végrehajtási részleteit az adott parancsfájl végrehajtási azonosítóhoz.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

A .NET SDK használatával lekérheti a parancsfájlokat egy fürtből, előléptetheti vagy lefeketítheti a parancsfájlokat, olvassa el a [Parancsfájl-művelet alkalmazása egy futó Linux-alapú HDInsight-fürthöz című témakört.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

> [!NOTE]  
> Ez a példa azt is bemutatja, hogyan telepíthet egy HDInsight-alkalmazást a .NET SDK használatával.

## <a name="support-for-open-source-software"></a>Nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatás az Apache Hadoop körül kialakított nyílt forráskódú technológiák ökoszisztémáját használja. A Microsoft Azure általános szintű támogatást nyújt a nyílt forráskódú technológiákhoz. További információt az Azure-támogatás – gyakori kérdések **– Támogatási hatókör** című [szakaszában talál.](https://azure.microsoft.com/support/faq/) A HDInsight szolgáltatás további támogatási szintet biztosít a beépített összetevők számára.

A HDInsight szolgáltatásban kétféle nyílt forráskódú összetevő érhető el:

* **Beépített alkatrészek**. Ezek az összetevők előtelepítettHDInsight-fürtökön vannak telepítve, és a fürt alapvető funkcióit biztosítják. A következő összetevők tartoznak ebbe a kategóriába:

  * [Apache Hadoop FONAL](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Erőforrás-kezelő.
  * A Hive lekérdezési nyelv [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/).

    A fürtösszetevők teljes listája a [Mik azok az Apache Hadoop-összetevők és -verziók, amelyek a HDInsight segítségével érhetők el?](hdinsight-component-versioning.md)

* **Egyéni összetevők**. A fürt felhasználójaként telepítheti vagy használhatja a munkaterhelésben a közösségben elérhető vagy Ön által létrehozott bármely összetevőt.

> [!WARNING]  
> A HDInsight-fürthöz mellékelt összetevők teljes mértékben támogatottak. A Microsoft támogatási szolgálata segít az összetevőkkel kapcsolatos problémák elkülönítéséhez és megoldásához.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további elhárításához. Előfordulhat, hogy a Microsoft támogatási szolgálata meg tudja oldani a problémát. Vagy megkérhetik Önt, hogy vegyen részt a nyílt forráskódú technológiák rendelkezésre álló csatornáiban, ahol mély szakértelem rejlehet az adott technológiával kapcsolatosan. Számos közösségi oldal használható. Ilyenek például a HDInsight és a Stack Overflow [MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) [fóruma.](https://stackoverflow.com)
>
> Apache projektek is projekt oldalak az [Apache honlapján](https://apache.org). Erre példa a [Hadoop](https://hadoop.apache.org/).

A HDInsight szolgáltatás számos lehetőséget kínál az egyéni összetevők használatára. Az összetevők fürtre való alkalmazásának vagy telepítésétől függetlenül ugyanaz a támogatási szint érvényes. Az alábbi lista az egyéni összetevők HDInsight-fürtökön való használatának leggyakoribb módjait ismerteti:

1. **Állásbenyújtás**. A Hadoop vagy más típusú feladatok, amelyek egyéni összetevőket hajtanak végre vagy használnak, elküldhetők a fürtnek.

2. **Fürt testreszabása**. A fürt létrehozása során további beállításokat és egyéni összetevőket adhat meg, amelyek a fürtcsomópontokon vannak telepítve.

3. **Minták**. A népszerű egyéni összetevők esetében a Microsoft és mások mintákat nyújthatnak arról, hogyan használhatók ezek az összetevők a HDInsight-fürtökön. Ezek a minták támogatás nélkül állnak rendelkezésre.

## <a name="troubleshooting"></a>Hibaelhárítás

Az Ambari webes felhasználói felületsegítségével megtekintheti a parancsfájlműveletek által naplózott információkat. Ha a parancsfájl a fürt létrehozása során sikertelen, a naplók a fürthöz társított alapértelmezett tárfiókban is elérhetők. Ez a szakasz a naplók mindkét beállítás használatával történő beolvasásával kapcsolatos információkat tartalmazza.

### <a name="the-apache-ambari-web-ui"></a>Az Apache Ambari webes felhasználói felülete

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. A lap tetején lévő sávból válassza ki a **műveleti** bejegyzést. A lista az Ambari-n keresztül a fürtön végzett aktuális és korábbi műveleteket jeleníti meg.

    ![Ambari webes felhasználói felület sáv jattot kijelölt műveleti](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. Keresse meg azokat a bejegyzéseket, amelyek az Operations oszlopban **futtatták\_** az egyéni **parancsfájlt.** Ezek a bejegyzések a parancsfájlműveletek futtatásakor jönnek létre.

    ![Apache Ambari parancsfájlműveleti műveletek](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Az **STDOUT** és az **STDERR** kimenet megtekintéséhez jelölje ki a **run\customscriptaction** bejegyzést, és ásson le a hivatkozásokközött. Ez a kimenet akkor jön létre, amikor a parancsfájl fut, és hasznos információkat tartalmazhat.

### <a name="access-logs-from-the-default-storage-account"></a>Naplók elérése az alapértelmezett tárfiókból

Ha a fürt létrehozása parancsfájlhiba miatt sikertelen, a naplók a fürttárfiókban maradnak.

* A tárolónaplók a `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`helyen érhetők el.

    ![Parancsfájl-műveletnaplók](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Ebben a könyvtárban a naplók külön vannak rendezve a **headnode**, **a feldolgozócsomópont**és **az állattartó csomópont**számára. Lásd az alábbi példákat:

    * **Csomópont**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Feldolgozó csomópont:**`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper csomópont:**`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* A megfelelő gazdagép összes **stdout** és **stderr** feltöltésre kerül a tárfiókba. Minden parancsfájlművelethez egy **kimenet\*van- .txt** és **errors-\*.txt.** A **output-*.txt** fájl az állomáson futtatott parancsfájl URI-járól tartalmaz információkat. A következő szöveg egy példa erre az információra:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Lehetséges, hogy ismételten létrehoz egy parancsfájl-műveletfürtet ugyanazzal a névvel. Ebben az esetben a **DÁTUM** mappa neve alapján megkülönböztetheti a megfelelő naplókat. Például egy fürt mappaszerkezete, a különböző időpontokban létrehozott **mycluster**a következő naplóbejegyzésekhez hasonlóan jelenik meg:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Ha ugyanazon a napon azonos nevű parancsfájl-műveletfürtöt hoz létre, az egyedi előtaggal azonosíthatja a megfelelő naplófájlokat.

* Ha 12:00 óra, éjfél közelében hoz létre fürtöt, lehetséges, hogy a naplófájlok két napig terjednek. Ebben az esetben két különböző dátummappa jelenik meg ugyanahhoz a fürthöz.

* A naplófájlok feltöltése az alapértelmezett tárolóba akár öt percet is igénybe vehet, különösen nagy fürtök esetén. Tehát ha szeretné elérni a naplókat, ne azonnal törölje a fürtöt, ha egy parancsfájl művelet sikertelen.

### <a name="ambari-watchdog"></a>Ambari házőrző

> [!WARNING]  
> Ne módosítsa az Ambari figyelő, hdinsightwatchdog jelszavát a Linux-alapú HDInsight-fürtön. A fiók jelszavának módosítása megszakítja az új parancsfájlműveletek futtatásának lehetőségét a HDInsight-fürtön.

### <a name="cant-import-name-blobservice"></a>A BlobService név nem importálható

__Tünetek__. A parancsfájlművelet sikertelen. Az Ambari művelet megtekintésekor a következőhöz hasonló szöveg jelenik meg:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Oka__. Ez a hiba akkor fordul elő, ha frissíti a Python Azure Storage-ügyfél, amely a HDInsight-fürt részét képezi. A HDInsight 0.20.0-s Azure Storage-ügyfélre számít.

__felbontás .__ A hiba elhárításához manuálisan csatlakozzon az `ssh`egyes fürtcsomópontokhoz a használatával. A megfelelő tárolóügyfél-verzió újratelepítéséhez futtassa a következő parancsot:

```bash
sudo pip install azure-storage==0.20.0
```

A fürthöz az SSH-val való csatlakozásról a [Csatlakozás hdinsighthoz (Apache Hadoop) ssh használatával](hdinsight-hadoop-linux-use-ssh-unix.md)című témakörben talál további információt.

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Az előzmények nem jelenítik meg a fürt létrehozása során használt parancsfájlokat

Ha a fürt 2016. A fürt átméretezése azt eredményezi, hogy a parancsfájlok megjelennek a parancsfájlok műveletelőzményeiben.

Két kivétel van:

* A fürt 2015. Ez a dátum az, amikor parancsfájlműveleteket vezettek be. Az ezt a dátum előtt létrehozott fürt nem használhatott parancsfájlműveleteket a fürt létrehozásához.

* A fürt létrehozása során több parancsfájlműveletet használt. Vagy ugyanazt a nevet használta több parancsfájlhoz, vagy ugyanazt a nevet, ugyanazt az URI-t, de több parancsfájl különböző paramétereit. Ezekben az esetekben a következő hibaüzenet jelenik meg:

    Ezen a fürtön nem futtathatók új parancsfájl-műveletek, mert a meglévő parancsfájlok nem ütköznek egymásnak. A fürt létrehozásánál megadott parancsfájlneveknek egyedinek kell lenniük. A meglévő parancsfájlok átméretezéskor futnak.

## <a name="next-steps"></a>További lépések

* [Parancsfájl-műveletparancsfájlok fejlesztése a HDInsighthoz](hdinsight-hadoop-script-actions-linux.md)
* [További tárhely hozzáadása HDInsight-fürthöz](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "A fürt létrehozása során fellépő szakaszok"
