---
title: Gyakori hibák elhárítása
description: Megtudhatja, hogyan lehet elhárítani a tervrajzok létrehozásával, hozzárendelésével és eltávolításával kapcsolatos problémákat.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 14e957986df7a114b8c865ee82e2ac447683dc2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257175"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Az Azure-tervezetekkel kapcsolatos hibák elhárítása

A tervrajzok létrehozásakor vagy kiosztásakor hibákat lehet futtatni. Ez a cikk az esetlegesen előforduló különböző hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

Számos hiba lesz a terv egy hatókörhöz való hozzárendelésének eredménye. Ha egy hozzárendelés meghiúsul, a terv részletesen ismerteti a sikertelen telepítés részleteit. Ez az információ azt jelzi, hogy a probléma kijavítható, és a következő telepítés sikeres lesz.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza ki a **kijelölt tervrajzokat** a bal oldali oldalról, és a keresőmező segítségével szűrje a terv-hozzárendeléseket a sikertelen hozzárendelés megkereséséhez. A hozzárendelések tábláját a **kiépítési állapot** oszlopban is rendezheti, ha az összes sikertelen hozzárendelést együtt szeretné látni.

1. Kattintson a _nem_ megfelelő állapotú tervezetre, vagy kattintson a jobb gombbal, és válassza a **hozzárendelés részleteinek megtekintése**lehetőséget.

1. Egy piros szalagcím figyelmeztetése, hogy a hozzárendelés meghiúsult a terv-hozzárendelés oldal tetején. További részletekért kattintson a szalagcím tetszőleges pontjára.

Gyakori, hogy a hiba oka egy összetevő, nem pedig a terv egésze. Ha egy összetevő létrehoz egy Key Vault, és Azure Policy megakadályozza a Key Vault létrehozását, a teljes hozzárendelés sikertelen lesz.

## <a name="general-errors"></a>Általános hibák

### <a name="policy-violation"></a>Forgatókönyv Szabályzat megsértése

#### <a name="issue"></a>Probléma

A sablon központi telepítése a szabályzat megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok

Egy házirend több okból is ütközhet az üzembe helyezéssel:

- A létrehozandó erőforrást házirend korlátozza (általában SKU vagy Location korlátozásokkal).
- Az üzemelő példány olyan mezőket állít be, amelyeket a szabályzat konfigurál (közös címkékkel)

#### <a name="resolution"></a>Megoldás:

Módosítsa a tervet úgy, hogy az ne ütközzön a hiba részleteiben szereplő szabályzatokkal. Ha ez a változás nem lehetséges, egy másik lehetőség, hogy a házirend-hozzárendelés hatóköre megváltozott, így a terv már nem ütközik a szabályzattal.

### <a name="escape-function-parameter"></a>Forgatókönyv A Blueprint paraméter egy függvény

#### <a name="issue"></a>Probléma

A függvények feldolgozására szolgáló tervrajzi paramétereket a rendszer az összetevőknek való továbbítás előtt dolgozza fel.

#### <a name="cause"></a>Ok

Egy függvényt (például `[resourceGroup().tags.myTag]`) használó tervrajzi paraméter átadása az összetevőnek a dinamikus függvény helyett a tárgyban beállított függvény feldolgozott eredményét eredményezi.

#### <a name="resolution"></a>Megoldás:

Egy függvény paraméterként való átadásához escape a teljes karakterláncot `[` oly módon, hogy a terv paramétere `[[resourceGroup().tags.myTag]`hasonlítson. Az escape-karakter olyan tervrajzokat okoz, amelyek az értéket karakterláncként kezelik a terv feldolgozásakor. A tervrajzok ezután elhelyezik a függvényt az adott összetevőn, ami lehetővé teszi, hogy a várt módon dinamikus legyen. További információ: [szintaxis és kifejezések Azure Resource Manager sablonokban](../../../azure-resource-manager/template-expressions.md).

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
- Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.