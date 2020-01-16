---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogyan lehet elhárítani a tervezetek létrehozásával, hozzárendelésével és eltávolításával kapcsolatos problémákat, például a szabályzat megsértését és a terv paramétereinek funkcióit.
ms.date: 11/22/2019
ms.topic: troubleshooting
ms.openlocfilehash: 5b8a20b0757934bbd356ab037a22521a248a7eb2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982490"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Az Azure-tervezetekkel kapcsolatos hibák elhárítása

A tervrajzok létrehozásakor vagy kiosztásakor hibákat lehet futtatni. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

Számos hiba lesz a terv egy hatókörhöz való hozzárendelésének eredménye. Ha egy hozzárendelés meghiúsul, a terv részletesen ismerteti a sikertelen telepítés részleteit. Ez az információ azt jelzi, hogy a probléma kijavítható, és a következő telepítés sikeres lesz.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza ki a **kijelölt tervrajzokat** a bal oldali oldalról, és a keresőmező segítségével szűrje a terv-hozzárendeléseket a sikertelen hozzárendelés megkereséséhez. A hozzárendelések tábláját a **kiépítési állapot** oszlopban is rendezheti, ha az összes sikertelen hozzárendelést együtt szeretné látni.

1. Kattintson a _nem_ megfelelő állapotú tervezetre, vagy kattintson a jobb gombbal, és válassza a **hozzárendelés részleteinek megtekintése**lehetőséget.

1. Egy piros szalagcím figyelmeztetése, hogy a hozzárendelés meghiúsult a terv-hozzárendelés oldal tetején. További részletekért kattintson a szalagcím tetszőleges pontjára.

Gyakori, hogy a hiba oka egy összetevő, nem pedig a terv egésze. Ha egy összetevő létrehoz egy Key Vault, és Azure Policy megakadályozza a Key Vault létrehozását, a teljes hozzárendelés sikertelen lesz.

## <a name="general-errors"></a>Általános hibák

### <a name="policy-violation"></a>Forgatókönyv: szabályzat megsértése

#### <a name="issue"></a>Probléma

A sablon központi telepítése a szabályzat megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok

Egy házirend több okból is ütközhet az üzembe helyezéssel:

- A létrehozandó erőforrást házirend korlátozza (általában SKU vagy Location korlátozásokkal).
- Az üzemelő példány olyan mezőket állít be, amelyeket a szabályzat konfigurál (közös címkékkel)

#### <a name="resolution"></a>Felbontás

Módosítsa a tervet úgy, hogy az ne ütközzön a hiba részleteiben szereplő szabályzatokkal. Ha ez a változás nem lehetséges, egy másik lehetőség, hogy a házirend-hozzárendelés hatóköre megváltozott, így a terv már nem ütközik a szabályzattal.

### <a name="escape-function-parameter"></a>Forgatókönyv: a Blueprint paraméter egy függvény

#### <a name="issue"></a>Probléma

A függvények feldolgozására szolgáló tervrajzi paramétereket a rendszer az összetevőknek való továbbítás előtt dolgozza fel.

#### <a name="cause"></a>Ok

Egy függvényt (például `[resourceGroup().tags.myTag]`t) használó tervrajzi paraméter átadása egy összetevőnek, amely a dinamikus függvény helyett a tárgyon beállított függvény feldolgozott eredményét eredményezi.

#### <a name="resolution"></a>Felbontás

Ha paraméterként át szeretne adni egy függvényt, a teljes karakterláncot a `[` úgy kell elmenekülnie, hogy a terv paramétere `[[resourceGroup().tags.myTag]`. Az escape-karakter olyan tervrajzokat okoz, amelyek az értéket karakterláncként kezelik a terv feldolgozásakor. A tervrajzok ezután elhelyezik a függvényt az adott összetevőn, ami lehetővé teszi, hogy a várt módon dinamikus legyen. További információ: [szintaxis és kifejezések Azure Resource Manager sablonokban](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
- Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.