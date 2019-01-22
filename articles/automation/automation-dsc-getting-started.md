---
title: Ismerkedés az Azure Automation Állapotkonfiguráció
description: MAGYARÁZAT és példák a legáltalánosabb feladatokat az Azure Automation állapot Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 19e1721a638dfc750938e09e1626c4e291367723
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432445"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Ismerkedés az Azure Automation Állapotkonfiguráció

Ez a cikk ismerteti az Azure Automation-konfiguráló szolgáltatása, például a létrehozása, importálása, és konfigurációk esetén kezelheti, gépek előkészítése fordítása és -jelentések megtekintése a leggyakoribb feladatokat végezheti el. Milyen az Azure Automation konfiguráló áttekintése van, lásd: [Azure Automation konfiguráló áttekintése](automation-dsc-overview.md). A Desired State Configuration (DSC) dokumentációja: [Windows PowerShell Desired State Configuration áttekintése](/powershell/dsc/overview).

Ez a cikk részletesen ismerteti az Azure Automation State Configuration használatával. Ha azt szeretné, hogy egy minta környezet nélkül ebben a cikkben ismertetett lépések már be van állítva, a következő Resource Manager-sablon is használhatja: [Azure Automation által felügyelt csomópontra sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Ez a sablon állít be egy befejezett konfigurációs Azure Automation-környezetben, többek között az Azure virtuális gép konfigurációs Azure Automation által kezelt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő példák elvégzéséhez a következőkre szükség:

- Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
- Egy Azure Resource Manager virtuális gép (nem klasszikus) futó Windows Server 2008 R2 vagy újabb. A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.

## <a name="creating-a-dsc-configuration"></a>DSC-konfiguráció létrehozása

Létrehozhat egy egyszerű [DSC-konfiguráció](/powershell/dsc/configurations) , amely biztosítja a jelenléte vagy hiánya az **webkiszolgálón** Windows funkciót (IIS), attól függően, hogyan rendelje hozzá csomópontokat.

1. Indítsa el [VSCode](https://code.visualstudio.com/docs) (vagy bármilyen szövegszerkesztővel).
1. Írja be a következőket:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Mentse a fájlt az `TestConfig.ps1`.

Ez a konfiguráció egy erőforrás szúrhasson be minden egyes csomópont letiltása a [WindowsFeature erőforrás](/powershell/dsc/windowsfeatureresource), amely biztosítja a jelenléte vagy hiánya az **webkiszolgálón** funkció.

## <a name="importing-a-configuration-into-azure-automation"></a>Az Azure Automationbe konfiguráció importálása

Ezután a konfigurációt importálja az Automation-fiókot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** lapon jelölje be **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **konfigurációk** lapfülre, majd kattintson a **+ Hozzáadás**.
1. Az a **konfiguráció importálása** lapon, keresse meg a `TestConfig.ps1` fájlt a számítógépen.

   ![Képernyőkép az ** Import Configuration ** panel](./media/automation-dsc-getting-started/AddConfig.png)

1. Kattintson az **OK** gombra.

## <a name="viewing-a-configuration-in-azure-automation"></a>A konfiguráció megtekintése az Azure Automationben

Miután importálta a konfiguráció, megtekintheti az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** lapon jelölje be **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **konfigurációk** lapfülre, majd kattintson a **TestConfig** (azt a nevet az előző importált konfiguráció az eljárás).
1. Az a **TestConfig konfigurációs** kattintson **konfiguráció forrásának megjelenítése**.

   ![TestConfig konfigurációs panelen – képernyőfelvétel](./media/automation-dsc-getting-started/ViewConfigSource.png)

   A **TestConfig konfiguráció forrása** lap megnyitásakor, a PowerShell-kóddal, a konfiguráció megjelenítése.

## <a name="compiling-a-configuration-in-azure-automation"></a>Az Azure Automationben konfiguráció fordítása

Egy csomópont kívánt állapotát is alkalmazhat, mielőtt a DSC-konfiguráció definiálása az állapotban legyen lefordítva egy vagy több csomópont-konfigurációk (MOF dokumentum), és meg az Automation DSC lekéréses kiszolgálón. Konfigurációk az Azure Automation Állapotkonfiguráció összeállításának részletes ismertetését lásd: [összeállítása az Azure Automation Állapotkonfiguráció konfigurációk](automation-dsc-compile.md).
Konfiguráció fordítása kapcsolatos további információkért lásd: [DSC-konfigurációk](/powershell/dsc/configurations).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** kattintson **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **konfigurációk** lapfülre, majd kattintson a **TestConfig** (a korábban importált konfiguráció neve).
1. A a **TestConfig konfigurációs** kattintson **összeállítása**, és kattintson a **Igen**. Ekkor elindul a fordítási feladat.

   ![A fordítási gomb kiemelése TestConfig konfigurációs oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Az Azure Automationben konfiguráció fordítása, amikor telepíti a megfelelő bármely létrehozott csomópont-konfiguráció MOF-fájlok automatikusan a lekéréses kiszolgálón.

## <a name="viewing-a-compilation-job"></a>A fordítási feladat megtekintése

Után elindítja a fordítás, megtekintheti a a **fordítási feladatok** csempéje a **konfigurációs** lapot. A **fordítási feladatok** csempe megjeleníti a jelenleg futó, befejeződött, és sikertelen feladatok. Amikor megnyit egy fordítási feladat oldalát, a feladat, beleértve az esetleges hibákat vagy figyelmeztetéseket észlelt adatait mutatja, használt bemeneti paramétereket a konfigurációt, és a fordítási naplók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** kattintson **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **konfigurációk** lapfülre, majd kattintson a **TestConfig** (a korábban importált konfiguráció neve).
1. A **fordítási feladatok**, válassza ki a megtekinteni kívánt fordítási feladat. A **fordítási feladat** lap megnyitásakor a fordítási feladatot indított dátumával.

   ![A fordítási feladat oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/CompilationJob.png)

1. Bármelyik csempére a a **fordítási feladat** oldalt, hogy további a feladat részleteit.

## <a name="viewing-node-configurations"></a>Csomópont-konfigurációk megtekintése

A fordítási feladat sikeres végrehajtása egy vagy több új csomópont-konfigurációkat hoz létre. Csomópont-konfiguráció, amelyet a lekéréses kiszolgálóra, és készen áll a lekért és a egy vagy több csomópont által alkalmazott MOF dokumentum. A csomópont-konfigurációkat tekintheti meg az Automation-fiókját a **State configuration (DSC)** lapot. Csomópont-konfiguráció az űrlap-névvel rendelkezik *ConfigurationName*. *NodeName*.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** panelen kattintson a **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **konfigurációk lefordított** fülre.

   ![A lefordított konfiguráció lap képernyőképe](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Bevezetés az Azure Automation State Configuration Management Azure VM

Használhatja az Azure Automation Állapotkonfiguráció kezelése az Azure virtuális gépek (klasszikus és Resource Manager), a helyszíni virtuális gépek, Linux rendszerű gépek, az AWS-beli virtuális gépek és a helyszíni fizikai gépek. Ebből a cikkből megtudhatja, hogyan lehet csak az Azure Resource Manager virtuális gépek előkészítése. Gépek, más típusú bevezetési kapcsolatos információk: [gépek előkészítése kezelésre által az Azure Automation Állapotkonfiguráció](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>A bevezetni által az Azure Automation Állapotkonfiguráció kezelése az Azure Resource Manager virtuális gép

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** panelen kattintson a **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, miközben a a **csomópontok** lapra, majd **+ Hozzáadás**.

   ![Képernyőkép a DSC-csomópontok kiemelése a hozzáadása Azure-beli Virtuálisgép-gomb](./media/automation-dsc-getting-started/OnboardVM.png)

1. Az a **virtuális gépek** lapra, jelölje be a virtuális gép.
1. Az a **virtuális gép** Részletek lap, kattintson a **+ Connect**.

   > [!IMPORTANT]
   > Ez lehet egy Azure Resource Manager virtuális Gépen futó Windows Server 2008 R2 vagy újabb.

1. Az a **regisztrációs** lapra, jelölje be a virtuális géphez a alkalmazni szeretné a csomópont-konfiguráció nevét a **csomópont-konfiguráció neve** mezőbe. Ezen a ponton nyújtó nevét nem kötelező. A hozzárendelt csomópont-konfigurációt a csomópont előkészítése után módosíthatja.
   Ellenőrizze **szükség esetén újraindítás csomópont**, majd kattintson a **OK**.

   ![A regisztrációs panelen – képernyőfelvétel](./media/automation-dsc-getting-started/RegisterVM.png)

   A csomópont-konfiguráció által meghatározott időközönként érvényesek a virtuális géphez megadott a **a konfigurációs mód gyakoriságának**, és a csomópont-konfiguráció által meghatározott időközönként frissítéseket keres a virtuális gép a **frissítése Gyakoriság**. Ezek az értékek módjáról további információkért lásd: [a Local Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. Az a **hozzáadása az Azure virtuális gépek** panelen kattintson a **létrehozás**.

Az Azure a virtuális gép bevezetési folyamata elindul. Ha elkészült, a virtuális gép megjelenik-e a **csomópontok** lapján a **State configuration (DSC)** lap az Automation-fiókban.

## <a name="viewing-the-list-of-managed-nodes"></a>A felügyelt csomópontok listájának megtekintése

Megtekintheti a listáját, amelyeket felügyeletre előkészített az Automation-fiókban lévő összes gép a **csomópontok** lapján a **State configuration (DSC)** lapot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** panelen kattintson a **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **csomópontok** fülre.

## <a name="viewing-reports-for-managed-nodes"></a>A felügyelt csomópontok-jelentések megtekintése

Minden alkalommal, amikor az Azure Automation Állapotkonfiguráció felügyelt csomóponton, konzisztencia-ellenőrzést hajt végre a csomópont egy állapotjelentést küld a a lekéréses kiszolgálóra. Ezeket a jelentéseket megtekintheti adott csomópont az oldalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** panelen kattintson a **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **csomópontok** fülre. Itt láthatja a szolgáltatás konfigurációs állapotát és minden egyes csomópont esetében a részletek áttekintése.

   ![Képernyőfelvétel: a csomópont lap](./media/automation-dsc-getting-started/NodesTab.png)

1. A során a **csomópontok** lapra, majd nyissa meg a jelentéskészítési csomópont rekord. Kattintson a jelentés jelentéskészítő további részletek megtekintéséhez.

   ![A jelentés panelen – képernyőfelvétel](./media/automation-dsc-getting-started/NodeReport.png)

Az egyéni jelentés panelen láthatja a megfelelő konzisztencia-ellenőrzés állapota a következő információkat:

- A jelentés állapotának – hogy a csomópont "Megfelelő", a konfiguráció "Sikertelen", vagy a csomópont "Nem megfelelő" (amikor a csomópont szerepel **ApplyandMonitor** mód és a gép nem a kívánt állapotban van).
- A konzisztencia-ellenőrzés kezdetének időpontja.
- A konzisztencia-ellenőrzést az összes futásidejének.
- A konzisztencia-ellenőrzés típusa.
- Esetleges hibákat, beleértve a hibakódot és a hibaüzenet jelenik meg.
- A konfigurációban, és minden egyes (a csomópont-e a kívánt állapotban az adott erőforráshoz) erőforrás állapotának használt bármely DSC-erőforrások – kattintson a mindegyik erőforrást, melyeken részletesebb információkat találhat az adott erőforráshoz.
- A név, IP-cím és a csomópont konfigurációs módot.

Is **nyers jelentés megtekintése** a csomópont küld a kiszolgáló tényleges adatok megtekintéséhez.
Az adatok használatával kapcsolatos további információkért lásd: [DSC jelentéskészítő kiszolgálójának használata](/powershell/dsc/reportserver).

Némi várakozás után csomópont előkészítve, mielőtt az első jelentés érhető el is igénybe vehet. Várjon az első jelentés körülbelül 30 perc után, felvétele egy csomópont lehet, hogy kell.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Egy másik csomópont-konfiguráció egy csomópont újbóli hozzárendelése

Hozzárendelhet egy csomópont kezdetben hozzárendelt hierarchiától különböző csomópont-konfiguráció használatához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** panelen kattintson a **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **csomópontok** fülre.
1. Az a **csomópontok** fülre, kattintson a hozzá szeretné rendelni a csomópont neve.
1. Kattintson a lap a csomópontra vonatkozóan, **csomópont-konfiguráció hozzárendelése**.

    ![A csomópont részletei lapon kiemelése a hozzárendelése csomópont konfigurációs gomb képernyőképe](./media/automation-dsc-getting-started/AssignNode.png)

1. Az a **csomópont-konfiguráció hozzárendelése** lapra, jelölje be a csomópont-konfiguráció hozzárendelése a csomópontot, és kattintson a kívánt **OK**.

    ![A csomópont-konfiguráció hozzárendelése oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Csomópont regisztrációjának törlése

Ha már nincs szüksége az Azure Automation DSC kezelendő csomópont, regisztrációját is.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** panelen kattintson a **State configuration (DSC)** alatt **konfigurációkezelés**.
1. Az a **State configuration (DSC)** lap, kattintson a **csomópontok** fülre.
1. Az a **csomópontok** lapra, majd a csomópont regisztrációjának törlése kívánt nevére.
1. Kattintson a lap a csomópontra vonatkozóan, **Unregister**.

    ![A csomópont részletei lapon kiemelése, a Unregister gomb képernyőképe](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Azure Automation konfiguráló áttekintése](automation-dsc-overview.md)
- [Gépek előkészítése kezelésre, az Azure Automation állapot konfigurációja](automation-dsc-onboarding.md)
- [Windows PowerShell Desired State Configuration áttekintése](/powershell/dsc/overview)
- [Azure Automation állapota konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- [Azure Automation állapot Configuration díjszabása](https://azure.microsoft.com/pricing/details/automation/)
