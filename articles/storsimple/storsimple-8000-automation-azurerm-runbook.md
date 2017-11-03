---
title: "Azure Automation-forgatókönyv segítségével kezelheti a StorSimple eszközökhöz |} Microsoft Docs"
description: "Azure Automation-forgatókönyv használata StorSimple feladatok automatizálásához"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: cfd0e4dbb6a4f24df5ba42cd45f9c16fbe5b493c
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Azure Automation-forgatókönyv használatával StorSimple eszközök kezelése

Ez a cikk ismerteti, hogyan Azure Automation-forgatókönyv használatával kezelheti a StorSimple 8000 series eszköz Azure-portálon. A minta-runbookhoz lépésről-lépésre Ez a forgatókönyv végrehajtásához a környezet konfigurálásának a lépéseit tartalmazza.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurálhatja, hozzáadása, és futtassa az Azure-forgatókönyvek

Ez a szakasz a Windows PowerShell-parancsfájl például telik a StorSimple, és a különböző importálnia kell a parancsfájl egy runbook közzététele és a runbook végrehajtása szükséges lépéseket részletezi.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

* a StorSimple 8000 series eszköz regisztrálva a StorSimple Device Manager szolgáltatáshoz tartozó aktív Azure-előfizetéssel.


* A számítógépen telepített Windows PowerShell 5.0 (vagy a Windows Server gazdagépet a StorSimple használata egy).


### <a name="create-automation-runbook-module-in-windows-powershell"></a>Automation-runbook-modul létrehozása a Windows PowerShellben

Az automation-modul a StorSimple 8000 series eszközkezeléshez létrehozásához hajtsa végre az alábbi lépéseket:

