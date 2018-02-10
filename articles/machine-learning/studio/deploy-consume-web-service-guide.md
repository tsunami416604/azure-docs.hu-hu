---
title: "Az Azure Machine Learning webszolgáltatások: Telepítés és használat |} Microsoft Docs"
description: "Erőforrások üzembe helyezéséhez és webszolgáltatások felhasználása."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl
ms.openlocfilehash: 88a61467a79a424670d49e662315cab59ab52d13
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning webszolgáltatások: telepítés és használat
Azure Machine Learning segítségével telepítheti a gépi tanulásra munkafolyamatok és -modellek webszolgáltatásként. Ezek a webszolgáltatások majd használható a gépi tanulási modelljeit felelnek meg, alkalmazásokat és a valós idejű vagy kötegelt módban előrejelzéseket ehhez az interneten keresztül. Mivel a webes szolgáltatások RESTful, hívása azokat a különböző programozási nyelveket és platformok, például a .NET és a Java, és az alkalmazások, például az Excel.

A következő szakaszokban mutató hivatkozásokat forgatókönyvek, a kódot, valamint a dokumentációt, amelyik az első lépések megtételéhez.

## <a name="deploy-a-web-service"></a>Webszolgáltatás üzembe helyezése

### <a name="with-azure-machine-learning-studio"></a>Az Azure Machine Learning Studio
A Machine Learning Studio és a Microsoft Azure Machine Learning webszolgáltatások portál segítségével telepítheti és kezelheti egy webszolgáltatás-bővítmény kód írása nélkül.

Az alábbi hivatkozások egy új webszolgáltatás-bővítmény telepítésével kapcsolatos általános adatok megadása:

* Megtudhatja, hogyan telepíthet egy új webes szolgáltatás, amely az Azure Resource Manageren alapul, [egy új webszolgáltatás-bővítmény telepítése](publish-a-machine-learning-web-service.md).
* Egy webszolgáltatás-bővítmény telepítésével kapcsolatos útmutatást lásd: [központi telepítése az Azure Machine Learning webszolgáltatás](publish-a-machine-learning-web-service.md).
* Arról, hogyan hozhat létre és telepíthet egy webes szolgáltatás teljes útmutatást lásd: [forgatókönyv 1. lépés: a Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md).
* Adott, amely egy webszolgáltatás-bővítmény telepítése című részben talál példákat:

  * [Útmutató 5. lépés: A Azure Machine Learning webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
  * [Több régióba egy webszolgáltatás-bővítmény telepítése](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>A web services erőforrás-szolgáltató API-k (Azure Resource Manager API-k)
Az Azure Machine Learning webszolgáltatások erőforrás-szolgáltató REST API-hívásokkal üzembe helyezési és kezelési szolgáltatás lehetővé teszi. További részletekért lásd: a [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) hivatkozás.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>A PowerShell-parancsmagokkal
Az Azure Machine Learning erőforrás-szolgáltató web Services lehetővé teszi, hogy üzembe helyezési és kezelési webszolgáltatások PowerShell-parancsmagok használatával.

A parancsmagokat használja, akkor először be kell jelentkeznie Azure-fiókjába a PowerShell környezetben használatával a [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) parancsmag. Ha nincs tisztában a hogyan hívhatja meg PowerShell-parancsok alapuló erőforrás-kezelő, lásd: [az Azure PowerShell használata Azure Resource Managerrel](../../azure-resource-manager/powershell-azure-resource-manager.md#log-in-to-your-azure-account).

A prediktív kísérletté exportálásához használja [a mintakód](https://github.com/ritwik20/AzureML-WebServices). Miután létrehozta az .exe fájl kód, adhatja meg:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Az alkalmazás futtatása hoz létre a JSON webes szolgáltatássablont. A sablon telepítéséhez egy webszolgáltatás-bővítmény használatához hozzá kell adni a következő információkat:

* A tárfiók neve vagy a kulcs

    A tárfiók nevének beolvasása és a kulcsot a [Azure-portálon](https://portal.azure.com/).
* Előfizetési csomag azonosítója

    A terv azonosítója kaphat a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portál jelentkezik be, majd kattintson a csomag neve.

Adja hozzá a JSON-sablon gyermekeként a *tulajdonságok* csomópont ugyanazon a szinten az *MachineLearningWorkspace* csomópont.

Például:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

A következő cikkek és mintakód további részletekért lásd:

* [Az Azure Machine Learning parancsmagok](https://msdn.microsoft.com/library/azure/mt767952.aspx) hivatkozást az MSDN webhelyen
* A minta [forgatókönyv](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) a Githubon

## <a name="consume-the-web-services"></a>A webszolgáltatások felhasználása
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Az az Azure Machine Learning-webszolgáltatásokat (tesztelés) felhasználói felület
A webszolgáltatás az Azure Machine Learning webszolgáltatások portálról tesztelheti. Ez magában foglalja a kérés-válasz szolgáltatás (RR-EKET) tesztelése, és hogyan kötegelt végrehajtási szolgáltatás (BES).

* [Új webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md)
* [Az Azure Machine Learning webszolgáltatás telepítése](publish-a-machine-learning-web-service.md)
* [Útmutató 5. lépés: A Azure Machine Learning webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Az Excelből
Egy Excel-sablont, amely a webes szolgáltatás akkor tölthető le:

* [Az Azure Machine Learning webszolgáltatásba az Excelből felhasználása](consuming-from-excel.md)
* [Az Excel beépülő modul az Azure Machine Learning webszolgáltatások](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Egy REST-alapú ügyfél
Az Azure Machine Learning webszolgáltatások RESTful API-k. Használatba vehetné ezen API-k, a különböző platformokon, például a .NET, Python, R, Java, stb. A **felhasználás** lapot a webszolgáltatás a [Microsoft Azure Machine Learning webszolgáltatások portal](https://services.azureml.net) , amelyik segíthet a kezdéshez mintakód rendelkezik. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).
