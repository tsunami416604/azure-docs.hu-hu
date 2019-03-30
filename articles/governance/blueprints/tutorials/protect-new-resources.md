---
title: Új erőforrások védelme a tervek erőforrás-zárolásával
description: Ismerje meg, hogyan használhatja az Azure-tervek erőforrászárat csak olvasható, és ne törölje a védelme érdekében az újonnan üzembe helyezett erőforrásokat.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f39d59ef7ab3f555637aef69b301a0e77c00fc24
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629216"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Az Azure-tervek erőforrászárat új erőforrások védelme

Az Azure tervezetek [erőforrászárat](../concepts/resource-locking.md) módosítástól, még akkor is el olyan fiók az újonnan üzembe helyezett erőforrások elleni lehetővé teszik, hogy a _tulajdonosa_ szerepkör. Ez a védelem is hozzáadhatók a tervezet-definícióban egy Resource Manager-sablon összetevő által létrehozott erőforrásokat.

A következő lépéseket tartalmazza:

> [!div class="checklist"]
> - Hozzon létre egy új tervezetdefiníciót
> - Jelölje meg a tervezet definíciója adható meg **közzétett**
> - Egy meglévő előfizetéshez a tervezetdefiníció hozzárendelése
> - Vizsgálja meg az új erőforráscsoport
> - A Zárolás eltávolítása a tervrajz hozzárendelésének megszüntetése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-new-blueprint-definition"></a>Új tervezetdefiníció létrehozása

Először hozza létre az új tervezetdefiníciót.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza a **létrehozás** gomb alatt _tervrajz létrehozása_.

1. Keresse meg a **üres tervezet** felső részén a lapot, majd válassza a tervezet minta **üres tervezet kezdje**.

1. Adja meg a _alapjai_ a tervezet-minta:

   - **Tervrajz neve**: Adja meg a tervezet-minta a példányának nevét. Ebben az oktatóanyagban a nevét használjuk _zárolva storageaccount_.
   - **Leírás tervezetet**: Ismerteti a tervezetdefiníciót. Használja a "tesztelési tervezet erőforrás esetében a telepített erőforrások zárolása."
   - **Definíció helye**: Használja a három pontra, és válassza ki a felügyeleti csoport vagy az előfizetés, a tervezetdefiníciót spóroláshoz.

1. Válassza ki a _összetevők_ fülre az oldal tetején lévő vagy **tovább: Összetevők** az oldal alján.

1. Előfizetés hozzáadása az erőforráscsoportot: Válassza ki a **+ Hozzáadás összetevő...**  sor alatt **előfizetés**.
   Az _Összetevőtípus_ értékeként válassza az „Erőforráscsoport” lehetőséget. Állítsa be a _összetevő megjelenített neve_ való **RGtoLock**.
   Hagyja üresen az _Erőforráscsoport neve_ és a _Hely_ mezőket, de ellenőrizze, hogy mindkét tulajdonsághoz be van jelölve az azokat **dinamikus paraméterré** tevő jelölőnégyzet. Az összetevőt a **Hozzáadás** lehetőségre kattintva adhatja hozzá a tervhez.

1. Adja hozzá az erőforráscsoporthoz tartozó sablon: Válassza ki a **+ Hozzáadás összetevő...** a sor a **RGtoLock** bejegyzés. Az _Összetevőtípus_ értékeként válassza az „Azure Resource Manager-sablon” lehetőséget, az _Összetevő megjelenített neve_ tulajdonságnak állítsa be a „StorageAccount” értéket, és hagyja üresen a _Leírás_ mezőt. A szerkesztőmező **Sablon** lapján illessze be az alábbi Resource Manager-sablont. Válassza ki a sablont a beillesztés után **Hozzáadás** az összetevő hozzáadása a tervezethez.

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

1. Válassza ki **Piszkozat mentése** az oldal alján.

Ebben a lépésben a tervezetdefiníciót hoz létre a kiválasztott felügyeleti csoportba vagy előfizetésbe.

Miután a **mentése sikerült a tervezetdefiníció** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="publish-the-blueprint-definition"></a>Közzétenni a tervezetdefiníciót

A tervezetdefiníció létrehozása megtörtént a környezetben. A létrehozást **Draft** módban kell lennie, és **közzétett** előtt az hozzárendelve, és telepítve.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a _zárolva storageaccount_ tervezetet definícióját, és válassza ki azt.

