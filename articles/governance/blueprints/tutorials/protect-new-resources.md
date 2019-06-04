---
title: Oktatóanyag – a tervezet erőforrás-zárolások új erőforrások védelme
description: Ez az oktatóanyag azt ismerteti, hogyan használhatók az Azure tervezetek erőforrás zárolása beállításai csak olvasható, és ne törölje a védelme érdekében az újonnan üzembe helyezett erőforrásokat.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 274c437acd8df50d631727fc352c4b9ebecead18
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479972"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Oktatóanyag: Az Azure-tervek erőforrászárat új erőforrások védelme

Az Azure-tervek [erőforrászárat](../concepts/resource-locking.md), újonnan üzembe helyezett erőforrások védheti a tartalom, még akkor is el olyan fiók, az a _tulajdonosa_ szerepkör. Ez a védelem az erőforrások egy Resource Manager-sablon összetevő által létrehozott tervezetdefiníciók adhat hozzá.

Ebben az oktatóanyagban befejezheti ezeket a lépéseket:

> [!div class="checklist"]
> - Tervrajz-definíció létrehozása
> - Jelölje meg a tervezet definíciója adható meg **közzétett**
> - Egy meglévő előfizetéshez a tervezetdefiníció hozzárendelése
> - Vizsgálja meg az új erőforráscsoport
> - A Zárolás eltávolítása a tervrajz hozzárendelésének megszüntetése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-blueprint-definition"></a>Tervrajz-definíció létrehozása

Először hozza létre a tervezetdefiníciót.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza **létrehozás** alatt **tervrajz létrehozása**.

1. Keresse meg a **üres tervezet** tervezet minta az oldal tetején. Válassza ki **üres tervezet kezdődnie**.

1. Adja meg az adatokat a **alapjai** lapon:

   - **Tervrajz neve**: Adja meg a tervezet-minta a példányának nevét. Ebben az oktatóanyagban a nevét használjuk **zárolva storageaccount**.
   - **Leírás tervezetet**: Adjon hozzá egy leírást a tervezetdefiníciót. Használat **a tesztelési tervezet erőforrás zárolását az üzembe helyezett erőforrások**.
   - **Definíció helye**: Válassza ki a három pont gombra (…), és válassza ki a felügyeleti csoport vagy az előfizetést, a tervezetdefiníciót spóroláshoz.

1. Válassza ki a **összetevők** fülre az oldal tetején, vagy válasszon **tovább: Összetevők** az oldal alján.

1. Adjon hozzá egy erőforráscsoportot az előfizetés szintjén:
   1. Válassza ki a **Hozzáadás összetevő** sor alatt **előfizetés**.
   1. Válassza ki **erőforráscsoport** alatt **összetevőtípussal**.
   1. Állítsa be a **összetevő megjelenített neve** való **RGtoLock**.
   1. Hagyja a **erőforráscsoport-név** és **hely** mezőbe üres, de győződjön meg arról, a jelölőnégyzet be van jelölve az egyes tulajdonságok, így **dinamikus paraméterek**.
   1. Válassza ki **Hozzáadás** összetevő hozzáadása a tervezethez.

1. Adja hozzá az erőforráscsoporthoz tartozó sablon:
   1. Válassza ki a **Hozzáadás összetevő** sor alatt a **RGtoLock** bejegyzés. 
   1. Válassza ki **Azure Resource Manager-sablon** alatt **összetevő típusa**állítsa be **összetevő megjelenített neve** való **StorageAccount**, és hagyja  **Leírás** üres. 
   1. Az a **sablon** fülre, illessze be a következő Resource Manager-sablon szerkesztő. Miután illessze be a sablonban, válassza ki **Hozzáadás** összetevő hozzáadása a tervezet.

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

Miután a **mentése sikerült a tervezetdefiníció** portál értesítés jelenik meg, nyissa meg a következő lépéssel.

## <a name="publish-the-blueprint-definition"></a>Közzétenni a tervezetdefiníciót

A tervezetdefiníció létrehozása megtörtént a környezetben. A létrehozást **Draft** mód és azt hozzárendelve, és üzembe helyezett előtt közzé kell tenni.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a **zárolva storageaccount** tervezetet definícióját, és jelölje ki.

1. Válassza ki **közzététel tervezet** az oldal tetején. Adja meg az új ablaktáblán a jobb oldali **1.0** , a **verzió**. Ez a tulajdonság akkor hasznos, ha később lehet módosítani. Adja meg **megjegyzések módosítása**, mint például **első verziója közzétett tervezet üzembe helyezett erőforrások zárolása**. Válassza ki **közzététel** az oldal alján.

