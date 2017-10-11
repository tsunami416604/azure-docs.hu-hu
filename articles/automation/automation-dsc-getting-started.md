---
title: "Ismerkedés az Azure Automation DSC szolgáltatással |} Microsoft Docs"
description: "MAGYARÁZAT és példákat a leggyakoribb feladatokat az Azure Automation szükséges konfiguráló (DSC)"
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;eslesar
ms.openlocfilehash: 8a10d961ad7c107c68b57c64ee6c88544ff8832b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="getting-started-with-azure-automation-dsc"></a>Ismerkedés az Azure Automation DSC
Ez a témakör ismerteti a leggyakoribb feladatokat az Azure Automation szükséges konfiguráló (DSC), például a létrehozása, importálása, és konfigurációk, kezeléséhez, a bevezetési gépek fordítása és jelentések megtekintése. Milyen Azure Automation DSC áttekintést van, a következő témakörben: [Azure Automation DSC – áttekintés](automation-dsc-overview.md). A DSC-dokumentáció, lásd: [Windows PowerShell kívánt állapot beállítása – áttekintés](https://msdn.microsoft.com/PowerShell/dsc/overview).

Ez a témakör részletesen ismerteti, Azure Automation DSC használata. Ha azt szeretné, hogy egy minta-környezet, amely már be van állítva a jelen témakörben ismertetett lépések nélkül, [a következő ARM-sablon](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup). Ez a sablon állít be egy befejezett Azure Automation DSC-környezetben, például egy Azure Automation DSC által felügyelt Azure virtuális Gépen.

## <a name="prerequisites"></a>Előfeltételek
Ebben a témakörben szereplő példák elvégzéséhez a következőkre szükség:

* Egy Azure Automation-fiók. Egy Azure Automation futtató fiók létrehozása, lásd: [Azure futtató fiók](automation-sec-configure-azure-runas-account.md).
* Az Azure Resource Manager virtuális gép (klasszikus) Windows Server 2008 R2 rendszerű vagy újabb. Virtuális gép létrehozása, lásd: [az első Windows rendszerű virtuális gép létrehozása az Azure portálon](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>A DSC-konfiguráció létrehozása
Létre fogunk hozni egy egyszerű [DSC-konfiguráció](https://msdn.microsoft.com/powershell/dsc/configurations) megléte vagy hiánya, ezzel biztosítható a **webkiszolgáló** Windows szolgáltatás (IIS), attól függően, hogy hogyan lehet kijelölni csomópontok.

1. Indítsa el a Windows PowerShell ISE (és bármilyen szövegszerkesztővel).
2. Írja be a következőket:
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
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
3. Mentse a fájlt `TestConfig.ps1`.

Ez a konfiguráció minden csomópont blokkban meghívja a egy erőforrást a [WindowsFeature erőforrás](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), megléte vagy hiánya, ezzel biztosítható a **webkiszolgáló** szolgáltatás.

## <a name="importing-a-configuration-into-azure-automation"></a>Azure Automation konfiguráció importálása
Ezután azt fogja importálnia kell a konfiguráció az Automation-fiók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **a DSC-konfigurációk**.
4. A a **a DSC-konfigurációk** panelen kattintson a **hozzáadása egy konfigurációs**.
5. Az a **konfiguráció importálása** panelen keresse meg a `TestConfig.ps1` fájlt a számítógépen.
   
    ![Képernyőfelvétel a ** importálási konfigurációs ** panel](./media/automation-dsc-getting-started/AddConfig.png)
6. Kattintson az **OK** gombra.

## <a name="viewing-a-configuration-in-azure-automation"></a>A beállítások megjelenítése az Azure Automationben
Miután importálta a konfiguráció, megtekintheti az Azure portálon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **a DSC-konfigurációk**
4. Az a **a DSC-konfigurációk** panelen kattintson a **TestConfig** (azt a nevet, a konfiguráció az előző eljárásban importált).
5. Az a **TestConfig konfigurációs** panelen kattintson a **konfigurációs forrás megtekintése**.
   
    ![A TestConfig konfigurációs paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    A **TestConfig konfigurációs forrás** panel jelenik meg, amelyen a PowerShell-kódot, a konfiguráció.

## <a name="compiling-a-configuration-in-azure-automation"></a>Az Azure Automationben konfiguráció fordítása
Alkalmazása előtt ki a kívánt állapot csomóponthoz, a DSC-konfiguráció meghatározása az állapotban kell egy vagy több csomópont-konfigurációt (MOF dokumentum) lefordítva, és a Automation DSC-lekéréses kiszolgálóra. Azure Automation DSC-konfigurációja összeállításának részletes ismertetését lásd: [Azure Automation DSC-konfigurációja fordítása](automation-dsc-compile.md). Konfiguráció fordítása kapcsolatos további információkért lásd: [a DSC-konfigurációk](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **a DSC-konfigurációk**
4. Az a **a DSC-konfigurációk** panelen kattintson a **TestConfig** (a korábban importált konfiguráció neve).
5. Az a **TestConfig konfigurációs** panelen kattintson a **fordítási**, és kattintson a **Igen**. Egy fordítási feladat elindul.
   
    ![Fordítási gomb kiemelés TestConfig konfigurációs paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Ha egy Azure Automation konfigurációs lefordításához azt automatikusan telepíti a bármely létrehozott csomópont-konfiguráció MOF-ot a lekérési kiszolgálójával.
> 
> 

## <a name="viewing-a-compilation-job"></a>Egy fordítási feladat megtekintése
Egy fordítási elindítása után megtekintheti a a **fordítási feladatok** csempére a **konfigurációs** panelen. A **fordítási feladatok** csempe megjeleníti a jelenleg futó, befejeződött, és sikertelen feladatok. Amikor megnyit egy fordítási feladat panelen, azt mutatja, beleértve az esetleges hibákat vagy figyelmeztetéseket észlelt feladatot információt, a bemeneti paramétereket használni a konfigurációs és a fordítás naplók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **a DSC-konfigurációk**.
4. Az a **a DSC-konfigurációk** panelen kattintson a **TestConfig** (a korábban importált konfiguráció neve).
5. Az a **fordítási feladatok** csempéjén a **TestConfig konfigurációs** panelen kattintson a felsorolt feladatok bármelyikét. A **fordítási feladat** panelen nyitja meg, a fordítási feladat el lett indítva dátumával.
   
    ![A fordítási feladat paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/CompilationJob.png)
6. Kattintson az összes csempe az a **fordítási feladat** panelen tekintse meg a további részleteket a feladat.

## <a name="viewing-node-configurations"></a>Csomópont-konfigurációt megtekintése
Egy fordítási feladat sikeres létrehozása után egy vagy több új csomópont-konfigurációkat hoz létre. A csomópont-konfiguráció a lekérési kiszolgálójával, és készen áll a lekért és egy vagy több csomópont által alkalmazott telepített MOF dokumentum. Az Automation-fiókban lévő meg tudja tekinteni a csomópont-konfigurációt a **DSC-Csomópontkonfigurációk** panelen. A csomópont-konfiguráció van az űrlap egy nevű *ConfigurationName*. *Csomópontnév*.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **DSC-Csomópontkonfigurációk**.
   
    ![A DSC-Csomópontkonfigurációk paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Egy Azure virtuális gép Management az Azure Automation DSC Szolgáltatásban
Azure Automation DSC segítségével kezelheti az Azure virtuális gépeken (klasszikus és Resource Manager), a helyszíni virtuális gépek, Linux rendszerű gépek, AWS virtuális gépek és a helyszíni fizikai gépeket. Ebben a témakörben azt fedezi történő előkészítésére csak Azure Resource Manager virtuális gépeken. Gépek, más típusú bevezetési kapcsolatos információk: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>A bevezetni az Azure Resource Manager virtuális gép Azure Automation DSC általi kezelésre
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **DSC-csomópontok**.
4. Az a **DSC-csomópontok** panelen kattintson a **adja hozzá az Azure virtuális gép**.
   
    ![Az Azure VM hozzáadása gomb kiemelés DSC-csomópontok paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/OnboardVM.png)
5. Az a **adja hozzá az Azure virtuális gépek** panelen kattintson a **válassza ki a bevezetni kívánt virtuális gépeket**.
6. Az a **válassza ki a virtuális gépek** panelen, válassza ki a virtuális gép kívánt bevezetésében, és kattintson **OK**.
   
   > [!IMPORTANT]
   > Az értéknek kell lennie az Azure Resource Manager virtuális gép Windows Server 2008 R2 rendszerű vagy újabb.
   > 
   > 
7. Az a **adja hozzá az Azure virtuális gépek** panelen kattintson a **regisztrációs adatok konfigurálása**.
8. Az a **regisztrációs** panelen adja meg a virtuális gépre, az alkalmazni kívánt csomópont-konfiguráció nevét a **csomópont-konfiguráció neve** mezőbe. Ez pontosan egyeznie kell a csomópont-konfiguráció az Automation-fiók nevét. Ezen a ponton biztosító nevét nem kötelező megadni. A csomópont bevezetése után a hozzárendelt csomópont-konfiguráció módosítása
   Ellenőrizze **szükség esetén újraindítás csomópont**, és kattintson a **OK**.
   
    ![A regisztrációs paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/RegisterVM.png)
   
    A megadott csomópont-konfiguráció által meghatározott időközönként alkalmazzuk a virtuális gép a **konfigurációs mód gyakoriságának**, és a virtuális gép frissítések a csomópont-konfiguráció által meghatározott időközönként ellenőrzi a  **Frissítési gyakoriság**. Ezek az értékek módjáról további információkért lásd: [konfigurálása a helyi Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
9. Az a **adja hozzá az Azure virtuális gépek** panelen kattintson a **létrehozása**.

Azure indul el a virtuális gép folyamatán. Ha befejeződött, a virtuális Gépet fog megjelenni a **DSC-csomópontok** az Automation-fiók panelén.

## <a name="viewing-the-list-of-dsc-nodes"></a>A DSC-csomópontok listájának megtekintése
Lett előkészítve felügyeletre a az Automation-fiókban lévő összes gép listáját megtekintheti a **DSC-csomópontok** panelen.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **DSC-csomópontok**.

## <a name="viewing-reports-for-dsc-nodes"></a>A DSC-csomópontok számára jelentések megtekintése
Minden alkalommal, amikor az Azure Automation DSC a felügyelt csomóponton konzisztencia-ellenőrzést végez a csomópont visszaküldi egy állapotjelentést a lekérési kiszolgálójával. Az adott csomópont a panelen megtekintheti ezeket a jelentéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **DSC-csomópontok**.
4. Az a **jelentések** csempére, kattintson a jelentések listájában.
   
    ![A jelentés paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/NodeReport.png)

Egy egyedi jelentés paneljén megtekintheti a következő állapotadatokat a megfelelő konzisztencia-ellenőrzés céljából:

* A jelentés állapotának – hogy a csomópont "Megfelelő", a konfiguráció "Sikertelen" vagy a csomópont "Nem megfelelő" (ha van a csomópont **applyandmonitor** mód és a gép nincs a kívánt állapot).
* A konzisztencia-ellenőrzés kezdési idejét.
* A teljes futási a konzisztencia-ellenőrzés céljából.
* A konzisztencia-ellenőrzés típusa.
* Hibáit, beleértve a hibakódot és a hibaüzenet. 
* A konfigurációban, és minden egyes (hogy a csomópont állapotban van a kívánt erőforrás) erőforrás állapotának használt DSC erőforrásokat – meg az egyes erőforrások további részletes információk az adott erőforráshoz.
* A név, IP-cím és a csomópont konfigurációs módja.

Is **nyers jelentés megtekintéséhez** a csomópont küld a kiszolgálónak a tényleges adatokat. Adatok használatával kapcsolatos további információkért lásd: [a DSC-jelentés kiszolgálóval](https://msdn.microsoft.com/powershell/dsc/reportserver).

Miután egy csomópont előkészítve, mielőtt az első jelentés érhető el némi időbe telhet. Várjon, amíg az első jelentés akár 30 percet követően bevezetésében csomópont módosítania.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Egy csomópont egy másik csomópont-konfiguráció újbóli hozzárendelése
Egy csomópont kezdetben rendelve egy másik csomópont-konfiguráció rendelhet hozzá.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **DSC-csomópontok**.
4. Az a **DSC-csomópontok** panelen kattintson a szeretne rendelni a csomópont neve.
5. Az adott csomópont paneljén kattintson **hozzárendelése csomópont**.
   
    ![A csomópont hozzárendelése gomb kiemelés csomópont paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/AssignNode.png)
6. Az a **csomópont-konfiguráció hozzárendelése** panelen válassza ki a kívánt rendelje hozzá a csomópontot, és kattintson a csomópont-konfiguráció **OK**.
   
    ![A csomópont-konfiguráció hozzárendelése paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Egy csomópont
Ha már nem szeretné, hogy egy csomópont Azure Automation DSC Szolgáltatásban történő kezeléshez, regisztrációjának megszüntetésére.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **DSC-csomópontok**.
4. Az a **DSC-csomópontok** panelen kattintson a kívánt regisztrációjának törlése a csomópont neve.
5. Az adott csomópont paneljén kattintson **Unregister**.
   
    ![A Unregister gomb kiemelés csomópont paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Azure Automation DSC – áttekintés](automation-dsc-overview.md)
* [Azure Automation DSC általi kezelésre bevezetési gépek](automation-dsc-onboarding.md)
* [A Windows PowerShell célállapot-konfiguráló áttekintése](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC-parancsmagokkal](/powershell/module/azurerm.automation/#automation)
* [Azure Automation DSC – díjszabás](https://azure.microsoft.com/pricing/details/automation/)

