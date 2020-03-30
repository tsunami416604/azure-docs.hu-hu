---
title: Apache Hadoop-fürtök létrehozása sablonok használatával – Azure HDInsight
description: Megtudhatja, hogy miként hozhat létre fürtöket a HDInsight számára az Erőforrás-kezelő sablonok használatával
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 9498f2cf56f0bfe20d0806e5dc9872403dabb180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979107"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Apache Hadoop-fürtök létrehozása a HDInsightban erőforrás-kezelősablonok használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebben a cikkben az Azure Resource Manager-sablonok használatával számos módon hozhat létre Azure HDInsight-fürtöket. További információ: [Alkalmazás telepítése az Azure Resource Manager sablonnal](../azure-resource-manager/templates/deploy-powershell.md)című témakörben talál. Ha többet szeretne megtudni a fürtlétrehozási eszközökről és szolgáltatásokról, kattintson a lap tetején található fülválasztóra, vagy olvassa el a [Fürtlétrehozási módszerek című témakört.](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Egy Azure-előfizetés](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell és/vagy Azure CLI.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az Erőforrás-kezelő sablonokkal egyszerűen hozhat létre a következő erőforrásokat az alkalmazáshoz egyetlen, összehangolt műveletben:
* HDInsight-fürtök és azok függő erőforrásait (például az alapértelmezett tárfiókot).
* Egyéb erőforrások (például az Apache [Sqoop](https://sqoop.apache.org/)használatához szükséges Azure SQL Database).

A sablonban megadhatja az alkalmazáshoz szükséges erőforrásokat. A különböző környezetekben megadott telepítési paramétereket is megadhatja. A sablon JSON-ból és kifejezésekből áll, amelyek segítségével értékeket hozhat létre a központi telepítéshez.

A HDInsight-sablonmintákat az [Azure gyorsindítási sablonjaiban találhatja](https://azure.microsoft.com/resources/templates/?term=hdinsight)meg. Az [Erőforrás-kezelő kiterjesztéssel](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) rendelkező [platformfüggetlen Visual Studio-kód](https://code.visualstudio.com/#alt-downloads) dal vagy szövegszerkesztővel mentheti a sablont egy munkaállomáson lévő fájlba.

Az Erőforrás-kezelő sablonjairól az alábbi cikkekben és példákban talál további információt:

* [Az Azure Resource Manager sablonjainak szerzője](../azure-resource-manager/templates/template-syntax.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions) sablon – referencia
* [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Sablonok létrehozása

Az Erőforrás-kezelő lehetővé teszi, hogy különböző eszközökkel exportáljon egy Erőforrás-kezelő sablont az előfizetés meglévő erőforrásaiból. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését. További információt a Sablonok exportálása című [témakörben talál.](../azure-resource-manager/templates/export-template-portal.md)

## <a name="deploy-using-the-portal"></a>Üzembe helyezés a portál használatával

A Resource Manager-sablon az Azure Portalon keresztül telepítheti. További információt az [Erőforrások telepítése egyéni sablonból című](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)témakörben talál.

## <a name="deploy-using-powershell"></a>Üzembe helyezés a PowerShell használatával

A Resource Manager-sablon t az Azure PowerShell használatával telepítheti. További információ: [Erőforrások üzembe helyezése Erőforrás-kezelő sablonokkal és Az Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) és [a Private Resource Manager-sablon SAS-jogkivonattal és Az Azure PowerShell használatával](../azure-resource-manager/resource-manager-powershell-sas-token.md)című témakörben olvashat.

## <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

A Resource Manager-sablon üzembe helyezhető az Azure CLI használatával. További információ: [Erőforrások üzembe helyezése Erőforrás-kezelő sablonokkal és Az Azure CLI](../azure-resource-manager/templates/deploy-cli.md) és [a Private Resource Manager sablon telepítése SAS-jogkivonattal és Az Azure CLI szolgáltatással](../azure-resource-manager/resource-manager-cli-sas-token.md)című témakörben olvashat.

## <a name="deploy-using-the-rest-api"></a>Telepítés a REST API használatával

A REST API használatával telepíthet egy Erőforrás-kezelő sablont. További információ: [Erőforrások telepítése erőforrás-kezelői sablonokkal és Erőforrás-kezelő REST API-val](../azure-resource-manager/templates/deploy-rest.md)című témakörben talál.

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval

 A Visual Studio segítségével hozzon létre egy erőforráscsoport-projektet, és telepítse azt az Azure-ba a felhasználói felületen keresztül. Kiválaszthatja, hogy milyen típusú erőforrásokat vegyen fel a projektbe. Ezek az erőforrások automatikusan hozzáadódnak az Erőforrás-kezelő sablonhoz. A projekt egy PowerShell-parancsfájlt is biztosít a sablon üzembe helyezéséhez.

A Visual Studio erőforráscsoportokkal való használatának bemutatása Az [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióban című](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)témakörben található.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>További lépések

Ebben a cikkben a HDInsight-fürt létrehozásának számos módját ismerteelheti. További információ: a következő cikkek:

* További HDInsight-kapcsolódó sablonokról az [Azure gyorsindítási sablonjai ban](https://azure.microsoft.com/resources/templates/?term=hdinsight)olvashat.
* Az erőforrások .NET ügyféltáron keresztül történő központi telepítéséről az [Erőforrások telepítése .NET tárak és egy sablon használatával](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben olvashat.
* Egy alkalmazás üzembe helyezésének részletes példáját [lásd: A mikroszolgáltatások kiszámíthatóan az Azure-ban.](../app-service/deploy-complex-application-predictably.md)
* Útmutató a megoldások különböző környezetekben történő telepítéséhez: [Fejlesztési és tesztelési környezetek a Microsoft Azure eszközben](../solution-dev-test-environments.md).
* Az Azure Resource Manager-sablon szakaszairól a [Sablonok készítése](../azure-resource-manager/templates/template-syntax.md)című témakörben olvashat.
* Az Azure Resource Manager-sablonban használható függvények listáját a [Sablonfüggvények című témakörben olvashatja.](../azure-resource-manager/templates/template-functions.md)
