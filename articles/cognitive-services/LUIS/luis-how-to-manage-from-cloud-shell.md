---
title: A LUIS tekintse meg az Azure Cloud Shell |} A Microsoft Docs
description: Ismerje meg, hogyan tehet szert a használati adatokat az Azure Cloud Shellben a LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: diberry
ms.openlocfilehash: f9d3a489328263bd300d60935bfde2f0dfc0245c
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224041"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Intelligens HANGFELISMERÉSI szolgáltatás kezelése az Azure Cloud Shell
Az Azure Portalon a LUIS-erőforrásokat a PowerShell-parancsmagok használatával teszi lehetővé. 

Ezek a parancsmagok lehetővé teszik [létrehozása](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) LUIS-előfizetéssel, tájékoztatást nyújt az előfizetést, beleértve a [használati](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), és [eltávolítása](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) az előfizetés. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Cloud shell-storage-fiók és hitelesítés
Annak érdekében, hogy a PowerShell használata az Azure Portalon [cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), szüksége lesz egy Azure storage-fiókot. Ha nem rendelkezik egy [tárfiók](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), kérni fogja hozzon létre egyet. A storage-fiók lehetővé teszi, hogy mentse a PowerShell-parancsprogramok a cloud shellben.  

Is kell hitelesíteni a cloud shellben bármely erőforrás eléréséhez. 

Miután egy tárfiókot és a hitelesítés, a PowerShell-parancsmagok is futtathatja.

## <a name="open-cloud-shell"></a>Cloud Shell megnyitása
Az Azure portal cloud shell használata esetén Ön mindig a legújabb PowerShell-verziót. 

Használja a **Cloud Shell indítása** gombra, és a Cloud shellt, vagy nyisson meg egy böngészőt a [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>A LUIS végpont használati adatai

A PowerShell-6.x parancsmag `Get-AzureRmCognitiveServicesAccountUsage`, beleértve a LUIS Microsoft Cognitive Services használati információkat nyújt. [A GET-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) az erőforráscsoportot és az erőforrás neve a szolgáltatás szükséges. 

A parancs szintaxisa:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

A következő példában az erőforráscsoport neve van `luis-westus-rg` és az intelligens HANGFELISMERÉSI szolgáltatás előfizetés neve `luis-westus-1`. Mindkét ezeket a neveket közül választ, az intelligens HANGFELISMERÉSI szolgáltatás létrehozásakor. 

A parancsmag 10 000 a június 7 a befejezési idővel rendelkező egy 30 napos időszakban használt végpont a találatok 16 használati adatokat adja vissza:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Szeretné menteni a parancsot egy PowerShell fájlt *.ps1, a cloud shellben társított Azure storage-fiókban, és hajtsa végre tetszőleges időpontban. 

![Parancsfájl futtatása a storage-ból](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Miután a parancsfájl a felhő meghajtón mentette, futtathatja a PowerShell-szkriptet az Azure telefonos alkalmazás a cloud shell. 

![Futtassa a szkriptet a telefonos alkalmazás storage-ból](./media/luis-how-to-manage-from-powershell/phone-app.png)