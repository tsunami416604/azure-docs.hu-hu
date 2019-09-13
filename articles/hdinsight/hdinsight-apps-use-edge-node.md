---
title: Üres peremhálózati csomópontok használata Apache Hadoop-fürtökön a HDInsight-Azure-ban
description: Egy üres peremhálózati csomópont hozzáadása egy HDInsight-fürthöz, amely ügyfélként használható, majd tesztelheti/üzemeltetheti a HDInsight-alkalmazásokat.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: 31cffc71937f7be158da8cbbd1d8abe6e3fe8372
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916654"
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

Ahogy azt a minta is mutatja, meghívhat egy parancsfájl- [műveletet](hdinsight-hadoop-customize-cluster-linux.md) további konfiguráció végrehajtására, például az [Apache Hue](hdinsight-hadoop-hue-linux.md) telepítését a peremhálózati csomóponton. A parancsfájl-művelet parancsfájljának nyilvánosan elérhetőnek kell lennie a weben.  Ha például a szkriptet az Azure Storage tárolja, használja a nyilvános tárolókat vagy a nyilvános blobokat.

Az Edge-csomópont virtuális gép méretének meg kell felelnie a HDInsight-fürt munkavégző csomópontjának a virtuálisgép-méret követelményeinek. A munkavégző csomópontok ajánlott virtuálisgép-méreteit lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Miután létrehozott egy peremhálózati csomópontot, az SSH használatával kapcsolódhat a peremhálózati csomóponthoz, és az ügyféleszközök futtatásával érheti el a Hadoop-fürtöt a HDInsight-ben.

