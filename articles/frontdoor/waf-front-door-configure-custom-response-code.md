---
title: 'Egyéni válasz a webalkalmazási tűzfal konfigurálása Azure bejárati ajtajának:'
description: Ismerje meg, hogy egy egyéni válaszkód és üzenet konfigurálása, ha a webalkalmazási tűzfal (WAF) blokkol egy kérelmet.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363018"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Egyéni válasz for Azure webalkalmazási tűzfal konfigurálása

Alapértelmezés szerint ha Azure webalkalmazási tűzfal (WAF) az Azure bejárati ajtajának blokkol egy kérelem egy egyező szabály miatt, adja vissza a 403-as állapotkódot **a kérelem le van tiltva** üzenet. Ez a cikk ismerteti egy egyéni válasz állapotkódja és válaszüzenet konfigurálása, ha egy kérelmet a WAF által le van tiltva.

## <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat az oldalon, jelentkezzen be Azure hitelesítő adataival, és Az PowerShell-modul telepítéséhez.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Az Azure-bA összekapcsolása a bejelentkezéshez egy interaktív párbeszédpanel
```
Connect-AzAccount
Install-Module -Name Az
```
Ellenőrizze, hogy telepítve van a PowerShellGet aktuális verziójával rendelkezik. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoportba foglalhat. Ebben a példában, hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Hozzon létre egy új WAF házirendet egyéni válasz 

Az alábbi példában az egyéni válasz állapotkódja 405-ös és az üzenethez, és állítsa be az új WAF-házirend létrehozása, **le vannak tiltva.** using [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Egyéni válaszkód vagy válasz törzsében beállításokat egy meglévő WAF-szabályzatok használatával [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [bejárati ajtajának](front-door-overview.md)