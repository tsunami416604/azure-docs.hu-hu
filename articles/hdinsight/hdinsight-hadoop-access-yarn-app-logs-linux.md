---
title: "Linux-alapú HDInsight - Azure bejelentkezik hozzáférést Hadoop YARN alkalmazás |} Microsoft Docs"
description: "Ismerje meg, hogyan férhet hozzá a YARN alkalmazásnaplók a parancssori és egy webes böngésző használata Linux-alapú HDInsight (Hadoop) fürtön."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: eea30e60a793563fbde96d11ab4bdb2d01241e60
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Linux-alapú HDInsight bejelentkezik hozzáférést YARN alkalmazás

Ismerje meg, hogyan férhet hozzá a naplók a YARN (még egy másik erőforrás egyeztető) alkalmazásokhoz az Azure HDInsight Hadoop-fürthöz.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight-összetevők verziószámozása](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN ütemterv kiszolgáló

A [YARN ütemterv Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) általános információkat nyújt a kész alkalmazások és a keretrendszer-specifikus alkalmazással kapcsolatos adatok két különböző felületeken keresztül. Konkrétan:

* Tárolása és lekérése általános alkalmazás információk a HDInsight-fürtökön engedélyezett 3.1.1.374 verziójával vagy magasabb volt.
* A keretrendszer-specifikus információk alkalmazásösszetevő ütemterv kiszolgáló jelenleg nem áll rendelkezésre a HDInsight-fürtökön.

Alkalmazások általános információkat tartalmaz a következő típusú adatok:

* Az Alkalmazásazonosító, az alkalmazás egyedi azonosítója
* A felhasználó az alkalmazást
* Az alkalmazás befejezéséhez kísérletek információk
* A tárolók kísérel meg adott alkalmazás által használt

## <a name="YARNAppsAndLogs"></a>YARN alkalmazások és a naplókat

YARN erőforrás-kezelés az ütemezés/alkalmazásfigyelés leválasztásával több programozási modellek (MapReduce egyik folyamatban) támogatja. YARN használ egy globális *ResourceManager* (RM) worker-csomópontonként *NodeManagers* (NMs), és alkalmazásonként *ApplicationMasters* (AMs). Az alkalmazás kor (Processzor, memória, lemez, hálózati) erőforrások egyezteti az alkalmazás futtatásához a RM a Az erőforrás-kezelő együttműködve biztosítja ezeket az erőforrásokat, amelyek kiadott megadását NMs *tárolók*. A AM felelős a tárolók által a RM rendelt állapotának nyomon követése Az alkalmazás az alkalmazás természetétől függően számos tárolók lehet szükség.

Minden alkalmazás több állhat *alkalmazás kísérletek*. Ha egy alkalmazás sikertelen, akkor előfordulhat, hogy újra megkísérli egy új kísérlet. A tárolóban lévő egyes kísérletek fut. Bizonyos értelemben egy tárolót biztosít a YARN alkalmazások által végzett munka alapvető egysége a környezetben. Egy tároló keretében végrehajtott összes munka a egyetlen munkavégző csomóponton, amelyen a tároló lefoglalt történik. Lásd: [YARN fogalmak] [ YARN-concepts] további referenciaként.

Alkalmazásnaplók (és a társított tároló naplók) kritikusak megoldani a problémát okozó Hadoop-alkalmazások. YARN gyűjtése, összesítése és tárolása az alkalmazásnaplók töltött keretet biztosít a [napló összesítési] [ log-aggregation] szolgáltatás. A napló összesítési szolgáltatás révén elérése során alkalmazásnaplók sokkal kiszámíthatóbb. Naplók von össze az összes tároló, a munkavégző csomópont, és tárolja őket egy összesített naplófájlhoz munkavégző csomópont. A napló tárolja az alapértelmezett fájlrendszer egy alkalmazás befejeződése után. Az alkalmazás használhat több száz vagy ezer tárolók, de egyetlen munkavégző csomóponton futtassa az összes tároló naplókat mindig összesítése egy fájlba. Így nincs csak egyes munkavégző csomópontok az alkalmazás által használt 1 napló. Napló összesítési HDInsight fürtök 3.0-s verzió vagy újabb rendszeren alapértelmezés szerint engedélyezve van. Összesített találhatók a fürt tárolóhelyét alapértelmezett. A következő elérési út a naplók a HDFS elérési útja:

    /app-logs/<user>/logs/<applicationId>

Az elérési út `user` az alkalmazást elindító felhasználó neve. A `applicationId` egy alkalmazás a YARN RM által hozzárendelt egyedi azonosítója

Az összesített naplók nincsenek közvetlenül is olvasható, mivel az oktatóprogram egy [TFile][T-file], [bináris formátum] [ binary-format] indexelik a tárolóban. A YARN erőforrás-kezelő naplók vagy a parancssori eszközök segítségével az alkalmazások vagy a tárolókat érdeklő egyszerű szövegként ezek a naplók megtekintéséhez.

## <a name="yarn-cli-tools"></a>YARN CLI-eszközei

A YARN CLI eszközök használatához először csatlakoznia kell a HDInsight-fürthöz SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

Ezek a naplók egyszerű szövegként tekintheti meg a következő parancsok egyikét futtatja:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Adja meg a &lt;applicationId >, &lt;felhasználói-akik-elindult-az-alkalmazási >, &lt;Tárolóazonosító >, és &lt;worker-csomópont-címe > adatokat, amikor a parancsok futtatásával.

## <a name="yarn-resourcemanager-ui"></a>YARN erőforrás-kezelő felhasználói felületen

A YARN erőforrás-kezelő felhasználói felületén a fürt headnode futtatja. Az Ambari webes felhasználói felületen keresztül érhető el. A YARN naplók megtekintéséhez tegye a következőket:

1. A böngészőben navigáljon https://CLUSTERNAME.azurehdinsight.net. CLUSTERNAME cserélje le a HDInsight-fürt nevét.
2. Válassza ki a listáról a bal oldali szolgáltatások **YARN**.

    ![Kijelölt yarn szolgáltatás](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Az a **Gyorshivatkozások** legördülő menüben válasszon ki egy központi csomópont, és válassza ki **ResourceManager napló**.

    ![Yarn Gyorshivatkozások](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    A YARN naplóit hivatkozáslista jelenik meg.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
