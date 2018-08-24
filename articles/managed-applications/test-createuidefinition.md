---
title: Az Azure által felügyelt alkalmazások a felhasználói felület definíció tesztelése |} A Microsoft Docs
description: Tesztelje a felhasználói élmény a portálon keresztül az Azure által felügyelt alkalmazás létrehozásának módját ismerteti.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: c88bdce64e88f8639da2c4ebb01f4594fccff8a0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747088"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Az Azure portal felületet a felügyelt alkalmazás tesztelése
Miután [a createUiDefinition.json fájl létrehozása](create-uidefinition-overview.md) az Azure által felügyelt alkalmazás a felhasználói élmény tesztelni kell. Tesztelés leegyszerűsítése használja egy parancsfájlt, amely betölti a fájlt a portálon. Nem kell üzembe helyezni a felügyelt alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* A **createUiDefinition.json** fájlt. Ha nem ezt a fájlt, másolja a [mintafájl](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json) , és mentse helyileg.

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="download-test-script"></a>Teszt-szkript letöltése

A kapcsolat tesztelése a portálon, másolja az alábbi parancsfájlok egyikét a helyi számítógépre:

* [PowerShell-példaszkript – közvetlen](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-példaszkript – közvetlen](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Parancsfájl futtatása

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

## <a name="test-your-interface"></a>A kapcsolat tesztelése

A parancsfájl egy új lapot a böngészőben nyílik meg. A felületet a felügyelt alkalmazás létrehozása a portál megjeleníti.

![Portál nézet](./media/test-createuidefinition/view-portal.png)

A mezők kitöltésekor, a böngészőben nyissa meg a Web Developer Tools. A **konzol** a felület fontos üzeneteket jelenít meg.

![Válassza ki a konzol](./media/test-createuidefinition/select-console.png)

Ha a felületdefiníció azonban hibát tartalmaz, a konzol leírása jelenik meg.

![Hiba megjelenítése](./media/test-createuidefinition/show-error.png)

Adja meg a mezők értékeit. Amikor végzett, megjelenik az értékeket, amelyeket a rendszer átad a sablont.

![Értékek megjelenítése](./media/test-createuidefinition/show-json.png)

A központi telepítési sablont tesztelési alkalmazásparaméter-fájlt is használhatja ezeket az értékeket.

## <a name="troubleshooting-the-interface"></a>A kapcsolat hibaelhárítása

Előfordulhat, hogy látható néhány gyakori hibák a következők:

* A portál nem tölt be a kapcsolat. Ehelyett könnycsepp dobja el a felhő ikon látható. Általában ez ikon látható, ha a fájl szintaktikai hiba van. Nyissa meg a fájlt a VS Code (vagy más JSON-szerkesztő, amely rendelkezik a séma érvényesítése), és keresse meg a szintaktikai hibákat.

* A portálon az Összegzés képernyőn lefagy. Általában ez a megszakítás történik, ha a kimeneti szakaszban programhiba van. Például, előfordulhat, hogy rendelkezik a hivatkozott olyan vezérlőelem, amely nem létezik.

* A kimenet egy paraméter üres. Előfordulhat, hogy a paraméter lehet hivatkozik olyan tulajdonságot, amely nem létezik. A vezérlő mutató hivatkozást például érvényes, de a tulajdonsághivatkozás nem érvényes.

## <a name="test-your-solution-files"></a>A megoldás fájl tesztelése

Most, hogy a portál felület elvárt működésének ellenőrzése, ideje, hogy a createUiDefinition fájl megfelelően integrálva van a mainTemplate.json fájl ellenőrzéséhez. Futtathat egy érvényesítési parancsfájlja tesztelésével ellenőrizheti, hogy a megoldás fájlokat, beleértve a createUiDefinition fájl tartalmát. A parancsfájl ellenőrzi a JSON-szintaxist, ellenőrzi, hogy a szövegmezők regex kifejezés, és biztosítja, hogy a kimeneti értékeket a portál felület felel meg a sablon paramétereit. Ez a szkript futtatásával további információkért lásd: [sablonok statikus érvényességi ellenőrzések futtatása](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>További lépések

Miután ellenőrizte a felületén, további információ a [Azure felügyelt a piactéren elérhető alkalmazás](publish-marketplace-app.md).
