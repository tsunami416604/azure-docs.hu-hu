---
title: Saját egyéni Hadoop-alkalmazások telepítése Azure HDInsight platformon | Microsoft Docs
description: A HDInsight-alkalmazások HDInsight-alkalmazásokra való telepítésének ismertetése.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e556b29c-8176-4bc5-a90b-aa01abfd3aee
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: ab0395f9ee9a8950b439f19502ca6cef6c4f4cf2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201635"
---
# <a name="install-custom-hadoop-applications-on-azure-hdinsight"></a>Egyéni Hadoop-alkalmazások telepítése Azure HDInsight platformon

Ebből a cikkből megtudhatja, hogyan telepíthet korábban az Azure Portalon közzé nem tett Hadoop-alkalmazásokat Azure HDInsight platformon. Ebben a cikkben a [Hue](http://gethue.com/) alkalmazást fogja telepíteni.

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek.  Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Egyéb kapcsolódó cikkek:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-hdinsight-applications"></a>HDInsight-alkalmazások telepítése
HDInsight-alkalmazások fürt létrehozása közben vagy egy meglévő HDInsight-fürtre is telepíthetők. Azure Resource Manager-sablonok meghatározása: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).

Az alkalmazás üzembe helyezéséhez szükséges fájlok (Hue esetén):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight-alkalmazás telepítésére szolgáló Resource Manager-sablon. Lásd: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Az élcsomópont konfigurálásához a Resource Manager-sablon által meghívott szkriptművelet.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): A hui-install_v0.sh fájlból meghívott bináris Hue-fájl.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): A hui-install_v0.sh fájlból meghívott bináris Hue-fájl.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): A hui-install_v0.sh fájlból meghívott webes mintaalkalmazás (Tomcat).

**A Hue telepítése meglévő HDInsight-fürtre**

1. Az alábbi képre kattintva beléphet az Azure szolgáltatásba, és megnyithatja a Resource Manager-sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Erre a gombra kattintva megnyithat egy Resource Manager-sablont az Azure Portalon.  A Resource Manager-sablon itt található: [ https://github.com/hdinsight/Iaas-Applications/tree/master/Hue ](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue).  A Resource Manager-sablon írásának ismertetése: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).
2. A **Parameters** (Paraméterek) panelen adja meg a következőket:

   * **ClusterName**: Adja meg annak a fürtnek a nevét, amelyen az alkalmazást telepíteni kívánja. Ennek létező fürtnek kell lennie.
3. Kattintson az **OK** gombra a paraméterek mentéséhez.
4. A **Custom deployment** (Egyéni üzembe helyezés) panelen adjon meg egy **erőforráscsoportot**.  Az erőforráscsoport egy olyan tároló, amely csoportosítja a fürtöt, a függő tárfiókot és egyéb erőforrásokat. A fürttel megegyező erőforráscsoportot kell használnia.
5. Kattintson a **Legal terms** (Jogi feltételek), majd a **Create** (Létrehozás) gombra.
6. Ellenőrizze, hogy a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzet be van-e jelölve, majd kattintson a **Create** (Létrehozás) gombra. A telepítés állapotát a portál irányítópultjára rögzített csempén vagy a portál értesítései között (ehhez kattintson a portál felső részén található harang ikonra) követheti.  Az alkalmazás telepítése nagyjából 10 percet vesz igénybe.

**A Hue telepítése fürt létrehozása közben**

1. Az alábbi képre kattintva beléphet az Azure szolgáltatásba, és megnyithatja a Resource Manager-sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Erre a gombra kattintva megnyithat egy Resource Manager-sablont az Azure Portalon.  A Resource Manager-sablon itt található: [ https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json ](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json).  A Resource Manager-sablon írásának ismertetése: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).
2. Kövesse a fürt létrehozására és a Hue telepítésére vonatkozó utasításokat. További információk a HDInsight-fürtökről: [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsight szolgáltatásban).

A Resource Manager-sablonok meghívására az Azure Portalon kívül az [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) és az [Azure parancssori felület](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-cli) is használható.

