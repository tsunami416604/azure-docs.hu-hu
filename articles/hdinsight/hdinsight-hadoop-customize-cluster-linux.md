---
title: Az Azure HDInsight-fürtök testreszabása parancsfájlműveletek használatával
description: Parancsfájlműveletek használatával egyéni összetevőket adhat hozzá a HDInsight-fürtökhöz. A parancsfájlműveletek Bash-parancsfájlok, amelyek a fürt konfigurációjának testreszabására használhatók. Vagy adjon hozzá további szolgáltatásokat és segédprogramokat, például hue, Solr vagy R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 434d4adb763fd0e0a29c065ce051bfd4fa461180
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770736"
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

Valaki, aki legalább közreműködői hozzáféréssel rendelkezik az Azure-előfizetéshez, korábban regisztrálnia kell a szolgáltatót. A szolgáltató regisztrációja akkor történik, amikor egy közreműködői hozzáféréssel rendelkező felhasználó létrehoz egy erőforrást. Erőforrás létrehozása nélkül lásd: [szolgáltató regisztrálása a REST használatával.](https://msdn.microsoft.com/library/azure/dn790548.aspx)

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

* Lehet tartani, `ad hoc`vagy .

    A megőrzött parancsfájlműveleteknek egyedi névvel kell rendelkezniük. A megőrzött parancsfájlok a fürthöz méretezési műveleteken keresztül hozzáadott új munkavégző csomópontok testreszabására szolgálnak. A megőrzött parancsfájl egy másik csomóponttípusra is alkalmazhat módosításokat méretezési műveletek esetén. Erre példa a főcsomópont.

    `Ad hoc`a parancsfájlok nem maradnak meg. A fürt létrehozása során használt parancsfájlműveletek automatikusan megmaradnak. Ezek nem lesznek alkalmazva a fürthöz a parancsfájl futtatása után hozzáadott munkavégző csomópontokra. Ezután előléptethet egy `ad hoc` parancsfájlt egy megőrzött parancsfájlba, `ad hoc` vagy lefokozhat egy megőrzött parancsfájlt egy parancsfájlba. A sikertelen parancsfájlok nem maradnak meg, még akkor sem, ha kifejezetten jelzi, hogy azoknak kellene lenniük.

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

Olyan műveleteket is elérhet, mint a szolgáltatások leállítása és indítása, beleértve az Apache Hadoop-hoz kapcsolódó szolgáltatásokat is. Ha leállítja a szolgáltatásokat, győződjön meg arról, hogy az Ambari és más Hadoop-szolgáltatások futnak, mielőtt a parancsfájl befejeződik. Ezek a szükséges szolgáltatások határozzák meg a fürt állapotát és állapotát a létrehozás ideje alatt.

A fürt létrehozása során egyszerre több parancsfájlműveletet is használhat. Ezeket a parancsfájlokat a megadott sorrendben hívja meg a parancsfájlok.

> [!IMPORTANT]  
> A parancsfájlműveleteknek 60 percen belül be kell fejeződniük, különben időbeli megtelnek. A fürt kiépítése során a parancsfájl más beállítási és konfigurációs folyamatokkal egyidejűleg fut. Az erőforrásokért, például a processzoridőért vagy a hálózati sávszélességért való verseny miatt a parancsfájl befejezése hosszabb időt vehet igénybe, mint a fejlesztői környezetben.
>
> A parancsfájl futtatásához szükséges idő minimalizálása érdekében kerülje az olyan feladatokat, mint az alkalmazások letöltése és összeállítása a forrásból. Előre lefordítani alkalmazásokat, és tárolja a bináris az Azure Storage.Precompile applications and store the binary in Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Parancsfájlművelet futó fürtön

Egy már futó fürt parancsfájlhibája nem eredményezi automatikusan a fürt hibás állapotba váltását. A parancsfájl befejezése után a fürtnek vissza kell térnie egy futó állapotba. Még akkor is, ha a fürt egy futó állapot, a sikertelen parancsfájl lehet, hogy megszakadt a dolgokat. Előfordulhat például, hogy egy parancsfájl törli a fürt által szükséges fájlokat.

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

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

A HDInsight parancsfájlokat biztosít a következő összetevők HDInsight-fürtökön való telepítéséhez:

| Name (Név) | Parancsfájl |
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
    | Name (Név) |Adja meg a parancsfájlművelet nevét. |
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
    | Name (Név) |Adja meg a parancsfájlművelet nevét. |
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

1. A parancsfájlműveletek szakasz bejegyzéseitől **...** jobbra lévő három pontot is kiválaszthatja a műveletek műveleteihez.

    ![A megőrzött parancsfájlműveletek törlése](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Parancsmag | Függvény |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |A megőrzött parancsfájlműveletekre vonatkozó információk lekérése. Ez a parancsmag nem vonja vissza a parancsfájl által végrehajtott műveleteket, csak a megőrzött jelzőt távolítja el.|
| `Get-AzHDInsightScriptActionHistory` |A fürtre alkalmazott parancsfájl-műveletek előzményeinek lekérése vagy egy adott parancsfájl részletei. |
| `Set-AzHDInsightPersistedScriptAction` |Parancsfájl-művelet előléptetése `ad hoc` megőrzött parancsfájlműveletre. |
| `Remove-AzHDInsightPersistedScriptAction` |Egy megőrzött parancsfájlművelet lefokozása műveletre. `ad hoc` |

A következő példaparancsfájl bemutatja a parancsmagok használatát egy parancsfájl előléptetésére, majd lefokozására.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Parancs | Leírás |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Törli a fürt megadott, nem őrzött parancsfájlműveletét. Ez a parancs nem vonja vissza a parancsfájl által végrehajtott műveleteket, csak a megőrzött jelzőt távolítja el.|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Parancsfájlműveletek végrehajtása a megadott HDInsight-fürtön.|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |A megadott fürt összes megőrzött parancsfájlműveletének listája. |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Felsorolja az összes parancsfájl végrehajtási előzményeit a megadott fürthöz.|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|A megadott ad-hoc parancsfájl-végrehajtást egy megőrzött parancsfájllá lépteti elő.|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Lekéri a parancsfájl végrehajtási részleteit az adott parancsfájl végrehajtási azonosítóhoz.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

A .NET SDK használatával lekérheti a parancsfájlokat egy fürtből, előléptetheti vagy lefeketítheti a parancsfájlokat, olvassa el a [Parancsfájl-művelet alkalmazása egy futó Linux-alapú HDInsight-fürthöz című témakört.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

> [!NOTE]  
> Ez a példa azt is bemutatja, hogyan telepíthet egy HDInsight-alkalmazást a .NET SDK használatával.

## <a name="next-steps"></a>További lépések

* [Parancsfájl-műveletparancsfájlok fejlesztése a HDInsighthoz](hdinsight-hadoop-script-actions-linux.md)
* [További tárhely hozzáadása HDInsight-fürthöz](hdinsight-hadoop-add-storage.md)
* [Parancsfájlműveletek – problémamegoldás](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "A fürt létrehozása során fellépő szakaszok"
