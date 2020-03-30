---
title: PowerShell-modulok
titleSuffix: ML Studio (classic) - Azure
description: A PowerShell használatával azure Machine Learning Studio (klasszikus) munkaterületeket, kísérleteket, webszolgáltatásokat és egyebeket hozhat létre és kezelhet.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204290"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>PowerShell-modulok az Azure Machine Learning Studio-hoz (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A PowerShell-modulok használatával programozott módon kezelheti a Studio (klasszikus) erőforrásait és eszközeit, például a munkaterületeket, az adatkészleteket és a webszolgáltatásokat.

A Studio (klasszikus) erőforrásait három Powershell-modul használatával használhatja:

* A 2018-ban kiadott [Azure PowerShell Az](#az-rm) az AzureRM összes funkcióját tartalmazza, bár különböző parancsmagnevekkel
* [Az AzureRM](#az-rm) 2016-ban jelent meg, helyébe a PowerShell Az lép
* 2016-ban megjelent [klasszikus Azure Machine Learning PowerShell](#classic)

Bár ezek a PowerShell-modulok bizonyos hasonlóságokkal rendelkeznek, mindegyik adott forgatókönyvekhez készült. Ez a cikk ismerteti a PowerShell-modulok közötti különbségeket, és segít eldönteni, hogy melyiket válassza.  

Tekintse meg az alábbi [támogatási táblázatot,](#support-table) hogy mely erőforrásokat támogatja az egyes modulok. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a>Az Azure PowerShell Az és az AzureRM

Az Az mostantól a tervezett PowerShell-modul az Azure-ral való interakcióhoz, és tartalmazza az AzureRM összes korábbi funkcióját. Az AzureRM továbbra is megkapja a hibajavításokat, de nem kap új parancsmagokat vagy funkciókat.  Az Az és az AzureRM egyaránt kezeli az **Azure Resource Manager** telepítési modelljével telepített megoldásokat. Ezek az erőforrások közé tartozik a Studio (klasszikus) munkaterületek és a Studio (klasszikus) "Új" webes szolgáltatások. 

A klasszikus PowerShell az Az vagy az AzureRM mellett telepíthető az "új" és a "klasszikus" erőforrástípusok lefedéséhez. Azonban nem ajánlott az Az és az AzureRM telepítése egy időben. Az Az és az AzureRM közötti döntéshez a Microsoft az Az-t ajánlja az összes jövőbeli telepítéshez.  Tudjon meg többet az Az és az AzureRM és a migrálási útvonal [az Azure PowerShell Az bevezetőben.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

Az Az használatával kapcsolatos első lépésekhez kövesse az [Azure Az telepítési utasításait.](https://docs.microsoft.com/powershell/azure/install-az-ps)

## <a name="powershell-classic"></a><a name="classic"></a>PowerShell klasszikus

A Studio (klasszikus) [PowerShell klasszikus modul](https://aka.ms/amlps) lehetővé teszi a **klasszikus telepítési modell**használatával üzembe helyezett erőforrások kezelését. Ezek az erőforrások közé tartozik a Studio (klasszikus) felhasználói eszközök, a "klasszikus" webes szolgáltatások és a "klasszikus" webszolgáltatás-végpontok.

A Microsoft azonban azt javasolja, hogy az Erőforrás-kezelő telepítési modelljét használja az összes jövőbeli erőforráshoz az erőforrások üzembe helyezésének és felügyeletének egyszerűsítése érdekében. Ha szeretne többet megtudni a központi telepítési modellek, tekintse meg az [Azure Resource Manager vs. klasszikus üzembe helyezési cikket.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)

A klasszikus PowerShell használatával ismerkedjen meg, töltse le a [kiadási csomagot](https://github.com/hning86/azuremlps/releases) a GitHubról, és kövesse a [telepítéshez adott utasításokat.](https://github.com/hning86/azuremlps/blob/master/README.md) Az utasítások ismertetik, hogyan oldhatja fel a letöltött/kibontott DLL, majd importálja a PowerShell-környezetbe.

A klasszikus PowerShell az Az vagy az AzureRM mellett telepíthető az "új" és a "klasszikus" erőforrástípusok lefedéséhez.

## <a name="powershell-support-table"></a><a name="support-table"></a>PowerShell támogatási tábla


| | **Az** |  **PowerShell klasszikus** |
| --- | --- | --- |
| Munkaterületek létrehozása/törlése | [Resource Manager-sablonok](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Munkaterületi kötelezettségvállalási tervek kezelése | [Új-AzmlcommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Munkaterület-felhasználók kezelése |  | [Add-AmlWorkspaceFelhasználók](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Webszolgáltatások kezelése | [Új-AzmlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("új" webes szolgáltatások)|| [Új-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasszikus" webes szolgáltatások) |
| Webszolgáltatás-végpontok/kulcsok kezelése |  [Get-AzmlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Felhasználói adatkészletek/betanított modellek kezelése| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Felhasználói kísérletek kezelése |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Egyéni modulok kezelése | | [Új-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>További lépések
Tekintse meg a powershell-modul teljes dokumentációját:
* [PowerShell klasszikus](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
