---
title: A telepítő az Azure-SSIS integrációs modul testreszabása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan használhatja az egyéni telepítés felületet, az Azure-SSIS integrációs modul további összetevők telepítéséhez, vagy a beállítások módosítása
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2edaea1cfb02b250b27c47d58b6c1d1ef6501480
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420268"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>A telepítő az Azure-SSIS integrációs modul testreszabása

Az egyéni telepítés felület az Azure-SSIS integrációs modul saját telepítő lépések hozzáadása a kiépítés vagy újrakonfigurálása az Azure-SSIS integrációs modult. felületet biztosít. Egyéni telepítés lehetővé teszi az alapértelmezett konfiguráció vagy a környezetben (például további Windows szolgáltatások elindítása vagy maradnak a fájlmegosztások hozzáférési hitelesítő adatok) működő alter vagy telepítenek további összetevőket (például szerelvényeket, illesztőprogramok vagy bővítmények) minden egyes csomópontján az Azure-SSIS integrációs modult.

Az egyéni telepítés előkészítése egy parancsfájlt, és az ahhoz tartozó fájlokat, és feltölti őket az Azure Storage-fiókban blobtárolóba konfigurálásához. Egy közös hozzáférésű Jogosultságkód (SAS) egységes erőforrás-azonosító (URI) a tároló adnia, amikor üzembe helyezése, vagy konfigurálja újra az Azure-SSIS integrációs modult. Egyes csomópontok az Azure-SSIS integrációs modul helyének ezután a parancsfájl, és az ahhoz tartozó fájlokat tölt le a tároló és az egyéni telepítés fut az emelt szintű jogosultságokkal. Ha egyéni telepítés befejeződött, minden egyes csomópont a tárolóba tölt fel a normál a kimenetbe végrehajtása és a többi naplófájlt.