1. Indítsa el a Windows PowerShell. Hozzon létre egy új mappát, és módosítsa a könyvtárat az új mappába.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Töltse le a NuGet CLI](http://www.nuget.org/downloads) alapján az előző lépésben létrehozott mappába. Különböző verziói _nuget.exe_. Válassza ki a megfelelő az SDK verziót. Minden egyes letöltési hivatkozás mutat közvetlenül egy _.exe_ fájlt. Ügyeljen arra, hogy kattintson a jobb gombbal, és mentse a fájlt a számítógépre, hanem a böngésző történő futtatásával.

    Töltse le, és tárolja a parancsfájl ugyanabban a mappában, amelyet korábban hozott létre a következő parancsot is futtathatja.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. A függő SDK letöltése.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. A parancsprogram letölthető a GitHub mintaprojektet.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1

    ```
5. Hozzon létre egy Azure Automation-Runbook modul a StorSimple 8000 Series eszközök felügyeletére. A Windows Powershell-ablakot írja be a következő parancsokat:

    ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"
            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip

    ```

6. Győződjön meg arról, hogy egy automatizálási modul zip-fájl létrejön `C:\scripts\StorSimpleSDKTools`.

    ![Győződjön meg arról automation-modul](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. A következő kimeneti áll rendelkezésre, amikor az automation-modul a Windows PowerShell segítségével hoz létre. 

    ```
    -----------------------------------------
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\WINDOWS\system32> mkdir C:\scripts\StorSimpleSDKTools

        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools


    PS C:\WINDOWS\system32> cd c:\scripts\StorSimpleSDKTools
    PS C:\scripts\StorSimpleSDKTools> wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorS
    impleSDKTools\nuget.exe
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8
    000series

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.Active
    Directory -Version 2.28.3

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Aut
    hentication -Version 2.2.9-preview

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTool
    s
    Executing nuget actions took 717.48 ms
    PS C:\scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Mo
    nitor-Backups.ps1 -Out Monitor-Backups.ps1
    PS C:\scripts\StorSimpleSDKTools> # set path variables
    PS C:\scripts\StorSimpleSDKTools>             $downloadDir = "C:\scripts\StorSimpleSDKTools"
    PS C:\scripts\StorSimpleSDKTools>             $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.Sto
    rSimple8000Series"
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Se
    ries"
    PS C:\scripts\StorSimpleSDKTools>             mkdir "$moduleDir"

        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series

    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\
    lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Mic
    rosoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft
    .Rest.ClientRuntime*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $
    moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9
    -preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\l
    ib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #Don't change the name of the Archive
    PS C:\scripts\StorSimpleSDKTools>             compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Manag
    ement.StorSimple8000Series.zip
    PS C:\scripts\StorSimpleSDKTools>

    -------------------------------------------

    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importálja, közzétételét és Automation-forgatókönyv futtatása

1. Egy Azure-beli futtató automation-fiók létrehozása az Azure portálon. Ehhez nyissa meg a **Azure piactér > minden** majd keresse meg a **Automation**. Válassza ki **Automation-fiók**.

    ![keresési-automatizálás](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Az a **Automation-fiók hozzáadása** panel:

    1. Adja meg a **neve** az Automation-fiók.
    2. Válassza ki a **előfizetés** csatolva a StorSimple eszköz Manager szolgáltatást.
    3. Hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévő erőforráscsoportot a.
    4. Válassza ki a **hely** (ha lehetséges ugyanaz, mint amelyen a szolgáltatás fut).
    5. Hagyja meg az alapértelmezett **létrehozása Futtatás mint fiók** kiválasztott beállítás.
    5. Ellenőrizze, nem kötelezően **rögzítés az irányítópulton**. Kattintson a **Create** (Létrehozás) gombra.

        ![automation-fiók létrehozása](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

    Miután az automation-fiók sikeresen létrejött, értesítés jelenik meg. Automation-fiók létrehozásával kapcsolatos további információkért látogasson el [hozzon létre egy futtató fiókot](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Győződjön meg arról, hogy az automation-fiók létrehozva férhetnek hozzá a StorSimple Device Manager szolgáltatáshoz, az automation-fiók megfelelő engedélyeket kell. Ugrás a **hozzáférés-vezérlés** a StorSimple Device Manager szolgáltatásban. Kattintson a **+ Hozzáadás** , és adja meg az Azure Automation-fiók nevét. **Mentés** a beállításokat.

    ![engedélyek-automation-fiók hozzáadása](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Az újonnan létrehozott fiókjához, keresse meg **megosztott erőforrások > modulok** kattintson **+ Hozzáadás modul**.

5. Az a **Hozzáadás modul** panelen keresse meg a helyet, a tömörített modul, és válassza ki, majd nyissa meg a modul. Kattintson az **OK** gombra.

    ![modul hozzáadása](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Ugrás a **folyamat > Runbookok, kattintson a + hozzáadása egy runbook**. Az a **vegye fel a runbook** panelen kattintson a **meglévő forgatókönyv importálása**. A Windows PowerShell-parancsfájlt a mutasson a **Runbook fájl**. A runbook típusa automatikusan ki van jelölve. Adjon meg egy nevet és egy leírást a runbook. Kattintson a **Create** (Létrehozás) gombra.

    ![modul hozzáadása](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. A runbook kerül forgatókönyvek listája. Jelölje ki, majd kattintson a runbookot.

    ![Kattintson új runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. A runbook szerkesztése, és kattintson a **teszt ablaktábla**. Adja meg a paraméterek, például nevét a StorSimple eszköz kezelő szolgáltatás, a StorSimple eszköz és az előfizetés nevét. **Start** a vizsgálatot. A jelentés jön létre, amikor a Futtatás befejeződött. További információkért látogasson el [egy runbook tesztelése](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![a runbook tesztelése](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Vizsgálja meg a ablaktáblán a runbook kimenete. Ha mindent megfelelőnek talált, zárja be a panelen. Kattintson a **közzététel** és amikor felszólítja a megerősítésre, erősítse meg, közzé a runbookot.

    ![runbook közzététele](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)


## <a name="next-steps"></a>Következő lépések

[Kezelheti a StorSimple eszköz StorSimple az Eszközkezelő szolgáltatás](storsimple-8000-manager-service-administration.md).