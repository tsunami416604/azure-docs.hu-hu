---
title: A kulcstartó Bérlőazonosítójának módosítása az előfizetés áthelyezése – Azure Key Vault után |} A Microsoft Docs
description: A cikkből megtudhatja, hogyan módosíthatja a kulcstartó bérlőazonosítóját, miután egy előfizetést másik bérlőhöz helyezett át.
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: f32146697be234a8a288ff991b1f7adf6e76dc7e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724489"
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
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Mivel ez a kulcstartó előzőleg az eredeti értéke az áthelyezést megelőzően az a bérlőhöz **$vault. Properties.TenantId** a bérlő, ideje **(Get-AzContext). Tenant.TenantId** értéke pedig b bérlő.

Most, hogy a tároló társítva a helyes Bérlőazonosítóhoz, és a régi hozzáférésiszabályzat-bejegyzések el lesznek távolítva, beállíthatja az új hozzáférésiszabályzat-bejegyzéseket a [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

## <a name="next-steps"></a>További lépések

Amennyiben az Azure Key Vaulttal kapcsolatban kérdése merülne fel, tekintse meg az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
