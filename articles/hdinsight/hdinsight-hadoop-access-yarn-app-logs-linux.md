---
title: Hadoop YARN-alkalmazásnaplók elérése a Linux-alapú HDInsight – Azure
description: Ismerje meg a parancssori és a egy webes böngésző használatával egy Linux-alapú HDInsight (Hadoop) fürtön YARN-alkalmazásnaplók elérése.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.openlocfilehash: 0aa8d76ca97789844482d2b8ad7212c2f61a8ab8
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591803"
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>YARN-alkalmazásnaplók elérése Linux-alapú HDInsight a

Megtudhatja, hogyan (még egy másik Resource Negotiator) YARN-alkalmazások az Azure HDInsight Hadoop-fürtön a naplók eléréséhez.

> [!IMPORTANT]
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight összetevők verziószámozása](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN idővonal-kiszolgáló

A [YARN idővonal-kiszolgáló](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) általános információkat tartalmaz a befejezett alkalmazásokkal és keretrendszer-specifikus alkalmazás adatokat két különböző felületek segítségével. Konkrétan:

* Tárolásához és lekéréséhez általános alkalmazással kapcsolatos információk a HDInsight-fürtökön lett engedélyezve 3.1.1.374 verziójával vagy újabb.
* A keretrendszer-specifikus alkalmazás információk összetevője az idővonal-kiszolgáló jelenleg nem áll rendelkezésre a HDInsight-fürtökön.

Az alkalmazások általános információkat a következő típusú adatokat tartalmazza:

* Az Alkalmazásazonosító, az alkalmazás egyedi azonosítója
* A felhasználó az alkalmazást
* Végezze el az alkalmazás tett kísérletet információk
* A tárolók kísérel meg adott alkalmazás által használt

## <a name="YARNAppsAndLogs"></a>YARN-alkalmazások és a naplók

A YARN erőforrás-kezelést, ütemezés/alkalmazásfigyelés leválasztásával több programozási modellek (folyamatban, az egyiket MapReduce) támogatja. YARN használ egy globális *ResourceManager* (RM), feldolgozó-csomópontonkénti *NodeManagers* (NMs), és alkalmazásonkénti *ApplicationMasters* (AMs). Az alkalmazás kor erőforrások (CPU, memória, lemez vagy hálózat) egyezteti az alkalmazás futtatására és a RM. Az erőforrás-kezelő működik együtt, adja meg ezeket az erőforrásokat, mint megadó NMs *tárolók*. A AM felelős a tárolók a RM. által hozzárendelt állapotának nyomon követése Egy alkalmazás az alkalmazás jellegétől függően több tároló lehet szükség.

Minden alkalmazás több állhat *alkalmazás kísérletek*. Ha egy alkalmazás nem tudja, akkor előfordulhat, hogy újra megkísérli egy új kísérlet. Minden kísérlet egy tárolóban futtatja. Bizonyos értelemben egy tárolót a YARN-alkalmazás által végzett munka alapvető egysége a kontextust biztosít a. Egy tároló kontextusában végrehajtott összes munka a egyetlen munkavégző csomóponton, amelyen a tároló lefoglalt történik. Lásd: [YARN fogalmak] [ YARN-concepts] további referenciaként.

Protokoly aplikací (és a társított tároló naplóinak) létfontosságúak a problémás Hadoop-alkalmazások hibakeresése. YARN összegyűjtése, összevonása és az alkalmazás naplók tárolásához nice keretrendszert biztosít a [Log összesítési] [ log-aggregation] funkció. A naplózási összesítési szolgáltatás kiszámíthatóbbá teszi hozzáférni az alkalmazásnaplókat. Ez összesíti a naplók között a munkavégző csomóponton az összes tárolót, és tárolja őket egy összesített naplófájl száma feldolgozó csomópontonként. A naplóban tárolja az alapértelmezett fájlrendszer egy alkalmazás befejezése után. Az alkalmazás felhasználhatja, több száz vagy akár több ezer olyan tárolók, de a egyetlen munkavégző csomóponton futó összes tároló naplóinak mindig egyetlen fájlban vannak összesítve. Így nincs csak 1 log száma feldolgozó csomópontonként az alkalmazása által használt. A HDInsight fürtök 3.0-s verzió vagy újabb alapértelmezés szerint engedélyezve van a naplózási összesítési. Összesített naplókat a fürt alapértelmezett tárolója mappában találhatók. A következő elérési út a naplók HDFS elérési útja:

    /app-logs/<user>/logs/<applicationId>

Az elérési út `user` az alkalmazást elindító felhasználó neve. A `applicationId` alkalmazáshoz a YARN-RM. által hozzárendelt egyedi azonosítója

Az összesített naplókat amelyek nem közvetlenül olvasható, nyelven íródtak, egy [TFile][T-file], [bináris formátumú] [ binary-format] indexeli a tárolót. A YARN ResourceManager naplók vagy a parancssori eszközök használatával az alkalmazások vagy a lényeges tárolók egyszerű szövegként ezek a naplók megtekintéséhez.

## <a name="yarn-cli-tools"></a>YARN parancssori felületi eszközök

YARN parancssori felületi eszközök használata, először csatlakoznia kell a HDInsight-fürthöz SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

Ezek a naplók pedig egyszerű szövegként tekintheti meg a következő parancsok futtatásával:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Adja meg a &lt;applicationId >, &lt;felhasználói – ki – elindult-a-alkalmazás >, &lt;cseréjekor a Tárolóazonosító >, és &lt;feldolgozó – csomópont-címe > Ezek a parancsok futtatásakor adatokat.

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager felhasználói felülete

A YARN ResourceManager felhasználói felülete a fürt átjárócsomópontjával futtat. Ez az Ambari webes felhasználói felületen keresztül érhetők el. Használja az alábbi lépéseket a YARN-naplók megtekintéséhez:

1. Navigáljon a böngészőben https://CLUSTERNAME.azurehdinsight.net. CLUSTERNAME cserélje le a HDInsight-fürt nevére.
2. A bal oldali szolgáltatások listájából válassza ki a **YARN**.

    ![Kiválasztott yarn-szolgáltatás](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Az a **Gyorshivatkozások** legördülő menüben válassza ki a fürt fő csomópontjának egyikét, majd **ResourceManager Log**.

    ![Yarn Gyorshivatkozások](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    YARN-naplókat mutató hivatkozások listája jelenik meg.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
