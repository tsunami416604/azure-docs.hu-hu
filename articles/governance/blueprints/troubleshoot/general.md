---
title: Gyakori hibák elhárítása
description: Ismerje meg, létrehozásához, hozzárendeléséhez és tervek eltávolításával kapcsolatos problémák elhárítása.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 42fdd6645a7a0e7cd9a2f0a7bc969e8eee62758c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789617"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Azure-tervek használatával kapcsolatos hibák elhárítása

Előfordulhat, hogy hibákba ütközik létrehozásakor vagy tervezetek hozzárendelése. Ez a cikk különféle előforduló hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Keresés, a hiba részletei

Sok hiba lesz a tervezet hozzárendelése egy hatókör eredményét. A helyhozzárendelés meghiúsul, ha a megoldás lehetővé teszi az a sikertelen üzembe helyezés részleteit. Ez az információ azt jelzi, hogy a probléma, hogy lehet meghatározni, és a következő üzembe helyezését.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki **hozzárendelt tervezetek** a lap bal oldalon, a Keresés mezőbe a tervezet-hozzárendelések keresése a sikertelen hozzárendelés szűréséhez használja. A tábla-hozzárendelések alapján is rendezheti a **kiépítési állapot** oszlopban tekintheti meg az összes sikertelen hozzárendelések csoportosítva.

1. Kattintson a tervezet-a a _sikertelen_ állapotát, vagy kattintson a jobb gombbal, és válassza **hozzárendelés részleteinek megtekintése**.

1. Egy figyelmeztetés, hogy a hozzárendelés sikertelen, piros sávban van a tervezet-hozzárendelés lap tetején. Kattintson bárhová a szalagcímre kattintva további részleteket jeleníthet meg.

A hiba oka lehet egy összetevő, és nem a teljes tervezet szokás. Ha egy összetevő egy Key Vaultot hoz létre, és az Azure Policy megakadályozza, hogy a Key Vault létrehozási, a teljes hozzárendelés sikertelen lesz.

## <a name="general-errors"></a>Általános hiba

### <a name="policy-violation"></a>Forgatókönyv: Szabályzat megsértése

#### <a name="issue"></a>Probléma

A sablon üzembe helyezéséhez szabályzat megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok

Egy házirend ütközést idézhet elő a központi telepítés a számos oka:

- A létrehozott erőforrás korlátozza a házirend (általában a Termékváltozat vagy a hely korlátozások)
- Az üzembe helyezés (közös címkékkel rendelkező) szabályzat által konfigurált mezők beállítása

#### <a name="resolution"></a>Megoldás:

Módosítsa a tervezet, így nem ütközik az a hiba részleteit a szabályzatokat. Ha ez a módosítás nem lehetséges, a másik lehetséges, hogy a szabályzat-hozzárendelés megváltozott, így a tervezet már nem ütközik a szabályzat hatóköre.

### <a name="escape-function-parameter"></a>Forgatókönyv: Tervrajz paraméter függvény

#### <a name="issue"></a>Probléma

Tervezet paramétereinek megadása, amelyek a functions dolgozza fel előbb átadódik az összetevők.

#### <a name="cause"></a>Ok

Tervezet paraméter átadásával, amely függvényt tartalmaz, például `[resourceGroup().tags.myTag]`, az összetevőt a függvény helyett a dinamikus függvény összetevő történő beállítása feldolgozott eredményét eredményez.

#### <a name="resolution"></a>Megoldás:

Egy alkalmazáson keresztül működjön paraméterként átadni a teljes karakterláncot escape `[` úgy, hogy néz ki a tervezetparaméter `[[resourceGroup().tags.myTag]`. Az escape-karaktert hatására tervezetek kezelni az érték egy karakterláncot a a tervezet feldolgozásakor. Tervezetek majd helyezi a függvény az összetevőben, lehetővé téve, hogy a várt módon dinamikus. További információkért lásd: [sablon fájlstruktúra – szintaxis](../../../azure-resource-manager/resource-group-authoring-templates.md#syntax).

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

- Válaszokat kaphat az Azure-szakértőktől [Azure fórumok](https://azure.microsoft.com/support/forums/).
- Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
- Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.