## <a name="validate-the-installation"></a>A telepítés ellenőrzése
Az alkalmazás telepítésének ellenőrzéséhez az alkalmazás állapotát az Azure Portalon követheti. Emellett a várt megjelenésük sorrendjében ellenőrizheti a HTTP-végpontokat is, illetve a webhelyet (ha van):

**A Hue portál megnyitása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.  Ha nem látja, kattintson a **Browse** (Tallózás), majd a **HDInsight Clusters** (HDInsight-fürtök) elemre.
3. Kattintson arra a fürtre, amelyre az alkalmazást telepítette.
4. A **Settings** (Beállítások) panelen kattintson az **Applications** (Alkalmazások) elemre a **General** (Általános) kategóriában. A **hue** ekkor megjelenik az **Installed Apps** (Telepített alkalmazások) panelen.
5. A tulajdonságok listázásához kattintson a **Hue** elemre a listában.  
6. Kattintson annak hivatkozására; a webhely ellenőrzéséhez Nyissa meg a HTTP-végpont egy böngészőt, és ellenőrizze a Hue webes felhasználói felület, nyissa meg az SSH-végpont SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="troubleshoot-the-installation"></a>A telepítési folyamat hibaelhárítása
Az alkalmazás telepítési állapotát a portál értesítései között (ehhez kattintson a portál felső részén található harang ikonra) követheti.

Ha az alkalmazás telepítése sikertelen, a következő három helyen tekintheti meg a hibaüzeneteket és a hibakeresési információkat:

* HDInsight-alkalmazások: általános hibainformációk.

    Nyissa meg a fürtöt a portálról, majd kattintson az Applications (Alkalmazások) lehetőségre a Settings (Beállítások) panelen:

    ![hdinsight applications application installation error](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* HDInsight-parancsfájlművelet: ha a HDInsight-alkalmazás hibaüzenete parancsfájlművelet-hibát jelez, a hibáról további információkat a parancsfájlműveletek paneljén talál.

    A Settings (Beállítások) panelen kattintson a Script Action (Parancsfájlművelet) elemre. A hibaüzeneteket megjelenítő parancsfájlművelet-előzmény

    ![hdinsight applications script action error](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Ambari webes felhasználói felület esetén: ha a hiba oka a telepítési parancsfájl volt, az Ambari webes felhasználói felületen áttekintheti a teljes telepítésiparancsfájl-naplót.

    További információk: [Hibaelhárítás](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>HDInsight-alkalmazások eltávolítása
A HDInsight-alkalmazások többféleképpen törölhetők.

### <a name="use-portal"></a>A portál használatával
**Alkalmazások eltávolítása a portál használatával**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.  Ha nem látja, kattintson a **Browse** (Tallózás), majd a **HDInsight Clusters** (HDInsight-fürtök) elemre.
3. Kattintson arra a fürtre, amelyre az alkalmazást telepítette.
4. A **Settings** (Beállítások) panelen kattintson az **Applications** (Alkalmazások) elemre a **General** (Általános) kategóriában. Ekkor a telepített alkalmazások listája jelenik meg. A **hue** elem ebben az oktatóanyagban az **Installed Apps** (Telepített alkalmazások) panelen található.
5. Kattintson a jobb gombbal az eltávolítani kívánt alkalmazásra, majd kattintson a **Delete** (Törlés) lehetőségre.
6. Kattintson a **Yes** (Igen) gombra a megerősítéshez.

A portálon törölheti a fürtöt, vagy akár az alkalmazást tartalmazó erőforráscsoportot is.

### <a name="use-azure-powershell"></a>Azure PowerShell használatával
Az Azure PowerShell használatával törölheti a fürtöt vagy az erőforráscsoportot. Lásd: [Fürtök törlése az Azure PowerShell használatával](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Az Azure parancssori felület használatával törölheti a fürtöt vagy az erőforráscsoportot. Lásd: [Fürtök törlése az Azure parancssori felület használatával](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>További lépések
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan fejleszthet Resource Manager-sablonokat HDInsight-alkalmazások üzembe helyezéséhez.
* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével): Megtudhatja, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.
