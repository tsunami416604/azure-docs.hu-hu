---
title: Azure Cloud rendszerhéjból LUIS megtekinthető |} Microsoft Docs
description: 'Útmutató: Azure Cloud rendszerhéj használati adatok lekérése LUIS.'
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: v-geberr
ms.openlocfilehash: 2de25645e5377efdd53bcc980695804d34db5ee2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348995"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Azure Cloud rendszerhéjból LUIS szolgáltatás kezelése
Az Azure-portál lehetővé teszi a LUIS erőforrásokat a PowerShell-parancsmagok segítségével. 

Ezek a parancsmagok lehetővé teszik [létrehozása](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) LUIS előfizetés, információk lekérdezése az előfizetést, beleértve a [használati](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), és [eltávolítása](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) az előfizetés. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Felhő rendszerhéj tárfiók és a hitelesítés
PowerShell használatához az Azure portálon [rendszerhéj cloud](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), egy Azure storage-fiók szükséges. Ha nem rendelkezik egy [tárfiók](https://docs.microsoft.com/en-us/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), kérni fogja a létrehozáshoz. A tárfiók lehetővé teszi a felhő rendszerhéj PowerShell-parancsfájlok menteni.  

Szükség hitelesítésre a felhő rendszerhéj hozzá erőforrásaihoz. 

Miután egy tárfiókot, és megtörténik, a PowerShell-parancsmagok is futtathatja.

## <a name="open-cloud-shell"></a>Cloud Shell megnyitása
Az Azure portál felhőalapú rendszerhéj használata esetén áll mindig a legfrissebb PowerShell-verzión. 

Használja a **indítsa el a felhő rendszerhéj** gombra kattintva nyissa meg a felhő rendszerhéjat, vagy nyisson meg egy böngészőt a [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>LUIS végpont használati adatait

A PowerShell-6.x parancsmag `Get-AzureRmCognitiveServicesAccountUsage`, többek között a LUIS Microsoft kognitív szolgáltatások használati információkat nyújt. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) igényel az erőforráscsoport nevének és a szolgáltatás erőforrás neve. 

A parancs szintaxisa:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

A következő példában az erőforráscsoport neve van `luis-westus-rg` és a LUIS szolgáltatásnév-előfizetés `luis-westus-1`. Mindkét ezeket a neveket a LUIS szolgáltatás létrehozásakor közül választ. 

A parancsmag 10 000 végpont a találatok egy 30 napos időszak az az időszak. június 7 használt 16 használati adatait adja vissza:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Mentse a parancs *.ps1, a felhő rendszerhéj társított Azure storage-fiók a PowerShell fájlt, és bármikor hajtható végre. 

![Parancsfájl futtatása a tárolóból](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Miután a parancsfájl a felhőalapú meghajtójukra menti, futtathatja a PowerShell-parancsfájl az Azure phone alkalmazás felhő rendszerhéj. 

![Futtassa a parancsfájlt a telefonalkalmazás tárolóból](./media/luis-how-to-manage-from-powershell/phone-app.png)