---
title: 'Oktatóanyag: Új erőforrások védelme zárolásokkal'
description: Ebben az oktatóanyagban az Azure Blueprints erőforrás zárolási lehetőségek csak olvasható és a Ne törölje az újonnan üzembe helyezett erőforrások védelme.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327448"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Oktatóanyag: Új erőforrások védelme az Azure Blueprints erőforrászárolásokkal

Az Azure Blueprints [erőforrás-zárolások](../concepts/resource-locking.md)használatával megvédheti az újonnan üzembe helyezett erőforrásokat attól, hogy illetéktelenül illetéktelenül módosítsák őket, még a _Tulajdonos_ szerepkörrel rendelkező fiókkal is. Ezt a védelmet hozzáadhatja az Erőforrás-kezelő sablonösszetevő által létrehozott erőforrások tervezetdefinícióiban.

Ebben az oktatóanyagban hajtsa végre az alábbi lépéseket:

> [!div class="checklist"]
> - Tervdefiníció létrehozása
> - A tervrajz definíciójának **megjelölése közzétettként**
> - A tervezetdefiníció hozzárendelése meglévő előfizetéshez
> - Az új erőforráscsoport vizsgálata
> - A zárolások eltávolításához rendelje vissza a tervrajzot

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-a-blueprint-definition"></a>Tervdefiníció létrehozása

Először hozza létre a tervrajz-definíciót.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** a **tervrajz létrehozása**csoportban lehetőséget.

1. Keresse meg a **Blank Blueprint** tervrajz minta tetején az oldal. Válassza **a Kezdés üres tervrajzsal**lehetőséget.

1. Adja meg ezt az információt az **Alapok** lapon:

   - **Tervrajz neve:** Adja meg a tervezet minta példányának nevét. Ebben az oktatóanyagban a **zárolt tárfiók nevet használjuk.**
   - **Terv leírása**: Adjon meg egy leírást a terv definícióját. Az **üzembe helyezett erőforrások tervezetének zárolásának teszteléséhez**használható.
   - **Definíció helye:** Jelölje ki a három pontot gombot (...), majd válassza ki a felügyeleti csoportot vagy előfizetést a tervezet definíciójának mentéséhez.

1. Válassza az **Eltérések** lapot a lap tetején, vagy válassza a **Tovább: Eltérések** elemet a lap alján.

1. Erőforráscsoport hozzáadása az előfizetés szintjén:
   1. Válassza a **Műtermék hozzáadása** sor lehetőséget az **Előfizetés csoportban.**
   1. Válassza az **Erőforráscsoport** lehetőséget **a Műtermék típus csoportban.**
   1. Állítsa a **műtermék megjelenítendő nevét** **RGtoLock**-ra.
   1. Hagyja üresen az **Erőforráscsoport neve** és **helye** jelölőnégyzetet, de győződjön meg arról, hogy a jelölőnégyzet minden tulajdonságon be van jelölve, hogy **azok dinamikus paraméterek legyenek.**
   1. Válassza **a Hozzáadás** lehetőséget, ha hozzá szeretné adni az összetevőt a tervrajzhoz.

1. Sablon hozzáadása az erőforráscsoport hoz:
   1. Jelölje ki az **RGtoLock** bejegyzés alatt a **Műtermék hozzáadása** sort.
   1. Válassza az **Azure Resource Manager sablont** **a Műtermék típus csoportban,** állítsa be a **műtermék megjelenítendő nevét** **storageaccount**értékre, és hagyja üresen **a Leírás lehetőséget.**
   1. A **Sablon** lapon illessze be a következő Erőforrás-kezelő sablont a szerkesztőmezőbe.
      Miután beillesztte a sablont, válassza a **Hozzáadás** lehetőséget, hogy hozzáadja az összetevőt a tervrajzhoz.

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

1. Válassza a **Piszkozat mentése** lehetőséget a lap alján.

Ez a lépés létrehozza a tervezet definícióját a kiválasztott felügyeleti csoportban vagy előfizetésben.

Miután a **mentési tervezet definíciója sikeres** portál értesítés jelenik meg, folytassa a következő lépéssel.

## <a name="publish-the-blueprint-definition"></a>A tervezetdefiníció közzététele

A tervmeghatározás létrehozása a környezetben. **Vázlat** módban jön létre, és közzé kell tenni, mielőtt hozzárendelhető és üzembe helyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a **zárolt tárfiók** tervrajz-definícióját, majd jelölje ki.

1. Válassza a **Tervrajz közzététele** lehetőséget a lap tetején. A jobb oldali új ablaktáblában írja be az **1.0** értéket **verzióként.** Ez a tulajdonság akkor hasznos, ha később módosítja a hibát. Írja be **a Változások megjegyzéseket**, például **az első közzétett verzió talapzaton üzembe helyezett erőforrások zárolásához.** Ezután válassza a **Közzététel** lehetőséget a lap alján.

