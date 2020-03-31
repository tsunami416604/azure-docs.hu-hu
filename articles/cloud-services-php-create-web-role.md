---
title: Azure webes és feldolgozói szerepkörök létrehozása PHP-hez
description: Útmutató a PHP webes és feldolgozói szerepkörök létrehozásához egy Azure felhőszolgáltatásban, valamint a PHP-futásidő konfigurálása.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297254"
---
# <a name="create-php-web-and-worker-roles"></a>Webes és feldolgozói PHP-szerepkörök létrehozása

## <a name="overview"></a>Áttekintés

Ez az útmutató bemutatja, hogyan hozhat létre PHP webes vagy feldolgozói szerepköröket egy Windows fejlesztői környezetben, hogyan választhatja ki a PHP egy adott verzióját a rendelkezésre álló "beépített" verziókból, módosíthatja a PHP konfigurációját, engedélyezheti a bővítményeket, és végül telepíteni az Azure-ba. Azt is ismerteti, hogyan konfigurálhat egy webes vagy feldolgozói szerepkört az Ön által megadott PHP-futásidejű (egyéni konfigurációval és bővítményekkel) használatára.

Az Azure három számítási modellt kínál a futó alkalmazásokhoz: az Azure App Service, az Azure virtual machines és az Azure Cloud Services. Mindhárom modell támogatja a PHP-t. A felhőszolgáltatások, amelyek webes és feldolgozói szerepköröket tartalmaznak, platformot biztosít *szolgáltatásként (PaaS).* A felhőalapú szolgáltatáson belül a webes szerepkör dedikált Internet Information Services (IIS) webkiszolgálót biztosít az előtér-webalkalmazások üzemeltetéséhez. A feldolgozói szerepkör aszinkron, hosszú ideig futó vagy örökös feladatokat futtathat a felhasználói beavatkozástól vagy a beviteltől függetlenül.

Ezekről a lehetőségekről az [Azure által biztosított Számítási üzemeltetési beállítások](cloud-services/cloud-services-choose-me.md)című témakörben talál további információt.

## <a name="download-the-azure-sdk-for-php"></a>Töltse le az Azure SDK PHP-hez

