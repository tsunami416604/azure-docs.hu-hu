---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179077"
---
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
 

Futtassa a [ **Connect – Az Account** ](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) parancsmagot, és megjelenik egy bejelentkezési képernyő, amely a hitelesítő adatainak megadását. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

```powershell
Connect-AzAccount
```

Ha több előfizetés használata az [ **Set-AzContext** ](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) parancsmagot, amellyel kiválaszthatja, hogy melyik előfizetéssel, a PowerShell-munkamenetet használjon. Milyen előfizetésre az aktuális PowerShell-munkamenetet használ megtekintéséhez futtassa [ **Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Összes előfizetés megtekintéséhez futtassa [ **Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

