---
title: Terv üzembe helyezésének szakaszai
description: Megtudhatja, hogyan haladnak végig a Azure Blueprint szolgáltatások a telepítés során.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: b329613e4e4954a1ea1452017a6e6c8b7343f2d3
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048616"
---
# <a name="stages-of-a-blueprint-deployment"></a>Terv üzembe helyezésének szakaszai

Egy terv üzembe helyezése után az Azure BluePrints szolgáltatás számos műveletet végez a tervben meghatározott erőforrások üzembe helyezéséhez. Ez a cikk részletesen ismerteti az egyes lépések részleteit.

A terv üzembe helyezése egy tervnek egy előfizetéshez rendelésével vagy [egy meglévő hozzárendelés frissítésével](../how-to/update-existing-assignments.md)aktiválódik. Az üzembe helyezés során a tervek a következő magas szintű lépéseket végzik el:

> [!div class="checklist"]
> - Tulajdonosi jogosultságokat kapott tervrajzok
> - A terv-hozzárendelési objektum létrejött
> - Opcionális – a tervrajzok **rendszer által hozzárendelt** felügyelt identitást hoznak létre
> - A felügyelt identitás üzembe helyezi a tervrajz-összetevőket
> - A terv szolgáltatás és a **rendszer által hozzárendelt** felügyelt identitási jogosultságok visszavonása

## <a name="blueprints-granted-owner-rights"></a>Tulajdonosi jogosultságokat kapott tervrajzok

Az Azure BluePrints egyszerű szolgáltatás tulajdonosi jogosultságokat kap a hozzárendelt előfizetéshez vagy előfizetésekhez, ha a [rendszerhez hozzárendelt felügyelt identitású](../../../active-directory/managed-identities-azure-resources/overview.md) felügyelt identitás van használatban. A megadott szerepkör lehetővé teszi, hogy a tervek a **rendszer által hozzárendelt** felügyelt identitást hozzanak létre, majd később visszavonják. Ha **felhasználó által hozzárendelt** felügyelt identitást használ, az Azure BluePrints egyszerű szolgáltatás nem kap, és nincs szüksége tulajdonosi jogosultságokra az előfizetésben.

A jogosultságok automatikusan megadhatók, ha a hozzárendelés a portálon keresztül történik. Ha azonban a hozzárendelés a REST APIon keresztül történik, a jogokat külön API-hívással kell megadnia. A Azure Blueprint AppId `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, az egyszerű szolgáltatás azonban a bérlőtől függ. Az egyszerű szolgáltatás beszerzéséhez használja a [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) és a REST-végpont [servicePrincipals](/graph/api/resources/serviceprincipal) . Ezután adja meg az Azure a _tulajdonosi_ szerepkört a [portálon](../../../role-based-access-control/role-assignments-portal.md), az [Azure CLI](../../../role-based-access-control/role-assignments-cli.md)-n, a [Azure PowerShellon](../../../role-based-access-control/role-assignments-powershell.md), a [Rest APIon](../../../role-based-access-control/role-assignments-rest.md)vagy egy [Resource Manager-sablonon](../../../role-based-access-control/role-assignments-template.md)keresztül.

A tervrajzok szolgáltatás nem telepíti közvetlenül az erőforrásokat.

## <a name="the-blueprint-assignment-object-is-created"></a>A terv-hozzárendelési objektum létrejött

Egy felhasználó, csoport vagy szolgáltatásnév egy tervezetet rendel egy előfizetéshez. A hozzárendelési objektum létezik az előfizetés szintjén, ahol a terv hozzá lett rendelve. A központi telepítés által létrehozott erőforrások nem az üzembe helyezési entitás kontextusában vannak végrehajtva.

A terv-hozzárendelés létrehozásakor a [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusa van kiválasztva. Az alapértelmezett érték egy **rendszerhez rendelt** felügyelt identitás. A **felhasználó által hozzárendelt** felügyelt identitás választható. **Felhasználó által hozzárendelt** felügyelt identitás használatakor a terv-hozzárendelés létrehozása előtt meg kell határozni és engedélyeket adni. A [tulajdonos](../../../role-based-access-control/built-in-roles.md#owner) és a [Blueprint operátor](../../../role-based-access-control/built-in-roles.md#blueprint-operator) beépített szerepkörei egyaránt rendelkeznek a szükséges `blueprintAssignment/write` engedéllyel olyan hozzárendelés létrehozásához, amely **felhasználó által hozzárendelt** felügyelt identitást használ.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcionális – a tervrajzok rendszer által hozzárendelt felügyelt identitást hoznak létre

Ha a hozzárendelés során a [rendszerhez rendelt felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) van kiválasztva, a tervrajzok létrehozzák az identitást, és a [tulajdonos](../../../role-based-access-control/built-in-roles.md#owner) szerepkört a felügyelt identitás számára engedélyezik [Meglévő hozzárendelés frissítésekor](../how-to/update-existing-assignments.md)a tervrajzok a korábban létrehozott felügyelt identitást használják.

A terv-hozzárendeléshez kapcsolódó felügyelt identitás a tervben meghatározott erőforrások üzembe helyezésére vagy újratelepítésére szolgál. Ez a kialakítás megakadályozza, hogy a hozzárendelések véletlenül zavarják egymással.
Ez a kialakítás az [erőforrás-zárolási](./resource-locking.md) funkciót is támogatja azáltal, hogy az egyes üzembe helyezett erőforrások biztonságát a tervezetből szabályozza.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A felügyelt identitás üzembe helyezi a tervrajz-összetevőket

A felügyelt identitás ezt követően elindítja az összetevők Resource Manager általi telepítését a tervben a meghatározott sorrend szerinti [sorrendben](./sequencing-order.md). A sorrend beállítható úgy, hogy a többi összetevőtől függő összetevők megfelelő sorrendben legyenek telepítve.

Az üzemelő példányok hozzáférési meghibásodása gyakran a felügyelt identitáshoz megadott hozzáférési szint eredményét eredményezi. A tervrajzok szolgáltatás kezeli a **rendszer által hozzárendelt** felügyelt identitás biztonsági életciklusát. A felhasználó azonban felelős a **felhasználóhoz rendelt** felügyelt identitások jogainak és életciklusának kezeléséért.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>A terv szolgáltatás és a rendszer által hozzárendelt felügyelt identitási jogosultságok visszavonása

A központi telepítések befejezése után a tervrajzok visszavonják a **rendszer által hozzárendelt** felügyelt identitás jogosultságait az előfizetésből. Ezt követően a tervrajzok szolgáltatás visszavonja a jogosultságait az előfizetésből. A Rights eltávolítása megakadályozza, hogy a tervezetek állandó tulajdonosként legyenek előfizetésben.

## <a name="next-steps"></a>Következő lépések

- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).