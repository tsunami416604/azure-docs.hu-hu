---
title: "Ismerkedés az Azure Automation DSC szolgáltatással |} Microsoft Docs"
description: "MAGYARÁZAT és példákat a leggyakoribb feladatokat az Azure Automation szükséges konfiguráló (DSC)"
services: automation
documentationcenter: na
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;gwallace
ms.openlocfilehash: 45afb09f09e754e37ae8dba02e1e16b3fde1e408
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="getting-started-with-azure-automation-dsc"></a>Ismerkedés az Azure Automation DSC
Ez a cikk ismerteti a leggyakoribb feladatokat az Azure Automation szükséges konfiguráló (DSC), például a létrehozása, importálása, és konfigurációk, kezeléséhez, a bevezetési gépek fordítása és jelentések megtekintése. Milyen Azure Automation DSC áttekintést van, a következő témakörben: [Azure Automation DSC – áttekintés](automation-dsc-overview.md). A DSC-dokumentáció, lásd: [Windows PowerShell kívánt állapot beállítása – áttekintés](https://msdn.microsoft.com/PowerShell/dsc/overview).

A cikkben egy Azure Automation DSC használata lépésenkénti útmutatót. Ha azt szeretné, hogy egy minta-környezet, amely már be van állítva a cikkben ismertetett lépések nélkül, a következő használható [Resource Manager-sablon](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup). Ez a sablon állít be egy befejezett Azure Automation DSC-környezetben, például egy Azure Automation DSC által felügyelt Azure virtuális Gépen.

## <a name="prerequisites"></a>Előfeltételek
Ebben a cikkben szereplő példák elvégzéséhez a következőkre szükség:

* Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
* Az Azure Resource Manager virtuális gép (klasszikus) Windows Server 2008 R2 rendszerű vagy újabb. A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.

## <a name="creating-a-dsc-configuration"></a>A DSC-konfiguráció létrehozása
Létrehozhat egy egyszerű [DSC-konfiguráció](https://msdn.microsoft.com/powershell/dsc/configurations) megléte vagy hiánya, ezzel biztosítható a **webkiszolgáló** Windows szolgáltatás (IIS), attól függően, hogy hogyan lehet kijelölni csomópontok.

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
Ezután a konfigurációt importálja az Automation-fiók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** lapon jelölje be **a DSC-konfigurációk** alatt **konfigurációkezelés**.
4. A a **a DSC-konfigurációk** kattintson **+ Hozzáadás konfiguráció**.
5. Az a **konfiguráció importálása** párbeszédpanelen keresse meg a `TestConfig.ps1` fájlt a számítógépen.
   
    ![Képernyőfelvétel a ** importálási konfigurációs ** panel](./media/automation-dsc-getting-started/AddConfig.png)
6. Kattintson az **OK** gombra.

## <a name="viewing-a-configuration-in-azure-automation"></a>A beállítások megjelenítése az Azure Automationben
Miután importálta a konfiguráció, megtekintheti az Azure portálon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** lapon jelölje be **a DSC-konfigurációk** alatt **konfigurációkezelés**.
4. Az a **a DSC-konfigurációk** kattintson **TestConfig** (azt a nevet, a konfiguráció az előző eljárásban importált).
5. Az a **TestConfig konfigurációs** kattintson **konfigurációs forrás megtekintése**.
   
    ![A TestConfig konfigurációs paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    A **TestConfig konfigurációs forrás** panel jelenik meg, amelyen a PowerShell-kódot, a konfiguráció.

## <a name="compiling-a-configuration-in-azure-automation"></a>Az Azure Automationben konfiguráció fordítása
Alkalmazása előtt ki a kívánt állapot csomóponthoz, a DSC-konfiguráció meghatározása az állapotban kell egy vagy több csomópont-konfigurációt (MOF dokumentum) lefordítva, és a Automation DSC-lekéréses kiszolgálóra. Azure Automation DSC-konfigurációja összeállításának részletes ismertetését lásd: [Azure Automation DSC-konfigurációja fordítása](automation-dsc-compile.md). Konfiguráció fordítása kapcsolatos további információkért lásd: [a DSC-konfigurációk](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **a DSC-konfigurációk** alatt **konfigurációkezelés**.
4. Az a **a DSC-konfigurációk** kattintson **TestConfig** (a korábban importált konfiguráció neve).
5. Az a **TestConfig konfigurációs** kattintson **fordítási**, és kattintson a **Igen**. Egy fordítási feladat elindul.
   
    ![A fordítási gomb kiemelés TestConfig konfigurációs oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Ha egy Azure Automation konfigurációs lefordításához azt automatikusan telepíti a bármely létrehozott csomópont-konfiguráció MOF-ot a lekérési kiszolgálójával.
> 
> 

## <a name="viewing-a-compilation-job"></a>Egy fordítási feladat megtekintése
Egy fordítási elindítása után megtekintheti a a **fordítási feladatok** csempére a **konfigurációs** panelen. A **fordítási feladatok** csempe megjeleníti a jelenleg futó, befejeződött, és sikertelen feladatok. Amikor megnyit egy fordítási feladat panelen, azt mutatja, beleértve az esetleges hibákat vagy figyelmeztetéseket észlelt feladatot információt, a bemeneti paramétereket használni a konfigurációs és a fordítás naplók.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **a DSC-konfigurációk** alatt **konfigurációkezelés**.
4. Az a **a DSC-konfigurációk** kattintson **TestConfig** (a korábban importált konfiguráció neve).
5. A **fordítási feladatok**, válassza ki a megtekinteni kívánt fordítási feladat. A **fordítási feladat** lap megnyitásakor, a fordítási feladat el lett indítva dátumával.
   
    ![A fordítási feladat oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/CompilationJob.png)
6. Kattintson az összes csempe az a **fordítási feladat** lapot, melyen megtekintheti további részleteket a feladatot.

## <a name="viewing-node-configurations"></a>Csomópont-konfigurációt megtekintése
Egy fordítási feladat sikeres létrehozása után egy vagy több új csomópont-konfigurációkat hoz létre. A csomópont-konfiguráció a lekérési kiszolgálójával, és készen áll a lekért és egy vagy több csomópont által alkalmazott telepített MOF dokumentum. Az Automation-fiókban lévő meg tudja tekinteni a csomópont-konfigurációt a **DSC-Csomópontkonfigurációk** panelen. A csomópont-konfiguráció van az űrlap egy nevű *ConfigurationName*. *Csomópontnév*.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A központ menüben kattintson a **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** panelen kattintson a **DSC-Csomópontkonfigurációk**.
   
    ![A DSC-Csomópontkonfigurációk paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Egy Azure virtuális gép Management az Azure Automation DSC Szolgáltatásban
Azure Automation DSC segítségével kezelheti az Azure virtuális gépeken (klasszikus és Resource Manager), a helyszíni virtuális gépek, Linux rendszerű gépek, AWS virtuális gépek és a helyszíni fizikai gépeket. Ebből a cikkből megismerheti, hogyan kell bevezetni csak Azure Resource Manager virtuális gépek. Gépek, más típusú bevezetési kapcsolatos információk: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>A bevezetni az Azure Resource Manager virtuális gép Azure Automation DSC általi kezelésre
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **DSC-csomópontok** alatt **konfigurációkezelés**...
4. Az a **DSC-csomópontok** kattintson **adja hozzá az Azure virtuális gép**.
   
    ![A kiemelés az Azure VM hozzáadása gombra a DSC-csomópontok oldalát bemutató képernyőkép](./media/automation-dsc-getting-started/OnboardVM.png)
5. Az a **virtuális gépek** oldalon válassza ki a virtuális Gépet.  **Azure virtuális gépek hozzáadása** kattintson **válassza ki a bevezetni kívánt virtuális gépeket**.
6. Kattintson a **Connect** (Csatlakozás) gombra.
   
   > [!IMPORTANT]
   > Az értéknek kell lennie az Azure Resource Manager virtuális gép Windows Server 2008 R2 rendszerű vagy újabb.
   > 
   > 
1. Az a **regisztrációs** lapján adja meg a virtuális gépre, az alkalmazni kívánt csomópont-konfiguráció nevét a **csomópont-konfiguráció neve** mezőbe. Ez pontosan egyeznie kell a csomópont-konfiguráció az Automation-fiók nevét. Ezen a ponton biztosító nevét nem kötelező megadni. A csomópont bevezetése után a hozzárendelt csomópont-konfiguráció módosítása
   Ellenőrizze **szükség esetén újraindítás csomópont**, és kattintson a **OK**.
   
    ![A regisztrációs paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/RegisterVM.png)
   
    A csomópont-konfiguráció által meghatározott időközönként alkalmazzák a virtuális géphez megadott a **konfigurációs mód gyakoriságának**, és a virtuális gép frissítések a csomópont-konfiguráció által meghatározott időközönként ellenőrzi a **frissítése Gyakoriság**. Ezek az értékek módjáról további információkért lásd: [konfigurálása a helyi Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
9. Az a **adja hozzá az Azure virtuális gépek** panelen kattintson a **létrehozása**.

Azure elindítja a folyamatot, a virtuális gép. Ha befejeződött, a virtuális gép megjelenik a **DSC-csomópontok** az Automation-fiók panelén.

## <a name="viewing-the-list-of-dsc-nodes"></a>A DSC-csomópontok listájának megtekintése
Lett előkészítve felügyeletre a az Automation-fiókban lévő összes gép listáját megtekintheti a **DSC-csomópontok** panelen.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **DSC-csomópontok**.

## <a name="viewing-reports-for-dsc-nodes"></a>A DSC-csomópontok számára jelentések megtekintése
Minden alkalommal, amikor az Azure Automation DSC a felügyelt csomóponton konzisztencia-ellenőrzést végez a csomópont visszaküldi egy állapotjelentést a lekérési kiszolgálójával. Ezek a jelentések az adott csomópont a lapon tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **DSC-csomópontok**.
4. Az a **DSC-csomópontok** listára, válassza ki a megtekinteni kívánt csomópontot.
5. Az a **csomópont** lapján kattintson a jelentés a megtekinteni kívánt **jelentések**.
   
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
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **DSC-csomópontok**.
4. Az a **DSC-csomópontok** lapján kattintson a szeretne rendelni a csomópont neve.
5. Kattintson a lap az adott csomópont **hozzárendelése csomópont**.
   
    ![A csomópont hozzárendelése gomb kiemelés csomópont paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/AssignNode.png)
6. Az a **csomópont-konfiguráció hozzárendelése** lapon, válassza ki a kívánt rendelje hozzá a csomópontot, és kattintson a csomópont-konfiguráció **OK**.
   
    ![A csomópont-konfiguráció hozzárendelése paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Egy csomópont
Ha már nem szeretné, hogy egy csomópont Azure Automation DSC Szolgáltatásban történő kezeléshez, regisztrációjának megszüntetésére.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali **összes erőforrás** és majd az Automation-fiók nevét.
3. Az a **Automation-fiók** kattintson **DSC-csomópontok**.
4. Az a **DSC-csomópontok** lapján kattintson a kívánt regisztrációjának törlése a csomópont neve.
5. Kattintson a lap az adott csomópont **Unregister**.
   
    ![A Unregister gomb kiemelés csomópont paneljét bemutató képernyőkép](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Azure Automation DSC – áttekintés](automation-dsc-overview.md)
* [Azure Automation DSC általi kezelésre bevezetési gépek](automation-dsc-onboarding.md)
* [A Windows PowerShell célállapot-konfiguráló áttekintése](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC-parancsmagokkal](/powershell/module/azurerm.automation/#automation)
* [Azure Automation DSC – díjszabás](https://azure.microsoft.com/pricing/details/automation/)

