---
title: StorSimple-eszközök kezelése az Azure Automation Runbook használatával
description: Ismerje meg, hogyan automatizálhatja az Azure Automation Runbookot a StorSimple-feladatok automatizálásához
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 727bebe0c190ed4dff4408884c45fe166ad541a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276972"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>StorSimple-eszközök kezelése azure automation-i runbookokkal

Ez a cikk azt ismerteti, hogy az Azure Automation runbookok hogyan használhatók a StorSimple 8000 sorozatú eszköz kezelésére az Azure Portalon. A minta runbook tartalmazza, hogy végigvezeti a lépéseket a környezet konfigurálása a runbook végrehajtása.


## <a name="configure-add-and-run-azure-runbook"></a>Az Azure runbook konfigurálása, hozzáadása és futtatása

Ez a szakasz egy példa a Windows PowerShell-parancsfájl storSimple és részletezi a különböző szükséges lépéseket a parancsfájl importálásához egy runbook, majd közzéteszi és végrehajtja a runbook.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

* a StorSimple 8000 sorozatú eszközzel regisztrált StorSimple Eszközkezelő szolgáltatáshoz társított aktív Azure-előfizetés.

* Windows PowerShell 5.0 telepítve van a számítógépre (Vagy, a Windows Server host a StorSimple használata esetén).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Automatizálási runbookmodul létrehozása a Windows PowerShellben

A StorSimple 8000 sorozatú eszközkezelés automatizálási moduljának létrehozásához hajtsa végre a következő lépéseket:

1. Indítsa el a Windows PowerShellt. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappára.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. Töltse le a [NuGet CLI-t](https://www.nuget.org/downloads) az előző lépésben létrehozott mappában. Vannak különböző változatai _nuget.exe_. Válassza ki az SDK-nak megfelelő verziót. Minden letöltési hivatkozás közvetlenül egy _.exe_ fájlra mutat. Ne a jobb gombbal kattintson a jobb gombbal, és mentse a fájlt a számítógépre, és ne futtassa a böngészőből.

    A parancsfájl letöltéséhez és tárolásához a parancsfájl t is futtatható, amelykorábban a korábban létrehozott mappában volt.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Töltse le a függő SDK-t.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Töltse le a parancsfájlt a minta GitHub-projektből.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Hozzon létre egy Azure Automation Runbook modult a StorSimple 8000 sorozatú eszközkezeléshez. A Windows Powershell ablakába írja be a következő parancsokat:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Ellenőrizze, hogy létrejön-e `C:\scripts\StorSimpleSDKTools`egy automatizálási modul zip-fájlja a alkalmazásban.

    ![ellenőrzés-automatizálás-modul](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. A következő kimenet jelenik meg, amikor az automatizálási modul a Windows PowerShell en keresztül jön létre.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Automation-runbook importálása, közzététele és futtatása

1. Hozzon létre egy Azure Run As automation-fiókot az Azure Portalon. Ehhez nyissa meg **az Azure piacterét > mindent,** és keresse meg az **Automation**kifejezést. Válassza **az Automation-fiókok lehetőséget**.

    ![keresés-automatizálás](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Az **Automatizálási fiók hozzáadása** panelen:

   1. Adja meg az Automation-fiók **nevét.**
   2. Válassza ki a StorSimple Eszközkezelő szolgáltatáshoz kapcsolódó **előfizetést.**
   3. Hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévő erőforráscsoportból.
   4. Válasszon egy **helyet** (ha lehetséges, ugyanaz, mint ahol a szolgáltatás fut).
   5. Hagyja bejelölve az alapértelmezett **Futtatás létrehozása fiókként** beállítást.
   6. Szükség esetén ellenőrizze **a Pin to dashboard -t.** Kattintson **a Létrehozás gombra.**

       ![létrehozás-automatizálás-fiók](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Az automatizálási fiók sikeres létrehozása után értesítést kap. Az Automation-fiók létrehozásáról a Futtatás [másként fiók létrehozása](https://docs.microsoft.com/azure/automation/automation-create-runas-account)című területen talál további információt.

3. Annak érdekében, hogy a létrehozott automatizálási fiók hozzáférhessen a StorSimple Eszközkezelő szolgáltatáshoz, hozzá kell rendelnie a megfelelő engedélyeket az automatizálási fiókhoz. Nyissa meg a **Hozzáférés-vezérlést** a StorSimple Eszközkezelő szolgáltatásban. Kattintson **a + Add** and adja meg az Azure Automation-fiók nevét. **Mentse** a beállításokat.

    ![add-permissions-automation-fiók](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Az újonnan létrehozott fiókban nyissa meg **a Megosztott erőforrások > modulok at,** és kattintson a + Add module ( Modul **hozzáadása**) elemre.

5. A **Modul hozzáadása** panelen keresse meg a tömörített modul helyét, és válassza ki és nyissa meg a modult. Kattintson az **OK** gombra.

    ![kiegészítő modul](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Nyissa meg **a Folyamatautomatizálás > Runbookokat, és kattintson a + Runbook hozzáadása gombra**. A **Runbook hozzáadása** panelen kattintson **a Meglévő runbook importálása**elemre. Mutasson a **Runbook-fájl**Windows PowerShell-parancsfájljára. A runbook típusa automatikusan kiválasztásra kerül. Adja meg a runbook nevét és nem kötelező leírását. Kattintson **a Létrehozás gombra.**

    ![kiegészítő modul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. A runbook hozzáadódik a runbookok listájához. Jelölje ki és kattintson erre a runbookra.

    ![kattintás-új-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Szerkesztsd a runbookot, és kattintson a **Teszt ablaktábla**elemre. Adja meg a paramétereket, például a StorSimple Eszközkezelő szolgáltatás nevét, a StorSimple-eszköz nevét és az előfizetést. **Kezdd el** a tesztet. A jelentés akkor jön létre, amikor a futtatás befejeződött. További információért látogasson el [a runbook teszteléséhez.](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook)

    ![teszt-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Vizsgálja meg a runbook kimenetét a tesztablakban. Ha elégedett, zárja be az ablaktáblát. Kattintson **a Közzététel gombra,** és amikor megerősítést kér, erősítse meg és tegye közzé a runbookot.

    ![közzététel-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>További lépések

[A StorSimple Eszközkezelő szolgáltatás segítségével kezelheti a StorSimple-eszközt.](storsimple-8000-manager-service-administration.md)
