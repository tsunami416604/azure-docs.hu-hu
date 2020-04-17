---
title: Üres peremhálózati csomópontok használata Apache Hadoop-fürtökön az Azure HDInsightban
description: Üres peremhálózati csomópont hozzáadása HDInsight-fürthöz. Ügyfélként használatos, majd tesztelje vagy üzemeltetje a HDInsight-alkalmazásokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537797"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Üres peremhálózati csomópontok használata apache hadoop-fürtökön a HDInsightban

Ismerje meg, hogyan adhat hozzá üres peremhálózati csomópontot egy HDInsight-fürthöz. Az üres peremhálózati csomópont egy Linux virtuális gép, amely ugyanazokat az ügyféleszközöket telepíti és konfigurálja, mint a headnodes. De nem [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) szolgáltatások fut. A peremhálózati csomópont segítségével elérheti a fürtöt, tesztelheti az ügyfélalkalmazásokat, és üzemeltetheti az ügyfélalkalmazásokat.

A fürt létrehozásakor hozzáadhat egy üres peremhálózati csomópontot egy meglévő HDInsight-fürthöz. Üres peremhálózati csomópont hozzáadása az Azure Resource Manager-sablon használatával történik.  A következő minta bemutatja, hogyan történik a sablon használata:

```json
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
```

Amint az a mintában látható, szükség esetén [parancsfájlműveletet](hdinsight-hadoop-customize-cluster-linux.md) hívhat meg további konfigurációk hozására. Például az [Apache Hue](hdinsight-hadoop-hue-linux.md) telepítése a peremhálózati csomóponton. A parancsfájlművelet-parancsfájlnak nyilvánosan elérhetőnek kell lennie az interneten.  Ha például a parancsfájl az Azure Storage-ban van tárolva, használjon nyilvános tárolókat vagy nyilvános blobokat.

