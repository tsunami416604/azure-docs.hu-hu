---
title: Azure-tervek használatával kapcsolatos hibák elhárítása
description: Ismerje meg, hogyan hoz létre, és tervek hibáinak elhárítása
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982290"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Azure-tervek használatával kapcsolatos hibák elhárítása

Hibák létrehozása vagy hozzárendelése tervezetek során merülhetnek fel. Ez a cikk különféle előforduló hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Keresés, a hiba részletei

Sok hiba lesz a tervezet hozzárendelése egy hatókör eredményét. Hozzárendelés sikertelen lesz, amikor a tervezet a sikertelen üzembe helyezés részletesen ismerteti. Ez az információ a probléma jelzi, hogy lehet meghatározni, és a későbbi központi telepítés sikeres lesz.

1. Indítsa el az Azure-tervek szolgáltatás az Azure Portalon kattintson a **minden szolgáltatás** keresése és kiválasztása **házirend** a bal oldali panelen. Az a **házirend** lapon **tervezetek**.

1. Válassza ki **hozzárendelt tervezetek** a lap bal oldalon, a Keresés mezőbe a tervezet-hozzárendelések keresése a sikertelen hozzárendelés szűréséhez használja. A tábla-hozzárendelések alapján is rendezheti a **kiépítési állapot** oszlopban tekintheti meg az összes sikertelen hozzárendelések csoportosítva.

1. Kattintson a tervezet-a a _sikertelen_ állapotát, vagy kattintson a jobb gombbal, és válassza **hozzárendelés részleteinek megtekintése**.

1. Felső részén a tervezet hozzárendelése oldal egy piros sávban, figyelmeztetés, hogy a hozzárendelés sikertelen volt. Kattintson bárhová a szalagcímre kattintva további részleteket jeleníthet meg.

A hiba oka lehet egy összetevő szerepelni fog a tervezet és nem a teljes tervezet szokás. Például ha a tervezet tartalmaz egy összetevő hozhat létre egy Key Vaultot, de a Key Vault létrehozási miatt nem lehetséges az Azure Policy, a teljes hozzárendelés sikertelen lesz.

## <a name="general-errors"></a>Általános hiba

### <a name="policy-violation"></a>Forgatókönyv: Szabályzat megsértése

#### <a name="issue"></a>Probléma

A sablon üzembe helyezéséhez szabályzat megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok

Egy házirend ütközést idézhet elő a központi telepítés a számos oka:

- A létrehozott erőforrás korlátozza a házirend (általában a Termékváltozat vagy a hely korlátozások)
- Az üzembe helyezés (közös címkékkel rendelkező) szabályzat által konfigurált mezők beállítása

#### <a name="resolution"></a>Megoldás:

A tervezet ütközik a hiba adatait a felsorolt házirendek nem kell módosítani. Ha ez nem lehetséges, az egy másik lehetőség, hogy a szabályzat-hozzárendelés megváltozott, így a tervezet már nem ütközik a szabályzat hatóköre.

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

- Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
- Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
- Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.