Ez a lépés lehetővé teszi a tervezet hozzárendelése egy előfizetést. A tervezetdefiníció közzététele után továbbra is végezhet módosításokat. Ha módosít, kell közzétenni a definíció egy új verzió értékkel nyomon követéséhez az azonos tervezetdefiníció verziója közötti különbségek.

Után az **közzététel sikerült definíció tervezetet** portál értesítés jelenik meg, nyissa meg a következő lépéssel.

## <a name="assign-the-blueprint-definition"></a>A tervezetdefiníció hozzárendelése

A tervezetdefiníció közzététele után hozzárendelheti egy előfizetést a felügyeleti csoportban, hogy hová mentette. Ebben a lépésben adja meg, hogy az egyes telepítések a tervrajz-definíció egyedi paramétereket.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a **zárolva storageaccount** tervezetet definícióját, és jelölje ki.

1. Válassza ki **tervezet hozzárendelése** a tervrajz-definíció lap tetején.

1. Adja meg a paraméter értékét a tervezet-hozzárendelést:

   - **Alapvető beállítások**

     - **Előfizetések**: Válasszon legalább egy, az előfizetéseket, amelyek a felügyeleti csoportban, ahová mentette a tervezetdefiníciót. Ha több előfizetéssel, hozzárendelés az egyes előfizetésekhez, akkor adja meg a paraméterek használatával hozható létre.
     - **Hozzárendelés neve**: A név ki van töltve a tervrajz-definíció neve alapján. Ezt a hozzárendelést zárolását az új erőforráscsoport képviselik, ezért módosítsa a hozzárendelési nevet szeretnénk **hozzárendelés-zárolt-tárfiók-TestingBPLocks**.
     - **Hely**: Válasszon egy régiót, amelyben létrehozza a felügyelt identitás. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../active-directory/managed-identities-azure-resources/overview.md).
       A jelen oktatóanyag esetében válassza ki a **USA keleti RÉGIÓJA 2**.
     - **Tervrajz-definíció verziója**: Válassza ki a közzétett verziót **1.0** a tervrajz-definíció.

   - **Zárolási hozzárendelés**

     Válassza ki a **csak olvasható** tervezet zárolási üzemmódban. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - **Managed Identity**

     Használja az alapértelmezett beállítást: **Rendszer által hozzárendelt**. További információkért lásd: [felügyelt identitások](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Összetevő-paraméterek**

     A lehívandó összetevő, amellyel definiálva van ebben a szakaszban definiált paraméterek érvényesek. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) mert azok a a tervezet-hozzárendelés során van definiálva. Minden egyes összetevő beállításra a paraméter értéke megjelenik a **érték** oszlop.

     |Összetevő neve|Összetevő típusa|Paraméter neve|Érték|Leírás|
     |-|-|-|-|-|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Name (Név)|TestingBPLocks|Meghatározza a tervezet zárolások a alkalmazni az új erőforráscsoport nevét.|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Location egység|USA nyugati régiója, 2.|Tervrajz zárolások a alkalmazni az új erőforráscsoport helye határozza meg.|
     |StorageAccount|Resource Manager-sablon|storageAccountType (StorageAccount)|Standard_GRS|A tároló-Termékváltozat. Az alapértelmezett érték _Standard_LRS_.|

1. Miután megadta az összes paramétert, válassza ki a **hozzárendelése** az oldal alján.

Ebben a lépésben üzembe helyezi a definiált erőforrásokat, és konfigurálja a kijelölt **zárolási hozzárendelés**. Tervrajz zárolások alkalmazásához akár 30 percet is igénybe vehet.

Miután a **a tervezetdefiníció hozzárendelése sikerült** portál értesítés jelenik meg, nyissa meg a következő lépéssel.

## <a name="inspect-resources-deployed-by-the-assignment"></a>A hozzárendelés által üzembe helyezett erőforrások vizsgálata

A hozzárendelés az erőforráscsoport jön létre _TestingBPLocks_ és a Resource Manager sablon összetevő által üzembe helyezett tárfiókot. Az új erőforráscsoport és a kiválasztott zárolási állapot hozzárendelés részleteit megjelenítő oldalon jelennek meg.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **hozzárendelt tervezetek** a bal oldalon. A szűrők segítségével keresse meg a **hozzárendelés-zárolt-tárfiók-TestingBPLocks** tervezetet hozzárendelést, és jelölje ki.

   Ezen az oldalon láthatjuk, hogy a hozzárendelés sikeres volt, és, hogy az erőforrások telepített-e az új tervezet zárolási állapotú. A hozzárendelés frissül, ha a **hozzárendelési művelet** legördülő minden definíció verzió az üzembe helyezéssel kapcsolatos részleteket jeleníti meg. Kiválaszthatja az erőforráscsoportot, amelybe a Tulajdonságok oldal megnyitásához.

