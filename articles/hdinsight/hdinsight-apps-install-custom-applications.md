---
title: Telepítse egyéni Apache Hadoop alkalmazásait az Azure HDInsightszolgáltatásra
description: Ismerje meg, hogyan telepíthet HDInsight-alkalmazásokat Apache Hadoop-fürtökhöz az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: b25e9d860f118c1b044b13e01a80aaf7a24963cf
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768250"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Egyéni Apache Hadoop-alkalmazások telepítése az Azure HDInsight szolgáltatásra

Ebből a cikkből megtudhatja, hogyan telepíthet egy [Apache Hadoop-alkalmazást](https://hadoop.apache.org/) az Azure HDInsightra, amely még nem lett közzétéve az Azure Portalon. A cikkben telepíti a [Hue](https://gethue.com/)alkalmazást.

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók hdinsight-fürtre telepíthetnek.  Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

## <a name="prerequisites"></a>Előfeltételek

Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-hdinsight-applications"></a>HDInsight-alkalmazások telepítése

HDInsight-alkalmazások fürt létrehozása közben vagy egy meglévő HDInsight-fürtre is telepíthetők. Azure Resource Manager-sablonok meghatározása: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).

Az alkalmazás üzembe helyezéséhez szükséges fájlok (Hue esetén):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight-alkalmazás telepítésére szolgáló Resource Manager-sablon. Lásd: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Az élcsomópont konfigurálásához a Resource Manager-sablon által meghívott szkriptművelet.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): A hui-install_v0.sh fájlból meghívott bináris Hue-fájl.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): A hui-install_v0.sh fájlból meghívott bináris Hue-fájl.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): A hui-install_v0.sh fájlból meghívott webes mintaalkalmazás (Tomcat).

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>A Hue telepítése meglévő HDInsight-fürtre

1. Válassza ki az alábbi képet az Azure-ba való bejelentkezéshez, és nyissa meg a Resource Manager sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Az Erőforrás-kezelő sablon [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)a helyen található.  A Resource Manager-sablon írásának ismertetése: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).

1. Válassza ki a fürtöt tartalmazó meglévő **erőforráscsoportot** a legördülő listából. Ugyanazt az erőforráscsoportot kell használnia, mint a fürt.

1. Adja meg annak a fürtnek a nevét, amelybe telepíteni szeretné az alkalmazást. Ennek létező fürtnek kell lennie.

1. Jelölje be a bejelölő I **agree to the terms ind stated (A fenti feltételeket**.

1. Válassza a **Beszerzés** lehetőséget.

A telepítés állapotát a portál irányítópultjára rögzített csempén vagy a portál értesítései között (ehhez kattintson a portál felső részén található harang ikonra) követheti.  Az alkalmazás telepítése nagyjából 10 percet vesz igénybe.

### <a name="to-install-hue-while-creating-a-cluster"></a>A Hue telepítése fürt létrehozása közben

1. Válassza ki az alábbi képet az Azure-ba való bejelentkezéshez, és nyissa meg a Resource Manager sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Az Erőforrás-kezelő sablon [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)a helyen található.  A Resource Manager-sablon írásának ismertetése: [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése).

2. Kövesse a fürt létrehozására és a Hue telepítésére vonatkozó utasításokat. További információk a HDInsight-fürtökről: [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsight szolgáltatásban).

### <a name="other-installation-methods"></a>Egyéb telepítési módszerek

A Resource Manager-sablonok meghívására az Azure Portalon kívül az [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) és az [Azure parancssori felület](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) is használható.

## <a name="validate-the-installation"></a>A telepítés ellenőrzése

Az alkalmazás telepítésének ellenőrzéséhez az alkalmazás állapotát az Azure Portalon követheti. Emellett az összes HTTP-végpontot is ellenőrizheti a várt módon, és a weblapot is, ha van ilyen.

A **Hue**esetében a következő lépéseket használhatja:

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki azt a fürtöt, amelyen az alkalmazást telepítette.
1. A **Beállítások** menüben válassza az **Alkalmazások**lehetőséget.
1. A tulajdonságok megtekintéséhez jelölje ki a **színárnyalatot** a listából.  
1. A webhely érvényesítéséhez válassza a weblap hivatkozását.

### <a name="azure-cli"></a>Azure CLI

Cserélje `CLUSTERNAME`ki `RESOURCEGROUP` a megfelelő értékeket, majd írja be az alábbi parancsokat:

* A HDInsight-fürt összes alkalmazásának listázása.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* A megadott alkalmazás tulajdonságainak beolvasása.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>A telepítési folyamat hibaelhárítása

Az alkalmazás telepítési állapotát a portál értesítései között (ehhez kattintson a portál felső részén található harang ikonra) követheti.

Ha egy alkalmazás telepítése nem sikerült, három helyről láthatja a hibaüzeneteket és a hibakeresési információkat:

* HDInsight-alkalmazások: általános hibainformációk.

    Nyissa meg a fürtöt a portálról, és válassza az Alkalmazások elemet a Beállítások közül:

    ![hdinsight applications application installation error](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* HDInsight-parancsfájlművelet: ha a HDInsight-alkalmazás hibaüzenete parancsfájlművelet-hibát jelez, a hibáról további információkat a parancsfájlműveletek paneljén talál.

    Válassza a Parancsfájlművelet lehetőséget a Beállítások között. A hibaüzeneteket megjelenítő parancsfájlművelet-előzmény

    ![hdinsight applications script action error](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari Web UI: Ha a telepítési parancsfájl volt az oka a hiba, használja Ambari Web UI, hogy ellenőrizze a teljes naplókat a telepítési parancsfájlok.

    További információt a [Parancsfájlműveletek hibaelhárítása című témakörben talál.](./troubleshoot-script-action.md)

## <a name="remove-hdinsight-applications"></a>HDInsight-alkalmazások eltávolítása

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki azt a fürtöt, amelyen az alkalmazást telepítette.
1. A **Beállítások** menüben válassza az **Alkalmazások**lehetőséget.
1. Kattintson a jobb gombbal az eltávolítani kívánt alkalmazásra, majd válassza a **Törlés parancsot.**
1. Válassza az **Igen** lehetőséget a megerősítéshez.

### <a name="azure-cli"></a>Azure CLI

Cserélje `NAME` `CLUSTERNAME`ki `RESOURCEGROUP` a , és a megfelelő értékeket, majd adja meg az alábbi parancsot:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>További lépések

* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan fejleszthet Resource Manager-sablonokat HDInsight-alkalmazások üzembe helyezéséhez.
* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtöket hozhat létre a HDInsightban az Erőforrás-kezelő sablonjaival:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)ismerje meg, hogyan hívhatja meg az Erőforrás-kezelő sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.
