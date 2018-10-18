---
title: Az Azure Resource Graph áttekintése
description: Az Azure Resource Graph egy olyan Azure-szolgáltatás, amely lehetővé teszi az erőforrások nagy mennyiségben való komplex lekérdezését.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d68183f4d0a928ac72f3f73ea5225ad174820cb7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162100"
---
# <a name="what-is-azure-resource-graph"></a>Mi az Azure Resource Graph

Az Azure Resource Graph egy szolgáltatás az Azure-ban, amelynek rendeltetése az Azure-erőforrások felügyeletének kiterjesztése. Ennek érdekében hatékony és nagy teljesítményű erőforrás-kezelést kínál, az összes előfizetésre és felügyeleti csoportra kiterjedő, nagyméretű lekérdezések lehetőségével, hogy Ön hatékonyan szabályozhassa környezetét. Ezek a lekérdezések a következő képességeket kínálják:

- Erőforrások lekérdezése az erőforrás-tulajdonságok alapján végzett összetett szűréssel, csoportosítással és rendezéssel.
- Erőforrások lépésenkénti kezelése a szabályozási követelmények alapján, és az így előálló kifejezés szabályzatdefinícióvá konvertálása.
- Szabályzatok alkalmazásából adódó következmények felmérése kiterjedt felhőkörnyezetben.

Ez a dokumentáció mindegyik képességet részletesen tárgyalja.

> [!NOTE]
> Az Azure Resource Graphot használja az Azure Portal új, „Minden erőforrás” tallózására szolgáló felülete. Olyan ügyfelek támogatására tervezték, akiknek nagyméretű környezeteket kell felügyelniük.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hogyan egészíti ki a Resource Graph az Azure Resource Managert

Az Azure Resource Manager jelenleg egy korlátozott erőforrás-gyorsítótárba küld adatokat, amely elérhetővé tesz számos erőforrás-mezőt, így az Erőforrásnév, az Azonosító, a Típus, az Erőforráscsoport, az Előfizetések és a Hely mezőt is. Jelenleg ha több erőforrás-tulajdonsággal szeretne dolgozni, minden erőforrás-szolgáltatót külön kellene hívnia, hogy lekérje az egyes erőforrások tulajdonságainak adatait.

Az Azure Resource Graph segítségével az erőforrás-szolgáltatók egyenkénti hívása nélkül is hozzáférhet az általuk visszaadott tulajdonságokhoz.

## <a name="the-query-language"></a>A lekérdezőnyelv

Most, hogy már jobban érti az Azure Resource Graph lényegét, ismerkedjünk meg közelebbről a lekérdezések összeállításával.

Fontos tisztában lenni azzal, hogy az Azure Resource Graph lekérdezőnyelvének alapja az [Azure Data Explorer lekérdezési nyelv](../../data-explorer/data-explorer-overview.md).

Első lépésként olvassa el az Azure Resource Graphfal használható műveleteket és funkciókat ismertető, [a Resource Graph lekérdezőnyelve](./concepts/query-language.md) című cikket. Az erőforrások tallózását az [erőforrások kezeléséről](./concepts/explore-resources.md) szóló cikk írja le.

## <a name="permissions-in-azure-resource-graph"></a>Engedélyek az Azure Resource Graphban

A Resource Graph használatához [szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md) (RBAC) kell hitelesítenie magát, és legalább olvasási jogosultsággal kell rendelkeznie a lekérdezni kívánt erőforrásokon. Ha nem rendelkezik a `read` jogosultsággal a felügyeleti csoporton, előfizetésen, erőforráscsoporton vagy egyéni erőforráson, akkor a Resource Graph-lekérdezés nem adja vissza azt.

## <a name="running-your-first-query"></a>Az első lekérdezés futtatása

A Resource Graph az Azure CLI és az Azure PowerShell használatát is támogatja. A lekérdezési összetevő szerkezete a használt nyelvtől függetlenül mindig ugyanolyan. Az Azure Resource Graph támogatása alapértelmezés szerint egyelőre egy SDK-ban sem érhető el, ezért a szükséges parancsokat csak egy betöltött kiterjesztés vagy modul nyújthatja.
Útmutató a Resource Graph engedélyezéséhez az [Azure CLI-ben](first-query-azurecli.md#add-the-resource-graph-extension) és az [Azure PowerShellben](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>További lépések

- Az első lekérdezés futtatása az [Azure CLI-vel](first-query-azurecli.md)
- Az első lekérdezés futtatása az [Azure PowerShell-lel](first-query-powershell.md)
- Indulás [Kezdő szintű lekérdezésekkel](./samples/starter.md)
- Az ismeretek elmélyítése [Haladó szintű lekérdezésekkel](./samples/advanced.md)