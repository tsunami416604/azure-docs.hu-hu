---
title: Az ügyfél által felügyelt kulcsok konfigurálása az Azure-ban Adatkezelő a Azure Resource Manager sablon használatával
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az ügyfél által felügyelt kulcsok titkosítását az Azure-Adatkezelőban tárolt adataihoz a Azure Resource Manager sablon használatával.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: bff8c50cdece803e030c0975a9b14ac5739baaf7
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725805"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Az ügyfél által felügyelt kulcsok konfigurálása a Azure Resource Manager sablon használatával

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Azure Resource Manager-sablon](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal

Ebben a szakaszban Azure Resource Manager-sablonok használatával konfigurálja az ügyfél által felügyelt kulcsokat. Alapértelmezés szerint az Azure Adatkezelő encryption a Microsoft által felügyelt kulcsokat használja. Ebben a lépésben konfigurálja az Azure Adatkezelő-fürtöt az ügyfél által felügyelt kulcsok használatára, és adja meg a fürthöz társítandó kulcsot.

A Azure Resource Manager sablont a Azure Portal vagy a PowerShell használatával is telepítheti.

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

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcsok új verziójának létrehozásakor frissítenie kell a fürtöt az új verzió használatára. Először hívja meg `Get-AzKeyVaultKey` a kulcs legújabb verziójának beszerzéséhez. Ezután frissítse a fürt kulcstartójának tulajdonságait úgy, hogy az a kulcs új verzióját használja, ahogy azt a [titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal](#configure-encryption-with-customer-managed-keys)című rész mutatja.

## <a name="next-steps"></a>Következő lépések

* [Azure Adatkezelő-fürtök védelme az Azure-ban](security.md)
* [Felügyelt identitások konfigurálása az Azure Adatkezelő-fürthöz](managed-identities.md)
* [A fürt biztonságossá tétele az Azure adatkezelő-Azure Portalban](manage-cluster-security.md) a titkosítás nyugalmi állapotban való engedélyezésével.
* [Ügyfél által felügyelt kulcsok konfigurálása a használatávalC#](customer-managed-keys-csharp.md)

