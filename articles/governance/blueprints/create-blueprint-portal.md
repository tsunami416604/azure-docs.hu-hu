---
title: 'Rövid útmutató: Tervrajz létrehozása a portálon'
description: Ebben a rövid útmutatóban az Azure Blueprints használatával hoz létre, definiál, és üzembe helyezi az összetevők et az Azure Portalon keresztül.
ms.date: 03/25/2020
ms.topic: quickstart
ms.openlocfilehash: dd50b1833f16d364a4494483fcccfee017bb982b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381892"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Rövid útmutató: Tervrajz definiálása és hozzárendelése a portálon

Amikor megtanulja, hogyan hozhat létre és rendelhet hozzá tervrajzokat, közös mintákat határozhat meg az Azure Resource Manager-sablonok, szabályzat, biztonság és egyebek alapján újrafelhasználható és gyorsan üzembe helyezhető konfigurációk fejlesztéséhez. Ebben az oktatóanyagban megtanulja az Azure Blueprints használatát a szervezeten belüli tervkészítés létrehozásával, közzétételével és hozzárendelésével kapcsolatos gyakori feladatok elvégzéséhez. Ezek a feladatok a következők:

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Ebben a példában hozzon létre egy új tervezet nevű **MyBlueprint** szerepkör és házirend-hozzárendelések az előfizetéshez. Ezután vegyen fel egy új erőforráscsoportot, és hozzon létre egy Erőforrás-kezelő sablont és szerepkör-hozzárendelést az új erőforráscsoportban.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza a **tervrajzok definícióit** a bal oldali lapon, és válassza a lap tetején található **+ Tervezet létrehozása** gombot.

   Vagy válassza a **Létrehozás** lehetőséget az **Első lépések** lapon, ha egyenesen egy tervrajz létrehozásához szeretne menni.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Tervrajz létrehozása a Blueprint-definíciók lapon" border="false":::

1. Válassza a **Kezdés üres tervrajzsal** lehetőséget a beépített tervrajzok listájának tetején található kártyán.

1. Adjon meg egy **blueprint nevet,** például **A MyBlueprint**. (Legfeljebb 48 betűt és számot használjon, szóközök et és speciális karaktereket nem). Egyelőre hagyja üresen a **Tervrajz leírását.**

1. A **Definíció helye** mezőben jelölje ki a három pontot a jobb oldalon, jelölje ki azt a [felügyeleti csoportot](../management-groups/overview.md) vagy előfizetést, amelybe menteni szeretné a tervezetet, és válassza a **Kijelölés lehetőséget.**

1. Ellenőrizze, hogy az adatok helyesek-e. A **Blueprint neve** és a Definíció **helymezői** később nem módosíthatók. Ezután válassza a **Tovább : A** lap alján található eltérések vagy a lap tetején található **Eltérések** lap lehetőséget.

1. Szerepkör-hozzárendelés hozzáadása előfizetési szinten:

   1. Válassza a **+ Műtermék hozzáadása** sor lehetőséget **az Előfizetés csoportban.** Megnyílik a **Műtermék hozzáadása** ablak a böngésző jobb oldalán.

   1. Válassza a **Szerepkör-hozzárendelés** lehetőséget a **Műtermék típushoz.**

   1. A **Role (Szerepkör) csoportban**válassza **a Közreműködő**lehetőséget. Hagyja meg a **Felhasználó, alkalmazás vagy csoport hozzáadása** jelölőnégyzetet a dinamikus paramétert jelző jelölőnégyzettel.

   1. Válassza **a Hozzáadás** lehetőséget, ha hozzá szeretné adni ezt az összetevőt a tervrajzhoz.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Tervrajz-összetevő szerepkör-hozzárendelése" border="false":::

   > [!NOTE]
   > Az összetevők többsége paramétereket is támogat. A tervezet létrehozása során értékhez rendelt paraméter egy _statikus paraméter._ Ha a paraméter a tervezet hozzárendelése során van hozzárendelve, akkor ez egy _dinamikus paraméter._ További információ: [Blueprint parameters](./concepts/parameters.md).

1. Házirend-hozzárendelés hozzáadása előfizetési szinten:

   1. Válassza a **+ Műtermék hozzáadása** sort a szerepkör-hozzárendelési összetevő alatt.

   1. Válassza a **Házirend-hozzárendelést** a **Műtermék típushoz.**

   1. A **Típus** módosítása **beépített**re. A **Keresés mezőbe**írja be a **címkét.**

   1. A szűrés végrehajtásához kattintson a **Keresésen** kívülre. Válassza a **Hozzáfűzéscímke és annak alapértelmezett értékét az erőforráscsoportokhoz**lehetőséget.

   1. Válassza **a Hozzáadás** lehetőséget, ha hozzá szeretné adni ezt az összetevőt a tervrajzhoz.

