---
title: Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz – Azure HDInsight
description: Megtudhatja, hogyan érheti el a fonal-alkalmazási naplókat egy Linux-alapú HDInsight-(Apache Hadoop-) fürtön a parancssori felület és a webböngésző használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: 263456769ab391cbc0588eed1a714a1ea5788154
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494878"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz a Linux-alapú HDInsight

Megtudhatja, hogyan érheti el a naplókat [Apache HADOOP fonalak](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (még egy erőforrás-egyeztető) alkalmazásai számára egy [Apache Hadoop](https://hadoop.apache.org/) -fürtön az Azure HDInsight.

## <a name="YARNTimelineServer"></a>FONAL idővonal-kiszolgálója

A [Apache HADOOP fonál idővonal-kiszolgálója](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) általános információkat biztosít a befejezett alkalmazásokról

A fonal idővonal-kiszolgálója a következő típusú adattípusokat tartalmazza:

* Az alkalmazás azonosítója, az alkalmazás egyedi azonosítója
* Az alkalmazást elindító felhasználó
* Információk az alkalmazás befejezésére tett kísérletekről
* Az adott alkalmazás által megkísérelt tárolók

## <a name="YARNAppsAndLogs"></a>FONALas alkalmazások és naplók

A FONALak több programozási modellt támogatnak ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) is) az erőforrás-kezelés leválasztásával az alkalmazás ütemezése/monitorozása során. A fonal globális *erőforráskezelő* (RM), munkavégző-csomópontos *csomópontkezelők* (alrendszerek) és felhasználónkénti *ApplicationMasters* (AMs) használ. Az per-Application AM egyezteti az erőforrásokat (processzor, memória, lemez, hálózat) az alkalmazásnak az RM-vel való futtatásához. Az RM úgy működik, hogy az ilyen erőforrásokat *tárolóként*adja meg. Az AM feladata az RM által hozzárendelt tárolók állapotának nyomon követése. Egy alkalmazás az alkalmazás természetétől függően számos tárolót igényelhet.

Minden alkalmazás több *alkalmazási kísérletből*állhat. Ha egy alkalmazás meghibásodik, előfordulhat, hogy a rendszer újrapróbálkozik új kísérletként. Minden kísérlet egy tárolóban fut. Bizonyos értelemben a tárolók a FONALas alkalmazások alapszintű munkaegységének környezetét biztosítják. A tároló környezetében elvégzett összes munkát az egyetlen feldolgozó csomóponton hajtja végre, amelyen a tároló le lett foglalva. További információért lásd: [Apache Hadoop a fonalat ismertető fogalmakat](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) .

Az alkalmazás naplói (és a kapcsolódó tárolók naplói) kritikus fontosságúak a problematikus Hadoop-alkalmazások hibakereséséhez. A fonal szép keretrendszert biztosít az alkalmazások naplóinak a [naplózási összesítési](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) funkcióval való összegyűjtéséhez, összesítéséhez és tárolásához. A naplózási összesítés funkció lehetővé teszi, hogy az alkalmazások naplói több determinisztikus. Összesíti a munkavégző csomópont összes tárolójában lévő naplókat, és a munkavégző csomóponton egy összesített naplófájlként tárolja őket. Az alkalmazás befejeződése után a rendszer az alapértelmezett fájlrendszerben tárolja a naplót. Az alkalmazás több száz vagy akár több ezer tárolót is használhat, de egyetlen feldolgozó csomóponton futtatott összes tároló naplóit mindig egyetlen fájlba összesíti a rendszer. Így az alkalmazás csak 1 log/feldolgozó csomópontot használ. A naplózási összesítés alapértelmezés szerint engedélyezve van a 3,0-es és újabb verziójú HDInsight-fürtökön. Az összesített naplók a fürt alapértelmezett tárolójában találhatók. A következő elérési út a naplók HDFS elérési útja:

    /app-logs/<user>/logs/<applicationId>

Az elérési útban `user` az alkalmazást elindító felhasználó neve. A `applicationId` a fonal RM-alkalmazáshoz rendelt egyedi azonosító.

Az összesített naplók nem olvashatók közvetlenül, mivel azok [TFile][T-file], a tároló által indexelt [bináris formátumban][binary-format] vannak írva. A erőforráskezelő-naplók vagy a CLI-eszközök segítségével egyszerű szövegként tekintheti meg ezeket a naplókat alkalmazások vagy tárolók esetében.

## <a name="yarn-cli-tools"></a>FONAL CLI-eszközök

A fonal CLI-eszközeinek használatához először az SSH használatával kell csatlakoznia a HDInsight-fürthöz. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

Ezeket a naplókat egyszerű szövegként tekintheti meg a következő parancsok egyikének futtatásával:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

A parancsok futtatásakor határozza meg a &lt;applicationId >, &lt;a felhasználó által elindított >, &lt;containerId > és &lt;feldolgozó-csomópont-címek > információit.

## <a name="yarn-resourcemanager-ui"></a>FONAL erőforráskezelő felhasználói felülete

A fonal erőforráskezelő felhasználói felülete a fürt átjárócsomóponthoz fut. Ez a Ambari webes felhasználói felületén keresztül érhető el. A következő lépések végrehajtásával tekintheti meg a FONALak naplóit:

1. A böngészőben nyissa meg a https://CLUSTERNAME.azurehdinsight.net. Cserélje le a CLUSTERNAME-t a HDInsight-fürt nevére.
2. A bal oldali szolgáltatások listájából válassza a **fonal**lehetőséget.

    ![Apache Ambari fonal-szolgáltatás kiválasztva](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. A **rövid hivatkozások** legördülő listából válassza ki az egyik fürtcsomópont-csomópontot, majd válassza a **erőforráskezelő-napló**elemet.

    ![Apache Ambari-fonal – gyors hivatkozások](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Megjelenik a FONALak naplóira mutató hivatkozások listája.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
