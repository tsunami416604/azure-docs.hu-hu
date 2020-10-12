---
title: Cloud Service-tároló létrehozása a PowerShell használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozható létre felhőalapú szolgáltatás-tároló a PowerShell-lel. A tároló webes és feldolgozói szerepköröket üzemeltet.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: d40a5b64cc8018f45bf08158ce808b2baae27962
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87049082"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Üres felhőalapú szolgáltatás tárolójának létrehozása Azure PowerShell parancs használatával

Ez a cikk azt ismerteti, hogyan hozhat létre gyorsan Cloud Services-tárolót Azure PowerShell-parancsmagok használatával. Kövesse az alábbi lépéseket:

1. Telepítse a Microsoft Azure PowerShell parancsmagot a [Azure PowerShell letöltések](https://aka.ms/webpi-azps) lapról.
2. Nyissa meg a PowerShell-parancssort.
3. Jelentkezzen be az [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-4.0.0) használatával.

   > [!NOTE]
   > Az Azure PowerShell parancsmag telepítésével és az Azure-előfizetéshez való csatlakozással kapcsolatos további útmutatásért tekintse meg a [Azure PowerShell telepítése és konfigurálása című témakört](/powershell/azure/).
   >
   >
4. A **New-AzureService** parancsmag használatával hozzon létre egy üres Azure Cloud Service-tárolót.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Kövesse ezt a példát a parancsmag meghívásához:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Az Azure Cloud Service létrehozásával kapcsolatos további információkért futtassa a következőt:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>További lépések

* A felhőalapú szolgáltatás központi telepítésének kezeléséhez tekintse meg a [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService?view=azuresmps-4.0.0), a [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService?view=azuresmps-4.0.0)és a [set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice?view=azuresmps-4.0.0) parancsokat. További információért tekintse meg a [Cloud Services konfigurálását](cloud-services-how-to-configure-portal.md) is.
* Ha közzé szeretné tenni a Cloud Service-projektet az Azure-ban, tekintse meg az [archivált Cloud Services-tárház](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery) **PublishCloudService.ps1** kód mintáját.