1. Válassza ki a házirend-hozzárendelés **hozzáfűző címkéjének sorát és alapértelmezett értékét az erőforráscsoportokhoz.**

1. Megnyílik az ablak, amelyben a tervdefiníció részeként adhatók meg paraméterek az összetevőhöz, és a paraméterek minden ezen a terven alapuló hozzárendeléshez beállíthatók (statikus paraméterek), nem pedig a hozzárendelés során kell megadni azokat (dinamikus paraméterek). Ez a példa dinamikus paramétereket használ a tervezet hozzárendelése során, ezért hagyja meg az alapértelmezett értékeket, és válassza a **Mégse lehetőséget.**

1. Erőforráscsoport hozzáadása az előfizetés szintjén:

   1. Válassza a **+ Műtermék hozzáadása** sor lehetőséget **az Előfizetés csoportban.**

   1. Válassza **az Erőforráscsoportot** a **Műtermék típushoz.**

   1. Hagyja üresen a **Műtermék megjelenítendő nevét**, **az Erőforráscsoport neve**és a **Hely** jelölőnégyzetet, de győződjön meg arról, hogy a jelölőnégyzet be van jelölve az egyes paramétertulajdonságokhoz, hogy dinamikus paraméterekké tegyék őket.

   1. Válassza **a Hozzáadás** lehetőséget, ha hozzá szeretné adni ezt az összetevőt a tervrajzhoz.

1. Sablon hozzáadása az erőforráscsoport hoz:

   1. Jelölje ki a **+ Műtermék hozzáadása** sort az **Erőforráscsoport** bejegyzés alatt.

   1. Válassza az **Azure Resource Manager sablont** **a műtermék típushoz,** állítsa be a **műtermék megjelenítendő nevét** **storageaccount**értékre, és hagyja üresen **a Leírás lehetőséget.**

   1. A szerkesztőmező **Sablon** lapján illessze be az alábbi Resource Manager-sablont.
      A sablon beillesztése után válassza a **Paraméterek** lapot, és vegye figyelembe, hogy a rendszer a **storageAccountType** és **a hely** sablonparamétereket észlelte. A rendszer minden paramétert automatikusan észlelt és kitöltött, de dinamikus paraméterként konfigurált.

      > [!IMPORTANT]
      > Ha importálja a sablont, győződjön meg arról, hogy a fájl csak JSON, és nem tartalmaz HTML-t. Amikor a GitHubon egy URL-címre mutat, győződjön meg arról, hogy a **RAW-t** választotta a tiszta JSON-fájl lekéri, és nem a HTML-kóddal csomagolt html-t a GitHubon való megjelenítéshez. Ha az importált sablon nem tiszta JSON, hiba történik.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. Törölje a jelet a **StorageAccountType** jelölőnégyzetből, és vegye figyelembe, hogy a legördülő lista csak az **engedélyezett értékek**csoportban található Erőforrás-kezelő sablonban található értékeket tartalmazza. Jelölje be a jelölőnégyzetet, ha dinamikus paraméterre szeretné visszaállítani.

   1. Válassza **a Hozzáadás** lehetőséget, ha hozzá szeretné adni ezt az összetevőt a tervrajzhoz.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Erőforrás-kezelő sablon a tervezet összetevő" border="false":::

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy minden összetevő x y paraméterek a **Paraméterek** oszlopban ** _x_ feltöltve. _y_ ** A dinamikus paraméterek beállítása a terv egyes hozzárendeléseikor történik.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Befejezett tervezetdefiníció" border="false":::

1. Most, hogy az összes tervezett összetevő hozzá lett adva, válassza a **Vázlat mentése lehetőséget** a lap alján.

## <a name="edit-a-blueprint"></a>Terv szerkesztése

