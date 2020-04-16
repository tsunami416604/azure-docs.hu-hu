---
title: A key vault bérlői azonosítójának módosítása előfizetés áthelyezése után – Azure Key Vault | Microsoft dokumentumok
description: A cikkből megtudhatja, hogyan módosíthatja a kulcstartó bérlőazonosítóját, miután egy előfizetést másik bérlőhöz helyezett át.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 94bcba80e5768d57e3dc97bed1a74a8369ac60b9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422892"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Kulcstartó bérlőazonosítójának módosítása az előfizetés áthelyezése után

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


Amikor új kulcstartót hoz létre egy előfizetésben, az automatikusan annak az előfizetésnek az alapértelmezett Azure Active Directory-bérlőazonosítójához kötődik. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. 

Ha az Azure-előfizetést az A bérlőről a B bérlőre helyezi át, a meglévő kulcstartók nem érhetők el a b bérlőben lévő egyszerű (felhasználók és alkalmazások) számára. A probléma megoldásához a következőket kell a következőkre tudnia:

* Módosítsa a bérlői azonosítót társított összes meglévő kulcstartók a B bérlői előfizetésben.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Új hozzáférési házirend-bejegyzések hozzáadása a B bérlőhöz.

Ha például a "myvault" key vault egy előfizetésben, amely át került az A bérlőről a B bérlőre, az Azure PowerShell segítségével módosíthatja a bérlőazonosítót, és eltávolíthatja a régi hozzáférési szabályzatokat.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Vagy használhatja az Azure CLI.Or you can use the Azure CLI.

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Most, hogy a tároló a megfelelő bérlőazonosítóval és a régi hozzáférési szabályzat-bejegyzésekkel van társítva, állítson be új hozzáférési szabályzat-bejegyzéseket az Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) parancsmaggal vagy az Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal.

Ha felügyelt identitást használ az Azure-erőforrásokhoz, frissítenie kell azt az új Azure AD-bérlőre is. A felügyelt identitásokkal kapcsolatos további információkért [lásd: Key Vault-hitelesítés felügyelt identitással.](managed-identity.md)


Ha MSI-t használ, az MSI-identitást is frissítenie kell, mivel a régi identitás már nem lesz a megfelelő AAD-bérlőben.

## <a name="next-steps"></a>További lépések

Amennyiben az Azure Key Vaulttal kapcsolatban kérdése merülne fel, tekintse meg az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
