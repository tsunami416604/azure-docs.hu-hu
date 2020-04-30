---
title: DSC-konfigurációk összeállítása Azure Automation állapot-konfigurációban összetett erőforrások használatával
description: Megtudhatja, hogyan hozhat létre konfigurációkat összetett erőforrásokkal Azure Automation állapot konfigurációjában.
keywords: PowerShell DSC, a kívánt állapot konfigurációja, PowerShell DSC Azure, összetett erőforrások
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682930"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>DSC-konfigurációk összeállítása Azure Automation állapot-konfigurációban összetett erőforrások használatával

Ha több mint egy kívánt állapot-konfigurációval (DSC) kell kezelnie az erőforrást, a legjobb elérési út az [összetett erőforrások](/powershell/scripting/dsc/resources/authoringresourcecomposite)használata. Az összetett erőforrás egy beágyazott és paraméteres konfiguráció, amely DSC-erőforrásként van használatban egy másik konfiguráción belül. Az összetett erőforrások használata lehetővé teszi, hogy összetett konfigurációkat hozzon létre, miközben lehetővé teszi az alapul szolgáló összetett erőforrások egyedi felügyeletét és létrehozását.

Azure Automation lehetővé teszi az [összetett erőforrások importálását és összeállítását](automation-dsc-compile.md). Miután importálta az összetett erőforrásokat az Automation-fiókjába, használhatja Azure Automation állapot konfigurációját az Azure Portal **DSC** szolgáltatásával.

## <a name="composing-a-configuration-from-composite-resources"></a>Konfiguráció összeállítása összetett erőforrásokból

A Azure Portal összetett erőforrásaiból származó konfiguráció hozzárendeléséhez létre kell hoznia a konfigurációt. A kompozíció az állapot konfigurálása (DSC) lapon a **konfigurációk** vagy a **lefordított konfigurációk** lapon található összeállítási **konfigurációt** használja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon válassza az **állapot konfigurálása (DSC)** lehetőséget a **konfiguráció kezelése**területen.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **konfigurációk** vagy a **lefordított konfigurációk** fülre, majd kattintson a **konfiguráció összeállítása** lehetőségre a lap tetején található menüben.
1. Az **alapvető** beállítások lépésben adja meg az új konfiguráció nevét (kötelező), majd kattintson bárhová az új konfigurációba felvenni kívánt összetett erőforrások sorában, majd kattintson a **tovább** gombra, vagy kattintson a **forráskód** lépésre. A következő lépésekhez kiválasztottuk `PSExecutionPolicy` és `RenameAndDomainJoin` összetett erőforrásokat.
   ![Az összeállítási konfiguráció oldalának alapjai lépésének képernyőképe](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. A **forráskód** lépés azt mutatja be, hogy a kiválasztott összetett erőforrások hogyan állíthatók össze. Láthatja az összes paraméter egyesítését, valamint azt, hogy a rendszer hogyan adja át őket az összetett erőforrásnak. Ha elkészült az új forráskód megtekintésével, kattintson a **tovább** gombra, vagy kattintson a **Parameters (paraméterek** ) lépésre.
   ![Képernyőkép az összeállítási konfigurációs oldal forráskód lépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. A **Parameters (paraméterek** ) lépésben minden összetett erőforrás paramétere elérhetővé vált, így az értékek megadhatók. Ha egy paraméternek van leírása, akkor a paraméter mező mellett jelenik meg. Ha egy paraméter `PSCredential` típusú, a legördülő lista az aktuális Automation-fiókban található **hitelesítőadat** -objektumok listáját tartalmazza. A **+ a hitelesítő adatok hozzáadása** lehetőség is elérhető. Ha minden szükséges paramétert megadtak, kattintson a **Mentés és fordítás**elemre.
   ![Képernyőkép az összeállítási konfigurációs oldal paraméterek lépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Az új konfiguráció mentése után a rendszer elküldi a fordítást. A fordítási feladatok állapota megtekinthető úgy, mint bármely importált konfiguráció. További információ: [fordítási feladatok megtekintése](automation-dsc-getting-started.md#viewing-a-compilation-job).

A fordítás sikeresen befejeződött, az új konfiguráció megjelenik a **lefordított konfigurációk** lapon. Ezt követően a konfigurációt hozzárendelheti egy felügyelt csomóponthoz, a [csomópont ismételt hozzárendelésének lépéseivel egy másik csomópont-konfigurációhoz](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>További lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md)című témakört.
- A csomópontok bevezetésének megismeréséhez tekintse meg a [Azure Automation állapot-konfigurációval történő felügyelethez szükséges bevezetési gépeket](automation-dsc-onboarding.md).
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfigurációval és a csokoládéval](automation-dsc-cd-chocolatey.md).
