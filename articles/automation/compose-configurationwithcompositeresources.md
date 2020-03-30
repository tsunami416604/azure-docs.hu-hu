---
title: DSC-konfigurációk írása az Azure Automation state configuration (DSC) szolgáltatásban összetett erőforrások használatával
description: Ismerje meg, hogyan írhat konfigurációkat összetett erőforrások használatával az Azure Automation állapotkonfigurációjában (DSC)
keywords: powershell dsc, kívánt állapotkonfiguráció, powershell dsc azure, összetett erőforrások
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: e5083ec55ee0a57cd7defd466f5baf1704336320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370671"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>DSC-konfigurációk írása az Azure Automation state configuration (DSC) szolgáltatásban összetett erőforrások használatával

Ha egy erőforrást egynél több kívánt állapotkonfigurációval (DSC) kell kezelni, a legjobb elérési út az [összetett erőforrások](/powershell/scripting/dsc/resources/authoringresourcecomposite)használata. Az összetett erőforrás egy beágyazott és paraméterezett konfiguráció, amelyet egy másik konfiguráción belül DSC-erőforrásként használnak. Ez lehetővé teszi az összetett konfigurációk létrehozását, miközben lehetővé teszi az alapul szolgáló összetett erőforrások (paraméterezett konfigurációk) egyedi legeredményesen felügyelt és létrehozási.

Az Azure Automation lehetővé teszi az [összetett erőforrások importálását és összeállítását.](automation-dsc-compile.md)
Miután az összetett erőforrásokimportálást kapott az Automation-fiókba, használhatja a **Konfiguráció összeállítása** az **Állapotkonfiguráció (DSC)** lapon.

## <a name="composing-a-configuration-from-composite-resources"></a>Konfiguráció összeállítása összetett erőforrásokból

Mielőtt hozzárendelheti az Azure Portalon összetett erőforrásokból készült konfigurációt, meg kell írnia azt. Ez a Konfigurációk **(DSC) lapon az Állapotkonfiguráció (DSC)** lapon található **Konfiguráció összeállítása** lapon végezhető el, miközben a **Konfigurációk** vagy a **Lefordított konfigurációk** lapon található.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az **Automation-fiók** lapon válassza az **Állapotkonfiguráció (DSC)** lehetőséget a **Konfigurációkezelés csoportban.**
1. Az **Állapotkonfiguráció (DSC)** lapon kattintson a **Konfigurációk** vagy a **Lefordított konfigurációk** fülre, majd a lap tetején lévő menükonfiguráció **összeállítása** parancsára.
1. Az **Alapok lépésben** adja meg az új konfigurációs nevet (kötelező), és kattintson az összes olyan összetett erőforrás sorának tetszőleges pontjára, amelyet fel szeretne venni az új konfigurációba, majd kattintson a **Tovább** gombra, vagy kattintson a **Forráskód** lépésre. A következő lépésekhez a **PSExecutionPolicy** és a **RenameAndDomainJoin** összetett erőforrásokat választottuk.
   ![Képernyőkép az összeállítási konfigurációs lap alapjairól](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. A **Forráskód** lépés megmutatja, hogyan néz ki a kijelölt összetett erőforrások összeállított konfigurációja. Láthatja az összes paraméter egyesítését, és azt, hogy hogyan kerülnek át az összetett erőforráshoz. Ha végzett az új forráskód ellenőrzésével, kattintson a **Tovább** gombra, vagy kattintson a **Paraméterek** lépésre.
   ![Képernyőkép a konfigurációs lap forráskódlépéséről](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. A **Paraméterek** lépésben az egyes összetett erőforrások paramétere elérhetővé vált, hogy azok megadhatók legyenek. Ha egy paraméternek van leírása, akkor az a paramétermező mellett jelenik meg. Ha egy mező **PSCredential** típusparaméter, a konfigurálandó legördülő lista az aktuális Automation-fiók **hitelesítő adatait** tartalmazó objektumok listáját tartalmazza. A **+ Add a credential** opció is rendelkezésre áll. Miután megadta az összes szükséges paramétert, kattintson a **Mentés és fordítás gombra.**
   ![Képernyőkép a konfigurációs lap paraméterlépéseiről](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Az új konfiguráció mentése után a rendszer elküldésre küldi összeállításra. A fordítási feladat állapota ugyanúgy megtekinthető, mint bármely importált konfiguráció. További információt a [Fordítási feladat megtekintése című](automation-dsc-getting-started.md#viewing-a-compilation-job)témakörben talál.

Ha a fordítás sikeresen befejeződött, az új konfiguráció megjelenik a **Lefordított konfigurációk** lapon. Ha már látható ezen a lapon, akkor hozzárendelhető egy felügyelt csomóponthoz a [csomópont hozzárendelése egy másik csomópontkonfigurációhoz](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)című lépések lépéseivel.

## <a name="next-steps"></a>További lépések

- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései](automation-dsc-getting-started.md)
- A fedélzeti csomópontok ról a [Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezelésről olvashat.](automation-dsc-onboarding.md)
- A DSC-konfigurációk összeállításáról, hogy hozzárendelhesse őket a célcsomópontokhoz, olvassa el [a Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)
- A PowerShell-parancsmagok hivatkozásáról az [Azure Automation állapotkonfigurációs parancsmagjai](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation State Configuration pricing](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation állapotkonfigurációjának folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és csokoládés használatával](automation-dsc-cd-chocolatey.md)
