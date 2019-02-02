---
title: Tervrajz létrehozása a portálon
description: Összetevők létrehozása, definiálása és üzembe helyezése az Azure Blueprints használatával.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c6c498920d407dbbac2449d1da0d76237db0c63d
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566312"
---
# <a name="define-and-assign-an-azure-blueprint-in-the-portal"></a>Definiálása és hozzárendelése egy Azure tervezet a portálon

A tervek létrehozásának és hozzárendelésének elsajátítása lehetővé teszi a közös minták definiálását, hogy újrahasználható és gyorsan üzembe helyezhető konfigurációkat dolgozhasson ki Resource Manager-sablonok, szabályzatok, biztonsági és egyéb szempontok alapján. Ez az oktatóanyag bemutatja, hogyan hajthatja végre az Azure Blueprints használatával a tervek a szervezeten belüli létrehozásával, közzétételével és hozzárendelésével kapcsolatos olyan általános feladatokat, mint az alábbiak:

> [!div class="checklist"]
> - Új terv létrehozása és különféle támogatott összetevők hozzáadása
> - Meglévő, **Vázlat** állapotú tervek módosítása
> - Terv készként való megjelölése a hozzárendeléshez a **Közzétéve** állapottal
> - Terv hozzárendelése egy meglévő előfizetéshez
> - Hozzárendelt terv állapotának és feldolgozottságának ellenőrzése
> - Egy előfizetéshez rendelt terv törlése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. A példában létrehozzuk a „MyBlueprint” nevű új tervet az előfizetés szerepkör- és szabályzat-hozzárendeléseinek konfigurálására, hozzáadunk egy új erőforráscsoportot, és létrehozunk egy Resource Manager-sablont és szerepkör-hozzárendelést az új erőforráscsoporton.

1. Kattintson a **Minden szolgáltatás** elemre, és keresse meg, majd válassza ki a **Szabályzat** elemet a bal oldali panelen. Kattintson a **Szabályzat** oldal **Tervek** elemére.

1. Az oldal bal szélén válassza a **Tervdefiníciók** lehetőséget, majd kattintson a **+ Terv létrehozása** gombra az oldal tetején.

   - A **Létrehozás** lehetőségre az **Első lépések** oldalon is rákattinthat, így közvetlenül a terv létrehozására léphet tovább.

   ![Terv létrehozása](./media/create-blueprint-portal/create-blueprint-button.png)

1. A **Terv neve** tulajdonságban adja meg például a „MyBlueprint” nevet a tervnek (betűkből és számokból, legfeljebb 48 karakterből állhat, szóközt és különleges karaktert nem tartalmazhat), a **Terv leírása** viszont egyelőre maradjon üresen.  Az a **definíció helye** kattintson a jobb oldalon a három pontra, válassza ki a [felügyeleti csoport](../management-groups/overview.md) vagy ahol a tervezet mentse, és kattintson a kívánt előfizetés **kiválasztása**.

1. Ellenőrizze a helyes-e az adatokat (a **Tervrajznév** és **definíció helye** mezők később már nem módosítható), és kattintson a **tovább: Összetevők** a lap alján, vagy a **összetevők** fülre az oldal tetején.

