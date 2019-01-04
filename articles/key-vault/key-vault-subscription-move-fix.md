---
title: A kulcstartó Bérlőazonosítójának módosítása az előfizetés áthelyezése – Azure Key Vault után |} A Microsoft Docs
description: A cikkből megtudhatja, hogyan módosíthatja a kulcstartó bérlőazonosítóját, miután egy előfizetést másik bérlőhöz helyezett át.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: 185a1954b1dbc3f56864b75fdbb32bdf9f18bc1a
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002229"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Kulcstartó bérlőazonosítójának módosítása az előfizetés áthelyezése után

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>KÉRDÉS: Az előfizetésem át lett helyezve az A bérlőből a B bérlőbe. Hogyan módosíthatom a meglévő kulcstartó bérlőazonosítóját, és hogyan állíthatom be megfelelően a résztvevők hozzáférés-vezérlési listáját (ACL) a B bérlőben?

Amikor új kulcstartót hoz létre egy előfizetésben, az automatikusan annak az előfizetésnek az alapértelmezett Azure Active Directory-bérlőazonosítójához kötődik. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. Ha áthelyezi az Azure előfizetését az A bérlőtől a B bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a B bérlőhöz tartozó rendszerbiztonsági tagok (felhasználók és alkalmazások) számára. A következőképpen oldhatja meg a problémát:

* Módosítania kell minden ebbe az előfizetésbe tartozó meglévő kulcstartó bérlőazonosítóját a B bérlőre.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Új, a B bérlőhöz kapcsolódó hozzáférésiszabályzat-bejegyzéseket kell létrehoznia.

Ha például a „kucstarto” kulcstartót használja egy olyan előfizetésben, amelyet áthelyezett az A bérlőtől a B bérlőhöz, a következőképpen módosíthatja a kulcs bérlőazonosítóját, és törölheti a régi hozzáférési szabályzatokat.

<pre>
Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Ez a kulcstartó az áthelyezést megelőzően az A bérlőhöz tartozott, ezért a **$vault.Properties.TenantId** eredeti értéke az A bérlő, a **(Get-AzureRmContext).Tenant.TenantId** értéke pedig B bérlő.

A kulcstartó most már a helyes bérlőazonosítóhoz van társítva, a régi hozzáférésiszabályzat-bejegyzések pedig törölve vannak, tehát beállíthatja az új hozzáférésiszabályzat-bejegyzéseket a [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) parancsmaggal.

## <a name="next-steps"></a>További lépések

Amennyiben az Azure Key Vaulttal kapcsolatban kérdése merülne fel, tekintse meg az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
