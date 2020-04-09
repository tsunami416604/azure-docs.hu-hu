---
title: Biztonság kényszerítése linuxos virtuális gépekházirendjeivel az Azure-ban
description: Szabályzat alkalmazása Azure Resource Manager Linux virtuális gépre
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: b585b4d0aef06b01cba07ab8ef016b59f9b4f61b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878918"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Szabályzatok alkalmazása Linuxos virtuális gépekre az Azure Resource Managerrel
A házirendek használatával a szervezet különböző konvenciókat és szabályokat kényszeríthet ki a vállalaton belül. A kívánt viselkedés kényszerítése segíthet a kockázat csökkentésében, miközben hozzájárul a szervezet sikeréhez. Ebben a cikkben azt ismertetjük, hogyan használhatja az Azure Resource Manager-szabályzatok a szervezet virtuális gépek kívánt viselkedésének meghatározásához.

A szabályzatok bemutatása: [Mi az Azure-szabályzat?](../../governance/policy/overview.md)

## <a name="permitted-virtual-machines"></a>Engedélyezett virtuális gépek
Annak érdekében, hogy a szervezet virtuális gépei kompatibilisek legyenek egy alkalmazással, korlátozhatja az engedélyezett operációs rendszereket. A következő házirend-példában csak ubuntu 14.04.2-LTS virtuális gépek létrehozását engedélyezi.

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

Használjon helyettesítő kártyát az előző szabályzat módosításához bármely Ubuntu LTS-lemezkép engedélyezéséhez: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

A házirendmezőkről a [Házirendaliasok című témakörben](../../governance/policy/concepts/definition-structure.md#aliases)talál további információt.

## <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezek használatának megköveteléséhez használja a következő házirendet:

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

## <a name="images-for-virtual-machines"></a>Képek virtuális gépekhez

Biztonsági okokból megkövetelheti, hogy csak jóváhagyott egyéni lemezképek vannak telepítve a környezetben. Megadhatja a jóváhagyott lemezképeket tartalmazó erőforráscsoportot vagy az adott jóváhagyott lemezképeket.

A következő példa egy jóváhagyott erőforráscsoportból származó képeket igényel:

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

A következő példa a jóváhagyott képazonosítókat adja meg:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Virtuálisgép-bővítmények

Előfordulhat, hogy meg szeretné tiltani bizonyos típusú bővítmények használatát. Előfordulhat például, hogy egy bővítmény nem kompatibilis bizonyos egyéni virtuálisgép-lemezképekkel. A következő példa bemutatja, hogyan blokkolhatja egy adott bővítmény. A közzétevő és a típus segítségével határozza meg, hogy melyik bővítményt kell blokkolni.

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
* Miután definiált egy házirendszabályt (ahogy az előző példákban is látható), létre kell hoznia a házirend-definíciót, és hozzá kell rendelnie egy hatókörhöz. A hatókör lehet előfizetés, erőforráscsoport vagy erőforrás. Házirendek hozzárendeléséhez olvassa el az [Azure Portal használata erőforrás-házirendek hozzárendeléséhez és kezeléséhez,](../../governance/policy/assign-policy-portal.md) [a PowerShell használata szabályzatok hozzárendeléséhez,](../../governance/policy/assign-policy-powershell.md)vagy [az Azure CLI használata házirendek hozzárendeléséhez.](../../governance/policy/assign-policy-azurecli.md)
* Az erőforrás-szabályzatok bemutatása: [Mi az Azure-szabályzat?](../../governance/policy/overview.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
