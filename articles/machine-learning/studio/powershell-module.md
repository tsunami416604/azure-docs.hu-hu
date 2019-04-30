---
title: A Machine Learning Studio PowerShell-modulok
titleSuffix: Azure Machine Learning Studio
description: Létrehozása és kezelése az Azure Machine Learning Studio-munkaterületek, kísérletek, webszolgáltatások és további PowerShell használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: e3c2587fceed265c9768b6ea6f2ecf3b9a8b7b1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094355"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>PowerShell-modulokat az Azure Machine Learning Studióban

Használja a PowerShell-modulok, programozott módon kezelheti a Studio erőforrásainak és az adategységeket, mint a-munkaterületek, adatkészletek és webszolgáltatások.

Studio erőforrások három Powershell-modulok használata kezelheti:

* [Az Azure PowerShell Az](#az-rm) kiadás dátuma: 2018-ban, habár a különböző parancsmagok neveivel AzureRM, funkciókat tartalmazza
* [AzureRM](#az-rm) kiadás dátuma: 2016 PowerShell Az cserélve
* [Az Azure Machine Learning PowerShell – klasszikus](#classic) kiadás dátuma: 2016-ban

Bár ezek a PowerShell-modulok elérésük szempontjából, adott helyzetekben egyes lett tervezve. Ez a cikk a PowerShell-modulokat, és eldöntheti, melyiket válassza segít közötti különbségeket ismerteti.  

Ellenőrizze a [támogatási tábla](#support-table) állapotellenőrzésére melyik erőforrást egyes modul által támogatott. 

## <a name="az-rm"></a> Az Azure PowerShell Az és AzureRM

Az ezzel a megfelelő PowerShell-modult az Azure-ral való interakcióhoz és AzureRM az előző funkciókat tartalmazza. AzureRM továbbra is fogadni hibajavításokat tartalmaz, de nincs új parancsmagok vagy a szolgáltatások fog fogadni.  Az és AzureRM mindkettő használatával üzembe helyezett megoldások kezelése az **Azure Resource Manager** üzemi modellt. Ilyen erőforrások többek között a Studio-munkaterületek és a Studio-webszolgáltatások "Új". 

Klasszikus PowerShell mellett Az vagy a AzureRM mindkét "új" és "klasszikus" erőforrástípusok fedezésére is telepíthető. Azonban nem ajánlott Az és a egy időben telepített AzureRM. Annak eldöntéséhez, Az és AzureRM között, a Microsoft javasolja Az összes későbbi központi telepítésekhez.  További információ Az AzureRM- és az áttelepítési elérési utat és [bemutatása az Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Az használatának megkezdéséhez kövesse a [telepítési utasításokat Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> Klasszikus PowerShell

A Studio [klasszikus PowerShell-modul](https://aka.ms/amlps) lehetővé teszi, hogy használatával üzembe helyezett erőforrások kezelése a **klasszikus üzemi modell**. Ilyen erőforrások többek között a Studio felhasználói eszközök, a "klasszikus" és a "klasszikus" webszolgáltatás-végpontok.

Azonban a Microsoft azt javasolja, hogy az összes jövőbeli erőforrások Resource Manager üzemi modell használatával egyszerűsíthető a központi telepítési és az erőforrások kezelését. Ha szeretne további információ az üzembe helyezési modellel, tekintse meg a [Azure Resource Manager és klasszikus üzembe helyezési](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) cikk.

Klasszikus PowerShell használatának megkezdéséhez töltse le a [kiadási csomag](https://github.com/hning86/azuremlps/releases) a Githubról, majd kövesse a [telepítési utasításokat](https://github.com/hning86/azuremlps/blob/master/README.md). Az útmutató azt ismerteti, hogy feloldása a letöltött/kicsomagolt DLL zárolását, és importálja azt a PowerShell-környezet.

Klasszikus PowerShell mellett Az vagy a AzureRM mindkét "új" és "klasszikus" erőforrástípusok fedezésére is telepíthető.

## <a name="support-table"></a> PowerShell-támogatás tábla


| | **Az** |  **Klasszikus PowerShell** |
| --- | --- | --- |
| Munkaterületek létrehozása/törlése | [Resource Manager-sablonok](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Munkaterület kötelezettségvállalásos csomagok kezelése | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Munkaterület-felhasználók kezelése |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Webszolgáltatások kezelése | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>(a "új" web services)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasszikus" webszolgáltatások) |
| Webes szolgáltatás végpontok és kulcsok kezelése |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Felhasználói adatkészletet és betanított modellek kezelése| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Felhasználói kísérletek kezelése |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Egyéni modulok kezelése | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>További lépések
Tekintse át az alábbi PowerShell-modul teljes dokumentációját:
* [Klasszikus PowerShell](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
