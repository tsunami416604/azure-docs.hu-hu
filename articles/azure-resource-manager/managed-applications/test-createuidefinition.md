---
title: A felhasználói felület definíciós fájljának tesztelése
description: Bemutatja, hogyan tesztelheti az Azure felügyelt alkalmazás a portálon keresztül történő létrehozásához a felhasználói élményt.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250176"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>A portálfelület tesztelése az Azure felügyelt alkalmazásaihoz

A felügyelt alkalmazás [createUiDefinition.json fájljának létrehozása](create-uidefinition-overview.md) után tesztelnie kell a felhasználói élményt. A tesztelés egyszerűsítése érdekében használjon olyan sandbox környezetet, amely betölti a fájlt a portálon. Nem kell ténylegesen telepítenie a felügyelt alkalmazást. A sandbox bemutatja a felhasználói felület a jelenlegi, teljes képernyős portál élményt. Vagy használhat parancsfájlt a felület teszteléséhez. Mindkét megközelítés ebben a cikkben látható. A sandbox az ajánlott módja a felület előnézetének.

## <a name="prerequisites"></a>Előfeltételek

* **CreateUiDefinition.json** fájl. Ha nem rendelkezik ezzel a fájllal, másolja a [mintafájlt](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="use-sandbox"></a>A homokozó használata

1. Nyissa meg a [Felhasználói felület definíciójának létrehozása sandboxot.](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)

   ![Homokozó megjelenítése](./media/test-createuidefinition/show-sandbox.png)

1. Cserélje le az üres definíciót a createUiDefinition.json fájl tartalmára. Válassza **az Előnézet**lehetőséget.

   ![Előnézet kiválasztása](./media/test-createuidefinition/select-preview.png)

1. Megjelenik a létrehozott űrlap. Végigléphet a felhasználói élményen, és kitöltheti az értékeket.

   ![Űrlap megjelenítése](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Hibaelhárítás

Ha az űrlap nem jelenik meg az **Előnézet**gombra kattintva, akkor szintaktikai hiba léphet fel. Keresse meg a piros jelzőt a jobb oldali görgetősávon, és keresse meg azt.

![Szintaktikai hiba megjelenítése](./media/test-createuidefinition/show-syntax-error.png)

Ha az űrlap nem jelenik meg, és ehelyett egy szakadást jelző felhőikon jelenik meg, az űrlapon hiba, például egy hiányzó tulajdonság látható. Nyissa meg a webfejlesztői eszközöket a böngészőben. A **konzol** fontos üzeneteket jelenít meg a felülettel kapcsolatban.

![Hiba mutatása](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Tesztparancsfájl használata

A portálon lévő felület teszteléséhez másolja az alábbi parancsfájlok egyikét a helyi számítógépre:

* [PowerShell oldalsó betöltési parancsfájl – Az modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell-oldalterhelési parancsfájl – Azure-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI oldalterheléses parancsfájl](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

A portálon lévő kapcsolatfájl megtekintéséhez futtassa a letöltött parancsfájlt. A parancsfájl létrehoz egy tárfiókot az Azure-előfizetésében, és feltölti a createUiDefinition.json fájlt a tárfiókba. A tárfiók jön létre az első alkalommal, amikor a parancsfájl futtatásakor, vagy ha a tárfiók törölve lett. Ha a tárfiók már létezik az Azure-előfizetésben, a parancsfájl újrafelhasználja azt. A parancsfájl megnyitja a portált, és betölti a fájlt a tárfiókból.

Adja meg a tárfiók helyét, és adja meg a createUiDefinition.json fájlt tartalmazó mappát.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI esetén használja az alábbi parancsot:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Ha a createUiDefinition.json fájl ugyanabban a mappában van, mint a parancsfájl, és már létrehozta a tárfiókot, nem kell megadnia ezeket a paramétereket.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Azure CLI esetén használja az alábbi parancsot:

```bash
./sideload-createuidef.sh
```

A parancsfájl új lapot nyit meg a böngészőben. Megjeleníti a portált a felügyelt alkalmazás létrehozásához.

Adja meg a mezők értékeit. Amikor elkészült, megjelennek azok az értékek, amelyek átkerülnek a sablonba, amelyek a böngésző fejlesztői eszközkonzolján találhatók.

![Értékek megjelenítése](./media/test-createuidefinition/show-json.png)

Ezeket az értékeket használhatja paraméterfájlként a központi telepítési sablon teszteléséhez.

Ha a portál lefagy az összefoglaló képernyőn, lehet, hogy egy hiba a kimeneti szakaszban. Előfordulhat például, hogy nem létező vezérlőre hivatkozott. Ha a kimenetben egy paraméter üres, előfordulhat, hogy a paraméter nem létező tulajdonságra hivatkozik. A vezérlőre mutató hivatkozás például érvényes, de a tulajdonsághivatkozás nem érvényes.

## <a name="test-your-solution-files"></a>A megoldásfájlok tesztelése

Most, hogy ellenőrizte, hogy a portálfelület a várt módon működik-e, itt az ideje annak ellenőrzése, hogy a createUiDefinition fájl megfelelően integrálva van-e a mainTemplate.json fájllal. Futtathat egy érvényesítési parancsfájl-tesztet a megoldásfájlok tartalmának teszteléséhez, beleértve a createUiDefinition fájlt is. A parancsfájl ellenőrzi a JSON szintaxist, ellenőrzi a regex kifejezéseket a szövegmezőkön, és gondoskodik arról, hogy a portálfelület kimeneti értékei megegyeznek a sablon paramétereivel. A parancsfájl futtatásáról a [Sablonok statikus érvényesítési ellenőrzésének futtatása című témakörben olvashat.](https://github.com/Azure/azure-quickstart-templates/tree/master/test)

## <a name="next-steps"></a>További lépések

A portálfelület érvényesítése után ismerje meg, hogyan lehet elérhetővé tenni az [Azure felügyelt alkalmazását a Marketplace-en.](publish-marketplace-app.md)
