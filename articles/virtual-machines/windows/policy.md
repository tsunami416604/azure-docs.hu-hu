---
title: A házirendek a Windows-alapú virtuális gépek Azure-ban biztonság kényszerítése |} Microsoft Docs
description: Egy házirend alkalmazása az Azure Resource Manager Windows virtuális gépeket
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: fc95b57c244e855f6788b7f21fc9abc0eba3d44d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654708"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Windows virtuális gépek az Azure Resource Manager-szabályzatok alkalmazása
Házirendek segítségével a szervezetek kényszerítheti a különböző egyezmények és szabályok a vállalaton belül. A kívánt viselkedés végrehajtását segítségével mérsékelhetik a kockázatokat hozzájárul a szervezet sikeres. Ez a cikk azt ismerteti használatát Azure Resource Manager-házirendek megadhatók a kívánt viselkedés a szervezet virtuális gépekhez.

Házirendek bemutatása, lásd: [Mi az Azure házirend?](../../azure-policy/azure-policy-introduction.md).

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

Házirend mezőkkel kapcsolatos információkért lásd: [házirend aliasok](../../azure-policy/policy-definition.md#aliases).

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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

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

## <a name="next-steps"></a>További lépések
* (A fenti példákban szerint) házirend szabály megadása után kell a házirend-definíció létrehozása, és rendelje hozzá hatókör. A hatókör lehet egy előfizetés, az erőforráscsoportot, vagy az erőforrás. Házirendek rendeléséhez lásd: [hozzárendelésére és kezelésére erőforrás-házirendek használata Azure-portálon](../../azure-policy/assign-policy-definition.md), [házirendek rendelhetők a PowerShell](../../azure-policy/assign-policy-definition-ps.md), vagy [használja az Azure parancssori felület házirendek rendelhetők](../../azure-policy/assign-policy-definition-cli.md).
* Megismerkedhet az erőforrás-házirendek, lásd: [Mi az Azure házirend?](../../azure-policy/azure-policy-introduction.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.