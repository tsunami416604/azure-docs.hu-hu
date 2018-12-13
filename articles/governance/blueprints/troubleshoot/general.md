---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogyan hoz létre, és tervek hibáinak elhárítása
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 40668fed2fcc2a04e39fa3a4d7e8e8923c75ae05
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315526"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Azure-tervek használatával kapcsolatos hibák elhárítása

Előfordulhat, hogy hibákba ütközik létrehozásakor vagy tervezetek hozzárendelése. Ez a cikk különféle előforduló hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Keresés, a hiba részletei

Sok hiba lesz a tervezet hozzárendelése egy hatókör eredményét. A helyhozzárendelés meghiúsul, ha a megoldás lehetővé teszi az a sikertelen üzembe helyezés részleteit. Ez az információ azt jelzi, hogy a probléma, hogy lehet meghatározni, és a következő üzembe helyezését.

1. Kattintson a **Minden szolgáltatás** elemre, és keresse meg, majd válassza ki a **Szabályzat** elemet a bal oldali panelen. Kattintson a **Szabályzat** oldal **Tervek** elemére.

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

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

- Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
- Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
- Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.