---
title: Delegáláshoz való hozzáférés eltávolítása
description: Ismerje meg, hogyan távolíthatja el az Azure Lighthouse szolgáltatáshoz delegált erőforrásokhoz való hozzáférést.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: d3442bb9fd2f6e7423fd4bf28cace1f7fd91ad80
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608452"
---
# <a name="remove-access-to-a-delegation"></a>Delegáláshoz való hozzáférés eltávolítása

Ha az ügyfél előfizetése vagy erőforráscsoport delegálása megtörtént az [Azure Lighthouse](../overview.md)szolgáltatásban, a delegálást szükség esetén el lehet távolítani. A delegálás eltávolítása után az Azure-beli [delegált erőforrás-kezelési](../concepts/azure-delegated-resource-management.md) hozzáférés már nem lesz érvényes a szolgáltatói bérlő felhasználói számára.

A delegálás eltávolítását az ügyfél vagy a szolgáltató bérlője felhasználója hajthatja végre, amennyiben a felhasználó rendelkezik a megfelelő engedélyekkel.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják.

## <a name="customers"></a>Ügyfelek

Azok a felhasználók, akik az előfizetéshez tartozó [tulajdonos beépített szerepkörrel](../../role-based-access-control/built-in-roles.md#owner) rendelkeznek az ügyfél bérlője számára, eltávolíthatják az adott előfizetéshez tartozó szolgáltatói hozzáférést (vagy az adott előfizetéshez tartozó erőforráscsoportokat). Ehhez az ügyfél bérlője hozzáférhet a Azure Portal [szolgáltatói oldalához](view-manage-service-providers.md#add-or-remove-service-provider-offers) , keresse meg az ajánlatot a **szolgáltatói ajánlatok** képernyőn, és válassza ki az ajánlat sorában látható Kuka ikont.

A Törlés megerősítése után a szolgáltató bérlője egyik felhasználója sem férhet hozzá a korábban delegált erőforrásokhoz.

## <a name="service-providers"></a>Szolgáltatók

A felügyeleti bérlőben lévő felhasználók eltávolíthatják a delegált erőforrásokhoz való hozzáférést, ha a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) adták meg az ügyfél erőforrásaihoz. Ha ez a szerepkör nem lett hozzárendelve valamelyik szolgáltató felhasználóhoz, a delegálást csak az ügyfél bérlője által eltávolított felhasználó távolíthatja el.

Az alábbi példa egy olyan hozzárendelést mutat be, amely megadja a **felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét** , amelyet a rendszer a bevezetési [folyamat](onboard-customer.md)során is tartalmaz.

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Ez a **szerepkör az Azure** Marketplace-en való közzétételre szolgáló [felügyelt szolgáltatási ajánlat létrehozásakor](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) is kiválasztható.

Az ezzel az engedéllyel rendelkező felhasználók a következő módszerek egyikével törölhetik a delegálást.

### <a name="azure-portal"></a>Azure Portal

1. Navigáljon a [saját ügyfelek oldalra](view-manage-customers.md).
2. Válassza a **delegálások** lehetőséget.
3. Keresse meg az eltávolítani kívánt delegálást, majd válassza ki a sorban megjelenő Kuka ikont.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md) részleteit.
- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
- Megtudhatja, hogyan [frissíthet egy korábbi delegálást](update-delegation.md).
