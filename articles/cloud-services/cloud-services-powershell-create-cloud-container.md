---
title: Egy felhőalapú szolgáltatás tárolójának létrehozása a PowerShell használatával |} Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan egy felhőalapú szolgáltatás tárolójának létrehozása a PowerShell használatával. A tároló webes és feldolgozói szerepköröket futtat.
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: ''
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376133"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Az Azure PowerShell-parancs segítségével hozzon létre egy üres felhőalapú szolgáltatás tárolójának
Ez a cikk azt ismerteti, hogyan hozhat létre gyorsan egy Azure PowerShell-parancsmagok használatával Felhőszolgáltatások tároló. Kérjük, kövesse az alábbi lépéseket:

1. Telepítse a Microsoft Azure PowerShell parancsmag a [Azure PowerShell letölti](http://aka.ms/webpi-azps) lap.
2. Nyissa meg a PowerShell-parancssort.
3. Használja a [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) való bejelentkezéshez.

   > [!NOTE]
   > További információk az telepítése az Azure PowerShell-parancsmag és az Azure-előfizetéssel való kapcsolódás, lásd [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Használja a **New-AzureService** parancsmaggal hozzon létre egy üres Azure felhőalapú szolgáltatás tárolójának.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Kövesse az ebben a példában a parancsmag meghívásához:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Az Azure felhőszolgáltatást létrehozásával kapcsolatos további információkért futtassa:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>További lépések
* A felhőalapú szolgáltatás telepítés kezeléséhez, tekintse meg a [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0), és [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0) parancsok. Akkor is hivatkozhat [konfigurálása felhőszolgáltatások](cloud-services-how-to-configure-portal.md) további tájékoztatást talál.
* A felhőszolgáltatás-projekt közzététele az Azure-ba, tekintse meg a **PublishCloudService.ps1** a kódminta [archivált felhőalapú szolgáltatások tárház](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
