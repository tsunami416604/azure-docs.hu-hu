---
title: Használati adatok – a Cloud Shell
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ismerje meg, hogyan végpont találatok száma használati adatokat az Azure Cloud Shellben a LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 703332ece0208856bfbedb852b4b1e985d157dc9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605952"
---
# <a name="usage-data-for-luis-service-from-azure-cloud-shell"></a>Intelligens HANGFELISMERÉSI szolgáltatás az Azure Cloud Shell a használati adatok

Ismerje meg, hogyan végpont találatok száma használati adatokat az Azure Cloud Shellben a LUIS.

Az Azure Portalon a LUIS-erőforrásokat a PowerShell-parancsmagok használatával teszi lehetővé. 

Ezek a parancsmagok lehetővé teszik [létrehozása](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) LUIS-előfizetéssel, tájékoztatást nyújt az előfizetést, beleértve a [használati](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), és [eltávolítása](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) az előfizetés. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Cloud shell-storage-fiók és hitelesítés

Annak érdekében, hogy a PowerShell használata az Azure Portalon [cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), szüksége lesz egy Azure storage-fiókot. Ha nem rendelkezik egy [tárfiók](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage), kérni fogja hozzon létre egyet. A storage-fiók lehetővé teszi, hogy mentse a PowerShell-parancsprogramok a cloud shellben.  

Is kell hitelesíteni a cloud shellben bármely erőforrás eléréséhez. 

Miután egy tárfiókot és a hitelesítés, a PowerShell-parancsmagok is futtathatja.

## <a name="open-cloud-shell"></a>Cloud Shell megnyitása

Az Azure portal cloud shell használata esetén Ön mindig a legújabb PowerShell-verziót. 

Használja a **Cloud Shell indítása** gombra, és a Cloud shellt, vagy nyisson meg egy böngészőt a [ https://shell.azure.com ](https://shell.azure.com). Válassza ki a Power Shell környezet. Ha nem rendelkezik Azure Storage-fiókot, szüksége lesz a hozzon létre egyet. 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" alt="Start powershell" /></a>

## <a name="luis-endpoint-usage-information"></a>A LUIS végpont használati adatai

A PowerShell-6.x parancsmag `Get-AzureRmCognitiveServicesAccountUsage`, beleértve a LUIS Microsoft Cognitive Services használati információkat nyújt. [A GET-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) az erőforráscsoportot és az erőforrás neve a szolgáltatás szükséges. 

A parancs szintaxisa:

```powershell
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

A következő példában az erőforráscsoport neve van `luis-westus-rg` és az intelligens HANGFELISMERÉSI szolgáltatás előfizetés neve `luis-westus-1`. Mindkét ezeket a neveket közül választ, az intelligens HANGFELISMERÉSI szolgáltatás létrehozásakor. 

A parancsmag 10 000 a június 7 a befejezési idővel rendelkező egy 30 napos időszakban használt végpont a találatok 16 használati adatokat adja vissza:

```powershell
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