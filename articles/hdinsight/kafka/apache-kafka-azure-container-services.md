---
title: Azure Kubernetes szolgáltatás használata a Kafkával a HDInsighton
description: Ismerje meg, hogyan használhatja a Kafka a HDInsight az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett tárolórendszerképek.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 6abb4f632535f1bda7e9f337f111ba372a624f2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239622"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Azure Kubernetes szolgáltatás használata az Apache Kafkával a HDInsighton

Ismerje meg, hogyan használhatja az Azure Kubernetes-szolgáltatást (AKS) [az Apache Kafkával](https://kafka.apache.org/) a HDInsight-fürtön. A jelen dokumentum lépései az AKS-ben üzemeltetett Node.js alkalmazás használatával ellenőrzik a Kafka-val való kapcsolatot. Ez az alkalmazás a [kafka-csomópont](https://www.npmjs.com/package/kafka-node) csomagot használja a Kafka-val való kommunikációhoz. A [Socket.io](https://socket.io/) használ a böngészőügyfél és az AKS-ben üzemeltetett háttérrendszer közötti eseményvezérelt üzenetküldéshez.

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. Az Azure Kubernetes-szolgáltatás kezeli a üzemeltetett Kubernetes-környezetet, és gyorsan és egyszerűen üzembe helyezhető a tárolóba helyezett alkalmazások. Az Azure virtuális hálózat használatával csatlakoztathatja a két szolgáltatást.

> [!NOTE]  
> A dokumentum középpontjában a szükséges lépéseket, amelyek lehetővé teszik az Azure Kubernetes szolgáltatás kommunikálni Kafka a HDInsight. Maga a példa csak egy egyszerű Kafka-ügyfél annak bizonyítására, hogy a konfiguráció működik.

## <a name="prerequisites"></a>Előfeltételek

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Azure-előfizetés

Ez a dokumentum feltételezi, hogy ismeri a következő Azure-szolgáltatások létrehozását és használatát:

* Kafka on HDInsight
* Azure Kubernetes Service
* Azure virtuális hálózatok

Ez a dokumentum azt is feltételezi, hogy végigment az [Azure Kubernetes szolgáltatás oktatóanyagán.](../../aks/tutorial-kubernetes-prepare-app.md) Ez a cikk létrehoz egy tárolószolgáltatást, létrehoz egy Kubernetes-fürtöt, egy tároló-beállításjegyzéket, és konfigurálja a `kubectl` segédprogramot.

## <a name="architecture"></a>Architektúra

### <a name="network-topology"></a>Hálózati topológia

A HDInsight és az AKS egyaránt egy Azure virtuális hálózatot használ számítási erőforrások tárolójaként. A HDInsight és az AKS közötti kommunikáció engedélyezéséhez engedélyeznie kell a hálózatok közötti kommunikációt. A jelen dokumentum lépései a virtuális hálózati társviszony-létesítést használják a hálózatokhoz. Más kapcsolatoknak, például a VPN-nek is működniük kell. A társviszony-létesítésről további információt a [virtuális hálózati társviszony-létesítési](../../virtual-network/virtual-network-peering-overview.md) dokumentumban talál.

Az alábbi ábra a jelen dokumentumban használt hálózati topológiát mutatja be:

![HDInsight egy virtuális hálózatban, AKS egy másikban, társviszony-létesítés használatával](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> A névfeloldás nincs engedélyezve a társviszony-létesített hálózatok között, ezért az IP-címzést használja a program. Alapértelmezés szerint a Kafka a HDInsight-on úgy van beállítva, hogy az ügyfelek csatlakozásakor ip-címek helyett állomásneveket adjon vissza. A jelen dokumentum lépései úgy módosítják a Kafkát, hogy ip-hirdetéseket használjon helyette.

## <a name="create-an-azure-kubernetes-service-aks"></a>Azure Kubernetes-szolgáltatás (AKS) létrehozása

Ha még nem rendelkezik AKS-fürttel, az alábbi dokumentumok egyikével megtudhatja, hogyan hozhat létre egyet:

* [Azure Kubernetes-szolgáltatás (AKS) fürt jének üzembe helyezése – Portál](../../aks/kubernetes-walkthrough-portal.md)
* [Azure Kubernetes-szolgáltatás (AKS) fürt telepítése – CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> Az AKS virtuális hálózatot hoz létre egy **további** erőforráscsoportban történő telepítés során. A további erőforráscsoport követi a **MC_resourceGroup_AKSclusterName_location**elnevezési konvencióját.  
> Ez a hálózat a következő szakaszban a HDInsight számára létrehozott hálózatra tekint társviszonyban.

## <a name="configure-virtual-network-peering"></a>Virtuális hálózati társviszony-létesítés konfigurálása

### <a name="identify-preliminary-information"></a>Előzetes információk azonosítása

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a további **erőforráscsoportot,** amely az AKS-fürt virtuális hálózatát tartalmazza.

2. Az erőforráscsoportból válassza ki a __Virtuális hálózati__ erőforrást. Jegyezze fel a nevet későbbi felhasználásra.

3. A **Beállítások csoportban**válassza a __Címterület lehetőséget.__ Jegyezze fel a felsorolt címterületet.

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

1. Virtuális hálózat létrehozásához a HDInsight számára keresse meg __a + Erőforrás létrehozása__ > __hálózati__ > __virtuális hálózat__.

1. Hozza létre a hálózatot az alábbi irányelvek alapján bizonyos tulajdonságokhoz:

    |Tulajdonság | Érték |
    |---|---|
    |Címtér|Olyan címterületet kell használnia, amely nem fedi át az AKS-fürthálózat által használt területet.|
    |Hely|Használja ugyanazt a __helyet__ az AKS-fürthöz használt virtuális hálózathoz.|

1. Várjon, amíg a virtuális hálózat létrejött, mielőtt a következő lépésre lépne.

### <a name="configure-peering"></a>Társviszony-létesítés konfigurálása

1. A HDInsight-hálózat és az AKS-fürthálózat közötti társviszony-létesítés konfigurálásához jelölje ki a virtuális hálózatot, majd válassza a __Társviszony-létesítések__lehetőséget.

1. Válassza a + Add and use __(Hozzáadás__ és a következő értékek) lehetőséget az űrlap feltöltéséhez:

    |Tulajdonság |Érték |
    |---|---|
    |A Virtuális hálózat \<> távoli virtuális hálózatra létesítésének neve|Adjon meg egy egyedi nevet a társviszony-létesítési konfigurációnak.|
    |Virtuális hálózat|válassza ki az **AKS-fürt virtuális hálózatát.**|
    |Az AKS \<VN> társviszony-létesítésének neve erre a \<virtuális>|Adjon meg egy egyedi nevet.|

    Hagyja az összes többi mezőt az alapértelmezett értéken, majd a társviszony-létesítés konfigurálásához válassza az __OK__ gombot.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Apache Kafka-fürt létrehozása a HDInsighton

A Kafka HDInsight-fürtön történő létrehozásakor csatlakoznia kell a HDInsight számára korábban létrehozott virtuális hálózathoz. A Kafka-fürt létrehozásáról további információt az [Apache Kafka fürt létrehozása](apache-kafka-get-started.md) dokumentum ban talál.

## <a name="configure-apache-kafka-ip-advertising"></a>Az Apache Kafka IP-hirdetések konfigurálása

A következő lépésekkel konfigurálhatja a Kafka-t az IP-címek tartománynevek helyett történő hirdetésére:

1. Egy böngészőben nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le a CLUSTERNAME nevet a HDInsight-fürt Kafka nevére.

    Amikor a rendszer kéri, használja a fürt HTTPS-felhasználónevét és jelszavát. Megjelenik a fürt Ambari webfelhasználói felülete.

2. A Kafka-val kapcsolatos információk megtekintéséhez válassza a __Kafka__ elemet a bal oldali listából.

    ![Szolgáltatási lista kiemelt Kafkával](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. A Kafka-konfiguráció megtekintéséhez válassza a __Konfigurációk__ elemet a felső középről.

    ![Apache Ambari szolgáltatások konfigurációja](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. A __kafka-env__ konfiguráció megkereséséhez írja be `kafka-env` a jobb felső __sarokszűrő__ mezőjét.

    ![Kafka konfiguráció, kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Ha a Kafkát az IP-címek hirdetésére szeretné beállítani, adja hozzá a következő szöveget a __kafka-env-template__ mező aljához:

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A Kafka által figyelt összeköttetés konfigurálásához írja be `listeners` a jobb felső __sarokszűrő__ mezőjébe.

7. Ha azt szeretné, hogy a Kafka az összes hálózati `PLAINTEXT://0.0.0.0:9092`csatolón figyelje a figyelést, módosítsa a __figyelők__ mező értékét a értékre.

8. A konfigurációs módosítások mentéséhez használja a __Mentés__ gombot. Írjon be egy szöveges üzenetet a módosításokról. A módosítások mentése után válassza az __OK__ gombot.

    ![Apache Ambari menteni konfiguráció](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. A Kafka újraindításakor előforduló hibák elkerülése érdekében használja a __Szolgáltatási műveletek__ gombot, és válassza a Karbantartási __mód bekapcsolása lehetőséget.__ A művelet végrehajtásához válassza az OK gombot.

    ![Szolgáltatási műveletek, kiemelve a karbantartás bekapcsolva](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. A Kafka újraindításához használja az __Újraindítás__ gombot, és válassza __az Újraindítás minden érintettet.__ Erősítse meg az újraindítást, majd a művelet befejezése után használja az __OK__ gombot.

    ![Újraindítás gomb az összes érintett érintett újraindításával kiemelve](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. A karbantartási mód letiltásához használja a __Szervizműveletek__ gombot, és válassza __a Karbantartási mód kikapcsolása__lehetőséget. A művelet végrehajtásához válassza az **OK** gombot.

## <a name="test-the-configuration"></a>A konfiguráció tesztelése

Ezen a ponton a Kafka és az Azure Kubernetes-szolgáltatás a társviszony-létesített virtuális hálózatokon keresztül kommunikál. A kapcsolat teszteléséhez kövesse az alábbi lépéseket:

1. Hozzon létre egy Kafka-témakört, amelyet a tesztalkalmazás használ. A Kafka-témakörök létrehozásáról az [Apache Kafka fürtlétrehozása](apache-kafka-get-started.md) című dokumentumban olvashat bővebben.

2. Töltse le a [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test)példaalkalmazást a alkalmazásból.

3. Módosítsa a `index.js` fájlt, és módosítsa a következő sorokat:

    * `var topic = 'mytopic'`: `mytopic` Cserélje ki az alkalmazás által használt Kafka-témakör nevét.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Cserélje le a fürt egyik brókerállomásának belső IP-címét.

        A közvetítői állomások (workernodes) belső IP-címének megkereséséhez tekintse meg az [Apache Ambari REST API-dokumentumot.](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) Válassza ki az onkóbejegyzés ek valamelyikének IP-címét, amelynek a tartománynév kezdődik. `wn`

4. A címtár parancssorából telepítse a `src` függőségeket, és a Docker segítségével hozzon létre egy lemezképet a telepítéshez:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Az alkalmazás által igényelt csomagok bevannak jelentkezve a tárházba, így nem kell a segédprogramot használnia a `npm` telepítésükhöz.

5. Jelentkezzen be az Azure Container Registry (ACR) szolgáltatásba, és keresse meg a loginServer nevét:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Ha nem ismeri az Azure Container Registry nevét, vagy nem ismeri az Azure CLI használatát az Azure Kubernetes szolgáltatással való együttműködésre, tekintse meg az [AKS-oktatóanyagokat.](../../aks/tutorial-kubernetes-prepare-app.md)

6. Címkézze `kafka-aks-test` fel a helyi lemezképet az ACR loginServer szolgáltatásával. Is `:v1` hozzá, hogy a végén, hogy jelezze a kép verziója:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. A lemezkép leküldése a rendszerleíró adatbázisba:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    A művelet több percet vesz igénybe.

8. Írja be a Kubernetes`kafka-aks-test.yaml`jegyzékfájlját ( ) , és cserélje le `microsoft` a 4.

9. Az alkalmazásbeállítások at a jegyzékfájlból telepítheti a következő paranccsal:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Az alkalmazás `EXTERNAL-IP` figyeléséhez használja a következő parancsot:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Külső IP-cím hozzárendelése után a __CTRL + C billentyűkombinációval__ lépjen ki az óra

11. Nyisson meg egy webböngészőt, és adja meg a szolgáltatás külső IP-címét. Az alábbihoz hasonló oldalra érkezik:

    ![Apache Kafka teszt weboldal kép](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Írjon be szöveget a mezőbe, majd válassza a __Küldés__ gombot. Az adatokat a rendszer elküldi Kafkának. Ezután a Kafka-fogyasztó az alkalmazásban beolvassa az üzenetet, és hozzáadja a __Kafka üzenetek__ szakaszhoz.

    > [!WARNING]  
    > Egy üzenet több példányát is megkaphatja. Ez a probléma általában akkor fordul elő, ha a csatlakozás után frissíti a böngészőt, vagy több böngészőkapcsolatot nyit meg az alkalmazással.

## <a name="next-steps"></a>További lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [Ismerkedés az Apache Kafkával a HDInsighton](apache-kafka-get-started.md)

* [A MirrorMaker segítségével létrehozhatja az Apache Kafka másolatát a HDInsight-on](apache-kafka-mirroring.md)

* [Az Apache Storm használata az Apache Kafkával a HDInsighton](../hdinsight-apache-storm-with-kafka.md)

* [Az Apache Spark használata az Apache Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)

* [Csatlakozás az Apache Kafkához egy Azure virtuális hálózaton keresztül](apache-kafka-connect-vpn-gateway.md)
