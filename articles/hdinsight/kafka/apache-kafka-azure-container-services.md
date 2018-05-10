---
title: Azure Kubernetes szolgáltatás használata a HDInsight Kafka |} Microsoft Docs
description: Megtudhatja, hogyan Kafka használata a HDInsight a tároló lemezképeinek üzemeltetett Azure Kubernetes szolgáltatás (AKS).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: f54039a0e702aa3c789363969120e000760f6ef5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="use-azure-kubernetes-service-with-kafka-on-hdinsight"></a>Azure Kubernetes szolgáltatás használata a HDInsight Kafka

Ismerje meg, hogyan használható Azure Kubernetes szolgáltatás (AKS) Kafka HDInsight-fürt. A jelen dokumentumban leírt lépések AKS tárolt Node.js-alkalmazás használatával Kafka való kapcsolat ellenőrzésére. Ez az alkalmazás használja a [kafka-csomópont](https://www.npmjs.com/package/kafka-node) csomag Kafka folytatott kommunikációhoz. Használja [Socket.io](https://socket.io/) az esemény üzenetküldési a böngészőalapú ügyfél és a háttér-AKS üzemeltetett között.

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. Azure Kubernetes szolgáltatás a szolgáltatott Kubernetes környezet kezeli, és teszi gyorsan és egyszerűen indexelése alkalmazások központi telepítése. Egy Azure virtuális hálózatot használ, a két szolgáltatást is elérheti.

> [!NOTE]
> A jelen dokumentum elsősorban a HDInsight Kafka kommunikálni Azure Kubernetes szolgáltatás engedélyezése szükséges lépéseket. A példa magát a csak alapvető, annak bemutatásához, hogy működik-e a konfigurációs Kafka ügyfél.

## <a name="prerequisites"></a>Előfeltételek

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Azure-előfizetés

Jelen dokumentum céljából feltételezzük, hogy jártas létrehozása és használata az Azure-szolgáltatásokat:

* Kafka on HDInsight
* Azure Kubernetes szolgáltatás
* Azure virtuális hálózatok

Ez a dokumentum is feltételezi, hogy rendelkezik telefonon a [Azure Kubernetes szolgáltatás oktatóanyag](../../aks/tutorial-kubernetes-prepare-app.md). Ebben az oktatóanyagban létrehoz egy tároló szolgáltatást, egy Kubernetes fürtöt, a tároló beállításjegyzék hoz létre, és konfigurálja a `kubectl` segédprogram.

## <a name="architecture"></a>Architektúra

### <a name="network-topology"></a>Hálózati topológia

HDInsight és AKS is használják az Azure virtuális hálózat egy tárolót a számítási erőforrásokat. HDInsight és AKS közötti kommunikáció engedélyezéséhez engedélyeznie kell a hálózatok közötti kommunikációt. A jelen dokumentumban leírt lépések használja a virtuális hálózati társviszony-létesítés a hálózatokhoz. Más kapcsolatok, például VPN, is működnek. A társviszony-létesítés további információkért lásd: a [virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md) dokumentum.


A következő ábra szemlélteti a hálózati topológia itt:

![HDInsight egy virtuális hálózat, egy másik AKS és a társviszony-létesítés használatával csatlakoztatott hálózatok](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> Névfeloldás nem engedélyezett a peered hálózatok között, így az IP-címzés használatos. Alapértelmezés szerint a HDInsight Kafka állomásnév helyett IP-címek vissza, ha az ügyfelek van konfigurálva. A jelen dokumentumban leírt lépések módosításához az IP-címet használ Kafka hirdetési helyette.

## <a name="create-an-azure-kubernetes-service-aks"></a>Hozzon létre egy Azure Kubernetes szolgáltatást (AKS)

Ha még nem rendelkezik egy AKS fürthöz, a következő dokumentumokat segítségével megtudhatja, hogyan hozzon létre egyet:

* [Fürt üzembe helyezése Azure Kubernetes szolgáltatás (AKS) - portál](../../aks/kubernetes-walkthrough-portal.md)
* [Fürt üzembe helyezése Azure Kubernetes szolgáltatás (AKS) - parancssori felület](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> AKS a telepítés során létrehoz egy virtuális hálózatot. Ezen a hálózaton nincsenek társviszonyban, hogy a HDInsight a következő szakaszban létrehozott.

## <a name="configure-virtual-network-peering"></a>Konfigurálja a virtuális hálózati társviszony-létesítés

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be __erőforráscsoportok__, majd keresse meg a virtuális hálózat a AKS fürt tartalmazó erőforráscsoportot. Az erőforráscsoport neve `MC_<resourcegroup>_<akscluster>_<location>`. A `resourcegroup` és `akscluster` bezár a rendszer a fürthöz létrehozott erőforráscsoport nevét, és a fürt nevét. A `location` az a hely, amely a fürt sikeresen létrehozva.

2. Válassza ki az erőforráscsoportot, a __virtuális hálózati__ erőforrás.

3. Válassza ki __Címtéren__. Vegye figyelembe, hogy a címtartomány szerepel a listában.

4. Válassza ki a virtuális hálózat létrehozása a HDInsight, __+ hozzon létre egy erőforrást__, __hálózati__, majd __virtuális hálózati__.

    > [!IMPORTANT]
    > Az az érték megadásakor az új virtuális hálózat, egy, amely nem fedi át a AKS fürthálózat által használt címtartománnyal kell használnia.

    Ugyanazon __hely__ használja a virtuális hálózat, amely a AKS fürthöz.

    Várjon, amíg a virtuális hálózat létrehozása a következő lépés előtt.

5. A társviszony-létesítés között a HDInsight és a AKS fürt hálózat konfigurálásához válassza ki a virtuális hálózatot, és válassza ki __Társviszony__. Válassza ki __+ Hozzáadás__ és az űrlap feltölti a következő értékeket használja:

    * __Név__: Adjon meg egy egyedi nevet a társviszony-létesítési konfiguráció.
    * __Virtuális hálózati__: a mező használatával válassza ki a virtuális hálózatot a **AKS fürt**.

    Az alapértelmezett értéket az összes többi mezőt hagyja, és válasszon __OK__ konfigurálása a társviszony-létesítést.

6. A társviszony-létesítés között a AKS fürt és a HDInsight-hálózat konfigurálásához jelölje ki a __AKS fürt virtuális hálózati__, majd válassza ki __Társviszony__. Válassza ki __+ Hozzáadás__ és az űrlap feltölti a következő értékeket használja:

    * __Név__: Adjon meg egy egyedi nevet a társviszony-létesítési konfiguráció.
    * __Virtuális hálózati__: a mező használatával válassza ki a virtuális hálózatot a __HDInsight-fürt__.

    Az alapértelmezett értéket az összes többi mezőt hagyja, és válasszon __OK__ konfigurálása a társviszony-létesítést.

## <a name="install-kafka-on-hdinsight"></a>A HDInsight Kafka telepítése

A Kafka HDInsight-fürt létrehozásakor a HDInsight a korábban létrehozott virtuális hálózathoz kell csatlakoztatni. A Kafka fürtök létrehozásáról további információk: a [Kafka fürt létrehozása](apache-kafka-get-started.md) dokumentum.

> [!IMPORTANT]
> A fürt létrehozásakor kell használnia a __speciális beállítások__ csatlakozni a virtuális hálózathoz, amelyet a HDInsight hozott létre.

## <a name="configure-kafka-ip-advertising"></a>Kafka IP-közzététel konfigurálása

Az alábbi lépésekkel konfigurálhatja Kafka tartománynevek helyett IP-címek hivatkozik:

1. A webböngészőben nyissa https://CLUSTERNAME.azurehdinsight.net. Cserélje le __CLUSTERNAME__ HDInsight-fürt Kafka nevével.

    Amikor a rendszer kéri, használja a HTTPS-felhasználónevet és jelszót a fürthöz. Az Ambari webes felhasználói felületén, a fürt akkor jelenik meg.

2. Kafka adatok megtekintéséhez válassza ki a __Kafka__ a bal oldali listában.

    ![A kijelölt Kafka szolgáltatás lista](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Válassza ki, ha Kafka konfigurációs __Configs__ felső közepén.

    ![Kafka Configs hivatkozások](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Található a __kafka-env__ konfigurációs, adja meg `kafka-env` a a __szűrő__ jobb felső részén található.

    ![Kafka konfigurációját, kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Konfigurálja az IP-címek hivatkozik Kafka, vegye fel a következő szöveg alsó részén található a __kafka-env-sablon__ mező:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A felület, amely figyeli Kafka konfigurálásához írja be a következőt `listeners` a a __szűrő__ jobb felső részén található.

7. Minden hálózati interfészen figyelésére Kafka konfigurálásához módosítsa a __figyelői__ mezőről `PLAINTEXT://0.0.0.0:9092`.

8. A konfigurációs módosítások mentéséhez használja a __mentése__ gombra. Adja meg a módosítások leíró üzenet. Válassza ki __OK__ a módosítások mentése után.

    ![Mentse a konfigurációs gomb](./media/apache-kafka-azure-container-services/save-button.png)

9. Megakadályozhatja, hogy hibák Kafka újraindításakor a __szolgáltatás műveletek__ gombra, majd az __kapcsolja be a karbantartási mód__. Kattintson az OK gombra a művelet végrehajtásához.

    ![Szolgáltatási művelet, amely a kijelölt karbantartási bekapcsolása](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Indítsa újra a Kafka, használja a __indítsa újra a__ gombra, majd az __indítsa újra az összes érintett__. Erősítse meg az újraindítás, és használja a __OK__ gombra kattint, a művelet befejeződése után.

    ![Indítsa újra a gomb, újraindítással minden érintett](./media/apache-kafka-azure-container-services/restart-button.png)

11. A karbantartási mód letiltásához használja a __szolgáltatás műveletek__ gombra, majd az __kapcsolja ki a karbantartási mód__. Válassza ki **OK** a művelet végrehajtásához.

## <a name="test-the-configuration"></a>Tesztelje a konfigurációt

Ezen a ponton Kafka és Azure Kubernetes szolgáltatás van a peered virtuális hálózatok a kommunikáció. Ez a kapcsolat teszteléséhez tegye a következőket:

1. Hozzon létre egy Kafka témakör, amely a tesztelési alkalmazás használja. Létrehozásával kapcsolatos információkat Kafka témakörök, tekintse meg a [Kafka fürt létrehozása](apache-kafka-get-started.md) dokumentum.

2. Töltse le a mintaalkalmazás az [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Szerkessze a `index.js` fájlt, és módosítsa a következő sorokat:

    * `var topic = 'mytopic'`: A csere `mytopic` a Kafka a témakör az alkalmazás által használt nevével.
    * `var brokerHost = '176.16.0.13:9092`: A csere `176.16.0.13` a broker gazdagépek, a fürt egyik belső IP-címét.

        A belső IP-címének közvetítő hosts (workernodes) a fürtben, tekintse meg a [Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentum. Válassza ki az IP-címét egy bejegyzést, ahol a tartománynév kezdődik `wn`.

4. A parancssorból a `src` directory függőségek telepítése és a központi telepítési lemezképet készít a Docker segítségével:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Ez az alkalmazás által igényelt csomagokat a rendszer ellenőrzi a tárházba, így nem kell használni a `npm` segédprogram, hogy telepítse őket.

5. Jelentkezzen be az Azure tároló beállításjegyzék (ACR), és keresse meg loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Ha nem ismeri az Azure-tároló beállításjegyzék nevét vagy a biztosan ismeri az Azure CLI használata az Azure Kubernetes szolgáltatással tekintse meg a [AKS oktatóanyagok](../../aks/tutorial-kubernetes-prepare-app.md).

6. A helyi címke `kafka-aks-test` , az ACR loginServer lemezkép. Is hozzáadhat `:v1` jelzi a lemezkép verziója célból:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. A kép leküldése a beállításjegyzékben:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Ez a művelet több percet is igénybe vehet.

8. Szerkessze a Kubernetes jegyzékfájl (`kafka-aks-test.yaml`), és cserélje le `microsoft` a 4. lépésben beolvasott ACR loginServer névvel.

9. Az alábbi parancs segítségével a jegyzékfájlból alkalmazás-beállításokat léptethet érvénybe:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. A következő paranccsal figyelendő a `EXTERNAL-IP` az alkalmazás:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Ha egy külső IP-cím hozzá van rendelve, a __CTRL + C__ való kilépéshez a figyelés

11. Nyisson meg egy webböngészőt, és adja meg a külső IP-címet a szolgáltatáshoz. Az alábbi képen hasonló lap elvégzésével:

    ![A weblap képe](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Szöveg bevitele a mezőbe, és válassza ki a __küldése__ gombra. Az adatok Kafka érkezik. Ezt követően az alkalmazás Kafka fogyasztó kiolvassa az üzenetet, és hozzáadja azt, hogy a __Kafka üzeneteit__ szakasz.

    > [!WARNING]
    > Többszörös lemásolását, egy üzenet jelenhet meg. Ez a probléma általában frissítse a böngészőt csatlakoztatása után történik, vagy nyissa meg az alkalmazás több böngésző kapcsolatot.

## <a name="next-steps"></a>További lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [A HDInsighton futó Kafka használatának első lépései](apache-kafka-get-started.md)

* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](apache-kafka-mirroring.md)

* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)

* [Az Apache Spark használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)

* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)
