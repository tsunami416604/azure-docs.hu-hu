---
title: A Key Vault-bérlő AZONOSÍTÓjának módosítása az előfizetés áthelyezése után – Azure Key Vault | Microsoft Docs
description: A cikkből megtudhatja, hogyan módosíthatja a kulcstartó bérlőazonosítóját, miután egy előfizetést másik bérlőhöz helyezett át.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 3819742e82fe6877b6a1aa58e52eec01b6b05515
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001241"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Kulcstartó bérlőazonosítójának módosítása az előfizetés áthelyezése után

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Amikor új kulcstartót hoz létre egy előfizetésben, az automatikusan annak az előfizetésnek az alapértelmezett Azure Active Directory-bérlőazonosítójához kötődik. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. 

Ha áthelyezi az Azure-előfizetését az A bérlőtől a B bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a B bérlőhöz tartozó rendszerbiztonsági tag (felhasználók és alkalmazások) számára. A probléma megoldásához a következőket kell tennie:

* Módosítsa az előfizetésben lévő összes meglévő kulcstartóhoz tartozó bérlői azonosítót a B bérlőre.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Adja hozzá a B bérlőhöz társított új hozzáférési szabályzatok bejegyzéseit.

Ha például egy "myvault" kulcstartó található egy olyan előfizetésben, amely az A bérlőtől a B bérlőhöz lett áthelyezve, akkor a Azure PowerShell használatával módosíthatja a bérlői azonosítót, és eltávolíthatja a régi hozzáférési házirendeket.

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

Vagy használhatja az Azure CLI-t is.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Most, hogy a tároló hozzá van rendelve a megfelelő bérlői AZONOSÍTÓhoz és a régi hozzáférési szabályzatok bejegyzéseihez, az új hozzáférési szabályzat bejegyzéseit a Azure PowerShell [set-AzKeyVaultAccessPolicy](https://powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) parancsmaggal vagy az Azure CLI az Key [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal teheti meg.

Ha felügyelt identitást használ az Azure-erőforrásokhoz, azt is frissítenie kell az új Azure AD-bérlőre. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Key Vault hitelesítés biztosítása felügyelt identitással](managed-identity.md).


Ha az MSI-t használja, frissítenie kell az MSI-identitást is, mivel a régi identitás többé nem lesz a megfelelő HRE-bérlőben.

## <a name="next-steps"></a>További lépések

Amennyiben az Azure Key Vaulttal kapcsolatban kérdése merülne fel, tekintse meg az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
