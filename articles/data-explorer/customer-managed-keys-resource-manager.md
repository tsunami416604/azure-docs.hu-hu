---
title: Ügyfél által kezelt kulcsok konfigurálása az Azure Data Explorerben az Azure Resource Manager sablon használatával
description: Ez a cikk ismerteti, hogyan konfigurálhatja az ügyfél által felügyelt kulcsok titkosítását az adatok az Azure Data Explorer az Azure Resource Manager sablon használatával.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297906"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával

> [!div class="op_single_selector"]
> * [Portál](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sablon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása ügyfél által kezelt kulcsokkal

Ebben a szakaszban konfigurálhatja az ügyfelek által felügyelt kulcsokat az Azure Resource Manager-sablonok használatával. Alapértelmezés szerint az Azure Data Explorer titkosítás a Microsoft által felügyelt kulcsokat használja. Ebben a lépésben konfigurálja az Azure Data Explorer-fürtaz ügyfél által felügyelt kulcsok at, és adja meg a fürthöz társítani kívánt kulcsot.

Az Azure Resource Manager-sablont az Azure Portalon vagy a PowerShell használatával telepítheti.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>A kulcsverzió frissítése

Amikor egy kulcs új verzióját hozza létre, frissítenie kell a fürtöt az új verzió használatához. Először `Get-AzKeyVaultKey` hívja a kulcs legújabb verzióját. Ezután frissítse a fürt kulcstartójának tulajdonságait a kulcs új verziójának használatához, ahogy az [a Titkosítás konfigurálása ügyfél által kezelt kulcsokkal](#configure-encryption-with-customer-managed-keys)című részben látható.

## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Data Explorer-fürtök az Azure-ban](security.md)
* [Felügyelt identitások konfigurálása az Azure Data Explorer-fürthöz](managed-identities.md)
* [Biztonságossá a fürt az Azure Data Explorer – Azure Portal](manage-cluster-security.md) in in-in titkosítás engedélyezésével.
* [Ügyfél által kezelt kulcsok konfigurálása C használatával #](customer-managed-keys-csharp.md)

