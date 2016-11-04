---
title: Kulcstartó bérlőazonosítójának módosítása az előfizetés áthelyezése után | Microsoft Docs
description: A cikkből megtudhatja, hogyan módosíthatja a kulcstartó bérlőazonosítóját, miután egy előfizetést másik bérlőhöz helyezett át.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# Kulcstartó bérlőazonosítójának módosítása az előfizetés áthelyezése után
### Kérdés: Az előfizetésem átkerült A bérlőtől B bérlőhöz. Hogyan módosíthatom a meglévő kulcstartó bérlőazonosítóját, és hogyan állíthatok be helyes ACL-eket a B bérlő rendszerbiztonsági tagjai számára?
Amikor új kulcstartót hoz létre egy előfizetésben, az automatikusan annak az előfizetésnek az alapértelmezett Azure Active Directory-bérlőazonosítójához kötődik. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. Ha áthelyezi az Azure előfizetését az A bérlőtől a B bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a B bérlőhöz tartozó rendszerbiztonsági tagok (felhasználók és alkalmazások) számára. A következőképpen oldhatja meg a problémát:

* Módosítania kell minden ebbe az előfizetésbe tartozó meglévő kulcstartó bérlőazonosítóját a B bérlőre.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Új, a B bérlőhöz kapcsolódó hozzáférésiszabályzat-bejegyzéseket kell létrehoznia.

Ha például a „kucstarto” kulcstartót használja egy olyan előfizetésben, amelyet áthelyezett az A bérlőtől a B bérlőhöz, a következőképpen módosíthatja a kulcs bérlőazonosítóját, és törölheti a régi hozzáférési szabályzatokat.

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName kulcstarto).ResourceId $vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties $vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId $vault.Properties.AccessPolicies = @() Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Ez a kulcstartó előzőleg az A bérlőhöz tartozott, ezért a **$vault.Properties.TenantId** értéke az A bérlő, a **(Get-AzureRmContext).Tenant.TenantId** értéke pedig B bérlő.

A kulcstartó most már a helyes bérlőazonosítóhoz van társítva, a régi hozzáférésiszabályzat-bejegyzések pedig törölve vannak, tehát beállíthatja az új hozzáférésiszabályzat-bejegyzéseket a [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx) parancsmaggal.

## Következő lépések
* Amennyiben a Key Vault szolgáltatással kapcsolatos kérdése merülne fel, tekintse az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

<!--HONumber=Sep16_HO4-->


