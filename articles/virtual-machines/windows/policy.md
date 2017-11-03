---
title: "A házirendek a Windows-alapú virtuális gépek Azure-ban biztonság kényszerítése |} Microsoft Docs"
description: "Egy házirend alkalmazása az Azure Resource Manager Windows virtuális gépeket"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 246f5958478fd6d9afc9ba990413ab08429bd25d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Windows virtuális gépek az Azure Resource Manager-szabályzatok alkalmazása
Házirendek segítségével a szervezetek kényszerítheti a különböző egyezmények és szabályok a vállalaton belül. A kívánt viselkedés végrehajtását segítségével mérsékelhetik a kockázatokat hozzájárul a szervezet sikeres. Ez a cikk azt ismerteti használatát Azure Resource Manager-házirendek megadhatók a kívánt viselkedés a szervezet virtuális gépekhez.

Házirendek bemutatása, lásd: [kezelheti az erőforrásokat, és hozzáférés szabályozása házirendekkel](../../azure-resource-manager/resource-manager-policy.md).

## <a name="permitted-virtual-machines"></a>Engedélyezett virtuális gépek
Győződjön meg arról, hogy a szervezet virtuális gépek kompatibilisek egy alkalmazás, korlátozhatja az engedélyezett operációs rendszerek. A következő házirend-példa engedélyezése csak a Windows Server 2012 R2 Datacenter virtuális gépeket létrehozni:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Egy helyettesítő karakter használatával módosíthatja a Windows Server Datacenter képet előző házirendhez:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

AnyOf segítségével bármilyen Windows Server 2012 R2 Datacenter vagy magasabb kép előző szabályzat módosítása:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Házirend mezőkkel kapcsolatos információkért lásd: [házirend aliasok](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="managed-disks"></a>Felügyelt lemezek

Felügyelt lemezek használata szükséges, használja a következő házirendet:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>A virtuális gépek lemezképek

Biztonsági okokból megkövetelheti, hogy csak a jóváhagyott egyéni lemezképek telepítve vannak-e a környezetében. Vagy az erőforráscsoport, amely tartalmazza a jóváhagyott lemezképeket is megadhat, vagy a specifikus jóváhagyott lemezképeket.

A következő példa egy jóváhagyott erőforráscsoportból képek van szükség:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

A következő példa meghatározza, hogy a jóváhagyott lemezkép-azonosítók:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Virtuálisgép-bővítmények

Érdemes lehet megtiltják bizonyos típusú bővítmények használatát. Egy bővítmény például nem lehet kompatibilis bizonyos egyéni virtuálisgép-lemezképeket. A következő példa bemutatja, hogyan blokkolja egy adott kiterjesztéssel. Gyártó és típus használatával határozza meg, melyik bővítmény letiltása.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Azure hibrid használata juttatás

Ha a helyszíni licenccel rendelkezik, a licenc díj is mentheti a virtuális gépek. A licenccel nem rendelkező, meg kell megtiltják a beállítást. A következő házirend tiltja a használati Azure hibrid használata juttatás (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Következő lépések
* (A fenti példákban szerint) házirend szabály megadása után kell a házirend-definíció létrehozása, és rendelje hozzá hatókör. A hatókör lehet egy előfizetés, az erőforráscsoportot, vagy az erőforrás. A portálon keresztül házirendek rendeléséhez lásd: [hozzárendelésére és kezelésére erőforrás-házirendek használata Azure-portálon](../../azure-resource-manager/resource-manager-policy-portal.md). REST API-t, a PowerShell vagy az Azure CLI-házirendeket rendeléséhez lásd: [meg és kezelheti a parancsfájl-házirendeket](../../azure-resource-manager/resource-manager-policy-create-assign.md).
* Megismerkedhet az erőforrás-házirendek, lásd: [erőforrás házirendek – áttekintés](../../azure-resource-manager/resource-manager-policy.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](../../azure-resource-manager/resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
