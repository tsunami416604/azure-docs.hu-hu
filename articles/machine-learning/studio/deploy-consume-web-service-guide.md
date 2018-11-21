---
title: 'Az Azure Machine Learning webszolgáltatások: Telepítés és használat |} A Microsoft Docs'
description: Erőforrások üzembe helyezéséhez és webszolgáltatások felhasználása.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 886cf0669565eeb87050331d787c33eb8b19d8c1
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261242"
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning webszolgáltatások: telepítés és használat
Az Azure Machine Learning segítségével telepítheti a machine-learning munkafolyamatok és a modellek webszolgáltatásként. Ezekhez a webszolgáltatásokhoz majd használható alkalmazások, a gépi tanulási modellek hívja meg az interneten-t az előrejelzések valós időben vagy kötegelt módban. Mivel a webes szolgáltatások RESTful, meghívhatja azokat a különböző programozási nyelvekhez és platformokhoz, mint például a .NET, a Java, és az alkalmazások, például az Excel.

A következő szakaszok forgatókönyvek, kód és a dokumentációt, első lépések megtételéhez mutató hivatkozásokat.

## <a name="deploy-a-web-service"></a>Webszolgáltatás üzembe helyezése

### <a name="with-azure-machine-learning-studio"></a>Az Azure Machine Learning studióval
Machine Learning Studio és a Microsoft Azure Machine Learning Web Services portál segítségével üzembe helyezése és kezelése egy webszolgáltatás kódírás nélkül.

Az alábbi hivatkozások új webszolgáltatásként üzembe helyezésével kapcsolatos általános adatok megadása:

* Azure Resource Manageren alapuló új webszolgáltatásként üzembe helyezésével kapcsolatos áttekintéséhez lásd: [egy új webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md).
* Egy webszolgáltatás üzembe helyezésével kapcsolatos általános bemutatóért lásd: [egy Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md).
* Hogyan hozhat létre, és a egy webszolgáltatás üzembe helyezése kapcsolatos teljes forgatókönyv, lásd: [forgatókönyv 1. lépés: hozzon létre a Machine Learning-munkaterület](walkthrough-1-create-ml-workspace.md).
* Webszolgáltatás üzembe helyezése konkrét példákat lásd:

  * [Az útmutató 5. lépés: Az Azure Machine Learning webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
  * [Webszolgáltatás üzembe helyezése több régióban](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>A web services-szolgáltató API-k (Azure Resource Manager API-k)
Az Azure Machine Learning web services-erőforrás-szolgáltató REST API-hívások segítségével üzembe helyezését és felügyeletét a webes szolgáltatások teszi lehetővé. További részletekért lásd: a [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) hivatkozást.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>A PowerShell-parancsmagokkal
Az Azure Machine Learning erőforrás-szolgáltató webszolgáltatások üzembe helyezését és felügyeletét a webes szolgáltatások lehetővé teszi PowerShell-parancsmagok használatával.

A parancsmagok használatához, először jelentkezzen be Azure-fiókjába, a PowerShell környezetben használatával a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot. Ha ismeri, hogyan hívhat meg alapuló PowerShell-parancsokat a Resource Managerrel, tekintse meg [az Azure PowerShell az Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

A prediktív kísérletté exportálásához használja [a mintakód](https://github.com/ritwik20/AzureML-WebServices). Az .exe fájlt hoz létre a kódot, miután írhatja be:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Az alkalmazás futtatása hoz létre a JSON-sablon. A webszolgáltatás üzembe helyezése a sablon használatához hozzá kell adnia a következő információkat:

* Tárfiók neve és kulcsa

    Beszerezheti a tárfiók nevét és a kulcs a [az Azure portal](https://portal.azure.com/).
* Kötelezettségvállalási csomag azonosítója

    A csomag Azonosítójának a lekéréséhez a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portál jelentkezik be, majd a csomag nevét.

Gyermekeiként hozzáadhatja őket a JSON-sablon a *tulajdonságok* csomópont azonos szinten, a *MachineLearningWorkspace* csomópont.

Például:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Tekintse meg a következő cikkek és további részleteket a mintakód:

* [Az Azure Machine Learning parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.machinelearning) hivatkozást az MSDN-en
* Minta [forgatókönyv](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) a Githubon

## <a name="consume-the-web-services"></a>A webszolgáltatások felhasználása
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Az Azure Machine Learning Web Services (tesztelés) felhasználói felület
Az Azure Machine Learning Web Services portálon a webszolgáltatás tesztelheti. Tesztelés a kérés-válasz szolgáltatás (RRS) és a kötegelt végrehajtási szolgáltatás (BES) felületek.

* [Új webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md)
* [Az Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md)
* [Az útmutató 5. lépés: Az Azure Machine Learning webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Az Excelből
Egy Excel-sablont, amely a web service tölthető le:

* [Excel-fájlból az Azure Machine Learning webszolgáltatás felhasználása](consuming-from-excel.md)
* [Excel-bővítmény az Azure Machine Learning-webszolgáltatások](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>A REST-alapú ügyfél
Az Azure Machine Learning webszolgáltatások olyan REST-alapú API-k. A különböző platformokon, például a .NET, Python, R, Java és egyéb API-k is használhatók. A **felhasználás** lapot a webszolgáltatás a [a Microsoft Azure Machine Learning Web Services portál](https://services.azureml.net) rendelkezik, amelyek segíthetnek a kezdéshez. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).
