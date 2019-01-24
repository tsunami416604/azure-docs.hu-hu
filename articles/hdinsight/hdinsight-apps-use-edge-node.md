---
title: Üres élcsomópontok használata a HDInsight - Azure Apache Hadoop-fürtök
description: Hogyan lehet üres élcsomópontot hozzáadni egy HDInsight-fürtöt, amely ügyfélként is használható, és ezután teszt/állomás a HDInsight-alkalmazások.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: fca14eb5a51799e6d3c0e4f96cb956e4e6886cdb
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844823"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Üres élcsomópontok használata a HDInsight Apache Hadoop-fürtök

Ismerje meg, hogyan lehet üres élcsomópontot hozzáadni egy HDInsight-fürt. Üres élcsomópontot egy Linuxos virtuális gép ugyanazokat az ügyfél eszközöket telepíteni és konfigurálni az átjárócsomópontokkal hasonlóan, de nem [Apache Hadoop](https://hadoop.apache.org/) futó szolgáltatásokat. A peremhálózati csomópont a fürthöz hozzáférő, az ügyfél alkalmazásokat kíván tesztelni, és az ügyfélalkalmazásokat üzemeltető is használhatja. 

Egy meglévő HDInsight-fürtön, egy új fürt a fürt létrehozásakor egy üres élcsomópontot is hozzáadhat. Üres élcsomópontot hozzáadása történik az Azure Resource Manager-sablon használatával.  A következő példa bemutatja, hogyan történik egy sablon használatával:

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

Ahogyan azt a mintát, igény szerint meghívhatja egy [műveleti parancsfájl](hdinsight-hadoop-customize-cluster-linux.md) további konfigurálást, például a telepítés végrehajtásához [Apache Hue](hdinsight-hadoop-hue-linux.md) az élcsomóponthoz. A parancsfájl parancsfájlművelet kell lennie a weben nyilvánosan elérhető.  Például ha a parancsfájl az Azure storage-ban, használjon nyilvános tárolók vagy nyilvános blobok.

A peremhálózati csomópont virtuális gépének mérete a HDInsight fürt munkavégző csomópont virtuális gép mérete követelményeknek kell megfelelnie. A javasolt munkavégző csomópont virtuálisgép-méretek, lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Miután létrehozta az élcsomóponthoz, az élcsomóponthoz SSH használatával csatlakozhat, és futtassa az ügyfél eszközök eléréséhez a HDInsight Hadoop-fürtöt.

> [!WARNING]   
> Egyéni telepített összetevőinek támogatásához az élcsomóponti operacionalizáláshoz üzletileg ésszerű támogatást kapni a Microsofttól. Emiatt előfordulhat, hogy a felmerülő problémák elhárításához. Másik lehetőségként, további segítségért közösségi erőforrások lehet hivatkozni. A következő néhány a legtöbb aktív helyek segítség kérése a Közösségtől:
>
> * [A HDInsight MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Ha egy Apache-okat használ, akkor lehet a projekt helyek megtalálhatja a segítséget az Apache [ https://apache.org ](https://apache.org), mint például a [Apache Hadoop](https://hadoop.apache.org/) hely.

> [!IMPORTANT]
> Ubuntu-rendszerképek válnak elérhetővé az új HDInsight-fürt létrehozása közzétehető 3 hónapon belül. Január a 2019-tól futó fürtök (beleértve a élcsomópontok) vannak **nem** automatikus tudjon fókuszálni. Ügyfeleink a futó fürt javítására irányuló szkriptműveletek vagy más mechanizmusok kell használnia.  További információkért lásd: [operációs rendszer javításai a HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Egy peremhálózati csomópont hozzáadása meglévő fürthöz
Ebben a szakaszban a Resource Manager-sablonnal egy meglévő HDInsight-fürt egy élcsomópontot hozzáadandó használhatja.  A Resource Manager-sablon található [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). A Resource Manager-sablon meghívja a helyen található szkriptműveletet https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. A parancsfájl nem más műveletet végrehajtani.  A Resource Manager-sablonnal hívó parancsfájlművelet bemutatásához.

**Üres élcsomópontot hozzáadása egy meglévő fürthöz**

1. Kattintson az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg az Azure Resource Manager-sablon az Azure Portalon. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Adja meg a következő tulajdonságokkal:
   
   * **Előfizetés**: Válassza ki a fürt létrehozásához használt Azure-előfizetés.
   * **Erőforráscsoport**: Válassza ki a meglévő HDInsight-fürt használt erőforráscsoport.
   * **Hely**: Válassza ki a meglévő HDInsight-fürt helyét.
   * **Fürt neve**: Adjon meg egy meglévő HDInsight-fürt nevére.
   * **Csomópontméret él**: Válassza ki a Virtuálisgép-méretek egyikét. A virtuálisgép-méretet, meg kell felelnie a munkavégző csomópont virtuálisgép-méret követelmények. A javasolt munkavégző csomópont virtuálisgép-méretek, lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Csomópont-előtag él**: Az alapértelmezett érték **új**.  Az az alapértelmezett érték, a peremhálózati csomópont nevét a következő **új élcsomópontok**.  Testre szabhatja az előtag a portálról. A teljes nevet, a sablon alapján is testreszabhatja.

4. Ellenőrizze **elfogadom a feltételeket és a fenti feltételeket**, és kattintson a **beszerzési** hozhat létre az élcsomóponthoz.

>[!IMPORTANT]  
> Ellenőrizze, hogy válassza ki az Azure-erőforráscsoportot a meglévő HDInsight-fürt.  Ellenkező esetben a következő üzenet jelenik "nem hajtható végre, a beágyazott erőforrás kért műveletet. Szülőerőforrás "&lt;ClusterName >" nem található. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adja hozzá az élcsomóponthoz, a fürt létrehozásakor
Ebben a szakaszban egy Resource Manager-sablon a HDInsight-fürt létrehozása a élcsomópontot használja.  A Resource Manager-sablon megtalálható a [Azure gyorsindítási sablonok katalógusában](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). A Resource Manager-sablon meghívja a helyen található szkriptműveletet https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. A parancsfájl nem más műveletet végrehajtani.  A Resource Manager-sablonnal hívó parancsfájlművelet bemutatásához.

**Az élcsomópont egy HDInsight-fürt létrehozása**

1. Ha még nincs ilyen, hozzon létre egy HDInsight-fürtön.  Lásd: [Hadoop első lépései a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Kattintson az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg az Azure Resource Manager-sablon az Azure Portalon. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Adja meg a következő tulajdonságokkal:
   
   * **Előfizetés**: Válassza ki a fürt létrehozásához használt Azure-előfizetés.
   * **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, a fürthöz.
   * **Hely**: Válasszon egy helyet az erőforráscsoportnak.
   * **Fürt neve**: Adjon meg egy nevet az új fürt létrehozása.
   * **A fürt bejelentkezési név**: Adja meg a Hadoop HTTP-felhasználó nevét.  Az alapértelmezett név az **admin**.
   * **A fürt bejelentkezési jelszavának**: Adja meg a Hadoop HTTP-felhasználó jelszavát.
   * **Ssh-felhasználónév**: Adja meg az SSH-felhasználónév. Alapértelmezés szerint ez **sshuser**.
   * **Ssh jelszó**: Adja meg az SSH-felhasználói jelszóra.
   * **Telepítse a Script Action**: Tartsa meg az alapértelmezett érték az oktatóanyag.
     
     Egyes tulajdonságok kötöttek a sablonban: Fürt típusa, a fürt munkavégző csomópontok száma, a peremhálózati csomópont mérete és a peremhálózati csomópont nevét.
4. Ellenőrizze **elfogadom a feltételeket és a fenti feltételeket**, és kattintson a **beszerzési** a fürt létrehozásához az élcsomóponthoz.

## <a name="add-multiple-edge-nodes"></a>Több peremhálózati csomópont hozzáadása

Egy HDInsight-fürt több élcsomópontok adhat hozzá.  A több peremhálózati csomópont konfiguráció kizárólag valósítható meg az Azure Resource Manager-sablonok használatával.  Tekintse meg a sablon mintát, ez a cikk elején.  Frissítenie kell a **targetInstanceCount** szeretne létrehozni edge csomópontok számának megfelelően.

## <a name="access-an-edge-node"></a>Az élcsomópont eléréséhez
Az élcsomópont ssh végpont van &lt;EdgeNodeName >.&lt; ClusterName >-ssh.azurehdinsight.net:22.  For example, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Az élcsomópont egy alkalmazást, az Azure-portálon jelenik meg.  A portál eléréséhez az élcsomópont SSH-val a információkat biztosít.

**A peremhálózati csomópont SSH-végpont ellenőrzése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürt egy élcsomópontot.
3. Kattintson a **alkalmazások**. Meg kell jelennie az élcsomóponthoz.  Alapértelmezés szerint ez **új élcsomópontok**.
4. Kattintson az élcsomóponthoz. Meg kell jelennie az SSH-végpont.

**A Hive használata az élcsomópontra**

1. Csatlakozzon az élcsomóponthoz SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután csatlakozott az élcsomóponthoz SSH használatával, a Hive konzolt használja a következő parancsot:
   
        hive
3. A következő parancsot a fürt megjelenítése a Hive-táblákat:
   
        show tables;

## <a name="delete-an-edge-node"></a>Élcsomópont törlése
Élcsomópont törölheti az Azure Portalról.

**Az élcsomópont eléréséhez**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight-fürt egy élcsomópontot.
3. Kattintson a **alkalmazások**. Az élcsomópontok listáját gondoskodnak.  
4. Kattintson a jobb gombbal az élcsomóponthoz, törlése, és kattintson a kívánt **törlése**.
5. Kattintson a **Yes** (Igen) gombra a megerősítéshez.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett hozzáadása az élcsomóponthoz, és hogyan lehet az élcsomópont eléréséhez. További tudnivalókért tekintse meg a következő cikkeket:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtökhöz.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Ismerje meg, hogyan teheti közzé egyéni HDInsight-alkalmazásait akár az Azure Marketplace-en.
* [MSDN: Egy HDInsight-alkalmazás telepítése](https://msdn.microsoft.com/library/mt706515.aspx): Ismerje meg, hogyan adhat meg HDInsight-alkalmazások.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md): ismerje meg, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.

