---
title: "Üres peremhálózati csomópontok használata a hdinsight - Azure Hadoop-fürtök |} Microsoft Docs"
description: "Hogyan adhat hozzá egy üres élcsomópontot ügyfélként is használható, amely majd vizsgálati és a gazdagép a HDInsight-alkalmazások HDInsight-fürtöt."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: ad161ada875ab30ed8de24506ed27265f26e994a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Üres peremhálózati csomópontok használata a hdinsight Hadoop-fürtök

Ismerje meg, egy üres élcsomópontot felvétele a HDInsight-fürtöt. Egy üres élcsomópontot ügyfél eszközök telepítése és konfigurálása, mint a headnodes, de nincs Hadoop-szolgáltatás fut a Linux virtuális gép. A fürt eléréséhez, az ügyfél alkalmazások tesztelése és az ügyfélalkalmazások üzemeltető élcsomópont is használhatja. 

Egy üres élcsomópontot is hozzáadhat egy meglévő HDInsight-fürthöz az új fürtre a fürt létrehozásakor. Történik, egy üres élcsomópontot hozzáadása Azure Resource Manager-sablon használatával.  A következő példa bemutatja, hogyan történik a sablon használatával:

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
                            "vmSize": "Standard_D3"
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

Mintában látható módon a, opcionálisan hívása egy [parancsfájl-művelet](hdinsight-hadoop-customize-cluster-linux.md) további konfigurációra, mint például telepítése [Apache Hue](hdinsight-hadoop-hue-linux.md) peremhálózati csomópontjában. A parancsfájl parancsfájlművelet nyilvánosan elérhető a weben kell lennie.  Például ha a parancsfájl az Azure-tárfiókba, használja a nyilvános tárolók vagy nyilvános blobok.

