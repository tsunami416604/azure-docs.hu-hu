---
title: Üres peremhálózati csomópontok használata Apache Hadoop fürtökön az Azure HDInsight
description: Egy üres peremhálózati csomópont hozzáadása egy HDInsight-fürthöz, amely ügyfélként használható, majd tesztelheti/üzemeltetheti a HDInsight-alkalmazásokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/27/2020
ms.openlocfilehash: b8bb68f7da74e547b020ae00a4672e10d70cbfd1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844462"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Üres peremhálózati csomópontok használata Apache Hadoop-fürtökön a HDInsight-ben

Megtudhatja, hogyan adhat hozzá üres peremhálózati csomópontot egy HDInsight-fürthöz. Az üres peremhálózati csomópont egy olyan linuxos virtuális gép, amely ugyanazokkal az eszközökkel van telepítve és konfigurálva, mint a átjárócsomópontokkal, de nem fut [Apache Hadoop](https://hadoop.apache.org/) szolgáltatás. Használhatja a peremhálózati csomópontot a fürt eléréséhez, az ügyfélalkalmazások teszteléséhez és az ügyfélalkalmazások üzemeltetéséhez.

A fürt létrehozásakor hozzáadhat egy üres peremhálózati csomópontot egy meglévő HDInsight-fürthöz egy új fürthöz. Üres peremhálózati csomópont hozzáadását Azure Resource Manager sablon használatával végezheti el.  Az alábbi minta azt mutatja be, hogyan történik a sablon használata:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "{}"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Ahogy azt a minta is mutatja, meghívhat egy parancsfájl- [műveletet](hdinsight-hadoop-customize-cluster-linux.md) további konfiguráció végrehajtására, például az [Apache Hue](hdinsight-hadoop-hue-linux.md) telepítését a peremhálózati csomóponton. A parancsfájl-művelet parancsfájljának nyilvánosan elérhetőnek kell lennie a weben.  Ha például a szkriptet az Azure Storage tárolja, használja a nyilvános tárolókat vagy a nyilvános blobokat.

Az Edge-csomópont virtuális gép méretének meg kell felelnie a HDInsight-fürt munkavégző csomópontjának a virtuálisgép-méret követelményeinek. A munkavégző csomópontok ajánlott virtuálisgép-méreteit lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Miután létrehozott egy peremhálózati csomópontot, az SSH használatával kapcsolódhat a peremhálózati csomóponthoz, és az ügyféleszközök futtatásával érheti el a Hadoop-fürtöt a HDInsight-ben.

> [!WARNING]
> Az Edge-csomópontra telepített egyéni összetevők a Microsofttól kereskedelmileg ésszerű támogatást kapnak. Ez az észlelt problémák megoldásához vezethet. Vagy ha további segítségre van szüksége, közösségi erőforrásokra is utalhat. A következőkben néhány, a Közösségtől származó Súgó közül a legaktívabb helyek közül:
>
> * [MSDN-fórum a HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Ha Apache-technológiát használ, előfordulhat, hogy a [https://apache.org on ](https://apache.org), például a [Apache Hadoop](https://hadoop.apache.org/) webhelyen található Apache Project-webhelyeken tud segítséget nyújtani.

> [!IMPORTANT]
> Az Ubuntu-lemezképek elérhetővé válnak az új HDInsight-fürt létrehozásakor a közzétételtől számított 3 hónapon belül. Január 2019-ig a fürtök (beleértve az Edge-csomópontokat is) **nem** automatikus javítással működnek. Az ügyfeleknek parancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürtök javításához.  További információ: operációsrendszer- [javítás a HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Peremhálózati csomópont hozzáadása meglévő fürthöz

Ebben a szakaszban egy Resource Manager-sablon használatával ad hozzá egy peremhálózati csomópontot egy meglévő HDInsight-fürthöz.  A Resource Manager-sablon a [githubon](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)érhető el. A Resource Manager-sablon meghívja a https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh címen található parancsfájl-műveletet. A parancsfájl nem hajt végre műveleteket.  Ez egy Resource Manager-sablonban való hívási parancsfájl-művelet bemutatását mutatja be.

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez, majd nyissa meg a Azure Resource Manager sablont a Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurálja a következő tulajdonságokat:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válassza ki a fürt létrehozásához használt Azure-előfizetést.|
    |Erőforráscsoport|Válassza ki a meglévő HDInsight-fürthöz használt erőforráscsoportot.|
    |Földrajzi egység|Válassza ki a meglévő HDInsight-fürt helyét.|
    |Fürt neve|Adja meg egy meglévő HDInsight-fürt nevét.|

1. Győződjön meg **arról, hogy elfogadom a fenti feltételeket és kikötéseket**, majd válassza a **vásárlás** lehetőséget a peremhálózati csomópont létrehozásához.

> [!IMPORTANT]  
> Ügyeljen arra, hogy kiválassza a meglévő HDInsight-fürthöz tartozó Azure-erőforráscsoportot.  Ellenkező esetben a következő hibaüzenet jelenik meg: "nem lehet végrehajtani a kért műveletet a beágyazott erőforráson. A (z) "&lt;ClusterName >" fölérendelt erőforrás nem található. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Peremhálózati csomópont hozzáadása fürt létrehozásakor

Ebben a szakaszban egy Resource Manager-sablon használatával HDInsight-fürtöt hoz létre egy peremhálózati csomóponttal.  A Resource Manager-sablon az [Azure gyorsindítási sablonok galériájában](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)található. A Resource Manager-sablon meghívja a https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh címen található parancsfájl-műveletet. A parancsfájl nem hajt végre műveleteket.  Ez egy Resource Manager-sablonban való hívási parancsfájl-művelet bemutatását mutatja be.

1. Ha még nem rendelkezik ilyennel, hozzon létre egy HDInsight-fürtöt.  Lásd: [a Hadoop használatának első lépései a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez, majd nyissa meg a Azure Resource Manager sablont a Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurálja a következő tulajdonságokat:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válassza ki a fürt létrehozásához használt Azure-előfizetést.|
    |Erőforráscsoport|Hozzon létre egy új erőforráscsoportot a fürthöz.|
    |Földrajzi egység|Válasszon egy helyet az erőforráscsoportnak.|
    |Fürt neve|Adja meg a létrehozandó új fürt nevét.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a Hadoop HTTP-felhasználónevét.  Az alapértelmezett név az **admin**.|
    |Fürt bejelentkezési jelszava|Adja meg a Hadoop HTTP-felhasználói jelszavát.|
    |SSH-Felhasználónév|Adja meg az SSH-Felhasználónév nevét. Az alapértelmezett név a **sshuser**.|
    |SSH-jelszó|Adja meg az SSH-felhasználó jelszavát.|
    |Parancsfájl telepítése művelet|Tartsa meg az alapértelmezett értéket a jelen cikkben leírtak szerint.|

    Egyes tulajdonságok a következő sablonban lettek hardcoded: fürt típusa, fürt munkavégző csomópontok száma, Edge-csomópont mérete és peremhálózati csomópont neve.

1. Győződjön meg **arról, hogy elfogadom a fenti feltételeket és kikötéseket**, majd válassza a **vásárlás** lehetőséget a fürt peremhálózati csomóponttal való létrehozásához.

## <a name="add-multiple-edge-nodes"></a>Több peremhálózati csomópont hozzáadása

Több peremhálózati csomópontot is hozzáadhat egy HDInsight-fürthöz.  A többoldalas csomópontok konfigurálása csak Azure Resource Manager sablonok használatával végezhető el.  A cikk elején tekintse meg a sablon mintáját.  A létrehozni kívánt peremhálózati csomópontok számának megfelelően frissítenie kell a **targetInstanceCount** .

## <a name="access-an-edge-node"></a>Hozzáférés egy peremhálózati csomóponthoz

Az Edge Node SSH-végpontja &lt;EdgeNodeName >.&lt;ClusterName >-ssh.azurehdinsight.net:22.  Például: new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

A peremhálózati csomópont alkalmazásként jelenik meg a Azure Portalon.  A portálon megtekintheti a peremhálózati csomópont SSH használatával való eléréséhez szükséges információkat.

**Az Edge Node SSH-végpontjának ellenőrzése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight fürtöt egy peremhálózati csomóponttal.
3. Válassza az **alkalmazások**lehetőséget. Ekkor látnia kell a peremhálózati csomópontot.  Az alapértelmezett név a **New-élcsomópontok**.
4. Válassza ki a peremhálózati csomópontot. Meg kell tekintenie az SSH-végpontot.

**A kaptár használata a peremhálózati csomóponton**

1. Csatlakozzon az élcsomóponthoz SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután az SSH-val csatlakozott az Edge-csomóponthoz, a következő parancs használatával nyissa meg a kaptár-konzolt:

        hive

3. Futtassa a következő parancsot a kaptár-táblák megjelenítéséhez a fürtben:

        show tables;

## <a name="delete-an-edge-node"></a>Peremhálózati csomópont törlése

A Azure Portalból törölhet egy peremhálózati csomópontot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight fürtöt egy peremhálózati csomóponttal.
3. Válassza az **alkalmazások**lehetőséget. Ekkor megjelenik a peremhálózati csomópontok listája.  
4. Kattintson a jobb gombbal a törölni kívánt peremhálózati csomópontra, majd válassza a **Törlés**lehetőséget.
5. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan adhat hozzá egy Edge-csomópontot, és hogyan érheti el a peremhálózati csomópontot. További információt a következő cikkekben talál:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): megtudhatja, hogyan helyezhet üzembe egy közzé nem tett HDInsight alkalmazást a HDInsight.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsight Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md): megtudhatja, hogyan hívhat Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
