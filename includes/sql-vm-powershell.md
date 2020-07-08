---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179077"
---
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
 

Futtassa a [**Kapcsolódás az Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) parancsmagot, és megjelenik egy bejelentkezési képernyő a hitelesítő adatok megadásához. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, válassza ki a PowerShell-munkamenet által használandó előfizetést a [**set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) parancsmag használatával. Ha szeretné megtekinteni, hogy az aktuális PowerShell-munkamenet milyen előfizetést használ, futtassa a [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext)parancsot. Az összes előfizetés megtekintéséhez futtassa a [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)parancsot.

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

