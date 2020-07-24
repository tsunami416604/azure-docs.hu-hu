---
title: Azure HDInsight-fürtök testreszabása parancsfájl-műveletek használatával
description: Egyéni összetevők hozzáadása a HDInsight-fürtökhöz parancsfájl-műveletek használatával. A parancsfájl-műveletek olyan bash-parancsfájlok, amelyek a fürtkonfiguráció testreszabására használhatók. További szolgáltatásokat és segédprogramokat (például Hue, Solr vagy R) adhat hozzá.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 383c64c585f05869e1d01b5c99693fcf560cdedc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006671"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Azure HDInsight-fürtök testreszabása parancsfájl-műveletek használatával

Az Azure HDInsight egy **parancsfájl-műveletek** nevű konfigurációs módszert biztosít, amely egyéni parancsfájlokat hív meg a fürt testreszabásához. Ezek a parancsfájlok további összetevők telepítésére és a konfigurációs beállítások módosítására szolgálnak. Parancsfájl-műveletek a fürt létrehozása során vagy után is használhatók.

A parancsfájlok műveletei az Azure Marketplace-en is HDInsight-alkalmazásként közzétehetők. A HDInsight alkalmazásokkal kapcsolatos további információkért lásd: [HDInsight-alkalmazás közzététele az Azure Marketplace-](hdinsight-apps-publish-applications.md)en.

## <a name="permissions"></a>Engedélyek

Tartományhoz csatlakoztatott HDInsight-fürt esetén két Apache Ambari engedély szükséges, ha parancsfájl-műveleteket használ a fürthöz:

* **AMBARI. \_egyéni \_ parancs futtatása**. A Ambari rendszergazdai szerepkör alapértelmezés szerint rendelkezik ezzel az engedéllyel.
* **Fürt. \_egyéni \_ parancs futtatása**. Alapértelmezés szerint a HDInsight-fürt rendszergazdája és a Ambari rendszergazdája is rendelkezik ezzel az engedéllyel.

