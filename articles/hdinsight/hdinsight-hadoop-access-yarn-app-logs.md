---
title: "Hozzáférés Hadoop YARN alkalmazásnaplók szoftveres - Azure |} Microsoft Docs"
description: "Hozzáférési kérelem programozott módon bejelentkezik a HDInsight Hadoop-fürthöz."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 90323af4a1f4526ab9b26811c8679337076112d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Windows-alapú HDInsight bejelentkezik hozzáférést YARN alkalmazás
Ez a témakör ismerteti, hogyan férhet hozzá a naplókat, hogy végzett a Windows-alapú Hadoop-fürthöz az Azure HDInsight a YARN (még egy másik erőforrás egyeztető) alkalmazások

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

A HDInsight-fürtökön ezeket az információkat tárolja az Azure erőforrás-kezelő által az alapértelmezett tárolóban, az alapértelmezett Azure Storage-fiókjának előzmények tárolóval. Az általános kész alkalmazások adatokat lehet beolvasni a REST API-n keresztül:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN alkalmazások és a naplókat
YARN erőforrás-kezelés az ütemezés/alkalmazásfigyelés leválasztásával több programozási modellek (MapReduce egyik folyamatban) támogatja. Ez egy globális segítségével történik *ResourceManager* (RM) worker-csomópontonként *NodeManagers* (NMs), és alkalmazásonként *ApplicationMasters* (AMs). Az alkalmazás kor (Processzor, memória, lemez, hálózati) erőforrások egyezteti az alkalmazás futtatásához a RM a Az erőforrás-kezelő együttműködve biztosítja ezeket az erőforrásokat, amelyek kiadott megadását NMs *tárolók*. A AM felelős a tárolók által a RM rendelt állapotának nyomon követése Az alkalmazás az alkalmazás természetétől függően számos tárolók lehet szükség.

Továbbá minden alkalmazás több állhat *alkalmazás kísérletek* mellett összeomlásokat, illetve egy óra közötti kommunikáció elvesztését befejezéséhez és az RM Ezért tárolók egy adott kísérlet az alkalmazások számára. Bizonyos értelemben egy tárolót biztosít a YARN alkalmazások által végzett munka alapvető egysége a környezetben, és a tároló kontextusában végrehajtott összes munka történik a egyetlen munkavégző csomóponton, amelyen a tároló lett lefoglalva. Lásd: [YARN fogalmak] [ YARN-concepts] további referenciaként.

Alkalmazásnaplók (és a társított tároló naplók) kritikusak megoldani a problémát okozó Hadoop-alkalmazások. YARN gyűjtése, összesítése és tárolása az alkalmazásnaplók töltött keretet biztosít a [napló összesítési] [ log-aggregation] szolgáltatás. A napló összesítési szolgáltatás elérhetővé teszi az elérése során alkalmazásnaplók sokkal kiszámíthatóbb naplók von össze az összes tároló, a munkavégző csomópont, és tárolja őket egy összesített naplófájlhoz munkavégző csomópont az alapértelmezett fájlrendszeren alkalmazás befejeződése után. Az alkalmazás használhat több száz vagy ezer tárolók, de egyetlen munkavégző csomóponton futtassa az összes tároló naplókat mindig összesíthető egy fájlba, ami azt eredményezi, amelyet az alkalmazás munkavégző csomópont kiszolgálónként egy naplófájlba. A HDInsight-fürtökön alapértelmezés szerint engedélyezve van a napló összesítési (3.0-s verzió vagy újabb verzió), és az összesített naplók az alapértelmezett tárolóban, a fürt a következő helyen találhatók:

    wasb:///app-logs/<user>/logs/<applicationId>

Az adott helyre *felhasználói* az alkalmazást elindító felhasználó neve és *applicationId* van egy alkalmazás egyedi azonosítója, a YARN RM által hozzárendelt

Az összesített naplók nincsenek közvetlenül is olvasható, mivel az oktatóprogram egy [TFile][T-file], [bináris formátum] [ binary-format] indexelik a tárolóban. YARN dump ezek a naplók az alkalmazások vagy a tárolókat érdeklő egyszerű szövegként CLI eszközöket biztosít. Ezek a naplók (való csatlakozását követően az RDP keresztül) közvetlenül a fürtcsomópontokon parancsok futtatásával a következő YARN egyik egyszerű szöveges módon tekintheti meg:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN erőforrás-kezelő felhasználói felületen
A YARN erőforrás-kezelő felhasználói felületén a fürt headnode fut, és az Azure-portál irányítópultjának keresztül érhetők el:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A bal oldali menüben kattintson **Tallózás**, kattintson a **a HDInsight-fürtök**, kattintson egy Windows-alapú fürt, amely a használni kívánt alkalmazás YARN naplóit.
3. Kattintson a felső menüben **irányítópult**. Megjelenik egy lap nyílik meg egy új böngészőt nevű lap **HDInsight lekérdezés konzol**.
4. A **HDInsight lekérdezés konzol**, kattintson a **Yarn felhasználói felületen**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