> [!WARNING]   
> Az Edge-csomópontra telepített egyéni összetevők a Microsofttól kereskedelmileg ésszerű támogatást kapnak. Ez az észlelt problémák megoldásához vezethet. Vagy ha további segítségre van szüksége, közösségi erőforrásokra is utalhat. A következőkben néhány, a Közösségtől származó Súgó közül a legaktívabb helyek közül:
>
> * [MSDN-fórum a HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Ha Apache-technológiát használ, lehet, hogy az Apache [https://apache.org](https://apache.org)-projekt webhelyein, például a [Apache Hadoop](https://hadoop.apache.org/) webhelyen tud segítséget találni.

> [!IMPORTANT]
> Az Ubuntu-lemezképek elérhetővé válnak az új HDInsight-fürt létrehozásakor a közzétételtől számított 3 hónapon belül. Január 2019-ig a fürtök (beleértve az Edge-csomópontokat is) **nem** automatikus javítással működnek. Az ügyfeleknek parancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürtök javításához.  További információ: operációsrendszer- [javítás a HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Peremhálózati csomópont hozzáadása meglévő fürthöz
Ebben a szakaszban egy Resource Manager-sablon használatával ad hozzá egy peremhálózati csomópontot egy meglévő HDInsight-fürthöz.  A Resource Manager-sablon a [githubon](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)érhető el. A Resource Manager-sablon egy parancsfájl-műveletet hív https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh meg, amely a következő helyen található:. A parancsfájl nem hajt végre műveleteket.  Ez egy Resource Manager-sablonban történő hívás parancsfájl-műveletének bemutatása.

**Üres peremhálózati csomópont hozzáadása meglévő fürthöz**

1. Az alábbi képre kattintva bejelentkezhet az Azure-ba, és megnyithatja a Azure Resource Manager sablont a Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
3. Konfigurálja a következő tulajdonságokat:
   
   * **Előfizetés**: Válassza ki a fürt létrehozásához használt Azure-előfizetést.
   * **Erőforráscsoport**: Válassza ki a meglévő HDInsight-fürthöz használt erőforráscsoportot.
   * **Hely**: Válassza ki a meglévő HDInsight-fürt helyét.
   * **Fürt neve**: Adja meg egy meglévő HDInsight-fürt nevét.
   * **Peremhálózati csomópont mérete**: Válasszon egy virtuálisgép-méretet. A virtuális gép méretének meg kell felelnie a munkavégző csomópont virtuálisgép-méretére vonatkozó követelményeknek. A munkavégző csomópontok ajánlott virtuálisgép-méreteit lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Peremhálózati csomópont előtagja**: Az alapértelmezett érték az **új**.  Az alapértelmezett érték használatával a peremhálózati csomópont neve **New-élcsomópontok**.  A portálon testreszabhatja az előtagot. A sablon teljes nevét testre is szabhatja.

4. Győződjön meg **arról, hogy elfogadom a fenti feltételeket és kikötéseket**, majd kattintson a **vásárlás** elemre a peremhálózati csomópont létrehozásához.

>[!IMPORTANT]  
> Ügyeljen arra, hogy kiválassza a meglévő HDInsight-fürthöz tartozó Azure-erőforráscsoportot.  Ellenkező esetben a következő hibaüzenet jelenik meg: "nem lehet végrehajtani a kért műveletet a beágyazott erőforráson. A (&lt;z) "ClusterName > szülő erőforrás nem található."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Peremhálózati csomópont hozzáadása fürt létrehozásakor
Ebben a szakaszban egy Resource Manager-sablon használatával HDInsight-fürtöt hoz létre egy peremhálózati csomóponttal.  A Resource Manager-sablon az [Azure gyorsindítási sablonok galériájában](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)található. A Resource Manager-sablon egy parancsfájl-műveletet hív https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh meg, amely a következő helyen található:. A parancsfájl nem hajt végre műveleteket.  Ez egy Resource Manager-sablonban történő hívás parancsfájl-műveletének bemutatása.

**HDInsight-fürt létrehozása peremhálózati csomóponttal**

1. Ha még nem rendelkezik ilyennel, hozzon létre egy HDInsight-fürtöt.  Lásd: [a Hadoop használatának első lépései a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Az alábbi képre kattintva bejelentkezhet az Azure-ba, és megnyithatja a Azure Resource Manager sablont a Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
3. Konfigurálja a következő tulajdonságokat:
   
   * **Előfizetés**: Válassza ki a fürt létrehozásához használt Azure-előfizetést.
   * **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot a fürthöz.
   * **Hely**: Válasszon egy helyet az erőforráscsoportnak.
   * **Fürt neve**: Adja meg a létrehozandó új fürt nevét.
   * **Fürt bejelentkezési felhasználónevének neve**: Adja meg a Hadoop HTTP-felhasználónevét.  Az alapértelmezett név az **admin**.
   * **Fürt bejelentkezési jelszava**: Adja meg a Hadoop HTTP-felhasználói jelszavát.
   * **SSH-Felhasználónév**: Adja meg az SSH-Felhasználónév nevét. Az alapértelmezett név a **sshuser**.
   * **SSH-jelszó**: Adja meg az SSH-felhasználó jelszavát.
   * **Parancsfájl telepítése művelet**: Tartsa meg az alapértelmezett értéket a jelen cikkben leírtak szerint.
     
     Néhány tulajdonságot a sablon hardcoded: A fürt típusa, a fürt munkavégző csomópontjának száma, az Edge-csomópont mérete és a peremhálózati csomópont neve.
4. Győződjön meg **arról, hogy elfogadom a fenti feltételeket és kikötéseket**, majd kattintson a **vásárlás** gombra a fürt peremhálózati csomóponttal való létrehozásához.

## <a name="add-multiple-edge-nodes"></a>Több peremhálózati csomópont hozzáadása

Több peremhálózati csomópontot is hozzáadhat egy HDInsight-fürthöz.  A többoldalas csomópontok konfigurálása csak Azure Resource Manager sablonok használatával végezhető el.  A cikk elején tekintse meg a sablon mintáját.  A létrehozni kívánt peremhálózati csomópontok számának megfelelően frissítenie kell a **targetInstanceCount** .

## <a name="access-an-edge-node"></a>Hozzáférés egy peremhálózati csomóponthoz
Az Edge Node SSH-végpontja &lt;> EdgeNodeName.&lt; ClusterName > – ssh.azurehdinsight.net:22.  Például: new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

A peremhálózati csomópont alkalmazásként jelenik meg a Azure Portalon.  A portálon megtekintheti a peremhálózati csomópont SSH használatával való eléréséhez szükséges információkat.

**Az Edge Node SSH-végpontjának ellenőrzése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight fürtöt egy peremhálózati csomóponttal.
3. Kattintson az **alkalmazások**elemre. Ekkor látnia kell a peremhálózati csomópontot.  Az alapértelmezett név a **New-élcsomópontok**.
4. Kattintson a peremhálózati csomópontra. Meg kell tekintenie az SSH-végpontot.

**A kaptár használata a peremhálózati csomóponton**

1. Csatlakozzon az élcsomóponthoz SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután az SSH-val csatlakozott az Edge-csomóponthoz, a következő parancs használatával nyissa meg a kaptár-konzolt:
   
        hive
3. Futtassa a következő parancsot a kaptár-táblák megjelenítéséhez a fürtben:
   
        show tables;

## <a name="delete-an-edge-node"></a>Peremhálózati csomópont törlése
A Azure Portalból törölhet egy peremhálózati csomópontot.

**Egy peremhálózati csomópont eléréséhez**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight fürtöt egy peremhálózati csomóponttal.
3. Kattintson az **alkalmazások**elemre. Ekkor megjelenik a peremhálózati csomópontok listája.  
4. Kattintson a jobb gombbal a törölni kívánt peremhálózati csomópontra, majd kattintson a **Törlés**parancsra.
5. Kattintson a **Yes** (Igen) gombra a megerősítéshez.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan adhat hozzá egy peremhálózati csomópontot, és hogyan érheti el a peremhálózati csomópontot. További információt a következő cikkekben talál:

* [HDInsight alkalmazások telepítése](hdinsight-apps-install-applications.md): Ismerje meg, hogyan telepíthet HDInsight-alkalmazásokat a fürtökre.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Ismerje meg, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Marketplace-en.
* [MSDN HDInsight-alkalmazás](https://msdn.microsoft.com/library/mt706515.aspx)telepítése: Megtudhatja, hogyan definiálhat HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md): ismerje meg, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.