1. Válassza ki a **TestingBPLocks** erőforráscsoportot.

1. Válassza ki a **hozzáférés-vezérlés (IAM)** a bal oldalon. Válassza ki a **szerepkör-hozzárendelések** fülre.

   Itt láthatjuk, hogy a _hozzárendelés-zárolt-tárfiók-TestingBPLocks_ tervezet-hozzárendelést rendelkezik a _tulajdonosa_ szerepkör. Mivel ez a szerepkör üzembe helyezéséhez és az erőforráscsoport zárolása használt rendelkezik ehhez a szerepkörhöz.

1. Válassza ki a **hozzárendelések megtagadása** fülre.

   A tervezet-hozzárendelést, létrehozott egy [hozzárendelés megtagadása](../../../role-based-access-control/deny-assignments.md) kényszerítése a telepített erőforráscsoportot, amelybe a a **csak olvasható** tervezet zárolási mód. A megtagadási hozzárendelés megakadályozza, hogy valaki megfelelő jogosultságokkal a a **szerepkör-hozzárendelések** bizonyos műveleteket, fülre. A megtagadási hozzárendelés befolyásolja _összes rendszerbiztonsági tag_.

   További információ a rendszerbiztonsági tag kizárása egy megtagadási-hozzárendelést: [tervezetek erőforrás zárolása](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Válassza ki a Megtagadás hozzárendelést, és válassza a **megtagadva engedélyek** a bal oldalon.

   A megtagadási hozzárendelés megakadályozza, hogy az összes műveletet a **\*** és **művelet** konfigurációs van, de lehetővé teszi, hogy olvasási hozzáférés kizárásával  **\* /olvasási**keresztül **NotActions**.

1. Válassza ki az Azure portal webhely-navigációs, **TestingBPLocks - hozzáférés-vezérlés (IAM)** . Válassza ki a **áttekintése** a bal oldalon, majd a **erőforráscsoport törlése** gombra. Adja meg a nevét **TestingBPLocks** a törlés megerősítéséhez, majd kattintson **törlése** a panel alján.

   A portál értesítései **TestingBPLocks sikertelen erőforráscsoport törlése** jelenik meg. A hibaállapothoz, amely a fiók rendelkezik a törölheti az erőforráscsoportot, a hozzáférés megtagadva a tervezet-hozzárendelés. Ne feledje, hogy kiválasztott a **csak olvasható** tervezet zárolási mód tervezet-hozzárendelés során. A tervezet zárolása megakadályozza, hogy, engedéllyel rendelkező fiók még _tulajdonosa_, az erőforrás törlését. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

Ezek a lépések bemutatják, hogy a telepített erőforrások védettek tervezet zárolásokat, amelyek meggátolják a nemkívánatos, amelytől a törlés, akár egy fiókot, amely jogosult az erőforrások törlése.

## <a name="unassign-the-blueprint"></a>A tervrajz hozzárendelésének megszüntetése

Az utolsó lépés, hogy távolítsa el a tervezetdefiníciót hozzárendelését. A hozzárendelés eltávolítása nem távolítja el a kapcsolódó összetevőket.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **hozzárendelt tervezetek** a bal oldalon. A szűrők segítségével keresse meg a **hozzárendelés-zárolt-tárfiók-TestingBPLocks** tervezetet hozzárendelést, és jelölje ki.

1. Válassza ki **kijelölés megszüntetése tervezet** az oldal tetején. Olvassa el a figyelmeztetést a művelet megerősítését kérő párbeszédpanelen, és válassza ki **OK**.

   A tervezet-hozzárendelés eltávolítása után a tervezet zárolása is törlődnek. Az erőforrás ismét megfelelő engedélyekkel rendelkező fiók lehet törölni.

1. Válassza ki **erőforráscsoportok** az Azure menüjében, és válassza ki a **TestingBPLocks**.

1. Válassza ki a **hozzáférés-vezérlés (IAM)** a bal oldalon, és válassza ki a **szerepkör-hozzárendelések** fülre.

A biztonság, az erőforráscsoport látható, hogy rendelkezik-e már a tervezet-hozzárendelést _tulajdonosa_ hozzáférést.

Miután a **eltávolítása tervezet-hozzárendelés sikerült** portál értesítés jelenik meg, nyissa meg a következő lépéssel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, ebben az oktatóanyagban, törölje ezeket az erőforrásokat:

- Erőforráscsoport _TestingBPLocks_
- Definíció tervezetet _storageaccount zárolva van_

## <a name="next-steps"></a>További lépések

- További információ a [életciklus tervezetet](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- Ismerje meg, hogyan használható [tervezetet erőforrás zárolása](../concepts/resource-locking.md).
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- [Hibaelhárítás](../troubleshoot/general.md) a a tervezet-hozzárendelés során.
