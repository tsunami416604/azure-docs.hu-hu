---
title: Azure Automation DSC – első lépések
description: MAGYARÁZAT és példák a legáltalánosabb feladatokat az Azure Automation Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9f2312064e9fb7676d5609ee077d5ed7e02e8f30
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524464"
---
# <a name="getting-started-with-azure-automation-dsc"></a>Azure Automation DSC – első lépések

Ez a cikk bemutatja, hogyan teheti a legáltalánosabb feladatokat az Azure Automation Desired State Configuration (DSC), például a létrehozása, importálása, és konfigurációk esetén kezelheti, gépek előkészítése fordítása és -jelentések megtekintése. Milyen az Azure Automation DSC áttekintést van, lásd: [Azure Automation DSC áttekintése](automation-dsc-overview.md). A DSC-dokumentációja: [Windows PowerShell Desired State Configuration áttekintése](/powershell/dsc/overview).

Ez a cikk részletesen ismerteti az Azure Automation DSC használatával. Ha azt szeretné, hogy egy minta környezet nélkül ebben a cikkben ismertetett lépések már be van állítva, használhatja a következő Resource Manager-sablonnal: Ez a sablon egy befejezett Azure Automation DSC-környezetben, beleértve az Azure virtuális gép, amely beállítja a Azure Automation DSC kezeli.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő példák elvégzéséhez a következőkre szükség:

* Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
* Egy Azure Resource Manager virtuális gép (nem klasszikus) futó Windows Server 2008 R2 vagy újabb. A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.

## <a name="creating-a-dsc-configuration"></a>DSC-konfiguráció létrehozása

Létrehozhat egy egyszerű [DSC-konfiguráció](/powershell/dsc/configurations) , amely biztosítja a jelenléte vagy hiánya az **webkiszolgálón** Windows funkciót (IIS), attól függően, hogyan rendelje hozzá csomópontokat.

1. Indítsa el a Windows PowerShell ISE-ben (vagy bármilyen szövegszerkesztővel).
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

Ez a konfiguráció egy erőforrás szúrhasson be minden egyes csomópont letiltása a [WindowsFeature erőforrás](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), amely biztosítja a jelenléte vagy hiánya az **webkiszolgálón** funkció.

## <a name="importing-a-configuration-into-azure-automation"></a>Az Azure Automationbe konfiguráció importálása

Ezután a konfigurációt importálja az Automation-fiókot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** lapon jelölje be **DSC-konfigurációk** alatt **konfigurációkezelés**.
1. Az a **DSC-konfigurációk** kattintson **+ -konfiguráció hozzáadása**.
1. Az a **konfiguráció importálása** lapon, keresse meg a `TestConfig.ps1` fájlt a számítógépen.

    ![Képernyőkép az ** Import Configuration ** lap](./media/automation-dsc-getting-started/AddConfig.png)
1. Kattintson az **OK** gombra.

## <a name="viewing-a-configuration-in-azure-automation"></a>A konfiguráció megtekintése az Azure Automationben

Miután importálta a konfiguráció, megtekintheti az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** lapon jelölje be **DSC-konfigurációk** alatt **konfigurációkezelés**.
1. Az a **DSC-konfigurációk** kattintson **TestConfig** (azt a nevet a konfiguráció az előzőleg importált).
1. Az a **TestConfig konfigurációs** kattintson **konfiguráció forrásának megjelenítése**.

    ![A TestConfig konfigurációs oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/ViewConfigSource.png)

    A **TestConfig konfiguráció forrása** lap megnyitásakor, a PowerShell-kóddal, a konfiguráció megjelenítése.

## <a name="compiling-a-configuration-in-azure-automation"></a>Az Azure Automationben konfiguráció fordítása

