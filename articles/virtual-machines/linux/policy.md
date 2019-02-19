---
title: A házirendek az Azure-beli Linuxos virtuális gépeken biztonság kényszerítése |} A Microsoft Docs
description: Szabályzat alkalmazása az Azure Resource Manager Linux rendszerű virtuális gépként
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 8ad1bf371c5d5dbcbf3657ad69eace2003a8dda9
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342011"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Linux rendszerű virtuális gépek az Azure Resource Manager-szabályzatok alkalmazása
Házirendek segítségével egy szervezet különböző konvenciók és szabályokat a vállalaton kényszeríthető. A kívánt viselkedés kényszerítésének segíthet hozzájárul a sikeres, a szervezet kockázatok. Ebben a cikkben ismertetünk, hogyan használhatja az Azure Resource Manager-házirendek a szervezet virtuális gépek számára a kívánt viselkedés meghatározására.

Házirendek bemutatása, lásd: [Mi az Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Engedélyezett a virtuális gépek
Annak érdekében, hogy egy alkalmazás kompatibilis virtuális gépek a szervezet számára, korlátozhatja az engedélyezett operációs rendszerek. A következő példában a házirend engedélyezése csak Ubuntu 14.04.2-LTS virtuális gépek hozhatók létre.

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
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
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

Egy helyettesítő karakter segítségével, hogy minden olyan Ubuntu LTS rendszerképet az előző házirend módosítása: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

A házirend mezőkkel kapcsolatos információkért lásd: [házirend aliasok](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezek használata szükséges, használja a következő szabályzatot:

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

## <a name="images-for-virtual-machines"></a>Virtuális gépek lemezképei

Biztonsági okokból megkövetelheti, hogy csak a jóváhagyott egyéni rendszerképek telepítve vannak-e a környezetben. Vagy az erőforráscsoport, amely tartalmazza a jóváhagyott lemezképeket is megadhat, vagy az adott jóváhagyott lemezképeket.

Az alábbi példa egy jóváhagyott erőforráscsoportból lemezképek van szükség:

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

Az alábbi példa meghatározza az engedélyezett lemezkép-azonosítók:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Virtuálisgép-bővítmények

Előfordulhat, hogy szeretné megtiltsa bizonyos típusú bővítmények használatát. Például egy bővítmény előfordulhat, hogy nem kompatibilisek az egyes egyéni virtuálisgép-lemezképeket. Az alábbi példa bemutatja, hogyan blokkolja egy adott kiterjesztéssel. Közzétevő és a típus használatával határozza meg, melyik bővítmény letiltása.

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


## <a name="next-steps"></a>További lépések
* (Ahogyan az az előző példákból látható) házirend szabály meghatározása, után kell létrehozni a szabályzatdefiníciót, és rendelje hozzá egy hatókörhöz. A hatókör egy előfizetés, erőforráscsoport vagy erőforrás lehet. Házirendek rendeléséhez lásd: [erőforrás-házirendek hozzárendelése és kezelése az Azure Portalon](../../governance/policy/assign-policy-portal.md), [PowerShell-lel történő szabályzatokat rendelhet hozzájuk](../../governance/policy/assign-policy-powershell.md), vagy [használható Azure CLI-vel házirendek rendelhetők](../../governance/policy/assign-policy-azurecli.md).
* Erőforrás-házirendek bemutatása, lásd: [Mi az Azure Policy?](../../governance/policy/overview.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
