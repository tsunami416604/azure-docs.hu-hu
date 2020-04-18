---
title: Azure-szabályzat bővítmény a Visual Studio-kódhoz
description: Megtudhatja, hogy miként keresheti meg az Azure Policy bővítményt a Visual Studio-kódhoz az Erőforrás-kezelő aliasai kikereséséhez.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 5e31af652f2746adbf0eda386bdb178c752f5f84
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641026"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Az Azure Policy bővítmény használata a Visual Studio-kódhoz

> Az Azure Policy bővítmény **0.0.21-es** és újabb verziójára vonatkozik

Megtudhatja, hogy miként használhatja az Azure Policy bővítményt a Visual Studio-kódhoz [aliasok](../concepts/definition-structure.md#aliases) felkeresése, valamint az erőforrások és szabályzatok áttekintéséhez. Először ismertetjük, hogyan telepítheti az Azure Policy bővítményt a Visual Studio-kódban. Aztán végigmegyünk, hogyan nézzünk utána az álneveknek.

A Visual Studio-kód Azure Policy bővítménye minden olyan platformra telepíthető, amelyet a Visual Studio-kód támogat. Ez a támogatás magában foglalja a Windows, Linux és macOS operációs rendszert.

> [!NOTE]
> A Visual Studio-kód Azure Policy bővítményében megtekintett szabályzatok helyi módosításai nincsenek szinkronizálva az Azure-ral.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következő elemek szükségesek:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
- [Visual Studio kód](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Az Azure Policy bővítmény telepítése

Miután teljesítette az előfeltételeket, az alábbi lépésekkel telepítheti a Visual Studio-kód Azure Policy bővítményét:

1. Nyissa meg a Visual Studio Code-ot.

1. A menüsorban nyissa meg a**Bővítmények** **megtekintése** > lapot.

1. A keresőmezőbe írja be az **Azure Policy (Azure Policy) kifejezést.**

1. Válassza az **Azure Policy elemet** a keresési eredmények között, majd kattintson a Telepítés **gombra.**

1. Szükség esetén válassza **az Újratöltés** lehetőséget.

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

Egy nemzeti felhőfelhasználó esetén kövesse az alábbi lépéseket az Azure-környezet beállításához először:

1. Válassza **a Fájl\Beállítások\Beállítások lehetőséget.**

1. Keresés a következő karakterláncon: _Azure: Cloud_

1. Válassza ki a nemzet felhőjét a listából:

   ![Alapértelmezett Azure-felhőbeli bejelentkezés beállítása a Visual Studio-kódhoz](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Csatlakozás Azure-fiókhoz

Az erőforrások és a keresése aliasok kiértékeléséhez csatlakoznia kell az Azure-fiókjához. A Visual Studio-kódból az alábbi lépésekkel csatlakozhat az Azure-hoz:

1. Jelentkezzen be az Azure-ba az Azure-szabályzat bővítményből vagy a parancspalettából.

   - Azure-szabályzat bővítmény

     Az Azure Policy bővítményben válassza **a Bejelentkezés az Azure-ba**lehetőséget.

     ![Az Azure-felhőbejelentkezés a Visual Studio-kódszolgáltatáshoz az Azure Policy bővítményből](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Parancspaletta

     A menüsorban nyissa meg a**Parancspaletta** **megtekintése** > lapot, és írja be az **Azure: Bejelentkezés**.

     ![Az Azure-felhő bejelentkezés a Visual Studio-kódhoz a parancspalettáról](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Kövesse a bejelentkezési utasításokat az Azure-ba való bejelentkezéshez. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio-kód ablak alján található állapotsoron.

## <a name="select-subscriptions"></a>Előfizetések kiválasztása

Amikor először jelentkezik be, csak az azure-szabályzat bővítmény csak az alapértelmezett előfizetési erőforrásokat és szabályzatokat tölti be. Ha előfizetéseket szeretne hozzáadni vagy eltávolítani az erőforrások és szabályzatok megjelenítéséből, kövesse az alábbi lépéseket:

1. Indítsa el az előfizetési parancsot a Parancspalettáról vagy az ablaklábról.

   - Parancspaletta: 

     A menüsorban nyissa meg a**Parancspaletta** **megtekintése** > lehetőséget, és írja be az **Azure: Előfizetések lehetőséget.**

   - Ablakláb

     A képernyő alján lévő ablaklábon válassza ki az **Azure-nak megfelelő szegmenst: \<a fiókját.\>**

1. A szűrőmező segítségével gyorsan megtalálhatja az előfizetéseket név szerint. Ezután ellenőrizze vagy távolítsa el az ellenőrzést az egyes előfizetések az Azure Policy bővítmény által megjelenített előfizetések beállításához. Amikor befejezte az előfizetések megjelenítését, kattintson az **OK gombra.**

## <a name="search-for-and-view-resources"></a>Erőforrások keresése és megtekintése

Az Azure Policy bővítmény az erőforrásokat az erőforrás-szolgáltató és az **erőforrások** ablaktábla erőforráscsoport szerint sorolja fel a kiválasztott előfizetésekben. A treeview a következő erőforráscsoportokat tartalmazza a kiválasztott előfizetésen belül vagy az előfizetés szintjén:

- **Erőforrás-szolgáltatók**
  - Minden regisztrált erőforrás-szolgáltató olyan erőforrásokkal és kapcsolódó gyermekerőforrásokkal rendelkezik, amelyek házirendaliasokkal rendelkeznek
- **Erőforráscsoportok**
  - Az összes erőforrás az erőforráscsoport szerint, amelyben vannak

Alapértelmezés szerint a bővítmény szűri az "Erőforrás-szolgáltató" részt a meglévő erőforrások és erőforrások, amelyek házirend aliasok. Módosítsa ezt a viselkedést a **Beállítások** > **bővítmények** > **Azure-szabályzatban,** hogy az összes erőforrás-szolgáltatót szűrés nélkül láthassa.

Az egyetlen előfizetésben több száz vagy több ezer erőforrással rendelkező ügyfelek kereshető módon kereshetik meg az erőforrásaikat. Az Azure Policy bővítmény lehetővé teszi egy adott erőforrás keresését a következő lépésekkel:

1. Indítsa el a keresési felületet az Azure Policy bővítményből vagy a parancspalettából.

   - Azure-szabályzat bővítmény

     Az Azure Policy bővítményben mutasson az **Erőforrások** panelre, és válassza a három pontot, majd válassza az **Erőforrások keresése**lehetőséget.

   - Parancspaletta:

     A menüsorban válassza a **Parancspaletta** **megtekintése** > lapot, és írja be az **Erőforrások: Keresés az erőforrások mezőbe.**

1. Ha egynél több előfizetés van kiválasztva a megjelenítéshez, a szűrő segítségével válassza ki, hogy melyik előfizetést szeretné keresni.

1. A szűrő segítségével válassza ki, hogy melyik erőforráscsoportot keresse meg, amely a korábban kiválasztott előfizetés részét képezi.

1. A szűrő segítségével válassza ki, hogy melyik erőforrást szeretné megjeleníteni. A szűrő az erőforrás nevéhez és az erőforrástípushoz is működik.

## <a name="discover-aliases-for-resource-properties"></a>Erőforrás-tulajdonságok aliasok felderítése

Ha egy erőforrás van kiválasztva, akár a keresési felületen keresztül, vagy kiválasztja azt a treeview, az Azure Policy bővítmény megnyitja az erőforrást képviselő JSON-fájlt és annak Erőforrás-kezelő tulajdonságértékeit.

Miután egy erőforrás meg van nyitva, a Resource Manager tulajdonság neve vagy értéke fölé mutató egérmutató megjeleníti az Azure Policy aliast, ha létezik ilyen. Ebben a példában az `Microsoft.Compute/virtualMachines` erőforrás egy erőforrástípus, és a **properties.storageProfile.imageReference.offer** tulajdonság fölé kerül. Az egérmutató az egyező aliasokat jeleníti meg.

![Az Azure Policy bővítmény rámutatása az Erőforrás-kezelő tulajdonságaliasát jeleníti meg](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Házirendek és hozzárendelések keresése és megtekintése

Az Azure Policy bővítmény felsorolja a szabályzattípusok at és a szabályzat-hozzárendeléseket fanézetként a **Szabályzatok** ablaktáblában megjeleníthető előfizetésekhez. Azok az ügyfelek, akik több száz vagy több ezer szabályzattal vagy-hozzárendeléssel rendelkeznek egyetlen előfizetésben, kereshető módot részesíthetnek előnyben a szabályzataik vagy -hozzárendeléseik megkeresésére. Az Azure Policy bővítmény lehetővé teszi egy adott szabályzat vagy hozzárendelés keresését a következő lépésekkel:

1. Indítsa el a keresési felületet az Azure Policy bővítményből vagy a parancspalettából.

   - Azure-szabályzat bővítmény

     Az Azure Policy bővítményben mutasson a **Szabályzat panelre,** és válassza ki a három pontot, majd válassza a **Keresési házirendek**lehetőséget.

   - Parancspaletta:

     A menüsorban válassza a **Parancspaletta** **megtekintése** > lapot, és írja be a **Házirendek: Keresési házirendek**című részt.

1. Ha egynél több előfizetés van kiválasztva a megjelenítéshez, a szűrő segítségével válassza ki, hogy melyik előfizetést szeretné keresni.

1. A szűrő segítségével kiválaszthatja, hogy melyik házirendtípust vagy -hozzárendelést szeretné keresni, amely a korábban kiválasztott előfizetés részét képezi.

1. A szűrő segítségével válassza ki, hogy melyik házirendet vagy megjeleníteni kívánt. A szűrő a házirend-definíció vagy a házirend-hozzárendelés _displayName-jéhez_ működik.

Amikor kiválaszt egy szabályzatot vagy hozzárendelést, akár a keresési felületen keresztül, akár a treeview-ban, az Azure Policy bővítmény megnyitja a JSON-t, amely a szabályzatot vagy hozzárendelést és annak erőforrás-kezelői tulajdonságértékeit képviseli. A bővítmény érvényesítheti a megnyitott Azure Policy JSON-sémát.

## <a name="sign-out"></a>Kijelentkezés

A menüsorban nyissa meg a**Parancspaletta** **megtekintése** > lapot, és írja be az **Azure: Kijelentkezés**című lapot.

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](programmatically-create.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)