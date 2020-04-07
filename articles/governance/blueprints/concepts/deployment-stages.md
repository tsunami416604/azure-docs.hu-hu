---
title: Terv üzembe helyezésének szakaszai
description: Ismerje meg a biztonsági és műtermékkel kapcsolatos lépéseket az Azure Blueprints szolgáltatások megy keresztül, miközben egy tervezet-hozzárendelés létrehozása.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 61d19c84cd659b9df3a272c5c2743944e51df06e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677313"
---
# <a name="stages-of-a-blueprint-deployment"></a>Terv üzembe helyezésének szakaszai

Amikor egy tervtervezet üzembe helyezése, az Azure Blueprints szolgáltatás műveletek sorozatát a tervben meghatározott erőforrások üzembe helyezéséhez. Ez a cikk részletesen ismerteti, hogy az egyes lépéseket milyen részletekkel jár.

A blueprint központi telepítés takarásban, ha egy előfizetéshez rendel egy tervezetet, vagy [frissít egy meglévő hozzárendelést.](../how-to/update-existing-assignments.md) Az üzembe helyezés során az Azure Blueprints a következő magas szintű lépéseket teszi:

> [!div class="checklist"]
> - Az Azure Blueprints tulajdonosi jogokat kapott
> - Létrejön a tervezet-hozzárendelési objektum
> - Nem kötelező – Az Azure Blueprints **rendszerhez rendelt** felügyelt identitást hoz létre
> - A felügyelt identitás telepíti a tervezetösszetevőket
> - Az Azure Blueprints szolgáltatás és **a rendszer által hozzárendelt** felügyelt identitásjogok visszavonásra kerülnek

## <a name="azure-blueprints-granted-owner-rights"></a>Az Azure Blueprints tulajdonosi jogokat kapott

Az Azure Blueprints szolgáltatás névszerint a hozzárendelt előfizetés vagy előfizetések tulajdonosi jogokat kapnak, ha egy [rendszer által hozzárendelt felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) felügyelt identitás t használ. A megadott szerepkör lehetővé teszi az Azure Blueprints számára, hogy létrehozza, majd később visszavonja a **rendszer által hozzárendelt** felügyelt identitást. Ha egy **felhasználó által hozzárendelt** felügyelt identitás használata, az Azure Blueprints egyszerű szolgáltatás nem kap, és nem kell tulajdonosi jogokat az előfizetéshez.

A jogosultságok automatikusan megadhatók, ha a hozzárendelés a portálon keresztül történik. Ha azonban a hozzárendelés a REST API-n keresztül történik, a jogok megadása külön API-hívással történik. Az Azure Blueprints AppId van, `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`de a szolgáltatásnév bérlőnként változik. Használja [az Azure Active Directory Graph API-t](../../../active-directory/develop/active-directory-graph-api.md) és a REST-végponti [szolgáltatáscéljait](/graph/api/resources/serviceprincipal) a szolgáltatásegyszerű lehívásához. Ezután adja meg az Azure Blueprints a _tulajdonosi_ szerepkört a [portálon,](../../../role-based-access-control/role-assignments-portal.md)az [Azure CLI-n,](../../../role-based-access-control/role-assignments-cli.md)az [Azure PowerShellen,](../../../role-based-access-control/role-assignments-powershell.md)a [REST API-n](../../../role-based-access-control/role-assignments-rest.md)vagy egy [Resource Manager-sablonon](../../../role-based-access-control/role-assignments-template.md)keresztül.

Az Azure Blueprints szolgáltatás nem közvetlenül telepíti az erőforrásokat.

## <a name="the-blueprint-assignment-object-is-created"></a>Létrejön a tervezet-hozzárendelési objektum

Egy felhasználó, csoport vagy egyszerű szolgáltatás egy előfizetéshez rendel egy tervrajzot. A hozzárendelés-objektum azelőfizetés i. A központi telepítés által létrehozott erőforrások nem a központi üzembe helyező entitás környezetében történik.

A tervezethozzárendelés létrehozása során a [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusa van kiválasztva. Az alapértelmezett beállítás egy **rendszeráltal hozzárendelt** felügyelt identitás. Felhasználó **által hozzárendelt** felügyelt identitás választható. **Felhasználó által hozzárendelt** felügyelt identitás használataesetén meg kell határozni, és engedélyeket kell adni a tervezet-hozzárendelés létrehozása előtt. Mind a [tulajdonos,](../../../role-based-access-control/built-in-roles.md#owner) mind a [Blueprint](../../../role-based-access-control/built-in-roles.md#blueprint-operator) `blueprintAssignment/write` Operator beépített szerepkörök rendelkeznek a szükséges engedéllyel egy olyan hozzárendelés létrehozásához, amely **egy felhasználó által hozzárendelt** felügyelt identitást használ.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Nem kötelező – Az Azure Blueprints rendszerhez rendelt felügyelt identitást hoz létre

Ha [a rendszer által hozzárendelt felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) kiválasztása során a hozzárendelés, az Azure Blueprints létrehozza az identitást, és megadja a felügyelt identitás a [tulajdonosi](../../../role-based-access-control/built-in-roles.md#owner) szerepkör. Ha egy [meglévő hozzárendelés továbbfejlesztett, Az](../how-to/update-existing-assignments.md)Azure Blueprints a korábban létrehozott felügyelt identitást használja.

A tervezet-hozzárendeléshez kapcsolódó felügyelt identitás a tervben meghatározott erőforrások üzembe helyezésére vagy újratelepítésére szolgál. Ezzel a kialakítással elkerülhetők, hogy a hozzárendelések véletlenül megzavarják egymást.
Ez a kialakítás is támogatja az [erőforrás-zárolási](./resource-locking.md) funkció az egyes üzembe helyezett erőforrások biztonságának szabályozásával a tervezetből.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A felügyelt identitás telepíti a tervezetösszetevőket

A felügyelt identitás ezután elindítja az Erőforrás-kezelő üzembe helyezését a tervezetben lévő összetevők a megadott [sorrendben.](./sequencing-order.md) A sorrend módosítható annak érdekében, hogy a más összetevőktől függő összetevők a megfelelő sorrendben legyenek telepítve.

A központi telepítés hozzáférési hibája gyakran a felügyelt identitáshoz biztosított hozzáférési szint eredménye. Az Azure Blueprints szolgáltatás kezeli a **rendszer által hozzárendelt** felügyelt identitás biztonsági életciklusát. A felhasználó azonban felelős a **felhasználó által hozzárendelt** felügyelt identitás jogainak és életciklusának kezeléséért.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>A blueprint szolgáltatás és a rendszer által hozzárendelt felügyelt identitásjogok visszavonásra kerülnek

Miután a központi telepítések befejeződtek, az Azure Blueprints visszavonja a **rendszer által hozzárendelt** felügyelt identitás jogait az előfizetésből. Ezután az Azure Blueprints szolgáltatás visszavonja a jogait az előfizetésből. A jogok eltávolítása megakadályozza, hogy az Azure Blueprints egy előfizetés állandó tulajdonosává váljon.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../how-to/update-existing-assignments.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.
