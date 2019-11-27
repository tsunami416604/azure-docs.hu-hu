---
title: Üzembe helyezés és felhasználás
titleSuffix: ML Studio (classic) - Azure
description: A gépi tanulási munkafolyamatok és modellek webszolgáltatásként való üzembe helyezéséhez Azure Machine Learning Studio (klasszikus) használható. Ezekhez a webszolgáltatásokhoz majd hívja a gépi tanulási modellek alkalmazásokból az interneten-t az előrejelzések valós időben vagy kötegelt módban használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 1c97fd4f4d5646b6654f5261abd99372c521c389
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228237"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások: üzembe helyezés és felhasználás

A gépi tanulási munkafolyamatok és modellek webszolgáltatásként való üzembe helyezéséhez Azure Machine Learning Studio (klasszikus) használható. Ezekhez a webszolgáltatásokhoz majd hívja a gépi tanulási modellek alkalmazásokból az interneten-t az előrejelzések valós időben vagy kötegelt módban használható. Mivel a webes szolgáltatások RESTful, meghívhatja azokat a különböző programozási nyelvekhez és platformokhoz, mint például a .NET, a Java, és az alkalmazások, például az Excel.

A következő szakaszok forgatókönyvek, kód és a dokumentációt, első lépések megtételéhez mutató hivatkozásokat.

## <a name="deploy-a-web-service"></a>Webszolgáltatás üzembe helyezése

### <a name="with-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasszikus)

A Studio (klasszikus) portál és a Microsoft Azure Machine Learning Web Services portál segítségével programkód írása nélkül telepítheti és kezelheti a webszolgáltatásokat.

Az alábbi hivatkozások új webszolgáltatásként üzembe helyezésével kapcsolatos általános adatok megadása:

* A Azure Resource Manageron alapuló új webszolgáltatás üzembe helyezéséről az [új webszolgáltatás üzembe helyezését](deploy-a-machine-learning-web-service.md)ismertető cikkben olvashat.
* A webszolgáltatások üzembe helyezésével kapcsolatos útmutatóért lásd: [Azure Machine learning webszolgáltatás üzembe](deploy-a-machine-learning-web-service.md)helyezése.
* A webszolgáltatások létrehozásával és üzembe helyezésével kapcsolatos teljes útmutatóért először az [1. Oktatóanyag: a hitelkockázat előrejelzése](tutorial-part1-credit-risk.md)című témakörben olvashat.
* Webszolgáltatás üzembe helyezése konkrét példákat lásd:

  * [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md)
  * [Webszolgáltatás üzembe helyezése több régióban](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>A web services-szolgáltató API-k (Azure Resource Manager API-k)

A webszolgáltatások Azure Machine Learning Studio erőforrás-szolgáltatójának klasszikus verziója lehetővé teszi a webszolgáltatások üzembe helyezését és kezelését REST API-hívások használatával. További információ: [Machine learning webszolgáltatás (REST)](/rest/api/machinelearning/index) referenciája.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>A PowerShell-parancsmagokkal

A webszolgáltatások Azure Machine Learning Studio erőforrás-szolgáltatójának klasszikus verziója lehetővé teszi a webszolgáltatások üzembe helyezését és kezelését a PowerShell-parancsmagok használatával.

A parancsmagok használatához először be kell jelentkeznie az Azure-fiókjába a PowerShell-környezetből a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Ha nem tudja, hogyan hívhatja meg a Resource Manageren alapuló PowerShell-parancsokat, tekintse meg a [Azure PowerShell használata a Azure Resource Manager használatával](../../azure-resource-manager/manage-resources-powershell.md)című témakört.

A prediktív kísérlet exportálásához használja [ezt a mintakód-kódot](https://github.com/ritwik20/AzureML-WebServices). Az .exe fájlt hoz létre a kódot, miután írhatja be:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Az alkalmazás futtatása hoz létre a JSON-sablon. A webszolgáltatás üzembe helyezése a sablon használatához hozzá kell adnia a következő információkat:

* Tárfiók neve és kulcsa

    A Storage-fiók nevét és kulcsát a [Azure Portal](https://portal.azure.com/)kérheti le.
* Kötelezettségvállalási csomag azonosítója

    A díjcsomag beszerzéséhez jelentkezzen be a [Azure Machine learning Web Services](https://services.azureml.net) portálról, és kattintson a csomag nevére.

Adja hozzá őket a JSON-sablonhoz a *Tulajdonságok* csomópont gyermekeiként a *MachineLearningWorkspace* csomóponttal megegyező szinten.

Például:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Tekintse meg a következő cikkek és további részleteket a mintakód:

* [Azure Machine learning Studio (klasszikus) parancsmagok](https://docs.microsoft.com/powershell/module/az.machinelearning) referenciája az MSDN-ben
* [Útmutató](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) a githubon

## <a name="consume-the-web-services"></a>A webszolgáltatások felhasználása

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Az Azure Machine Learning Web Services (tesztelés) felhasználói felület

Az Azure Machine Learning Web Services portálon a webszolgáltatás tesztelheti. Tesztelés a kérés-válasz szolgáltatás (RRS) és a kötegelt végrehajtási szolgáltatás (BES) felületek.

* [Új webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
* [Azure Machine Learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
* [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Az Excelből

Egy Excel-sablont, amely a web service tölthető le:

* [Azure Machine Learning webszolgáltatás kihasználása az Excelből](consuming-from-excel.md)
* [Azure Machine Learning webszolgáltatások Excel-bővítménye](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>A REST-alapú ügyfél

Az Azure Machine Learning webszolgáltatások olyan REST-alapú API-k. Ezeket az API-kat különböző platformokról, például a .NET, a Python, az R, a Java stb. használatával lehet felhasználni. A webszolgáltatások felhasználható lapja a [Microsoft Azure Machine learning webszolgáltatások portálján](https://services.azureml.net) olyan mintakód található, amely segítséget nyújt az első lépésekhez. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).
