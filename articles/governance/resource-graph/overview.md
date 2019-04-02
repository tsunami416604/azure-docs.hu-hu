---
title: Az Azure Resource Graph áttekintése
description: Ismerje meg, hogyan az Azure-erőforrás Graph szolgáltatás lehetővé teszi, hogy nagy mennyiségű erőforrást összetett lekérdezés.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 15cfdc87fafa25e9f37c63c8159289b25a547817
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802322"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Az Azure-erőforrás Graph szolgáltatás áttekintése

Az Azure Resource Graph egy szolgáltatás az Azure-ban, amelynek rendeltetése az Azure-erőforrások felügyeletének kiterjesztése. Ennek érdekében hatékony és nagy teljesítményű erőforrás-kezelést kínál, az összes előfizetésre és felügyeleti csoportra kiterjedő, nagyméretű lekérdezések lehetőségével, hogy Ön hatékonyan szabályozhassa környezetét. Ezek a lekérdezések a következő funkciókat biztosítják:

- Erőforrások lekérdezése az erőforrás-tulajdonságok alapján végzett összetett szűréssel, csoportosítással és rendezéssel.
- Erőforrások lépésenkénti kezelése a szabályozási követelmények alapján, és az így előálló kifejezés szabályzatdefinícióvá konvertálása.
- Szabályzatok alkalmazásából adódó következmények felmérése kiterjedt felhőkörnyezetben.

Ez a dokumentáció mindegyik funkciót részletesen tárgyalja.

> [!NOTE]
> Az Azure Resource Graphot használja az Azure Portal új, „Minden erőforrás” tallózására szolgáló felülete. Olyan ügyfelek támogatására tervezték, akiknek nagyméretű környezeteket kell felügyelniük.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hogyan egészíti ki a Resource Graph az Azure Resource Managert

Az Azure Resource Manager jelenleg egy korlátozott erőforrás-gyorsítótárba küld adatokat, amely elérhetővé tesz számos erőforrás-mezőt, így az Erőforrásnév, az Azonosító, a Típus, az Erőforráscsoport, az Előfizetések és a Hely mezőt is. Korábban különböző erőforrás-tulajdonságok használata esetén minden egyes erőforrás-szolgáltatót meg kellett hívni, és minden erőforrás tulajdonságrészleteit le kellett kérni.

Az Azure Resource Graph segítségével az erőforrás-szolgáltatók egyenkénti hívása nélkül is hozzáférhet az általuk visszaadott tulajdonságokhoz.

## <a name="the-query-language"></a>A lekérdezőnyelv

Most, hogy már jobban érti az Azure Resource Graph lényegét, ismerkedjünk meg közelebbről a lekérdezések összeállításával.

Fontos megérteni, hogy az Azure Resource Graph lekérdezési nyelv alapul a [Kusto-lekérdezés nyelvi](../../data-explorer/data-explorer-overview.md) Azure Data Explorer által használt.

Első lépésként olvassa el az Azure Resource Graphfal használható műveleteket és funkciókat ismertető, [a Resource Graph lekérdezőnyelve](./concepts/query-language.md) című cikket. Az erőforrások tallózását az [erőforrások kezeléséről](./concepts/explore-resources.md) szóló cikk írja le.

## <a name="permissions-in-azure-resource-graph"></a>Engedélyek az Azure Resource Graphban

A Resource Graph használatához megfelelő jogosultságokkal kell rendelkeznie a [szerepköralapú hozzáférés-vezérlésben](../../role-based-access-control/overview.md) (RBAC), és legalább olvasási jogosultsággal kell rendelkeznie a lekérdezni kívánt erőforrásokon. Ha nem rendelkezik legalább `read` engedélyekkel az Azure-objektumhoz vagy -objektumcsoporthoz, a rendszer nem ad vissza eredményeket.

## <a name="throttling"></a>Throttling

Erőforrás-grafikon a lekérdezések szabályozott a legjobb felhasználói élményt és válasz ideje biztosít minden ügyfél számára. Ha a szervezet által támogatni kívánt nagy méretű és gyakori lekérdezések a erőforrás Graph API használatával, használja az Erőforrás-grafikon oldalról "Visszajelzés" portálon. Mindenképpen adja meg az üzleti esetekhez, és jelölje be a "Microsoft e-mail üzeneteket küldhet Önnek a Visszajelzésével kapcsolatban" jelölőnégyzetet ahhoz, hogy a csapat Önnel a kapcsolatot.

## <a name="running-your-first-query"></a>Az első lekérdezés futtatása

Erőforrás-grafikon támogatja a .NET-hez készült Azure CLI-vel, az Azure PowerShell és az Azure SDK-t. A lekérdezés szerkezete ugyanaz az egyes nyelvekhez. Útmutató a Resource Graph engedélyezéséhez az [Azure CLI-ben](first-query-azurecli.md#add-the-resource-graph-extension) és az [Azure PowerShellben](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>További lépések

- Az első lekérdezés futtatása az [Azure CLI-vel](first-query-azurecli.md)
- Az első lekérdezés futtatása az [Azure PowerShell-lel](first-query-powershell.md)
- Indulás [Kezdő szintű lekérdezésekkel](./samples/starter.md)
- Az ismeretek elmélyítése [Haladó szintű lekérdezésekkel](./samples/advanced.md)