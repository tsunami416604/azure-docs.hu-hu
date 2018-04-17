---
title: Sablonokkal - Azure HDInsight Hadoop-fürtök létrehozása |} Microsoft Docs
description: Ismerje meg a fürt létrehozása HDInsight Resource Manager-sablonok segítségével
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 1c50c3fbef5495171693b64e410f858af6176147
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Hadoop-fürtök létrehozása a Hdinsightban Resource Manager-sablonok használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebből a cikkből megismerheti Azure Resource Manager-sablonok Azure HDInsight-fürtök létrehozásának számos módja. További információkért lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](../azure-resource-manager/resource-group-template-deploy.md). Más fürt létrehozása eszközeivel és szolgáltatásaival kapcsolatos információkért kattintson az ezen a lapon lévő lapválasztót, vagy tekintse meg [Fürtlétrehozási módszerekhez](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ez a cikk útmutatását, az alábbiak szükségesek:

* Egy [Azure-előfizetés](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Az Azure PowerShell és/vagy Azure CLI-t.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok
A Resource Manager-sablon megkönnyíti, hogy az alkalmazás a következő resoruces egyetlen, koordinált műveletben létrehozásához:
* A HDInsight-fürtök és a tőle függő erőforrások (például az alapértelmezett tárfiók)
* További erőforrások (például az Azure SQL Database Apache Sqoop használatával)

A sablon határozza meg az erőforrásokat, amelyek szükségesek az alkalmazás. Is meg az üzembe helyezéshez megadott paraméterek a felhasználótól a különböző környezetekhez tartozó értékeket. A sablon JSON és az üzemelő példány értékeit összeállításához használt kifejezések áll.

HDInsight sablon minták található [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/?term=hdinsight). Használja a platformok közötti [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) rendelkező a [erőforrás-kezelő bővítmény](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) vagy egy szövegszerkesztőben, hogy menti a sablont egy fájlba a munkaállomáson. 

Erőforrás-kezelő sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Szerző Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md)
* [Alkalmazás üzembe helyezése az Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Sablonok készítése

Erőforrás-kezelő lehetővé teszi a Resource Manager-sablonok exportálása létező erőforrásokból különböző eszközök használatával az előfizetésben. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

- Azure-portálon: lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](../azure-resource-manager/resource-manager-export-template.md).
- Az Azure PowerShell: Lásd: [exportálása Azure Resource Manager-sablon is van a PowerShell](../azure-resource-manager/resource-manager-export-template-powershell.md).
- Az Azure CLI: Lásd: [exportálása Azure Resource Manager-sablon is van az Azure parancssori felület](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>A portál használatával telepítése

A Resource Manager-sablon az Azure portál használatával telepítheti. További információkért lásd: [egyéni sablont az erőforrások telepítése](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Telepítheti a PowerShell használatával

A Resource Manager-sablon Azure PowerShell használatával telepítheti. További információkért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../azure-resource-manager/resource-group-template-deploy.md) és [telepítés titkos Resource Manager-sablon Azure PowerShell és a SAS-jogkivonat](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-cli"></a>Telepítheti a parancssori felület használatával

A Resource Manager-sablon Azure parancssori felülettel is telepíthet. További információkért lásd: [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](../azure-resource-manager/resource-group-template-deploy-cli.md) és [telepítés titkos Resource Manager-sablon Azure CLI és SAS-jogkivonat](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>A REST API használatával telepítése
A Resource Manager-sablon REST API használatával telepítheti. További információkért lásd: [központi telepítése a Resource Manager-sablonok és a Resource Manager REST API erőforrások](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval
 Visual Studio használatával hozzon létre egy erőforráscsoport-projekt, és telepítse az Azure a felhasználói felületen keresztül. Milyen típusú erőforrásokat tartalmazza a projekt választja. Ezeket az erőforrásokat a rendszer automatikusan hozzáadja a Resource Manager-sablon. A projekt a sablon telepítéséhez PowerShell parancsfájlt is tartalmaz.

Visual Studio használatával az erőforráscsoportokhoz bemutatása, lásd: [létrehozása és telepítése a Visual Studio használatával Azure erőforráscsoport-sablonok a](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta rendelkezik többféle módon hozhat létre HDInsight-fürtöt. További tudnivalókért tekintse meg a következő cikkeket:

* Több hdinsight kapcsolódó sablonjainak használatáról [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Például a .NET ügyféloldali kódtár erőforrásoknak történő telepítésének, [erőforrások telepíteni a .NET-kódtárakra és egy sablon](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Részletes példa az alkalmazások központi telepítése, lásd: [kiépítése és mikroszolgáltatások kiszámítható módon tudja az Azure-ban telepítheti](../app-service/app-service-deploy-complex-application-predictably.md).
* Útmutató a megoldások különböző környezetekben történő telepítéséhez: [Fejlesztési és tesztelési környezetek a Microsoft Azure eszközben](../solution-dev-test-environments.md).
* A szakaszok az Azure Resource Manager sablon kapcsolatos további tudnivalókért lásd: [sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).
* Az Azure Resource Manager-sablonokban használható függvények listáját lásd: [sablonfüggvényei](../azure-resource-manager/resource-group-template-functions.md).