Ez a lépés lehetővé teszi, hogy a tervezet egy előfizetéshez. A tervezetdefiníció közzététele után is módosíthatja. Ha módosításokat hajt végre, közzé kell tennie a definíciót egy új verzióértékkel az ugyanazon tervezetdefiníció verziói közötti különbségek nyomon követéséhez.

Miután a **közzétételi tervezet definíciója sikeres** portál értesítés jelenik meg, folytassa a következő lépéssel.

## <a name="assign-the-blueprint-definition"></a>A tervezetdefiníció hozzárendelése

A tervezet definíciójának közzététele után hozzárendelheti egy előfizetéshez a felügyeleti csoporton belül, ahol mentette. Ebben a lépésben paramétereket ad meg, hogy a tervezet definíciójának minden egyes központi telepítése egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a **zárolt tárfiók** tervrajz-definícióját, majd jelölje ki.

1. Válassza **a Blueprint hozzárendelése** lehetőséget a tervezetdefiníciós lap tetején.

1. Adja meg a tervezet hozzárendelésének paraméterértékeit:

   - **Alapvető beállítások**

     - **Előfizetések:** Válasszon ki egy vagy több olyan előfizetést, amely abban a felügyeleti csoportban található, ahol a tervezetdefiníciót mentette. Ha egynél több előfizetést választ ki, minden egyes előfizetéshez létrehoz egy hozzárendelést a megadott paraméterek használatával.
     - **Hozzárendelés neve**: A név előre ki van töltve a tervezetdefiníció neve alapján. Azt szeretnénk, hogy ez a hozzárendelés az új erőforráscsoport zárolását képviselje, ezért módosítsa a hozzárendelés nevét **hozzárendelés-zárolt-storageaccount-TestingBPLocks**-ra.
     - **Hely**: Válassza ki azt a régiót, ahol létre szeretné hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../../../active-directory/managed-identities-azure-resources/overview.md)
       Ehhez az oktatóanyaghoz válassza **az USA keleti része 2**lehetőséget.
     - **Blueprint definíciós verzió:** Válassza ki a tervezet definíciójának közzétett **1.0-s** verzióját.

   - **Hozzárendelés zárolása**

     Válassza az **Írásvédett** tervzárolási módot. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - **Felügyelt identitás**

     Használja az alapértelmezett beállítást: **Rendszer hozzárendelt**. További információt a felügyelt identitások című [témakörben talál.](../../../active-directory/managed-identities-azure-resources/overview.md)

   - **Műtermék-paraméterek**

     Az ebben a szakaszban meghatározott paraméterek azokra a műtermékre vonatkoznak, amelyek alatt definiálva vannak. Ezek a paraméterek [dinamikus paraméterek,](../concepts/parameters.md#dynamic-parameters) mert a terv hozzárendelése során vannak definiálva. Minden egyes műtermékhez állítsa be a paraméter értékét az **Érték** oszlopban látható értékre.

     |Műtermék neve|Műtermék típusa|Paraméter neve|Érték|Leírás|
     |-|-|-|-|-|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Név|TESZTELÉSBPLock|Az új erőforráscsoport nevét határozza meg, amelyre a tervezetzárolásokat alkalmazni szeretné.|
     |RGtoLock erőforráscsoport|Erőforráscsoport|Hely|USA nyugati régiója, 2.|Az új erőforráscsoport helyét határozza meg, amelyre a tervezetzárolásokat alkalmazni szeretné.|
     |StorageAccount|Resource Manager-sablon|storageAccountType (StorageAccount)|Standard_GRS|A tároló termékváltozat. Az alapértelmezett érték _a Standard_LRS_.|

1. Miután megadta az összes paramétert, válassza a **Hozzárendelés** lehetőséget a lap alján.

Ez a lépés telepíti a definiált erőforrásokat, és konfigurálja a kijelölt **Zárolási hozzárendelést.** A tervrajz zárolásának alkalmazása akár 30 percet is igénybe vehet.

Miután a **hozzárendelési tervezet definíciója sikerült** portál értesítés jelenik meg, folytassa a következő lépéssel.

## <a name="inspect-resources-deployed-by-the-assignment"></a>A hozzárendelés által üzembe helyezett erőforrások vizsgálata

A hozzárendelés létrehozza a _TestingBPLocks_ erőforráscsoportot és az Erőforrás-kezelő sablonmű által üzembe helyezett tárfiókot. Az új erőforráscsoport és a kijelölt zárolási állapot megjelenik a hozzárendelés részletei lapon.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **Hozzárendelt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a **hozzárendelés-zárolt-storageaccount-TestingBPLocks** tervezet hozzárendelés, majd jelölje ki azt.

   Ebből a lapból láthatjuk, hogy a hozzárendelés sikeres volt, és hogy az erőforrások at az új tervezet zárolási állapottal telepítették. Ha a hozzárendelés frissül, a **Hozzárendelési művelet** legördülő menü az egyes definíciós verziók központi telepítésével kapcsolatos részleteket jeleníti meg. A tulajdonságlap megnyitásához kiválaszthatja az erőforráscsoportot.

1. Válassza ki a **TestingBPLocks** erőforráscsoportot.

1. Válassza a **hozzáférés-vezérlés (IAM)** lapot a bal oldalon. Ezután válassza a **Szerepkör-hozzárendelések** lapot.

   Itt azt látjuk, hogy a _hozzárendelés-zárolt-storageaccount-TestingBPLocks_ tervezet hozzárendelés a _tulajdonos_ szerepkört. Ez a szerepkör, mert ezt a szerepkört használták az erőforráscsoport üzembe helyezéséhez és zárolásához.

1. Válassza a **Hozzárendelések megtagadása** lapot.

   A tervezet-hozzárendelés létrehozott egy [megtagadási hozzárendelést](../../../role-based-access-control/deny-assignments.md) az üzembe helyezett erőforráscsoporton az **írásvédett** tervezet zárolási mód kényszerítéséhez. A megtagadási hozzárendelés megakadályozza, hogy a **szerepkör-hozzárendelések** lapon megfelelő jogosultságokkal rendelkező személy meghatározott műveleteket hajtson végre. A megtagadási hozzárendelés _az összes megbízóra_hatással van.

   A tagmegbízásból való kizárásáról a [tervrajzok erőforrászárolás című](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)témakörben talál tájékoztatást.

1. Jelölje ki a megtagadási **hozzárendelést,** majd a bal oldalon a Megtagadott engedélyek lapot.

   A megtagadási hozzárendelés megakadályozza az **\*** összes műveletet a és a **művelet** konfigurációval, de lehetővé teszi az olvasási hozzáférést a ** \*/read** via **NotActions**kizárásával.

1. Az Azure Portalon a zsemlemorzsa válassza **tesztelésBPLocks - hozzáférés-vezérlés (IAM)** lehetőséget. Ezután válassza az **Áttekintés** lapot a bal oldalon, majd az **Erőforráscsoport törlése** gombot. Írja be a **TestingBPLocks** nevet a törlés megerősítéséhez, majd válassza a **Törlés** lehetőséget az ablaktábla alján.

   Megjelenik a portál **értesítése: A TestingBPLocks erőforráscsoport törlése sikertelen.** A hiba azt állítja, hogy bár a fiók rendelkezik engedéllyel az erőforráscsoport törléséhez, a hozzáférést a tervezet hozzárendelése megtagadja. Ne feledje, hogy az **írásvédett** tervrögzítési módot választottuk a tervleési hozzárendelés során. A tervezet zárolása megakadályozza, hogy egy engedéllyel rendelkező fiók , még _a Tulajdonos_is , de az erőforrás törlése. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

Ezek a lépések azt mutatják, hogy az üzembe helyezett erőforrások most már védett tervezet zárolások, amelyek megakadályozzák a nem kívánt törlés, még egy fiók, amely rendelkezik az erőforrások törlésére engedéllyel.

## <a name="unassign-the-blueprint"></a>A tervrajz hozzárendelésének visszavonása

Az utolsó lépés a tervezetdefiníció hozzárendelésének eltávolítása. A hozzárendelés eltávolítása nem távolítja el a társított összetevőket.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **Hozzárendelt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a **hozzárendelés-zárolt-storageaccount-TestingBPLocks** tervezet hozzárendelés, majd jelölje ki azt.

1. A lap tetején válassza a **Hozzárendelés visszavonása tervrajz** lehetőséget. Olvassa el a figyelmeztetést a megerősítést kérő párbeszédpanelen, majd kattintson az **OK gombra.**

   A tervrajz-hozzárendelés eltávolításakor a tervrajzzárolások is törlődnek. Az erőforrásokat ismét törölheti a megfelelő engedélyekkel rendelkező fiók.

1. Válassza az Azure menü **Erőforráscsoportok parancsát,** majd a **TestingBPLocks**lehetőséget.

1. Válassza a bal oldali **Hozzáférés-vezérlés (IAM)** lapot, majd a **Szerepkör-hozzárendelések** lapot.

Az erőforráscsoport biztonsága azt mutatja, hogy a tervezet-hozzárendelés már nem rendelkezik _tulajdonosi_ hozzáféréssel.

Miután a **tervhozzárendelés eltávolítása sikeres** portálértesítés jelenik meg, folytassa a következő lépéssel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az oktatóanyagot, törölje ezeket az erőforrásokat:

- Erőforráscsoport _TesztelésBPLocks_
- Blueprint-definíció _zárolt tárolófiók_

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan védheti meg az Azure Blueprints használatával üzembe helyezett új erőforrásokat. Ha többet szeretne megtudni az Azure Blueprints, folytassa a blueprint életciklus-cikk.

> [!div class="nextstepaction"]
> [További információ a tervterv életciklusáról](../concepts/lifecycle.md)