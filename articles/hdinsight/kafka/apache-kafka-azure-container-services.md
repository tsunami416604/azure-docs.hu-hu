---
title: Az Azure Kubernetes Service használata a HDInsight alatt futó Kafka
description: Útmutató a HDInsight Kafka használata az Azure Kubernetes Service (AKS) lévő üzemeltetett tárolórendszerképek.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 5d855c2747194ccdeb9f5cdbe07e25c96dedc9b6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041993"
---
# <a name="use-azure-kubernetes-service-with-kafka-on-hdinsight"></a>Az Azure Kubernetes Service használata a HDInsight alatt futó Kafka

Ismerje meg, a Kafka HDInsight-fürtön az Azure Kubernetes Service (AKS) használata. A jelen dokumentumban leírt lépések az aks-ben üzemeltetett Node.js-alkalmazás használatával ellenőrizze a kapcsolatot a Kafka. Ez az alkalmazás használ a [kafka-csomópont](https://www.npmjs.com/package/kafka-node) kommunikálni a Kafka-csomagot. Használ [a Socket.IO kódtár](https://socket.io/) a böngészőalapú ügyfél és a háttéralkalmazás az aks-ben üzemeltetett közötti üzenetek eseményvezérelt számára.

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. Az Azure Kubernetes Service felügyeli az üzemeltetett Kubernetes környezetet, és gyorsan és egyszerűen üzembe helyezhetők a tárolóalapú alkalmazásokat. Az Azure Virtual Network használatával, a két szolgáltatás is csatlakoztathatja.

> [!NOTE]
> A jelen dokumentum célja az Azure Kubernetes Service kommunikálni a HDInsight alatt futó Kafka engedélyezéséhez szükséges lépéseket. Maga a példában csak alapszintű Kafka ügyfél bemutatásához, hogy működik-e a konfigurációs.

## <a name="prerequisites"></a>Előfeltételek

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Azure-előfizetés

Jelen dokumentum céljából feltételezzük, hogy Ön ismeri a létrehozásáról és használatáról a következő Azure-szolgáltatások:

* Kafka on HDInsight
* Azure Kubernetes Service
* Azure virtuális hálózatok

Jelen dokumentum céljából feltételezzük is, hogy rendelkezik bizonnyal a [Azure Kubernetes Service-oktatóanyag](../../aks/tutorial-kubernetes-prepare-app.md). Ebben az oktatóanyagban létrehoz egy container service létrehoz egy Kubernetes-fürtöt, egy tároló-beállításjegyzéket, és konfigurálja a `kubectl` segédprogramot.

## <a name="architecture"></a>Architektúra

### <a name="network-topology"></a>Hálózati topológia

HDInsight és az AKS használata az Azure Virtual Network tárolójaként számítási erőforrásokat. HDInsight és az AKS közötti kommunikáció engedélyezéséhez engedélyeznie kell a hálózatok közötti adatforgalmat. A jelen dokumentumban leírt lépések használja a virtuális hálózatok közötti Társviszony-hálózatokhoz. Egyéb kapcsolatok, például VPN is működnie kell. A társviszony-létesítés további információkért lásd: a [virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md) dokumentumot.


A következő ábra szemlélteti a hálózati topológiát, ebben a dokumentumban használt:

![Egy virtuális hálózatot, egy másik AKS és a hálózatok társviszony-létesítés használatával összekapcsolt HDInsight](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> Névfeloldás a társviszonyban lévő hálózatok, így használt IP-címkezelés között nincs engedélyezve. Alapértelmezés szerint a HDInsight alatt futó Kafka vissza az állomásneveket IP-címek helyett, ha az ügyfelek csatlakoznak van konfigurálva. A jelen dokumentumban leírt lépések módosítása IP használata a Kafka hirdetési helyette.

## <a name="create-an-azure-kubernetes-service-aks"></a>Hozzon létre egy Azure Kubernetes Service (AKS)

Ha Ön még nem rendelkezik egy AKS-fürtöt, egyet a következő dokumentumok segítségével megtudhatja, hogyan hozhat létre egyet:

* [Fürt üzembe helyezése az Azure Kubernetes Service (AKS) – portál](../../aks/kubernetes-walkthrough-portal.md)
* [Fürt üzembe helyezése az Azure Kubernetes Service (AKS) – CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> Az AKS a telepítés során létrehoz egy virtuális hálózatot. Ez a hálózat társviszonyban áll, a HDInsight a következő szakaszban létrehozott.

## <a name="configure-virtual-network-peering"></a>Konfigurálja a virtuális hálózatok közötti társviszony

1. Az a [az Azure portal](https://portal.azure.com)válassza __erőforráscsoportok__, majd keresse meg az erőforráscsoport, amely tartalmazza a virtuális hálózat számára az AKS-fürt. Az erőforráscsoport neve `MC_<resourcegroup>_<akscluster>_<location>`. A `resourcegroup` és `akscluster` bejegyzései a fürthöz létrehozott erőforráscsoport nevét, és a fürt nevére. A `location` az a hely, amely a fürt sikeresen létrehozva.

2. Az erőforráscsoportban, válassza ki a __virtuális hálózati__ erőforrás.

3. Válassza ki __címtér__. Vegye figyelembe, hogy a címtér szerepel.

4. A HDInsight virtuális hálózat létrehozásához válassza __+ erőforrás létrehozása__, __hálózatkezelés__, majd __virtuális hálózati__.

    > [!IMPORTANT]
    > Az új virtuális hálózat az értékek megadásakor egy címtér, amely nincs átfedésben az AKS-fürt hálózati által használt kell használnia.

    Ugyanaz, mint __hely__ a virtuális hálózati az AKS-fürtöt használt.

    Várjon, amíg a virtuális hálózat létrehozása a következő lépés előtt.

5. Konfigurálhatja a társviszony-létesítést a HDInsight-hálózat és az AKS-fürt hálózati között, válassza ki a virtuális hálózatot, majd __Társviszonyok__. Válassza ki __+ Hozzáadás__ és feltölti az űrlapot a következő értékeket használja:

    * __Név__: Adjon meg egy egyedi nevet ehhez a társviszony-létesítési konfigurációhoz.
    * __Virtuális hálózat__: Ez a mező használatával válassza ki a virtuális hálózat számára a **AKS-fürt**.

    Az alapértelmezett értéket az összes többi mezőt hagyja, majd válassza a __OK__ konfigurálhatja a társviszony-létesítést.

6. Konfigurálhatja a társviszony-létesítést az AKS-fürt hálózati és a HDInsight-hálózat között, válassza ki a __AKS-fürt virtuális hálózati__, majd válassza ki __Társviszonyok__. Válassza ki __+ Hozzáadás__ és feltölti az űrlapot a következő értékeket használja:

    * __Név__: Adjon meg egy egyedi nevet ehhez a társviszony-létesítési konfigurációhoz.
    * __Virtuális hálózat__: Ez a mező használatával válassza ki a virtuális hálózat számára a __HDInsight-fürt__.

    Az alapértelmezett értéket az összes többi mezőt hagyja, majd válassza a __OK__ konfigurálhatja a társviszony-létesítést.

## <a name="install-kafka-on-hdinsight"></a>A HDInsight Kafka telepítése

A Kafka HDInsight-fürt létrehozásakor a HDInsight a korábban létrehozott virtuális hálózathoz kell csatlakoztatni. Kafka-fürt létrehozásával kapcsolatos további információkért lásd: a [Kafka-fürt létrehozása](apache-kafka-get-started.md) dokumentumot.

> [!IMPORTANT]
> A fürt létrehozásakor kell használnia a __speciális beállítások__ csatlakozni a virtuális hálózat, HDInsight létrehozott.

## <a name="configure-kafka-ip-advertising"></a>A Kafka IP hirdetés konfigurálása

A következő lépéseket követve konfigurálja Kafka meghirdetése tartománynevek helyett IP-címek:

1. Egy böngészőben nyissa meg https://CLUSTERNAME.azurehdinsight.net. Cserélje le __CLUSTERNAME__ a Kafka on HDInsight-fürt nevére.

    Amikor a rendszer kéri, használja a HTTPS-felhasználónevet és jelszót a fürthöz. Az Ambari webes felhasználói Felületet, a fürt akkor jelenik meg.

2. A Kafka információ megtekintéséhez jelölje ki __Kafka__ a bal oldali listából.

    ![A Kafka lista kiemelésével](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Kafka-konfigurációt megtekintéséhez jelölje ki __Configs__ felső közepén.

    ![A Kafka Configs hivatkozások](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Keresése a __kafka-env__ konfigurációs, adja meg `kafka-env` a a __szűrő__ a jobb felső sarokban található.

    ![Kafka-konfigurációt, a kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Kafka meghirdetése IP-címek konfigurálásához adja hozzá a következő szöveget a alján a __kafka-boríték-template__ mező:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A Kafka figyel-kapcsolat konfigurálásához írja be a `listeners` a a __szűrő__ a jobb felső sarokban található.

7. Figyeljen az összes hálózati adapteren Kafka konfigurálásához módosítsa az értéket a __figyelői__ mezőt `PLAINTEXT://0.0.0.0:9092`.

8. A konfigurációs módosítások mentéséhez használja a __mentése__ gombra. Adja meg a módosításokat leíró szöveges üzenetet. Válassza ki __OK__ után a rendszer mentette a módosításokat.

    ![Mentés gomb konfigurálása](./media/apache-kafka-azure-container-services/save-button.png)

9. Hibák a Kafka újraindításához használja a __szolgáltatás műveletek__ gombra, majd __karbantartási mód bekapcsolása__. Kattintson az OK gombra a művelet végrehajtásához.

    ![Szolgáltatási művelet, amely a kiemelt karbantartási bekapcsolása](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Indítsa újra a Kafka, használja a __indítsa újra a__ gombra, majd __indítsa újra az összes érintett__. Erősítse meg az újraindítást, és használja a __OK__ gombra a művelet befejeződése után.

    ![Indítsa újra az összes érintett újraindítás gomb kiemelésével](./media/apache-kafka-azure-container-services/restart-button.png)

11. Tiltsa le a karbantartási mód, használja a __szolgáltatás műveletek__ gombra, majd __kapcsolja ki karbantartási módba__. Válassza ki **OK** végrehajtani a műveletet.

## <a name="test-the-configuration"></a>A konfiguráció tesztelése

Ezen a ponton a Kafka és az Azure Kubernetes Service szerepelnek, a társviszonyban álló virtuális hálózatba keresztüli kommunikációt. Ez a kapcsolat teszteléséhez használja a következő lépéseket:

1. Egy Kafka-témakört, amely a test-alkalmazás létrehozása. Kafka-témakörök létrehozásával kapcsolatos információkért lásd: a [Kafka-fürt létrehozása](apache-kafka-get-started.md) dokumentumot.

2. A mintaalkalmazás letöltése [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Szerkessze a `index.js` fájlt, és módosítsa a következő sorokat:

    * `var topic = 'mytopic'`: A csere `mytopic` a Kafka-témakörbe az alkalmazás által használt nevét.
    * `var brokerHost = '176.16.0.13:9092`: A csere `176.16.0.13` a közvetítő gazdagépek, a fürt egyik belső IP-címét.

        A belső IP-címének a közvetítő gazdagépek (workernodes) a fürtben, tekintse meg a [az Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentumot. Válasszon ki egy bejegyzést a tartománynév kezdődik, az IP-cím `wn`.

4. A parancssorból a `src` directory függőségek telepítése és a központi telepítési lemezképet készít a Docker használatával:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Ez az alkalmazás által igényelt csomagokat ellenőrzi a tárházba, így Önnek nem kell használni a `npm` segédprogram, hogy telepítse őket.

5. Jelentkezzen be az Azure Container Registry (ACR), és keresse meg a kiszolgáló nevének lekéréséhez:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Ha nem ismeri az Azure Container Registry neve, vagy nem tudja dolgozni az Azure Kubernetes Service-ben az Azure CLI használatával tekintse meg a [AKS oktatóanyagait](../../aks/tutorial-kubernetes-prepare-app.md).

6. A helyi címkézése `kafka-aks-test` rendszerképet, az ACR bejelentkezési kiszolgálójának nevével. Is hozzáadhat `:v1` jelzi a rendszerkép verziószámát vége:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. A rendszerkép leküldése a beállításjegyzékbe:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Ez a művelet több percet vesz igénybe.

8. A Kubernetes-jegyzékfájl szerkesztése (`kafka-aks-test.yaml`), és cserélje le `microsoft` a 4. lépésben lekért az ACR bejelentkezési kiszolgálójának nevét.

9. Használja a következő parancsot a jegyzékfájl az alkalmazás-beállításokat:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. A következő paranccsal tekintse meg a a `EXTERNAL-IP` az alkalmazás:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Miután a külső IP-cím hozzá van rendelve, a __CTRL + C__ kilép az órával

11. Nyisson meg egy webböngészőt, és adja meg a külső IP-cím a szolgáltatáshoz. Az alábbi képhez hasonló lap érkezik:

    ![A webalkalmazás lapjának képe](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Az mezőbe írja be a szöveget, majd a __küldése__ gombra. A Kafka küldi az adatokat. Az alkalmazás a Kafka-fogyasztók kiolvassa az üzenetet, és hozzáadja azt, majd a __Kafka üzeneteit__ szakaszban.

    > [!WARNING]
    > Több példány, egy üzenet jelenhet meg. Ez a probléma általában történik, ha a csatlakozás után, frissítse a böngészőt, vagy nyissa meg az alkalmazás több böngésző kapcsolatot.

## <a name="next-steps"></a>További lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [A HDInsighton futó Kafka használatának első lépései](apache-kafka-get-started.md)

* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](apache-kafka-mirroring.md)

* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)

* [Az Apache Spark használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)

* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)
