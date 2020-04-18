---
title: Az Azure-SSIS-integrációs futásidejű beállítás testreszabása
description: Ez a cikk azt ismerteti, hogy miként használható az Azure-SSIS-integrációs futásidejű egyéni beállítási felülete további összetevők telepítéséhez vagy beállítások módosításához
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: ab2ba31d6b712bd3399bc8bf5b491337d462dac9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606205"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Az Azure-SSIS-integrációs futásidejű beállítás testreszabása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) egyéni beállítása felületet biztosít a saját lépések hozzáadásához az Azure-SSIS IR beállítása vagy újrakonfigurálása során. 

Az egyéni beállítás használatával módosíthatja az alapértelmezett működési konfigurációt vagy környezetet, például további Windows-szolgáltatások indítására, a fájlmegosztások hozzáférési hitelesítő adatainak megőrzésére, vagy erős titkosítási/biztonságosabb hálózati protokoll (TLS 1.2) használatára. Vagy további összetevőket is telepíthet, például szerelvényeket, illesztőprogramokat vagy bővítményeket az Azure-SSIS IR minden csomópontján.

Az Azure-SSIS ir-en az egyéni beállításokat kétféleképpen teheti meg: 
* **Egyéni beállítás expresszelt írással:** Futtasson néhány általános rendszerkonfigurációt és Windows-parancsot, vagy telepítsen néhány népszerű vagy ajánlott további összetevőt parancsfájl használata nélkül.
* **Szabványos egyéni beállítás parancsfájllal:** Készítsen elő egy parancsfájlt és a hozzá tartozó fájlokat, és töltse fel őket együtt egy blobtárolóba az Azure storage-fiókjában. Ezután az Azure-SSIS-ir beállításakor vagy újrakonfigurálásakor megad egy egységes erőforrás-azonosítót (SAS) a tárolóhoz. Az Azure-SSIS-ir minden egyes csomópontja letölti a parancsfájlt és a hozzá tartozó fájlokat a tárolóból, és emelt szintű engedélyekkel futtatja az egyéni telepítést. Amikor az egyéni beállítás befejeződött, minden csomópont feltölti a végrehajtás és más naplók szabványos kimenetét a tárolóba.

Telepítheti mind az ingyenes, engedély nélküli összetevők és a fizetett, licencelt alkatrészek expressz és szabványos egyéni beállításokat. Ha Ön független szoftverszállító (ISV), olvassa el a Fizetett vagy licencelt összetevők fejlesztése Azure-SSIS ir.If you're a independent software vendor (ISV), see [Develop paid or licensed components for an Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> A jövőbeli fejlesztések előnyeinek kihasználása érdekében azt javasoljuk, hogy az Azure-SSIS-ir-hez az Azure-SSIS-ir-hez használjon v3-as vagy újabb csomópont-sorozatot egyéni beállítással.

## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások csak a szabványos egyéni beállításokra vonatkoznak:

- Ha a *gacutil.exe* fájlt szeretné használni a parancsfájlban a szerelvények globális összeállítási gyorsítótárba (GAC) történő telepítéséhez, akkor a *gacutil.exe* fájlt az egyéni beállítás részeként kell megadnia. Vagy használhatja a *nyilvános előzetes verziótárolóban* biztosított másolatot, amelyet később az "Utasítások" című szakaszban tárgyalunk.

