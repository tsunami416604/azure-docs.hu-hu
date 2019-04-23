---
title: Terv üzembe helyezésének szakaszai
description: Az Azure tervezet szolgáltatások halad át a telepítés során szükséges lépések ismertetése.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796633"
---
# <a name="stages-of-a-blueprint-deployment"></a>Terv üzembe helyezésének szakaszai

Tervrajz megtörténik, amikor több csökken az Azure-tervek szolgáltatás által definiált a tervezet az erőforrásokat üzembe kívánja. Ez a cikk részletesen, mi minden lépésnek része.

Tervrajz telepítési váltja ki a tervezet hozzárendelése egy előfizetést vagy [a meglévő hozzárendelések módosítása](../how-to/update-existing-assignments.md). Tervezetek az üzembe helyezés során a következő magas szintű lépéseket hajtja végre:

> [!div class="checklist"]
> - Tervezetek ügyfélbérlői
> - A tervezet-hozzárendelés objektum létrehozása
> - Választható - tervek hoz létre **alapértelmezett** identitás
> - A felügyelt identitás tervezetösszetevők helyez üzembe.
> - Szolgáltatás tervezetet és **alapértelmezett** felügyelt identitás rights visszavonódnak.

## <a name="blueprints-granted-owner-rights"></a>Tervezetek ügyfélbérlői

Az Azure-tervek egyszerű szolgáltatás egy ügyfélbérlői a hozzárendelt előfizetés vagy az előfizetések. A megadott szerepkör lehetővé teszi, hogy a tervek létrehozása, és a későbbiekben visszavonja, a [alapértelmezett felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md).

A jogosultságokat automatikusan Ha a hozzárendelés a portálon keresztül történik. Azonban a hozzárendelés történik a REST API-n keresztül kell lennie a jogosultságok biztosítása egy külön API-val hívjuk. Az Azure tervezet AppID `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, azonban az egyszerű szolgáltatás bérlői függően változik. Használat [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) és REST-végpont [szolgáltatásnevek](/graph/api/resources/serviceprincipal) beolvasni az egyszerű szolgáltatás. Ezt követően adja meg az Azure-tervek a _tulajdonosa_ szerepkör keresztül a [portál](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI-vel](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md), vagy egy [Resource Manager-sablon](../../../role-based-access-control/role-assignments-template.md).

A tervrajzok szolgáltatás nem helyezheti közvetlenül üzembe az erőforrásokat.

## <a name="the-blueprint-assignment-object-is-created"></a>A tervezet-hozzárendelés objektum létrehozása

Egy felhasználó, csoport vagy szolgáltatásnév tervrajz rendel egy előfizetést. A hozzárendelési objektum létezik az előfizetés szintjén, ahol a tervezet hozzá volt rendelve. A központi telepítés által létrehozott erőforrásokat a környezetben a központi telepítés entitás nem történik.

A tervezet-hozzárendelést, milyen típusú létrehozásakor [identitás](../../../active-directory/managed-identities-azure-resources/overview.md) van kiválasztva. Az alapértelmezett érték egy **alapértelmezett** identitás. A **felhasználó által hozzárendelt** felügyelt identitás választható ki. Használata esetén egy **felhasználó által hozzárendelt** felügyelt identitást, azt kell meghatározni és jogosultságot kap a tervezet-hozzárendelés létrehozása előtt.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Választható - tervek hoz létre a rendszer által hozzárendelt felügyelt identitás

Amikor [alapértelmezett felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) van kiválasztva, hozzárendelés során tervek az eszközidentitást hoz létre, és a felügyelt identitást biztosít a [tulajdonosa](../../../role-based-access-control/built-in-roles.md#owner) szerepkör. Ha egy [frissítve van a meglévő hozzárendelést](../how-to/update-existing-assignments.md), tervek a korábban létrehozott felügyelt identitást használja.

A felügyelt identitás a tervezet-hozzárendelést kapcsolódó központi telepítése, illetve helyeznie az erőforrásokat a tervezet meghatározott szolgál. Ez a kialakítás elkerülhető a hozzárendelések véletlenül ütköznének egymással.
Ez a kialakítás is támogatja a [erőforrás zárolása](./resource-locking.md) a szolgáltatás szabályozásával a tervezet egyes üzembe helyezett erőforrások biztonságát.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A felügyelt identitás tervezetösszetevők helyez üzembe.

A felügyelt identitás majd elindítják a Resource Manager üzembe helyezések a tervezet a a definiált összetevőinak [alkalmazás-előkészítés rendelés](./sequencing-order.md). A sorrend módosítható, annak érdekében, hogy más összetevők függő összetevők a megfelelő sorrendben legyenek telepítve.

Központi telepítés által hozzáférési hibát gyakran a felügyelt identitás számára biztosított hozzáférési szintet eredménye. A tervrajzok szolgáltatás a biztonsági életciklusát felügyeli a **alapértelmezett** identitás. Azonban a felhasználó felelős a jogok és életciklusának kezelése egy **felhasználó által hozzárendelt** identitás.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Tervrajz szolgáltatás és a felügyelt identitás rendszer által hozzárendelt jogosultságok visszavonása

A központi telepítések követően tervezetek visszavonja jogait a **alapértelmezett** felügyelt identitás az előfizetésből. Ezt követően a tervek szolgáltatás visszavonása a az előfizetésből származó jogosultságok. Jogosultságok eltávolítása megakadályozza, hogy a tervek egy állandó tulajdonos egy adott előfizetés váljon.

## <a name="next-steps"></a>További lépések

- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).