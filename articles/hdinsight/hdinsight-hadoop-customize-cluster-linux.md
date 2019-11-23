---
title: Azure HDInsight-fürtök testreszabása parancsfájl-műveletek használatával
description: Egyéni összetevőket adhat hozzá a Linux-alapú HDInsight-fürtökhöz parancsfájl-műveletek használatával. A parancsfájl-műveletek olyan bash-parancsfájlok, amelyek segítségével testre szabhatja a fürtkonfiguráció beállításait, illetve további szolgáltatásokat és segédprogramokat (például Hue, Solr vagy R) adhat hozzá.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 16b0fdcbae51b30e14fbf7ea4d98699dfaf19804
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035734"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Azure HDInsight-fürtök testreszabása parancsfájl-műveletek használatával

Az Azure HDInsight egy **parancsfájl-műveletek** nevű konfigurációs módszert biztosít, amely egyéni parancsfájlokat hív meg a fürt testreszabásához. Ezek a parancsfájlok további összetevők telepítésére és a konfigurációs beállítások módosítására szolgálnak. Parancsfájl-műveletek a fürt létrehozása során vagy után is használhatók.

A parancsfájlok műveletei az Azure Marketplace-en is HDInsight-alkalmazásként közzétehetők. A HDInsight alkalmazásokkal kapcsolatos további információkért lásd: [HDInsight-alkalmazás közzététele az Azure Marketplace-](hdinsight-apps-publish-applications.md)en.

## <a name="permissions"></a>Engedélyek

Tartományhoz csatlakoztatott HDInsight-fürt esetén két Apache Ambari engedély szükséges, ha parancsfájl-műveleteket használ a fürthöz:

* **AMBARI.\_egyéni\_parancs futtatása**. A Ambari rendszergazdai szerepkör alapértelmezés szerint rendelkezik ezzel az engedéllyel.
* **Fürt.\_egyéni\_parancs futtatása**. Alapértelmezés szerint a HDInsight-fürt rendszergazdája és a Ambari rendszergazdája is rendelkezik ezzel az engedéllyel.