- Ha a parancsfájl ban almappára szeretne hivatkozni, az *msiexec.exe* nem támogatja a `.\` gyökérmappára való hivatkozást. Használjon parancsot, `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`például a helyett.

- Az Azure-SSIS IR jelenleg nem támogatja a felügyeleti megosztásokat vagy a Windows által automatikusan létrehozott rejtett hálózati megosztásokat.

- The IBM iSeries Access ODBC driver is not supported on the Azure-SSIS IR. Az egyéni telepítés során telepítési hibák jelenhetnek meg. Ha így tesz, kérjen segítséget az IBM támogatási szolgálatátél.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure-SSIS IR testreszabásához a következő elemekre van szükség:

- [Azure-előfizetés](https://azure.microsoft.com/)

- [Az Azure-SSIS IR kiépítése](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Egy Azure-tárfiók.](https://azure.microsoft.com/services/storage/) Nem szükséges az expressz egyéni beállításokhoz. A szabványos egyéni beállítások, feltölti és tárolja az egyéni beállítási parancsfájlt és a kapcsolódó fájlokat egy blob tárolóban. Az egyéni telepítési folyamat is feltölti a végrehajtási naplókat ugyanabba a blob tárolóba.

## <a name="instructions"></a>Utasítások

1. Ha be szeretné állítani vagy újra szeretné konfigurálni az Azure-SSIS ir-t a PowerShell segítségével, töltse le és telepítse az [Azure PowerShellt.](/powershell/azure/install-az-ps) Az expressz egyéni beállításokhoz ugorjon a 4.

1. Készítse elő az egyéni telepítőparancsfájlt és a hozzá tartozó fájlokat (például .bat, .cmd, .exe, .dll, .msi vagy .ps1 fájlokat).

   * Rendelkeznie kell egy *main.cmd*nevű parancsfájllal, amely az egyéni beállítás belépési pontja.  
   * Annak érdekében, hogy a parancsfájl tinőcsendben végrehajtható legyen, javasoljuk, hogy először tesztelje a helyi számítógépen.  
   * Ha azt szeretné, hogy a más eszközök (például *az msiexec.exe)* által létrehozott további naplókat `CUSTOM_SETUP_SCRIPT_LOG_DIR`töltse fel a tárolóba, adja meg az előre definiált környezeti változót , a parancsfájlok naplómappájaként (például *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Töltse le, telepítse és nyissa meg [az Azure Storage Explorert.](https://storageexplorer.com/) Ehhez tegye a következőket:

   a. A **(Helyi és csatolt)** területen kattintson a jobb gombbal **a Tárfiókok**elemre, majd válassza **a Csatlakozás az Azure storage-hoz parancsot.**

      ![Csatlakozás az Azure Storage-hoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Válassza **a Tárfiók nevének és kulcsának használata**lehetőséget, majd a **Tovább**gombot.

      ![Tárfióknév és -kulcs használata](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Adja meg az Azure tárfiók nevét és kulcsát, válassza a **Tovább**gombot, majd a **Csatlakozás**lehetőséget.

      ![Tárfiók nevének és kulcsának megadására](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. A csatlakoztatott Azure-tárfiók alatt kattintson a jobb gombbal **a Blob-tárolók**elemre, válassza **a Blob-tároló létrehozása parancsot,** és nevezze el az új tárolót.

      ![Blobtároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Válassza ki az új tárolót, és töltse fel az egyéni beállítási parancsfájlt és a hozzá tartozó fájlokat. Győződjön meg arról, hogy a *main.cmd* fájlt a tároló legfelső szintjén töltötte fel, nem pedig egy mappába. Győződjön meg arról is, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza, így az Azure-SSIS IR későbbi letöltése nem fog sokáig tartani. Az egyéni beállítások maximális időtartama jelenleg 45 perccel az időtúltöltés előtt van beállítva. Ez magában foglalja az összes fájl letöltésének és az Azure-SSIS ir-re való telepítésének idejét. Ha a telepítés több időt igényel, emelje fel a támogatási jegyet.

      ![Fájlok feltöltése a blobtárolóba](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Kattintson a jobb gombbal a tárolóra, és válassza **a Közös hozzáférésű aláírás bekerülése parancsot.**

      ![A tároló megosztott hozzáférési aláírásának beszereznie](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Hozza létre a SAS URI-t a tárolóhoz elegendően hosszú lejárati idővel és olvasási/írási/lista engedéllyel. A SAS URI-ra az egyéni beállítási parancsfájl és a hozzá tartozó fájlok letöltéséhez és futtatásához szükség van, amikor az Azure-SSIS-ir bármely csomópontját újrarendszerezik vagy újraindítják. A telepítő végrehajtási naplóinak feltöltéséhez írási engedélyszükséges.

      > [!IMPORTANT]
      > Győződjön meg arról, hogy a SAS URI nem jár le, és az egyéni beállítási erőforrások mindig elérhetők az Azure-SSIS ir teljes életciklusa során, a létrehozástól a törlésig, különösen akkor, ha ebben az időszakban rendszeresen leállítja és elindítja az Azure-SSIS IR-t.

      ![A tároló megosztott hozzáférési aláírásának létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Másolja és mentse a tároló SAS URI-ját.

      ![A megosztott hozzáférésű aláírás másolása és mentése](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Amikor beállítja vagy újrakonfigurálja az Azure-SSIS ir-t egy adatgyári felhasználói felülettel, egyéni beállításokat adhat hozzá vagy távolíthat el az **Azure-SSIS-integrációs futásidő testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzet bejelölésével az **Integrációs futásidejű beállítási** ablaktábla **Speciális beállítások** szakaszában. 

   Ha szabványos egyéni beállításokat szeretne hozzáadni, írja be a tároló SAS URI-ját az **Egyéni beállítási tároló SAS** URI-mezőjébe. 
   
   Ha expressz egyéni beállításokat szeretne hozzáadni, válassza az **Új** lehetőséget az **Expressz hozzáadása egyéni beállítási** ablaktábla megnyitásához, majd válasszon egy típust az **Express egyéni beállítási típus** legördülő listájában:

   * Ha a **Cmdkey futtatása parancstípust** választja, a fájlmegosztások vagy az Azure Files-megosztások azure-SSIS-alapú hozzáférés-hozzáférési hitelesítő adatait megőrizheti a célzott számítógépnév vagy tartománynév, a fióknév vagy felhasználónév, valamint a fiókkulcs vagy jelszó megadásával a **/Add**, **/User**és **/Pass** mezőkben. Ez hasonló a Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) parancs helyi számítógépen való futtatásához.
   
   * Ha a **Környezeti környezet hozzáadása változó** típus, windowsos környezeti változókat adhat hozzá az Azure-SSIS ir-en futó csomagokban, ha beírja a környezeti változó nevét és értékét a **változó nevébe** és a **változó értékmezőbe.** Ez hasonló a Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) parancs helyi számítógépen való futtatásához.

   * Ha a **Licencelt összetevő telepítése** típust választja, a **Komponensnév** legördülő listában kiválaszthat egy integrált összetevőt a független szoftverfejlesztési partnereink közül:

     * Ha a **SentryOne Task Factory összetevőjét választja,** telepítheti a [SentryOne-ból](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) származó Task Factory összetevőcsomagot az Azure-SSIS IR-re, ha beírja a tőlük vásárolt terméklicenckulcsot a **Licenckulcs** mezőbe. A jelenlegi integrált verzió **a 2019.4.3**.

     * Ha az **oh22 HEDDA-ját választja. IO** komponens, telepítheti a [HEDDA. Io-adatok](https://hedda.io/ssis-component/) minősége/tisztító összetevő az oh22-től az Azure-SSIS IR-en, miután megvásárolta a szolgáltatásukat. A jelenlegi integrált verzió **1.0.13**.

     * Ha az **oh22 SQLPhonetics.NET összetevőjét választja,** telepítheti az oh22 SQLPhonetics.NET adatminőség/egyezési összetevő [t](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) az Azure-SSIS ir-re, ha beírja a tőlük vásárolt terméklicenckulcsot a **Licenckulcs** mezőbe. A jelenlegi integrált változat **1.0.43**.

     * Ha a **KingswaySoft SSIS-integrációs eszközkészlet-összetevőjét választja,** telepítheti az [SSIS-integrációs eszközkészletcrm/ERP/marketing/együttműködési](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) alkalmazások összekötőit csoportját, például a Microsoft Dynamics/SharePoint/Project Server, oracle/Salesforce Marketing Cloud stb. **License key** A jelenlegi integrált verzió **a 2019.2.**

     * Ha a **KingswaySoft SSIS Productivity Pack összetevőjét választja,** telepítheti a KingswaySoft [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) összetevőinek csomagját az Azure-SSIS IR-re, ha beírja a tőlük vásárolt terméklicenckulcsot a **Licenc kulcsmezőbe.** A jelenlegi integrált verzió **10.0**.

     * Ha a **Theobald Software Xtract IS összetevőjét választja,** telepítheti az [Xtract IS](https://theobald-software.com/en/xtract-is/) csatlakozók at Sap rendszer (ERP, S/4HANA, BW) az Azure-SSIS IR-en az Azure-SSIS IR-on, ha húzza & a tőlük vásárolt terméklicenc-fájl eldobását/feltöltését a **Licenc fájldobozba.** A jelenlegi integrált verzió **6.1.1.3**.

   A hozzáadott expressz egyéni beállítások a **Speciális beállítások** szakaszban jelennek meg. Az eltávolításukhoz jelölje be a jelölőnégyzeteket, majd kattintson a **Törlés gombra.**

   ![Speciális beállítások egyéni beállításokkal](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Amikor beállítja vagy újrakonfigurálja az Azure-SSIS ir-t a PowerShell segítségével, hozzáadhatja vagy eltávolíthatja az egyéni beállításokat a parancsmag futtatásával az `Set-AzDataFactoryV2IntegrationRuntime` Azure-SSIS IR indítása előtt.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   Miután a szabványos egyéni beállítás befejeződött, és az Azure-SSIS IR elindul, a tároló *main.cmd.log* mappájában megtalálhatja a *main.cmd* és más végrehajtási naplók szabványos kimenetét.

1. A szabványos egyéni beállítások néhány mintamegtekintéséhez csatlakozzon a nyilvános előzetes verziótárolóhoz az Azure Storage Explorer használatával.

   a. A **(Helyi és csatolt)** területen kattintson a jobb gombbal **a Tárfiókok**elemre, válassza **a Csatlakozás az Azure storage-hoz**parancsot, válassza **a Kapcsolati karakterlánc vagy a megosztott hozzáférésű aláírás URI-jának használata**parancsot, majd kattintson a Tovább **gombra.**

      ![Csatlakozás az Azure storage-hoz a megosztott hozzáférésű aláírással](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Válassza **a SAS-URI használata lehetőséget,** majd az **URI** mezőbe írja be a következő SAS URI-értéket:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![A tároló megosztott hozzáférési aláírásának biztosítása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Válassza a **Tovább**lehetőséget, majd a **Csatlakozás**lehetőséget.

   d. A bal oldali ablaktáblában jelölje ki a csatlakoztatott **nyilvános előnézeti** tárolót, majd kattintson duplán a *CustomSetupScript* mappára. Ebben a mappában a következő elemek találhatók:

      * A *Minta* mappa, amely egy egyéni telepítőt tartalmaz, amely az Azure-SSIS ir minden csomópontjára telepít egy alapvető feladatot. A feladat nem csinál semmit, de aludni néhány másodpercig. A mappa tartalmaz egy *gacutil* mappát is, amelynek teljes tartalma (*gacutil.exe*, *gacutil.exe.config*és *1033\gacutlrc.dll*) a tárolóba másolható.

      * A *UserScenarios* mappa, amely több egyéni beállítási mintát tartalmaz a valós felhasználói forgatókönyvekből.

        ![A nyilvános előzetes verziótároló tartalma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. A következő elemek megkereséséhez kattintson duplán a *UserScenarios* mappára:

      * A *.* *main.cmd*

      * Egy *BCP* mappa, amely az SQL Server parancssori segédprogramjainak (*MsSqlCmdLnUtils.msi)* telepítéséhez egyéni telepítőparancsfájlt (*main.cmd*) tartalmaz, beleértve a tömeges másolási programot *(bcp)* az Azure-SSIS ir minden csomópontjára.

      * Egy *EXCEL* EXCEL-mappa, amely egy egyéni beállítási parancsfájlt *(main.cmd)* tartalmaz a C# szerelvények és kódtárak telepítéséhez, amelyek segítségével parancsfájlfeladatokban dinamikusan olvashat és írhat Excel-fájlokat az Azure-SSIS IR minden csomópontján. 
      
        Először töltse le [*az ExcelDataReader.dll fájlt*](https://www.nuget.org/packages/ExcelDataReader/) és [*a DocumentFormat.OpenXml.dll fájlt,*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)majd töltse fel őket *a main.cmd* fájllal együtt a tárolóba. Ha csak a szabványos Excel Csatlakozáskezelőt, az Excel-forrást és az Excel-célt szeretné használni, a szükséges hozzáférés-terjeszthető szolgáltatás már előre telepítve van az Azure-SSIS ir-re, így nincs szükség egyéni beállításokra.
      
      * Egy *MYSQL ODBC* mappa, amely egy egyéni beállítási parancsfájlt *(main.cmd)* tartalmaz a MySQL ODBC illesztőprogramok telepítéséhez az Azure-SSIS IR minden csomópontjára. Ez a beállítás lehetővé teszi az ODBC csatlakozáskezelő, a forrás és a cél használatát a MySQL-kiszolgálóhoz való csatlakozáshoz. 
     
        Először [töltse le a MySQL ODBC illesztőprogram-telepítők legújabb 64 és 32 bites verzióit](https://dev.mysql.com/downloads/connector/odbc/) (például a *mysql-connector-odbc-8.0.13-winx64.msi* és *a mysql-connector-odbc-8.0.13-win32.msi),* majd töltse fel őket *a tárolóba.*

      * Egy *ORACLE ENTERPRISE* mappa, amely egy egyéni telepítő parancsfájlt (*main.cmd*) és egy csendes telepítési konfigurációs fájlt (*client.rsp*) tartalmaz az Oracle-összekötők és OCI-illesztőprogram telepítéséhez az Azure-SSIS IR Enterprise Edition minden csomópontjára. Ez a beállítás lehetővé teszi az Oracle Csatlakozáskezelő, a forrás és a célrendszer használatát az Oracle kiszolgálóhoz való csatlakozáshoz. 
      
        Először töltse le a Microsoft Connectors v5.0-t az Oracle-hez (*AttunitySSISOraAdaptersSetup.msi* és *AttunitySSIsOraAdaptersSetup64.msi*) a [Microsoft Download Centerből](https://www.microsoft.com/en-us/download/details.aspx?id=55179) és a legújabb Oracle klienst (például *winx64_12102_client.zip)* az [Oracle-ből,](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)majd töltse fel őket a *main.cmd* és *client.rsp* fájllal együtt a tárolóba. Ha a TNS segítségével csatlakozik az Oracle-hez, akkor le kell töltenie *a tnsnames.ora*fájlt is, szerkesztenie kell, és fel kell töltenie a tárolóba, hogy a telepítés során átmásolható legyen az Oracle telepítési mappájába.

      * Egy *ORACLE STANDARD ADO.NET* mappa, amely egy egyéni beállítási parancsfájlt *(main.cmd)* tartalmaz az Oracle ODP.NET illesztőprogram nak az Azure-SSIS IR minden csomópontjára történő telepítéséhez. Ez a beállítás lehetővé teszi, hogy a ADO.NET Csatlakozáskezelő, forrás és cél segítségével csatlakozzon az Oracle kiszolgálóhoz. 
      
        Először [töltse le a legújabb Oracle ODP.NET illesztőprogramot](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (például AZ *ODP.NET_Managed_ODAC122cR1.zip*fájlt), majd töltse fel *a fő.cmd* fájllal együtt a tárolóba.
       
      * Egy *ORACLE STANDARD ODBC* mappa, amely egy egyéni beállítási parancsfájlt *(main.cmd)* tartalmaz az Oracle ODBC illesztőprogram telepítéséhez és az adatforrás név (DSN) konfigurálásához az Azure-SSIS ir minden csomópontján. Ez a beállítás lehetővé teszi az ODBC Csatlakozáskezelő, a forrás és a cél, illetve az ODBC adatforrás-típussal rendelkező Power Query csatlakozáskezelő és -forrás használatát az Oracle kiszolgálóhoz való csatlakozáshoz. 
      
        Először töltse le a legújabb Oracle Instant Client (Basic Package or Basic Lite Package) és ODBC csomagot, majd töltse fel őket *a main.cmd-vel* együtt a tárolóba:
        * [64 bites csomagok letöltése](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Alapcsomag: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic Lite csomag: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC csomag: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Letöltés 32 bites csomagok](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic Package: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic Lite csomag: *instantclient-basiclite-nt-18.3.0.0.0.0dbru.zip*; ODBC csomag: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Oracle *STANDARD OLEDB* mappa, amely egy egyéni beállítási parancsfájlt *(main.cmd)* tartalmaz az Oracle OLEDB illesztőprogram telepítéséhez az Azure-SSIS IR minden csomópontjára. Ez a beállítás lehetővé teszi az OLEDB csatlakozáskezelő, a forrás és a cél használatát az Oracle kiszolgálóhoz való csatlakozáshoz. 
     
        Először [töltse le a legújabb Oracle OLEDB illesztőprogramot](https://www.oracle.com/partners/campaign/index-090165.html) (például *ODAC122010Xcopy_x64.zip),* majd töltse fel *a main.cmd* fájllal együtt a tárolóba.

      * Egy *POSTGRESQL ODBC* mappa, amely egy egyéni beállítási parancsfájlt *(main.cmd)* tartalmaz a PostgreSQL ODBC illesztőprogramok telepítéséhez az Azure-SSIS IR minden csomópontjára. Ez a beállítás lehetővé teszi az ODBC csatlakozáskezelő, a forrás és a cél használatát a PostgreSQL kiszolgálóhoz való csatlakozáshoz. 
     
        Először [töltse le a PostgreSQL ODBC illesztőprogram-telepítők legújabb 64 és 32 bites verzióit](https://www.postgresql.org/ftp/odbc/versions/msi/) (például *psqlodbc_x64.msi* és *psqlodbc_x86.msi),* majd töltse fel őket *a main.cmd* fájllal együtt a tárolóba.

      * Az *SAP BW* mappa, amely egy egyéni telepítő parancsfájlt (*main.cmd*) tartalmaz az SAP .NET összekötő szerelvény (*librfc32.dll*) telepítéséhez az Azure-SSIS IR Enterprise Edition minden csomópontjára. Ez a beállítás lehetővé teszi az SAP Business Warehouse (BW) Csatlakozáskezelő, forrás és cél használatával az SAP BW-kiszolgálóhoz való csatlakozást. 
      
        Először töltse fel a *librfc32.dll* 64 bites vagy 32 bites verzióját az SAP telepítési mappából a *main.cmd* fájllal együtt a tárolóba. A parancsfájl ezután átmásolja az SAP-szerelvényt a *%windir%\SysWow64* vagy *%windir%\System32* mappába a telepítés során.

      * Egy *STORAGE* STORAGE-mappa, amely egy egyéni beállítási parancsfájlt *(main.cmd)* tartalmaz az Azure PowerShell telepítéséhez az Azure-SSIS ir minden csomópontján. Ez a beállítás lehetővé teszi a PowerShell-parancsfájlokat futtató SSIS-csomagok telepítését és [futtatását az Azure storage-fiók kezeléséhez.](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell) 
      
        Másolja *a main.cmd-t*, egy minta *AzurePowerShell.msi* (vagy a legújabb verziót használja) és *storage.ps1* a tárolóba. Használja *a PowerShell.dtsx-et* a csomagok sablonjaként. A csomagsablon egy [Azure Blob-letöltési feladatot](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)egyesít, amely a *storage.ps1-et* módosítható PowerShell-parancsfájlként, valamint egy [Végrehajtási folyamat feladatot](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)kombinál, amely minden csomóponton végrehajtja a parancsfájlt.

      * Egy *TERADATA* mappa, amely egyéni telepítőparancsfájlt (*main.cmd*), a hozzá tartozó fájlt (*install.cmd*) és telepítőcsomagokat (*.msi*) tartalmazza. Ezek a fájlok telepítik a Teradata-összekötők, a Teradata párhuzamos átvitel (TPT) API-t és az ODBC-illesztőprogramot az Azure-SSIS IR Enterprise Edition minden csomópontján. Ez a beállítás lehetővé teszi a Teradata Connection Manager, a forrás és a cél használatát a Teradata-kiszolgálóhoz való csatlakozáshoz. 
      
        Először [töltse le a Teradata Tools and Utilities 15.x zip fájlt](http://partnerintelligence.teradata.com) (például *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* majd töltse fel a korábban említett *.cmd* és *.msi* fájlokkal együtt a tárolóba.

      * Egy *TLS 1.2-es* mappa, amely egy egyéni beállítási parancsfájlt (*main.cmd)* tartalmaz az erős titkosítási/biztonságosabb hálózati protokoll (TLS 1.2) használatához és a régebbi SSL/TLS-verziók letiltásához az Azure-SSIS IR minden csomópontján.

      * Egy *ZULU OPENJDK* mappa, amely egy egyéni beállítási parancsfájlt (*main.cmd)* és PowerShell-fájlt (*install_openjdk.ps1*) tartalmaz a Zulu OpenJDK telepítéséhez az Azure-SSIS IR minden csomópontjára. Ez a beállítás lehetővé teszi az Azure Data Lake Store és a Rugalmas fájlösszekötők használatát az ORC és a Parketta fájlok feldolgozásához. További információt az [Azure Feature Pack for Integration Services című témakörben talál.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) 
      
        Először [töltse le a legújabb Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (például *zulu8.33.0.1-jdk8.0.192-win_x64.zip),* majd töltse fel együtt *main.cmd* és *install_openjdk.ps1* a tartályba.

        ![Mappák a felhasználói forgatókönyvek mappájában](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Az egyéni beállítási minták kipróbálásához másolja a tartalmat a kijelölt mappából a tárolóba.
   
      Amikor beállítja vagy újrakonfigurálja az Azure-SSIS ir-t a Data Factory felhasználói felületén, jelölje be az **Azure-SSIS-integrációs futásidő testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet a **Speciális beállítások** szakaszban, majd adja meg a tároló SAS URI-ját az **Egyéni beállítási tároló SAS** URI-jába.
   
      Amikor beállítja vagy újrakonfigurálja az Azure-SSIS `Set-AzDataFactoryV2IntegrationRuntime` ir-t a PowerShell segítségével, futtassa `SetupScriptContainerSasUri` a parancsmast a tároló SAS URI-jával a paraméter értékeként.

## <a name="next-steps"></a>További lépések

- [Az Azure-SSIS-integrációs futásidejű Enterprise Edition beállítása](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Fizetős vagy licencelt egyéni összetevők fejlesztése az Azure-SSIS integrációs futásórához](how-to-develop-azure-ssis-ir-licensed-components.md)
