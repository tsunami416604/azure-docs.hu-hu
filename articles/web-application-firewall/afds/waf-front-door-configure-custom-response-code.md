---
title: Egyéni válasz konfigurálása a WAF-hez az Azure Bejárati ajtóval
description: Megtudhatja, hogy miként konfigurálhat egyéni válaszkódot és üzenetet, ha a Web Application Firewall (WAF) letiltja a kérelmeket.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185348"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Egyéni válasz konfigurálása az Azure webalkalmazás-tűzfalhoz

Alapértelmezés szerint, ha az Azure Web Application Firewall (WAF) az Azure Bejárati ajtó blokkolja a kérelmet, mert egyező szabály, akkor egy 403-as állapotkódot **a kérelem blokkolva van.** Ez a cikk azt ismerteti, hogyan konfigurálható egy egyéni válaszállapot-kód és válaszüzenet, ha a WAF blokkolja a kérést.

## <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat a lapon, az Azure-hitelesítő adatokkal való bejelentkezéshez, és telepítse az Az PowerShell-modult.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Csatlakozás az Azure-hoz egy interaktív párbeszédpanellel a bejelentkezéshez
```
Connect-AzAccount
Install-Module -Name Az
```
Győződjön meg arról, hogy a PowerShellJelenlegi verziója telepítve van. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Az Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt meg egy erőforráscsoporthoz. Ebben a példában a [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)használatával hoz létre erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Új WAF-házirend létrehozása egyéni válaszokkal 

Az alábbi példa egy új WAF-házirend et hozhat létre, 405-re beállított egyéni válaszállapot-kóddal, és **a You are blocked üzenettel.** [a New-AzFrontDoorWafPolicy használatával.](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

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

Módosítsa egy meglévő WAF-házirend egyéni válaszkódjának vagy választörzs-beállításainak módosítását [az Update-AzFrontDoorFireWallPolicy használatával.](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)

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
- További információ az [Azure Bejárati ajtóval rendelkező webalkalmazás-tűzfalról](../afds/afds-overview.md)