---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: f9a45da2703518000aa464da067c5cf71a198fd4
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984970"
---
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
 

Futtassa a [ **Connect – Az Account** ](https://docs.microsoft.com/powershell/module/az.accounts/connect-azmaccount) parancsmagot, és megjelenik egy bejelentkezési képernyő, amely a hitelesítő adatainak megadását. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

```powershell
Connect-AzAccount
```

Ha több előfizetés használata az [ **Set-AzContext** ](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) parancsmagot, amellyel kiválaszthatja, hogy melyik előfizetéssel, a PowerShell-munkamenetet használjon. Milyen előfizetésre az aktuális PowerShell-munkamenetet használ megtekintéséhez futtassa [ **Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Összes előfizetés megtekintéséhez futtassa [ **Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

