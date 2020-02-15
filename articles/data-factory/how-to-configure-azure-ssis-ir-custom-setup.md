---
title: Azure-SSIS Integration Runtime beállításainak testreszabása
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure-SSIS Integration Runtime egyéni telepítési felülete további összetevők telepítéséhez vagy a beállítások módosításához
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
ms.date: 02/14/2020
ms.openlocfilehash: 9c084564fec3faf59317fe9e05f3e850a38454d6
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251974"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime beállításainak testreszabása

Az Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) egyéni beállítása egy felületet biztosít a saját lépéseinek hozzáadásához a Azure-SSIS IR telepítése vagy újrakonfigurálása során. 

Az egyéni telepítéssel megváltoztathatja az alapértelmezett működési konfigurációt vagy környezetet, például megkezdheti a további Windows-szolgáltatások használatát, vagy megtarthatja a fájlmegosztás hozzáférési hitelesítő adatait. További összetevőket, például szerelvényeket, illesztőprogramokat vagy bővítményeket is telepíthet a Azure-SSIS IR minden egyes csomópontján.

A Azure-SSIS IR kétféleképpen végezheti el az egyéni telepítéseket: 
* **Expressz egyéni beállítás parancsfájl nélkül**: Futtasson néhány gyakori rendszerkonfigurációt és Windows-parancsot, vagy telepítsen néhány népszerű vagy ajánlott további összetevőt a parancsfájlok használata nélkül.
* **Standard egyéni telepítő parancsfájl**használatával: Készítse elő a parancsfájlt és a hozzá tartozó fájlokat, és töltse fel őket az Azure Storage-fiókban található blob-tárolóba. Ezután meg kell adnia egy közös hozzáférési aláírás (SAS) Uniform Resource Identifier (URI) a tárolóhoz a Azure-SSIS IR beállításakor vagy újrakonfigurálásakor. A Azure-SSIS IR mindegyik csomópontja letölti a parancsfájlt és a hozzá tartozó fájlokat a tárolóból, és az Egyéni telepítést emelt szintű engedélyekkel futtatja. Ha az egyéni telepítés elkészült, minden egyes csomópont feltölti a végrehajtás és más naplók szabványos kimenetét a tárolóba.

Az ingyenes, a licenc nélküli összetevőket és a fizetős licenceket is telepítheti expressz és standard szintű egyéni telepítéssel. Ha Ön független szoftvergyártó (ISV), tekintse meg a [fizetett vagy licencelt összetevők fejlesztése egy Azure-SSIS IR számára](how-to-develop-azure-ssis-ir-licensed-components.md)című témakört.

> [!IMPORTANT]
> Mivel az Azure-SSIS IR v2 sorozatú csomópontjai nem alkalmasak az egyéni telepítéshez, inkább a v3 sorozatú csomópontokat használják. Ha már használja a v2 sorozatú csomópontokat, a lehető leghamarabb váltson a v3 sorozatú csomópontokra.

## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások érvényesek kizárólag a szabványos egyéni telepítésekre:

- Ha a *Gacutil. exe* fájllal szeretné telepíteni a szerelvényeket a globális szerelvény-gyorsítótárban (GAC), akkor az egyéni telepítés részeként meg kell adnia a *Gacutil. exe fájlt* . Vagy használhatja a *nyilvános előzetes* tárolóban megadott másolatot, amelyet később az "utasítások" szakaszban ismertetünk.

