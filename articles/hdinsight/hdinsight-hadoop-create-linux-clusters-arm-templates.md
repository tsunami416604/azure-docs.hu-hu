---
title: Az Apache Hadoop-fürtök létrehozása – Azure HDInsight-sablonok használatával
description: Útmutató a HDInsight-fürtök létrehozásának Resource Manager-sablonok használatával
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 1a053f92433b6091f0d77439399e8419a58df185
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495143"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>HDInsight az Apache Hadoop-fürtök létrehozása Resource Manager-sablonok használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebből a cikkből megtudhatja, többféle módon hozhat létre az Azure HDInsight-fürtöket az Azure Resource Manager-sablonok használatával. További információkért lásd: [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](../azure-resource-manager/resource-group-template-deploy.md). Más eszközök a fürt létrehozása és a szolgáltatások kapcsolatos további információkért kattintson ezen az oldalon lévő lapválasztót, vagy tekintse meg [Fürtlétrehozási módszerekhez](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

A cikkben található útmutatások követéséhez lesz szüksége:

* Egy [Azure-előfizetés](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Az Azure PowerShell, illetve az Azure klasszikus parancssori felület.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok
A Resource Manager-sablon megkönnyíti a következő resoruces az alkalmazás létrehozása az egyetlen, koordinált műveletben:
* HDInsight-fürtök és a tőle függő erőforrások (például az alapértelmezett tárfiók)
* Egyéb erőforrások (például az Azure SQL Database használatához [Apache Sqoop](http://sqoop.apache.org/))

A sablon az erőforrásokat, amelyek szükségesek az alkalmazás határoz meg. Is adja meg az üzembe helyezéshez megadott paraméterek beviteli különböző környezetekhez tartozó értékeket. A sablon JSON-t és kifejezések, amelyek segítségével kialakíthatja az üzemelő példány értékeit áll.

A HDInsight-sablonminták annak [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?term=hdinsight). Használja a platformok közötti [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) együtt a [erőforrás-kezelő bővítmény](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) vagy egy szövegszerkesztőben menteni a sablont egy fájlba a munkaállomáson. 

Resource Manager-sablonokkal kapcsolatos további információkért lásd a következő cikkeket:

* [Szerzői Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Sablonok készítése

Resource Manager lehetővé teszi, hogy a Resource Manager-sablonok exportálása létező erőforrásokból, különböző eszközök használatával az előfizetésében. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

- Az Azure portal: lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](../azure-resource-manager/resource-manager-export-template.md).
- Az Azure PowerShell: Lásd: [exportálása az Azure Resource Manager-sablonok a PowerShell-lel](../azure-resource-manager/resource-manager-export-template-powershell.md).
- Az Azure klasszikus parancssori felület: Lásd: [exportálása az Azure Resource Manager-sablonok az Azure klasszikus parancssori felület](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Üzembe helyezés a portálon

Telepíthet egy Resource Manager-sablon az Azure portal használatával. További információkért lásd: [erőforrások egyéni sablon üzembe helyezése](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Üzembe helyezés a PowerShell használatával

A Resource Manager-sablon Azure PowerShell-lel is telepítheti. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md) és [üzembe helyezés saját Resource Manager-sablon az SAS-jogkivonat és az Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI használatával

Klasszikus parancssori felület használatával a Resource Manager-sablon is telepítheti. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../azure-resource-manager/resource-group-template-deploy-cli.md) és [üzembe helyezés saját Resource Manager-sablon az SAS-jogkivonat és az Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Üzembe helyezés a REST API használatával
A Resource Manager-sablon a REST API használatával is telepítheti. További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonok és a Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval
 A Visual Studio használatával egy erőforráscsoport-projekt létrehozása és üzembe helyezése az Azure a felhasználói felületen keresztül. A projektnek erőforrások típusának kiválasztásával. Ezeket az erőforrásokat a rendszer automatikusan hozzáadja a Resource Manager-sablon. A projekt is biztosít egy PowerShell-parancsfájlt a sablon üzembe helyezéséhez.

Megismerkedhet a Visual studióval való erőforráscsoportok, lásd: [létrehozása és telepítése az Azure erőforráscsoport-sablonok a Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>További lépések
Ebben a cikkben, hogyan hozhat létre HDInsight-fürtöt többféleképpen. További tudnivalókért tekintse meg a következő cikkeket:

* További HDInsight kapcsolódó sablonok lásd [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Az üzembe helyezni erőforrásokat, a .NET ügyféloldali kódtár használatával egy példa: [használatával a .NET-kódtárakkal és sablonokkal helyezheti üzembe az erőforrásokat](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Részletes példa egy alkalmazás központi telepítése, lásd: [rendelkezést kiszámítható módon az Azure mikroszolgáltatások üzembe helyezéséhez és](../app-service/app-service-deploy-complex-application-predictably.md).
* Útmutató a megoldások különböző környezetekben történő telepítéséhez: [Fejlesztési és tesztelési környezetek a Microsoft Azure eszközben](../solution-dev-test-environments.md).
* Az Azure Resource Manager-sablon szakaszok kapcsolatos további információkért lásd: [sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).
* Az Azure Resource Manager-sablon használható függvények listáját lásd: [sablonfüggvények](../azure-resource-manager/resource-group-template-functions.md).
