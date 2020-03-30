---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179077"
---
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
 

Futtassa a [**Connect-Az fiók**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) parancsmamot, és megjelenik egy bejelentkezési képernyő a hitelesítő adatok megadásához. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, használja a [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) parancsmag segítségével válassza ki, hogy melyik előfizetést kell használnia a PowerShell-munkamenet. Ha meg szeretné tekinteni, hogy az aktuális PowerShell-munkamenet milyen előfizetést használ, futtassa a [**Get-AzContext billentyűt.**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext) Az összes előfizetés megtekintéséhez futtassa a [**Get-AzSubscription rendszert.**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

