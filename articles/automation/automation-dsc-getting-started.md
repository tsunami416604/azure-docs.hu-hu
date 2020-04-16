---
title: Ismerkedés az Azure Automation állapotkonfigurációjával
description: Az Azure Automation állapotkonfigurációjának (DSC) leggyakoribb feladatainak magyarázata és példái
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 43268d5b48b44fffefa222f566c40151c85a5895
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392170"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Ismerkedés az Azure Automation állapotkonfigurációjával

Ez a cikk bemutatja, hogyan lehet elvégezni a leggyakoribb feladatokat az Azure Automation állapotkonfiguráció, például a konfigurációk létrehozása, importálása és összeállítása, bevezetési gépek kezelésére, és a jelentések megtekintése. Az Azure Automation állapotkonfigurációjának áttekintését az [Azure Automation állapotkonfiguráció – áttekintés című témakörben találja.](automation-dsc-overview.md) A kívánt állapotkonfigurációs (DSC) dokumentációról a [Windows PowerShell kívánt állapotkonfiguráció – áttekintéscímű témakörében olvashat.](/powershell/scripting/dsc/overview/overview)

Ez a cikk részletes útmutatót nyújt az Azure Automation-állapotkonfiguráció használatához. Ha olyan mintakörnyezetet szeretne, amely már be van állítva a cikkben ismertetett lépések benem követése nélkül, használhatja a következő Erőforrás-kezelő sablont: [Azure Automation felügyelt csomópontsablon.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration) Ez a sablon egy befejezett Azure Automation state konfigurációs környezetet állít be, beleértve az Azure Automation állapotkonfiguráció által felügyelt Azure-virtuális gép.This template sets up a completed Azure Automation State Configuration environment, including a Azure VM that is managed by Azure Automation State Configuration.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő példák kitöltéséhez a következő re van szükség:

- Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
- [Egy támogatott operációs rendszert](automation-dsc-overview.md#operating-system-requirements)futtató Azure Resource Manager virtuális gép (nem klasszikus). A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.

## <a name="creating-a-dsc-configuration"></a>DSC-konfiguráció létrehozása

Hozzon létre egy egyszerű [DSC-konfigurációt,](/powershell/scripting/dsc/configurations/configurations) amely a csomópontok hozzárendelésének módjától függően biztosítja a **Web-Server** Windows Szolgáltatás (IIS) jelenlétét vagy hiányát.

1. Indítsa el [a VSCode-ot](https://code.visualstudio.com/docs) (vagy bármely szövegszerkesztőt).
1. Írja be a következő szöveget:

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
1. Mentse a fájlt **TestConfig.ps1 néven.**

Ez a konfiguráció minden csomópontblokkban egy erőforrást, a [WindowsFeature erőforrást](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)hív meg. Ez az erőforrás biztosítja a **Web-Server** szolgáltatás jelenlétét vagy hiányát.

## <a name="importing-a-configuration-into-azure-automation"></a>Konfiguráció importálása az Azure Automationbe

Ezután importálja a konfigurációt az Automation-fiókba.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon válassza az **Állapotkonfiguráció (DSC)** lehetőséget a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Konfigurációk** fülre, majd a **Hozzáadás**gombra.
1. A Konfiguráció importálása ablaktáblán keresse meg a `TestConfig.ps1` számítógépen lévő fájlt.

   ![Képernyőkép a **Import Configuration** panelről](./media/automation-dsc-getting-started/AddConfig.png)

1. Kattintson az **OK** gombra.

## <a name="viewing-a-configuration-in-azure-automation"></a>Konfiguráció megtekintése az Azure Automationben

Miután importált egy konfigurációt, megtekintheti azt az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon válassza az **Állapotkonfiguráció (DSC)** lehetőséget a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Konfigurációk** fülre, majd a **TestConfig gombra.** Ez az előző eljárásban importált konfiguráció neve.
1. A TestConfig konfigurációablakában kattintson a **Konfigurációs forrás megtekintése**gombra.

   ![Képernyőkép a TestConfig konfigurációs panelről](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Megnyílik egy TestConfig konfigurációs forrásablak, amely a konfiguráció PowerShell-kódját jeleníti meg.

## <a name="compiling-a-configuration-in-azure-automation"></a>Konfiguráció fordítása az Azure Automationben

A kívánt állapot csomópontra való alkalmazása előtt az adott állapotot meghatározó DSC-konfigurációt egy vagy több csomópontkonfigurációba (MOF-dokumentumba) kell lefordítani, és az Automation DSC lekéréses kiszolgálóra kell helyezni. A konfigurációk Azure Automation-állapotkonfigurációban való összeállításának részletesebb leírását a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában című témakörben ismerteti.](automation-dsc-compile.md)
A konfigurációk összeállításáról a [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations)című témakörben talál további információt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Konfigurációk** fülre, majd a **TestConfig gombra.** Ez a korábban importált konfiguráció neve.
1. A TestConfig Konfiguráció ablaktábláján kattintson a **Fordítás**gombra, majd az **Igen**gombra. Ez egy összeállítási feladatot indít el.

   ![Képernyőkép a TestConfig konfigurációs lapjáról, amely kihangsúlyozza a fordításgombot](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Amikor lefordít egy konfigurációt az Azure Automationben, automatikusan telepíti a létrehozott csomópontkonfigurációs MOF-fájlokat a lekéréses kiszolgálóra.

## <a name="viewing-a-compilation-job"></a>Fordítási feladat megtekintése

A fordítás megkezdése után megtekintheti azt a **Konfiguráció** lap **Fordítási feladatok** csempéjén. A **Fordítási feladatok** csempe jelenleg futó, befejezett és sikertelen feladatokat jelenít meg. Amikor megnyit egy fordítási feladat ablaktábláját, az információkat jelenít meg az adott feladatról, beleértve a felmerült hibákat vagy figyelmeztetéseket, a konfigurációban használt bemeneti paramétereket és a fordítási naplókat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Konfigurációk** fülre, majd a **TestConfig gombra.** Ez a korábban importált konfiguráció neve.
1. A **Fordítási feladatok csoportban**válassza ki a megtekinteni kívánt fordítási feladatot. Megnyílik egy összeállítási feladat ablaktábla, amely a fordítási feladat elindításának dátumával van ellátva.

   ![Képernyőkép a Fordítási feladat lapról](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kattintson bármelyik csempére a Fordítási feladat ablaktáblában a feladattal kapcsolatos további részletek megtekintéséhez.

## <a name="viewing-node-configurations"></a>Csomópont-konfigurációk megtekintése

A fordítási feladat sikeres befejezése egy vagy több új csomópontkonfigurációt hoz létre. A csomópontkonfiguráció egy MOF-dokumentum, amely a lekéréses kiszolgálóra van telepítve, és készen áll egy vagy több csomópont lehúzására és alkalmazására. A csomópontkonfigurációk az Automation-fiók ban az állapotkonfiguráció (DSC) lapon tekintheti meg. A csomópontkonfigurációnak neve van `ConfigurationName.NodeName`az űrlappal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Lefordított konfigurációk** fülre.

   ![Képernyőkép a Lefordított konfigurációk lapról](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Bevezetés egy Azure virtuális gép felügyelethez az Azure Automation állapotkonfigurációjával

Az Azure Automation State Configuration használatával kezelheti az Azure virtuális gépeket (klasszikus és erőforrás-kezelőt is), a helyszíni virtuális gépeket, a Linux-gépeket, az AWS virtuális gépeket és a helyszíni fizikai gépeket. Ebben a cikkben megtudhatja, hogyan csak az Azure Resource Manager virtuális gépek. A más típusú gépek bevezetéséről a [Bevezetési gépek az Azure Automation állapotkonfigurációáltali kezeléscímű témakörben](automation-dsc-onboarding.md)talál.

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Azure Resource Manager virtuális gép alaplapja az Azure Automation állapotkonfigurációja által im-hez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon válassza a **Csomópontok** lapot, majd kattintson a **+ Hozzáadás gombra.**

   ![Képernyőkép a DSC-csomópontok lapról, amely kiemeli az Azure virtuális gép hozzáadása gombot](./media/automation-dsc-getting-started/OnboardVM.png)

1. A Virtuális gépek ablaktáblán válassza ki a virtuális gépet.
1. A Virtuális gép részletes ablaktábláján kattintson a **+ Csatlakozás gombra.**

   > [!IMPORTANT]
   > A virtuális gépnek [támogatott operációs rendszert](automation-dsc-overview.md#operating-system-requirements)futtató Azure Resource Manager virtuális gépnek kell lennie.

2. A Regisztráció lapon válassza ki a csomópont konfigurációját a virtuális gépre a **csomópont konfigurációjának neve** mezőben. Ezen a ponton nem kötelező nevet adni. A csomópont bevezetésután módosíthatja a hozzárendelt csomópont konfigurációját.

3. Szükség esetén jelölje be **a Csomópont újraindítása**jelölőnégyzetet, majd kattintson **az OK gombra.**

   ![Képernyőkép a Regisztráció panelről](./media/automation-dsc-getting-started/RegisterVM.png)

   A megadott csomópontkonfiguráció a **konfigurációs mód gyakoriságához**megadott értékben megadott időközönként lesz alkalmazva a virtuális gépre. A virtuális gép a **frissítési gyakoriság** érték által meghatározott időközönként frissítéseket keres a csomópont konfigurációjához. Az értékek használatának módjáról [a Helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig)című témakörben talál további információt.

Az Azure elindítja a virtuális gép bevezetési folyamatát. Ha elkészült, a virtuális gép megjelenik az Automation-fiók Állapotkonfiguráció (DSC) **lapjának Csomópontok** lapján.

## <a name="viewing-the-list-of-managed-nodes"></a>A felügyelt csomópontok listájának megtekintése

Az Állapotkonfiguráció (DSC) lap **Csomópontok** lapján megtekintheti a felügyeletre beépített gépek listáját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Csomópontok** fülre.

## <a name="viewing-reports-for-managed-nodes"></a>Felügyelt csomópontok jelentésének megtekintése

Minden alkalommal, amikor az Azure Automation-állapot konfigurációja konzisztencia-ellenőrzést végez egy felügyelt csomóponton, a csomópont állapotjelentést küld a lekéréses kiszolgálónak. Ezeket a jelentéseket az adott csomópont oldalán tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Csomópontok** fülre. Itt láthatja a konfigurációs állapot áttekintését és az egyes csomópontok részleteit.

   ![Képernyőkép a Csomópont lapról](./media/automation-dsc-getting-started/NodesTab.png)

1. A **Csomópontok** lapon kattintson a csomópontrekordra a jelentés megnyitásához. Kattintson arra a jelentésre, amelyet meg szeretne tekinteni a további jelentési adatokkal.

   ![Képernyőkép a Jelentés lapról](./media/automation-dsc-getting-started/NodeReport.png)

Az egyes jelentések paneljén a következő állapotadatok láthatók a megfelelő konzisztencia-ellenőrzéshez:

- A jelentés állapota. Lehetséges értékek:
    * Megfelelő – a csomópont megfelel az ellenőrzésnek.
   * Nem sikerült - a konfiguráció nem tudta ellenőrizni.
   * Nem megfelelő – a `ApplyandMonitor` csomópont módban van, és a gép nincs a kívánt állapotban.
- A konzisztencia-ellenőrzés kezdési időpontja.
- A konzisztencia-ellenőrzés teljes futásideje.
- A konzisztencia-ellenőrzés típusa.
- Bármilyen hiba, beleértve a hibakódot és a hibaüzenetet.
- A konfigurációban használt DSC-erőforrások és az egyes erőforrások állapota (függetlenül attól, hogy a csomópont az adott erőforrás kívánt állapotában van-e). Az egyes erőforrásokra kattintva részletesebb információkat kaphat az adott erőforrásról.
- A csomópont neve, IP-címe és konfigurációs módja.

A Nyers **jelentés megtekintése** gombra kattintva megtekintheti a csomópont által a kiszolgálónak küldött tényleges adatokat is.
Az adatok használatáról további információt a [DSC-jelentéskiszolgáló használata című témakörben talál.](/powershell/scripting/dsc/pull-server/reportserver)

Az első jelentés elérhetővé vájata előtt eltarthat egy ideig, miután egy csomópontot bevan rakva. Előfordulhat, hogy a csomópont fedélzetének bedeszkázása után legfeljebb 30 percet kell várnia az első jelentésre.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Csomópont újbóli hozzárendelése másik csomópontkonfigurációba

Hozzárendelhet egy csomópontot, hogy az eredetileg hozzárendeltkonfigurációtól eltérő csomópontkonfigurációt használjon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Csomópontok** fülre.
1. A **Csomópontok** lapon kattintson az újrahozzárendelni kívánt csomópont nevére.
1. A csomópont lapján kattintson a **Csomópontkonfiguráció hozzárendelése gombra.**

    ![Képernyőkép a Csomópont részletei lapról, amely kiemeli a Csomópont konfigurációs gombjának hozzárendelése](./media/automation-dsc-getting-started/AssignNode.png)

1. A Csomópont konfigurációjának hozzárendelése lapon válassza ki azt a csomópontkonfigurációt, amelyhez hozzá szeretné rendelni a csomópontot, majd kattintson az **OK**gombra.

    ![Képernyőkép a Csomópont konfigurációjának hozzárendelése lapról](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Csomópont regisztrációjának megszüntetése

Ha már nem szeretné, hogy egy csomópontot az Azure Automation DSC kezeljen, törölje a regisztrációját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson a **Minden erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **Állapotkonfiguráció (DSC)** elemre a **Konfigurációkezelés csoportban.**
1. Az Állapotkonfiguráció (DSC) lapon kattintson a **Csomópontok** fülre.
1. A **Csomópontok** lapon kattintson a törölni kívánt csomópont nevére.
1. A csomópont ablaktábláján kattintson a **Regisztráció törlése gombra.**

    ![Képernyőkép a Csomópont részletei lapról, amely kiemeli a Regisztráció törlése gombot](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Az Azure Automation állapotkonfigurációja – áttekintés](automation-dsc-overview.md)
- [Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezeléshez](automation-dsc-onboarding.md)
- [A Windows PowerShell kívánt állapotkonfigurációja – áttekintés](/powershell/scripting/dsc/overview/overview)
- [Az Azure Automation állapotkonfigurációs díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)