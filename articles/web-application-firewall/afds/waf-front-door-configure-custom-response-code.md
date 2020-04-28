---
title: Egyéni válasz beállítása WAF az Azure bejárati ajtóval
description: Megtudhatja, hogyan konfigurálhat egyéni hibakódot és üzenetet, ha a webalkalmazási tűzfal (WAF) egy kérést blokkol.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185348"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Egyéni válasz konfigurálása az Azure webalkalmazási tűzfalhoz

Alapértelmezés szerint, ha az Azure-webalkalmazási tűzfal (WAF) és az Azure bejárati ajtó blokkolja a kérelmet egy egyeztetett szabály miatt, egy 403 állapotkódot ad vissza, amely **a kérésben blokkolt** üzenet. Ez a cikk azt ismerteti, hogyan konfigurálható egy egyéni válasz állapotkódot és válaszüzenetet, ha a WAF letiltotta a kérelmet.

## <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Kapcsolódás az Azure-hoz interaktív párbeszédablak a bejelentkezéshez
```
Connect-AzAccount
Install-Module -Name Az
```
Győződjön meg arról, hogy a PowerShellGet aktuális verziója telepítve van. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Telepítés az. FrontDoor modul 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Ebben a példában egy erőforráscsoportot hoz létre a [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)használatával.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Új WAF-házirend létrehozása egyéni választal 

Az alábbi példa egy új WAF szabályzat létrehozását mutatja be, amely az egyéni válasz állapotkód 405-ra van beállítva, és az üzenet, **amelyet Ön blokkol.** a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)használata.

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

Módosítsa egy meglévő WAF-házirend egyéni válasz kódját vagy válasz törzsének beállításait az [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)használatával.

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
- További információ a [webalkalmazási tűzfalról az Azure bejárati ajtóval](../afds/afds-overview.md)