1. Válassza ki **közzététel tervezet** az oldal tetején. Adja meg az új ablaktáblán a jobb oldali **verzió** , _1.0_. Ez a tulajdonság hasznos Ha később egy módosítása. Adja meg **megjegyzések módosítása** például az "első verziója közzé a tervezet üzembe helyezett erőforrások zárolása." Válassza ki **közzététel** az oldal alján.

Ez a lépés lehetővé teszi a tervezet hozzárendelése egy előfizetést. Közzététele után továbbra is módosíthatók. További módosításokat kell közzétenni egy új **verzió** értéket nyomon követéséhez az azonos tervezetdefiníció különböző verziói közötti különbségeket.

Egyszer a **közzététel sikerült definíció tervezetet** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="assign-the-blueprint-definition"></a>A tervezetdefiníció hozzárendelése

Miután sikeresen befejeződött a tervezetdefiníciót **közzétett**, előfizetésre történő mentése felügyeleti csoporton belüli rendelhetők. Ez a lépés nem, melyekben a paraméterek vannak-e adva az, hogy az egyes telepítések a tervrajz-definíció egyedi.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a _zárolva storageaccount_ tervezetet definícióját, és válassza ki azt.

1. Válassza ki **tervezet hozzárendelése** a tervrajz-definíció lap tetején.

1. Adja meg a paraméter értékét a tervezet-hozzárendelést:

   - Alapvető beállítások

     - **Előfizetések**: Válasszon ki egy vagy több az előfizetéseket, amelyek a felügyeleti csoportban, mentve a tervezet-definíciót. Ha egynél több előfizetéssel, hozzárendelést a megadott paraméterek használatával hozható létre.
     - **Hozzárendelés neve**: Az Ön a tervrajz-definíció neve alapján előre megadott név. Ezt a hozzárendelést zárolását az új erőforráscsoport képviselik, ezért módosítsa a hozzárendelési nevet szeretnénk _hozzárendelés-zárolt-tárfiók-TestingBPLocks_.
     - **Hely**: Válassza ki a régiót, a felügyelt identitás kell létrehozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../active-directory/managed-identities-azure-resources/overview.md).
       A jelen oktatóanyag esetében válassza ki a _USA keleti RÉGIÓJA 2_.
     - **Tervrajz-definíció verziója**: Válassza ki a **közzétett** verzió _1.0_ a tervrajz-definíció.

   - Hozzárendelés zárolása

     Válassza ki a _csak olvasható_ tervezet zárolási üzemmódban. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ lehetőséget. További információkért lásd: [felügyelt identitások](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Összetevő paraméterei

     A lehívandó összetevő definiálva van ebben a szakaszban definiált paraméterek érvényesek. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) mivel azok van megadva a a tervezet-hozzárendelés során. Minden egyes összetevő, állítsa a paraméter értéke határozzák meg a a **érték** oszlop.

     |Elemnév|Összetevő típusa|Paraméter neve|Érték|Leírás|
     |-|-|-|-|-|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Name (Név)|TestingBPLocks|Meghatározza a tervezet zárolások a alkalmazni az új erőforráscsoport nevét.|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Hely|USA nyugati régiója, 2.|Tervrajz zárolások a alkalmazni az új erőforráscsoport helye határozza meg.|
     |Tárfiók|Resource Manager-sablon|storageAccountType (StorageAccount)|Standard_GRS|Válassza ki a tároló-Termékváltozat. Alapértelmezett érték _Standard_LRS_.|

1. Után minden paraméter van megadva, válassza a **hozzárendelése** az oldal alján.

Ebben a lépésben üzembe helyezi a definiált erőforrásokat, és konfigurálja a kijelölt **zárolási hozzárendelés**. Tervrajz zárolások alkalmazásához akár 30 percet is igénybe vehet.

Miután a **sikeres tervezetdefiníció hozzárendelése** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="inspect-resources-deployed-by-the-assignment"></a>A hozzárendelés által üzembe helyezett erőforrások vizsgálata

A hozzárendelés az erőforráscsoport létrehozása _TestingBPLocks_ és a Resource Manager sablon összetevő által üzembe helyezett tárfiókot. Az új erőforráscsoport és a kiválasztott zárolási állapot hozzárendelés részleteit megjelenítő oldalon jelennek meg.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **hozzárendelt tervezetek** a bal oldalon. A szűrők segítségével keresse meg a _hozzárendelés-zárolt-tárfiók-TestingBPLocks_ tervezetet hozzárendelést, és válassza ki azt.

   Ezen az oldalon láthatjuk a hozzárendelés sikerült, és az erőforrások lettek telepítve az új tervezet zárolási állapotú. A hozzárendelés frissül, ha a **hozzárendelési művelet** legördülő minden definíció verzió az üzembe helyezéssel kapcsolatos részleteket jeleníti meg. Az erőforráscsoport, ha rájuk kattint közvetlenül a Tulajdonságok oldal megnyitásához.

