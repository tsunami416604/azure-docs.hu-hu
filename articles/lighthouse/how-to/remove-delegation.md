---
title: Delegáláshoz való hozzáférés eltávolítása
description: Megtudhatja, hogyan távolíthatja el az Azure-beli delegált erőforrás-kezeléshez a szolgáltatóhoz delegált erőforrásokhoz való hozzáférést.
ms.date: 04/24/2020
ms.topic: how-to
ms.openlocfilehash: 2a8024a47b3f7ea461e4ea7648db7003889552cf
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636495"
---
# <a name="remove-access-to-a-delegation"></a>Delegáláshoz való hozzáférés eltávolítása

Miután az ügyfél előfizetését vagy erőforráscsoportot delegálták az Azure-beli [delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md)szolgáltatójának, a delegálás szükség esetén eltávolítható. A delegálás eltávolítását követően a szolgáltatói bérlőben lévő felhasználók számára korábban megadott hozzáférés többé nem lesz érvényes.

A delegálás eltávolítását az ügyfél vagy a szolgáltató bérlője felhasználója hajthatja végre, amennyiben a felhasználó rendelkezik a megfelelő engedélyekkel.

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
2. Válassza a **delegálások**lehetőséget.
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
