---
title: Hozzáférés Hadoop YARN alkalmazásnaplók szoftveres - Azure |} Microsoft Docs
description: Hozzáférési kérelem programozott módon bejelentkezik a HDInsight Hadoop-fürthöz.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: aab7865548c034cb550874c31977b05936dc45b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Windows-alapú HDInsight bejelentkezik hozzáférést YARN alkalmazás
Ez a dokumentum ismerteti a naplókat, amely a Windows-alapú Hadoop-fürthöz az Azure HDInsight végzett YARN alkalmazások eléréséhez

> [!IMPORTANT]
> A jelen dokumentumban szereplő információk csak a Windows-alapú HDInsight-fürtök vonatkozik. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). YARN eléréséről a Linux-alapú HDInsight-fürtök bejelentkezik, a következő témakörben: [Linux-based Hadoop on HDInsight bejelentkezik hozzáférést YARN alkalmazás](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Előfeltételek
* Egy Windows-alapú HDInsight-fürtöt.  Lásd: [hdinsight-fürtök létrehozása Windows-alapú Hadoop](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>YARN ütemterv kiszolgáló
A <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN ütemterv Server</a> általános információkat nyújt a befejeződött alkalmazásokkal, valamint a keretrendszer-specifikus alkalmazás adatait két különböző felületeken keresztül. Konkrétan:

* Tárolása és lekérése általános alkalmazás információk a HDInsight-fürtökön engedélyezett 3.1.1.374 verziójával vagy magasabb volt.
* A keretrendszer-specifikus információk alkalmazásösszetevő ütemterv kiszolgáló jelenleg nem áll rendelkezésre a HDInsight-fürtökön.

Alkalmazások általános információkat tartalmaz a következő típusú adatok:

* Az Alkalmazásazonosító, az alkalmazás egyedi azonosítója
* A felhasználó az alkalmazást
* Az alkalmazás befejezéséhez kísérletek információk
* A tárolók kísérel meg adott alkalmazás által használt

Ezek az információk a HDInsight-fürtök által Azure Resource Manager tárolja. A fürt alapértelmezett tárolóhelyét a előzmények tárban menti az adatokat. Az általános kész alkalmazások adatokat lehet beolvasni a REST API-n keresztül:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN alkalmazások és a naplókat
YARN erőforrás-kezelés az ütemezés/alkalmazásfigyelés leválasztásával több programozási modellt támogatja. YARN használ egy globális *ResourceManager* (RM) worker-csomópontonként *NodeManagers* (NMs), és alkalmazásonként *ApplicationMasters* (AMs). Az alkalmazás kor (Processzor, memória, lemez, hálózati) erőforrások egyezteti az alkalmazás futtatásához a RM a Az erőforrás-kezelő együttműködve biztosítja ezeket az erőforrásokat, amelyek kiadott megadását NMs *tárolók*. A AM felelős a tárolók által a RM rendelt állapotának nyomon követése Az alkalmazás az alkalmazás természetétől függően számos tárolók lehet szükség.

* Minden alkalmazás több állhat *alkalmazás kísérletek*. 
* Tárolók egy adott kísérlet az alkalmazások számára. 
* Egy tároló a kontextust biztosít a alapvető munkaegysége. 
* Egy tároló keretében végrehajtott munka történik a tároló lett lefoglalva egyetlen munkavégző csomóponton. 

További információkért lásd: [YARN fogalmak][YARN-concepts].

Alkalmazásnaplók (és a társított tároló naplók) kritikusak megoldani a problémát okozó Hadoop-alkalmazások. YARN gyűjtése, összesítése és tárolása az alkalmazásnaplók töltött keretet biztosít a [napló összesítési] [ log-aggregation] szolgáltatás. A napló összesítési szolgáltatás elérhetővé teszi az elérése során alkalmazásnaplók sokkal kiszámíthatóbb naplók von össze az összes tároló, a munkavégző csomópont, és tárolja őket egy összesített naplófájlhoz munkavégző csomópont az alapértelmezett fájlrendszeren alkalmazás befejeződése után. Az alkalmazás használhat több száz vagy ezer tárolók, de egyetlen munkavégző csomóponton futtassa az összes tároló naplókat összesítése egy fájlba, ami azt eredményezi, az alkalmazás által használt munkavégző csomópontonként egy fájl. A HDInsight-fürtökön alapértelmezés szerint engedélyezve van a napló összesítési (3.0-s verzió vagy újabb verzió), és az összesített naplók az alapértelmezett tárolóban, a fürt a következő helyen találhatók:

    wasb:///app-logs/<user>/logs/<applicationId>

Az adott helyre *felhasználói* az alkalmazást elindító felhasználó neve és *applicationId* van egy alkalmazás egyedi azonosítója, a YARN RM által hozzárendelt

Az összesített naplók nincsenek közvetlenül is olvasható, mivel az oktatóprogram egy [TFile][T-file], [bináris formátum] [ binary-format] indexelik a tárolóban. YARN dump ezek a naplók az alkalmazások vagy a tárolókat érdeklő egyszerű szövegként CLI eszközöket biztosít. Ezek a naplók (való csatlakozását követően az RDP keresztül) közvetlenül a fürtcsomópontokon parancsok futtatásával a következő YARN egyik egyszerű szöveges módon tekintheti meg:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN erőforrás-kezelő felhasználói felületen
A YARN erőforrás-kezelő felhasználói felületén a fürt headnode fut, és az Azure-portál irányítópultjának keresztül érhetők el:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A bal oldali menüben kattintson **Tallózás**, kattintson a **a HDInsight-fürtök**, kattintson egy Windows-alapú fürt, amely a használni kívánt alkalmazás YARN naplóit.
3. Kattintson a felső menüben **irányítópult**. Egy új böngészőt megnyitott lap jelenik meg nevű lap **HDInsight lekérdezés konzol**.
4. A **HDInsight lekérdezés konzol**, kattintson a **Yarn felhasználói felületen**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
