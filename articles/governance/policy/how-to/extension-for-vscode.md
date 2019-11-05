---
title: Azure Policy-bővítmény a Visual Studio Code-hoz
description: Megtudhatja, hogyan használhatja a Visual Studio Code Azure Policy bővítményét Resource Manager-aliasok kereséséhez.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 20af29c40ec13add90294e28d64f0166acc95319
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514809"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Azure Policy-bővítmény használata a Visual Studio Code-hoz

> A Azure Policy bővítmény **0.0.21** és újabb verziójára vonatkozik

Megtudhatja, hogyan kereshet [aliasokat](../concepts/definition-structure.md#aliases) , és hogyan tekintheti át az erőforrásokat és a házirendeket a Visual Studio Code Azure Policy bővítményének használatával. Először is leírjuk, hogyan kell telepíteni a Azure Policy bővítményt a Visual Studio Code-ban. Ezután megismerheti az aliasok keresésének módját.

A Visual Studio Code-hoz készült Azure Policy-bővítmény a Visual Studio Code által támogatott összes platformra telepíthető. Ez a támogatás magában foglalja a Windows, a Linux és a macOS rendszer használatát.

> [!NOTE]
> A következő, a Visual Studio Code-hoz készült Azure Policy-bővítményben megtekintett házirendek nem szinkronizálhatók az Azure-ba.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek végrehajtásához a következő elemek szükségesek:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Azure Policy bővítmény telepítése

Az előfeltételek teljesítése után a következő lépésekkel telepítheti Azure Policy-bővítményt a Visual Studio Code-hoz:

1. Nyissa meg a Visual Studio Code-ot.

1. A menüsávban lépjen a > - **bővítmények** **megtekintése** elemre.

1. A keresőmezőbe írja be a **Azure Policy**kifejezést.

1. Válassza a **Azure Policy** elemet a keresési eredmények közül, majd válassza a **telepítés**lehetőséget.

1. Szükség esetén válassza az **Újratöltés** lehetőséget.

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

A nemzeti Felhőbeli felhasználók esetében kövesse az alábbi lépéseket az Azure-környezet első beállításához:

1. Válassza a **File\Preferences\Settings**lehetőséget.

1. Keressen rá a következő sztringre: _Azure: Cloud_

1. Válassza ki a nemzet felhőjét a listából:

   ![Alapértelmezett Azure Cloud Sign in beállítása a Visual Studio Code-hoz](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Kapcsolódás Azure-fiókhoz

Az erőforrások és a keresési aliasok kiértékeléséhez csatlakoznia kell az Azure-fiókjához. Kövesse az alábbi lépéseket az Azure-hoz való kapcsolódáshoz a Visual Studio Code-ból:

1. Jelentkezzen be az Azure-ba a Azure Policy bővítményből vagy a parancssorból.

   - Azure Policy bővítmény

     A Azure Policy bővítményben válassza a **Bejelentkezés az Azure**-ba lehetőséget.

     ![Azure Felhőbeli bejelentkezés a Visual Studio Code-hoz Azure Policy bővítményből](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Parancs paletta

     A menüsávban válassza a > **parancs-paletta** **megjelenítése** lehetőséget, és írja be az **Azure: bejelentkezés**lehetőséget.

     ![Azure Felhőbeli bejelentkezés a Visual Studio Code-hoz a parancssorból](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Az Azure-ba való bejelentkezéshez kövesse a bejelentkezési utasításokat. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio Code ablak alján található állapotsorban.

## <a name="select-subscriptions"></a>Előfizetések kiválasztása

Amikor először jelentkezik be, csak az alapértelmezett előfizetési erőforrásokat és házirendeket tölti be a Azure Policy bővítmény. Az erőforrások és házirendek megjelenítéséhez szükséges előfizetések hozzáadásához vagy eltávolításához kövesse az alábbi lépéseket:

1. Indítsa el az előfizetés parancsot a parancssorból vagy az ablak láblécből.

   - Parancs paletta: 

     A menüsávban válassza a > **parancs-paletta** **megjelenítése** elemet, majd írja be a **Azure Policy: Select Subscriptions (előfizetések kiválasztása**) elemet.

   - Ablak lábléce

     A képernyő alján található ablak láblécében válassza ki az Azure-nak megfelelő szegmenst **: \<a fiókját\>** .

1. A szűrő mező használatával gyorsan megkeresheti az előfizetéseket név szerint. Ezután a Azure Policy-bővítmény által megjelenített előfizetések beállításához jelölje be vagy távolítsa el az egyes előfizetések ellenőrzését. Ha végzett a megjelenítendő előfizetések hozzáadásával vagy eltávolításával, kattintson **az OK gombra**.

## <a name="search-for-and-view-resources"></a>Erőforrások keresése és megtekintése

A Azure Policy bővítmény a kiválasztott előfizetések erőforrás-szolgáltatója és az **erőforrások** ablaktáblán erőforráscsoport alapján listázza az erőforrásokat. A TreeView vezérlő a kiválasztott előfizetésben vagy az előfizetési szinten lévő erőforrások alábbi csoportosításait tartalmazza:

- **Erőforrás-szolgáltatók**
  - Minden regisztrált erőforrás-szolgáltató erőforrásokkal és kapcsolódó alárendelt erőforrásokkal, amelyek szabályzat-aliasokkal rendelkeznek
- **Erőforráscsoportok**
  - Az erőforráscsoport összes erőforrása

Alapértelmezés szerint a bővítmény az "erőforrás-szolgáltató" részét a meglévő erőforrások és a házirend-aliasokkal rendelkező erőforrások alapján szűri. Módosítsa ezt a viselkedést a **beállítások** > **extensions** > **Azure Policy** , hogy minden erőforrás-szolgáltató szűrés nélkül megjelenjen.

Az egyes előfizetésekben több száz vagy több ezer erőforrást használó ügyfelek kereshetnek keresési módot az erőforrásaik megkereséséhez. A Azure Policy bővítmény lehetővé teszi egy adott erőforrás keresését a következő lépésekkel:

1. Indítsa el a keresési felületet a Azure Policy-bővítményből vagy a parancssorból.

   - Azure Policy bővítmény

     Az Azure Policy-bővítményben vigye az egérmutatót az **erőforrások** panel fölé, válassza a három pontot, majd válassza az **erőforrások keresése**lehetőséget.

   - Parancs paletta:

     A menüsávban válassza a > **parancs-paletta** **megjelenítése** elemet, és írja be az **erőforrások: erőforrások keresése**lehetőséget.

1. Ha egynél több előfizetés van kiválasztva a megjelenítéshez, használja a szűrőt a keresendő előfizetés kiválasztásához.

1. A szűrő használatával kiválaszthatja, hogy melyik erőforráscsoport legyen a korábban kiválasztott előfizetés részét képező keresés.

1. A szűrő használatával kiválaszthatja, hogy melyik erőforrást szeretné megjeleníteni. A szűrő az erőforrás nevére és az erőforrás típusára is használható.

## <a name="discover-aliases-for-resource-properties"></a>Az erőforrás-tulajdonságok aliasának felderítése

Ha erőforrás van kiválasztva, akár a keresési felületen keresztül, akár a TreeView-ban való kiválasztásával, a Azure Policy bővítmény megnyitja az adott erőforrást jelképező JSON-fájlt, valamint az összes erőforrás-kezelő tulajdonság értékét.

Ha egy erőforrás meg van nyitva, a Resource Manager-tulajdonságnév vagy-érték fölé húzva megjelenik a Azure Policy alias, ha van ilyen. Ebben a példában az erőforrás egy `Microsoft.Compute/virtualMachines` erőforrástípus, és a **Properties. storageProfile. imageReference. Offer** tulajdonságot a rendszer fölé viszi. Az egérmutató a megfelelő aliasokat jeleníti meg.

![Azure Policy bővítmény hover a Resource Manager-tulajdonság aliasát mutatja](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Szabályzatok és hozzárendelések keresése és megtekintése

A Azure Policy bővítmény a házirendek ablaktáblán megjelenítendő előfizetések számára a **szabályzatok** és a házirendek hozzárendelését jeleníti meg. Egy adott előfizetésben több száz vagy akár több ezer szabályzatot vagy hozzárendelést is használhatnak, így kereshetik meg a szabályzatokat vagy a hozzárendeléseket. A Azure Policy bővítmény lehetővé teszi egy adott házirend vagy hozzárendelés keresését a következő lépésekkel:

1. Indítsa el a keresési felületet a Azure Policy-bővítményből vagy a parancssorból.

   - Azure Policy bővítmény

     Az Azure Policy-bővítményben vigye a kurzort a **házirendek** panel fölé, válassza a három pontot, majd válassza a **keresési szabályzatok**lehetőséget.

   - Parancs paletta:

     A menüsávban válassza a > **parancs-paletta** **megjelenítése** lehetőséget, és adja meg a **szabályzatok: keresési házirendek**elemet.

1. Ha egynél több előfizetés van kiválasztva a megjelenítéshez, használja a szűrőt a keresendő előfizetés kiválasztásához.

1. A szűrő használatával kiválaszthatja, hogy melyik házirend-típust vagy hozzárendelést szeretné megkeresni a korábban kiválasztott előfizetés részeként.

1. A szűrő használatával kiválaszthatja, hogy melyik házirendet vagy jelenjen meg. A szűrő a _DisplayName_ paraméterrel működik a házirend-definícióhoz vagy a házirend-hozzárendeléshez.

Amikor kijelöl egy házirendet vagy hozzárendelést, legyen az a keresési felületen keresztül, vagy kiválasztja azt a TreeView vezérlőben, a Azure Policy bővítmény megnyitja a szabályzatot vagy hozzárendelést jelölő JSON-értéket, valamint az összes erőforrás-kezelő tulajdonság értékét. A bővítmény ellenőrizheti a megnyitott Azure Policy JSON-sémát.

## <a name="sign-out"></a>Kijelentkezés

A menüsávban válassza a > **parancs-paletta** **megjelenítése** elemet, majd írja be az **Azure: kijelentkezés**lehetőséget.

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).