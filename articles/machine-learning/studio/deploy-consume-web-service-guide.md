---
title: Telepítés és felhasználás
titleSuffix: ML Studio (classic) - Azure
description: Az Azure Machine Learning Studio (klasszikus) segítségével gépi tanulási munkafolyamatokat és modelleket helyezhet üzembe webszolgáltatásként. Ezek a webszolgáltatások ezután használható a gépi tanulási modellek hívása alkalmazások az interneten keresztül, hogy valós időben vagy kötegelt módban előrejelzéseket.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204392"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások: Üzembe helyezés és felhasználás

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning Studio (klasszikus) segítségével gépi tanulási munkafolyamatokat és modelleket helyezhet üzembe webszolgáltatásként. Ezek a webszolgáltatások ezután az interneten keresztül iszákból származó gépi tanulási modellek hívására használhatók, hogy valós időben vagy kötegelt módban végezhessenek előrejelzéseket. Mivel a webszolgáltatások RESTful, hívhatja őket a különböző programozási nyelvek és platformok, mint például a .NET és a Java, és az alkalmazások, például az Excel.

A következő szakaszok a kezdéshez forgatókönyvekre, kódokra és dokumentációra mutató hivatkozásokat tartalmaznak.

## <a name="deploy-a-web-service"></a>Webszolgáltatás üzembe helyezése

### <a name="with-azure-machine-learning-studio-classic"></a>Az Azure Machine Learning Studio (klasszikus)

A Studio (klasszikus) portál és a Microsoft Azure Machine Learning webservices portál segítségével kódot írás nélkül telepíthet és kezelhet egy webszolgáltatást.

Az alábbi hivatkozások általános tájékoztatást nyújtanak az új webszolgáltatások üzembe helyezéséről:

* Az Azure Resource Manager en alapuló új webszolgáltatás üzembe helyezéséről az [Új webszolgáltatás telepítése című](deploy-a-machine-learning-web-service.md)témakörben olvashat.
* A webszolgáltatás üzembe helyezéséről szóló útmutatót az [Azure Machine Learning webszolgáltatás telepítése ( Központi telepítés) témakörben](deploy-a-machine-learning-web-service.md)talál.
* A webszolgáltatás létrehozásáról és üzembe helyezéséről szóló teljes forgatókönyvhöz kezdje az [1.](tutorial-part1-credit-risk.md)
* A webszolgáltatást üzembe helyező konkrét példákat a következő témakörben talál:

  * [3. oktatóanyag: Hitelkockázati modell telepítése](tutorial-part3-credit-risk-deploy.md)
  * [Webszolgáltatás telepítése több régióban](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Webszolgáltatások erőforrás-szolgáltatóAPI-ival (Azure Resource Manager API-kkal)

Az Azure Machine Learning Studio (klasszikus) erőforrás-szolgáltató a webszolgáltatások lehetővé teszi a webszolgáltatások üzembe helyezését és felügyeletét REST API-hívások használatával. További információt a [Machine Learning webszolgáltatás (REST)](/rest/api/machinelearning/index) hivatkozási.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>PowerShell-parancsmagokkal

Az Azure Machine Learning Studio (klasszikus) erőforrás-szolgáltató a webszolgáltatások lehetővé teszi a webszolgáltatások üzembe helyezését és felügyeletét a PowerShell-parancsmagok használatával.

A parancsmagok használatához először be kell jelentkeznie az Azure-fiókjába a PowerShell-környezetben a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Ha nem ismeri az Erőforrás-kezelőn alapuló PowerShell-parancsok hívásának módját, olvassa el [az Azure PowerShell használata az Azure Resource Managerrel című](../../azure-resource-manager/management/manage-resources-powershell.md)témakört.

A prediktív kísérlet exportálásához használja [ezt a mintakódot.](https://github.com/ritwik20/AzureML-WebServices) Miután létrehozta az .exe fájlt a kódból, a következőt írhatja be:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Az alkalmazás futtatása létrehoz egy webszolgáltatás JSON sablont. Ahhoz, hogy a sablont webszolgáltatás üzembe helyezéséhez használja, a következő információkat kell megadnia:

* Tárfiók neve és kulcsa

    A tárfiók nevét és kulcsát az [Azure Portalon](https://portal.azure.com/)szerezheti be.
* Kötelezettségvállalási terv azonosítója

    A csomagazonosítót az [Azure Machine Learning Web Services](https://services.azureml.net) portálról szerezheti be, ha bejelentkezik, és egy csomagnevére kattint.

Adja hozzá őket a JSON-sablonhoz a *Tulajdonságok* csomópont gyermekeiként, ugyanazon a szinten, mint a *MachineLearningWorkspace* csomópont.

Például:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

További részleteket az alábbi cikkekben és mintakódban talál:

* [Az Azure Machine Learning Studio (klasszikus) parancsmagok](https://docs.microsoft.com/powershell/module/az.machinelearning) hivatkozás az MSDN-en
* [Példaforgatókönyv](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) a GitHubon

## <a name="consume-the-web-services"></a>Használja a webes szolgáltatásokat

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Az Azure Machine Learning webszolgáltatások felhasználói felületéről (tesztelés)

A webszolgáltatás az Azure Machine Learning Web Services portálon tesztelheti. Ez magában foglalja a kérelem-válasz szolgáltatás (RRS) és a batch-végrehajtási szolgáltatás (BES) felületének tesztelését.

* [Új webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
* [Azure Machine Learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
* [3. oktatóanyag: Hitelkockázati modell telepítése](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Excelből

A webszolgáltatást használó Excel-sablont letöltheti:

* [Azure Machine Learning webszolgáltatás használata az Excelből](consuming-from-excel.md)
* [Excel-bővítmény az Azure Machine Learning webszolgáltatásokhoz](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>REST-alapú ügyféltől

Az Azure Machine Learning webszolgáltatások RESTful API-k. Ezeket az API-kat különböző platformokról, például .NET, Python, R, Java stb. A [Microsoft Azure Machine Learning Web Services portálwebszolgáltatásának](https://services.azureml.net) **felhasználási** lapja mintakódot kínál, amely segítséget nyújt az induláshoz. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).