A [Tervezet létrehozása](#create-a-blueprint)csoportban nem adott meg leírást, és nem adta hozzá a szerepkör-hozzárendelést az új erőforráscsoporthoz. Mindkettőt az alábbi lépésekkel javíthatja:

1. Válassza ki a **Blueprint-definíciókat** a bal oldali lapon.

1. A tervrajzok listájában kattintson a jobb gombbal a korábban létrehozott tervrajz elemre, és válassza a **Tervrajz szerkesztése parancsot.**

1. A **Blueprint leírása ,** adjon meg néhány információt a tervrajz és az összetevők, amelyek alkotják azt. Ebben az esetben adjon meg valami hasonlót: **Ez a tervezet beállítja a címkeszabályzatot és a szerepkör-hozzárendelést az előfizetésen, létrehoz egy Erőforráscsoportot, és telepít egy erőforrássablont és szerepkör-hozzárendelést az adott Erőforráscsoporthoz.**

1. Válassza a **Tovább : A** lap alján található eltérések vagy a lap tetején található **Eltérések** lap lehetőséget.

1. Szerepkör-hozzárendelés hozzáadása az erőforráscsoporthoz:

   1. Válassza ki a **+ Műtermék hozzáadása** sort közvetlenül az **Erőforráscsoport** bejegyzés alatt.

   1. Válassza a **Szerepkör-hozzárendelés** lehetőséget a **Műtermék típushoz.**

   1. A **Szerepkör csoportban**válassza a **Tulajdonos**lehetőséget, és törölje a jelet a **Felhasználó, alkalmazás vagy csoport hozzáadása** jelölőnégyzetből.

   1. Keressen rá egy hozzáadni kívánt felhasználóra, alkalmazásra vagy csoportra, és jelöljön ki. Az összetevő ugyanazt a statikus paraméterkészletet használja a terv minden hozzárendelésében.

   1. Válassza **a Hozzáadás** lehetőséget, ha hozzá szeretné adni ezt az összetevőt a tervrajzhoz.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="A tervezetösszetevő második szerepkör-hozzárendelése" border="false":::

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy az újonnan hozzáadott szerepkör-hozzárendelés **1-ből 1 feltöltött paramétert**jelenít meg. Ez azt jelenti, hogy ez egy statikus paraméter.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="A kitöltött terv második meghatározása" border="false":::

1. Válassza **a Vázlat mentése** lehetőséget, most, hogy frissült.

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy minden tervezett összetevő hozzá lett adva a tervhez, itt az idő közzétenni azt.
A közzététel elérhetővé teszi a tervezetet egy előfizetéshez rendelve.

1. Válassza ki a **Blueprint-definíciókat** a bal oldali lapon.

1. A tervrajzok listájában kattintson a jobb gombbal a korábban létrehozott tervrajzra, és válassza a **Tervrajz közzététele parancsot.**

1. A megnyíló ablaktáblán adjon meg egy **verziót** (betűket, számokat és kötőjeleket, amelyek maximális hossza 20 karakter), például **v1**. Tetszés szerint írjon be szöveget a **Jegyzetek módosítása mezőbe,** például **az Első közzététel mezőbe.**

1. Válassza a **Közzététel** lehetőséget a lap alján.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

A tervezet közzététele után egy előfizetéshez rendelhető. Rendelje hozzá a létrehozott tervrajzot a felügyeleti csoport hierarchia egyik előfizetéséhez. Ha a tervezet egy előfizetésbe van mentve, csak az adott előfizetéshez rendelhető hozzá.

1. Válassza ki a **Blueprint-definíciókat** a bal oldali lapon.

1. A tervrajzok listájában kattintson a jobb gombbal a korábban létrehozott tervrajzra (vagy jelölje ki a három pontot), és válassza a **Tervrajz hozzárendelése parancsot.**

1. A **Blueprint hozzárendelése** lap **Előfizetés** legördülő listájában válassza ki azokat az előfizetéseket, amelyekhez telepíteni szeretné ezt a tervezetet.

   Ha az [Azure Billing](../../billing/index.md)támogatott vállalati ajánlatai érhetők el, az **Előfizetés** mezőben egy új kapcsolat **létrehozása** aktiválódik. Kövesse az alábbi lépéseket:

   1. Válassza az **Új létrehozása** hivatkozást, ha új előfizetést szeretne létrehozni a meglévők kiválasztása helyett.

   1. Adja meg az új előfizetés **megjelenítendő nevét.**

   1. Válassza ki a rendelkezésre álló **ajánlatot** a legördülő listából.

   1. A három ponttal válassza ki azt a [felügyeleti csoportot,](../management-groups/overview.md) amelynek az előfizetése lesz.

   1. Válassza a Lap alján a **Létrehozás** lehetőséget.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Előfizetés létrehozása tervrajz-hozzárendelés-előfizetéshez" border="false":::

      > [!IMPORTANT]
      > Az új előfizetés közvetlenül a **Létrehozás**után jön létre.

   > [!NOTE]
   > Minden egyes kiválasztott előfizetéshez létrejön egy hozzárendelés. Egy előfizetés-hozzárendelést később is módosíthat anélkül, hogy a kijelölt előfizetések fennmaradó részében módosításokat kényszerelne.

1. A **Hozzárendelés neve**mezőbe adja meg a hozzárendelés egyedi nevét.

1. A **Hely területen**válassza ki a felügyelt identitás és az előfizetés központi telepítési objektumhoz létre kívánt régiót. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalókért lásd [az Azure-erőforrások felügyelt identitásait](../../active-directory/managed-identities-azure-resources/overview.md).

1. Hagyja a **Blueprint-definícióverzió** legördülő kiválasztása **közzétett** verziók a **v1** bejegyzést. (Az alapértelmezett beállítás a legutóbb közzétett verzió.)

1. A **Hozzárendelés zárolása** mezőben hagyja meg az alapértelmezett **Nem zárolandó** értéket. További információ: [Blueprints resource locking](./concepts/resource-locking.md).

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Zárolás és felügyelt identitások zárolása a hozzárendeléshez" border="false":::

1. A **Felügyelt identitás csoportban**hagyja meg a **rendszer alapértelmezett hozzárendelését.**

1. A **[Felhasználó, csoport vagy alkalmazás neve] : Közreműködő** előfizetés-szintű szerepkör-hozzárendeléshez keressen meg vagy jelöljön ki egy felhasználót, alkalmazást vagy csoportot.

1. Az előfizetési szintű házirend-hozzárendeléshez állítsa a **Címke nevét** **costcenter,** a **Címke érték étkeztetési értékét** **pedig a ContosoIT értékre.**

1. A **ResourceGroup**csoport esetében adja meg a **StorageAccount** **nevét** és az USA keleti részének **2** **helyét** a legördülő listából.

   > [!NOTE]
   > Az erőforráscsoport alatt a tervezet definíciója során hozzáadott minden egyes összetevő esetében az összetevő be van húzva, hogy igazodjon ahhoz az erőforráscsoporthoz vagy objektumhoz, amelyhez telepíteni fogja.
   > Azokat az összetevőket, amelyek nem vesznek paramétereket, vagy nem rendelkeznek a hozzárendeléskor definiálandó paramétereket, csak a környezetfüggő információk alapján sorolja fel.

1. Az Azure Resource Manager-sablon **StorageAccount,** válassza ki a **storageAccountType** paraméter **Standard_GRS.**

1. Olvassa el a lap alján található információs mezőt, és válassza a **Hozzárendelés lehetőséget.**

## <a name="track-deployment-of-a-blueprint"></a>Terv üzembe helyezésének nyomon követése

Miután egy terv hozzá lett rendelve egy vagy több előfizetéshez, két dolog történik:

- A tervezet minden egyes előfizetéshez hozzá adja a **Hozzárendelt tervrajzok** laphoz.
- A terv tervezet által meghatározott összes műtermék üzembe helyezésének folyamata megkezdődik.

Most, hogy a tervezet egy előfizetéshez van rendelve, ellenőrizze a központi telepítés előrehaladását:

1. A bal oldali lapon válassza a **Hozzárendelt tervrajzok lehetőséget.**

1. A tervrajzok listájában kattintson a jobb gombbal a korábban hozzárendelt tervrajzra, és válassza a **Hozzárendelés részleteinek megtekintése parancsot.**

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Hozzárendelés részleteinek megtekintése a Hozzárendelt tervrajzok lapon" border="false":::

1. A **Blueprint-hozzárendelés** lapon ellenőrizze, hogy az összes összetevő telepítése sikeresen megtörtént-e, és hogy nem voltak-e hibák a telepítés során. Ha hiba történt, olvassa [el a Hibatervrajzok hibaterveket](./troubleshoot/general.md) a hiba meghatározásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Ha már nincs szüksége blueprint-hozzárendelésre, távolítsa el azt egy előfizetésből. Előfordulhat, hogy a tervtervezetet egy újabb terv, frissített minták, szabályzatok és tervek váltotta fel. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. A tervek hozzárendelését az alábbi lépések szerint szüntetheti meg:

1. A bal oldali lapon válassza a **Hozzárendelt tervrajzok lehetőséget.**

1. A tervrajzok listájában válassza ki a visszarendelni kívánt tervrajzot. Ezután válassza a **Rajzrajz visszavonása** gombot a lap tetején.

1. Olvassa el a megerősítő üzenetet, majd kattintson **az OK gombra.**

### <a name="delete-a-blueprint"></a>Terv törlése

1. Válassza ki a **Blueprint-definíciókat** a bal oldali lapon.

1. Kattintson a jobb gombbal a törölni kívánt tervrajzra, és válassza **a Rajzrajz törlése parancsot.** Ezután a megerősítést kérő párbeszédpanelen válassza az **Igen** lehetőséget.

> [!NOTE]
> Ebben a módszerben egy tervrajz törlése is törli a kiválasztott terv összes közzétett verzióját.
> Egyetlen verzió törléséhez nyissa meg a tervezetet, válassza a **Közzétett verziók** lapot, jelölje ki a törölni kívánt verziót, majd válassza **a Verzió törlése**lehetőséget. Emellett nem törölheti a tervrajzokat, amíg nem törölte az adott tervezetdefiníció összes tervrajz-hozzárendelését.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott, hozzárendelt és eltávolított egy tervrajzot az Azure Portalon. Ha többet szeretne megtudni az Azure Blueprints, folytassa a blueprint életciklus-cikk.

> [!div class="nextstepaction"]
> [További információ a tervterv életciklusáról](./concepts/lifecycle.md)