Az engedélyek tartományhoz csatlakoztatott HDInsight való használatáról további információért lásd: [HDInsight-fürtök kezelése Enterprise Security Packagesal](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Hozzáférés-vezérlés

Ha nem Ön az Azure-előfizetés rendszergazdája vagy tulajdonosa, a fióknak legalább közreműködői hozzáféréssel kell rendelkeznie a HDInsight-fürtöt tartalmazó erőforráscsoporthoz.

Ha HDInsight-fürtöt hoz létre, az Azure-előfizetéshez legalább közreműködői hozzáféréssel rendelkező személynek előzőleg regisztrálnia kell a szolgáltatót a HDInsight. A szolgáltató regisztrálása akkor történik meg, ha egy olyan felhasználó, aki Közreműködői hozzáféréssel rendelkezik az előfizetéshez, először hoz létre erőforrást az előfizetésben. Az erőforrás létrehozása nélkül is elvégezhető, ha [a REST használatával regisztrálja a szolgáltatót](https://msdn.microsoft.com/library/azure/dn790548.aspx).

További információ a hozzáférés-kezeléssel kapcsolatban:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>A parancsfájl-műveletek ismertetése

A parancsfájl művelete egy HDInsight-fürt csomópontjain futó bash-parancsfájl. A parancsfájl-műveletek jellemzői és funkciói a következők:

* Olyan URI-n kell tárolni, amely elérhető a HDInsight-fürtből. A következő tárolási helyszínek lehetségesek:

    * Normál fürtök esetén:

      * ADLS Gen1: az egyszerű szolgáltatásnak a HDInsight által használt Data Lake Storage eléréséhez olvasási hozzáféréssel kell rendelkeznie a parancsfájlhoz. A Data Lake Storage Gen1ban tárolt parancsfájlok URI-formátuma `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Egy Azure Storage-fiókban található blob, amely a HDInsight-fürthöz tartozó elsődleges vagy további Storage-fiók. A HDInsight mindkét típusú Storage-fiókhoz hozzáférést kap a fürt létrehozása során.

        > [!IMPORTANT]  
        > Ne forgassa el ezen az Azure Storage-fiókon a Storage-kulcsot, mert az azt követő parancsfájl-műveleteket nem sikerül.

      * Egy nyilvános fájlmegosztás szolgáltatás http://elérési utakon keresztül érhető el. Ilyenek például az Azure Blob, a GitHub, a OneDrive.

        Az URI-k például a [parancsfájl műveleti parancsfájljai](#example-script-action-scripts)című részben olvashatók.

     * ESP-vel rendelkező fürtök esetén:

         * A wasb://vagy a wasbs://vagy a http [s]://URI-k támogatottak.

* Csak bizonyos csomópont-típusok futtatására korlátozható. Ilyenek például a fő csomópontok vagy a munkavégző csomópontok.

* Maradhat vagy alkalmi.

    A megőrzött parancsfájlok a fürthöz a skálázási műveletekkel hozzáadott új munkavégző csomópontok testreszabására szolgálnak. A megőrzött parancsfájlok a skálázási műveletek végrehajtásakor is alkalmazhatják a másik csomópont-típus módosításait. Ilyen például egy fő csomópont.

  > [!IMPORTANT]  
  > A megőrzött parancsfájl-műveleteknek egyedi névvel kell rendelkezniük.

    Az alkalmi parancsfájlok nem maradnak meg. Nem vonatkoznak a fürthöz a parancsfájl futtatása után hozzáadott munkavégző csomópontokra. Ezután előléptetheti az ad hoc parancsfájlt egy megőrzött parancsfájlba, vagy lefokozni lehet a megőrzött szkriptet egy ad hoc parancsfájlba.

  > [!IMPORTANT]  
  > A fürt létrehozásakor használt parancsfájl-műveleteket a rendszer automatikusan megőrzi.
  >
  > A meghiúsult parancsfájlok nem maradnak meg, még akkor is, ha kifejezetten arra utalnak, hogy legyenek.

* Elfogadhatja a parancsfájl által a végrehajtás során használt paramétereket.

* A fürt csomópontjain gyökérszintű jogosultságokkal futtassa a parancsot.

* A Azure Portal, a Azure PowerShell, a klasszikus Azure CLI vagy a HDInsight .NET SDK használatával használható.

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

> [!NOTE]  
> Olyan műveleteket hajthat végre, mint a szolgáltatások leállítása és elindítása, beleértve a Apache Hadoop kapcsolódó szolgáltatásokat is. Ha leállítja a szolgáltatásokat, győződjön meg arról, hogy a Ambari szolgáltatás és más Hadoop kapcsolódó szolgáltatások futnak a parancsfájl befejeződése előtt. Ezek a szolgáltatások a fürt állapotának és állapotának sikeres megállapításához szükségesek a létrehozása során.

A fürt létrehozása során egyszerre több parancsfájl-műveletet is használhat. Ezeket a parancsfájlokat a megadott sorrendben hívja meg a rendszer.

> [!IMPORTANT]  
> A parancsfájl-műveleteknek 60 percen belül be kell fejeződnie, vagy időtúllépéssel kell rendelkezniük. A fürt kiépítés során a parancsfájl egyidejűleg fut más beállítási és konfigurációs folyamatokkal. Az erőforrások, például a CPU-idő vagy a hálózati sávszélesség versenye miatt a szkript hosszabb időt vehet igénybe, mint a fejlesztői környezetben.
>
> A parancsfájl futtatásához szükséges idő minimalizálásához Kerülje a feladatok, például a forrásból származó alkalmazások letöltését és fordítását. Alkalmazások előfordítása és a bináris fájl tárolása az Azure Storage-ban.

### <a name="script-action-on-a-running-cluster"></a>Parancsfájl-művelet egy futó fürtön

Egy már futó fürtön futtatott parancsfájl meghibásodása nem okoz automatikusan hibát, mert a fürt meghibásodott állapotra vált. Egy parancsfájl befejeződése után a fürtnek egy futó állapotba kell visszatérnie.

> [!IMPORTANT]  
> Ha a fürtön futó állapot is fut, előfordulhat, hogy a sikertelen parancsfájl megszakadt. Előfordulhat például, hogy egy parancsfájl törli a fürt számára szükséges fájlokat.
>
> A parancsfájlok műveletei gyökérszintű jogosultságokkal futnak. Győződjön meg arról, hogy tisztában van azzal, hogy mit tesz a parancsfájl a fürtön való alkalmazása előtt.

Ha parancsfájlt alkalmaz egy fürtre, a fürt állapota a **futtatástól** az **elfogadás**értékre változik. Ezután megváltoztatja a **HDInsight konfigurációját** , és végül visszatér a sikeres parancsfájlok **futtatásához** . A parancsfájl-állapot naplózása a parancsfájl műveleti előzményeiben történik. Ez az információ jelzi, hogy a parancsfájl sikeres vagy sikertelen volt-e. A `Get-AzHDInsightScriptActionHistory` PowerShell-parancsmag például egy parancsfájl állapotát jeleníti meg. Az alábbi szöveghez hasonló adatokat ad vissza:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Ha megváltoztatja a fürt felhasználóját, a rendszergazdát és a jelszót a fürt létrehozása után, akkor a fürtön futó parancsfájlok műveletei sikertelenek lehetnek. Ha olyan megőrzött parancsfájl-művelettel rendelkezik, amely a munkavégző csomópontokat célozza meg, akkor előfordulhat, hogy ezek a parancsfájlok meghiúsulnak a fürt skálázásakor.

## <a name="example-script-action-scripts"></a>Példa parancsfájl-műveleti parancsfájlokra

A parancsfájl műveleti parancsfájljai a következő segédprogramok használatával használhatók:

* Az Azure Portal
* Azure PowerShell
* A klasszikus Azure CLI
* An méretű HDInsight .NET SDK

A HDInsight parancsfájlokat biztosít a következő összetevők telepítéséhez a HDInsight-fürtökön:

| Name (Név) | Parancsfájl |
| --- | --- |
| Azure Storage-fiók hozzáadása |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Lásd: [további Storage-fiókok hozzáadása a HDInsight](hdinsight-hadoop-add-storage.md). |
| A Hue telepítése |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Lásd: [a Hue telepítése és használata a HDInsight Hadoop-fürtökön](hdinsight-hadoop-hue-linux.md). |
| A Giraph telepítése |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Lásd: [az Apache Giraph telepítése a HDInsight Hadoop-fürtökön](hdinsight-hadoop-giraph-install-linux.md). |
| Betöltési struktúra kódtárai |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Lásd: [egyéni Apache Hive-kódtárak hozzáadása a HDInsight-fürt létrehozásakor](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Parancsfájl-művelet használata a fürt létrehozása során

Ez a szakasz ismerteti a HDInsight-fürtök létrehozásakor használható parancsfájl-műveletek különböző módszereit.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Parancsfájl-művelet használata a fürt létrehozása során a Azure Portal

1. Hozzon létre egy fürtöt a HDInsight-ben [a Linux-alapú fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-portal.md)című témakörben leírtak szerint a Azure Portal használatával. A fürt létrehozása során a 6. lépéssel, a **parancsfájlok műveleteivel**érkezik meg. Navigáljon a **választható** >  **+ új beküldése**elemre.

    ![Azure Portal-fürt parancsfájljának művelete](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-classic-script-action.png)

1. Az előkészített parancsfájl kiválasztásához használja a __parancsfájl kiválasztása__ bejegyzést. Egyéni parancsfájl használatához válassza az __Egyéni__lehetőséget. Ezután adja meg a parancsfájl __nevét__ és a __bash parancsfájl URI-ját__ .

    ![Parancsfájl hozzáadása a parancsfájl kiválasztása űrlapon](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Az alábbi táblázat az űrlap elemeit ismerteti:

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl kiválasztása | Saját parancsfájl használatához válassza az __Egyéni__lehetőséget. Ellenkező esetben válassza ki a megadott parancsfájlok egyikét. |
    | Name (Név) |Adja meg a parancsfájl művelet nevét. |
    | Bash-parancsfájl URI-ja |Adja meg a parancsfájl URI-JÁT. |
    | Head/Worker/ZooKeeper |Adja meg azokat a csomópontokat, amelyeken a parancsfájl fut: **Head**, **Worker**vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha azt a parancsfájl megköveteli. |

    A parancsfájl megtartása __művelet__ bejegyzésével győződjön meg arról, hogy a parancsfájlt a rendszer a skálázási műveletek során alkalmazza.

1. Válassza a __Létrehozás__ lehetőséget a parancsfájl mentéséhez. Ezt követően a __+ Submit New__ paranccsal adhat hozzá újabb parancsfájlokat.

    ![HDInsight több parancsfájl-művelet](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Ha elkészült a parancsfájlok hozzáadásával, kattintson a __kiválasztás__ gombra, majd a __Tovább gombra a__ __fürt összegzése__ szakasz folytatásához.

1. A fürt létrehozásához kattintson a __Létrehozás__ elemre a __fürt összesítésének__ kiválasztása listából.

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

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Parancsfájl-művelet használata a fürt létrehozásakor Azure PowerShell

Ebben a szakaszban az [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) parancsmaggal hozhat létre parancsfájlokat a fürt testreszabásához. A Kezdés előtt győződjön meg arról, hogy a Azure PowerShell telepítését és konfigurálását végzi. A PowerShell-parancsok használatához az az [modulra](https://docs.microsoft.com/powershell/azure/overview)van szükség.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő szkript bemutatja, hogyan alkalmazhat egy parancsfájl-műveletet a fürt létrehozásakor a PowerShell használatával:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

A fürt létrehozása előtt több percet is igénybe vehet.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Parancsfájl-művelet használata fürt létrehozásakor a HDInsight .NET SDK-ból

A HDInsight .NET SDK olyan ügyféloldali kódtárakat biztosít, amelyek megkönnyítik a .NET-alkalmazások HDInsight való együttműködését. A kód minta: [Linux-alapú fürtök létrehozása a HDInsight-ben a .net SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Parancsfájl-művelet alkalmazása futó fürtre

Ez a szakasz azt ismerteti, hogyan alkalmazhat parancsfájl-műveleteket egy futó fürtön.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Parancsfájl-művelet alkalmazása futó fürtre a Azure Portal

Ugrás a [Azure Portalra](https://portal.azure.com):

1. A bal oldali menüben navigáljon az **összes szolgáltatás** >  **Analytics** > **HDInsight-fürtök**elemre.

1. Válassza ki a fürtöt a listából, amely megnyitja az alapértelmezett nézetet.

1. Az alapértelmezett nézet **Beállítások**területén válassza a **parancsfájlok műveletek**elemet.

1. A **parancsfájl-műveletek** lap tetején válassza a **+ Küldés új**lehetőséget.

    ![Parancsfájl hozzáadása egy futó fürthöz](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Az előkészített parancsfájl kiválasztásához használja a __parancsfájl kiválasztása__ bejegyzést. Egyéni parancsfájl használatához válassza az __Egyéni__lehetőséget. Ezután adja meg a parancsfájl __nevét__ és a __bash parancsfájl URI-ját__ .

    ![Parancsfájl hozzáadása a parancsfájl kiválasztása űrlapon](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Az alábbi táblázat az űrlap elemeit ismerteti:

    | Tulajdonság | Érték |
    | --- | --- |
    | Parancsfájl kiválasztása | Saját parancsfájl használatához válassza az __Egyéni__lehetőséget. Ellenkező esetben válasszon egy megadott parancsfájlt. |
    | Name (Név) |Adja meg a parancsfájl művelet nevét. |
    | Bash-parancsfájl URI-ja |Adja meg a parancsfájl URI-JÁT. |
    | Head/Worker/Zookeeper |Adja meg azokat a csomópontokat, amelyeken a parancsfájl fut: **Head**, **Worker**vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha azt a parancsfájl megköveteli. |

    A parancsfájl megtartása __művelettel__ ellenőrizze, hogy a parancsfájlt a rendszer a skálázási műveletek során alkalmazza-e.

1. Végül a **Létrehozás** gombra kattintva alkalmazza a parancsfájlt a fürtre.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Parancsfájl-művelet alkalmazása futó fürtön Azure PowerShell

A PowerShell-parancsok használatához az az [modulra](https://docs.microsoft.com/powershell/azure/overview)van szükség.

Az alábbi példa bemutatja, hogyan alkalmazhat egy parancsfájl-műveletet egy futó fürtre:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

A művelet befejeződése után az alábbihoz hasonló adatokat kap:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Parancsfájl-művelet alkalmazása futó fürtre az Azure CLI-vel

Mielőtt elkezdené, győződjön meg róla, hogy telepíti és konfigurálja az Azure CLI-t. További információ: [a klasszikus Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Váltson Azure Resource Manager módra:

    ```bash
    azure config mode arm
    ```

2. Hitelesítés az Azure-előfizetésében:

    ```bash
    azure login
    ```

3. Parancsfájl-művelet alkalmazása futó fürtön:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Ha kihagyja a parancs paramétereit, a rendszer kérni fogja őket. Ha a `-u` paraméterrel megadott parancsfájl paramétereket fogad el, megadhatja azokat a `-p` paraméter használatával.

    Az érvényes csomópont-típusok a következők: `headnode`, `workernode`és `zookeeper`. Ha a parancsfájlt több csomópontra is alkalmazni szeretné, adja meg a pontosvesszővel elválasztott típusokat `;`. Például: `-n headnode;workernode`.

    A szkript megőrzése érdekében adja hozzá a `--persistOnSuccess`. A parancsfájlt később is megtarthatja `azure hdinsight script-action persisted set`használatával.

    A feladatok befejezése után a következő szöveghez hasonló kimenetet kap:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Parancsfájl-művelet alkalmazása futó fürtre REST API használatával

Lásd: [Cluster REST API az Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Parancsfájl-művelet alkalmazása futó fürtre a HDInsight .NET SDK-val

Ha a .NET SDK-t a parancsfájlok fürtön való alkalmazására szeretné használni, lásd: [parancsfájl-művelet alkalmazása egy futó Linux-alapú HDInsight-fürtön](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Megtekintheti az előzményeket, és előléptetheti és lefokozni a parancsfájlok műveleteit

### <a name="the-azure-portal"></a>Az Azure Portal

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben navigáljon az **összes szolgáltatás** > **Analytics** > **HDInsight-fürtök**elemre.

1. Válassza ki a fürtöt a listából, amely megnyitja az alapértelmezett nézetet.

1. Az alapértelmezett nézet **Beállítások**területén válassza a **parancsfájlok műveletek**elemet.

1. A fürt parancsfájljainak előzményei a parancsfájl-műveletek szakaszban jelennek meg. Ez az információ tartalmazza a megőrzött parancsfájlok listáját. A következő képernyőfelvételen látható, hogy a Solr parancsfájlt futtattak ezen a fürtön. A képernyőképen nem láthatók megőrzött parancsfájlok.

    ![A portál parancsfájljainak műveletei elküldik az előzményeket](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Válasszon ki egy parancsfájlt az előzményekből a parancsfájl **Tulajdonságok** szakaszának megjelenítéséhez. A képernyő felső részén újra futtathatja a szkriptet, vagy előléptetheti azt.

    ![Parancsfájl-műveletek tulajdonságai – előléptetés](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. A műveletek végrehajtásához a Scripting Actions (parancsfájl-műveletek) szakaszának jobb oldalán található három pontot is kiválaszthatja **.**

    ![Megőrzött parancsfájl-műveletek törlése](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| parancsmag | Függvény |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Információk beolvasása a megőrzött parancsfájl-műveletekről. |
| `Get-AzHDInsightScriptActionHistory` |A fürtre alkalmazott parancsfájl-műveletek előzményeinek vagy egy adott parancsfájl részleteinek beolvasása. |
| `Set-AzHDInsightPersistedScriptAction` |Ad hoc parancsfájl-művelet előléptetése egy megőrzött parancsfájl-művelethez. |
| `Remove-AzHDInsightPersistedScriptAction` |Megőrzött parancsfájl-művelet lefokozása ad hoc művelethez. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` nem vonja vissza a parancsfájlok által végrehajtott műveleteket. Ez a parancsmag csak a megőrzött jelzőt távolítja el.

Az alábbi példa azt mutatja be, hogyan lehet a parancsmagokat a parancsfájlok előléptetésére és lefokozására használni.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>A klasszikus Azure CLI

| parancsmag | Függvény |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |A megőrzött parancsfájl-műveletek listájának beolvasása. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Információ lekérése egy adott megőrzött parancsfájl-műveletről. |
| `azure hdinsight script-action history list <clustername>` |A fürtre alkalmazott parancsfájl-műveletek előzményeinek beolvasása. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Információ lekérése egy adott parancsfájl-műveletről. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Ad hoc parancsfájl-művelet előléptetése egy megőrzött parancsfájl-művelethez. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Megőrzött parancsfájl-művelet lefokozása ad hoc művelethez. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` nem vonja vissza a parancsfájlok által végrehajtott műveleteket. Ez a parancsmag csak a megőrzött jelzőt távolítja el.

### <a name="the-hdinsight-net-sdk"></a>A HDInsight .NET SDK

Ha például a .NET SDK-t használja a parancsfájlok egy fürtből való lekéréséhez, előléptetéséhez vagy lefokozásához, lásd: [parancsfájl-művelet alkalmazása egy futó Linux-alapú HDInsight-fürtön](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Ez a példa azt is bemutatja, hogyan telepíthet egy HDInsight alkalmazást a .NET SDK használatával.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatás a Apache Hadoop által létrehozott nyílt forráskódú technológiák ökoszisztémáját használja. A Microsoft Azure a nyílt forráskódú technológiák általános támogatását biztosítja. További információkért lásd az [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/) **támogatásának hatókörét** ismertető szakaszt. A HDInsight szolgáltatás további szintű támogatást biztosít a beépített összetevőkhöz.

A HDInsight szolgáltatás két különböző típusú nyílt forráskódú összetevőből áll:

* **Beépített összetevők**. Ezek az összetevők előre telepítve vannak a HDInsight-fürtökön, és biztosítják a fürt alapvető funkcióit. A következő összetevők tartoznak ehhez a kategóriához:

  * [Apache HADOOP fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Erőforráskezelő.
  * A kaptár lekérdezési nyelvének [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/).

    A fürt összetevőinek teljes listája elérhető a HDInsight-ben [elérhető Apache Hadoop-összetevők és-verziók közül?](hdinsight-component-versioning.md)

* **Egyéni összetevők**. A fürt felhasználója a munkaterheléshez bármilyen, a Közösségben elérhető vagy Ön által létrehozott összetevőt telepíthet vagy használhat.

> [!WARNING]  
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak. Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani ezeket az összetevőket érintő problémákat.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldása érdekében. Előfordulhat, hogy a Microsoft ügyfélszolgálata fel tudja oldani a problémát. Vagy kérheti, hogy a nyílt forráskódú technológiákhoz elérhető csatornákat is igénybe vehet, amelyekben az adott technológia mélyreható szaktudása található. Számos közösségi webhely használható. Ilyenek például a HDInsight és a [stack overflow](https://stackoverflow.com) [MSDN-fóruma](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) .
>
> Az Apache-projektek az [Apache webhelyén](https://apache.org)is rendelkeznek Project-webhelyekkel. Ilyen például a [Hadoop](https://hadoop.apache.org/).

A HDInsight szolgáltatás számos módszert kínál az egyéni összetevők használatára. Ugyanez a támogatási szint érvényes, függetlenül attól, hogy az összetevő hogyan használható vagy van telepítve a fürtön. Az alábbi lista az egyéni összetevők HDInsight-fürtökön használt leggyakoribb módszereit ismerteti:

1. A **feladatok beküldése**. Az egyéni összetevőket végrehajtó vagy használó Hadoop vagy más típusú feladatok elküldhető a fürtbe.

2. **Fürt testreszabása**. A fürt létrehozása során megadhat további beállításokat és a fürtcsomópontokon telepített egyéni összetevőket.

3. **Minták**. A népszerű egyéni összetevők esetében a Microsoft és mások példákat is biztosítanak az összetevők HDInsight-fürtökön való használatára. Ezeket a mintákat támogatás nélkül biztosítjuk.

## <a name="troubleshooting"></a>Hibaelhárítás

A Ambari webes felhasználói felületén megtekintheti a parancsfájlok műveletei által naplózott információkat. Ha a parancsfájl meghibásodik a fürt létrehozása során, a naplók a fürthöz társított alapértelmezett Storage-fiókban is elérhetők. Ez a szakasz azt ismerteti, hogyan lehet lekérdezni a naplókat mindkét lehetőség használatával.

### <a name="the-apache-ambari-web-ui"></a>Az Apache Ambari webes felhasználói felülete

1. A böngészőben nyissa meg a https://CLUSTERNAME.azurehdinsight.net. Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

    Ha a rendszer kéri, adja meg a fürt rendszergazdai fiókjának nevét, a **rendszergazdát**és a jelszót. Előfordulhat, hogy egy webes űrlapon újra meg kell adnia a rendszergazdai hitelesítő adatokat.

2. Az oldal tetején található sávban válassza az **Ops** bejegyzést. A lista a fürtön a Ambari használatával végzett aktuális és korábbi műveleteket jeleníti meg.

    ![Ambari webes FELHASZNÁLÓIFELÜLET-sáv kijelölése az Ops-mel](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

3. Keresse meg azokat a bejegyzéseket, amelyek **\_customscriptaction futtatták** az **Operations** oszlopban. Ezek a bejegyzések a parancsfájl műveleteinek futtatásakor jönnek létre.

    ![Apache Ambari parancsfájl műveleti műveletei](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Az **StdOut** és a **stderr** kimenetének megtekintéséhez válassza ki a **run\customscriptaction** bejegyzést, és részletezse a hivatkozásokat. Ezt a kimenetet a szkript futtatásakor generálja a rendszer, és hasznos információkhoz vezethet.

### <a name="access-logs-from-the-default-storage-account"></a>Hozzáférési naplók az alapértelmezett Storage-fiókból

Ha a fürt létrehozása parancsfájl hiba miatt meghiúsul, a rendszer a naplókat a fürt Storage-fiókjában tárolja.

* A tárolási naplók a `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`címen érhetők el.

    ![Parancsfájl műveleti naplói](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Ebben a könyvtárban a naplók külön vannak rendszerezve a **átjárócsomóponthoz**, a **munkavégző csomópont**és a **Zookeeper csomópont**számára. Lásd az alábbi példákat:

    * **Átjárócsomóponthoz**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Munkavégző csomópont**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper csomópont**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* A rendszer feltölti a megfelelő gazdagép összes **StdOut** -és **stderr** a Storage-fiókba. Minden parancsfájl-művelethez van egy **output-\*. txt** és **errors-\*. txt** . A **output-*. txt** fájl a gazdagépen futó parancsfájl URI azonosítóját tartalmazza. A következő szöveg egy példa erre az információra:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Lehetséges, hogy ismételten létrehoz egy azonos nevű parancsfájl-műveleti fürtöt. Ebben **az esetben a mappa neve** alapján megkülönböztetni a megfelelő naplókat. Például a fürt **mycluster**, amely különböző dátumokban lett létrehozva, a következő naplóbejegyzések szerint jelenik meg:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Ha azonos nevű parancsfájl-műveleti fürtöt hoz létre ugyanazon a napon, akkor a megfelelő naplófájlok azonosításához használhatja az egyedi előtagot.

* Ha olyan fürtöt hoz létre, amely a 12:00-es számú, éjfélt, akkor lehetséges, hogy a naplófájlok két nap alatt kiterjedhetnek. Ebben az esetben két különböző dátumú mappát láthat ugyanahhoz a fürthöz.

* A naplófájlok alapértelmezett tárolóba való feltöltése akár öt percet is igénybe vehet, különösen a nagyméretű fürtök esetében. Tehát ha el szeretné érni a naplókat, ne törölje azonnal a fürtöt, ha a parancsfájl művelete meghiúsul.

### <a name="ambari-watchdog"></a>Ambari watchdog

> [!WARNING]  
> Ne módosítsa a Ambari watchdog (hdinsightwatchdog) jelszavát a Linux-alapú HDInsight-fürtön. A fiók jelszavának módosítása megszakítja az új parancsfájl-műveletek futtatásának lehetőségét a HDInsight-fürtön.

### <a name="cant-import-name-blobservice"></a>Nem lehet importálni a BlobService nevet

__Tüneteket__. A parancsfájl művelete sikertelen. A következő hibához hasonló szöveg jelenik meg, amikor megtekinti a műveletet a Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__OK__. Ez a hiba akkor fordul elő, ha a HDInsight-fürthöz tartozó Python Azure Storage-ügyfelet frissíti. A HDInsight az Azure Storage ügyféloldali 0.20.0 várja.

__Megoldás__. A hiba megoldásához a `ssh`használatával manuálisan kapcsolódjon az egyes fürtcsomópontok csomópontjaihoz. Futtassa a következő parancsot a megfelelő tárolási ügyfél verziójának újratelepítéséhez:

```bash
sudo pip install azure-storage==0.20.0
```

További információ a fürt SSH-val történő csatlakoztatásáról: [Csatlakozás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>A előzmények nem jelenítik meg a fürt létrehozása során használt parancsfájlokat

Ha a fürt a 2016. március 15. előtt lett létrehozva, előfordulhat, hogy nem jelenik meg bejegyzés a parancsfájl-műveleti előzményekben. A fürt átméretezése hatására a parancsfájlok megjelennek a parancsfájl-műveletek előzményeiben.

Ez alól két kivétel van:

* A fürt a 2015. szeptember 1. előtt jött létre. Ez a dátum az, amikor parancsfájl-műveletek lettek bevezetve. Az ezen dátum előtt létrehozott összes fürthöz nem használható parancsfájl-művelet a fürt létrehozásához.

* A fürt létrehozása során több parancsfájl-műveletet is használt. Vagy ugyanazt a nevet használta több parancsfájlhoz, vagy ugyanazzal a névvel, azonos URI-val, de különböző paraméterekkel több parancsfájlhoz. Ezekben az esetekben a következő hibaüzenetet kapja:

    Nem lehet új parancsfájl-műveletet futtatni ezen a fürtön, mert a meglévő parancsfájlokban ütköző parancsfájlok nevei vannak. A fürt létrehozásakor megadott parancsfájloknak minden egyedinek kell lenniük. A meglévő parancsfájlok átméretezéssel futnak.

## <a name="next-steps"></a>Következő lépések

* [Parancsfájl-műveleti parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Apache Giraph telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-giraph-install-linux.md)
* [További tárterület hozzáadása egy HDInsight-fürthöz](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Szakaszok a fürt létrehozása során"
