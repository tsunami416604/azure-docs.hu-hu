---
title: Egy felhőszolgáltatás-tároló létrehozása a PowerShell használatával |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan egy felhőszolgáltatás-tároló létrehozása a PowerShell használatával. A tároló webes és feldolgozói szerepköröket futtat.
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
ms.openlocfilehash: 771f93edfee8f7b48fb7d0d2c98419f9427f6338
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793526"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Üres felhőszolgáltatás-tároló létrehozása az Azure PowerShell-parancs használatával

Ez a cikk azt ismerteti, hogyan hozhat létre gyorsan egy Cloud Services-tárolót az Azure PowerShell-parancsmagok használatával. Kövesse az alábbi lépéseket:

1. Telepítse a Microsoft Azure PowerShell-parancsmag a [letölti az Azure PowerShell](https://aka.ms/webpi-azps) lapot.
2. Nyissa meg a PowerShell-parancssort.
3. Használja a [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) való bejelentkezéshez.

   > [!NOTE]
   > Telepítése az Azure PowerShell-parancsmagot, és az Azure-előfizetéshez kapcsolódó további útmutatásért tekintse meg [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).
   >
   >
4. Használja a **New-AzureService** parancsmaggal hozzon létre egy üres Azure cloud service-tárolóban.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Kövesse az ebben a példában a parancsmag meghívásához:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Az Azure-felhőszolgáltatásban létrehozásával kapcsolatos további információkért futtassa:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>További lépések

* A felhőszolgáltatás üzembe helyezésének kezelése, tekintse meg a [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0), és [Set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) parancsokat. Akkor is hivatkozhat [konfigurálása a cloud services](cloud-services-how-to-configure-portal.md) találhat további információt.
* A felhőszolgáltatás-projekt közzététele az Azure-ba, tekintse meg a **PublishCloudService.ps1** kódmintát a [archivált cloud services-adattár](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).