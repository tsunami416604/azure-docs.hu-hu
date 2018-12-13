---
title: A PowerShell használatával – az Azure új Machine Learning Studio webszolgáltatás újratanítása |} A Microsoft Docs
description: Ismerje meg, hogyan programozott módon modellek szoftveres átképezése és frissíteni a webszolgáltatást a újonnan betanított modell használata az Azure Machine Learning, a Machine Learning Management PowerShell-parancsmagok használatával.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 0dc41b001ecca26239c0a9e8f69e2709d6927fcd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250576"
---
# <a name="retrain-a-new-resource-manager-based-studio-web-service-using-powershell"></a>A Powershell használatával új Studio Resource Manager-alapú webszolgáltatás újratanítása
Ha egy új webszolgáltatás újratanítása, a prediktív webszolgáltatás-definíciójának való hivatkozáshoz az új betanított modell frissítenie.

## <a name="prerequisites"></a>Előfeltételek
Be kell állítania egy tanítási kísérletet, és a egy prediktív kísérletet, ahogyan [Retrain Machine Learning-modellek](retrain-models-programmatically.md).

> [!IMPORTANT]
> A prediktív kísérletet, egy Azure Resource Manager-alapú (új) machine learning webszolgáltatás kell telepíteni.
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információkért lásd: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md).

Webszolgáltatások üzembe helyezése a további információkért lásd: [egy Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md).

Ehhez a folyamathoz szükséges, hogy telepítette az Azure Machine Learning-parancsmagok. A Machine Learning-parancsmagok telepítéséről információért lásd: a [Azure Machine Learning-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/) hivatkozást az MSDN Webhelyén.

Az alábbi adatokat másolja a megőrzési kimenetből:

* BaseLocation
* RelativeLocation

A lépéseket, a következők:

1. Jelentkezzen be az Azure Resource Manager-fiókot.
2. A webszolgáltatás-definíciójának beolvasása
3. A webszolgáltatás-definíciójának exportálása JSON-fájlként
4. Frissítse a ilearner blob, a JSON-mutató hivatkozást.
5. A JSON importálhat egy webszolgáltatás-definíciójának
6. A web service frissítése az új webszolgáltatás-definíciójának

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Jelentkezzen be az Azure Resource Manager-fiók
Először jelentkezzen be Azure-fiókjába, a PowerShell környezetben használatával belül a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot.

## <a name="get-the-web-service-definition"></a>A webszolgáltatás-definíciójának beolvasása
Ezután kérdezze le a Web Service meghívásával a [Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) parancsmagot. A webszolgáltatás-definíciójának egy belső ábrázolása a webszolgáltatás a betanított modell, ezért nem módosítható közvetlenül. Győződjön meg arról, hogy vannak-e a webszolgáltatás-definíciójának beolvasása a prediktív kísérletet, és nem a betanítási kísérlet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Meglévő webszolgáltatás, az erőforráscsoport nevének megállapításához futtassa a Get-AzureRmMlWebService parancsmag a webes szolgáltatások megjelenítéséhez az előfizetésében paraméterek nélkül. Keresse meg a webszolgáltatás, és tekintse meg a webszolgáltatás-azonosítót. Az erőforráscsoport neve nem az azonosító, a negyedik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport nevének alapértelmezett-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Azt is megteheti annak megállapításához, a meglévő webszolgáltatás az erőforráscsoport neve, jelentkezzen be a Microsoft Azure Machine Learning Web Services portálon. Válassza ki a web service. Az erőforráscsoport nevét a web service URL-CÍMÉT a minden ötödik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport nevének alapértelmezett-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>A webszolgáltatás-definíciójának exportálása JSON-fájlként
Módosíthatja a definíció, a betanított modell használata az újonnan betanított modell kell először használja a [Export-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) JSON formátumú fájlba exportálhatja, a parancsmag.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Frissítse a ilearner blob, a JSON-mutató hivatkozást.
Az eszközök, keresse meg a [betanított modell], frissítse a *uri* értékét a *locationInfo* ilearner BLOB URI-azonosítójú csomópont. Az URI egyesítésével jön létre a *BaseLocation* és a *RelativeLocation* a hívás átképezési BES kimenetéből származó. Ez frissíti az elérési út az új betanított modell.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>A JSON importálhat egy webszolgáltatás-definíciójának
Kell használnia a [Import-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) parancsmag a módosított JSON-fájlt vissza alakítható át egy webszolgáltatás-definíciójának, amelyek segítségével a webszolgáltatás-definíciójának frissítése.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>A web service frissítése az új webszolgáltatás-definíciójának
Végül a [Update-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) parancsmagot, hogy a webszolgáltatás-definíciójának frissítése.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Összegzés
A Machine Learning PowerShell-kezelési parancsmagok használatával, frissítheti a betanított modell egy prediktív webszolgáltatás például forgatókönyvek engedélyezése:

* Rendszeres modell új adatokkal átképezési.
* A modell az ügyfelek számára a cél az, amely tájékoztatja őket a modell használatával saját adataik újratanítás terjesztési.

