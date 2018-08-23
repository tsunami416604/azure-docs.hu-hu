---
title: Windows PowerShell-szkriptek használata a fejlesztési és tesztelési környezetek közzétételére |} A Microsoft Docs
description: Ismerje meg, hogyan használható a Windows PowerShell-szkriptek a Visual Studióból közzétételére a fejlesztési és tesztelési környezetek.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: dac5425f72ff57e412be664e1bc0c84aee3dec1f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056377"
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Windows PowerShell-parancsprogramok használata a fejlesztési és tesztelési környezetben való közzétételhez

Ha a Visual Studióban hozzon létre egy webalkalmazást, egy Windows PowerShell-parancsprogram, amellyel később automatikusan közzétehető a webhely Azure-webalkalmazás létrehozása az Azure App Service vagy egy virtuális gépet, is létrehozhat. Szerkesztés és kiterjesztése a Windows PowerShell-parancsfájlt a Visual Studio szerkesztőjében vonatkozó követelményeknek megfelelően, vagy belefoglalható a meglévő fordítási, tesztelési és közzétételi parancsprogramokba.

Ezeket a parancsfájlokat használ, testre szabott verzióit (más néven a fejlesztési és tesztelési környezetben) ideiglenes használatra a hely kiépítése. Beállíthat például egy adott verzióját a webhely egy Azure virtuális gépen, illetve egy webhelyen egy tesztcsomag futtassa, reprodukálni a hibát, tesztelje a hibajavítás, próbaverzió változtatást vagy egy bemutatót vagy bemutató egyéni környezet kialakítása az előkészítési pontot. Miután létrehozott egy parancsfájlt, amely közzéteszi a projektet, hozza létre újból az azonos környezetben a parancsfájl újrafuttatásával, igény szerint, vagy futtassa a szkriptet saját buildelési, tesztelési egy egyéni környezet létrehozásához a webalkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure SDK 2.3-as vagy újabb verziója. Lásd: [a Visual Studio letöltések](http://go.microsoft.com/fwlink/?LinkID=624384). (Az Azure SDK-t létrehozni a szkripteket a webes projektek nem szükséges. Ez a funkció a webes projektek, a cloud services webes szerepkörök-nem.)
* Az Azure PowerShell 0.7.4 vagy újabb. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) vagy újabb.

## <a name="additional-tools"></a>További eszközök

