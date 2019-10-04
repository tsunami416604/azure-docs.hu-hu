---
title: A Azure Managed Applications felhasználói felületi definíciójának tesztelése | Microsoft Docs
description: Leírja, hogyan lehet tesztelni az Azure által felügyelt alkalmazásnak a portálon keresztül történő létrehozásához szükséges felhasználói élményt.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 54eb2df06df56c33e1a3cd74e7a4a93c07aab682
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575661"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>A portál felületének tesztelése Azure Managed Applications

Miután létrehozta a felügyelt alkalmazáshoz tartozó [createUiDefinition. JSON fájlt](create-uidefinition-overview.md) , tesztelni kell a felhasználói élményt. A tesztelés egyszerűsítése érdekében használjon egy sandbox-környezetet, amely betölti a fájlt a portálon. Nem kell ténylegesen telepítenie a felügyelt alkalmazást. A Sandbox a felhasználói felületet mutatja be a jelenlegi, teljes képernyős portálon. Vagy használhat parancsfájlt az illesztőfelület teszteléséhez. Ebben a cikkben mindkét megközelítés látható. A munkaterületet az ajánlott eljárással tekintheti meg a kezelőfelületen.

## <a name="prerequisites"></a>Előfeltételek

* Egy **createUiDefinition. JSON** fájl. Ha nem rendelkezik ezzel a fájllal, másolja a [](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)fájlt.

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="use-sandbox"></a>A homokozó használata

1. Nyissa meg a [felhasználói felület definíciójának létrehozása Sandboxot](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Homokozó megjelenítése](./media/test-createuidefinition/show-sandbox.png)

1. Cserélje le az üres definíciót a createUiDefinition. JSON fájl tartalmára. Válassza az **előnézet**lehetőséget.

   ![Előnézet kiválasztása](./media/test-createuidefinition/select-preview.png)

1. Megjelenik a létrehozott űrlap. Áttekintheti a felhasználói élményt, és kitöltheti az értékeket.

   ![Űrlap megjelenítése](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Hibaelhárítás

Ha az űrlap nem jelenik meg az **előnézet**kiválasztása után, lehet, hogy szintaktikai hiba történt. Keresse meg a piros kijelzőt a jobb oldali görgetősávon, és navigáljon hozzá.

![Szintaktikai hiba megjelenítése](./media/test-createuidefinition/show-syntax-error.png)

Ha az űrlap nem jelenik meg, és helyette egy felhő ikonja jelenik meg a Tear droptel, az űrlap hibával rendelkezik, például hiányzó tulajdonság. Nyissa meg a webes Fejlesztői eszközök a böngészőben. A **konzolon** megjelennek a csatolóval kapcsolatos fontos üzenetek.

![Hiba megjelenítése](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Tesztelési parancsfájl használata

Az interfész a portálon való teszteléséhez másolja a következő parancsfájlok egyikét a helyi gépre:

* [PowerShell-oldali betöltési parancsfájl – az modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell-oldal – betöltési parancsfájl – Azure-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-oldal – betöltési parancsfájl](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Ha szeretné megtekinteni a csatoló fájlját a portálon, futtassa a letöltött parancsfájlt. A szkript létrehoz egy Storage-fiókot az Azure-előfizetésében, és feltölti a createUiDefinition. JSON fájlt a Storage-fiókba. A rendszer létrehozza a Storage-fiókot, amikor a parancsfájlt először futtatja, vagy ha a Storage-fiókot törölték. Ha a Storage-fiók már létezik az Azure-előfizetésben, a szkript újra felhasználja azt. A szkript megnyitja a portált, és betölti a fájlt a Storage-fiókból.

Adja meg a Storage-fiók helyét, és adja meg a createUiDefinition. JSON fájlt tartalmazó mappát.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Ha a createUiDefinition. JSON fájl ugyanabban a mappában található, mint a parancsfájl, és már létrehozta a Storage-fiókot, nem kell megadnia ezeket a paramétereket.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli
./sideload-createuidef.sh
```

A parancsfájl új lapot nyit meg a böngészőben. Megjeleníti a portált a kezelőfelülettel a felügyelt alkalmazás létrehozásához.

Adja meg a mezők értékeit. Ha elkészült, megjelennek a sablonhoz átadott értékek, amelyek a böngésző fejlesztői eszközeinek konzolján találhatók.

![Értékek megjelenítése](./media/test-createuidefinition/show-json.png)

Ezeket az értékeket használhatja paraméterként a telepítési sablon teszteléséhez.

Ha a portál az összefoglalás képernyőn leáll, lehet, hogy hiba van a kimenet szakaszban. Előfordulhat például, hogy olyan vezérlőelemre hivatkozott, amely nem létezik. Ha a kimenet egyik paramétere üres, akkor előfordulhat, hogy a paraméter olyan tulajdonságra hivatkozik, amely nem létezik. Például a vezérlőre mutató hivatkozás érvényes, de a tulajdonság hivatkozása érvénytelen.

## <a name="test-your-solution-files"></a>A megoldás fájljainak tesztelése

Most, hogy ellenőrizte, hogy a portál felülete a várt módon működik-e, ideje ellenőrizni, hogy a createUiDefinition-fájl megfelelően van-e integrálva a mainTemplate. JSON fájllal. Az érvényesítési parancsfájl tesztelésével tesztelheti a megoldás fájljainak tartalmát, beleértve a createUiDefinition fájlt is. A parancsfájl érvényesíti a JSON-szintaxist, ellenőrzi a regex kifejezéseket a szövegmezőben, és gondoskodik arról, hogy a portál felületének kimeneti értékei megegyezzenek a sablon paramétereivel. A parancsfájl futtatásával kapcsolatos további információkért lásd: [statikus ellenőrzési ellenőrzések futtatása sablonokhoz](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>További lépések

A portál felületének ellenőrzése után megismerheti, hogy az [Azure által felügyelt alkalmazás elérhető legyen a piactéren](publish-marketplace-app.md).
