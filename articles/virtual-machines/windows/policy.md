---
title: Biztonság kényszerítése az Azure-beli Windows-virtuális gépek házirendjeivel
description: Szabályzat alkalmazása Azure Resource Manager rendszerű virtuális gépre
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 407e5cefe9f7f60c86de6e80133ff1b3f8b9003d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086538"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Szabályzatok alkalmazása Windows-alapú virtuális gépekre az Azure Resource Manager rel
A házirendek használatával a szervezet különböző konvenciókat és szabályokat kényszeríthet ki a vállalaton belül. A kívánt viselkedés kényszerítése segíthet a kockázat csökkentésében, miközben hozzájárul a szervezet sikeréhez. Ebben a cikkben azt ismertetjük, hogyan használhatja az Azure Resource Manager-szabályzatok a szervezet virtuális gépek kívánt viselkedésének meghatározásához.

A szabályzatok bemutatása: [Mi az Azure-szabályzat?](../../governance/policy/overview.md)

## <a name="permitted-virtual-machines"></a>Engedélyezett virtuális gépek
Annak érdekében, hogy a szervezet virtuális gépei kompatibilisek legyenek egy alkalmazással, korlátozhatja az engedélyezett operációs rendszereket. A következő házirend-példában csak Windows Server 2012 R2 datacenter virtuális gépek létrehozását engedélyezi:

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

Helyettesítő karakter használatával módosítsa az előző házirendet a Windows Server Datacenter-lemezképek engedélyezéséhez:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Az előző házirend módosításával módosíthatja a Windows Server 2012 R2 datacenter vagy újabb lemezképet:

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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Ha rendelkezik helyszíni licenccel, mentheti a licencdíjat a virtuális gépeken. Ha nem rendelkezik a licenc, meg kell tiltani a lehetőséget. A következő szabályzat tiltja az Azure Hybrid Use Benefit (AHUB) használatát:

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
* Miután definiált egy házirendszabályt (ahogy az előző példákban is látható), létre kell hoznia a házirend-definíciót, és hozzá kell rendelnie egy hatókörhöz. A hatókör lehet előfizetés, erőforráscsoport vagy erőforrás. Házirendek hozzárendeléséhez olvassa el az [Azure Portal használata erőforrás-házirendek hozzárendeléséhez és kezeléséhez,](../../governance/policy/assign-policy-portal.md) [a PowerShell használata szabályzatok hozzárendeléséhez,](../../governance/policy/assign-policy-powershell.md)vagy [az Azure CLI használata házirendek hozzárendeléséhez.](../../governance/policy/assign-policy-azurecli.md)
* Az erőforrás-szabályzatok bemutatása: [Mi az Azure-szabályzat?](../../governance/policy/overview.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