A peremhálózati csomópont virtuálisgép-méret meg kell felelnie a HDInsight fürt munkavégző csomópont virtuális gép. Az ajánlott munkavégző csomópont virtuálisgép-méretek, lásd: [Hadoop létrehozása a HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Miután létrehozott egy élcsomópontot, csatlakozás az élcsomóponthoz SSH használatával, és futtassa a hdinsight Hadoop-fürt eléréséhez ügyféleszközök elől.

> [!WARNING] 
> Egy üres élcsomópontot használata a HDInsight jelenleg előzetes verzió. Egyéni összetevők élcsomópont telepített minden üzleti szempontból ésszerű támogatást kaphatnak a Microsofttól. Emiatt előfordulhat, hogy a felmerülő problémák elhárításához. Vagy, ha további segítségre van szüksége a közösségi források lehet hivatkozni. Az alábbiakban a segítségkérés a Közösségtől a legtöbb aktív helyek:
>
> * [A HDInsight MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com).
>
> Az Apache-okat használ, ha esetleg találni az Apache keresztül projekt helyek [http://apache.org](http://apache.org), például a [Hadoop](http://hadoop.apache.org/) hely.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Meglévő fürt egy élcsomópontot hozzáadása
Ebben a szakaszban egy Resource Manager-sablon egy élcsomópontot hozzáadása egy meglévő HDInsight-fürt használatára.  A Resource Manager-sablon található [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/). A Resource Manager-sablon meghívja a parancsfájlművelet https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh helyen. A parancsfájl nem más műveletet végrehajtani.  A Resource Manager-sablon a hívó parancsfájlművelet bemutatásához.

**Egy üres élcsomópontot hozzáadása egy meglévő fürthöz**

1. HDInsight-fürtök létrehozása, ha még nincs ilyen.  Lásd: [Hadoop oktatóanyag: Hadoop használatának megkezdésében a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Kattintson az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg az Azure Resource Manager-sablon az Azure portálon. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurálja a következő tulajdonságokat:
   
   * **Előfizetés**: válassza ki a fürt létrehozásához használt Azure-előfizetéssel.
   * **Erőforráscsoport**: válasszon a meglévő HDInsight-fürt erőforráscsoportot.
   * **Hely**: válassza ki azt a helyet a meglévő HDInsight-fürt.
   * **A fürt neve**: Adja meg egy meglévő HDInsight-fürt nevét.
   * **A szegély csomópont méretének**: válassza ki a Virtuálisgép-méretek. A virtuálisgép-méretet meg kell felelnie a munkavégző csomópont virtuális gép. Az ajánlott munkavégző csomópont virtuálisgép-méretek, lásd: [Hadoop létrehozása a HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **A szegély csomópont előtag**: az alapértelmezett érték **új**.  Az alapértelmezett érték, a peremhálózati csomópontnév használata **új edgenode**.  Testre szabhatja az előtag a portálról. Testre szabhatja a teljes nevet, a sablon alapján.

4. Ellenőrizze **elfogadom a feltételeket és a fenti feltételek**, és kattintson a **beszerzési** élcsomópont létrehozásához.

>[!IMPORTANT]
> Ügyeljen arra, hogy a meglévő HDInsight-fürthöz az Azure erőforráscsoport kiválasztását.  Ellenkező esetben a következő üzenet jelenik "nem hajtható végre a kért művelet beágyazott erőforráson. Szülő erőforrás "&lt;ClusterName >" nem található. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adja hozzá egy élcsomópontot, ha a fürt létrehozása
Ebben a szakaszban a Resource Manager-sablon létrehozása a HDInsight-fürt egy élcsomópontot a használhatja.  A Resource Manager-sablon megtalálható a [Azure gyors üzembe helyezés sablontárban](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). A Resource Manager-sablon meghívja a parancsfájlművelet https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh helyen. A parancsfájl nem más műveletet végrehajtani.  A Resource Manager-sablon a hívó parancsfájlművelet bemutatásához.

**Egy üres élcsomópontot hozzáadása egy meglévő fürthöz**

1. HDInsight-fürtök létrehozása, ha még nincs ilyen.  Lásd: [Hadoop használatának megkezdésében a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Kattintson az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg az Azure Resource Manager-sablon az Azure portálon. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurálja a következő tulajdonságokat:
   
   * **Előfizetés**: válassza ki a fürt létrehozásához használt Azure-előfizetéssel.
   * **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot a fürthöz.
   * **Hely**: Válasszon egy helyet az erőforráscsoportnak.
   * **A fürt neve**: Adjon meg egy nevet az új fürt létrehozásához.
   * **A fürt bejelentkezési felhasználónevét**: a Hadoop HTTP felhasználónevet adja meg.  Az alapértelmezett név az **admin**.
   * **A fürt bejelentkezési jelszó**: a Hadoop HTTP felhasználói jelszó.
   * **Ssh felhasználónév**: Adja meg az SSH-felhasználónév. Az alapértelmezett név az **sshuser**.
   * **Ssh jelszó**: Adja meg az SSH-felhasználó jelszót.
   * **Telepítse a parancsfájlművelet**: tartsa meg az oktatóanyag lépéseinek az alapértelmezett értéket.
     
     Néhány tulajdonság lett volna a sablonban szoftveresen kötött: fürt típusa, a fürt feldolgozó csomópontok száma, a peremhálózati csomópont mérete és a peremhálózati csomópont neve.
4. Ellenőrizze **elfogadom a feltételeket és a fenti feltételek**, és kattintson a **beszerzési** élcsomópont a fürt létrehozásához.

## <a name="access-an-edge-node"></a>Hozzáférés egy élcsomópontot
Élcsomópont ssh-végpont esetében &lt;EdgeNodeName >.&lt; ClusterName >-ssh.azurehdinsight.net:22.  Például új-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Élcsomópont egy alkalmazást az Azure-portálon jelenik meg.  A portál jelenít meg az adatokat a élcsomópontjához SSH eléréséhez.

**A peremhálózati csomópont SSH végpont ellenőrzése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürt egy élcsomópontot.
3. Kattintson a **alkalmazások** a fürt paneljén. Ekkor megjelenik az élcsomóponthoz.  Az alapértelmezett név az **új edgenode**.
4. Kattintson az élcsomóponthoz. Ekkor megjelenik az SSH-végpontot.

**A Hive használata a peremhálózati csomóponton**

1. Az SSH használata a csatlakozás az élcsomóponthoz. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az SSH használatával élcsomóponthoz csatlakoztatása után a Hive-konzolt használja a következő parancsot:
   
        hive
3. A következő parancsot a fürt megjelenítése a Hive táblák:
   
        show tables;

## <a name="delete-an-edge-node"></a>Egy élcsomópontot törlése
Azure-portálról egy élcsomópontot törölheti.

**Egy élcsomópontot eléréséhez**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürt egy élcsomópontot.
3. Kattintson a **alkalmazások** a fürt paneljén. Peremhálózati lista megjelenik.  
4. Kattintson a jobb gombbal az élcsomóponthoz, törlése, és kattintson a kívánt **törlése**.
5. Kattintson a **Yes** (Igen) gombra a megerősítéshez.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulhatta, egy élcsomópontot hozzáadása és az élcsomóponthoz elérését. További tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével): Megtudhatja, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.

