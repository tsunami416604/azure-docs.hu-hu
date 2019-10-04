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
ms.openlocfilehash: 87025767725142cc2f861ff8b390d6ea916f8e38
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947732"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Kulcstartó bérlőazonosítójának módosítása az előfizetés áthelyezése után

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>K: Az előfizetésem át lett helyezve az A bérlőből a B bérlőbe. Hogyan módosíthatom a meglévő kulcstartó bérlőazonosítóját, és hogyan állíthatom be megfelelően a résztvevők hozzáférés-vezérlési listáját (ACL) a B bérlőben?

Amikor új kulcstartót hoz létre egy előfizetésben, az automatikusan annak az előfizetésnek az alapértelmezett Azure Active Directory-bérlőazonosítójához kötődik. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. Ha áthelyezi az Azure előfizetését az A bérlőtől a B bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a B bérlőhöz tartozó rendszerbiztonsági tagok (felhasználók és alkalmazások) számára. A következőképpen oldhatja meg a problémát:

* Módosítania kell minden ebbe az előfizetésbe tartozó meglévő kulcstartó bérlőazonosítóját a B bérlőre.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Új, a B bérlőhöz kapcsolódó hozzáférésiszabályzat-bejegyzéseket kell létrehoznia.

Ha például a „kucstarto” kulcstartót használja egy olyan előfizetésben, amelyet áthelyezett az A bérlőtől a B bérlőhöz, a következőképpen módosíthatja a kulcs bérlőazonosítóját, és törölheti a régi hozzáférési szabályzatokat.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

Mivel ez a tár az áthelyezés előtt volt az A bérlőn, a $vault eredeti értéke **. A Properties. TenantId** az a bérlő, a **(Get-AzContext). A bérlő. TenantId** a bérlő B.

Most, hogy a tároló hozzá van rendelve a megfelelő bérlői AZONOSÍTÓhoz, és a régi hozzáférési szabályzatok bejegyzései törlődnek, állítsa be az új hozzáférési szabályzat bejegyzéseit a [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

Ha az MSI-t használja, frissítenie kell az MSI-identitást is, mivel a régi identitás többé nem lesz a megfelelő HRE-bérlőben.

## <a name="next-steps"></a>További lépések

Amennyiben az Azure Key Vaulttal kapcsolatban kérdése merülne fel, tekintse meg az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