1. Szerepkör-hozzárendelés hozzáadása az előfizetéshez: Kattintson a bal gombbal a **+ Hozzáadás összetevő...**  sor alatt **előfizetés** és a böngésző jobb oldalán megjelenik az "Add összetevő" ablak. Az _Összetevőtípus_ értékeként válassza a „Szerepkör-hozzárendelés” lehetőséget. A _Szerepkör_ alatt válassza a „Közreműködő” lehetőséget, a _Felhasználó, alkalmazás vagy csoport hozzáadása_ mező alatt pedig hagyja bejelölve a **dinamikus paramétert** jelző jelölőnégyzetet. Az összetevőt a **Hozzáadás** lehetőségre kattintva adhatja hozzá a tervhez.

   ![Összetevő – Szerepkör-hozzárendelés](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Az _összetevők_ többsége paramétereket is támogat. Az a paraméter, amelyhez a terv létrehozása során van érték rendelve, **statikus paraméter**. Ha a paraméter értéke a terv hozzárendelése során lesz megadva, akkor az **dinamikus paraméter**. További információkat a [tervparamétereket](./concepts/parameters.md) ismertető cikkben talál.

1. Szabályzat-hozzárendelés hozzáadása az előfizetéshez: Kattintson a bal gombbal a **+ Hozzáadás összetevő...**  közvetlenül a sor a **előfizetés**. Az _Összetevőtípus_ értékeként válassza a „Szabályzat-hozzárendelés” lehetőséget. A _Típust_ módosítása „Beépítettre”, a _Keresés_ mezőbe pedig írja be a „címke” szót. A szűrés végrehajtásához kattintson a _Keresésen_ kívülre. Kattintással válassza ki a „Címke és annak alapértelmezett értékének alkalmazása erőforráscsoportokra” lehetőséget. Az összetevőt a **Hozzáadás** lehetőségre kattintva adhatja hozzá a tervhez.

1. Kattintson a „Címke és annak alapértelmezett értékének alkalmazása erőforráscsoportokra” szabályzat-hozzárendelési sorra. Megnyílik az ablak, amelyben a tervdefiníció részeként adhatók meg paraméterek az összetevőhöz, és a paraméterek minden ezen a terven alapuló hozzárendeléshez beállíthatók (**statikus paraméterek**), nem pedig a hozzárendelés során kell megadni azokat (**dinamikus paraméterek**). Ez a példa **dinamikus paramétereket** használ a tervhozzárendelés során, ezért hagyja meg az alapértelmezett értékeket, és kattintson a **Mégse** elemre.

1. Előfizetés hozzáadása az erőforráscsoportot: Kattintson a bal gombbal a **+ Hozzáadás összetevő...**  sor alatt **előfizetés**. Az _Összetevőtípus_ értékeként válassza az „Erőforráscsoport” lehetőséget. Hagyja üresen az _Erőforráscsoport neve_ és a _Hely_ mezőket, de ellenőrizze, hogy mindkét tulajdonsághoz be van jelölve az azokat **dinamikus paraméterré** tevő jelölőnégyzet. Az összetevőt a **Hozzáadás** lehetőségre kattintva adhatja hozzá a tervhez.

1. Adja hozzá az erőforráscsoporthoz tartozó sablon: Kattintson a bal gombbal a **+ Hozzáadás összetevő...** Ezt közvetlenül az **Erőforráscsoport** alatt találja. Az _Összetevőtípus_ értékeként válassza az „Azure Resource Manager-sablon” lehetőséget, az _Összetevő megjelenített neve_ tulajdonságnak állítsa be a „StorageAccount” értéket, és hagyja üresen a _Leírás_ mezőt. A szerkesztőmező **Sablon** lapján illessze be az alábbi Resource Manager-sablont. A sablon beillesztése után kattintson a **Paraméterek** fülre, és figyelje meg, hogy a sablon **storageAccountType** paramétere és a **Standard_LRS** alapértelmezett értéke automatikusan észlelve lett, és ki lett töltve, de **dinamikus paraméterként** van konfigurálva. Távolítsa el jelölést a jelölőnégyzetből, és figyelje meg, hogy a legördülő lista csak a Resource Manager-sablon **allowedValues** szakaszában felsorolt értékekből áll. Állítsa vissza **Dinamikus paraméterré** a jelölőnégyzetet bejelölésével. Az összetevőt a **Hozzáadás** lehetőségre kattintva adhatja hozzá a tervhez.

   > [!IMPORTANT]
   > A sablon importálásakor ügyeljen rá, hogy a fájl tisztán JSON legyen, és ne tartalmazzon HTML-t. Ügyeljen rá, hogy a GitHub-beli URL-címekre való hivatkozáskor a **RAW** lehetőségre kattintson, hogy ne a GitHubon való megjelenítéshez HTML-be ágyazott, hanem a tiszta JSON-fájlt töltse le. Ha az importált sablon nem tiszta JSON, hiba történik.

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
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "apiVersion": "2016-01-01",
           "location": "[resourceGroup().location]",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "Storage",
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

   ![Összetevő – Resource Manager-sablon](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy minden összetevő _Paraméterek_ oszlopában szerepel az „_y_ paraméterből _x_ kitöltve” szöveg. A **dinamikus paraméterek** beállítása a terv egyes hozzárendeléseikor történik.

   ![Kész terv](./media/create-blueprint-portal/completed-blueprint.png)

1. Az összes tervezett összetevő hozzáadása után kattintson az oldal alján lévő **Piszkozat mentése**.

## <a name="edit-a-blueprint"></a>Terv szerkesztése

A [Terv létrehozása](#create-a-blueprint) során nem adott meg leírást, és a szerepkör-hozzárendelést sem adta hozzá az új erőforráscsoporthoz. Mindkettő kijavítható az alábbi lépésekkel:

1. A lap bal oldalán válassza a **Tervdefiníciók** lehetőséget.

1. A tervek listájában kattintson a jobb gombbal arra, amelyet az imént létrehozott, és válassza a **Terv szerkesztése** lehetőséget.

1. A **Terv leírásában** adjon meg néhány információt a tervről és az azt alkotó összetevőkről.  Ebben az esetben adja meg, például: "Ez a megoldás címke házirend- és szerepkör-hozzárendelés állítja be az előfizetés, létrehoz egy erőforráscsoport és erőforrás-sablon és a szerepkör-hozzárendelés telepíti a ResourceGroup."

1. Kattintson a **tovább: Összetevők** a lap alján, vagy a **összetevők** fülre az oldal tetején.

1. Adja hozzá az erőforráscsoporthoz tartozó szerepkör-hozzárendelés: Kattintson a bal gombbal a **+ Hozzáadás összetevő...**  közvetlenül a sor a **ResourceGroup** bejegyzés. Az _Összetevőtípus_ értékeként válassza a „Szerepkör-hozzárendelés” lehetőséget. A _Szerepkör_ alatt válassza a Tulajdonos lehetőséget, törölje a _Felhasználó, alkalmazás vagy csoport hozzáadása_ mező jelölését, majd keressen meg és jelöljön ki egy hozzáadni kívánt felhasználót, alkalmazást vagy csoportot. Az összetevő ugyanazt a **statikus paraméterkészletet** használja a terv minden hozzárendelésében. Az összetevőt a **Hozzáadás** lehetőségre kattintva adhatja hozzá a tervhez.

   ![Összetevő – Szerepkör-hozzárendelés #2](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy az újonnan hozzáadott szerepkör-hozzárendeléshez az **1 paraméterből 1 kitöltve** szöveg jelenik meg, tehát ez egy **statikus paraméter**.

   ![Kész terv #2](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Most, hogy a terv frissítve lett, kattintson a **Piszkozat mentése** lehetőségre.

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy minden tervezett összetevő hozzá lett adva a tervhez, itt az idő közzétenni azt.
A közzététellel a terv szabadon hozzárendelhető lesz az előfizetésekhez.

1. A lap bal oldalán válassza a **Tervdefiníciók** lehetőséget.

1. A tervek listájában kattintson a jobb gombbal arra, amelyet az imént létrehozott, és válassza a **Terv közzététele** lehetőséget.

1. A megnyíló párbeszédpanelen adjon meg egy **Verziót** (betűk, számok és kötőjelek, legfeljebb 20 karakter), mint például „v1”, és **Módosítási megjegyzéseket** (nem kötelező), például: „Első közzététel”.

1. Kattintson a lap alján lévő **Közzététel** gombra.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

Miután közzétett egy tervet, az hozzárendelhető az előfizetésekhez. A létrehozott tervet a felügyeleti csoport hierarchiájában rendelheti hozzá az egyik előfizetéshez. Ha egy előfizetést a tervezet menti, csak rendelhető előfizetéshez.

1. A lap bal oldalán válassza a **Tervdefiníciók** lehetőséget.

1. A tervek listájában kattintson a jobb gombbal arra, amelyet az imént létrehozott (vagy kattintson a három pontra), és válassza a **Terv hozzárendelése** lehetőséget.

1. A **Terv hozzárendelése** oldalon az **Előfizetések** legördülő listájában jelölje ki azt az egy vagy több előfizetést, amelyben üzembe kívánja helyezni ezt a tervet.

   > [!NOTE]
   > Minden kijelölt előfizetéshez létrejön egy hozzárendelés, így az egyes előfizetés-hozzárendelések később anélkül módosíthatók, hogy a többi kijelölt előfizetésben is módosítani kellene őket.

1. A **Hozzárendelt név**mezőben adjon meg egyedi nevet a hozzárendelésnek.

1. A **Hely** mezőben válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md).

1. A **Közzétett** verziók **Tervdefiníció verzió** legördülő listájában hagyja meg a „v1” bejegyzést (alapértelmezett érték a legutóbb **Közzétett** verzióhoz).

1. A **Hozzárendelés zárolása** mezőben hagyja meg az alapértelmezett **Nem zárolandó** értéket. További információkat talál a [terv-erőforrások zárolásáról](./concepts/resource-locking.md) szóló cikkben.

1. Az előfizetés szintű szerepkör-hozzárendelés **[felhasználói csoport vagy alkalmazás neve]: Közreműködői**, keresse meg és válassza ki a felhasználó, alkalmazás vagy csoport.

1. Az előfizetés-szintű szabályzat-hozzárendeléshez a **Címke neve** tulajdonságot a „CostCenter”, a **Címke értéke** tulajdonságot pedig a „ContosoIT” értékre állítsa be.

1. Az „Erőforráscsoporthoz” adja meg a „StorageAccount” **Nevet**, és az „USA 2. keleti régiója” **Helyet** a legördülő listából.

   > [!NOTE]
   > Az erőforráscsoportban a terv definiálása során hozzáadott összetevők mindegyikének ahhoz az erőforráscsoporthoz vagy objektumhoz kell igazodnia, amellyel üzembe lesz helyezve. A paramétereket nem elfogadó, valamint a hozzárendeléskor megadandó paraméterekkel nem rendelkező összetevők csak környezeti információként lesznek felsorolva.

1. A „StorageAccount” Azure Resource Manager-sablonban a **storageAccountType** paraméternek válassza a „Standard_GRS” értéket.

1. Olvassa el a lap alján lévő információs ablak tartalmát, majd kattintson a **Hozzárendelés** lehetőségre.

## <a name="track-deployment-of-a-blueprint"></a>Terv üzembe helyezésének nyomon követése

Miután egy terv hozzá lett rendelve egy vagy több előfizetéshez, két dolog történik:

- A terv fel lesz véve a **Hozzárendelt tervek** oldalra minden előfizetéshez, amelyhez hozzá van rendelve
- Megkezdődik a terv által definiált összetevők üzembe helyezésének folyamata

Most, hogy a terv hozzá lett rendelve egy előfizetéshez, ellenőrizze az üzembe helyezési folyamatot.

1. Válassza a **Hozzárendelt tervek** lehetőséget a lap bal oldalán.

1. A tervek listájában kattintson a jobb gombbal arra, amelyet az imént hozzárendelt, és válassza a **Hozzárendelés részleteinek megtekintése** lehetőséget.

   ![Hozzárendelés részleteinek megtekintése](./media/create-blueprint-portal/view-assignment-details.png)

1. Ellenőrizze az **Üzembe helyezés részletei** oldalon, hogy minden összetevő sikeresen üzembe lett helyezve, és hogy a folyamat során nem lépett fel hiba. Ha hiba történt, a [tervek hibaelhárításáról](./troubleshoot/general.md) szóló cikk alapján állapítsa meg a probléma okát.

## <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Ha már nincs rájuk szükség, eltávolíthatja a tervek hozzárendelését az előfizetésekből. Előfordulhat, hogy a tervet frissített mintákat, szabályzatokat és kialakításokat tartalmazó újabb terv váltotta fel. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. A tervek hozzárendelését az alábbi lépések szerint szüntetheti meg:

1. Válassza a **Hozzárendelt tervek** lehetőséget a lap bal oldalán.

1. A jelölje ki a tervek listájában azt, amelynek hozzárendelését meg kívánja szüntetni, majd kattintson az oldal tetején a **Terv hozzárendelésének megszüntetése** gombra.

1. Olvassa el a megerősítést kérő üzenetet, majd kattintson az **OK** lehetőségre.

## <a name="delete-a-blueprint"></a>Terv törlése

1. A lap bal oldalán válassza a **Tervdefiníciók** lehetőséget.

1. Kattintson a jobb gombbal a törölni kívánt tervre, válassza a **Terv törlése** lehetőséget, majd a megerősítési párbeszédpanelen kattintson az **Igen** lehetőségre.

> [!NOTE]
> Egy terv ilyen módon elvégzett törlése egyben a kijelölt terv összes **Közzétett verzióját** is törli. Egyetlen verzió törléséhez nyissa meg a tervet, kattintson a **Közzétett verziók** fülre, jelölje ki a törölni kívánt verziót, majd kattintson rá, és kattintson a **Verzió törlése** lehetőségre. Hozzárendeléssel rendelkező terv nem törölhető addig, amíg nem törli az összes hozzárendelését.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](./concepts/lifecycle.md)
- A [statikus és dinamikus paraméterek](./concepts/parameters.md) használatának elsajátítása
- A [tervekkel kapcsolatos műveleti sorrend](./concepts/sequencing-order.md) testreszabásának elsajátítása
- A [tervek erőforrás-zárolásának](./concepts/resource-locking.md) alkalmazásával kapcsolatos részletek
- A [meglévő hozzárendelések frissítésének](./how-to/update-existing-assignments.md) elsajátítása
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](./troubleshoot/general.md)
