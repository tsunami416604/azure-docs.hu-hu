---
title: Az Azure által felügyelt alkalmazások a felhasználói felület definíció tesztelése |} A Microsoft Docs
description: Tesztelje a felhasználói élmény a portálon keresztül az Azure által felügyelt alkalmazás létrehozásának módját ismerteti.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257599"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>A portál felület tesztelése az Azure által felügyelt alkalmazások

Miután [a createUiDefinition.json fájl létrehozása](create-uidefinition-overview.md) a felügyelt alkalmazás a felhasználói élmény tesztelni kell. Tesztelés leegyszerűsítése használja egy védőfal mögötti környezet, amely betölti a fájlt a portálon. Nem kell üzembe helyezni a felügyelt alkalmazás. A védőfal megadja a felhasználói felület az aktuális, teljes képernyős portál élményt nyújt. Vagy a tesztelés a felületet, de egy olyan örökölt nézetet a portál egy PowerShell-parancsfájlt használhatja. Ez a cikk mindkét módszerénél láthatók. A tesztkörnyezet-e az ajánlott módszer az előzetes verzióra a felületet.

## <a name="prerequisites"></a>Előfeltételek

* A **createUiDefinition.json** fájlt. Ha nem ezt a fájlt, másolja a [mintafájl](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="use-sandbox"></a>Használja a védőfalat

1. Nyissa meg a [létrehozása a felhasználói felület definíció védőfal](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![A védőfal megjelenítése](./media/test-createuidefinition/show-sandbox.png)

1. Cserélje le az üres definíciót a createUiDefinition.json fájl tartalmát. Válassza ki **előzetes**.

   ![Válassza ki az előzetes verzió](./media/test-createuidefinition/select-preview.png)

1. A létrehozott űrlapon jelenik meg. Lépkedjen végig a felhasználói élményt, és adja meg az értékeket.

   ![Űrlap megjelenítése](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a képernyőn nem jelenik meg kiválasztása után **előzetes**, előfordulhat, hogy szintaktikai hiba. Keresse meg a piros kijelző a megfelelő görgetősávon, és keresse meg a fájlt.

![Chyba syntaxe megjelenítése](./media/test-createuidefinition/show-syntax-error.png)

Ha az űrlap nem jeleníti meg, és ehelyett könnycsepp dobja el a felhő ikonja megjelenik, az űrlapon szerepel egy hiba, például a hiányzó tulajdonságot. A böngészőben nyissa meg a Web Developer Tools. A **konzol** a felület fontos üzeneteket jelenít meg.

![Hiba megjelenítése](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Teszt szkriptjét használja.

A kapcsolat tesztelése a portálon, másolja az alábbi parancsfájlok egyikét a helyi számítógépre:

* [PowerShell-példaszkript – közvetlen](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-példaszkript – közvetlen](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

A portálon a felület fájl megtekintéséhez futtassa a letöltött szkript. A szkript létrehoz egy tárfiókot az Azure-előfizetésben, és feltölti a createUiDefinition.json fájlt a storage-fiókot. A szkript első futtatásakor jön létre a storage-fiók, vagy ha a tárfiók törölve lett. Ha a tárfiók már létezik az Azure-előfizetése, az a parancsfájlt használja. A parancsfájl a portál megnyitja, és betölti a fájlt a tárfiókból.

Adja meg a tárfiók helyét, és adja meg a createUiDefinition.json fájlt tartalmazó mappát.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Ha a createUiDefinition.json fájl a parancsfájl ugyanabban a mappában, és a tárfiók már létrehozott, nem kell meg ezeket a paramétereket.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli
./sideload-createuidef.sh
```

A parancsfájl egy új lapot a böngészőben nyílik meg. A felületet a felügyelt alkalmazás létrehozása a portál megjeleníti.

![Portál nézet](./media/test-createuidefinition/view-portal.png)

Adja meg a mezők értékeit. Amikor végzett, megjelenik az értékeket, amelyeket a rendszer átad a sablont.

![Értékek megjelenítése](./media/test-createuidefinition/show-json.png)

A központi telepítési sablont tesztelési alkalmazásparaméter-fájlt is használhatja ezeket az értékeket.

Ha a portálon az Összegzés képernyőn lefagy, lehet egy hiba a kimeneti szakaszban. Például, előfordulhat, hogy rendelkezik a hivatkozott olyan vezérlőelem, amely nem létezik. Ha a kimeneti paraméter üres, előfordulhat, hogy a paraméter hivatkozik olyan tulajdonságot, amely nem létezik. A vezérlő mutató hivatkozást például érvényes, de a tulajdonsághivatkozás nem érvényes.

## <a name="test-your-solution-files"></a>A megoldás fájl tesztelése

Most, hogy a portál felület elvárt működésének ellenőrzése, ideje, hogy a createUiDefinition fájl megfelelően integrálva van a mainTemplate.json fájl ellenőrzéséhez. Futtathat egy érvényesítési parancsfájlja tesztelésével ellenőrizheti, hogy a megoldás fájlokat, beleértve a createUiDefinition fájl tartalmát. A parancsfájl ellenőrzi a JSON-szintaxist, ellenőrzi, hogy a szövegmezők regex kifejezés, és biztosítja, hogy a kimeneti értékeket a portál felület felel meg a sablon paramétereit. Ez a szkript futtatásával további információkért lásd: [sablonok statikus érvényességi ellenőrzések futtatása](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>További lépések

Miután ellenőrizte a felületén, további információ a [Azure felügyelt a piactéren elérhető alkalmazás](publish-marketplace-app.md).
