---
title: DSC-konfigurációk az Azure Automation állapot Configuration (DSC) használatával összetett erőforrások összeállítása
description: Ismerje meg, hogyan compose konfigurációk használatával összetett erőforrások az Azure Automation állapot Configuration (DSC)
keywords: PowerShell dsc, a kívánt állapot konfigurációs, a powershell dsc azure, összetett erőforrások
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 64588829cec964e52dcb44465869e0090f36f9f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278622"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>DSC-konfigurációk az Azure Automation állapot Configuration (DSC) használatával összetett erőforrások összeállítása

Egy erőforrás van szüksége a több mint egy egyetlen célállapot-konfiguráció (DSC) konfigurációja kell felügyelni, a legjobb útvonalat esetén használandó [összetett erőforrások](/powershell/dsc/authoringresourcecomposite). Egy összetett erőforrás egy beágyazott és paraméteres configuration belül egy másik konfigurációs DSC erőforrásként használatban. Ez lehetővé teszi, miközben lehetővé teszi az alapul szolgáló összetett erőforrások (paraméteres konfigurációk) önállóan felügyelt, és beépített összetett konfigurációk létrehozását.

Az Azure Automation lehetővé teszi, hogy a [importálása és összetett erőforrások összeállításának](automation-dsc-compile.md#composite-resources). Összetett erőforrások importálta az Automation-fiókját, ha is használni tudja a **összeállítás konfigurációs** felületét a **állapot Configuration (DSC)** lapot.

## <a name="composing-a-configuration-from-composite-resources"></a>Összetett erőforrások a konfiguráció összeállítása

Összetett erőforrások az Azure Portalon kérés érkezett egy konfigurációs hozzárendelés előtt létre kell hoznia. Ezt megteheti a **összeállítás konfigurációs** a a **állapot Configuration (DSC)** lapon, míg a vagy a **konfigurációk** vagy **Compiled konfigurációk** lapokon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** lapon jelölje be **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson vagy a **konfigurációk** vagy **konfigurációk lefordított** lapfülre, majd kattintson a **Compose-konfiguráció**  az oldal tetején lévő menüben.
1. Az a **alapjai** lépést, adja meg az új konfiguráció neve (kötelező) és kattintson bárhová a sorban az egyes összetett erőforrások, az új konfiguráció, majd kattintson a kívánt **tovább** , vagy kattintson a **Forráskódját** . lépés. Az alábbi lépéseket a kiválasztott **PSExecutionPolicy** és **RenameAndDomainJoin** összetett erőforrások.
   ![Képernyőkép az alapvető beállítások lépés a compose-konfiguráció lap](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. A **forráskódját** a lépés bemutatja, hogyan tevődik össze konfigurációját a kiválasztott összetett erőforrások néz ki. Láthatja az összes paraméter egyesítése és hogyan azokat a rendszer átadja az összetett erőforrást. Ha elkészült az új forráskód áttekintése, kattintson a **tovább** , vagy kattintson a **paraméterek** . lépés.
   ![A forrás kód lépés az összeállítás konfigurációs lapjának képernyőképe](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Az a **paraméterek** lépést, a paraméter, amely minden egyes összetett erőforrás van ki van téve, hogy azok adható meg. Ha a paraméter leírását, a paraméter mező mellett jelenik meg. Ha egy mező egy **PSCredential** típusú paramétert, a legördülő konfigurálása listáját tartalmazza **Credential** objektumok aktuális az Automation-fiókban. A **+ hitelesítő adatainak hozzáadása** a beállítás akkor is elérhető. Miután az összes szükséges paraméterek adtak meg, kattintson a **mentéséhez és a fordítási**.
   ![A paraméterek lépés az összeállítás konfigurációs lapjának képernyőképe](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Az új konfigurációt a mentés után a fordítási elküldésekor. A fordítási feladat állapota minden importált konfigurációs például tekinthet meg. További információkért lásd: [egy fordítási feladat megtekintése](automation-dsc-getting-started.md#viewing-a-compilation-job).

Fordítási sikeresen befejeződött, ha az új konfigurációt megjelenik a **konfigurációk lefordított** fülre. Ha ezen a lapon látható, akkor a lépésekkel, a felügyelt csomóponthoz rendelhető [újbóli hozzárendelése egy másik csomópont-konfiguráció csomópontot](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>További lépések

- Első lépésként lásd [Ismerkedés az Azure Automation Állapotkonfiguráció](automation-dsc-getting-started.md)
- Megtudhatja, hogyan előkészítheti a csomópontokat, hogy meg [gépek előkészítése kezelésre, az Azure Automation állapot konfigurációja](automation-dsc-onboarding.md)
- DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat kapcsolatos további információkért lásd: [összeállítása az Azure Automation Állapotkonfiguráció konfigurációk](automation-dsc-compile.md)
- PowerShell-parancsmagok leírása, lásd: [konfiguráló Azure Automation-parancsmagok](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért tekintse meg a [Azure Automation State Configuration díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Folyamatos üzembe helyezés folyamatban lévő Azure Automation Állapotkonfiguráció használatának példájáért lásd: [folyamatos üzembe helyezés használatával az Azure Automation Állapotkonfiguráció és a chocolatey-t](automation-dsc-cd-chocolatey.md)
