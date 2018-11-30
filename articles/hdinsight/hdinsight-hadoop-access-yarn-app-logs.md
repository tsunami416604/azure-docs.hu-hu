---
title: Hadoop YARN-alkalmazásnaplók elérése programozott módon – Azure
description: Alkalmazásnaplók elérése programozott módon a HDInsight Hadoop-fürtön.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 62499c35fd71d83f80a60e0511e6a27ce0109275
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495865"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-windows-based-hdinsight"></a>Az Apache Hadoop YARN-alkalmazásnaplók elérése a Windows-alapú HDInsight
Ez a dokumentum ismerteti, hogyan érhető el a naplókat [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) alkalmazásokat, amelyek befejezte egy Windows-alapú Apache hadoop-fürtön az Azure HDInsight

> [!IMPORTANT]
> Ebben a dokumentumban található információk csak a Windows-alapú HDInsight-fürtök vonatkozik. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). A Linux-alapú HDInsight-fürtökön eléréséről a YARN naplókat: [hozzáférés Apache Hadoop YARN-alkalmazásnaplók a HDInsight Linux-alapú Apache hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Előfeltételek
* Egy Windows-alapú HDInsight-fürt.  Lásd: [létrehozása Windows-alapú Apache Hadoop-fürtök a HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN idővonal-kiszolgáló
A <a href="http://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Apache Hadoop YARN idővonal-kiszolgáló</a> általános információkat biztosít a befejezett alkalmazásokkal, valamint a keretrendszer-specifikus alkalmazással kapcsolatos adatok két különböző felületen. Konkrétan:

* Tárolásához és lekéréséhez általános alkalmazással kapcsolatos információk a HDInsight-fürtökön lett engedélyezve 3.1.1.374 verziójával vagy újabb.
* A keretrendszer-specifikus alkalmazás információk összetevője az idővonal-kiszolgáló jelenleg nem áll rendelkezésre a HDInsight-fürtökön.

Az alkalmazások általános információkat a következő számos különféle adatokat tartalmazza:

* Az Alkalmazásazonosító, az alkalmazás egyedi azonosítója
* A felhasználó az alkalmazást
* Végezze el az alkalmazás tett kísérletet információk
* A tárolók kísérel meg adott alkalmazás által használt

A HDInsight-fürtök ezeket az adatokat tároló Azure Resource Manager által. A korábbi tárban a a fürt alapértelmezett tárolója menti az adatokat. A befejezett alkalmazásokkal az általános adatok lekérhetők a REST API-n keresztül:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN-alkalmazások és a naplók
A YARN erőforrás-kezelést, ütemezés/alkalmazásfigyelés leválasztásával több programozási modell támogatja. YARN használ egy globális *ResourceManager* (RM), feldolgozó-csomópontonkénti *NodeManagers* (NMs), és alkalmazásonkénti *ApplicationMasters* (AMs). Az alkalmazás kor erőforrások (CPU, memória, lemez vagy hálózat) egyezteti az alkalmazás futtatására és a RM. Az erőforrás-kezelő működik együtt, adja meg ezeket az erőforrásokat, mint megadó NMs *tárolók*. A AM felelős a tárolók a RM. által hozzárendelt állapotának nyomon követése Egy alkalmazás az alkalmazás jellegétől függően több tároló lehet szükség.

* Minden alkalmazás több állhat *alkalmazás kísérletek*. 
* Tárolók egy adott kísérlet egy alkalmazás számára. 
* Egy tárolót a kontextust biztosít a alapvető munkaegysége. 
* Egy tároló kontextusában van elvégzett munka történik, a tároló lett lefoglalva egyetlen feldolgozó csomóponton. 

További információkért lásd: [Apache Hadoop YARN fogalmak][YARN-concepts].

Protokoly aplikací (és a társított tároló naplóinak) létfontosságúak a problémás Hadoop-alkalmazások hibakeresése. YARN összegyűjtése, összevonása és az alkalmazás naplók tárolásához nice keretrendszert biztosít a [Log összesítési] [ log-aggregation] funkció. A naplózási összesítési szolgáltatás révén elérésére alkalmazásnaplókat sokkal kiszámíthatóbb, naplók összesíti az összes tárolót a munkavégző csomópont között, és a egy összesített naplófájl száma feldolgozó csomópontonként az alapértelmezett fájlrendszer tárolja őket egy alkalmazás befejezése után. Az alkalmazás felhasználhatja, több száz vagy akár több ezer olyan tárolók, de egyetlen fájlt, egy fájlt az alkalmazás által használt feldolgozó csomópontonkénti eredményez a egyetlen munkavégző csomóponton futó összes tároló naplóinak vannak összesítve. A HDInsight-fürtökön alapértelmezés szerint engedélyezve van a naplózási összesítési (3.0-s verzió vagy újabb verzió), és az összesített naplókat az alapértelmezett tároló, a fürt a következő helyen található:

    wasb:///app-logs/<user>/logs/<applicationId>

Az adott helyen *felhasználói* az alkalmazást elindító felhasználó neve és *applicationId* van egy alkalmazás egyedi azonosítója, a YARN-RM. által hozzárendelt

Az összesített naplókat amelyek nem közvetlenül olvasható, nyelven íródtak, egy [TFile][T-file], [bináris formátumú] [ binary-format] indexeli a tárolót. YARN parancssori felületi eszközöket kiírása ezeket a naplókat az alkalmazások vagy a lényeges tárolók egyszerű szövegként biztosít. Ezek a naplók tekintheti meg, futtassa a következő YARN egyik egyszerű szöveges parancsokat közvetlenül a fürtcsomópontokon (Miután csatlakozott, RDP-Kapcsolaton keresztül):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager felhasználói felülete
A YARN ResourceManager felhasználói felülete a fürt átjárócsomópontjával fut, és az Azure portal irányítópultján keresztül érhető el:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A bal oldali menüben kattintson a **Tallózás**, kattintson a **HDInsight-fürtök**, kattintson a YARN-alkalmazásnaplók elérése kívánt Windows-alapú fürt.
3. A felső menüben kattintson a **irányítópult**. Megjelenik egy oldal nyit meg egy új böngésző nevű lapot **HDInsight Lekérdezéskonzol**.
4. A **HDInsight Lekérdezéskonzol**, kattintson a **Yarn felhasználói felületén**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
