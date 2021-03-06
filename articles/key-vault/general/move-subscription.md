---
title: A tár másik előfizetésre való áthelyezése Azure Key Vault | Microsoft Docs
description: Útmutató a kulcstartó egy másik előfizetéshez való áthelyezéséhez.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: e0cd4cad74257dbf83ec8d30405eacca341a8d31
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289528"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Azure Key Vault áthelyezése másik előfizetésre

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> **Ha egy kulcstartót másik előfizetésre helyez át, a rendszer a környezetét is megváltoztatja.**
> Ügyeljen rá, hogy megértse a változás hatását, és figyelmesen kövesse a cikk útmutatását, mielőtt a Key Vault új előfizetésre való áthelyezését döntene.
> Ha felügyelt szolgáltatásbeli identitásokat (MSI-t) használ, olvassa el a dokumentum végén található áthelyezés utáni utasításokat. 

Kulcstartó létrehozásakor a rendszer automatikusan az alapértelmezett Azure Active Directory bérlői AZONOSÍTÓhoz kapcsolódik ahhoz az előfizetéshez, amelyben létrehozták. A hozzáférési szabályzatok minden bejegyzése is ehhez a bérlőazonosítóhoz kapcsolódik. Ha áthelyezi az Azure-előfizetését az A bérlőtől a B bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a B bérlőhöz tartozó egyszerű szolgáltatásnév (felhasználók és alkalmazások) számára. A probléma megoldásához a következőket kell tennie:

* Módosítsa az előfizetésben lévő összes meglévő kulcstartóhoz tartozó bérlői azonosítót a B bérlőre.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Adja hozzá a B bérlőhöz társított új hozzáférési szabályzatok bejegyzéseit.

## <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> **A lemez titkosításához használt kulcstartók nem helyezhetők át** Ha a Key vaultot a virtuális gép lemezes titkosításával használja, a kulcstároló nem helyezhető át másik erőforráscsoporthoz vagy előfizetésbe, amíg a lemez titkosítása engedélyezve van. A Key Vault új erőforráscsoporthoz vagy előfizetésbe való áthelyezése előtt le kell tiltania a lemez titkosítását. 

Egyes egyszerű szolgáltatások (felhasználók és alkalmazások) egy adott bérlőhöz vannak kötve. Ha áthelyezi a kulcstartót egy másik bérlőn lévő előfizetésbe, akkor előfordulhat, hogy nem fogja tudni visszaállítani az adott egyszerű szolgáltatáshoz való hozzáférést. Győződjön meg arról, hogy az összes alapvető szolgáltatásnév létezik abban a bérlőben, ahová a kulcstartót helyezi át.

## <a name="design-considerations"></a>Kialakítási szempontok

Előfordulhat, hogy a szervezete az előfizetési szinten kikényszerített vagy kizáró Azure Policy implementált. Előfordulhat, hogy az előfizetésben egy másik szabályzat-hozzárendelés szerepel, és az előfizetés, amelyben a kulcstartót helyezi át. A házirend-követelmények ütközésének lehetősége van az alkalmazások megszakítására.

### <a name="example"></a>Példa

Van egy olyan alkalmazás, amely a Key vaulthoz csatlakozik, és két évig érvényes tanúsítványokat hoz létre. Annak az előfizetésnek, amelyen át szeretné helyezni a kulcstartót, rendelkezik egy olyan szabályzat-hozzárendeléssel, amely letiltja az egy évnél hosszabb ideig érvényes tanúsítványok létrehozását. Miután áthelyezte a kulcstartót az új előfizetésre, a műveletet egy Azure-szabályzat-hozzárendelés letiltja, amely két évre érvényes tanúsítványt hoz létre.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a Azure Portal Azure Policy lapjára mutat, és tekintse meg a jelenlegi előfizetéshez tartozó szabályzat-hozzárendeléseket, valamint azt az előfizetést, amelybe áthelyezi, és ellenőrizze, hogy nincsenek-e eltérések.

## <a name="prerequisites"></a>Előfeltételek

* Közreműködői szintű hozzáférés vagy magasabb a jelenlegi előfizetéshez, ahol a kulcstartó létezik.
* Közreműködői szintű hozzáférés vagy magasabb ahhoz az előfizetéshez, ahová át szeretné helyezni a kulcstartót.
* Egy erőforráscsoport az új előfizetésben.

## <a name="procedure"></a>Eljárás

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>Key Vault áthelyezése egy új előfizetésre ugyanazon a bérlőn belül

1. Bejelentkezés az Azure Portalra
2. Navigáljon a kulcstartóhoz
3. Kattintson az "áttekintés" fülre
4. Válassza az "áthelyezés" gombot
5. Válassza a "áthelyezés másik előfizetésre" lehetőséget a legördülő listából.
6. Válassza ki azt az erőforráscsoportot, amelyben át szeretné helyezni a kulcstartót
7. Az erőforrások áthelyezésével kapcsolatos figyelmeztetés nyugtázása
8. Válassza az OK lehetőséget

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>További lépések, ha áthelyezte a Key vaultot egy új bérlőn lévő előfizetésbe

Ha áthelyezte a kulcstartót egy új bérlő egyik előfizetéséhez, manuálisan kell frissítenie a bérlő AZONOSÍTÓját, és el kell távolítania a régi hozzáférési házirendeket. Ezek a lépések a PowerShell és az Azure CLI-ben című témakörben találhatók. Ha a PowerShellt használja, akkor előfordulhat, hogy az alábbi, az aktuálisan kijelölt Hatókörön kívüli erőforrások megtekintését lehetővé tevő Clear-AzContext parancsot kell futtatnia. 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Most, hogy a tároló hozzá van rendelve a megfelelő bérlői AZONOSÍTÓhoz és a régi hozzáférési szabályzatok bejegyzéseihez, az új hozzáférési szabályzat bejegyzéseit a Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) parancsmaggal vagy az Azure CLI az Key [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal teheti meg.

Ha felügyelt identitást használ az Azure-erőforrásokhoz, frissítenie kell az új Azure Active Directory bérlőre is. A felügyelt identitásokkal kapcsolatos további információkért [tekintse át a felügyelt identitások áttekintése](../../active-directory/managed-identities-azure-resources/overview.md)című témakört.

Ha felügyelt identitást használ, az identitást is frissítenie kell, mert a régi identitás többé nem lesz a megfelelő Azure Active Directory bérlőn. A probléma megoldásához tekintse meg a következő dokumentumokat. 

* [MSI frissítése](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Előfizetés átszállítása új könyvtárba](../../role-based-access-control/transfer-subscription.md)