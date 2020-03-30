---
title: Ml-szolgáltatások üzembe helyezettszolgáltatása a HDInsightban – Azure
description: Ismerje meg, hogyan üzembe hatja az adatmodellt, hogy előrejelzéseket készítsen az Ml Services használatával az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71123562"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Ml-szolgáltatások fürtjének működőképessé tétele az Azure HDInsightban

Miután a HDInsight ml-szolgáltatások fürtjével befejezte az adatmodellezést, üzembe használhatja a modellt az előrejelzések elvégzéséhez. Ez a cikk a feladat végrehajtásával kapcsolatos utasításokat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

* Ml services-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza az **ML-szolgáltatások** **fürttípushoz lehetőséget.**

* Secure Shell- (SSH-) ügyfél: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: [SSH használata a HDInsight segítségével.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Ml Services fürt üzembe építése egydobozos konfigurációval

> [!NOTE]  
> Az alábbi lépések az R Server 9.0 és az ML Server 9.1 rendszerre vonatkoznak. Az ML Server 9.3-as kiszolgálója esetén olvassa el [a Felügyeleti eszköz használata az üzembesági konfiguráció kezeléséhez című hivatkozást.](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch)

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Az SSH Azure HDInsight szolgáltatással való használatáról az [SSH használata a HDInsight szolgáltatással.](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Változás könyvtár a megfelelő verziót, és sudo a dot net dll: 

    - Microsoft ML Server 9.1 esetén:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R Server 9.0 esetén:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Ön jelenik meg a lehetőségek közül lehet választani. Válassza ki az első lehetőséget, ahogy az a következő képernyőképen is látható, az **ML-kiszolgáló konfigurálása az üzembe üzemmódhoz**.

    ![R kiszolgáló felügyelete segédprogram kiválasztása](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Most megjelenik a lehetőséget, hogy válassza ki, hogyan szeretné üzembe lizálni ML Server. A bemutatott beállítások közül válassza ki az elsőt az **A**beírásával.

    ![R server Administration segédprogram működőképessé](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Amikor a rendszer kéri, írja be és írja be újra a jelszót egy helyi rendszergazdafelhasználószámára.

1. Látnia kell a kimenetek arra utalnak, hogy a művelet sikeres volt. A program arra is kéri, hogy válasszon másik lehetőséget a menüből. Válassza az E lehetőséget a főmenühöz való visszalépéshez.

    ![Az R-kiszolgáló felügyeleti segédprogramjának sikeres](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Diagnosztikai ellenőrzéseket az alábbiak szerint futtathat:

    a. A főmenüben válassza a **6** lehetőséget a diagnosztikai tesztek futtatásához.

    ![R kiszolgáló felügyelete segédprogram diagnosztikája](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. A Diagnosztikai tesztek menüben válassza az **A**lehetőséget. Amikor a rendszer kéri, adja meg a helyi rendszergazdai felhasználónak megadott jelszót.

    ![R-kiszolgáló felügyelete segédprogram tesztelése](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Ellenőrizze, hogy a kimenet azt mutatja- e, hogy az általános állapot egy áthaladás.

    ![R-kiszolgáló felügyeleti segédprogram-bérlete](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. A bemutatott menüpontokközül az **E** értéket adja meg a főmenühöz való visszatéréshez, majd a **8-as** értéket az admin segédprogramból való kilépéshez.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Hosszú késések az Apache Spark webszolgáltatásának fogyasztásakor

Ha hosszú késéseket tapasztal, amikor egy Apache Spark számítási környezetben mrsdeploy függvényekkel létrehozott webszolgáltatást próbál felhasználni, előfordulhat, hogy hozzá kell adnia néhány hiányzó mappát. A Spark-alkalmazás egy *rserve2* nevű felhasználóhoz tartozik, ha egy mrsdeploy függvényeket használó webszolgáltatásból hívja meg. Megkerülő megoldás a problémára:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Az operacionalizálás konfigurációja ezzel befejeződött. Most már használhatja a csomagot a `mrsdeploy` RClient csatlakozni a operationalization on edge csomópont, és elkezdi használni a funkciók, mint a távoli [végrehajtás](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) és a [web-szolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Attól függően, hogy fürt virtuális hálózaton van-e beállítva, szükség lehet porttovábbító bújtatás kialakítására SSH-bejelentkezésen keresztül. Az alábbi szakaszok ismertetik, hogyan állíthatja be ezt az alagutat.

### <a name="ml-services-cluster-on-virtual-network"></a>Ml-szolgáltatások fürtje a virtuális hálózaton

Bizonyosodjon meg róla, hogy engedélyezett a forgalom az 12800-as porton az élcsomópont felé. Így az élcsomópont használatával kapcsolódhat az operacionalizálási szolgáltatáshoz.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Ha a `remoteLogin()` metódus nem tud kapcsolódni az élcsomóponthoz, de SSH-n be tud jelentkezni az élcsomópontba, győződjön meg róla, hogy a szabály, amely engedélyezi a forgalmat az 12800-as porton, megfelelően van-e beállítva. Ha a probléma továbbra is jelentkezik, egy másik megoldás segítségével is beállíthat porttovábbító alagutat az SSH-n keresztül. További útmutatást a következő szakaszban talál:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Az ML-szolgáltatások fürtje nincs beállítva a virtuális hálózaton

Ha a fürt nem a virtuális hálózaton van beállítva vagy problémás a kapcsolódás a virtuális hálózaton keresztül, akkor használhatja az SSH porttovábbító alagutat:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Miután az SSH-munkamenet aktívvá vált, a helyi gép 12800-as portjáról érkező forgalmat a szélhálózati csomópont 12800-as portjára továbbítja az SSH-munkameneten keresztül. A `127.0.0.1:12800` címet használja a `remoteLogin()` metódusban. Ez bejelentkezik a peremhálózati csomópont üzembe megoldásába porttovábbítással.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Működőképes számítási csomópontok méretezése a HDInsight munkavégző csomópontjain

A számítási csomópontok méretezéséhez először lekell szerelnia a munkavégző csomópontokat, majd konfigurálnia kell a számítási csomópontokat a leszerelt munkavégző csomópontokon.

### <a name="step-1-decommission-the-worker-nodes"></a>1. lépés: A munkavégző csomópontok leszerelése

Az ML Services-fürt kezelése nem az [Apache Hadoop YARN-on](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)keresztül történik. Ha a munkavégző csomópontok nincsenek leállítva, a YARN erőforrás-kezelő nem a várt módon működik, mert nincs tisztában a kiszolgáló által felvett erőforrásokkal. Ennek a helyzetnek az elkerülésére javasoljuk a feldolgozó csomópontok leszerelését a számítási csomópontok horizontális felskálázása előtt.

A munkavégző csomópontok leszereléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a fürt Ambari konzoljára, és kattintson a **Hosts** fülre.

1. Munkavégző csomópontok kiválasztása (leszerelendő).

1. Kattintson a > **Kiválasztott** >  **gazdagépek** > **bekapcsolása karbantartási mód ra**lehetőséget.**Hosts** A következő képen például a wn3 és a wn4 pontokat választottuk ki leszerelésre.  

   ![Az Apache Ambari bekapcsolása karbantartási mód](./media/r-server-operationalize/get-started-operationalization.png)  

* Válassza a Kijelölt **műveletek** > **DataNodes** **Selected Hosts** > lehetőséget, > kattintson **a Leszerelés gombra.**
* Válassza a Kijelölt **gazdagépek** > **csomópontkezelői** **Selected Hosts** > > válassza a **Leszerelés**lehetőséget.
* Válassza a Kijelölt **műveletek** > **DataNodes** **Selected Hosts** > > válassza **a Leállítás**gombra.
* Válassza a Kijelölt > **gazdagépek** > **csomóponti** műveletek et, > kattintson a **Actions** **Leállítás**gombra.
* Jelölje be **a** > **kijelölt gazdagépek** > **en műveletek** et, > kattintson az Összes összetevő leállítása **gombra.**
* Szüntesse meg a feldolgozó csomópontok kijelölését, és jelölje ki az átjárócsomópontokat.
* Jelölje be **a** > **Kijelölt műveletek** > "**Állomások** > **Az összes összetevő újraindítása**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>2. lépés: Számítási csomópontok konfigurálása minden egyes leszerelt munkavégző csomóponton

1. Jelentkezzen be SSH-n keresztül minden egyes leszerelt feldolgozó csomópontba.

1. Futtassa a felügyeleti segédprogramot a megfelelő DLL használatával a megfelelő ML-szolgáltatások fürthöz. Az ML Server 9.1 esetén futtassa a következőket:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Írja be az **1** értéket az **ML-kiszolgáló konfigurálása az üzembe imita beállításához.**

1. A beállítás kiválasztásához írja be a **C** értéket. `C. Compute node` Ez konfigurálja a számítási csomópontot a feldolgozó csomóponton.

1. Lépjen ki az admin segédprogramból.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>3. lépés: Számítási csomópontok részleteinek hozzáadása a webes csomóponton

Miután az összes leszerelt munkavégző csomópont úgy van konfigurálva, hogy futtassa a számítási csomópontot, jöjjön vissza a peremhálózati csomópontra, és adja hozzá a leszerelt munkavégző csomópontok IP-címeit a ML-kiszolgáló webcsomópontkonfigurációjában:

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.

1. Futtassa az `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` parancsot.

1. Keresse meg az "Uris" szakaszt, és adja hozzá a munkavégző csomópont IP- és portadatait.

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
