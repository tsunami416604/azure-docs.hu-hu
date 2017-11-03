---
title: "Az Azure erőforrás-házirendek storage-fiókok |} Microsoft Docs"
description: "A tárfiókok a központi telepítés kezelése az Azure Resource Manager házirendek ismerteti."
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
ms.date: 07/05/2017
ms.author: tomfitz
ms.openlocfilehash: 6612ee61f5c50e743241b92030660cea7ae7094d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-to-storage-accounts"></a>Erőforrás-házirendek alkalmazása a storage-fiókok
Ez a témakör bemutatja a több [erőforrás-házirendek](resource-manager-policy.md) alkalmazhat az Azure storage-fiókok. Ezek a házirendek biztosítják a konzisztenciát a szervezetben telepített storage-fiókok. 

## <a name="define-permitted-storage-account-types"></a>Engedélyezett tárolási fiók típusainak definiálása

A következő házirend korlátozza, amely [tárfióktípusokat](../storage/common/storage-redundancy.md) telepíthető:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
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

Az engedélyezett termékváltozatok elfogadására való paraméterrel hasonló házirendszabály, beépített házirend-definíció érhető el. A beépített-házirendben engedélyezve van az erőforrás-azonosítója, `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1`. 

## <a name="define-permitted-access-tier"></a>Engedélyezett hozzáférési szint meghatározása

A következő házirend határozza meg a [hozzáférési szint](../storage/blobs/storage-blob-storage-tiers.md) storage-fiókok, amelyek adható meg:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>Győződjön meg róla, engedélyezve van

A következő házirend szükséges ahhoz, hogy az összes tárfiók [Storage szolgáltatás titkosítási](../storage/common/storage-service-encryption.md):

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Ez a házirend-szabály is rendelkezésre áll, mint az erőforrás-azonosító, a beépített házirend-definíció `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f`.

## <a name="next-steps"></a>Következő lépések
* (A fenti példákban szerint) házirend szabály megadása után kell a házirend-definíció létrehozása, és rendelje hozzá hatókör. A hatókör lehet egy előfizetés, az erőforráscsoportot, vagy az erőforrás. A portálon keresztül házirendek rendeléséhez lásd: [hozzárendelésére és kezelésére erőforrás-házirendek használata Azure-portálon](resource-manager-policy-portal.md). REST API-t, a PowerShell vagy az Azure CLI-házirendeket rendeléséhez lásd: [meg és kezelheti a parancsfájl-házirendeket](resource-manager-policy-create-assign.md). 
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