Ingyenes vagy a nem licencelt összetevőinek és fizetős vagy licenccel rendelkező összetevők telepítése. Ha Ön független Szoftverszállító, lásd: [, hogyan hozhat létre a fizetős verzióra, vagy rendelkezik licenccel az Azure-SSIS integrációs összetevők](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Aktuális korlátozások

-   Ha a használni kívánt `gacutil.exe` szerelvényeket a globális szerelvény gyorsítótárban (GAC) a telepítéséhez meg kell adnia `gacutil.exe` az egyéni telepítés, vagy használja a nyilvános előzetes verzióban a tárolóban biztosított másolat részeként.

-   Ha szeretne hivatkozni egy almappát a szkriptben `msiexec.exe` nem támogatja a `.\` jelöléssel való hivatkozáshoz a gyökérmappa. Használjon hasonló parancsot `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` helyett `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Ha egyéni telepítés az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat van szüksége, csak az Azure Resource Managerbeli virtuális hálózat használata támogatott. Klasszikus virtuális hálózat nem támogatott.

-   Rendszergazdai megosztás jelenleg nem támogatott az Azure-SSIS integrációs modult.

## <a name="prerequisites"></a>Előfeltételek

Testre szabhatja az Azure-SSIS integrációs modul, a következőkre van szükség:

-   [Azure-előfizetés](https://azure.microsoft.com/)

-   [Az Azure SQL Database- vagy Felügyeltpéldány-kiszolgáló](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Az Azure-SSIS integrációs modul üzembe helyezése](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Azure Storage-fiók](https://azure.microsoft.com/services/storage/). Egyéni telepítő feltöltése és a egy blobtárolót a egyéni telepítési parancsfájlt, és az ahhoz tartozó fájlokat tárolja. Az egyéni telepítési folyamat is feltölt a feladatvégrehajtási naplók blob-tárolóban.

## <a name="instructions"></a>Utasítások

1.  Töltse le és telepítse [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (5.4 vagy újabb verzió).

1.  Készítse elő a egyéni telepítési parancsfájlt, és az ahhoz tartozó fájlokat (például, .bat, .cmd, .exe, .dll, .msi vagy .ps1 fájlok).

    1.  Rendelkeznie kell egy parancsfájlt nevű `main.cmd`, amely az egyéni telepítés belépési pontja.

    1.  Ha azt szeretné, hogy más eszközök által létrehozott további naplók (például `msiexec.exe`) kell feltölteni a tárolóba, adja meg az előre definiált környezeti változót `CUSTOM_SETUP_SCRIPT_LOG_DIR` , a parancsfájlok a mappájához (például `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1.  Töltse le, telepítse és indítsa el a [Azure Storage Explorer](http://storageexplorer.com/).

    1.  A **(helyi és csatolt)**, a jobb gombbal válassza **Tárfiókok** válassza **csatlakozás az Azure storage**.

       ![Csatlakozás az Azure Storage-hoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    1.  Válassza ki **tárfiók nevének és kulcsának** válassza **tovább**.

       ![Tárfióknév és -kulcs használata](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    1.  Adja meg az Azure Storage-fióknevet és kulcsot, majd válassza **tovább**, majd válassza ki **Connect**.

       ![Adja meg a tároló nevének és kulcsának](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    1.  A csatlakoztatott Azure Storage-fiók alatt kattintson a jobb gombbal a **Blobtárolók**, jelölje be **Blobtároló létrehozása**, és nevezze el az új tárolóhoz.

       ![Blobtároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    1.  Jelölje ki az új tárolót, és töltse fel az egyéni telepítési parancsfájl és az ahhoz tartozó fájlokat. Győződjön meg arról, hogy feltöltött `main.cmd` a legfelső szinten, a tároló nem bármelyik mappájában. 

       ![Fájlok feltöltése a blob-tárolóba](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    1.  Kattintson a jobb gombbal a tárolóra, és válassza ki **közös hozzáférési jogosultságkód igénylése**.

       ![A tároló közös hozzáférésű Jogosultságkód beolvasása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    1.  A tároló SAS URI létrehozása egy elég hosszú lejárati idejét, és az írási + Írás + engedélyek listázása. Szüksége van az SAS URI-t töltse le és futtassa a egyéni telepítési parancsfájlt, és az ahhoz tartozó fájlokat, amikor bármelyik csomópontját az az Azure-SSIS integrációs modul, újraindítása vagy rendszerképét alaphelyzetbe állítják. Írási engedély beállítása végrehajtási naplók feltöltése szükséges.

        > [!IMPORTANT]
        > Győződjön meg arról, hogy a SAS URI-t nem jár le, és az egyéni telepítés erőforrások mindig elérhetők az Azure-SSIS integrációs, létrehozás, törlés, a teljes élettartama során különösen akkor, ha rendszeresen állítsa le és indítsa el az Azure-SSIS integrációs modul ezen időszak alatt.

       ![A tároló közös hozzáférésű Jogosultságkód létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    1.  Másolja és mentse a tároló SAS URI-t.

       ![Másolja ki és mentse a közös hozzáférésű jogosultságkód igénylése](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    1.  Amikor üzembe, vagy konfigurálja újra az Azure-SSIS IR a Data Factory felhasználói felülete, az Azure-SSIS integrációs modul indítása előtt, meg kell adni SAS URI-ját a tároló a megfelelő mezőben **speciális beállítások** panelen:

       ![Adja meg a közös hozzáférésű Jogosultságkód](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

       Amikor üzembe, vagy konfigurálja újra a PowerShell-lel, az Azure-SSIS integrációs modul az Azure-SSIS integrációs modul indítása előtt, futtassa a `Set-AzureRmDataFactoryV2IntegrationRuntime` parancsmagot az új értéket a tároló SAS URI-azonosítójú `SetupScriptContainerSasUri` paraméter. Példa:

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    1.  Miután az egyéni telepítés befejezése és az Azure-SSIS integrációs modul indítása, a normál a kimenetbe, annak `main.cmd` és más végrehajtási jelentkezik be a `main.cmd.log` mappájában, a storage-tároló.

1.  Példák az egyéb egyéni beállítás, a nyilvános előzetes verzióban tárolóhoz, az Azure Storage Explorer csatlakoztatása.

    a.  Alatt **(helyi és csatolt)**, kattintson a jobb gombbal **Tárfiókok**válassza **csatlakozás az Azure storage**, jelölje be **kapcsolati karakterlánc vagy közös hozzáférésű jogosultságkód URI Azonosítójának**, majd válassza ki **tovább**.

       ![Csatlakozás az Azure storage és a közös hozzáférésű jogosultságkód igénylése](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Válassza ki **használni egy SAS URI-t** , és adja meg a következő SAS URI-t a nyilvános előzetes verzióban tároló. Válassza ki **tovább**, és válassza a **Connect**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Adja meg a tároló közös hozzáférésű Jogosultságkód](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Válassza ki a csatlakoztatott nyilvános előzetes verzióban tárolót, és kattintson duplán a `CustomSetupScript` mappát. Ebben a mappában az alábbi elemek a következők:

       1. A `Sample` mappába, amely tartalmaz egy egyszerű feladat telepítése az Azure-SSIS integrációs modult. minden egyes csomópontjára egy egyéni telepítés A feladat nem végez műveletet, de néhány másodpercre alvó állapotba lépni. A mappa is tartalmaz egy `gacutil` mappát, amelybe `gacutil.exe`. Ezenkívül `main.cmd` fájlmegosztások hozzáférési hitelesítő adatok megőrzéséhez megjegyzéseket tartalmaz.

       1. A `UserScenarios` mappába, amely a valós felhasználóiélmény-forgatókönyvekhez számos egyéni beállításokat tartalmazza.

    ![A nyilvános előzetes verzióban tároló tartalmának](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Kattintson duplán a `UserScenarios` mappát. Ebben a mappában az alábbi elemek a következők:

       1. A `.NET FRAMEWORK 3.5` mappába, amely tartalmazza a .NET-keretrendszer, amely az Azure-SSIS integrációs modult. minden egyes csomópontjára egyéni összetevők szükség lehet egy korábbi telepítése egy egyéni telepítés

       1. Egy `AAS` mappába, amely tartalmaz egy egyéni telepítés klienskódtárak telepítése az Azure-SSIS integrációs modul helyének, amelyek lehetővé teszik a szolgáltatás egyszerű hitelesítés használata az Azure Analysis Serviceshez (AAS) példányhoz való csatlakozáshoz az Analysis Services feladatok minden egyes csomóponton. Először töltse le a legújabb **MSOLAP (amd64)** és **AMO** ügyfél könyvtárak/Windows telepítők – például `x64_15.0.900.108_SQL_AS_OLEDB.msi` és `x64_15.0.900.108_SQL_AS_AMO.msi` – ki [Itt](https://docs.microsoft.com/en-us/azure/analysis-services/analysis-services-data-providers), majd Töltse fel őket az összes együtt `main.cmd` alkalmazásfájlokat a tárolóba.  

       1. A `BCP` mappába, amely egy egyéni telepítő telepítse az SQL Server parancssori segédeszközöket tartalmaz (`MsSqlCmdLnUtils.msi`), beleértve a tömeges másolási funkciójával (`bcp`), az Azure-SSIS integrációs modult. minden egyes csomópontjára

       1. Egy `EXCEL` mappába, amely egy egyéni telepítőt, és telepítse a nyílt forráskódú szerelvényeket tartalmazza (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, és `ExcelDataReader.dll`) minden egyes csomópontján az Azure-SSIS integrációs modult.

       1. Egy `MSDTC` mappába, amely tartalmaz egy egyéni telepítés módosításához a hálózati és biztonsági konfigurációi a Microsoft elosztott tranzakciók koordinátora (MSDTC) szolgáltatást minden egyes csomóponton, az Azure-SSIS integrációs modult. Győződjön meg arról, hogy el van-e az MSDTC, adja hozzá folyamat végrehajtása tevékenység, hajtsa végre a következő parancsot a csomagokban átvitelvezérlés elejére: `%SystemRoot%\system32\cmd.exe /c powershell -Command "Start-Service MSDTC"` 

       1. Egy `ORACLE ENTERPRISE` mappába, amely tartalmazza az egyéni telepítési parancsfájl (`main.cmd`) és a Csendes telepítési konfigurációs fájl (`client.rsp`) az Oracle-összekötők és OCI illesztőprogram telepítése az Azure-SSIS integrációs modul Enterprise Edition minden egyes csomópontjára. Ez a beállítás lehetővé teszi az Oracle-Csatlakozáskezelő, forrás és cél használatát. Először töltse le a Microsoft Connectors 5.0-s verzió Oracle (`AttunitySSISOraAdaptersSetup.msi` és `AttunitySSISOraAdaptersSetup64.msi`) származó [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) és a legújabb Oracle-ügyfél – például `winx64_12102_client.zip` – ki [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), majd feltölti őket az összes együtt `main.cmd` és `client.rsp` alkalmazásfájlokat a tárolóba. Ha TNS használatával csatlakozhat az Oracle, is szeretné letölteni `tnsnames.ora`, szerkeszthetik és feltöltése a tárolóba, akkor lehet másolni az Oracle telepítési mappába telepítés során.

       1. Egy `ORACLE STANDARD` mappába, amely tartalmazza az egyéni telepítési parancsfájl (`main.cmd`) telepítése az Oracle ODP.NET-illesztőt használja az Azure-SSIS integrációs modult. minden egyes csomópontjára Ez a beállítás lehetővé teszi az ADO.NET kapcsolati Manager forrás és cél használatát. Először töltse le a legújabb Oracle ODP.NET-illesztőt – például `ODP.NET_Managed_ODAC122cR1.zip` – ki [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), majd töltse fel a `main.cmd` alkalmazásfájlokat a tárolóba.

       1. Egy `SAP BW` mappába, amely tartalmazza az egyéni telepítési parancsfájl (`main.cmd`) telepítése a SAP .NET összekötő szerelvény (`librfc32.dll`) az Azure-SSIS integrációs modul Enterprise Edition minden egyes csomópontjára. Ez a beállítás lehetővé teszi az SAP BW Csatlakozáskezelő, forrás és cél használatát. Első lépésként töltse fel a 64 bites vagy 32 bites verzióját `librfc32.dll` a SAP telepítési mappájából a tárolóba, együtt `main.cmd`. Ezután másolja a SAP szerelvényből a `%windir%\SysWow64` vagy `%windir%\System32` mappát a telepítés során.

       1. A `STORAGE` mappába, amely tartalmazza az Azure PowerShell telepítése az Azure-SSIS integrációs modult. minden egyes csomópontjára egy egyéni telepítés A telepítő lehetővé teszi a üzembe helyezése és futtatása SSIS-csomagok futtató [PowerShell-parancsfájlok segítségével kezelheti az Azure Storage-fiók](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Másolás `main.cmd`, egy minta `AzurePowerShell.msi` (vagy telepítse a legújabb verziót), és `storage.ps1` a tárolóba. A csomagok PowerShell.dtsx sablonként használni. A csomag sablont egyesít egy [Azure Blob letöltése feladat](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), mely letöltések `storage.ps1` módosíthatóvá PowerShell-parancsfájl, és a egy [folyamat végrehajtása tevékenység](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) a parancsfájl, amely végrehajtja a minden egyes csomóponton.

       1. A `TERADATA` mappába, amely tartalmazza az egyéni telepítési parancsfájl (`main.cmd`), az adott fájl (`install.cmd`), és csomagok (`.msi`). Ezeket a fájlokat a Teradata összekötők, a Tpt-vel API-t és az ODBC-illesztő az Azure-SSIS integrációs modul Enterprise Edition minden egyes csomópontjára telepítse. Ez a beállítás lehetővé teszi a Teradata Csatlakozáskezelő forrás és cél használatát. Először töltse le a Teradata-eszközök és segédprogramok (TTU) 15.x zip-fájl (például `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) származó [Teradata](http://partnerintelligence.teradata.com), majd töltse fel a fenti együtt `.cmd` és `.msi` fájlok a tárolóba.

    ![A felhasználó forgatókönyvek mappában található mappák](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Próbálja ki ezeket az egyéni telepítés a mintákat, másolja be a tartalom a kiválasztott mappát a tárolóba. Amikor üzembe, vagy konfigurálja újra a PowerShell-lel az Azure-SSIS integrációs modul, futtassa a `Set-AzureRmDataFactoryV2IntegrationRuntime` parancsmagot az új értéket a tároló SAS URI-azonosítójú `SetupScriptContainerSasUri` paraméter.

## <a name="next-steps"></a>További lépések

-   [Az Azure-SSIS integrációs modul Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Fejlesztés a fizetős verzióra, vagy egyéni összetevők az Azure-SSIS integrációs modul licencelt](how-to-develop-azure-ssis-ir-licensed-components.md)
