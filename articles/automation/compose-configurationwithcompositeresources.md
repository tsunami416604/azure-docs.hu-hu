---
title: DSC-konfigurációk írása az Azure Automation állapotkonfigurációjában összetett erőforrások használatával
description: Ismerje meg, hogyan írhat konfigurációkat összetett erőforrások használatával az Azure Automation állapotkonfigurációjában.
keywords: powershell dsc, kívánt állapotkonfiguráció, powershell dsc azure, összetett erőforrások
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682930"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>DSC-konfigurációk írása az Azure Automation állapotkonfigurációjában összetett erőforrások használatával

Ha egyetlen nél több kívánt állapotkonfigurációval (DSC) kell kezelnie az erőforrást, a legjobb elérési út az [összetett erőforrások](/powershell/scripting/dsc/resources/authoringresourcecomposite)használata. Az összetett erőforrás egy beágyazott és paraméterezett konfiguráció, amelyet egy másik konfiguráción belül DSC-erőforrásként használnak. Az összetett erőforrások használata lehetővé teszi összetett konfigurációk létrehozását, miközben lehetővé teszi az alapul szolgáló összetett erőforrások egyedi kezelését és létrehozását.

Az Azure Automation lehetővé teszi az [összetett erőforrások importálását és összeállítását.](automation-dsc-compile.md) Miután importálta az összetett erőforrásokat az Automation-fiókba, használhatja az Azure Automation állapotkonfigurációját az **Állapotkonfiguráció (DSC** szolgáltatás az Azure Portalon.

## <a name="composing-a-configuration-from-composite-resources"></a>Konfiguráció összeállítása összetett erőforrásokból

Mielőtt hozzárendelheti az Azure Portalon összetett erőforrásokból készült konfigurációt, meg kell írnia a konfigurációt. A Kompozíció a **Konfigurációk** (DSC) lapon a **Konfigurációk** vagy a **Lefordított konfigurációk** lapon található Konfiguráció összeállítása lapot használja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon válassza az **Állapotkonfiguráció (DSC)** lehetőséget a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Konfigurációk** vagy a **Lefordított konfigurációk** fülre, majd a lap tetején lévő menükonfiguráció **összeállítása** parancsára.
1. Az **Alapok lépésben** adja meg az új konfigurációs nevet (kötelező), és kattintson az összes olyan összetett erőforrás sorának tetszőleges pontjára, amelyet fel szeretne venni az új konfigurációba, majd kattintson a **Tovább** gombra, vagy kattintson a **Forráskód** lépésre. A következő lépésekhez `PSExecutionPolicy` kiválasztottuk és `RenameAndDomainJoin` összetettük az erőforrásokat.
   ![Képernyőkép az összeállítási konfigurációs lap alapjairól](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. A **Forráskód** lépés megmutatja, hogyan néz ki a kijelölt összetett erőforrások összeállított konfigurációja. Láthatja az összes paraméter egyesítését, és azt, hogy hogyan kerülnek át az összetett erőforráshoz. Ha végzett az új forráskód ellenőrzésével, kattintson a **Tovább** gombra, vagy kattintson a **Paraméterek** lépésre.
   ![Képernyőkép a konfigurációs lap forráskódlépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. A **Paraméterek** lépésben az egyes összetett erőforrások paramétere elérhetővé teszi az értékeket. Ha egy paraméternek van leírása, akkor az a paramétermező mellett jelenik meg. Ha egy paraméter `PSCredential` típusa van, a legördülő lista az aktuális Automation-fiók **hitelesítő adatait** tartalmazó objektumok listáját tartalmazza. A **+ Add a credential** opció is rendelkezésre áll. Miután megadta az összes szükséges paramétert, kattintson a **Mentés és fordítás gombra.**
   ![Képernyőkép a konfigurációs lap paraméterlépéseiről](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Az új konfiguráció mentése után a rendszer elküldésre küldi összeállításra. A fordítási feladat állapota ugyanúgy megtekinthető, mint bármely importált konfiguráció. További információt a [Fordítási feladat megtekintése című](automation-dsc-getting-started.md#viewing-a-compilation-job)témakörben talál.

Ha a fordítás sikeresen befejeződött, az új konfiguráció megjelenik a **Lefordított konfigurációk** lapon. Ezután hozzárendelheti a konfigurációt egy felügyelt csomóponthoz, a [csomópont hozzárendelése egy másik csomópontkonfigurációhoz](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)való hozzárendelése lépéseivel.

## <a name="next-steps"></a>További lépések

- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései.](automation-dsc-getting-started.md)
- A csomópontok alaplapi állapotáról a [Bevezetési gépek az Azure Automation állapotkonfigurációáltali kezelésről](automation-dsc-onboarding.md)olvashat.
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)témakörben olvashat.
- A PowerShell-parancsmagok hivatkozásáról az [Azure Automation állapotkonfigurációs parancsmagjai.](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation state configuration használatával kapcsolatos példa folyamatos üzembe helyezési folyamatban: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és a Csokoládé használata című témakörben.](automation-dsc-cd-chocolatey.md)
