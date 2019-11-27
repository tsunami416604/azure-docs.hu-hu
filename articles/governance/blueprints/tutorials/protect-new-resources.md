---
title: 'Oktatóanyag: új erőforrások megvédése zárolásokkal'
description: Ebben az oktatóanyagban az Azure-tervrajzok erőforrás-zárolási lehetőségeit csak olvasható módon használja, és nem törli az újonnan telepített erőforrások elleni védelemhez.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327448"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Oktatóanyag: új erőforrások biztosítása az Azure BluePrints erőforrás-zárolásokkal

Az Azure-tervrajzok [erőforrás-zárolásai](../concepts/resource-locking.md)révén az újonnan telepített erőforrásokat védetté teheti, akár a _tulajdonos_ szerepkörrel rendelkező fiókkal. Ezt a védelmet a Resource Manager-sablon által létrehozott erőforrások tervrajz-definíciójában adhatja hozzá.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> - Terv definíciójának létrehozása
> - A terv definíciójának megjelölése **közzétettként**
> - A terv definíciójának társítása meglévő előfizetéshez
> - Az új erőforráscsoport vizsgálata
> - A terv hozzárendelésének megszüntetése a zárolások eltávolításához

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-blueprint-definition"></a>Terv definíciójának létrehozása

Először hozza létre a terv definícióját.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Az **első lépések** oldalon, a bal oldalon válassza a **Létrehozás** **a terv létrehozása**alatt lehetőséget.

1. Keresse meg az oldal tetején található **üres terv** tervezetét. Válassza **az indítás üres tervtel**lehetőséget.

1. Adja meg ezt az információt az **alapok** lapon:

   - **Terv neve**: adjon meg egy nevet a tervezet mintájának másolatához. Ebben az oktatóanyagban a **zárolt storageaccount**nevet fogjuk használni.
   - **Terv leírása**: adja meg a terv definíciójának leírását. **Az üzembe helyezett erőforrásokra vonatkozó terv-erőforrás-zárolás tesztelésére**használható.
   - **Definíció helye**: kattintson a három pontot ábrázoló gombra (...), majd válassza ki azt a felügyeleti csoportot vagy előfizetést, amelybe menteni szeretné a terv definícióját.

1. Válassza ki az oldal tetején található **összetevők fület** , vagy válassza a Next (tovább) gombot **:** az oldal alján található összetevők.

1. Erőforráscsoport hozzáadása az előfizetési szinten:
   1. Válassza ki az összetevő **hozzáadása** sort az **előfizetés**alatt.
   1. Válassza az **erőforráscsoport** elemet az összetevő **típusa**területen.
   1. Állítsa az összetevő **megjelenítendő nevét** **RGtoLock**értékre.
   1. Hagyja üresen az **erőforráscsoport neve** és **helye** mezőket, de győződjön meg arról, hogy az egyes tulajdonságokon a jelölőnégyzet be van jelölve, hogy **dinamikus paramétereket**lehessen használni.
   1. A **Hozzáadás** gombra kattintva adja hozzá az összetevőt a tervhez.

1. Sablon hozzáadása az erőforráscsoport alatt:
   1. Válassza a **RGtoLock** bejegyzés alatt az összetevők **hozzáadása** sort.
   1. Válassza ki **Azure Resource Manager sablont** az összetevő **típusa**területen, állítsa a **lelet megjelenítendő nevét** **StorageAccount**értékre, és hagyja üresen a **leírást** .
   1. A **sablon** lapon illessze be az alábbi Resource Manager-sablont a szerkesztő mezőbe.
      A sablon beillesztése után a **Hozzáadás** gombra kattintva adja hozzá az összetevőt a tervhez.

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
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
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

1. Válassza a lap alján található **Piszkozat mentése** elemet.

Ez a lépés létrehozza a terv definícióját a kiválasztott felügyeleti csoportban vagy előfizetésben.

A **terv mentése sikeres** portál értesítése után lépjen a következő lépésre.

## <a name="publish-the-blueprint-definition"></a>A terv definíciójának közzététele

A terv definíciója már létre lett hozva a környezetben. A rendszer **Piszkozat** módban jön létre, és közzé kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők segítségével keresse meg a **zárolt storageaccount** terv definícióját, majd jelölje ki.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldali új ablaktáblán adja meg a **1,0** -as **verziót**. Ez a tulajdonság akkor hasznos, ha később módosítja a módosításokat. Adja meg a **módosítási megjegyzéseket**, például az **első verziót, amelyet a tervbe helyezett erőforrások zárolásához kell közzétenni**. Ezután válassza a **Közzététel** elemet az oldal alján.

Ez a lépés lehetővé teszi a terv hozzárendelését egy előfizetéshez. A terv definíciójának közzététele után továbbra is végezhet módosításokat. Ha módosítja a módosításokat, közzé kell tennie a definíciót egy új verzió értékkel, hogy nyomon követhesse az azonos terv definíciójának verziói közötti különbségeket.

Ha megjelenik a **közzétételi terv definíciója sikeres** portál értesítése, lépjen a következő lépésre.

## <a name="assign-the-blueprint-definition"></a>A terv definíciójának kiosztása

A terv definíciójának közzététele után hozzárendelheti azt egy előfizetéshez a felügyeleti csoporton belül, ahol mentette. Ebben a lépésben paramétereket biztosít a tervrajz-definíció egyedi telepítésének elvégzéséhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők segítségével keresse meg a **zárolt storageaccount** terv definícióját, majd jelölje ki.

1. Válassza a terv **kiosztása** elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - **Alapvető beállítások**

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely abban a felügyeleti csoportban található, ahol a terv definícióját mentette. Ha egynél több előfizetést választ ki, az egyes előfizetésekhez hozzárendelés jön létre a beírt paraméterek használatával.
     - **Hozzárendelés neve**: a név előre fel van töltve a terv definíciójának neve alapján. Azt szeretnénk, hogy ez a hozzárendelés az új erőforráscsoport zárolását képviseljék, ezért módosítsa a hozzárendelés nevét a **hozzárendelés-zárolt-storageaccount-TestingBPLocks**értékre.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../active-directory/managed-identities-azure-resources/overview.md).
       Ebben az oktatóanyagban válassza az **USA 2. keleti**régióját.
     - **Terv definíciójának verziója**: válassza ki a közzétett **1,0** -es verziót a terv definíciójában.

   - **Hozzárendelés zárolása**

     Válassza a **csak olvasható** terv zárolási módot. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - **Felügyelt identitás**

     Használja az alapértelmezett beállítást: **rendszer rendelve**. További információ: [felügyelt identitások](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Összetevő paramétereinek**

     Az ebben a szakaszban meghatározott paraméterek arra a tárgyra vonatkoznak, amelyben definiálva vannak. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. Az egyes összetevőknél állítsa be a paraméter értékét az **érték** oszlopban látható értékre.

     |Összetevő neve|Összetevő típusa|Paraméter neve|Érték|Leírás|
     |-|-|-|-|-|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Name (Név)|TestingBPLocks|Meghatározza az új erőforráscsoport nevét, amelyre a terv zárolásait alkalmazni kell.|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Hely|USA nyugati régiója, 2.|Meghatározza az új erőforráscsoport helyét, amelyre a terv zárolásait alkalmazni kívánja.|
     |StorageAccount|Resource Manager-sablon|storageAccountType (StorageAccount)|Standard_GRS|A Storage SKU. Az alapértelmezett érték _Standard_LRS_.|

1. Miután megadta az összes paramétert, válassza az oldal alján található **hozzárendelés** elemet.

Ez a lépés telepíti a definiált erőforrásokat, és konfigurálja a kiválasztott **zárolási hozzárendelést**. A tervbeli zárolások alkalmazása akár 30 percet is igénybe vehet.

A **terv kiosztásának meghatározása sikeres** portál értesítése után lépjen a következő lépésre.

## <a name="inspect-resources-deployed-by-the-assignment"></a>A hozzárendelés által üzembe helyezett erőforrások vizsgálata

A hozzárendelés létrehozza az erőforráscsoport _TestingBPLocks_ és a Resource Manager-sablon összetevő által üzembe helyezett Storage-fiókot. Az új erőforráscsoport és a kiválasztott zárolási állapot a hozzárendelés részletei lapon látható.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza ki a **kijelölt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a **hozzárendelés-zárolt-storageaccount-TestingBPLocks** terv-hozzárendelést, majd jelölje ki.

   Ebből a lapról láthatjuk, hogy a hozzárendelés sikeres volt, és az erőforrások az új terv zárolási állapotával lettek telepítve. Ha a hozzárendelés frissül, a **hozzárendelési művelet** legördülő lista az egyes definíciós verziók telepítésének részleteit jeleníti meg. A tulajdonságlap megnyitásához kiválaszthatja az erőforráscsoportot.

1. Válassza ki a **TestingBPLocks** erőforráscsoportot.

1. Válassza a bal oldali **hozzáférés-vezérlés (iam)** lapot. Ezután válassza ki a **szerepkör-hozzárendelések** lapot.

   Itt láthatjuk, hogy a _hozzárendelés-zárolt-storageaccount-TestingBPLocks_ terv-hozzárendelés _tulajdonosi_ szerepkörrel rendelkezik. Ez a szerepkör azért van, mert ez a szerepkör az erőforráscsoport üzembe helyezésére és zárolására szolgál.

1. Jelölje be a **megtagadási hozzárendelések** lapot.

   A terv-hozzárendelés [megtagadási hozzárendelést](../../../role-based-access-control/deny-assignments.md) hozott létre a központilag telepített erőforráscsoporthoz, hogy kikényszerítse az **írásvédett** terv zárolási módját. A megtagadási hozzárendelés megakadályozza, hogy valaki megfelelő jogokkal rendelkezik a **szerepkör-hozzárendelések** lapon bizonyos műveletek elvégzéséhez. A Megtagadás hozzárendelés az _összes rendszerbiztonsági tagra_hatással van.

   További információ a megtagadási hozzárendelésből származó rendszerbiztonsági tag kizárásáról: [tervrajzok erőforrás-zárolás](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Jelölje ki a megtagadási hozzárendelést, majd a bal oldalon válassza ki a **megtagadott engedélyek** lapot.

   A megtagadási hozzárendelés megakadályozza az összes műveletet a **\*** és a **művelet** konfigurációjában, de az olvasási hozzáférés engedélyezése a **\*/Read** kizárásával **.**

1. A Azure Portal navigációs menüben válassza a **TestingBPLocks-hozzáférés-vezérlés (iam)** lehetőséget. Ezután válassza ki az **Áttekintés** lapot a bal oldalon, majd az **erőforráscsoport törlése** gombot. Írja be a **TestingBPLocks** nevet a törlés megerősítéséhez, majd kattintson a **Törlés** gombra a panel alján.

   A portál értesítésének **törlési erőforráscsoport-TestingBPLocks sikertelen** . A hiba azt jelzi, hogy a fióknak van engedélye az erőforráscsoport törlésére, a hozzáférés megtagadva a terv-hozzárendeléssel. Ne feledje, hogy a terv hozzárendelése során a **csak olvasható** terv zárolási módot jelöltük ki. A terv zárolása megakadályozza, hogy egy olyan fiók, amely nem rendelkezik engedéllyel, akár _tulajdonossal_is, törölheti az erőforrást. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

Ezek a lépések bemutatják, hogy a telepített erőforrások mostantól a nem kívánt törlést megakadályozó, olyan fiókkal is védve vannak, amelyik jogosult az erőforrások törlésére.

## <a name="unassign-the-blueprint"></a>A terv hozzárendelésének megszüntetése

Az utolsó lépés a terv definíciójának hozzárendelésének eltávolítása. A hozzárendelés eltávolítása nem távolítja el a társított összetevőket.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza ki a **kijelölt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a **hozzárendelés-zárolt-storageaccount-TestingBPLocks** terv-hozzárendelést, majd jelölje ki.

1. Válassza az oldal tetején található **terv megszüntetése** elemet. Olvassa el a figyelmeztetést a megerősítő párbeszédpanelen, majd kattintson az **OK gombra**.

   A terv-hozzárendelés eltávolításakor a terv zárolásait is eltávolítja a rendszer. Az erőforrásokat újra törölheti egy megfelelő engedélyekkel rendelkező fiókkal.

1. Az Azure menüben válassza az **erőforráscsoportok** lehetőséget, majd válassza a **TestingBPLocks**lehetőséget.

1. Válassza a bal oldali **hozzáférés-vezérlés (iam)** lapot, majd válassza ki a **szerepkör-hozzárendelések** lapot.

Az erőforráscsoport biztonsága azt mutatja, hogy a terv hozzárendelése már nem rendelkezik _tulajdonosi_ hozzáféréssel.

Ha a **terv-hozzárendelés eltávolítása sikeres** portál értesítés jelenik meg, lépjen a következő lépésre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült ezzel az Oktatóanyaggal, törölje ezeket az erőforrásokat:

- Erőforráscsoport _TestingBPLocks_
- Terv definíciója _zárolva – storageaccount_

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan teheti elérhetővé az Azure-tervezetekkel üzembe helyezett új erőforrásokat. Ha többet szeretne megtudni az Azure-tervezetekről, folytassa a terv életciklusával foglalkozó cikkel.

> [!div class="nextstepaction"]
> [A terv életciklusának megismerése](../concepts/lifecycle.md)