Az [Azure SDK PHP-hez](https://github.com/Azure/azure-sdk-for-php) több összetevőből áll. Ez a cikk két közülük: az Azure PowerShell és az Azure-emulátorok. Ez a két összetevő a Microsoft Web Platform Installer segítségével telepíthető. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

## <a name="create-a-cloud-services-project"></a>Felhőszolgáltatási projekt létrehozása

A PHP webes vagy feldolgozói szerepkör létrehozásának első lépése egy Azure Service-projekt létrehozása. egy Azure Service-projekt a webes és feldolgozói szerepkörök logikai tárolójaként szolgál, és tartalmazza a projekt [szolgáltatásdefiníciós (.csdef)] és [szolgáltatáskonfigurációs (.cscfg)] fájljait.

Új Azure Service-projekt létrehozásához futtassa az Azure PowerShellt rendszergazdaként, és hajtsa végre a következő parancsot:

    PS C:\>New-AzureServiceProject myProject

Ez a parancs létrehoz`myProject`egy új könyvtárat ( ), amelyhez webes és feldolgozói szerepköröket adhat hozzá.

## <a name="add-php-web-or-worker-roles"></a>PHP webes vagy feldolgozói szerepkörök hozzáadása

Ha PHP webes szerepkört szeretne hozzáadni egy projekthez, futtassa a következő parancsot a projekt gyökérkönyvtárából:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Feldolgozói szerepkör esetén használja ezt a parancsot:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> A `roleName` paraméter megadása nem kötelező. Ha nincs megadva, a szerepkör neve automatikusan létrejön. Az első létrehozott webes `WebRole1`szerepkör lesz `WebRole2`, a második lesz , és így tovább. Az első létrehozott feldolgozói szerepkör `WorkerRole1`a `WorkerRole2`, a második lesz, és így tovább.
>
>

## <a name="use-your-own-php-runtime"></a>Használja a saját PHP runtime

Bizonyos esetekben ahelyett, hogy egy beépített PHP futásidőt választana és a fent leírt módon konfigurálna, érdemes lehet saját PHP futásidőt biztosítani. Használhatja például ugyanazt a PHP-futásidőt egy webes vagy feldolgozói szerepkörben, amelyet a fejlesztői környezetben használ. Ez megkönnyíti annak biztosítását, hogy az alkalmazás nem módosítja a viselkedést az éles környezetben.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Webes szerepkör konfigurálása saját PHP-futásidejű használatára

Ha egy webes szerepkört a megadott PHP-futásidejű használatra szeretne beállítani, kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure Service-projektet, és adjon hozzá egy PHP webes szerepkört a témakörben korábban leírtak szerint.
2. Hozzon `php` létre `bin` egy mappát a webes szerepkör gyökérkönyvtárában lévő mappában, majd adja hozzá a PHP futásidejét (az `php` összes bináris fájlt, konfigurációs fájlt, almappát stb.) a mappához.
3. (NEM KÖTELEZŐ) Ha a PHP futásideje a [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers]alkalmazást használja, konfigurálnia kell a webes szerepkört az SQL Server Native Client [2012][sql native client] telepítéséhez, amikor az ki van építve. Ehhez adja hozzá az [sqlncli.msi x64 telepítőt] a `bin` webes szerepkör gyökérkönyvtárának mappájához. A következő lépésben leírt indítási parancsfájl a szerepkör kiépítésekor csendben futtatja a telepítőt. Ha a PHP-futásidő nem használja a Microsoft Drivers for PHP for SQL Server alkalmazást, a következő lépésben látható parancsfájlból eltávolíthatja a következő sort:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiáljon egy indítási feladatot, amely úgy konfigurálja az Internet Information `.php` Services [(IIS) szolgáltatást,][iis.net] hogy a PHP futásidejű használatával kezelje a lapokra vonatkozó kérelmeket. Ehhez nyissa meg `setup_web.cmd` a fájlt `bin` (a webes szerepkör gyökérkönyvtárának fájljában) egy szövegszerkesztőben, és cserélje le annak tartalmát a következő parancsfájlra:

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
5. Adja hozzá az alkalmazásfájlokat a webes szerepkör gyökérkönyvtárához. Ez lesz a webkiszolgáló gyökérkönyvtára.
6. Tegye közzé az alkalmazást az alábbi [Alkalmazás közzététele](#publish-your-application) szakaszban leírtak szerint.

> [!NOTE]
> A `download.ps1` parancsfájl (a `bin` webes szerepkör gyökérkönyvtárának mappájában) törölhető, miután követte a fent leírt lépéseket a saját PHP futásidejének használatával kapcsolatban.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Feldolgozói szerepkör konfigurálása saját PHP-futásidejű használatára

Ha egy feldolgozói szerepkört a megadott PHP-futásidő használatára szeretne beállítani, kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure Service-projektet, és adjon hozzá egy PHP feldolgozói szerepkört a témakörben korábban leírtak szerint.
2. Hozzon `php` létre egy mappát a feldolgozói szerepkör gyökérkönyvtárában, majd adja hozzá a PHP futásidejét (az összes bináris fájlt, konfigurációs fájlt, almappát stb.) a `php` mappához.
3. (NEM KÖTELEZŐ) Ha a PHP futásideje [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers]alkalmazást használ, konfigurálnia kell a feldolgozói szerepkört az SQL Server Native Client [2012][sql native client] telepítéséhez, amikor az ki van építve. Ehhez adja hozzá az [sqlncli.msi x64 telepítőt] a feldolgozói szerepkör gyökérkönyvtárához. A következő lépésben leírt indítási parancsfájl a szerepkör kiépítésekor csendben futtatja a telepítőt. Ha a PHP-futásidő nem használja a Microsoft Drivers for PHP for SQL Server alkalmazást, a következő lépésben látható parancsfájlból eltávolíthatja a következő sort:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiáljon egy indítási `php.exe` feladatot, amely a szerepkör kiépítésekor hozzáadja a végrehajtható fájlt a feldolgozói szerepkör PATH környezeti változójához. Ehhez nyissa meg `setup_worker.cmd` a fájlt (a feldolgozói szerepkör gyökérkönyvtárában) egy szövegszerkesztőben, és cserélje le annak tartalmát a következő parancsfájlra:

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
5. Adja hozzá az alkalmazásfájlokat a feldolgozói szerepkör gyökérkönyvtárához.
6. Tegye közzé az alkalmazást az alábbi [Alkalmazás közzététele](#publish-your-application) szakaszban leírtak szerint.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Futtassa az alkalmazást a számítási és tárolási emulátorokban

Az Azure-emulátorok egy helyi környezetben, amelyben tesztelheti az Azure-alkalmazást, mielőtt üzembe helyezi a felhőben. Az emulátorok és az Azure-környezet között vannak különbségek. Ennek jobb megértéséhez olvassa [el az Azure storage-emulátor használata fejlesztési és tesztelési célokra című témakört.](storage/common/storage-use-emulator.md)

Ne feledje, hogy a számítási emulátor használatához a PHP-t helyileg kell telepítenie. A számítási emulátor a helyi PHP-telepítést fogja használni az alkalmazás futtatásához.

Ha a projektet az emulátorokban szeretné futtatni, hajtsa végre a következő parancsot a projekt gyökérkönyvtárából:

    PS C:\MyProject> Start-AzureEmulator

A kimenet ehhez hasonló lesz:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Az alkalmazás az emulátorban futó, webböngésző megnyitásával és a kimenetben látható`http://127.0.0.1:81` helyi címre való böngészéssel (a fenti példakimenetben) látható.

Az emulátorok leállításához hajtsa végre a következő parancsot:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Az alkalmazás közzététele

Az alkalmazás közzétételéhez először importálnia kell a közzétételi beállításokat az [Import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) parancsmag használatával. Ezután közzéteheti az alkalmazást a [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) parancsmag használatával. A bejelentkezésről az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

További információt a [PHP Fejlesztői központban talál.](https://azure.microsoft.com/develop/php/)

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[szolgáltatásdefiníció (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[szolgáltatás konfigurációja (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 telepítő]: https://go.microsoft.com/fwlink/?LinkID=239648
