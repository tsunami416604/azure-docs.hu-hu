---
title: "Egy felhőalapú szolgáltatás tárolójának létrehozása a PowerShell használatával |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan egy felhőalapú szolgáltatás tárolójának létrehozása a PowerShell használatával. A tároló webes és feldolgozói szerepköröket futtat."
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: cd703feb7bf5af765fc3a5448464499aa7b48d6a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Az Azure PowerShell-parancs segítségével hozzon létre egy üres felhőalapú szolgáltatás tárolójának
Ez a cikk azt ismerteti, hogyan hozhat létre gyorsan egy Azure PowerShell-parancsmagok használatával Felhőszolgáltatások tároló. Kérjük, kövesse az alábbi lépéseket:

1. Telepítse a Microsoft Azure PowerShell parancsmag a [Azure PowerShell letölti](http://aka.ms/webpi-azps) lap.
2. Nyissa meg a PowerShell-parancssort.
3. Használja a [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) való bejelentkezéshez.

   > [!NOTE]
   > További információk az telepítése az Azure PowerShell-parancsmag és az Azure-előfizetéssel való kapcsolódás, lásd [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Használja a **New-AzureService** parancsmaggal hozzon létre egy üres Azure felhőalapú szolgáltatás tárolójának.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Kövesse az ebben a példában a parancsmag meghívásához:

   ```
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Az Azure felhőszolgáltatást létrehozásával kapcsolatos további információkért futtassa:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Következő lépések
* A felhőalapú szolgáltatás telepítés kezeléséhez, tekintse meg a [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx), és [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) parancsok. Akkor is hivatkozhat [konfigurálása felhőszolgáltatások](cloud-services-how-to-configure-portal.md) további tájékoztatást talál.
* A felhőszolgáltatás-projekt közzététele az Azure-ba, tekintse meg a **PublishCloudService.ps1** a kódminta [folyamatos kézbesítését az Azure-ban a felhőszolgáltatás](cloud-services-dotnet-continuous-delivery.md).