További eszközöket és erőforrásokat az Azure-fejlesztés a PowerShell használatával a Visual Studio használatához érhetők el. Lásd: [PowerShell Tools for Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>A közzétételi parancsprogramok generálása

A közzétételi parancsprogramok, amelyen a webhely egy új projektet hoz létre a következő virtuális gép is létrehozhat [ezek az utasítások](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Emellett [készítése az Azure App Service web Apps parancsfájlok közzététele](app-service/scripts/app-service-powershell-deploy-github.md).

## <a name="scripts-that-visual-studio-generates"></a>A Visual Studio által létrehozott parancsfájlok

A Visual Studio létrehoz egy megoldás-szintű nevű mappába **PublishScripts** , amely tartalmazza a Windows PowerShell két fájlt, egy közzétételi szkriptet a virtuális gép vagy a webhelyet, és a egy modul, amely függvényeket tartalmaz, amelyek segítségével használhatja a parancsfájlok. A Visual Studio is létrehoz egy fájlt a JSON-formátumban, amely meghatározza a projektet telepít részletes adatait.

### <a name="windows-powershell-publish-script"></a>Windows PowerShell parancsfájl közzététele

A közzététel szkript adott közzététel egy webhelyen vagy virtuális gépen való telepítésének lépéseit tartalmazza. A Visual Studio szintaxisszínek Windows PowerShell-fejlesztéshez nyújt. A functions érhető el, és szabadon szerkesztheti a funkciókat a változó igényeinek megfelelően a parancsfájl segítségével.

### <a name="windows-powershell-module"></a>Windows PowerShell-modul

A Windows PowerShell-modult, amely a Visual Studio létrehozza a függvények, amelyek a közzététel a szkript tartalmazza. Azure PowerShell-lel függvényekhez nem tartozhat módosítható. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

### <a name="json-configuration-file"></a>JSON-konfigurációs fájl
A JSON-fájlt hoznak létre a **konfigurációk** mappa, és arról, pontosan mely erőforrásokat helyezze üzembe az Azure konfigurációs adatokat tartalmaz. A fájlt a Visual Studio által létrehozott név project-name-WAWS-dev.json Ha létrehozott egy webhely vagy projekt nevét – Virtuálisgép-dev.json Ha egy virtuális gépet hozott létre. Íme egy példa egy JSON konfigurációs fájl, amely akkor jön létre, amikor létrehoz egy webhelyet. Az értékek többsége magától értetődő. A webhely neve az Azure jön létre, akkor előfordulhat, hogy a projekt neve nem egyezik.

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

Amikor létrehoz egy virtuális gépet, a JSON konfigurációs fájl a következőhöz hasonlóan néz ki. Cloud service egy tároló, a virtuális gép jön létre. A virtuális gépet tartalmaz, a szokásos végpontjait a HTTP és HTTPS webes elérés, valamint a végpontok a Web Deploy, amely lehetővé teszi közzé a webhelyet a helyi gépen, a távoli asztal és a Windows PowerShell.

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

Szerkesztheti a JSON-konfiguráció módosításához, mi történik, ha a közzététel-parancsfájlok futtatásakor. A `cloudService` és `virtualMachine` szakasz használata kötelező, de törölheti a `databases` szakaszt, ha már nincs szüksége. Az alapértelmezett konfigurációs fájl, amely a Visual Studio létrehozza az üres tulajdonságok megadása nem kötelező; azokat a tulajdonságokat, az alapértelmezett konfigurációs fájl értéket tartalmazó szükség.

Ha egy webhely, amely rendelkezik több üzembehelyezési környezet (más néven a tárhelyek) helyett egy egyszeri éles webhelyhez, az Azure-ban, a JSON konfigurációs fájl belefoglalhatja a tárhely nevének be a webhely nevét. Például, ha rendelkezik egy webhellyel nevű **saját webhely** és a egy tárhely, nevű **tesztelése** ezután az URI-ja `mysite-test.cloudapp.net`, de a helyes nevét, a konfigurációs fájlban mysite(test). Csak ez, ha a webhely és a tárolóhely már létezik az előfizetésben. Webhely létrehozása, ha azok nem léteznek, a szkript futtatásával a tárolóhely megadása nélkül, majd hozza létre a tárolóhely a a [az Azure portal](https://portal.azure.com/), és ezt követően futtassa a parancsprogramot a módosított webhely nevét. A web apps üzembe helyezési pontok kapcsolatos további információkért lásd: [állítsa be átmeneti környezeteket az Azure App Service web apps](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>A közzétételi parancsprogramok futtatása

Ha soha nem futtatott Windows PowerShell-parancsfájl előtt, a végrehajtási házirend a parancsfájlok futtatásának engedélyezéséhez először meg kell adnia. A házirend olyan biztonsági funkció, amely megakadályozza, hogy a felhasználók Windows PowerShell-parancsfájlok futtatásakor, ha kártevőket vagy parancsfájlok végrehajtása érintő vírusok kitéve.

### <a name="run-the-script"></a>A parancsfájl futtatása

1. A Web Deploy csomag a projekt létrehozásához. A Web Deploy csomag egy tömörített (.zip kiterjesztésű fájl), amelyet szeretne a webhelyen vagy virtuális gépen másolja a fájlokat tartalmazó. Webes telepítési csomagokat hozhat létre bármely webalkalmazásba a Visual Studióban.

![Hozzon létre a Web csomag telepítése](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

További információkért lásd: [Útmutató: webes telepítési csomag létrehozása a Visual Studióban](https://msdn.microsoft.com/library/dd465323.aspx). Ugyanígy automatizálható a Web Deploy csomag létrehozása [testreszabása és kibővítése a közzététel scripts[(#customizing-and-extending-publish-scripts)] leírtak szerint

1. A **Megoldáskezelőben**, a parancsfájl a helyi menü megnyitásához, és válassza **nyissa meg a PowerShell ISE-ben**.
2. Ha a Windows PowerShell-parancsfájlok futtatásakor ezen a számítógépen, először, nyisson meg egy parancssori ablakot rendszergazdai jogosultságokkal, és írja be a következő parancsot:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Jelentkezzen be az Azure-bA a következő parancs segítségével.

    ```powershell
    Add-AzureAccount
    ```

    Amikor a rendszer kéri, adja meg a felhasználónevét és jelszavát.

    Vegye figyelembe, hogy automatizálja a parancsfájlt, amikor ezzel a módszerrel az Azure a hitelesítő adatokkal nem működik. Ehelyett használjon a `.publishsettings` fájl hitelesítő adatait. Egyszer, a paranccsal **Get-AzurePublishSettingsFile** töltse le a fájlt az Azure-ból, és ezt követően használata **Import-AzurePublishSettingsFile** importálja a fájlt. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

4. (Nem kötelező) Ha azt szeretné, például a virtuális gép Azure-erőforrások létrehozása, adatbázis és a webhely a webalkalmazás közzététele nélkül használja a **Publish-WebApplication.ps1** parancsot a **-konfiguráció**argumentum állítsa be a JSON konfigurációs fájl. Ez a parancssor segítségével, hogy a JSON konfigurációs fájl határozza meg, mely erőforrásokat hozhat létre. Parancssori argumentumok használ az alapértelmezett beállításokat, mert létrehozza az erőforrásokat, de nem közzéteheti webalkalmazását. A – részletes lehetőséget biztosít további információt, mi történik.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Használja a **Publish-WebApplication.ps1** parancs indítja el a szkriptet, és tegye közzé a webalkalmazást az alábbi példák egyikét látható módon. Ha szeretne felülírják az alapértelmezett beállításokat, sem a további argumentumok, például az előfizetés nevét, és tehet közzé, csomag neve, a virtuális gép hitelesítő adatainak vagy az adatbázis hitelesítő adatait, ezeket a paramétereket is megadhat. Használja a **– részletes** beállítással kapcsolatos további információ a folyamat állapotát a közzétételi folyamat.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Ha egy virtuális gépet hoz létre, a parancs a következőképpen néznek. Ebben a példában azt is bemutatja, hogyan adja meg a több adatbázis hitelesítő adatait. A virtuális gépek, ezek a szkriptek hoz létre az SSL-tanúsítvány nincs a megbízható főtanúsítvány tanúsítványláncát. Ezért kell használnia a **– AllowUntrusted** lehetőséget.

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

    A parancsfájl adatbázist is létrehozhat, de az adatbázis-kiszolgálók nem hoz létre. Ha azt szeretné, hozzon létre egy adatbázis-kiszolgálót, akkor használhatja a **New-AzureSqlDatabaseServer** függvényt az Azure-modul.

## <a name="customizing-and-extending-the-publish-scripts"></a>Testre szabható és bővíthető a közzétételi parancsprogramok
Testre szabhatja a publish-szkript és a JSON-konfigurációs fájlt. A Windows PowerShell-modul az a funkciók **AzureWebAppPublishModule.psm1** nem tartozhat módosítható. Ha csak át szeretné adjon meg egy másik adatbázist, vagy módosítsa a virtuális gép tulajdonságok némelyike, szerkessze a JSON konfigurációs fájl. Ha azt szeretné, a parancsfájl elkészítését és tesztelését a projekt automatizálásához funkcióinak bővítése érdekében valósítható meg a függvény fájl **Publish-WebApplication.ps1**.

Automatizálhatja a projekt, adja hozzá a kódot, amely meghívja ezt az MSBuild `New-WebDeployPackage` kód ebben a példában látható módon. Az MSBuild parancs elérési útja telepítette a Visual Studio verziójától függően eltérő. A funkció használata megszerezni a helyes elérési utat, **Get-MSBuildCmd**ebben a példában látható módon.

### <a name="to-automate-building-your-project"></a>A projekt automatizálása
1. Adja hozzá a `$ProjectFile` paraméter a globális param szakaszban.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Másolja a függvény `Get-MSBuildCmd` a szkript fájlba.

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

3. Cserélje le `New-WebDeployPackage` az a következő kódra, és cserélje le a helyőrzőket a sor felépítéséhez `$msbuildCmd`. Ez a kód a Visual Studio 2017. Ha a Visual Studio 2015-öt használ, módosítsa a **VisualStudioVersion** tulajdonságot `14.0` (`12.0` Pro Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    A webalkalmazások létrehozása, MsBuild.exe használja. Segítségre van szüksége MSBuild parancssori útmutatóban, lásd: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Indítsa el a build parancs végrehajtása

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

1. Hívja a `New-WebDeployPackage` függvény előtt ezt a sort: `$Config = Read-ConfigFile $Configuration` web Apps vagy `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` virtuális gépek számára.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. A parancssorból megadásának használatával egyéni szkript meghívása a `$Project` argumentum, a következő példához hasonlóan:

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Automatizálhatja az alkalmazás tesztelése, adja hozzá a kódot `Test-WebApplication`. Ügyeljen arra, hogy távolítsa el a sorokat **Publish-WebApplication.ps1** ahol ezek a függvények nevezzük. Implementációja nem ad meg, ha manuálisan hozhat létre a projektet a Visual Studio használatával, és futtassa a közzététel parancsfájl közzététele az Azure-bA.

## <a name="publishing-function-summary"></a>Közzétételi összesítő függvény
Segítséget a Windows PowerShell-parancssorban használható függvények, a parancs használata `Get-Help function-name`. A Súgó paraméter Súgó és a példákat tartalmaz. Az azonos súgószöveg is szerepel a parancsfájl forrásfájlok **AzureWebAppPublishModule.psm1** és **Publish-WebApplication.ps1**. A szkript és a Súgó a Visual Studio nyelvével honosított.

**AzureWebAppPublishModule**

| Függvény neve | Leírás |
| --- | --- |
| Add-AzureSQLDatabase |Létrehoz egy új Azure SQL-adatbázist. |
| Add-AzureSQLDatabases |A Visual Studio által létrehozott JSON-konfigurációs fájl értékeinek hoz létre az Azure SQL Database-adatbázisok. |
| Add-AzureVM |Létrehoz egy Azure virtuális gépen, és az üzembe helyezett virtuális gép URL-CÍMÉT adja vissza. A függvény állítja be az előfeltételeket, és ekkor meghívja a **New-AzureVM** egy új virtuális gép létrehozása (Azure-modul) függvényt. |
| Add-AzureVMEndpoints |Új bemeneti végpontok ad hozzá egy virtuális gépet, és visszaadja a virtuális gépről az új végpont. |
| Add-AzureVMStorage |Az aktuális előfizetésben hoz létre egy új Azure storage-fiókot. A fiók nevét a "devtest" követ egy egyedi alfanumerikus karakterláncnak kezdődik. A függvény az új tárfiók a nevét adja vissza. Adja meg, vagy egy olyan helyre, vagy az új tárfiók affinitáscsoport. |
| Add-AzureWebsite |Létrehoz egy webhelyet a megadott nevét és helyét. Ez a függvény meghívja a **New-AzureWebsite** függvényt az Azure-modul. Ha az előfizetés már nem tartalmazza a megadott névvel rendelkező webhely, ez a függvény hoz létre a webhely, és a egy webhely objektumot ad vissza. Ellenkező esetben adja vissza `$null`. |
| Backup-előfizetés |Menti az aktuális Azure-előfizetést, a `$Script:originalSubscription` változó parancsfájl hatókörében. Ez a funkció menti az aktuális Azure-előfizetés (módon nyert `Get-AzureSubscription -Current`) és a storage-fiókot, és az előfizetést, amelyhez ez a szkript módosítja (a változó tárolja `$UserSpecifiedSubscription`) és a storage-fiók, a parancsfájl hatókörében. Menti az értékeket, a funkció használata, mint például `Restore-Subscription`, állítsa vissza az eredeti aktuális előfizetés és a storage-fiók aktuális állapotát, ha a jelenlegi állapota megváltozott. |
| Find-AzureVM |Lekérdezi a megadott Azure virtuális gép. |
| Format-DevTestMessageWithTime |A dátum és idő, egy üzenet lefoglalja. Ez a funkció a hiba- és részletes Streamek üzenetek lett tervezve. |
| Get-AzureSQLDatabaseConnectionString |Több Azure SQL Database-adatbázishoz csatlakozni egy kapcsolati karakterláncot. |
| Get-AzureVMStorage |A minta az első storage-fiók nevét adja vissza "devtest *" (megkülönbözteti a kis és nagybetűk megkülönböztetése nélkül) a megadott helyre vagy az affinitáscsoport. Ha a "devtest*" tárfiók nem felel meg, a helye vagy affinitáscsoportja, a függvény figyelmen kívül hagyja. Adja meg, vagy egy olyan helyre, vagy az affinitáscsoportot. |
| Get-MSDeployCmd |A MsDeploy.exe eszköz futtatni kívánt parancs adja vissza. |
| New-AzureVMEnvironment |Megállapítja, vagy létrehoz egy virtuális gépet, amely megfelel a JSON konfigurációs fájlban szereplő értékek az előfizetésben. |
| Publish-WebPackage |Felhasználási MsDeploy.exe és a egy webes csomag közzététele. Erőforrások üzembe helyezése webhelyeken a zip-fájlt. Ez a függvény nem ad kimenetet. Ha MSDeploy.exe hívása sikertelen, a függvény kivételt jelez. Részletesebb kimenet lekéréséhez használja a **-Verbose** lehetőséget. |
| Publish-WebPackageToVM |Ellenőrzi a paraméterértékeket, és ezután meghívja a **Publish-WebPackage** függvény. |
| Read-ConfigFile |A JSON konfigurációs fájl érvényesíti, és a kiválasztott értékek kivonatoló tábláját adja vissza. |
| Restore-Subscription |Az eredeti előfizetés visszaállítja az aktuális előfizetésben. |
| Test-AzureModule |Értéket ad vissza `$true` 0.7.4 esetén a telepített Azure-modul verzió vagy újabb. Értéket ad vissza `$false` , ha a modul nincs telepítve, vagy korábbi verziójú. Ez a funkció nem tartozik paraméter. |
| Test-AzureModuleVersion |Értéket ad vissza `$true` 0.7.4 esetén az Azure-modul verzióját vagy újabb. Értéket ad vissza `$false` , ha a modul nincs telepítve, vagy korábbi verziójú. Ez a funkció nem tartozik paraméter. |
| Test-HttpsUrl |A bemeneti URL-cím alakít egy System.Uri objektumot. Értéket ad vissza `$True` Ha abszolút URL-CÍMÉT és a rendszer a https. Értéket ad vissza `$false` Ha az URL-cím relatív a rendszer nem HTTPS, vagy a a bemeneti karakterlánc nem alakítható át egy URL-címet. |
| Test-tag |Értéket ad vissza `$true` Ha egy tulajdonság és metódus az objektum tag. Ellenkező esetben adja vissza `$false`. |
| Write-ErrorWithTime |Ír egy hibaüzenet, az aktuális idő előtaggal. Ez a függvény meghívja a **formátumban – DevTestMessageWithTime** függvényt az üzenet a hibafolyam írása előtt az idő illesztenie. |
| Write-HostWithTime |A gazdagép program üzenetet ír (**Write-Host**) előtaggal van ellátva a jelenlegi idő. A gazdagép program írása hatásának változik. A legtöbb, amelyeken a Windows PowerShell az írási ezeket az üzeneteket normál a kimenetbe. |
| Write-VerboseWithTime |Az aktuális idő előtaggal részletes üzenetet ír. Mivel meghívja **Write-Verbose**, az üzenet jelenik meg, hogy csak az a szkript futtatásakor a **részletes** paraméter, vagy ha a **VerbosePreference** szabályozó értékrevanállítva **Továbbra is**. |

**Publish-WebApplication**

| Függvény neve | Leírás |
| --- | --- |
| New-AzureWebApplicationEnvironment |Azure-erőforrások, például egy webhelyen vagy virtuális gépen hoz létre. |
| New-WebDeployPackage |Ez a funkció nincs megvalósítva. Ez a funkció a projekt parancsokat adhat hozzá. |
| Publish-AzureWebApplication |Közzétesz egy webalkalmazást az Azure-bA. |
| Közzététel: webalkalmazás |Létrehozza, és a Web Apps, a virtuális gépek, a SQL-adatbázisok és a storage-fiókot egy Visual Studio web projektet telepíti. |
| Test-WebApplication |Ez a funkció nincs megvalósítva. Ez a függvény az alkalmazás teszteléséhez parancsokat adhat hozzá. |

## <a name="next-steps"></a>További lépések
További tudnivalók a PowerShell-parancsprogramok olvasásával [parancsfájlkezelés a Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) és egyéb Azure PowerShell-parancsfájlok, tekintse meg a [Script Center](https://azure.microsoft.com/documentation/scripts/).
