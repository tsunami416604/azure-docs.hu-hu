---
title: A házirendek az Azure-beli Windows virtuális gépeken biztonság kényszerítése |} A Microsoft Docs
description: Szabályzat alkalmazása az Azure Resource Manager Windows virtuális gépként
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
ms.openlocfilehash: 42b62c819fd3d26c6ea944f968e0d5956a7f055e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987458"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Windows virtuális gépek az Azure Resource Manager-szabályzatok alkalmazása
Házirendek segítségével egy szervezet különböző konvenciók és szabályokat a vállalaton kényszeríthető. A kívánt viselkedés kényszerítésének segíthet hozzájárul a sikeres, a szervezet kockázatok. Ebben a cikkben ismertetünk, hogyan használhatja az Azure Resource Manager-házirendek a szervezet virtuális gépek számára a kívánt viselkedés meghatározására.

Házirendek bemutatása, lásd: [Mi az Azure Policy?](../../azure-policy/azure-policy-introduction.md).

## <a name="permitted-virtual-machines"></a>Engedélyezett a virtuális gépek
Annak érdekében, hogy egy alkalmazás kompatibilis virtuális gépek a szervezet számára, korlátozhatja az engedélyezett operációs rendszerek. A következő példában a házirend engedélyezése csak a Windows Server 2012 R2 Datacenter virtuális gépek hozhatók létre:

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

Egy helyettesítő karakter segítségével bármilyen Windows Server Datacenter rendszerképet engedélyezéséhez az előző házirend módosítása:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

AnyOf segítségével bármilyen Windows Server 2012 R2 Datacenter vagy magasabb kép engedélyezi az előző házirend módosítása:

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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Ha egy helyszíni licenccel rendelkezik, az licencdíját mentheti a virtuális gépeken. Ha nem rendelkezik a licenc, meg kell megtiltsa lehetőséget. A következő házirend tiltja a használat az Azure hibrid használati juttatás (AHUB):

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
* (Ahogyan az az előző példákból látható) házirend szabály meghatározása, után kell létrehozni a szabályzatdefiníciót, és rendelje hozzá egy hatókörhöz. A hatókör egy előfizetés, erőforráscsoport vagy erőforrás lehet. Házirendek rendeléséhez lásd: [erőforrás-házirendek hozzárendelése és kezelése az Azure Portalon](../../azure-policy/assign-policy-definition.md), [PowerShell-lel történő szabályzatokat rendelhet hozzájuk](../../azure-policy/assign-policy-definition-ps.md), vagy [használható Azure CLI-vel házirendek rendelhetők](../../azure-policy/assign-policy-definition-cli.md).
* Erőforrás-házirendek bemutatása, lásd: [Mi az Azure Policy?](../../azure-policy/azure-policy-introduction.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.