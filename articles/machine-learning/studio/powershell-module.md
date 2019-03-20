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
ms.date: 01/25/2019
ms.openlocfilehash: bd3a82f326cdf7f51e8842e45333ff2bd647c260
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092752"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>PowerShell-modulokat az Azure Machine Learning Studióban

Használja a PowerShell-modulok, programozott módon kezelheti a Studio erőforrásainak és az adategységeket, mint a-munkaterületek, adatkészletek és webszolgáltatások.

Studio erőforrások három Powershell-modulok használata kezelheti:

* [Az Azure PowerShell Az](#az-rm) kiadás dátuma: 2018-ban, habár a különböző parancsmagok neveivel AzureRM, funkciókat tartalmazza
* [AzureRM](#az-rm) kiadás dátuma: 2016-ban
* [Az Azure Machine Learning PowerShell – klasszikus](#classic) kiadás dátuma: 2016-ban

Bár ezek a modulok elérésük szempontjából, adott helyzetekben egyes lett tervezve. Ez a cikk a PowerShell-modulokat, és eldöntheti, melyiket válassza segít közötti különbségeket ismerteti.

## <a name="choosing-modules"></a> Modulok kiválasztása

Kezelt erőforrások típusától függ, hogy az elérhető PowerShell-modulok közötti választáshoz.

Ellenőrizze a [támogatási tábla](#support-table) állapotellenőrzésére melyik erőforrást egyes modul által támogatott. Klasszikus PowerShell telepíthető Az vagy AzureRM párhuzamosan, mivel két modult telepítheti és terjed ki az összes erőforrástípus (Az a klasszikus vagy az AzureRM klasszikus)

Azonban nem ajánlott Az és a egy időben telepített AzureRM. Annak eldöntéséhez, Az és AzureRM között, a Microsoft javasolja Az összes későbbi központi telepítésekhez. Használja az AzureRm, ha nincsenek különleges körülmények között a környezetben, amelyek miatt.

Az AzureRM, valamint a megadott áttelepítési út közti különbségekkel kapcsolatos további tudnivalókért tekintse meg a [az Azure PowerShell Az. bemutatása](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> Az Azure PowerShell Az és AzureRM

Az és AzureRM mindkettő használatával üzembe helyezett megoldások kezelése az **Azure Resource Manager** üzemi modellt. Ilyen erőforrások többek között a Studio-munkaterületek és Studio új webszolgáltatások. A klasszikus üzemi modellel üzembe helyezett erőforrások kezeléséhez, a klasszikus PowerShell-modult kell használnia. Ha szeretne további információ az üzembe helyezési modellel, tekintse meg a [Azure Resource Manager és klasszikus üzembe helyezési](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) cikk.

Az ezzel a megfelelő PowerShell-modult az Azure-ral való interakcióhoz és AzureRM az előző funkciókat tartalmazza. AzureRM továbbra is fogadni hibajavításokat tartalmaz, de nincs új parancsmagok vagy a szolgáltatások fog fogadni. Amíg frissítési útvonal a AzureRM, ha problémája van Az Studio használatakor, jelentse a problémát, és visszatér AzureRM használatával.

Az használatának megkezdéséhez kövesse a [telepítési utasításokat Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> Klasszikus PowerShell

A Studio [klasszikus PowerShell-modul](https://aka.ms/amlps) lehetővé teszi, hogy használatával üzembe helyezett erőforrások kezelése a **klasszikus üzemi modell**. Ilyen erőforrások többek között a Studio felhasználói eszközök, a klasszikus webszolgáltatások és a klasszikus webszolgáltatás-végpontok.

Azonban a Microsoft azt javasolja, hogy az összes új erőforrások Resource Manager üzemi modell használatával egyszerűsíthető a központi telepítési és az erőforrások kezelését. Ha szeretne további információ az üzembe helyezési modellel, tekintse meg a [Azure Resource Manager és klasszikus üzembe helyezési](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) cikk.

Klasszikus PowerShell használatának megkezdéséhez töltse le a [kiadási csomag](https://github.com/hning86/azuremlps/releases) a Githubról, majd kövesse a [telepítési utasításokat](https://github.com/hning86/azuremlps/blob/master/README.md). Az útmutató azt ismerteti, hogy feloldása a letöltött/kicsomagolt DLL zárolását, és importálja azt a PowerShell-környezet.

## <a name="support-table"></a> PowerShell-támogatás tábla

 **Studio-munkaterületek** | **Az** |  **AzureRM** | **Klasszikus PowerShell** |
| --- | --- | --- | --- |
| Munkaterületek létrehozása/törlése | [Resource Manager-sablonok](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Resource Manager-sablonok](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Munkaterület-felhasználók kezelése |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Kötelezettségvállalásos csomagok kezelése | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Webszolgáltatások** | **Az** | **AzureRM** | **Klasszikus PowerShell** |
| Webszolgáltatások kezelése | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Új webszolgáltatások esetében) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Új webszolgáltatások esetében) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (klasszikus webszolgáltatások esetében) |
| Végpontok és kulcsok kezelése |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Új webszolgáltatások esetében) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Új webszolgáltatások esetében) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (klasszikus webszolgáltatások esetében) |
|||
| **Felhasználói eszközök** | **Az** | **AzureRM** | **Klasszikus PowerShell** |
| Adatkészletet és betanított modellek kezelése |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Kísérletek kezelése |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Egyéni modulok kezelése |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>További lépések
Kövesse az alábbi hivatkozások a PowerShell-modulok szóló teljes dokumentációt:
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [Klasszikus PowerShell](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
