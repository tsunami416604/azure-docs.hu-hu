---
title: Eszközkezelés a StorSimple az Azure Automation-Runbook használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Automation-Runbook StorSimple feladatok automatizálásához
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 30d70bb7e1f868060e3b287a0cdfb117c585b9ba
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793509"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Eszközkezelés a StorSimple az Azure Automation-runbookok használatával

Ez a cikk bemutatja, hogyan Azure Automation-runbookok segítségével felügyelhető a StorSimple 8000 sorozatú eszköz az Azure Portalon. Minta runbook végigvezeti a lépéseken, ez a forgatókönyv végrehajtásához a környezet konfigurálása tartalmazza.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurálása, hozzáadása és Azure-forgatókönyv futtatása

Ez a szakasz a storsimple-höz készült Windows PowerShell-parancsfájl például veszi, és részletesen ismerteti a különféle lépéseit importálja a parancsfájl egy runbook közzététele és a runbook végrehajtása szükséges.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

* a StorSimple 8000 sorozatú eszköz regisztrálva a StorSimple-Eszközkezelő szolgáltatással társított aktív Azure-előfizetéssel.

* A számítógépen telepített Windows PowerShell 5.0 (vagy a Windows Server a storsimple-höz készült üzemeltetésére, ha egy).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Hozzon létre automation-runbook-modulhoz a Windows PowerShellben

Automation-modul, a StorSimple 8000 sorozat eszközfelügyeleti létrehozásához hajtsa végre az alábbi lépéseket:

1. Launch-Windows PowerShell. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappába.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Töltse le a NuGet CLI](https://www.nuget.org/downloads) az az előző lépésben létrehozott mappa alatt. Különböző verziói _nuget.exe_. Válassza ki a megfelelő az SDK-verziót. Minden egyes letöltési hivatkozást közvetlenül a mutat egy _.exe_ fájlt. Ügyeljen arra, hogy kattintson a jobb gombbal, és mentse a fájlt a számítógépre, hanem a böngészőben történő futtatásával.

    Töltse le és tárolja a parancsfájl ugyanabban a mappában, amelyet korábban hozott létre a következő parancsot is futtathatja.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Töltse le a függő SDK-t.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Töltse le a parancsfájlt a minta GitHub-projekt.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Hozzon létre egy Azure Automation-Runbook modul a StorSimple 8000 sorozat felügyelethez. A Windows Powershell-ablakot írja be a következő parancsot:

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

6. Győződjön meg arról, hogy egy automatizálási a modul zip-fájl jön létre a `C:\scripts\StorSimpleSDKTools`.

    ![verify-automation-module](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. A következő kimenet jelenik meg az automation-modul a Windows Powershellen keresztül létrehozásakor.

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

### <a name="import-publish-and-run-automation-runbook"></a>Importálja, közzétételét és Automation-runbook futtatása

1. Azure-beli futtató automation-fiók létrehozása az Azure Portalon. Ehhez nyissa meg **az Azure marketplace > minden** és keressen **Automation**. Válassza ki **Automation-fiókok**.

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Az a **Automation-fiók hozzáadása** panelen:

   1. Adja meg a **neve** az Automation-fiók.
   2. Válassza ki a **előfizetés** a StorSimple-Eszközkezelő szolgáltatáshoz csatolt.
   3. Hozzon létre egy új erőforráscsoportot, vagy jelöljön ki egy meglévő erőforráscsoportot.
   4. Válassza ki a **helye** (amennyiben lehetséges ugyanaz, mint ahol a szolgáltatás fut).
   5. Hagyja meg az alapértelmezett **futtató fiók létrehozása** lehetőség van kijelölve.
   6. Szükség esetén ellenőrizze **rögzítés az irányítópulton**. Kattintson a **Create** (Létrehozás) gombra.

       ![create-automation-account](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Miután az automation-fiók sikeresen létrejött, értesítést kap. Az Automation-fiók létrehozásával kapcsolatos további információkért lépjen [hozzon létre egy futtató fiókot](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Győződjön meg arról, hogy a létrehozott automation-fiókkal hozzáférhet-e a StorSimple-Eszközkezelő szolgáltatás, az automation-fiók megfelelő engedélyeket kell. Lépjen a **hozzáférés-vezérlés** a StorSimple-Eszközkezelő szolgáltatásban. Kattintson a **+ Hozzáadás** , és adja meg az Azure Automation-fiók nevét. **Mentés** a beállításokat.

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Az újonnan létrehozott fiók, lépjen a **megosztott erőforrások > modulok** kattintson **+ Hozzáadás modul**.

5. Az a **Hozzáadás modul** panelen keresse meg a helyet, a tömörített modul, és válassza ki, majd nyissa meg a modult. Kattintson az **OK** gombra.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Lépjen a **Folyamatautomatizálás > forgatókönyveket, és kattintson a + forgatókönyv hozzáadása**. Az a **adja hozzá a runbook** panelen kattintson a **meglévő runbook importálása**. A Windows PowerShell-parancsfájl tárolásához a mutasson a **forgatókönyvfájl**. A runbook-típus automatikusan ki van jelölve. Adjon meg egy nevet és egy leírást a runbook. Kattintson a **Create** (Létrehozás) gombra.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. A runbook hozzáadódik a runbookok listája. Válassza ki, majd kattintson a runbook.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Szerkessze a runbookot, és kattintson a **teszt panel**. Adja meg a paraméterek, például a nevét, a StorSimple-Eszközkezelő szolgáltatás, a StorSimple eszköz és az előfizetés nevét. **Indítsa el** a vizsgálatot. A jelentés jön létre, ha a Futtatás befejeződött. További információért ugorjon [egy runbook tesztelése](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Vizsgálja meg a runbook a teszt panel kimenete. Ha elégedett az eredménnyel, zárja be a panelt. Kattintson a **közzététel** és amikor a rendszer megerősítést kér, erősítse meg, és tegye közzé a forgatókönyvet.

    ![publish-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>További lépések

[Használja a StorSimple-Eszközkezelő szolgáltatás kezelése a StorSimple-eszköz](storsimple-8000-manager-service-administration.md).
