---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926220"
---
Telepítse az Azure Service Management (SM) PowerShell-modulok és az ExpressRoute-modul legújabb verzióit. Az Azure CloudShell-környezet nem használható SM-modulok futtatásához.

1. Az Azure Service Management module telepítéséhez a [Szolgáltatáskezelés modul telepítése](/powershell/azure/servicemanagement/install-azure-ps) című cikkben található utasításokat. Ha az Az vagy RM modul már telepítve van, mindenképpen használja az "-AllowClobber" modult.
2. Importálja a telepített modulokat. A következő példában állítsa be az elérési utat, hogy tükrözze a telepített PowerShell-modulok helyét és verzióját.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Az Azure-fiókba való bejelentkezéshez nyissa meg emelt szintű jogosultságokkal rendelkező PowerShell-konzolját, és csatlakozzon a fiókjához. Az alábbi példával a Service Management modul használatával csatlakozhat:

   ```powershell
   Add-AzureAccount
   ```