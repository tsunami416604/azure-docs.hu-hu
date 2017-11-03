---
title: "A PowerShell-lel egy új Azure Machine Learning webszolgáltatás újratanítása |} Microsoft Docs"
description: "Megtudhatja, hogyan programozott módon modell működik, és frissíti a webszolgáltatás az újonnan betanított modell használatára az Azure Machine Learning a Machine Learning Management PowerShell-parancsmagok használatával."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 77bc78e7ed27f1566e5e5f6a3539c93c9aa73e2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>A Machine Learning Management PowerShell-parancsmagok használatával egy új erőforrás-kezelő alapú webszolgáltatás újratanítása
Ha újratanítása egy új webszolgáltatás-bővítmény, frissítenie kell hivatkoznia, az új betanított modell a prediktív webszolgáltatás-definíciójának.  

## <a name="prerequisites"></a>Előfeltételek
Be kell állítania egy tanítási kísérletet, és egy prediktív kísérletté látható módon [Machine Learning-modellek szoftveres](retrain-models-programmatically.md). 

> [!IMPORTANT]
> A prediktív kísérletté tanulás webszolgáltatás Azure Resource Manager (új) alapú gépként kell telepíteni. Egy új webszolgáltatás-bővítmény telepítése, megfelelő engedélyekkel kell rendelkeznie, amelyhez az előfizetést, a webszolgáltatás telepítése. További információkért lásd: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

A web Services további információkért lásd: [központi telepítése az Azure Machine Learning webszolgáltatás](publish-a-machine-learning-web-service.md).

Ehhez a folyamathoz szükséges, hogy telepítette az Azure Machine Learning parancsmagok. A Machine Learning-parancsmagok telepítése információkért lásd: a [Azure Machine Learning parancsmagok](https://msdn.microsoft.com/library/azure/mt767952.aspx) hivatkozást az MSDN Webhelyén.

A következő adatokat másolni a megőrzési kimenete:

* BaseLocation
* RelativeLocation

Lépései a következők:

1. Jelentkezzen be Azure Resource Manager-fiókját.
2. A webszolgáltatás-definíciójának beolvasása
3. A webszolgáltatás-definíciójának JSON exportálása
4. Frissítse a JSON-ban a ilearner blobra mutató hivatkozás.
5. A JSON importálnia kell a webszolgáltatás-definíciójának
6. A webszolgáltatás új webszolgáltatás-definíciójának frissítése

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Jelentkezzen be az Azure Resource Manager-fiókba
Ön először be kell jelentkeznie Azure-fiókjába a belül a PowerShell környezet használatával a [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) parancsmag.

## <a name="get-the-web-service-definition"></a>A webszolgáltatás-definíciójának beolvasása
A következő beolvasni a webszolgáltatás meghívásával a [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) parancsmag. A webszolgáltatás-definíciójának a betanított modell webszolgáltatás belső másolatát, és nincs közvetlen módosítható. Győződjön meg arról, hogy vannak-e a webszolgáltatás-definíciójának beolvasása a prediktív kísérletté és nem a tanítási kísérletet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Határozza meg az erőforráscsoport neve egy meglévő webszolgáltatás, futtassa a Get-AzureRmMlWebService parancsmagot a webes szolgáltatások megjelennek az előfizetés paraméter nélkül. Keresse meg a webes szolgáltatás, és tekintse meg a webes szolgáltatás azonosítóját. Az erőforráscsoport neve az azonosító, a negyedik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport neve alapértelmezett-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Azt is megteheti területen megállapíthatja, hogy az erőforráscsoport neve egy meglévő webszolgáltatás jelentkezzen be a Microsoft Azure Machine Learning webszolgáltatások portálra. Válassza ki a webszolgáltatás. Az erőforráscsoport neve a webszolgáltatás URL-CÍMÉT a ötödik eleme után a *resourceGroups* elemet. A következő példában az erőforráscsoport neve alapértelmezett-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>A webszolgáltatás-definíciójának JSON exportálása
Módosítani a definíciót a betanított modell használatára az újonnan betanított modell, előbb használnia kell a [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) parancsmag használatával exportálja a JSON formátumú fájlba.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Frissítse a JSON-ban a ilearner blobra mutató hivatkozás.
Az eszközök, keresse meg a [betanított modell], frissítse a *uri* értéket a *locationInfo* ilearner BLOB URI-azonosítójú csomópont. Az URI egyesítésével létrejön a *BaseLocation* és a *RelativeLocation* a BES átképezési hívás a kimenetből. Ekkor frissül az elérési út az új betanított modell.

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

## <a name="import-the-json-into-a-web-service-definition"></a>A JSON importálnia kell a webszolgáltatás-definíciójának
Kell használnia a [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) parancsmagot, hogy a módosított JSON-fájl átalakítása vissza egy webszolgáltatás-definíciójának frissítése a webszolgáltatás-definíciójának használható.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>A webszolgáltatás új webszolgáltatás-definíciójának frissítése
Végezetül használhatja [frissítés-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) parancsmagot, hogy a webszolgáltatás-definíciójának frissítése.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Összefoglalás
A Machine Learning PowerShell parancsmagokat használva frissítheti a betanított modell egy prediktív webszolgáltatás forgatókönyvek például engedélyezése:

* Az új adatokat átképezési rendszeres modell.
* Terjesztési ügyfelek-modell, azzal a céllal, hogy működik a modell használatával saját adataikat.

