---
title: Az R Server on HDInsight - Azure azok |} Microsoft Docs
description: R Server, az Azure HDInsight azok útmutató.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 93957b7ee10527039bf2e96cc5470420cdef0651
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Azok az Azure hdinsight R Server fürt

Után R Server-fürt hdinsightban kell használni a modellezési adatait befejezéséhez, üzembe előrejelzéseket készítsen a modellt. Ez a cikk útmutatás a feladat végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* **Az R Server-fürt a HDInsight**: útmutatásért lásd: [az R Server on HDInsight használatába](r-server-get-started.md).

* **Secure Shell- (SSH-) ügyfél**: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>R Server-fürt egyik-mezőben konfigurációjával kapcsolatban üzemeltető

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Az SSH használata Azure HDInsight útmutatásért lásd: [használja SSH a Hdinsighttal.](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Módosítsa a könyvtárat a megfelelő verzióját és a sudo pont nettó DLL-re: 

    - Microsoft R Server 9.1 esetén:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R Server 9.0 esetén:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Megnyílik a választási lehetőséget. Válassza ki az első lehetőség, ahogy az az alábbi képernyőfelvételen a **R Server konfigurálása a Operationalization**.

    ![one box op](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Most már lehetősége lesz választhatja ki, hogyan szeretné azok R Server. A bemutatott lehetőségek közül válassza ki az elsőt megadásával **A**.

    ![one box op](./media/r-server-operationalize/admin-util-one-box-2.png)

5. Amikor a rendszer kéri, adja meg, és adja meg újból egy felhasználót a helyi rendszergazda jelszavát.

6. Meg kell jelennie kimenetek, amelyek arra utalnak, hogy a művelet sikeres volt-e. Is kéri válasszon másik lehetőséget a menüből. Lépjen vissza a főmenübe válassza E.

    ![one box op](./media/r-server-operationalize/admin-util-one-box-3.png)

7. Diagnosztikai ellenőrzések szükség esetén a teszt az alábbiak szerint futtatásával hajthatja végre:

    a. Válassza ki a főmenü **6** diagnosztikai tesztek futtatásához.

    ![one box op](./media/r-server-operationalize/diagnostic-1.png)

    b. A diagnosztikai tesztek menüben válassza ki a **A**. Amikor a rendszer kéri, adja meg a jelszót a helyi rendszergazda felhasználó számára megadott.

    ![one box op](./media/r-server-operationalize/diagnostic-2.png)

    c. Győződjön meg arról, hogy a kimeneti mutatja, hogy általános állapotát egy fázisban.

    ![one box op](./media/r-server-operationalize/diagnostic-3.png)

    d. Adja meg a menüpont megjelenített **E** térjen vissza a főmenübe, majd **8** való kilépéshez a felügyeleti segédprogram.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Amikor Spark webhellyel fel nagy késleltetéseket

Ha hosszú késleltetést tapasztal, amikor egy Spark számítási környezetben mrsdeploy függvényekkel létrehozott webszolgáltatást próbál felhasználni, előfordulhat, hogy hozzá kell adnia néhány hiányzó mappát. A Spark-alkalmazás egy *rserve2* nevű felhasználóhoz tartozik, ha egy mrsdeploy függvényeket használó webszolgáltatásból hívja meg. Megkerülő megoldás a problémára:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Az operacionalizálás konfigurációja ezzel befejeződött. Most már használhatja a `mrsdeploy` csomagot a peremhálózati csomóponton operationalization és indíthatja annak szolgáltatásait, például a RClient [távoli végrehajtás](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) és [-webszolgáltatások](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Attól függően, hogy fürt virtuális hálózaton van-e beállítva, szükség lehet porttovábbító bújtatás kialakítására SSH-bejelentkezésen keresztül. Az alábbi szakaszok ismertetik, hogyan állíthatja be ezt az alagutat.

### <a name="r-server-cluster-on-virtual-network"></a>R Server-fürt virtuális hálózaton

Bizonyosodjon meg róla, hogy engedélyezett a forgalom az 12800-as porton az élcsomópont felé. Így az élcsomópont használatával kapcsolódhat az operacionalizálási szolgáltatáshoz.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Ha a `remoteLogin()` metódus nem tud kapcsolódni az élcsomóponthoz, de SSH-n be tud jelentkezni az élcsomópontba, győződjön meg róla, hogy a szabály, amely engedélyezi a forgalmat az 12800-as porton, megfelelően van-e beállítva. Ha a probléma továbbra is jelentkezik, egy másik megoldás segítségével is beállíthat porttovábbító alagutat az SSH-n keresztül. Útmutatásért lásd a következő:

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>R Server-fürt virtuális hálózaton nincs beállítva

Ha a fürt nem a virtuális hálózaton van beállítva vagy problémás a kapcsolódás a virtuális hálózaton keresztül, akkor használhatja az SSH porttovábbító alagutat:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Ha az SSH-munkamenet aktív, a rendszer számítógépe 12800-as portjáról az élcsomópont 12800-as portjára továbbítja a forgalmat az SSH-munkameneten keresztül. A `127.0.0.1:12800` címet használja a `remoteLogin()` metódusban. A rendszer azokat az élcsomóponthoz operationalization port továbbítási keresztül.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>A skála operationalized számítási csomópontok a HDInsight munkavégző csomópontokhoz

A számítási csomópontok méretezéséhez először szerelje le a munkavégző csomópontokról, majd válassza a számítási csomópontok a leszerelt feldolgozó csomópontokon.

### <a name="step-1-decommission-the-worker-nodes"></a>1. lépés: A feldolgozó csomópontok leszerelése

R Server fürt nem áll a YARN keresztül. Ha a munkavégző csomópontokhoz nincs leszerelve, a YARN erőforrás-kezelő nem működik megfelelően, mivel nincs tisztában az erőforrások foglalja el a kiszolgáló. Ennek a helyzetnek az elkerülésére javasoljuk a feldolgozó csomópontok leszerelését a számítási csomópontok horizontális felskálázása előtt.

Kövesse az alábbi lépéseket leszerelése munkavégző csomópontokhoz:

1. Jelentkezzen be a fürt Ambari konzolt, majd kattintson a **állomások** fülre.

2. Válassza ki a feldolgozó csomópontok (le kell szerelni).

3. Kattintson a **műveletek** > **állomások kijelölt** > **állomások** > **kapcsolja be a karbantartási mód**. A következő képen például a wn3 és a wn4 pontokat választottuk ki leszerelésre.  

   ![feldolgozó csomópont(ok) leszerelése](./media/r-server-operationalize/get-started-operationalization.png)  

* Válassza ki **műveletek** > **kijelölt gazdagépek** > **DataNodes** > kattintson **Decommission**.
* Válassza ki **műveletek** > **kijelölt gazdagépek** > **NodeManagers** > kattintson **Decommission**.
* Válassza ki **műveletek** > **kijelölt gazdagépek** > **DataNodes** > kattintson **leállítása**.
* Válassza ki **műveletek** > **kijelölt gazdagépek** > **NodeManagers** > kattintson a **leállítása**.
* Válassza ki **műveletek** > **kijelölt gazdagépek** > **állomások** > kattintson **állítsa le az összes összetevő**.
* Szüntesse meg a feldolgozó csomópontok kijelölését, és jelölje ki az átjárócsomópontokat.
* Válassza ki **műveletek** > **állomások kijelölt** > "**állomások** > **indítsa újra az összes összetevő**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>2. lépés: Konfigurálja a számítási csomópontok minden leszerelt munkavégző csomópont

1. Jelentkezzen be SSH-n keresztül minden egyes leszerelt feldolgozó csomópontba.

2. Az R Server-fürt, amely rendelkezik a megfelelő dll-fájl használatával felügyeleti segédprogram futtatásához. R Server 9.1, futtassa a következő parancsot:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Adja meg **1** lehetőségnek a választásához **R Server konfigurálása a Operationalization**.

4. Adja meg **C** lehetőségnek a választásához `C. Compute node`. Ez konfigurálja a számítási csomópontot a feldolgozó csomóponton.

5. Lépjen ki az admin segédprogramból.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>3. lépés: Adja hozzá a számítási csomópontok részletek webes csomóponton

Leszerelt feldolgozó csomópontjaihoz futtatásához a számítási csomópont konfigurálása után térjen vissza a peremhálózati csomóponton, és az R Server webes csomópont-konfiguráció a leszerelt munkavégző csomópontokhoz IP-címek hozzáadása:

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.

2. Futtassa az `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` parancsot.

3. Keresse meg az "URI" szakaszban, és adja hozzá a munkavégző csomópont IP-cím és port részleteit.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>További lépések

* [A HDInsight R Server-fürt kezeléséhez](r-server-hdinsight-manage.md)
* [Számítási R Server-fürt a HDInsight adatkörnyezet beállításai](r-server-compute-contexts.md)
* [A HDInsight fürt az R Serverhez Azure tárolási lehetőségek](r-server-storage.md)