- Ha a parancsfájl egyik almappájára szeretne hivatkozni, az *msiexec. exe* nem támogatja a `.\` jelölést a gyökérmappa hivatkozására. `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`helyett olyan parancsot használjon, mint például a `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`.

- A Windows által automatikusan létrehozott rendszergazdai megosztások vagy rejtett hálózati megosztások jelenleg nem támogatottak a Azure-SSIS IR.

- Az IBM iSeries Access ODBC-illesztő nem támogatott a Azure-SSIS IR. Előfordulhat, hogy az egyéni telepítés során telepítési hibák léptek fel. Ha ezt teszi, kérjen segítséget az IBM támogatási szolgálatának.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Azure-SSIS IR testreszabásához a következő elemek szükségesek:

- [Azure-előfizetés](https://azure.microsoft.com/)

- [A Azure-SSIS IR kiépítése](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Egy Azure Storage-fiók](https://azure.microsoft.com/services/storage/). Az expressz egyéni telepítésekhez nem szükséges. A standard szintű egyéni telepítésekhez feltöltheti és tárolhatja az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat egy blob-tárolóban. Az egyéni telepítési folyamat a végrehajtási naplókat is feltölti ugyanarra a blob-tárolóra.

## <a name="instructions"></a>Utasítások

1. Ha a PowerShell-lel szeretné beállítani vagy újrakonfigurálni a Azure-SSIS IRt, töltse le és telepítse a [Azure PowerShellt](/powershell/azure/install-az-ps). Expressz egyéni telepítések esetén ugorjon a 4. lépésre.

1. Készítse elő az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat (például. bat,. cmd,. exe,. dll,. msi vagy. ps1 fájlok).

   * Rendelkeznie kell egy *Main. cmd*nevű parancsfájl-fájllal, amely az egyéni telepítés belépési pontja.  
   * Annak érdekében, hogy a parancsfájlt csendesen lehessen végrehajtani, javasoljuk, hogy először tesztelje a helyi gépen.  
   * Ha más eszközök (például *msiexec. exe*) által generált további naplókat szeretne feltölteni a tárolóba, adja meg az előre definiált környezeti változót, `CUSTOM_SETUP_SCRIPT_LOG_DIR`a parancsfájlok naplófájljában (például *msiexec/i xxx. msi/quiet/lv% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. [Azure Storage Explorer](https://storageexplorer.com/)letöltése, telepítése és megnyitása. Ehhez tegye a következőket:

   a. A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok**elemre, majd válassza **a Kapcsolódás az Azure Storage-hoz**lehetőséget.

      ![Kapcsolódás az Azure Storage-hoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Válassza **a Storage-fiók nevének és kulcsának használata**lehetőséget, majd kattintson a **tovább**gombra.

      ![Tárfióknév és -kulcs használata](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Adja meg az Azure Storage-fiók nevét és kulcsát, válassza a **tovább**, majd a **kapcsolat**lehetőséget.

      ![Adja meg a Storage-fiók nevét és kulcsát](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. A csatlakoztatott Azure Storage-fiókban kattintson a jobb gombbal a **blob-tárolók**elemre, válassza a **blob-tároló létrehozása**elemet, és nevezze el az új tárolót.

      ![Blobtároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Válassza ki az új tárolót, és töltse fel az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat. Ügyeljen arra, hogy a *Main. cmd* fájlt a tároló legfelső szintjén töltse fel, nem pedig egyetlen mappában sem. Győződjön meg arról is, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza, így a Azure-SSIS IR későbbi letöltés nem hosszabb időt is igénybe veheti. Az egyéni beállítások maximális időtartama jelenleg 45 percen belül megtörténik, mielőtt időtúllépés történik. Ide tartozik az összes fájl letöltésének ideje a tárolóból, és a Azure-SSIS IR telepítése. Ha a telepítőnek több időre van szüksége, egy támogatási jegyet kell felhívnia.

      ![Fájlok feltöltése a blob-tárolóba](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférési aláírás beolvasása**elemet.

      ![A tároló közös hozzáférési aláírásának beolvasása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Hozza létre a tároló SAS URI-JÁT elég hosszú lejárati idővel és olvasási/írási/listázási engedéllyel. Az egyéni telepítési parancsfájl és a hozzá tartozó fájlok letöltéséhez és futtatásához szükség van az SAS URI-ra, amikor a Azure-SSIS IR bármely csomópontja rendszerképe vagy újraindítása megtörténik. Írási engedéllyel kell rendelkeznie a telepítési végrehajtási naplók feltöltéséhez.

      > [!IMPORTANT]
      > Győződjön meg arról, hogy a SAS URI-ja nem jár le, és az egyéni beállítási erőforrások mindig elérhetők a Azure-SSIS IR teljes életciklusa során, a létrehozástól a törlésig, különösen akkor, ha az adott időszakban rendszeresen leállítja és elindítja a Azure-SSIS IR.

      ![A tároló közös hozzáférési aláírásának előállítása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Másolja és mentse a tároló SAS URI-JÁT.

      ![A megosztott hozzáférés aláírásának másolása és mentése](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Amikor beállítja vagy újrakonfigurálja a Azure-SSIS IR egy adatfeldolgozó felhasználói felülettel, hozzáadhat vagy eltávolíthat egyéni beállításokat úgy, hogy az **integrációs modul telepítése** panel **Speciális beállítások** szakaszában bejelöli a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet. 

   Ha szabványos egyéni beállításokat szeretne felvenni, adja meg a tároló SAS URI-JÁT az **egyéni telepítési tároló sas URI-ja** mezőben. 
   
   Ha expressz egyéni beállításokat szeretne hozzáadni, válassza az **új** lehetőséget az **expressz egyéni telepítés hozzáadása** ablaktábla megnyitásához, majd válasszon egy típust az **expressz egyéni telepítés típusa** legördülő listában:

   * Ha a **Futtatás cmdkey parancs** típusát választja, megtarthatja a fájlmegosztás vagy a Azure Files megosztások hozzáférési hitelesítő adatait Azure-SSIS IR a megjelenő számítógép nevének vagy tartománynevének, a fiók nevének vagy felhasználónevének, valamint a fiók kulcsának vagy jelszavának megadásával az **/Add**, **/User**és **/pass** mezőkben. Ez hasonló a Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) parancs helyi gépen való futtatásához.
   
   * Ha a **környezeti változó hozzáadása** lehetőséget választja, akkor a Azure-SSIS IR futtatott csomagjaiban a környezeti változó nevének és értékének megadásával adhat hozzá Windows környezeti változókat a változók **neve** és **változó érték** mezőibe. Ez hasonló a Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) parancs helyi számítógépen való futtatásához.

   * Ha bejelöli a **licencelt összetevő** típusának telepítése lehetőséget, az **összetevő neve** legördülő listában kiválaszthatja az ISV-partnereinkből származó integrált összetevőt:

     * Ha kijelöli a **szoftverével Task Factory** összetevőjét, telepítheti az összetevők szoftverével [-csomagját](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) a Azure-SSIS IR. Ehhez írja be a **licenc kulcs** mezőjében megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **2019.4.3**.

     * Ha a **OH22'S HEDDA választja. I/o** -összetevő, telepítheti a [HEDDA. ](https://hedda.io/ssis-component/)A szolgáltatás megvásárlása után IO-adatminőség/-tisztító összetevő a Azure-SSIS IR oh22. Az aktuálisan integrált verzió a **1.0.13**.

     * Ha kijelöli a **oh22's SQLPhonetics.net** összetevőt, a [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) adatminőség/megfelelő összetevőjét az oh22-ből telepítheti a Azure-SSIS IR a **licenckulcs** mezőben megvásárolt licenckulcs megadásával. Az aktuálisan integrált verzió a **1.0.43**.

     * Ha a **KINGSWAYSOFT SSIS Integration Toolkit** összetevőjét választja, a [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) -csomagot telepítheti a CRM/ERP/marketing/csoportmunka-alkalmazások, például a Microsoft Dynamics/SharePoint/Project Server, az Oracle/Salesforce marketing-felhő stb Azure-SSIS IR. számára, és a **licenc kulcs** mezőjébe írja be a számukra megvásárolt termékkulcsot. Az aktuálisan integrált verzió a **2019,2**.

     * Ha a **KINGSWAYSOFT SSIS Productivity Pack** összetevőjét választja, akkor a [SSIS hatékonyságnövelő csomagját](https://www.kingswaysoft.com/products/ssis-productivity-pack) telepítheti a Azure-SSIS IR a KingswaySoft-ből a **licenc kulcs** mezőjébe, ha beírja a tőlük vásárolt termékkulcsot. Az aktuálisan integrált verzió a **10,0**.

   A hozzáadott expressz egyéni telepítések a **Speciális beállítások** szakaszban fognak megjelenni. Ha el szeretné távolítani őket, jelölje be a jelölőnégyzeteket, majd válassza a **Törlés**lehetőséget.

   ![Speciális beállítások egyéni telepítésekkel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Amikor beállítja vagy újrakonfigurálja a Azure-SSIS IR a PowerShell-lel, a Azure-SSIS IR elindítása előtt a `Set-AzDataFactoryV2IntegrationRuntime` parancsmag futtatásával adhatja hozzá vagy távolíthatja el az egyéni beállításokat.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
   
   A normál egyéni telepítés befejezése és a Azure-SSIS IR elindítása után megkeresheti a *Main.* cmd és más végrehajtási naplók standard kimenetét a Storage-tároló *fő. cmd. log* mappájába.

1. A standard egyéni beállítások egyes mintáinak megtekintéséhez Azure Storage Explorer használatával csatlakozhat a nyilvános előzetes verziójú tárolóhoz.

   a. A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok**elemre, válassza a **Kapcsolódás az Azure Storage-hoz**lehetőséget, válassza **a kapcsolati karakterlánc vagy a közös hozzáférési aláírás URI-ja**lehetőséget, majd kattintson a **tovább**gombra.

      ![Kapcsolódás az Azure Storage-hoz a közös hozzáférési aláírással](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Válassza a **sas URI használata** lehetőséget, majd az **URI** mezőbe írja be a következő sas URI-t:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![A tároló közös hozzáférési aláírásának megadása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Válassza a **tovább**, majd a **kapcsolat**lehetőséget.

   d. A bal oldali ablaktáblán válassza ki a csatlakoztatott **publicpreview** -tárolót, majd kattintson duplán a *CustomSetupScript* mappára. Ebben a mappában a következő elemek találhatók:

      * Egy *minta* mappa, amely egy egyéni telepítőt tartalmaz egy alapszintű feladat telepítéséhez a Azure-SSIS IR egyes csomópontjain. A feladat nem csinál semmit, de aludni néhány másodpercig. A mappa egy *Gacutil* mappát is tartalmaz, amelynek teljes tartalma (*Gacutil. exe*, *Gacutil. exe. config*és *1033 \ gacutlrc. dll*) a tárolóba másolható.

      * Egy *UserScenarios* mappa, amely a valós felhasználói forgatókönyvekben számos egyéni telepítési mintát tartalmaz.

        ![A nyilvános előzetes verziójú tároló tartalma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. A következő elemek megkereséséhez kattintson duplán a *UserScenarios* mappára:

      * A *.NET-keretrendszer 3,5* mappája, amely egy egyéni telepítőt tartalmaz a .NET-keretrendszer egy korábbi verziójának telepítéséhez, amelyre szükség lehet az egyéni összetevőkhöz a Azure-SSIS IR egyes csomópontjain.

      * Egy *BCP* -mappa, amely egy egyéni telepítőt tartalmaz SQL Server parancssori segédeszközök (*MsSqlCmdLnUtils. msi*) telepítéséhez, beleértve a tömeges másolási programot (*BCP*) a Azure-SSIS IR egyes csomópontjain.

      * Egy *Excel* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz C# olyan szerelvények és könyvtárak telepítéséhez, amelyeket parancsfájl-feladatokban használhat a Azure-SSIS IR összes csomópontján lévő Excel-fájlok dinamikus olvasásához és írásához. 
      
        Először töltse le a [*ExcelDataReader. dll*](https://www.nuget.org/packages/ExcelDataReader/) és a [*DocumentFormat. OpenXml. dll fájlt*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba. Ha csak a szabványos Excel-kapcsolatkezelő, az Excel-forrás és az Excel-célhelyet szeretné használni, a szükséges hozzáférés-Újraterjeszthető csomag már telepítve van a Azure-SSIS IR, így nincs szükség egyéni telepítésre.
      
      * Egy *MySQL ODBC* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a MySQL ODBC-illesztőprogramok telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ezzel a beállítással a MySQL-kiszolgálóhoz való kapcsolódáshoz használhatja az ODBC-kapcsolatkezelő, a forrás és a cél lehetőséget. 
     
        Először [töltse le a MySQL ODBC illesztőprogram-telepítők legújabb 64 bites és 32 bites verzióit](https://dev.mysql.com/downloads/connector/odbc/) (például *MySQL-Connector-ODBC-8.0.13-Winx64. msi* és *MySQL-Connector-ODBC-8.0.13-Win32. msi*), majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba.

      * Egy *Oracle Enterprise* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) és csendes telepítési konfigurációs fájlt (*Client. RSP*) tartalmaz az Oracle-összekötők és a OCI-illesztőprogram telepítéséhez a Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy az Oracle-Csatlakozáskezelő, a forrás és a cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. 
      
        Először töltse le az Oracle-hez készült Microsoft Connectors v 5.0-s verzióját (*AttunitySSISOraAdaptersSetup. msi* és *AttunitySSISOraAdaptersSetup64. msi*) a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=55179) és a legújabb oracle-ügyfélprogramból (például *winx64_12102_client. zip*) az [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)-ből, majd töltse fel őket mind a *Main. cmd* , mind a *Client. RSP* fájllal a tárolóba. Ha a TNS használatával csatlakozik az Oracle-hez, le kell töltenie a *tnsnames. ora*fájlt, szerkesztenie kell, és fel kell töltenie a tárolóba, hogy a telepítés során átmásolható legyen az Oracle telepítési mappájába.

      * Egy *Oracle STANDARD ADO.net* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az Oracle ODP.net-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a ADO.NET Csatlakozáskezelő, forrás és cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. 
      
        Először [töltse le a legújabb Oracle ODP.net-illesztőprogramot](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (például *ODP. NET_Managed_ODAC122cR1. zip*), majd töltse fel a *Main. cmd fájllal* együtt a tárolóba.
       
      * *Oracle standard ODBC* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az Oracle ODBC-illesztőprogram telepítéséhez és az adatforrás nevének (DSN) konfigurálásához a Azure-SSIS IR mindegyik csomópontján. Ezzel a beállítással az ODBC-kapcsolatkezelő, a forrás és a cél, illetve a Power Query Csatlakozáskezelő és a forrás használható az Oracle-kiszolgálóhoz való kapcsolódáshoz az ODBC-adatforrás típusával. 
      
        Először töltse le a legújabb Oracle Instant-ügyfelet (alapszintű csomag vagy alapszintű Lite-csomag) és az ODBC-csomagot, majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba:
        * [64 bites csomagok letöltése](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (alapszintű csomag: *instantclient-Basic-Windows. x64-18.3.0.0.0 dbru. zip*; Alapszintű Lite-csomag: *instantclient-basiclite-Windows. x64-18.3.0.0.0 dbru. zip*; ODBC-csomag: *instantclient-ODBC-Windows. x64-18.3.0.0.0 dbru. zip*) 
        * [32 bites csomagok letöltése](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (alapszintű csomag: *instantclient-Basic-NT-18.3.0.0.0 dbru. zip*; Alapszintű Lite-csomag: *instantclient-basiclite-NT-18.3.0.0.0 dbru. zip*; ODBC-csomag: *instantclient-ODBC-NT-18.3.0.0.0 dbru. zip*)

      * Egy *Oracle standard OLEDB* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az Oracle OLEDB-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy az OLEDB Csatlakozáskezelő, a forrás és a cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. 
     
        Először [töltse le a legújabb Oracle OLEDB-illesztőprogramot](https://www.oracle.com/partners/campaign/index-090165.html) (például *ODAC122010Xcopy_x64. zip*), majd töltse fel a *Main. cmd fájllal* együtt a tárolóba.

      * Egy *POSTGRESQL ODBC* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a PostgreSQL ODBC-illesztőprogramok telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a PostgreSQL-kiszolgálóhoz való kapcsolódáshoz az ODBC-Csatlakozáskezelő, a forrás és a cél használatával kapcsolódjon. 
     
        Először [töltse le a POSTGRESQL ODBC illesztőprogram-telepítők legújabb 64 bites és 32 bites verzióit](https://www.postgresql.org/ftp/odbc/versions/msi/) (például *psqlodbc_x64. msi* és *psqlodbc_x86. msi*), majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba.

      * Egy *SAP BW* mappát, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az SAP .net-összekötő szerelvény (*librfc32. dll*) telepítéséhez a Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ezzel a beállítással az SAP Business Warehouse (BW) Csatlakozáskezelő, a forrás és a cél használatával csatlakozhat a SAP BW-kiszolgálóhoz. 
      
        Először töltse fel a *librfc32. dll* 64-bites vagy 32-bites verzióját az SAP telepítési mappájából a *Main. cmd fájllal* együtt a tárolóba. A szkript ezután átmásolja az SAP-szerelvényt a *%windir%\syswow64 mappában* vagy a *%windir%\System32* mappába a telepítés során.

      * Egy *tárolási* mappa, amely egy egyéni telepítőt tartalmaz a Azure PowerShell telepítéséhez a Azure-SSIS IR egyes csomópontjain. Ez a beállítás lehetővé teszi, hogy [Az Azure Storage-fiók kezeléséhez PowerShell-parancsfájlokat](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)futtató SSIS-csomagokat telepítsen és futtasson. 
      
        Másolja a *Main. cmd*fájlt, egy mintát a *AzurePowerShell. msi fájlra* (vagy használja a legújabb verziót) és a *Storage. ps1* fájlt a tárolóba. A *PowerShell. dtsx* sablonként használhatja a csomagokat. A Package sablon kombinálja az [Azure Blob letöltési feladatát](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), amely a *Storage. ps1* fájl letöltését módosítható PowerShell-parancsfájlként tölti le, valamint egy [végrehajtási folyamat feladatot](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), amely végrehajtja a parancsfájlt az egyes csomópontokon.

      * Egy *TERADATA* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*), a hozzá tartozó fájlt (*install. cmd*) és a telepítő csomagokat ( *. msi*) tartalmaz. Ezek a fájlok telepítik a Teradata-összekötőket, a Teradata párhuzamos Transporter (TPT) API-t és az ODBC-illesztőt a Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a Teradata Csatlakozáskezelő, forrás és cél használatával kapcsolódjon a Teradata-kiszolgálóhoz. 
      
        Először [töltse le a Teradata-eszközök és-segédprogramok 15. x zip-fájlját](http://partnerintelligence.teradata.com) (például *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00. zip*), majd töltse fel a tárolóba a korábban említett *. cmd* és *. msi* fájlokkal együtt.

      * Egy *ZULU OPENJDK* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) és egy PowerShell-fájlt (*install_openjdk. ps1*) tartalmaz a Zulu-OPENJDK telepítéséhez a Azure-SSIS IR egyes csomópontjain. Ez a beállítás lehetővé teszi, hogy Azure Data Lake Store és rugalmas fájl-összekötőket használjon az ork és a Parquet fájlok feldolgozásához. További információ: [Az Azure Feature Pack az integrációs szolgáltatásokhoz](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Először [töltse le a legújabb Zulu-OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (például: *Zulu 8.33.0.1-jdk 8.0.192-win_x64. zip*), majd töltse fel a *main. cmd* és a *install_openjdk. ps1* fájllal együtt a tárolóba.

        ![A felhasználói forgatókönyvek mappában található mappák](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Az egyéni telepítési minták kipróbálásához másolja a tartalmat a kiválasztott mappából a tárolóba.
   
      Ha a Data Factory felhasználói felülettel állítja be vagy konfigurálja újra a Azure-SSIS IRt, jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációk/összetevő-telepítések** esetén jelölőnégyzetet a **Speciális beállítások** szakaszban, majd adja meg a tároló sas URI-ját az **egyéni telepítési tároló sas URI-ja** mezőben.
   
      Amikor beállítja vagy újrakonfigurálja a Azure-SSIS IR a PowerShell-lel, futtassa a `Set-AzDataFactoryV2IntegrationRuntime` parancsmagot a tároló SAS URI-JAként `SetupScriptContainerSasUri` paraméter értékeként.

## <a name="next-steps"></a>Következő lépések

- [A Azure-SSIS Integration Runtime Enterprise kiadásának beállítása](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Fizetett vagy licencelt egyéni összetevők fejlesztése a Azure-SSIS Integration Runtime számára](how-to-develop-azure-ssis-ir-licensed-components.md)