A peremhálózati csomópont virtuális gép méretének meg kell felelnie a HDInsight-fürt munkavégző csomópont virtuális gép méretére vonatkozó követelményeknek. Az ajánlott munkavégző csomópont virtuálisgép-méreteit az [Apache Hadoop-fürtök létrehozása a HDInsightban című témakörben található.](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

Miután létrehozott egy peremhálózati csomópontot, csatlakozhat a peremhálózati csomóponthoz az SSH használatával, és futtathat ügyféleszközöket a Hadoop-fürt HDInsight-ban való eléréséhez.

> [!WARNING]
> A peremhálózati csomópontra telepített egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a Microsofttól. Ez a felmerülő problémák megoldásához vezethet. Vagy további segítségért közösségi forrásokhoz fordulhatunk. Az alábbiakban a legaktívabb webhelyek et talál, amelyek segítséget kapnak a közösségtől:
>
> * [MSDN fórum a HDInsighthoz](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Ha Apache technológiát használ, segítséget találhat az Apache projektwebhelyein [https://apache.org](https://apache.org)keresztül , például az [Apache Hadoop](https://hadoop.apache.org/) webhelyen.

> [!IMPORTANT]
> Ubuntu képek válnak elérhetővé az új HDInsight fürt létrehozása 3 hónapon belül a közzétételt. 2019 januárjától a futó fürtök (beleértve a peremhálózati csomópontokat is) **nem** lesznek automatikusan javítva. Az ügyfelekparancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürt javításához.  További információ: [OS patching for HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Peremcsomópont hozzáadása meglévő fürthöz

Ebben a szakaszban egy Erőforrás-kezelő sablon használatával egy meglévő HDInsight-fürthöz adott hozzá egy peremcsomópontot.  Az Erőforrás-kezelő sablon a [GitHubban](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)található. Az Erőforrás-kezelő sablon parancsfájlműveletet hív meg a helyen. https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh A szkript nem végez semmilyen műveletet.  A parancsfájl-művelet hívásának bemutatása egy Erőforrás-kezelő sablonból.

1. Válassza ki az alábbi képet az Azure-ba való bejelentkezéshez, és nyissa meg az Azure Resource Manager sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurálja a következő tulajdonságokat:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válasszon ki egy Azure-előfizetést a fürt létrehozásához.|
    |Erőforráscsoport|Válassza ki a meglévő HDInsight-fürthöz használt erőforráscsoportot.|
    |Hely|Válassza ki a meglévő HDInsight-fürt helyét.|
    |Fürt neve|Adja meg egy meglévő HDInsight-fürt nevét.|

1. Jelölje **be, hogy elfogadom a fent említett feltételeket,** majd válassza a **Vásárlás** lehetőséget a peremhálózati csomópont létrehozásához.

> [!IMPORTANT]  
> Győződjön meg arról, hogy válassza ki az Azure erőforráscsoportot a meglévő HDInsight-fürthöz.  Ellenkező esetben a következő hibaüzenet jelenik meg: "Nem hajtható végre a kért művelet beágyazott erőforráson. A szülőerőforrás "&lt;ClusterName>" nem található."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Szegélycsomópont hozzáadása fürt létrehozásakor

Ebben a szakaszban egy Erőforrás-kezelő sablon használatával hozhat létre HDInsight-fürtöt peremhálózati csomóponttal.  Az Erőforrás-kezelő sablon az [Azure gyorsútmutató-sablonok gyűjteményében](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)található. Az Erőforrás-kezelő sablon parancsfájlműveletet hív meg a helyen. https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh A szkript nem végez semmilyen műveletet.  A parancsfájl-művelet hívásának bemutatása egy Erőforrás-kezelő sablonból.

1. Hozzon létre egy HDInsight-fürtöt, ha még nem rendelkezik ilyentel.  Lásd: [A Hadoop használatának első lépései a HDInsightban.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

1. Válassza ki az alábbi képet az Azure-ba való bejelentkezéshez, és nyissa meg az Azure Resource Manager sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurálja a következő tulajdonságokat:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válasszon ki egy Azure-előfizetést a fürt létrehozásához.|
    |Erőforráscsoport|Hozzon létre egy új erőforráscsoportot a fürthöz.|
    |Hely|Válasszon egy helyet az erőforráscsoportnak.|
    |Fürt neve|Adja meg a létrehozandó új fürt nevét.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a Hadoop HTTP felhasználónevét.  Az alapértelmezett név az **admin**.|
    |Fürt bejelentkezési jelszava|Adja meg a Hadoop HTTP felhasználói jelszavát.|
    |Ssh felhasználónév|Adja meg az SSH felhasználónevét. Az alapértelmezett név **sshuser**.|
    |Ssh jelszó|Adja meg az SSH felhasználói jelszavát.|
    |Parancsfájl-művelet telepítése|Tartsa meg a cikk alapértelmezett értékét.|

    Néhány tulajdonság kódolva van a sablonban: Fürt típusa, Fürtmunkavégző csomópontok száma, Peremcsomópont mérete és Edge csomópont neve.

1. Jelölje **be, hogy elfogadja a fent meghatározott feltételeket,** majd válassza a **Vásárlás** lehetőséget a fürt peremhálózati csomópontdal történő létrehozásához.

## <a name="add-multiple-edge-nodes"></a>Több peremcsomópont hozzáadása

Több peremhálózati csomópontot is hozzáadhat egy HDInsight-fürthöz.  A több peremhálózati csomópontok konfigurációja csak az Azure Resource Manager-sablonok használatával végezhető el.  Tekintse meg a sablonmintát a cikk elején.  Frissítse a **targetInstanceCount-ot,** hogy tükrözze a létrehozni kívánt peremhálózati csomópontok számát.

## <a name="access-an-edge-node"></a>Peremhálózati csomópont elérése

A peremcsomópont ssh &lt;végpontja EdgeNodeName>. &lt;ClusterName>-ssh.azurehdinsight.net:22.  Például new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

A peremhálózati csomópont alkalmazásként jelenik meg az Azure Portalon.  A portál megadja az ssh-t használó peremhálózati csomópont eléréséhez szükséges információkat.

**A peremcsomópont SSH-végpontjának ellenőrzése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürtöt egy peremcsomóponttal.
3. Válassza **az Alkalmazások**lehetőséget. Látni fogja a peremcsomópontot.  Az alapértelmezett név az **új edgenode**.
4. Jelölje ki a peremcsomópontot. Megjelenik az SSH-végpont.

**Hive használata a peremhálózati csomóponton**

1. Csatlakozzon az élcsomóponthoz SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután az SSH használatával csatlakozott a peremhálózati csomóponthoz, a következő paranccsal nyissa meg a Hive-konzolt:

        hive

3. A következő parancs futtatásával jelenítse meg a fürt Hive-tábláit:

        show tables;

## <a name="delete-an-edge-node"></a>Peremcsomópont törlése

Törölheti a peremhálózati csomópontot az Azure Portalról.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürtöt egy peremcsomóponttal.
3. Válassza **az Alkalmazások**lehetőséget. Megjelenik a peremcsomópontok listája.  
4. Kattintson a jobb gombbal a törölni kívánt peremcsomópontra, majd válassza a **Törlés parancsot.**
5. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan adhat hozzá egy peremhálózati csomópontot, és hogyan érheti el a peremhálózati csomópontot. További információ: a következő cikkek:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [Egyéni HDInsight-alkalmazások telepítése:](hdinsight-apps-install-custom-applications.md)megtudhatja, hogyan telepíthet közzé nem közzétett HDInsight-alkalmazásokat a HDInsightba.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtöket hozhat létre a HDInsightban az Erőforrás-kezelő sablonjaival:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)ismerje meg, hogyan hívhatja meg az Erőforrás-kezelő sablonokat HDInsight-fürtök létrehozásához.
