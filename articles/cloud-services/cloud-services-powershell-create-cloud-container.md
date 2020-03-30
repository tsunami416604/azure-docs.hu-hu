---
title: Felhőszolgáltatás-tároló létrehozása a PowerShell használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan hozhat létre egy felhőalapú szolgáltatás tárolót a PowerShell használatával. A tároló web- és feldolgozói szerepköröket tárol.
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
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359502"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Üres felhőszolgáltatás-tároló létrehozása Azure PowerShell-parancs használatával

Ez a cikk bemutatja, hogyan hozhat létre gyorsan egy Cloud Services-tárolót az Azure PowerShell-parancsmagok használatával. Kövesse az alábbi lépéseket:

1. Telepítse a Microsoft Azure PowerShell-parancsmast az [Azure PowerShell letöltési](https://aka.ms/webpi-azps) lapjáról.
2. Nyissa meg a PowerShell parancssorát.
3. Az [Add-AzureAccount használatával](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) jelentkezzen be.

   > [!NOTE]
   > Az Azure PowerShell-parancsmag telepítésével és az Azure-előfizetéshez való csatlakozással kapcsolatos további útmutatásért tekintse meg [az Azure PowerShell telepítése és konfigurálása című témakört.](/powershell/azure/overview)
   >
   >
4. A **New-AzureService** parancsmag használatával hozzon létre egy üres Azure-felhőszolgáltatás-tárolót.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. A parancsmag meghívásához kövesse ezt a példát:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Az Azure felhőszolgáltatás létrehozásáról további információt a következő futásra vonatkozóan talál:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>További lépések

* A felhőalapú szolgáltatás üzembe helyezésének kezeléséhez tekintse meg a [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)és [Set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) parancsokat. További információkért olvassa el [a Felhőszolgáltatások konfigurálása](cloud-services-how-to-configure-portal.md) című útmutatót is.
* A felhőszolgáltatási projekt Azure-ban való közzétételéhez tekintse meg a **PublishCloudService.ps1** kódmintát az [archivált felhőszolgáltatások tárházából.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)