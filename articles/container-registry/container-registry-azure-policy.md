---
title: Megfelelőség az Azure-szabályzat használatával
description: Az Azure-tároló-beállításjegyzékek megfelelőségének naplózásához rendeljen beépített szabályzatokat az Azure-szabályzatokban
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330736"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával

[Az Azure Policy](../governance/policy/overview.md) egy azure-beli szolgáltatás, amelyet szabályzatok létrehozására, hozzárendeléséhez és kezeléséhez használ. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek.

Ez a cikk bemutatja az Azure Container Registry beépített szabályzatait (előzetes verzió). Ezekkel a házirendekkel naplózhato az új és meglévő megfelelőségi jegyzékeket.

Az Azure-szabályzat használatáért nem számítunk fel díjat.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="built-in-policy-definitions"></a>Beépített házirend-definíciók

A következő beépített szabályzat-definíciók az Azure Container Registry-re vonatkoznak:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Lásd még a beépített hálózati házirend-definíció: [[Preview] Container Registry kell használnia a virtuális hálózati szolgáltatás végpontját.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)

## <a name="assign-policies"></a>Szabályzatok hozzárendelése

* Házirendek hozzárendelése az [Azure Portalon,](../governance/policy/assign-policy-portal.md)az [Azure CLI,](../governance/policy/assign-policy-azurecli.md)a [Resource Manager-sablon](../governance/policy/assign-policy-template.md)vagy az Azure Policy SDK-k használatával.
* Szabályzat-hozzárendelés hatóköre egy erőforráscsoporthoz, egy előfizetéshez vagy egy [Azure felügyeleti csoporthoz.](../governance/management-groups/overview.md) A tárolóbeállítási házirend-hozzárendelések a hatókörön belüli meglévő és új tárolójegyzékekre vonatkoznak.
* Bármikor engedélyezheti vagy letilthatja a [házirend-kényszerítést.](../governance/policy/concepts/assignment-structure.md#enforcement-mode)

> [!NOTE]
> A házirend hozzárendelése vagy frissítése után némi időt vesz igénybe, amíg a hozzárendelés a megadott hatókör erőforrásaira lesz alkalmazva. Tekintse meg a [házirend-értékelési eseményindítókról](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)szóló információkat.

## <a name="review-policy-compliance"></a>A szabályzatok megfelelőségének áttekintése

Az Azure Portalon, az Azure parancssori eszközein vagy az Azure Policy SDK-kon keresztül elérheti a szabályzat-hozzárendelések által létrehozott megfelelőségi információkat. További információt az [Azure-erőforrások megfelelőségi adatainak beszereznie.](../governance/policy/how-to/get-compliance-data.md)

Ha egy erőforrás nem megfelelő, számos oka lehet. Az ok meghatározásához vagy a változás felelősének megkereséséhez [olvassa el a Meg nem felelés megállapítása](../governance/policy/how-to/determine-non-compliance.md)című témakört.

### <a name="policy-compliance-in-the-portal"></a>A házirendek megfelelősége a portálon:

1. Válassza a **Minden szolgáltatás**lehetőséget, és keresse meg a **Házirend**kifejezést.
1. Válassza a **Megfelelőség**lehetőséget.
1. A szűrők segítségével korlátozhatja a megfelelőségi állapotokat, vagy megkeresheti a szabályzatok ![megfelelőségét a portálon.](./media/container-registry-azure-policy/azure-policy-compliance.png)
1. Válasszon ki egy szabályzatot az összesített megfelelőségi részletek és események áttekintéséhez. Ha szükséges, válasszon ki egy adott rendszerleíró adatbázist az erőforrás-megfelelőséghez.

### <a name="policy-compliance-in-the-azure-cli"></a>Házirend-megfelelőség az Azure CLI-ben

Az Azure CLI használatával megfelelőségi adatokat is beszerezhet. Például az [az policy assignment list](/cli/azure/policy/assignment#az-policy-assignment-list) parancs a CLI-ben az Azure Container Registry szabályzatok alkalmazott házirend-azonosítóinak lekérni:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Példa a kimenetre:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Ezután futtassa az [az házirendállapot-listát](/cli/azure/policy/state#az-policy-state-list) a JSON-formátumú megfelelőségi állapot visszaadásához egy adott házirend-azonosítóban szereplő összes erőforráshoz:

```azurecli
az policy state list \
  --resource <policyID>
```

Vagy [futtassa az az házirendállapot-listát](/cli/azure/policy/state#az-policy-state-list) egy adott rendszerleíró adatbázis JSON-formátumú megfelelőségi állapotának, például a myregistry nak a visszaadására: *myregistry*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések

* További információ az Azure-szabályzat [definícióiról](../governance/policy/concepts/definition-structure.md) és [hatásairól](../governance/policy/concepts/effects.md)

* Egyéni [házirend-definíció](../governance/policy/tutorials/create-custom-policy-definition.md) létrehozása

* További információ az Azure [cégirányítási képességeiről](../governance/index.yml)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/