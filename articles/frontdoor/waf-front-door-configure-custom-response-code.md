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
ms.date: 05/21/2019
ms.author: tyao;kumud
ms.openlocfilehash: 146d17fe457751fb950f723c34826e43516e4e86
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165369"
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

Az alábbi példában az egyéni válasz állapotkódja 405-ös és az üzenethez, és állítsa be az új WAF-házirend létrehozása, **le vannak tiltva.** használatával [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Egyéni válaszkód vagy válasz törzsében beállításokat egy meglévő WAF-szabályzatok használatával [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [bejárati ajtajának](front-door-overview.md)