Egy csomópont kívánt állapotát is alkalmazhat, mielőtt a DSC-konfiguráció definiálása az állapotban legyen lefordítva egy vagy több csomópont-konfigurációk (MOF dokumentum), és meg az Automation DSC lekéréses kiszolgálón. Azure Automation DSC-konfigurációja összeállításának részletes ismertetését lásd: [összeállítása az Azure Automation DSC-konfigurációja](automation-dsc-compile.md). Konfiguráció fordítása kapcsolatos további információkért lásd: [DSC-konfigurációk](/powershell/dsc/configurations).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** kattintson **DSC-konfigurációk** alatt **konfigurációkezelés**.
1. Az a **DSC-konfigurációk** kattintson **TestConfig** (a korábban importált konfiguráció neve).
1. A a **TestConfig konfigurációs** kattintson **összeállítása**, és kattintson a **Igen**. Ekkor elindul a fordítási feladat.

    ![A fordítási gomb kiemelése TestConfig konfigurációs oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Az Azure Automationben konfiguráció fordítása, amikor telepíti a megfelelő bármely létrehozott csomópont-konfiguráció MOF-fájlok automatikusan a lekéréses kiszolgálón.

## <a name="viewing-a-compilation-job"></a>A fordítási feladat megtekintése

Után elindítja a fordítás, megtekintheti a a **fordítási feladatok** csempéje a **konfigurációs** lapot. A **fordítási feladatok** csempe megjeleníti a jelenleg futó, befejeződött, és sikertelen feladatok. Amikor megnyit egy fordítási feladat oldalát, a feladat, beleértve az esetleges hibákat vagy figyelmeztetéseket észlelt adatait mutatja, használt bemeneti paramétereket a konfigurációt, és a fordítási naplók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** kattintson **DSC-konfigurációk** alatt **konfigurációkezelés**.
1. Az a **DSC-konfigurációk** kattintson **TestConfig** (a korábban importált konfiguráció neve).
1. A **fordítási feladatok**, válassza ki a megtekinteni kívánt fordítási feladat. A **fordítási feladat** lap megnyitásakor, a fordítási feladatot indított dátumával.

    ![A fordítási feladat oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/CompilationJob.png)
1. Bármelyik csempére a a **fordítási feladat** oldalt, hogy további a feladat részleteit.

## <a name="viewing-node-configurations"></a>Csomópont-konfigurációk megtekintése

A fordítási feladat sikeres végrehajtása egy vagy több új csomópont-konfigurációkat hoz létre. Csomópont-konfiguráció, amelyet a lekéréses kiszolgálóra, és készen áll a lekért és a egy vagy több csomópont által alkalmazott MOF dokumentum. A csomópont-konfigurációkat tekintheti meg az Automation-fiókját a **DSC-Csomópontkonfigurációk** lapot. Csomópont-konfiguráció az űrlap-névvel rendelkezik *ConfigurationName*. *NodeName*.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A központi menüben kattintson **összes erőforrás** és majd az Automation-fiók nevét.
1. Az a **Automation-fiók** kattintson **State configuration (DSC)**, majd **konfigurációk**.

    ![A DSC-Csomópontkonfigurációk oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Egy Azure virtuális gép kezelése az Azure Automation DSC-előkészítés

Azure Automation DSC segítségével kezelheti az Azure virtuális gépek (klasszikus és Resource Manager), a helyszíni virtuális gépek, Linux rendszerű gépek, az AWS-beli virtuális gépek és a helyszíni fizikai gépek. Ebből a cikkből megtudhatja, hogyan lehet csak az Azure Resource Manager virtuális gépek előkészítése. Gépek, más típusú bevezetési kapcsolatos információk: [gépek előkészítése kezelésre, amelyet az Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>A bevezetni egy Azure Resource Manager virtuális gép által az Azure Automation DSC-felügyelethez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
1. A a **Automation-fiók** kattintson **State configuration (DSC)** alatt **konfigurációkezelés** válassza **csomópontok**.
1. Az a **csomópontok** kattintson **+ Hozzáadás**.

    ![Képernyőkép a DSC-csomópontok kiemelése a hozzáadása Azure-beli Virtuálisgép-gomb](./media/automation-dsc-getting-started/OnboardVM.png)

1. A virtuális gépek oldalon válassza ki a virtuális Gépet. **Az Azure-beli virtuális gépek hozzáadása** kattintson **válassza ki az előkészítendő virtuális gépek**.
1. Kattintson a **Connect** (Csatlakozás) gombra.

   > [!IMPORTANT]
   > Ez lehet egy Azure Resource Manager virtuális Gépen futó Windows Server 2008 R2 vagy újabb.

1. Az a **regisztrációs** lap, adja meg a virtuális géphez a alkalmazni szeretné a csomópont-konfiguráció nevét a **csomópont-konfiguráció neve** mezőbe. Ez pontosan egyeznie kell a csomópont-konfiguráció az Automation-fiók nevére. Ezen a ponton nyújtó nevét nem kötelező. A hozzárendelt csomópont-konfigurációt a csomópont előkészítése után módosíthatja.
   Ellenőrizze **szükség esetén újraindítás csomópont**, és kattintson a **OK**.

    ![A regisztrációs oldal képernyőképe](./media/automation-dsc-getting-started/RegisterVM.png)

    A csomópont-konfiguráció által meghatározott időközönként érvényesek a virtuális géphez megadott a **a konfigurációs mód gyakoriságának**, és a csomópont-konfiguráció által meghatározott időközönként frissítéseket keres a virtuális gép a **frissítése Gyakoriság**. Ezek az értékek módjáról további információkért lásd: [a Local Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. Az a **hozzáadása az Azure virtuális gépek** kattintson **létrehozás**.

Az Azure a virtuális gép bevezetési folyamata elindul. Ha elkészült, a virtuális gép megjelenik-e a **csomópontok** lapján a **State configuration (DSC)** lap az Automation-fiókban.

## <a name="viewing-the-list-of-dsc-nodes"></a>A DSC-csomópontok listájának megtekintése

Megtekintheti a listáját, amelyeket felügyeletre előkészített az Automation-fiókban lévő összes gép a **csomópontok** lapján a **State configuration (DSC)** lapot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. A a **Automation-fiók** kattintson **State configuration (DSC)**, majd kattintson a **csomópontok** fülre.

## <a name="viewing-reports-for-dsc-nodes"></a>DSC-csomópontok-jelentések megtekintése

Minden alkalommal, amikor az Azure Automation DSC felügyelt csomóponton, konzisztencia-ellenőrzést hajt végre a csomópont egy állapotjelentést küld a a lekéréses kiszolgálóra. Ezeket a jelentéseket megtekintheti adott csomópont az oldalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. A a **Automation-fiók** kattintson **State configuration (DSC)** és kattintson a **csomópontok** fülre.
4. Az a **csomópontok** listájához, válassza ki a megtekinteni kívánt csomópontra.
5. Az a **csomópont** lap, kattintson a jelentés a megtekinteni kívánt **jelentések**.

    ![A jelentés oldalának képernyőképe](./media/automation-dsc-getting-started/NodeReport.png)

Egy egyedi jelentés lapon látható a megfelelő konzisztencia-ellenőrzés állapota a következő információkat:

* A jelentés állapotának – hogy a csomópont "Megfelelő", a konfiguráció "Sikertelen", vagy a csomópont "Nem megfelelő" (amikor a csomópont szerepel **applyandmonitor** mód és a gép nem a kívánt állapotban van).
* A konzisztencia-ellenőrzés kezdetének időpontja.
* A konzisztencia-ellenőrzést az összes futásidejének.
* A konzisztencia-ellenőrzés típusa.
* Esetleges hibákat, beleértve a hibakódot és a hibaüzenet jelenik meg.
* A konfigurációban, és minden egyes (a csomópont-e a kívánt állapotban az adott erőforráshoz) erőforrás állapotának használt bármely DSC-erőforrások – kattintson a mindegyik erőforrást, melyeken részletesebb információkat találhat az adott erőforráshoz.
* A név, IP-cím és a csomópont konfigurációs módot.

Is **nyers jelentés megtekintése** a csomópont küld a kiszolgáló tényleges adatok megtekintéséhez. Az adatok használatával kapcsolatos további információkért lásd: [DSC jelentéskészítő kiszolgálójának használata](https://msdn.microsoft.com/powershell/dsc/reportserver).

Némi várakozás után csomópont előkészítve, mielőtt az első jelentés érhető el is igénybe vehet. Várjon az első jelentés körülbelül 30 perc után, felvétele egy csomópont lehet, hogy kell.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Egy másik csomópont-konfiguráció egy csomópont újbóli hozzárendelése

Hozzárendelhet egy csomópont kezdetben hozzárendelt hierarchiától különböző csomópont-konfiguráció használatához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. A a **Automation-fiók** kattintson **State configuration (DSC)** és kattintson a **csomópontok** fülre.
4. Az a **csomópontok** fülre, kattintson a hozzá szeretné rendelni a csomópont neve.
5. Kattintson a lap a csomópontra vonatkozóan, **csomópont-konfiguráció hozzárendelése**.

    ![Képernyőkép a csomópont kiemelése a csomópont hozzárendelése gombra](./media/automation-dsc-getting-started/AssignNode.png)
6. Az a **csomópont-konfiguráció hozzárendelése** lapra, jelölje be a csomópont-konfiguráció hozzárendelése a csomópontot, és kattintson a kívánt **OK**.

    ![A csomópont-konfiguráció hozzárendelése oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Csomópont regisztrációjának törlése

Ha már nincs szüksége az Azure Automation DSC kezelendő csomópont, regisztrációját is.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **State configuration (DSC)** és kattintson a **csomópontok** tab.git 
4. Az a **csomópontok** lapra, majd a csomópont regisztrációjának törlése kívánt nevére.
5. Kattintson a lap a csomópontra vonatkozóan, **Unregister**.

    ![A kiemelés, a Unregister gomb csomópont oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Az Azure Automation DSC áttekintése](automation-dsc-overview.md)
* [Gépek előkészítése kezelésre, amelyet az Azure Automation DSC](automation-dsc-onboarding.md)
* [Windows PowerShell Desired State Configuration áttekintése](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC-parancsmagok](/powershell/module/azurerm.automation/#automation)
* [Az Azure Automation DSC díjszabása](https://azure.microsoft.com/pricing/details/automation/)
