---
title: A HDInsight - Azure Machine Learning-szolgáltatások üzembe helyezése
description: Útmutató az Azure HDInsight Machine Learning-szolgáltatások üzembe helyezése.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: ce701c029c63256714452aa13f646af77991cb67
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496922"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Machine Learning-szolgáltatások az Azure HDInsight-fürt üzembe helyezése

Után végezze el az adatok modellezése a HDInsight fürt Machine Learning-szolgáltatások már használta, a modellt, hogy előrejelzéseket végezzen működésbe hozhat. Ez a cikk útmutatást Ez a feladat elvégzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Machine Learning-szolgáltatások HDInsight-fürt**: útmutatásért lásd: [Ismerkedés a Machine Learning szolgáltatások a HDInsight](r-server-get-started.md).

* **Secure Shell- (SSH-) ügyfél**: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Egy beépített konfigurációban a Machine Learning-szolgáltatások-fürt üzembe helyezése

> [!NOTE]
> Az alábbi lépéseket az R Server 9.0 és gépi Tanulási Server 9.1 vonatkoznak. ML Server 9.3, tekintse meg a [az eszköz használata kezelheti az operacionalizálás konfigurációja](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Az SSH használata Azure HDInsight útmutatásért lásd: [használata SSH használata a HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Módosítsa a könyvtárat a megfelelő verzió és a sudo dot net dll parancsot: 

    - A Microsoft ML Server 9.1 esetén:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R Server 9.0 esetén:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Megjelennek a lehetőségek közül választhat. Válassza ki az első lehetőség, ahogyan az alábbi képernyőképen a **ML Server konfigurálása a működőképessé**.

    ![one box op](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Most már megjelennek a lehetőséget, kiválaszthatja, hogyan szeretné ML Server üzembe helyezése. Az itt bemutatott közül válassza ki az első adjon meg egy **A**.

    ![one box op](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Amikor a rendszer kéri, adja meg, és írja be újra a helyi rendszergazda felhasználó jelszavát.

1. Javasolja, hogy a művelet sikeres volt-e a kimenetek kell megjelennie. Is kéri, válassza ki egy másik lehetőséget a menüből. Lépjen vissza a főmenübe válassza E.

    ![one box op](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Diagnosztikai ellenőrzéseket végezhet szükség esetén a következő diagnosztikai tesztelés futtatásával:

    a. Válassza ki a főmenü **6** diagnosztikai tesztek futtatásához.

    ![one box op](./media/r-server-operationalize/diagnostic-1.png)

    b. A diagnosztikai tesztek menüből válassza ki a **A**. Amikor a rendszer kéri, adja meg a jelszót a helyi rendszergazdai felhasználó számára megadott.

    ![one box op](./media/r-server-operationalize/diagnostic-2.png)

    c. Győződjön meg arról, hogy a kimenet látható, hogy általános állapotát egy pass.

    ![one box op](./media/r-server-operationalize/diagnostic-3.png)

    d. A menüelemekért jelenik meg, adja meg a **E** térjen vissza a főmenübe, majd **8** , lépjen ki az admin segédprogramból.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Hosszú késések használatakor az Apache Spark-webszolgáltatása

Ha hosszú késleltetést tapasztal, amikor a létrehozott webszolgáltatások használata próbál számítási környezetben mrsdeploy függvényekkel egy Apache Spark, szükség lehet hozzá néhány hiányzó mappát. A Spark-alkalmazás egy *rserve2* nevű felhasználóhoz tartozik, ha egy mrsdeploy függvényeket használó webszolgáltatásból hívja meg. Megkerülő megoldás a problémára:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Az operacionalizálás konfigurációja ezzel befejeződött. Most már használhatja a `mrsdeploy` csomagot rclientben, hogy kapcsolódhasson az élcsomóponti operacionalizáláshoz, és annak szolgáltatásait, például használatának megkezdéséhez [távoli végrehajtás](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) és [webszolgáltatás](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Attól függően, hogy fürt virtuális hálózaton van-e beállítva, szükség lehet porttovábbító bújtatás kialakítására SSH-bejelentkezésen keresztül. Az alábbi szakaszok ismertetik, hogyan állíthatja be ezt az alagutat.

### <a name="ml-services-cluster-on-virtual-network"></a>Machine Learning-szolgáltatások fürt virtuális hálózaton

Bizonyosodjon meg róla, hogy engedélyezett a forgalom az 12800-as porton az élcsomópont felé. Így az élcsomópont használatával kapcsolódhat az operacionalizálási szolgáltatáshoz.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Ha a `remoteLogin()` metódus nem tud kapcsolódni az élcsomóponthoz, de SSH-n be tud jelentkezni az élcsomópontba, győződjön meg róla, hogy a szabály, amely engedélyezi a forgalmat az 12800-as porton, megfelelően van-e beállítva. Ha a probléma továbbra is jelentkezik, egy másik megoldás segítségével is beállíthat porttovábbító alagutat az SSH-n keresztül. Útmutatásért tekintse meg a következő szakaszt:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Machine Learning-szolgáltatások fürt virtuális hálózaton nincs beállítva

Ha a fürt nem a virtuális hálózaton van beállítva vagy problémás a kapcsolódás a virtuális hálózaton keresztül, akkor használhatja az SSH porttovábbító alagutat:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Ha az SSH-munkamenet aktív, a helyi számítógép 12800-as porton érkező forgalom az 12800-as porton az élcsomópont SSH-munkameneten keresztül továbbítja. A `127.0.0.1:12800` címet használja a `remoteLogin()` metódusban. Ez bejelentkezik az élcsomópontra porttovábbításon keresztül operacionalizálásra.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Üzembe helyezte azt méretezni a számítási csomópontok, a HDInsight feldolgozó csomópontjain

Méretezni a számítási csomópontok, először a feldolgozó csomópontok leszerelése, majd válassza a számítási csomópontok a leszerelt feldolgozó csomóponton.

### <a name="step-1-decommission-the-worker-nodes"></a>1. lépés: A feldolgozó csomópontok leszerelése

Machine Learning Services fürtje nem kezelhető [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Ha a feldolgozó csomópontokat nem szereli, a YARN Resource Manager nem működik, mert nem az erőforrásokat, a kiszolgáló által elvárt módon. Ennek a helyzetnek az elkerülésére javasoljuk a feldolgozó csomópontok leszerelését a számítási csomópontok horizontális felskálázása előtt.

Kövesse az alábbi lépéseket a feldolgozó csomópontok leszerelése:

1. Jelentkezzen be a fürt Ambari konzoljába, és kattintson a **gazdagépek** fülre.

1. Válassza ki a munkavégző csomópontok (le kell szerelni).

1. Kattintson a **műveletek** > **kiválasztott gazdagépek** > **gazdagépek** > **karbantartási mód bekapcsolása**. A következő képen például a wn3 és a wn4 pontokat választottuk ki leszerelésre.  

   ![feldolgozó csomópont(ok) leszerelése](./media/r-server-operationalize/get-started-operationalization.png)  

* Válassza ki **műveletek** > **Selected Hosts** > **DataNodes** > kattintson **Decommission**.
* Válassza ki **műveletek** > **Selected Hosts** > **NodeManagers** > kattintson **Decommission**.
* Válassza ki **műveletek** > **Selected Hosts** > **DataNodes** > kattintson **leállítása**.
* Válassza ki **műveletek** > **Selected Hosts** > **NodeManagers** > kattintson a **leállítása**.
* Válassza ki **műveletek** > **Selected Hosts** > **gazdagépek** > kattintson **az összes összetevő leállítása**.
* Szüntesse meg a feldolgozó csomópontok kijelölését, és jelölje ki az átjárócsomópontokat.
* Válassza ki **műveletek** > **kiválasztott gazdagépek** > "**gazdagépek** > **összes összetevő újraindítása**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>2. lépés: Konfigurálja számítási csomópontok az összes leszerelt feldolgozó csomóponton

1. Jelentkezzen be SSH-n keresztül minden egyes leszerelt feldolgozó csomópontba.

1. Futtassa az admin segédprogramot a Machine Learning-szolgáltatások fürt, amely rendelkezik a megfelelő dll-fájl használatával. ML Server 9.1 futtassa a következő parancsot:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Adja meg **1** lehetőség kiválasztásához **ML Server konfigurálása a működőképessé**.

1. Adja meg **C** lehetőség kiválasztásához `C. Compute node`. Ez konfigurálja a számítási csomópontot a feldolgozó csomóponton.

1. Lépjen ki az admin segédprogramból.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>3. lépés: Adja hozzá a számítási csomópontok részleteinek a web csomóponton

Az összes leszerelt feldolgozó csomópontok futtatásához a számítási csomópont konfigurálása után térjen vissza az élcsomóponti operacionalizáláshoz, és adja hozzá a leszerelt feldolgozó csomópontok IP-címek az ML Server webcsomópontjának konfigurációjában:

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.

1. Futtassa az `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` parancsot.

1. Keresse meg az "Uris" szakaszt, és adja hozzá a feldolgozó csomópontok IP-Címét és portrészleteit.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>További lépések

* [Az ML-szolgáltatások fürtjének kezelése a HDInsighton](r-server-hdinsight-manage.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [Azure Storage-lehetőségek az ML-szolgáltatások HDInsighton belüli fürtjéhez](r-server-storage.md)
