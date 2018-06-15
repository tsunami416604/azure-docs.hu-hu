---
title: Windows PowerShell-parancsfájlok használatával történő közzétételéhez fejlesztési és tesztkörnyezetek |} Microsoft Docs
description: Útmutató a Windows PowerShell-parancsfájlokat a Visual Studio használatával közzétegyék a fejlesztési és tesztelési környezetben.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 866575a483e705e1c972a0b56d98f26e9cf0c631
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798121"
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Windows PowerShell-parancsprogramok használata a fejlesztési és tesztelési környezetben való közzétételhez

A Visual Studio egy webalkalmazás létrehozásakor egy Windows PowerShell-parancsfájl, amely később használhatja egy webalkalmazást az Azure App Service vagy egy virtuális gépet, a webhely, az Azure-bA a közzétételi automatizálásához hozhat létre. Szerkesztheti és kiterjesztése a Windows PowerShell-parancsfájlt a Visual Studio-szerkesztőben saját követelményeinek megfelelően, vagy a parancsfájl integrálható a meglévő build, a vizsgálati és a parancsfájlok közzététele.

Az ezekhez a parancsfájlokhoz, megadhat testreszabott verziók (más néven a fejlesztési és tesztelési környezetben) a webhely ideiglenes használatra. Beállíthat például egy adott verzióját a webhely egy Azure virtuális géphez, vagy az átmeneti helyet egy tesztcsomag futtatása, programhiba Reprodukálja, teszteléséhez hibajavítás, próbaverzió változtatást, vagy egy bemutató vagy bemutató egyéni környezet beállítása egy webhelyen. Egy parancsfájl, amely közzéteszi a projekt létrehozása után hozza létre újra az azonos környezetben igény szerint a parancsfájl futtatásával, vagy futtassa a parancsfájlt a webes alkalmazás teszteléséhez egyéni környezet létrehozása a saját build a.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure SDK 2.3-as vagy újabb. Lásd: [Visual Studio letöltések](http://go.microsoft.com/fwlink/?LinkID=624384). (Az Azure SDK webes projektek parancsfájlok létrehozására nincs szükség. Ez a funkció a webes projektek, a felhőalapú szolgáltatások nem webes szerepkörök.)
* Az Azure PowerShell 0.7.4 vagy újabb. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* [A Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) vagy újabb.

## <a name="additional-tools"></a>További eszközök

További eszközök és erőforrások használatához a PowerShell használatával a Visual Studio Azure fejlesztési érhetők el. Lásd: [PowerShell Tools for Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>A közzététel parancsfájlok létrehozása

Létrehozhat egy virtuális gép, amelyen a webhely, amikor követve hozzon létre egy új projektet a közzététel parancsfájlok [ezeket az utasításokat](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Emellett [készítése az Azure App Service web Apps parancsfájlok közzététele](app-service/app-service-web-get-started-dotnet.md).

## <a name="scripts-that-visual-studio-generates"></a>Olyan parancsfájlok, amelyek a Visual Studio hoz létre

A Visual Studio megoldás szintű mappa állít elő **PublishScripts** két Windows PowerShell-fájlok, a virtuális gép vagy a webhely és a modult tartalmaz a parancsfájlokban használható funkciók közzététel parancsfájlt tartalmazó. A Visual Studio is létrehoz egy fájlt, amely meghatározza a projektet telepít részletes adatait a JSON formátumban.

### <a name="windows-powershell-publish-script"></a>A Windows PowerShell parancsfájl közzététele

A közzététel tartalmaz az adott közzététel webhelyére vagy a virtuális gép telepítésének lépései. A Visual Studio biztosít a Windows PowerShell fejlesztési színátmenetekhez szintaxist. Súgó a funkciók érhető el, és szabadon módosíthatja a funkciók a parancsfájl változó igényeinek.

### <a name="windows-powershell-module"></a>A Windows PowerShell-modul

A Windows PowerShell-modult, amely a Visual Studio hoz létre olyan függvényeket tartalmaz, a közzététel parancsfájl használja. Ezek az Azure PowerShell-funkciók nem célja, hogy módosítható. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

### <a name="json-configuration-file"></a>JSON-konfigurációs fájlt
A JSON-fájl jön létre a **konfigurációk** mappa, és pontosan mely üzembe helyezendő erőforrásokat az Azure-bA megadó konfigurációs adatokat tartalmaz. A fájl, amely a Visual Studio létrehozza a neve nem projekt-neve-WAWS-dev.json Ha létrehozott egy webhely vagy a név-VM-dev.json projekt Ha létrehozott egy virtuális gépet. Íme egy példa egy JSON-konfigurációs fájlt, amely jön létre, amikor egy webhely létrehozása. Az értékek többsége magától értetődő. A webhely neve Azure, hozza létre, akkor előfordulhat, hogy a projekt neve nem egyezik.

```json
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [{
            "connectionStringName": "DefaultConnection",
            "databaseName": "WebApplication26632_db",
            "serverName": "YourDatabaseServerName",
            "user": "sqluser2",
            "password": "",
            "edition": "",
            "size": "",
            "collation": "",
            "location": "West US"
        }]
    }
}
```

A virtuális gép létrehozásakor a JSON-konfigurációs fájl hasonlít-e a következő. Egy felhőszolgáltatás egy tárolót a virtuális gép jön létre. A virtuális gép tartalmazza a HTTP és HTTPS webes eléréshez szokásos végpontok, valamint a végpontok a Web Deploy, amely lehetővé teszi, hogy a helyi számítógépen, a távoli asztal és a Windows PowerShell a webhelyen közzétenni.

```json
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [{
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [{
            "connectionStringName": "",
            "databaseName": "",
            "serverName": "",
            "user": "",
            "password": ""
        }],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Szerkesztheti a JSON-konfiguráció módosítása, mi történik a publish-parancsfájlok futtatásakor. A `cloudService` és `virtualMachine` szakasz használata kötelező, de törölheti a `databases` szakaszban, ha nincs szüksége. Az alapértelmezett konfigurációs fájl, amely a Visual Studio létrehozza az üres tulajdonságot is kötelező megadni. az alapértelmezett konfigurációs fájl értéket tartalmazó tulajdonságokat szükség.

Ha olyan webhelyet, ahol több telepítési környezetekben (más néven üzembe helyezési ponti) helyett egy egyetlen munkakörnyezeti helyet az Azure-ban, a tárhely neve, a webhely nevében is felvehet a JSON-konfigurációs fájlt. Például, ha rendelkezik nevű webhellyel **saját webhely** és tárhely, nevű **tesztelése** , majd az URI `mysite-test.cloudapp.net`, de a megfelelő nevet a konfigurációs fájlban mysite(test). Csak ez, ha a webhely és műveletek üzembe helyezési ponti már szerepel az előfizetéshez. Ha azok még nem léteznek, a webhely létrehozása a parancsfájl futtatásával a tárolóhely megadása nélkül, majd hozza létre a tárolóhely a [Azure-portálon](https://portal.azure.com/), és ezt követően futtassa a parancsfájlt a módosított webhely nevét. A web Apps üzembe helyezési kapcsolatos további információkért lásd: [átmeneti környezet az Azure App Service web Apps beállítása](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>A publish-parancsfájlok futtatása

Soha ne futtatása előtt egy Windows PowerShell-parancsfájlt, ha a végrehajtási házirend a parancsfájlok futtatásának engedélyezéséhez először meg kell adnia. A csoportházirend egy olyan biztonsági beállítás megakadályozhatja, hogy a felhasználók a Windows PowerShell-parancsfájlok futtatásakor, ha ki van téve a kártevők és vírusok, például a parancsfájlok végrehajtása.

### <a name="run-the-script"></a>A parancsfájl futtatása

1. A projekt a Web Deploy csomagot hozhat létre. A Web Deploy csomag egy tömörített (.zip fájl), amely a webhely vagy a virtuális gép másolni kívánt fájlokat tartalmazó. A Visual Studio Web Deploy platformra alapuló csomagok bármely webalkalmazás hozhat létre.

![Webes hozható létre csomag telepítése](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

További információkért lásd: [Útmutató: webes telepítési csomag létrehozása a Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Automatizálható a Web Deploy csomag létrehozása a [testreszabása és a közzététel scripts[(#customizing-and-extending-publish-scripts) kiterjesztése]

1. A **Megoldáskezelőben**, nyissa meg a parancsfájl a helyi menüt, és válassza a **nyissa meg a PowerShell ISE**.
2. Ha ezen a számítógépen a Windows PowerShell-parancsfájlok futtatásakor először, nyisson meg egy parancssort rendszergazdai jogosultságokkal, és írja be a következő parancsot:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Jelentkezzen be az Azure-bA az alábbi paranccsal.

    ```powershell
    Add-AzureAccount
    ```

    Amikor a rendszer kéri, adja meg a felhasználónevét és jelszavát.

    Vegye figyelembe, hogy automatizálja a parancsfájlt, amikor ez a módszer az Azure hitelesítő adatokat adjanak nem működik. Ehelyett használjon a `.publishsettings` fájlt adjon meg hitelesítő adatokat. Egy alkalommal csak, a parancs segítségével **Get-AzurePublishSettingsFile** töltse le a fájlt az Azure-ból, és ezt követően használata **Import-AzurePublishSettingsFile** importálja a fájlt. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

4. (Választható) Az Azure erőforrások, például a virtuális gép létrehozásához, adatbázis, és a webalkalmazás közzétételét nélkül webhelyet használja a **Publish-WebApplication.ps1** parancsot a **-konfiguráció** argumentum kötelező értéke a JSON-konfigurációs fájlt. Ez a parancssor erőforrások létrehozásához a JSON-konfigurációs fájlt használja. Mivel a program az alapértelmezett beállításokat más parancssori argumentumokat, hoz létre az erőforrásokat, de nem tegye közzé a webalkalmazást. A-Verbose beállítás lehetővé teszi az eseményekről további információt.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Használja a **Publish-WebApplication.ps1** parancsot egy meghívása a parancsfájlt, és tegye közzé a webalkalmazást a következő példákban látható módon. Ha szeretné felülírják az alapértelmezett beállításokat az összes többi argumentum, például az előfizetés nevét, csomag neve, a virtuális gép hitelesítő adatait, vagy az adatbázis-kiszolgáló hitelesítő adatok közzététele, ezeket a paramétereket is megadhat. Használja a **– részletes** beállítás további információt a közzétételi folyamat előrehaladásának megtekintéséhez.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Ha egy virtuális gépet hoz létre, a parancs a következőképpen néznek. Ez a példa is mutatja meg a hitelesítő adatok több adatbázis. A virtuális gépek által létrehozott parancsfájlokat az SSL-tanúsítvány származik nem egy megbízható legfelső szintű hitelesítésszolgáltatóval. Ezért kell használnia a **– AllowUntrusted** lehetőséget.

    ```powershell
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    A parancsfájl adatbázisokat hozhat létre, de az adatbázis-kiszolgálók nem hoz létre. Ha azt szeretné, hozzon létre egy adatbázis-kiszolgálót, használhatja a **New-AzureSqlDatabaseServer** függvény a Azure modulban.

## <a name="customizing-and-extending-the-publish-scripts"></a>A közzététel parancsfájlok bővítik
Testre szabhatja a közzététel parancsfájlt és a JSON-konfigurációs fájlt. A funkciókat a Windows PowerShell-modul **AzureWebAppPublishModule.psm1** nem feltétlenül lehet módosítani. Ha szeretné adjon meg egy másik adatbázist, vagy módosítsa a virtuális gép tulajdonságok, szerkessze a JSON-konfigurációs fájlt. Ha kívánja kiterjeszteni a összeállításakor és tesztelésekor a projekt automatizálhatja a parancsfájl működésére, a függvény kódcsonkok Megvalósíthat **Publish-WebApplication.ps1**.

Automatizálható a projekt, vegye fel az MSBuild behívó kód `New-WebDeployPackage` a kód példában látható módon. Az MSBuild parancs elérési útja a telepített Visual Studio verziójától függően eltérnek. Ahhoz, hogy a helyes elérési utat, a funkció használata **Get-MSBuildCmd**, ebben a példában látható módon.

### <a name="to-automate-building-your-project"></a>A projekt automatizálásához
1. Adja hozzá a `$ProjectFile` paraméter a globális param szakaszban.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. A függvény másolni `Get-MSBuildCmd` a parancsfájl-fájlba.

    ```powershell
    function Get-MSBuildCmd
    {
            process
    {

                $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                    Sort-Object {[double]$_.PSChildName} -Descending |
                                    Select-Object -First 1 |
                                    Get-ItemProperty -Name MSBuildToolsPath |
                                    Select -ExpandProperty MSBuildToolsPath

                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

3. Cserélje le `New-WebDeployPackage` az az alábbi kód, és cserélje le a helyőrzőket a sor kiszámításakor `$msbuildCmd`. Ez a kód van, a Visual Studio 2017. Ha a Visual Studio 2015-öt használ, módosítsa a **VisualStudioVersion** tulajdonságot `14.0` (`12.0` Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    A webalkalmazás létrehozása a, használja a MsBuild.exe. Útmutatásért lásd: a MSBuild parancssori útmutatóban: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>A build parancs végrehajtásának indítása

```powershell
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru

if ($job.ExitCode -ne 0) {
    throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName

#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName

#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir

Write-VerboseWithTime 'Build-WebDeployPackage: End'

return $WebDeployPackage
}
```

1. Hívja a `New-WebDeployPackage` függvény előtt a sor: `$Config = Read-ConfigFile $Configuration` web Apps vagy `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` virtuális gépekhez.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. A egyéni parancsfájl használata a sikeres parancssorból meghívása a `$Project` argumentum, az alábbi példában látható módon:

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Automatizálható az alkalmazás tesztelése, adja hozzá a kódot a `Test-WebApplication`. Ügyeljen arra, hogy állítsa vissza a sorokat **Publish-WebApplication.ps1** ahol ezek a funkciók nevezzük. Ha nem ad meg megvalósítást, manuálisan a Visual Studio a projekt buildjének elkészítéséhez, és futtassa a a közzététel parancsfájl közzététele az Azure-bA.

## <a name="publishing-function-summary"></a>Közzétételi összesítő függvény
Ha segítséget szeretne kérni a funkciók elvégzésére is használhatja a Windows PowerShell parancssorába, a parancs használata `Get-Help function-name`. A Súgó paraméter Súgó és példákat tartalmaz. Az azonos súgószöveg egyben a parancsfájl forrásfájlok **AzureWebAppPublishModule.psm1** és **Publish-WebApplication.ps1**. A parancsfájl és a Súgó a Visual Studio nyelven honosítva vannak.

**AzureWebAppPublishModule**

| Függvény neve | Leírás |
| --- | --- |
| Add-AzureSQLDatabase |Létrehoz egy új Azure SQL-adatbázist. |
| Add-AzureSQLDatabases |Az értékek a JSON-konfigurációs fájlt, amely a Visual Studio létrehozza az Azure SQL adatbázisokat hoz létre. |
| Add-AzureVM |Egy Azure virtuális gépet hoz létre, és a központilag telepített virtuális gép URL-CÍMÉT adja vissza. A függvény állít be az Előfeltételek, és ekkor meghívja a **New-AzureVM** (Azure modul) működnek, hozzon létre egy új virtuális gépet. |
| Add-AzureVMEndpoints |Új bemeneti végpont ad hozzá egy virtuális gépet, és visszahelyezi a virtuális gépet az új végponttal. |
| Add-AzureVMStorage |A jelenlegi előfizetés hoz létre egy új Azure storage-fiók. A fiók neve kezdődik "devtest" egyedi alfanumerikus karakterlánc követ. A funkció az új tárfiók a nevét adja vissza. Adja meg egy helyet vagy az új tárfiók affinitáscsoport. |
| Add-AzureWebsite |A megadott név és hely egy webhelyet hoz létre. Ez a funkció meghívja a **New-AzureWebsite** függvény a Azure modulban. Ha az előfizetés már nem tartalmazza a megadott névvel rendelkező webhely, ez a funkció a webhelyet hoz létre, és egy webhely objektumot ad vissza. Ellenkező esetben az eredmény `$null`. |
| Backup-előfizetés |A jelenlegi Azure-előfizetést a menti a `$Script:originalSubscription` változó parancsfájl hatókörében. Ez a funkció menti az aktuális Azure-előfizetés (módon nyert `Get-AzureSubscription -Current`) és a tárfiók, és az előfizetés módosítja ezt a parancsfájlt (a változó tárolja `$UserSpecifiedSubscription`) és a tárfiókot, a parancsfájl hatókörében. Úgy, hogy elmenti az értékeket, a függvény használható, például a `Restore-Subscription`, állítsa vissza az eredeti aktuális előfizetés és a storage-fiók aktuális állapotát, ha a jelenlegi állapota megváltozott. |
| Find-AzureVM |Lekérdezi a megadott Azure virtuális géphez. |
| Format-DevTestMessageWithTime |A dátum és idő üzenetre lefoglalja. Ez a funkció a hiba- és részletes adatfolyamok üzenetek tervezték. |
| Get-AzureSQLDatabaseConnectionString |Állítja össze a kapcsolati karakterláncot egy Azure SQL adatbázishoz való kapcsolódáshoz. |
| Get-AzureVMStorage |A minta első tárfiók a nevét adja vissza "devtest *" (kis-és nagybetűket) a megadott helyre vagy az affinitáscsoport. Ha a "devtest*" tárfiók helye vagy affinitáscsoportja nem egyezik a, a függvény figyelmen kívül hagyja azt. Adja meg egy helyet vagy affinitáscsoport. |
| Get-MSDeployCmd |A MsDeploy.exe eszköz futtatni kívánt parancs adja vissza. |
| New-AzureVMEnvironment |Megállapítja, vagy létrehoz egy virtuális gépet, amely megfelel a JSON-konfigurációs fájlban lévő értékeket az előfizetést. |
| Publish-WebPackage |Felhasználási MsDeploy.exe és a webes közzétenni a csomagot. A zip-fájl erőforrások telepítése a webhelyen. Ez a függvény nem ad kimenetet. Ha MSDeploy.exe hívása sikertelen, a függvény kivételt vált. Részletesebb kimenet használatához a **-Verbose** lehetőséget. |
| Publish-WebPackageToVM |A paraméterértékek ellenőrzi, és ekkor meghívja a **Publish-WebPackage** függvény. |
| Read-ConfigFile |A JSON-konfigurációs fájl ellenőrzi, és egy kivonattáblát a kijelölt értékeket adja vissza. |
| Restore-Subscription |A jelenlegi előfizetés visszaállítása az eredeti előfizetés. |
| Test-AzureModule |Beolvasása `$true` Ha telepített Azure modul verziószáma 0.7.4 vagy újabb. Beolvasása `$false` , ha a modul nincs telepítve, vagy korábbi verziójú. Ez a funkció nem paraméterrel rendelkezik. |
| Test-AzureModuleVersion |Beolvasása `$true` Ha Azure-moduljának verziószáma 0.7.4 vagy újabb. Beolvasása `$false` , ha a modul nincs telepítve, vagy korábbi verziójú. Ez a funkció nem paraméterrel rendelkezik. |
| Test-HttpsUrl |A megadott URL-cím konvertál egy System.Uri objektumnak. Beolvasása `$True` Ha abszolút URL-CÍMÉT és az sémát használja https. Beolvasása `$false` Ha az URL-cím relatív, a rendszer nem HTTPS, vagy a bemeneti karakterlánc nem alakítható át egy URL-címet. |
| Teszt-tag |Beolvasása `$true` Ha egy tulajdonság vagy metódus tagja az objektum. Ellenkező esetben adja vissza `$false`. |
| Write-ErrorWithTime |Az aktuális idő előtagként hibaüzenetet ír. Ez a funkció meghívja a **formátum-DevTestMessageWithTime** függvény illesztenie az idő az üzenetet a hibafolyam írása előtt. |
| Write-HostWithTime |A gazdagép program üzenetet ír (**Write-Host**) az aktuális idő előtagként. A hatását, hogy a gazdagép program írásakor függ. A legtöbb üzemeltető Windows PowerShell írási ezek az üzenetek normál a kimenetbe. |
| Write-VerboseWithTime |Az aktuális idő előtagként egy részletes üzenetet ír. Mert meghívja **Write-Verbose**, az üzenet jelenik meg, csak ha a parancsfájl futtatása a a **részletes** paraméter, vagy ha a **VerbosePreference** beállítás értéke **Folytatás**. |

**Publish-WebApplication**

| Függvény neve | Leírás |
| --- | --- |
| New-AzureWebApplicationEnvironment |Azure-erőforrások, például a webhelyek vagy virtuális gépet hoz létre. |
| New-WebDeployPackage |Ez a funkció nincs megvalósítva. Ez a funkció a projekt felépítéséhez parancsok adhat hozzá. |
| Publish-AzureWebApplication |A webalkalmazások Azure közzéteszi. |
| Közzététele: webalkalmazás |Hoz létre, és a Web Apps, a virtuális gépek, a SQL-adatbázisok és a storage-fiókok a Visual Studio webes projektet telepít. |
| Teszt:-webalkalmazás |Ez a funkció nincs megvalósítva. Ez a függvény az alkalmazás teszteléséhez parancsok adhat hozzá. |

## <a name="next-steps"></a>További lépések
PowerShell parancsfájl-kezelési beolvasásával kapcsolatos további [a Windows PowerShell parancsfájlok](https://technet.microsoft.com/library/bb978526.aspx) és egyéb Azure PowerShell-parancsfájlok, tekintse meg a [Script Center](https://azure.microsoft.com/documentation/scripts/).
