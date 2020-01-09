---
title: DSC-konfigurációk összeállítása Azure Automation állapot-konfigurációban (DSC) összetett erőforrások használatával
description: Megtudhatja, hogyan hozhat létre konfigurációkat összetett erőforrásokkal Azure Automation állapot-konfigurációban (DSC)
keywords: PowerShell DSC, a kívánt állapot konfigurációja, PowerShell DSC Azure, összetett erőforrások
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 2c6d9aa13c8c320dc46f6634f3a60e0c1730c710
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418730"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>DSC-konfigurációk összeállítása Azure Automation állapot-konfigurációban (DSC) összetett erőforrások használatával

Ha egy erőforrást egynél több, a kívánt állapot-konfiguráció (DSC) konfigurációval kell felügyelni, a legjobb elérési út az [összetett erőforrások](/powershell/scripting/dsc/resources/authoringresourcecomposite)használata. Az összetett erőforrás egy beágyazott és paraméteres konfiguráció, amely DSC-erőforrásként van használatban egy másik konfiguráción belül. Ez lehetővé teszi az összetett konfigurációk létrehozását, miközben lehetővé teszi az alapul szolgáló összetett erőforrások (paraméteres konfigurációk) egyedi felügyeletét és kialakítását.

Azure Automation lehetővé teszi az [összetett erőforrások importálását és összeállítását](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources).
Miután importálta az összetett erőforrásokat az Automation-fiókjába, használhatja az **összeállítási konfigurációs** élményt az **állapot konfigurálása (DSC)** lapon.

## <a name="composing-a-configuration-from-composite-resources"></a>Konfiguráció összeállítása összetett erőforrásokból

A Azure Portal összetett erőforrásaiból származó konfiguráció hozzárendeléséhez létre kell hoznia azt. Ez az **állapot-konfiguráció (DSC)** lapon található összeállítási **konfiguráció** használatával végezhető el, a **konfigurációk** vagy a **lefordított konfigurációk** lapjain.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az **Automation-fiók** lapon válassza az **állapot konfigurálása (DSC)** lehetőséget a **konfiguráció kezelése**területen.
1. Az **állapot-konfiguráció (DSC)** lapon kattintson a **konfigurációk** vagy a **lefordított konfigurációk** fülre, majd kattintson a **konfiguráció összeállítása** lehetőségre a lap tetején található menüben.
1. Az **alapvető** beállítások lépésben adja meg az új konfiguráció nevét (kötelező), majd kattintson bárhová az új konfigurációba felvenni kívánt összetett erőforrások sorában, majd kattintson a **tovább** gombra, vagy kattintson a **forráskód** lépésre. A következő lépésekben a **PSExecutionPolicy** és a **RenameAndDomainJoin** összetett erőforrásokat választottuk.
   ![képernyőkép az összeállítási konfigurációs oldal alapismeretek lépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. A **forráskód** lépés azt mutatja be, hogy a kiválasztott összetett erőforrások hogyan állíthatók össze. Láthatja az összes paraméter egyesítését, valamint azt, hogy a rendszer hogyan adja át őket az összetett erőforrásnak. Ha elkészült az új forráskód megtekintésével, kattintson a **tovább** gombra, vagy kattintson a **Parameters (paraméterek** ) lépésre.
   ![képernyőkép az összeállítási konfigurációs oldal forráskód lépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. A **Parameters (paraméterek** ) lépésben az egyes összetett erőforrások számára elérhető paraméter látható. Ha egy paraméternek van leírása, akkor a paraméter mező mellett jelenik meg. Ha a mező egy **PSCredential** típusú paraméter, a configure (legördülő lista) beállítás megjeleníti a **hitelesítő adatok** listáját az aktuális Automation-fiókban. A **+ a hitelesítő adatok hozzáadása** lehetőség is elérhető. Ha minden szükséges paramétert megadtak, kattintson a **Mentés és fordítás**elemre.
   ![képernyőkép a levélírás konfigurációs oldal paraméterek lépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Az új konfiguráció mentése után a rendszer elküldi a fordítást. A fordítási feladatok állapota megtekinthető úgy, mint bármely importált konfiguráció. További információ: [fordítási feladatok megtekintése](automation-dsc-getting-started.md#viewing-a-compilation-job).

A fordítás sikeresen befejeződött, az új konfiguráció megjelenik a **lefordított konfigurációk** lapon. Amint az megjelenik ezen a lapon, hozzárendelhető egy felügyelt csomóponthoz a [csomópont egy másik csomópont-konfigurációhoz való ismételt hozzárendelésének](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)lépéseivel.

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md) című témakört.
- A csomópontok előkészítésének megismeréséhez lásd: [bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](automation-dsc-onboarding.md)
- A DSC-konfigurációk fordításának megismeréséhez, hogy hozzá lehessen rendelni azokat a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md) című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .
