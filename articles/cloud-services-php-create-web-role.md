---
title: "Php-hez tartozó Azure webes és feldolgozói szerepkörök létrehozása |} Microsoft Docs"
description: "Egy útmutató, a PHP webes és feldolgozói szerepkörök létrehozása az Azure-felhőszolgáltatás, és a PHP futtatókörnyezetben konfigurálása."
services: 
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 214fdcfe20f3fa4ebcbe41308404f8b7e7d15310
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-php-web-and-worker-roles"></a>A PHP webes és feldolgozói szerepkörök létrehozása
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan PHP webes vagy feldolgozói szerepkörök létrehozása a Windows fejlesztői környezetben, a "beépített" verziók lehetőségek közül választhat a PHP verzióját, a PHP-konfigurációt, bővítmények engedélyezése, és végül telepítse az Azure. Emellett bemutatja, hogyan lehet egy webes vagy feldolgozói szerepkör egy Ön által biztosított PHP futtatókörnyezetben (az egyéni konfiguráció és bővítmények) használatára konfigurálja.

## <a name="what-are-php-web-and-worker-roles"></a>Mik azok a PHP webes és feldolgozói szerepkörök?
Azure biztosít három számítási modellt futó alkalmazások: Azure App Service, Azure virtuális gépek és Azure Cloud Services. Mindhárom modell támogatja a PHP. Felhőszolgáltatás, amely magában foglalja a webes és feldolgozói szerepköröket, itt *platformszolgáltatást (PaaS)*. A felhőszolgáltatásban webes szerepkörrel rendelkező előtér-alkalmazások üzemeltetését egy dedikált Internet Information Services (IIS) webkiszolgálót biztosít a. A feldolgozói szerepkör aszinkron, hosszan futó vagy folyamatos feladatokat futtat függetlenül a felhasználói interakcióktól vagy bemenettől futtathatja.

Ezekről a beállításokról további információkért lásd: [Azure által biztosított lehetőségek futtató számítási](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>A PHP-hoz készült Azure SDK letöltése
A [Azure SDK for PHP] több összetevőből áll. Ez a cikk kettő fogja használni: Azure PowerShell és az Azure emulátorok. A két összetevő keresztül a Microsoft Webplatform-telepítővel telepíthető. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

## <a name="create-a-cloud-services-project"></a>Egy Felhőszolgáltatás-projekt létrehozása
Az első lépés a PHP webes vagy feldolgozói szerepkör létrehozása az Azure Service-projekt létrehozása. az Azure szolgáltatás projekt tárolójaként logikai webes és feldolgozói szerepkörök, és a projekt tartalmaz [szolgáltatás definíciós (.csdef)] és [szolgáltatás konfigurációs (.cscfg)] fájlokat.

Hozzon létre egy új Azure szolgáltatás-projektet, Azure PowerShell futtatása rendszergazdaként, és hajtsa végre a következő parancsot:

    PS C:\>New-AzureServiceProject myProject

Ez a parancs létrehoz egy új könyvtárat (`myProject`), amelyhez webes és feldolgozói szerepkörök adhat hozzá.

## <a name="add-php-web-or-worker-roles"></a>A PHP webes vagy feldolgozói szerepkörök hozzáadása
A PHP webes szerepkör hozzáadása a projekthez, futtassa a következő parancsot a projekt gyökérkönyvtárában:

    PS C:\myProject> Add-AzurePHPWebRole roleName

A feldolgozói szerepkör esetében az alábbi parancsot használja:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> A `roleName` paraméter nem kötelező. Ha ki van hagyva, a szerepkör neve automatikusan generált. Az első létrehozott webes szerepkör lesz `WebRole1`, a második lesz `WebRole2`, és így tovább. A létrehozott első feldolgozói szerepkör lesz `WorkerRole1`, a második lesz `WorkerRole2`, és így tovább.
>
>

## <a name="specify-the-built-in-php-version"></a>Adja meg a beépített PHP verzióját
A PHP webes vagy feldolgozói szerepkör projekt hozzáadásakor a projekt konfigurációs fájlok módosítják, hogy a PHP esetén telepítve lesz az alkalmazás összes webes vagy feldolgozói példányt telepítették. A verzió a PHP alapértelmezés szerint telepítendő megtekintéséhez futtassa a következő parancsot:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

A fenti parancs kimenetét fogja hasonlítania alább láthatók. Ebben a példában a `IsDefault` jelző értéke `true` php 5.3.17, jelezve, hogy az alapértelmezett a PHP-verzió telepítve lesz.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

A PHP verzióinak felsorolt állíthat be a PHP futásidejű verzióját. Ahhoz például, hogy állítsa be a PHP verzióját (nevű szerepkör `roleName`) 5.4.0, használja a következő parancsot:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> A rendelkezésre álló PHP-verziók a jövőben módosíthatja.
>
>

## <a name="customize-the-built-in-php-runtime"></a>A beépített PHP futtatókörnyezetben testreszabása
A PHP futtatókörnyezetben, ha követi a fenti lépéseket, beleértve a módosítása telepített konfigurációjának teljes hozzáférésük van `php.ini` beállítások és bővítmények engedélyezésével.

A beépített PHP futtatókörnyezetben testreszabásához kövesse az alábbi lépéseket:

1. Adja hozzá egy új mappát, `php`, hogy a `bin` mappában található a webes szerepkör. A feldolgozói szerepkör esetében adja hozzá a szerepkört gyökérkönyvtár.
2. Az a `php` mappa, hozzon létre egy másik nevű `ext`. Helyezzünk `.dll` szerepkörbővítmény-fájlok (pl. `php_mongo.dll`), amely engedélyezi a mappában.
3. Adja hozzá a `php.ini` fájlt a `php` mappát. Egyéni kiterjesztések engedélyezése, és állítsa be a PHP-irányelvek a fájlban található. Például, ha szeretné kapcsolni `display_errors` a, és engedélyezze a `php_mongo.dll` kiterjesztése, a tartalmát a `php.ini` fájl a következőképpen nézne ki:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> A beállításait, ne explicit módon beállítása a `php.ini` fájlt a rendszer automatikusan megadja az alapértelmezett értékre állítható be. Azonban vegye figyelembe, hogy adhat hozzá egy teljes `php.ini` fájlt.
>
>

## <a name="use-your-own-php-runtime"></a>A saját PHP futtatókörnyezetben használja
Válassza ki a beépített PHP futtatókörnyezetben, majd konfigurálja úgy a fent említett helyett bizonyos esetekben érdemes lehet adja meg a saját PHP futtatókörnyezetben. Például a fejlesztői környezetben használt webes vagy feldolgozói szerepkörnek ugyanaz a PHP futtatókörnyezetben is használhatja. Így könnyebben győződjön meg arról, hogy az alkalmazás nem módosítja az éles környezetben viselkedését.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>A saját PHP futtatókörnyezetben használja a webes szerepkör konfigurálása
A webes szerepkör egy Ön által biztosított PHP futtatókörnyezetben használandó konfigurálásához kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure szolgáltatás-projektet, és adja hozzá a PHP webes szerepkör ebben a témakörben korábban ismertetett módon.
2. Hozzon létre egy `php` mappájában a `bin` mappa, amely a webes szerepkör gyökérkönyvtárában, és hozzáadhatja a PHP futtatókörnyezetben (összes bináris fájljait, konfigurációs fájlokat, almappák stb.) a `php` mappát.
3. (VÁLASZTHATÓ) Ha a PHP futtatókörnyezetben használja a [Microsoft SQL Server PHP Drivers][sqlsrv drivers], szüksége lesz a webes szerepkör telepítéséhez konfigurálása [SQL Server Native Client 2012] [ sql native client] Ha ki van építve. Ehhez adja hozzá a [sqlncli.msi x64 installer] számára a `bin` a webes szerepkör gyökérkönyvtár mappájában. A következő lépésben ismertetett indítási parancsfájl csendes fog futtassa a telepítőt, amikor a szerepkör lett kiépítve. Ha a PHP futtatókörnyezetben nem használja a Microsoft Drivers php-hez tartozó SQL Server, a parancsfájl a következő lépésben látható eltávolíthat a következő sort:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Konfigurálja az indítási tevékenységhez meghatározása [Internet Information Services (IIS)] [ iis.net] a PHP futtatókörnyezetben használatára a tanúsítványigénylések `.php` lapokat. Ehhez nyissa meg a `setup_web.cmd` fájlt (a a `bin` a webes szerepkör gyökérkönyvtár fájl) egy szövegszerkesztőben, és cserélje ki annak tartalmát a következő parancsfájlt:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Adja hozzá az alkalmazásfájlokat a webes szerepkör gyökérkönyvtárba. Ez lesz a webkiszolgálón gyökérkönyvtár.
6. Az alkalmazás közzétételére, lásd: a [az alkalmazás közzétételére](#publish-your-application) az alábbi szakasz.

> [!NOTE]
> A `download.ps1` parancsfájl (a a `bin` mappában található a webes szerepkör gyökérkönyvtár) a saját PHP futtatókörnyezetben használja a fenti lépések végrehajtását követően törölhető.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>A saját PHP futtatókörnyezetben használandó feldolgozói szerepkörök konfigurálása
A feldolgozói szerepkör egy Ön által biztosított PHP futtatókörnyezetben használandó konfigurálásához kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure szolgáltatás-projektet, és adja hozzá a PHP-feldolgozói szerepkör ebben a témakörben korábban ismertetett módon.
2. Hozzon létre egy `php` mappa a feldolgozói szerepkör gyökérkönyvtárában, és adja hozzá a PHP futtatókörnyezetben (összes bináris fájljait, konfigurációs fájlokat, almappák stb.) a `php` mappát.
3. (VÁLASZTHATÓ) Ha a PHP futtatókörnyezetben használja [Microsoft SQL Server PHP Drivers][sqlsrv drivers], a feldolgozói szerepkör telepítéséhez konfigurálni kell [SQL Server Native Client 2012] [ sql native client] Ha ki van építve. Ehhez adja hozzá a [sqlncli.msi x64 installer] a feldolgozói szerepkör gyökérkönyvtárba. A következő lépésben ismertetett indítási parancsfájl csendes fog futtassa a telepítőt, amikor a szerepkör lett kiépítve. Ha a PHP futtatókörnyezetben nem használja a Microsoft Drivers php-hez tartozó SQL Server, a parancsfájl a következő lépésben látható eltávolíthat a következő sort:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Adja meg, amely indítási feladat a `php.exe` végrehajtható, a szerepkör ki van építve a feldolgozói szerepkör PATH környezeti változóhoz. Ehhez nyissa meg a `setup_worker.cmd` fájlt (a feldolgozói szerepkör gyökérkönyvtár) egy szövegszerkesztőben, és cserélje ki annak tartalmát a következő parancsfájlt:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Adja hozzá az alkalmazásfájlokat a feldolgozói szerepkör gyökérkönyvtárba.
6. Az alkalmazás közzétételére, lásd: a [az alkalmazás közzétételére](#publish-your-application) az alábbi szakasz.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Futtassa az alkalmazást a számítási és tárolási emulátorok
Az Azure emulátorok adjon meg egy helyi környezet, amelyben tesztelheti az Azure-alkalmazásában a felhőbe való telepítése előtt. Néhány az emulátorok és az Azure környezetbe közötti különbségek vannak. Ez jobb megértése, lásd: [fejlesztéshez és teszteléshez használja az Azure storage emulator](storage/common/storage-use-emulator.md).

Vegye figyelembe, hogy a PHP telepítése helyi a compute emulator használatával kell rendelkeznie. A compute emulator a helyi PHP-telepítés használatával futtassa az alkalmazást.

A projekt futtatásához az emulátorok, a projekt gyökérkönyvtárából végre az alábbi parancsot:

    PS C:\MyProject> Start-AzureEmulator

Ez hasonló kimenetet fog látni:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Láthatja, hogy az alkalmazás futtatása az emulátorban nyisson meg egy webböngészőben, és a helyi cím, a kimenetben megjelenő tallózással (`http://127.0.0.1:81` a fenti példa kimenetben).

Az emulátorok leállításához hajtsa végre a parancsot:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Az alkalmazás közzététele
Az alkalmazás közzétételére, előbb importálnia kell a közzétételi beállítások használatával a [Import-AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) parancsmag. Az alkalmazás használatával közzétehet, majd a [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) parancsmag. További információ a bejelentkezés: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Következő lépések
További információkért lásd: a [PHP fejlesztői központ](/develop/php/).

[Azure SDK for PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[szolgáltatás definíciós (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[szolgáltatás konfigurációs (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 installer]: http://go.microsoft.com/fwlink/?LinkID=239648
