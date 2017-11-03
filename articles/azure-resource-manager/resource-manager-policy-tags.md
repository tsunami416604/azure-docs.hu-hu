---
title: "Az Azure erőforrás-házirendek címkék |} Microsoft Docs"
description: "Erőforrás-házirendekre talál példákat nyújt címkéket az erőforrások kezelése"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: tomfitz
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-for-tags"></a>Címkék erőforrás-szabályzatok alkalmazása

Ez a témakör általános házirend szabályokat alkalmazhat címkék erőforrások egységes használata érdekében.

Egy címke házirend alkalmazása az egy erőforráscsoport vagy a meglévő erőforrásokkal folytatott előfizetés visszamenőleges vonatkozik a szabályzat ezeket az erőforrásokat. A szabályzatokat érvényesíthet az ezeket az erőforrásokat, indítható el, egy frissítést adunk ki a meglévő erőforrásokat. A cikk egy PowerShell-példa indítására frissítést tartalmaz.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Győződjön meg arról, összes erőforrást erőforráscsoportban kell egy címke

Általános követelmény, egy erőforráscsoportban található összes erőforrást rendelkezik-e egy adott címke és értéket. Ez a követelmény gyakran szükséges követéséhez költségek részleg által. A következő feltételeknek kell teljesülniük:

* A kötelező címke és az érték a címke nem rendelkező új és frissített erőforrások lesz hozzáfűzve.
* A kötelező címke és az érték nem lehet eltávolítani a meglévő erőforrásokat.

Az erőforráscsoporthoz két beépített házirendek alkalmazásával megfelelhet ennek a követelménynek.

| ID (Azonosító) | Leírás |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Egy szükséges kód és az alapértelmezett érték érvényes a felhasználó nincs megadva. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Kikényszeríti egy szükséges kód és az értéke. |

### <a name="powershell"></a>PowerShell

A következő PowerShell-parancsfájlt a két beépített házirend-definíciók rendel egy erőforráscsoportot. Az erőforráscsoport összes kötelező címkék hozzárendelése a parancsfájl futtatása előtt. Az erőforráscsoport egyes címke szükség a csoport az erőforrások. Az előfizetésében szereplő összes erőforráscsoport rendeléséhez nem biztosítanak a `-Name` beolvasásakor. az erőforráscsoportok paraméter.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

A szabályzatok hozzárendelését, miután egy frissítést adunk ki a hozzáadott címke házirendeknek az érvényesítését minden meglévő erőforrásokat indíthat el. A következő parancsfájl őrzi meg, amely létezett az erőforrások más címkéket:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>Címkék kérése erőforrástípus
A következő példa bemutatja, hogyan beágyazásához szükséges egy alkalmazás címke csak a megadott erőforrástípus (ebben az esetben storage-fiókok) logikai operátor.

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Címke megkövetelése
A következő házirend megtagadja, amelyek nem rendelkeznek a címke (minden érték alkalmazható) "costCenter" kulcsot tartalmazó:

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>Következő lépések
* (A fenti példákban szerint) házirend szabály megadása után kell a házirend-definíció létrehozása, és rendelje hozzá hatókör. A hatókör lehet egy előfizetés, az erőforráscsoportot, vagy az erőforrás. A portálon keresztül házirendek rendeléséhez lásd: [hozzárendelésére és kezelésére erőforrás-házirendek használata Azure-portálon](resource-manager-policy-portal.md). REST API-t, a PowerShell vagy az Azure CLI-házirendeket rendeléséhez lásd: [meg és kezelheti a parancsfájl-házirendeket](resource-manager-policy-create-assign.md).
* Megismerkedhet az erőforrás-házirendek, lásd: [erőforrás házirendek – áttekintés](resource-manager-policy.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

