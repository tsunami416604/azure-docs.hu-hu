---
title: Az Azure Resource Graph áttekintése
description: Ismerje meg, hogyan az Azure-erőforrás Graph szolgáltatás lehetővé teszi, hogy nagy mennyiségű erőforrást összetett lekérdezés.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/30/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d76a5b32403bd14f18181580f891925130808922
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622796"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Az Azure-erőforrás Graph szolgáltatás áttekintése

Az Azure Resource Graph egy szolgáltatás az Azure-ban, amelynek rendeltetése az Azure-erőforrások felügyeletének kiterjesztése. Ennek érdekében hatékony és nagy teljesítményű erőforrás-kezelést kínál, az összes előfizetésre és felügyeleti csoportra kiterjedő, nagyméretű lekérdezések lehetőségével, hogy Ön hatékonyan szabályozhassa környezetét. Ezek a lekérdezések a következő funkciókat biztosítják:

- Erőforrások lekérdezése az erőforrás-tulajdonságok alapján végzett összetett szűréssel, csoportosítással és rendezéssel.
- Erőforrások lépésenkénti kezelése a szabályozási követelmények alapján, és az így előálló kifejezés szabályzatdefinícióvá konvertálása.
- Szabályzatok alkalmazásából adódó következmények felmérése kiterjedt felhőkörnyezetben.
- Lehetővé teszi [erőforrás-tulajdonságok módosításait részletezik](./how-to/get-resource-changes.md) (előzetes verzió).

Ez a dokumentáció mindegyik funkciót részletesen tárgyalja.

> [!NOTE]
> Az Azure portal "Minden erőforrás" tapasztalható új tallózása és az Azure Policy által használt Azure Erőforrás-grafikon [módosítási előzmények](../policy/how-to/determine-non-compliance.md#change-history-preview).
> _a diff vizuális_. Úgy van kialakítva, hogy az ügyfelek kezelése nagyméretű környezetekben.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hogyan egészíti ki a Resource Graph az Azure Resource Managert

Az Azure Resource Manager jelenleg egy korlátozott erőforrás-gyorsítótárba küld adatokat, amely elérhetővé tesz számos erőforrás-mezőt, így az Erőforrásnév, az Azonosító, a Típus, az Erőforráscsoport, az Előfizetések és a Hely mezőt is. Korábban különböző erőforrás-tulajdonságok használata esetén minden egyes erőforrás-szolgáltatót meg kellett hívni, és minden erőforrás tulajdonságrészleteit le kellett kérni.

Az Azure Resource Graph segítségével az erőforrás-szolgáltatók egyenkénti hívása nélkül is hozzáférhet az általuk visszaadott tulajdonságokhoz. Támogatott erőforrástípusait listáját, keressen egy **Igen** a a [erőforrások teljes üzemmód telepítéseit](../../azure-resource-manager/complete-mode-deletion.md) tábla.

Az Azure Erőforrás-grafikon a következőket teheti:

- Hozzáférés a anélkül, hogy az egyes hívásokat mindegyik erőforrás-szolgáltató erőforrás-szolgáltató által visszaadott tulajdonságait.
- Az elmúlt 14 napban tulajdonságok változott az erőforrás végzett módosítási előzmények megtekintése és mikor. (előzetes verzió)

## <a name="the-query-language"></a>A lekérdezőnyelv

Most, hogy már jobban érti az Azure Resource Graph lényegét, ismerkedjünk meg közelebbről a lekérdezések összeállításával.

Fontos megérteni, hogy az Azure Resource Graph lekérdezési nyelv alapul a [Kusto-lekérdezés nyelvi](../../data-explorer/data-explorer-overview.md) Azure Data Explorer által használt.

Első lépésként olvassa el az Azure Resource Graphfal használható műveleteket és funkciókat ismertető, [a Resource Graph lekérdezőnyelve](./concepts/query-language.md) című cikket.
Az erőforrások tallózását az [erőforrások kezeléséről](./concepts/explore-resources.md) szóló cikk írja le.

## <a name="permissions-in-azure-resource-graph"></a>Engedélyek az Azure Resource Graphban

A Resource Graph használatához megfelelő jogosultságokkal kell rendelkeznie a [szerepköralapú hozzáférés-vezérlésben](../../role-based-access-control/overview.md) (RBAC), és legalább olvasási jogosultsággal kell rendelkeznie a lekérdezni kívánt erőforrásokon. Ha nem rendelkezik legalább `read` engedélyekkel az Azure-objektumhoz vagy -objektumcsoporthoz, a rendszer nem ad vissza eredményeket.

> [!NOTE]
> Erőforrás-grafikon egy egyszerű elérhető előfizetések használja a bejelentkezés során. Aktív munkamenet során hozzáadott új előfizetés erőforrások megtekintéséhez az egyszerű frissítenie kell a környezetben. Ez a művelet automatikusan megtörténik, amikor kijelentkezik, majd újból.

## <a name="throttling"></a>Throttling

Erőforrás-grafikon a lekérdezések szabályozott a legjobb felhasználói élményt és válasz ideje biztosít minden ügyfél számára. Ha a szervezet által támogatni kívánt nagy méretű és gyakori lekérdezések a erőforrás Graph API használatával, használja az Erőforrás-grafikon oldalról "Visszajelzés" portálon. Mindenképpen adja meg az üzleti esetekhez, és jelölje be a "Microsoft e-mail üzeneteket küldhet Önnek a Visszajelzésével kapcsolatban" jelölőnégyzetet ahhoz, hogy a csapat Önnel a kapcsolatot.

## <a name="running-your-first-query"></a>Az első lekérdezés futtatása

Erőforrás-grafikon támogatja a .NET-hez készült Azure CLI-vel, az Azure PowerShell és az Azure SDK-t. A lekérdezés szerkezete ugyanaz az egyes nyelvekhez. Útmutató a Resource Graph engedélyezéséhez az [Azure CLI-ben](first-query-azurecli.md#add-the-resource-graph-extension) és az [Azure PowerShellben](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>További lépések

- Az első lekérdezés futtatása [Azure CLI-vel](first-query-azurecli.md).
- Az első lekérdezés futtatása [Azure PowerShell-lel](first-query-powershell.md).
- Kezdje [alapszintű lekérdezéseket](./samples/starter.md).
- A elmélyítse [speciális lekérdezések](./samples/advanced.md).