1. Válassza ki a **TestingBPLocks** erőforráscsoportot.

1. Válassza ki a **hozzáférés-vezérlés (IAM)** a bal oldalon, majd a **szerepkör-hozzárendelések** fülre.

   Itt láthatjuk, hogy a _hozzárendelés-zárolt-tárfiók-TestingBPLocks_ tervezet-hozzárendelést rendelkezik a _tulajdonosa_ , mert a szerepkör üzembe helyezéséhez és az erőforráscsoport zárolása lett megadva.

1. Válassza ki a **hozzárendelések megtagadása** fülre.

   A tervezet-hozzárendelést, létrehozott egy [hozzárendelés megtagadása](../../../role-based-access-control/deny-assignments.md) kényszerítése a telepített erőforráscsoportot, amelybe a a _csak olvasható_ tervezet zárolási mód. A megtagadási hozzárendelés megakadályozza, hogy valaki megfelelő jogosultságokkal a a _szerepkör-hozzárendelések_ bizonyos műveleteket, fülre. A megtagadási hozzárendelés befolyásolja _összes rendszerbiztonsági tag_.

   További információ a rendszerbiztonsági tag kizárása egy megtagadási-hozzárendelést: [tervezetek erőforrás zárolása](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Válassza ki a Megtagadás hozzárendelést, majd válassza ki a **megtagadva engedélyek** a bal oldalon.

   A megtagadási hozzárendelés megakadályozza, hogy az összes műveletet a **\*** és **művelet** konfiguráció, de lehetővé teszi, hogy olvasási hozzáférés kizárásával  **\* /olvasási**keresztül **NotActions**.

1. Válassza ki az Azure portal webhely-navigációs, **TestingBPLocks - hozzáférés-vezérlés (IAM)**. Válassza ki a **áttekintése** a bal oldalon, majd a **erőforráscsoport törlése** gombra. Adja meg a nevét _TestingBPLocks_ a delete és select **törlése** a panel alján.

   A portál értesítései **TestingBPLocks sikertelen erőforráscsoport törlése** jelenik meg. A hiba-állapotokat, amely a fiók rendelkezik engedéllyel az erőforráscsoport törlése közben megtagadja a hozzáférést a tervezet-hozzárendelést. Ne feledje, hogy kiválasztott a _csak olvasható_ tervezet zárolási mód tervezet-hozzárendelés során. A tervezet zárolása megakadályozza, hogy, engedéllyel rendelkező fiók még _tulajdonosa_, az erőforrás törlését. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

Ezek a lépések bemutatják, hogy a telepített erőforrások védettek tervezet zárolásokat, amely ebben az esetben a nemkívánatos, amelytől a törlés, még akkor is engedéllyel rendelkező fiók.

## <a name="unassign-the-blueprint"></a>A tervrajz hozzárendelésének megszüntetése

Az utolsó lépés, hogy távolítsa el a tervezetdefiníciót hozzárendelését. A hozzárendelés eltávolítása nem távolítja el a megszáradásukig összetevőket.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **hozzárendelt tervezetek** a bal oldalon. A szűrők segítségével keresse meg a _hozzárendelés-zárolt-tárfiók-TestingBPLocks_ tervezetet hozzárendelést, és válassza ki azt.

1. Válassza ki a **kijelölés megszüntetése tervezet** gombra a lap tetején. Olvassa el a figyelmeztetést a megerősítő párbeszédpanelen, majd válassza a **OK**.

   Az eltávolított tervezet-hozzárendelést a tervezet zárolása is el lesznek távolítva. A létrehozott erőforrásokat ismét engedélyekkel rendelkező fiók lehet törölni.

1. Válassza ki **erőforráscsoportok** az Azure menüjében, majd válassza ki **TestingBPLocks**.

1. Válassza ki a **hozzáférés-vezérlés (IAM)** a bal oldalon, majd a **szerepkör-hozzárendelések** fülre.

A biztonság, az erőforráscsoport látható, hogy rendelkezik-e már a tervezet-hozzárendelést _tulajdonosa_ hozzáférést.

Miután a **eltávolítása tervezet-hozzárendelés sikerült** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag befejezésekor törölje az alábbi forrásanyagokat:

- Erőforráscsoport _TestingBPLocks_
- Definíció tervezetet _storageaccount zárolva van_

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).