Az engedélyek tartományhoz csatlakoztatott HDInsight való használatáról további információért lásd: [HDInsight-fürtök kezelése Enterprise Security Packagesal](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Hozzáférés-vezérlés

Ha nem Ön az Azure-előfizetés rendszergazdája vagy tulajdonosa, a fióknak legalább közreműködői hozzáféréssel kell rendelkeznie a HDInsight-fürtöt tartalmazó erőforráscsoporthoz.

Az Azure-előfizetéshez legalább közreműködői hozzáféréssel rendelkező személynek előzőleg regisztrálnia kell a szolgáltatót. A szolgáltató regisztrálása akkor történik meg, amikor az előfizetéshez közreműködői hozzáféréssel rendelkező felhasználó létrehoz egy erőforrást. Erőforrás létrehozása nélkül: [szolgáltató regisztrálása a REST használatával](https://msdn.microsoft.com/library/azure/dn790548.aspx).

További információ a hozzáférés-kezeléssel kapcsolatban:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>A parancsfájl-műveletek ismertetése

A parancsfájl művelete egy HDInsight-fürt csomópontjain futó bash-parancsfájl. A parancsfájl-műveletek jellemzői és funkciói a következők:

* Olyan URI-n kell tárolni, amely elérhető a HDInsight-fürtből. A következő tárolási helyszínek lehetségesek:

    * Normál fürtök esetén:

      * ADLS Gen1: az egyszerű szolgáltatásnak a HDInsight által használt Data Lake Storage eléréséhez olvasási hozzáféréssel kell rendelkeznie a parancsfájlhoz. A Data Lake Storage Gen1ban tárolt parancsfájlok URI-formátuma `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` .

      * Egy Azure Storage-fiókban található blob, amely a HDInsight-fürthöz tartozó elsődleges vagy további Storage-fiók. A HDInsight mindkét típusú Storage-fiókhoz hozzáférést kap a fürt létrehozása során.

        > [!IMPORTANT]  
        > Ne forgassa el ezen az Azure Storage-fiókon a Storage-kulcsot, mert az azt követő parancsfájl-műveleteket nem sikerül.

      * Egy nyilvános fájlmegosztás szolgáltatás http://elérési utakon keresztül érhető el. Ilyenek például az Azure Blob, a GitHub, a OneDrive. Az URI-k például a [parancsfájl műveleti parancsfájljai](#example-script-action-scripts)című részben olvashatók.

     * Az ESP-vel rendelkező fürtök esetén a wasb://vagy a wasbs://vagy a http [s]://URI-k támogatottak.

* Csak bizonyos csomópont-típusok futtatására korlátozható. Ilyenek például a fő csomópontok vagy a munkavégző csomópontok.

* Megtartható vagy `ad hoc` .

    A megőrzött parancsfájl-műveleteknek egyedi névvel kell rendelkezniük. A megőrzött parancsfájlok a fürthöz a skálázási műveletekkel hozzáadott új munkavégző csomópontok testreszabására szolgálnak. A megőrzött parancsfájlok a skálázási műveletek végrehajtásakor is alkalmazhatják a másik csomópont-típus módosításait. Ilyen például egy fő csomópont.

    `Ad hoc`a parancsfájlok nem maradnak meg. A fürt létrehozásakor használt parancsfájl-műveleteket a rendszer automatikusan megőrzi. Nem vonatkoznak a fürthöz a parancsfájl futtatása után hozzáadott munkavégző csomópontokra. Ezután előléptetheti `ad hoc` a parancsfájlokat egy megőrzött parancsfájlba, vagy lefokozni egy megőrzött parancsfájlt egy `ad hoc` parancsfájlba. A meghiúsult parancsfájlok nem maradnak meg, még akkor is, ha kifejezetten arra utalnak, hogy legyenek.

* Elfogadhatja a parancsfájl által a végrehajtás során használt paramétereket.

* A fürt csomópontjain gyökérszintű jogosultságokkal futtassa a parancsot.

* A Azure Portal, Azure PowerShell, Azure CLI vagy HDInsight .NET SDK használatával használható.

A fürt megőrzi a futtatott összes parancsfájl előzményeit. Az előzmények segítenek az előléptetési vagy lefokozási műveletekhez szükséges parancsfájlok AZONOSÍTÓjának megkeresésében.

> [!IMPORTANT]  
> Nincs automatikus módszer a parancsfájl által végrehajtott módosítások visszavonására. Manuálisan visszafordíthatja a módosításokat, vagy megadhat egy parancsfájlt, amely visszafordítja őket.

### <a name="script-action-in-the-cluster-creation-process"></a>Parancsfájl művelet a fürt létrehozási folyamatában

A fürt létrehozása során használt parancsfájl-műveletek némileg eltérnek egy meglévő fürtön futtatott parancsfájl-műveletektől:

* A szkript automatikusan megmarad.

* A parancsfájl hibája miatt a fürt létrehozási folyamata sikertelen lehet.

A következő ábra azt szemlélteti, hogy mikor futnak parancsfájl-művelet a létrehozási folyamat során:

![HDInsight-fürt testreszabása és szakaszai a fürt létrehozása során][img-hdi-cluster-states]

A parancsfájl a HDInsight konfigurálása közben fut. A parancsfájl párhuzamosan fut a fürt összes megadott csomópontján. A csomópontok gyökérszintű jogosultságokkal futnak.

Olyan műveleteket hajthat végre, mint a szolgáltatások leállítása és elindítása, beleértve a Apache Hadoop kapcsolódó szolgáltatásokat is. Ha leállítja a szolgáltatásokat, győződjön meg arról, hogy a Ambari és más Hadoop kapcsolódó szolgáltatások futnak a parancsfájl befejeződése előtt. Ezek a szükséges szolgáltatások határozzák meg a fürt állapotát és állapotát a létrehozásuk során.

A fürt létrehozása során egyszerre több parancsfájl-műveletet is használhat. Ezeket a parancsfájlokat a megadott sorrendben hívja meg a rendszer.

> [!IMPORTANT]  
> A parancsfájl-műveleteknek 60 percen belül be kell fejeződnie, vagy időtúllépéssel kell rendelkezniük. A fürt kiépítés során a parancsfájl egyidejűleg fut más beállítási és konfigurációs folyamatokkal. Az erőforrások, például a CPU-idő vagy a hálózati sávszélesség versenye miatt a szkript hosszabb időt vehet igénybe, mint a fejlesztői környezetben.
>
> A parancsfájl futtatásához szükséges idő minimalizálásához Kerülje a feladatok, például a forrásból származó alkalmazások letöltését és fordítását. Alkalmazások előfordítása és a bináris fájl tárolása az Azure Storage-ban.

### <a name="script-action-on-a-running-cluster"></a>Parancsfájl-művelet egy futó fürtön

Egy már futó fürt parancsfájl-meghibásodása nem eredményez automatikusan hibát, mert a fürt hibás állapotra vált. Egy parancsfájl befejeződése után a fürtnek egy futó állapotba kell visszatérnie. Ha a fürtön futó állapot is fut, előfordulhat, hogy a sikertelen parancsfájl megszakadt. Előfordulhat például, hogy egy parancsfájl törli a fürt számára szükséges fájlokat.

A parancsfájlok műveletei gyökérszintű jogosultságokkal futnak. Győződjön meg arról, hogy mit tesz a parancsfájl a fürtön való alkalmazása előtt.

Ha parancsfájlt alkalmaz egy fürtre, a fürt állapota a **futtatástól** az **elfogadás**értékre változik. Ezután megváltoztatja a **HDInsight konfigurációját** , és végül visszatér a sikeres parancsfájlok **futtatásához** . A parancsfájl-állapot naplózása a parancsfájl műveleti előzményeiben történik. Ez az információ jelzi, hogy a parancsfájl sikeres vagy sikertelen volt-e. A `Get-AzHDInsightScriptActionHistory` PowerShell-parancsmag például egy parancsfájl állapotát jeleníti meg. Az alábbi szöveghez hasonló adatokat ad vissza:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Ha megváltoztatja a fürt felhasználóját, a rendszergazdát és a jelszót a fürt létrehozása után, akkor a fürtön futó parancsfájlok műveletei sikertelenek lehetnek. Ha olyan megőrzött parancsfájl-művelettel rendelkezik, amely a munkavégző csomópontokat célozza meg, akkor előfordulhat, hogy ezek a parancsfájlok meghiúsulnak a fürt skálázásakor.

## <a name="example-script-action-scripts"></a>Példa parancsfájl-műveleti parancsfájlokra

A parancsfájl műveleti parancsfájljai a következő segédprogramok használatával használhatók:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

A HDInsight parancsfájlokat biztosít a következő összetevők telepítéséhez a HDInsight-fürtökön:

| Name | Script |
| --- | --- |
| Azure Storage-fiók hozzáadása |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Lásd: [további Storage-fiókok hozzáadása a HDInsight](hdinsight-hadoop-add-storage.md). |
| A Hue telepítése |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Lásd: [a Hue telepítése és használata a HDInsight Hadoop-fürtökön](hdinsight-hadoop-hue-linux.md). |
| Betöltési struktúra kódtárai |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Lásd: [egyéni Apache Hive-kódtárak hozzáadása a HDInsight-fürt létrehozásakor](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Parancsfájl-művelet a fürt létrehozása során

Ez a szakasz ismerteti a HDInsight-fürtök létrehozásakor használható parancsfájl-műveletek különböző módszereit.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Parancsfájl-művelet használata a fürt létrehozása során a Azure Portal

1. Hozzon létre egy fürtöt a HDInsight-ben [a Linux-alapú fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md)című témakörben leírtak szerint a Azure Portal használatával. A **konfiguráció + díjszabás** lapon válassza a **+ parancsfájl hozzáadása műveletet**.

    ![Azure Portal-fürt parancsfájljának művelete](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Az előkészített parancsfájl kiválasztásához használja a __parancsfájl kiválasztása__ bejegyzést. Egyéni parancsfájl használatához válassza az __Egyéni__lehetőséget. Ezután adja meg a parancsfájl __nevét__ és a __bash parancsfájl URI-ját__ .

    ![Parancsfájl hozzáadása a parancsfájl kiválasztása űrlapon](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Az alábbi táblázat az űrlap elemeit ismerteti:

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl kiválasztása | Saját parancsfájl használatához válassza az __Egyéni__lehetőséget. Ellenkező esetben válassza ki a megadott parancsfájlok egyikét. |
    | Name |Adja meg a parancsfájl művelet nevét. |
    | Bash-parancsfájl URI-ja |Adja meg a parancsfájl URI-JÁT. |
    | Head/Worker/ZooKeeper |Adja meg azokat a csomópontokat, amelyeken a parancsfájl fut: **Head**, **Worker**vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha azt a parancsfájl megköveteli. |

    A parancsfájl megtartása __művelet__ bejegyzésével győződjön meg arról, hogy a parancsfájlt a rendszer a skálázási műveletek során alkalmazza.

1. Válassza a __Létrehozás__ lehetőséget a parancsfájl mentéséhez. Ezt követően a __+ Submit New__ paranccsal adhat hozzá újabb parancsfájlokat.

    ![HDInsight több parancsfájl-művelet](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Ha elkészült a parancsfájlok hozzáadásával, térjen vissza a **konfiguráció + díjszabás** lapra.

1. Fejezze be a fürt többi létrehozási lépését a szokásos módon.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Parancsfájl-művelet használata Azure Resource Manager sablonokból

A parancsfájl-műveletek Azure Resource Manager sablonokkal is használhatók. Példa: [HDInsight Linux-fürt létrehozása és parancsfájl-művelet futtatása](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Ebben a példában a parancsfájl-műveletet a következő kód használatával adja hozzá a rendszer:

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

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Parancsfájl-művelet használata a fürt létrehozásakor Azure PowerShell

Ebben a szakaszban az [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) parancsmaggal hozhat létre parancsfájlokat a fürt testreszabásához. A Kezdés előtt győződjön meg arról, hogy a Azure PowerShell telepítését és konfigurálását végzi. A PowerShell-parancsok használatához az az [modulra](https://docs.microsoft.com/powershell/azure/)van szükség.

A következő szkript bemutatja, hogyan alkalmazhat egy parancsfájl-műveletet a fürt létrehozásakor a PowerShell használatával:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

A fürt létrehozása előtt több percet is igénybe vehet.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Parancsfájl-művelet használata fürt létrehozásakor a HDInsight .NET SDK-ból

A HDInsight .NET SDK olyan ügyféloldali kódtárakat biztosít, amelyek megkönnyítik a .NET-alkalmazások HDInsight való együttműködését. A kód minta esetében lásd: [parancsfájlok műveletei](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Parancsfájl művelete futó fürthöz

Ez a szakasz azt ismerteti, hogyan alkalmazhat parancsfájl-műveleteket egy futó fürtön.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Parancsfájl-művelet alkalmazása futó fürtre a Azure Portal

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a fürtöt.

1. Az alapértelmezett nézet **Beállítások**területén válassza a **parancsfájlok műveletek**elemet.

1. A **parancsfájl-műveletek** lap tetején válassza a **+ Küldés új**lehetőséget.

    ![Parancsfájl hozzáadása egy futó fürthöz](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Az előkészített parancsfájl kiválasztásához használja a __parancsfájl kiválasztása__ bejegyzést. Egyéni parancsfájl használatához válassza az __Egyéni__lehetőséget. Ezután adja meg a parancsfájl __nevét__ és a __bash parancsfájl URI-ját__ .

    ![Parancsfájl hozzáadása a parancsfájl kiválasztása űrlapon](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Az alábbi táblázat az űrlap elemeit ismerteti:

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl kiválasztása | Saját parancsfájl használatához válassza az __Egyéni__lehetőséget. Ellenkező esetben válasszon egy megadott parancsfájlt. |
    | Name |Adja meg a parancsfájl művelet nevét. |
    | Bash-parancsfájl URI-ja |Adja meg a parancsfájl URI-JÁT. |
    | Head/Worker/Zookeeper |Adja meg azokat a csomópontokat, amelyeken a parancsfájl fut: **Head**, **Worker**vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha azt a parancsfájl megköveteli. |

    A parancsfájl megtartása __művelettel__ ellenőrizze, hogy a parancsfájlt a rendszer a skálázási műveletek során alkalmazza-e.

1. Végül a **Létrehozás** gombra kattintva alkalmazza a parancsfájlt a fürtre.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Parancsfájl-művelet alkalmazása futó fürtön Azure PowerShell

A PowerShell-parancsok használatához az az [modulra](https://docs.microsoft.com/powershell/azure/)van szükség. Az alábbi példa bemutatja, hogyan alkalmazhat egy parancsfájl-műveletet egy futó fürtre:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

A művelet befejeződése után az alábbihoz hasonló adatokat kap:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Parancsfájl-művelet alkalmazása futó fürtre az Azure CLI-vel

Mielőtt elkezdené, győződjön meg róla, hogy telepíti és konfigurálja az Azure CLI-t. Győződjön meg arról, hogy a legújabb verzióval rendelkezik. További információ: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Hitelesítés az Azure-előfizetésében:

    ```azurecli
    az login
    ```

1. Parancsfájl-művelet alkalmazása futó fürtön:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Érvényes szerepkörök:,, `headnode` `workernode` `zookeepernode` , `edgenode` . Ha a parancsfájlt több csomópontra is alkalmazni kívánja, a szerepköröket szóközzel kell elválasztani. Például: `--roles headnode workernode`.

    A szkript megőrzése érdekében adja hozzá a következőt: `--persist-on-success` . A parancsfájlt később is megtarthatja a használatával `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Parancsfájl-művelet alkalmazása futó fürtre REST API használatával

Lásd: [Cluster REST API az Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Parancsfájl-művelet alkalmazása futó fürtre a HDInsight .NET SDK-val

Ha a .NET SDK-t a parancsfájlok fürtön való alkalmazására szeretné használni, lásd: [parancsfájl-művelet alkalmazása egy futó Linux-alapú HDInsight-fürtön](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Megtekintheti az előzményeket, és előléptetheti és lefokozni a parancsfájlok műveleteit

### <a name="the-azure-portal"></a>Azure Portal

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a fürtöt.

1. Az alapértelmezett nézet **Beállítások**területén válassza a **parancsfájlok műveletek**elemet.

1. A fürt parancsfájljainak előzményei a parancsfájl-műveletek szakaszban jelennek meg. Ez az információ tartalmazza a megőrzött parancsfájlok listáját. A következő képernyőfelvételen látható, hogy a Solr parancsfájlt futtattak ezen a fürtön. A képernyőképen nem láthatók megőrzött parancsfájlok.

    ![A portál parancsfájljainak műveletei elküldik az előzményeket](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Válasszon ki egy parancsfájlt az előzményekből a parancsfájl **Tulajdonságok** szakaszának megjelenítéséhez. A képernyő felső részén újra futtathatja a szkriptet, vagy előléptetheti azt.

    ![Parancsfájl-műveletek tulajdonságai – előléptetés](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. A műveletek végrehajtásához a Scripting Actions szakaszban a három pont, a. **..** lehetőség is választható.

    ![Megőrzött parancsfájl-műveletek törlése](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| parancsmag | Függvény |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Információk beolvasása a megőrzött parancsfájl-műveletekről. Ez a parancsmag nem vonja vissza a parancsfájlok által végzett műveleteket, hanem csak a megőrzött jelzőt távolítja el.|
| `Get-AzHDInsightScriptActionHistory` |A fürtre alkalmazott parancsfájl-műveletek előzményeinek vagy egy adott parancsfájl részleteinek beolvasása. |
| `Set-AzHDInsightPersistedScriptAction` |`ad hoc`Parancsfájl-művelet előléptetése egy megőrzött parancsfájl-művelethez. |
| `Remove-AzHDInsightPersistedScriptAction` |Megőrzött parancsfájl műveletének lefokozása egy `ad hoc` művelethez. |

Az alábbi példa azt mutatja be, hogyan lehet a parancsmagokat a parancsfájlok előléptetésére és lefokozására használni.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Parancs | Leírás |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Törli a fürt megadott megőrzött parancsfájl-műveletét. Ez a parancs nem vonja vissza a parancsfájlok által végzett műveleteket, hanem csak a megőrzött jelzőt távolítja el.|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Parancsfájl-műveletek végrehajtása a megadott HDInsight-fürtön.|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Felsorolja a megadott fürt összes megőrzött parancsfájl-műveletét. |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Felsorolja a megadott fürt összes parancsfájljának végrehajtási előzményeit.|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|A megadott ad-hoc parancsfájl végrehajtásának elősegítése a megőrzött szkriptek számára.|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Lekérdezi a parancsfájl végrehajtásának részleteit az adott parancsfájl-végrehajtási AZONOSÍTÓhoz.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Ha például a .NET SDK-t használja a parancsfájlok egy fürtből való lekéréséhez, előléptetéséhez vagy lefokozásához, lásd: [parancsfájl-művelet alkalmazása egy futó Linux-alapú HDInsight-fürtön](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Ez a példa azt is bemutatja, hogyan telepíthet egy HDInsight alkalmazást a .NET SDK használatával.

## <a name="next-steps"></a>További lépések

* [Parancsfájl-műveleti parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [További tárterület hozzáadása egy HDInsight-fürthöz](hdinsight-hadoop-add-storage.md)
* [Szkriptműveletek hibaelhárítása](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Szakaszok a fürt létrehozása során"
