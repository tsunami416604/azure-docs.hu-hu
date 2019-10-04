---
title: Az Azure-SSIS Integration Runtime telepítőjének testreszabása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható az Azure-SSIS Integration Runtime egyéni telepítési felülete további összetevők telepítéséhez vagy a beállítások módosításához
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 4962070d69af98d0c7b10dc6f931612766529dce
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515708"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Az Azure SSIS Integration Runtime telepítőjének testreszabása

Az Azure-SSIS Integration Runtime egyéni telepítési felülete felületet biztosít a saját telepítési lépéseinek hozzáadásához az Azure-SSIS IR üzembe helyezése vagy újrakonfigurálása során. Az egyéni telepítő lehetővé teszi az alapértelmezett működési konfiguráció vagy környezet módosítását (például további Windows-szolgáltatások indításához vagy a fájlmegosztás hozzáférési hitelesítő adatainak megtartásához), vagy további összetevők (például szerelvények, illesztőprogramok vagy bővítmények) telepítésére. Az Azure-SSIS IR minden egyes csomópontján.

Az Egyéni telepítést úgy konfigurálja, hogy létrehoz egy parancsfájlt és a hozzá tartozó fájlokat, és feltölti őket egy blob-tárolóba az Azure Storage-fiókban. Az Azure-SSIS integrációs modul telepítésekor vagy újrakonfigurálásakor meg kell adnia a tárolóhoz tartozó közös hozzáférési aláírás (SAS) Uniform Resource Identifier (URI). Az Azure-SSIS összes csomópontja ezután letölti a szkriptet és a hozzá tartozó fájlokat a tárolóból, és emelt szintű jogosultságokkal futtatja az Egyéni telepítést. Ha az egyéni telepítés befejeződött, minden egyes csomópont feltölti a végrehajtás és más naplók szabványos kimenetét a tárolóba.

Telepítheti az ingyenes vagy a licenc nélküli összetevőket, valamint a fizetős vagy licencelt összetevőket is. Ha Ön ISV-t használ, tekintse meg [Az Azure-SSIS IR-hez készült fizetős vagy licencelt összetevők fejlesztését](how-to-develop-azure-ssis-ir-licensed-components.md)ismertető témakört.

> [!IMPORTANT]
> Az Azure-SSIS IR v2 sorozatú csomópontjai nem alkalmasak az egyéni telepítéshez, ezért használja helyette a v3 sorozatú csomópontokat.  Ha már használja a v2 sorozatú csomópontokat, akkor a lehető leghamarabb váltson át a v3 sorozatú csomópontok használatára.

## <a name="current-limitations"></a>Aktuális korlátozások

-   Ha a szerelvényeket a globális szerelvény-gyorsítótárban (GAC) kívánja telepíteni, akkor az egyéni telepítés részeként kell megadnia `gacutil.exe` , vagy a nyilvános előzetes verziójú tárolóban megadott másolatot kell használnia. `gacutil.exe`

-   Ha a parancsfájl egyik almappájára szeretne hivatkozni, `msiexec.exe` a nem támogatja a `.\` gyökérkönyvtárra hivatkozó jelölést. Használjon olyan parancsot, `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` mint a `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`helyett.

-   Ha az Azure-SSIS integrációs modult egyéni telepítővel kell csatlakoztatni egy virtuális hálózathoz, csak Azure Resource Manager virtuális hálózat támogatott. A klasszikus virtuális hálózat nem támogatott.

-   A felügyeleti megosztás jelenleg nem támogatott az Azure-SSIS IR-ben.

-   Az IBM iSeries Access ODBC-illesztő nem támogatott az Azure-SSIS IR-ben. Előfordulhat, hogy az egyéni telepítés során telepítési hiba jelenik meg. Segítségért forduljon az IBM ügyfélszolgálatához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure-SSIS IR testreszabásához a következőkre van szükség:

-   [Azure-előfizetés](https://azure.microsoft.com/)

-   [Egy Azure SQL Database vagy felügyelt példány kiszolgálója](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Az Azure-SSIS IR kiépítése](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Egy Azure Storage-fiók](https://azure.microsoft.com/services/storage/). Az egyéni telepítéshez fel kell töltenie és tárolnia kell az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat egy blob-tárolóban. Az egyéni telepítési folyamat a végrehajtási naplókat is feltölti ugyanarra a blob-tárolóra.

## <a name="instructions"></a>Útmutatás

1. [Azure PowerShell](/powershell/azure/install-az-ps)letöltése és telepítése.

1. Készítse elő az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat (például. bat,. cmd,. exe,. dll,. msi vagy. ps1 fájlok).

   1.  Rendelkeznie kell egy nevű `main.cmd`parancsfájl-fájllal, amely az egyéni telepítés belépési pontja.

   1.  Ha más eszközök ( `msiexec.exe`például a) által generált további naplókat szeretne feltölteni a tárolóba, adja meg az előre definiált környezeti `CUSTOM_SETUP_SCRIPT_LOG_DIR` változót a `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`parancsfájlok naplófájljában (például:).

1. [Azure Storage Explorer](https://storageexplorer.com/)letöltése, telepítése és elindítása.

   1. A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok** elemre, majd válassza **a Kapcsolódás az Azure Storage-hoz**lehetőséget.

      ![Csatlakozás az Azure Storage-hoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Válassza **a Storage-fiók nevének és kulcsának használata** lehetőséget, és kattintson a **tovább**gombra.

      ![Tárfióknév és -kulcs használata](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Adja meg az Azure Storage-fiók nevét és kulcsát, válassza a **tovább**, majd a **kapcsolat**lehetőséget.

      ![Tár fiók nevének és kulcsának megadása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. A csatlakoztatott Azure Storage-fiókban kattintson a jobb gombbal a **blob**-tárolók elemre, válassza a **blob-tároló létrehozása**elemet, és nevezze el az új tárolót.

      ![Blobtároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Válassza ki az új tárolót, és töltse fel az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat. Ügyeljen arra, hogy a `main.cmd` tároló legfelső szintjén töltse fel a feltöltést, nem pedig egyetlen mappában sem. Győződjön meg arról is, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza, így az Azure-SSIS integrációs modulra később nem fog sokáig tartani. Az egyéni beállítás maximális időtartama 45 percen belül van beállítva, mielőtt időtúllépés történik, és ez magában foglalja az összes fájl letöltésének idejét a tárolóból, és telepíti őket az Azure-SSIS IR-be. Ha hosszabb időszakra van szükség, vegyen fel egy támogatási jegyet.

      ![Fájlok feltöltése a blob-tárolóba](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Kattintson a jobb gombbal a tárolóra, és válassza a **közös hozzáférési aláírás**beolvasása elemet.

      ![A tároló közös hozzáférési aláírásának beolvasása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Hozza létre a tároló SAS URI-JÁT elég hosszú lejárati idővel és olvasási + írási és listázási engedélyekkel. Az egyéni telepítési parancsfájl és a hozzá tartozó fájlok letöltéséhez és futtatásához az SAS URI-ra van szükség, amikor az Azure-SSIS-integrációs modul bármely csomópontja rendszerképe vagy újraindul. Írási engedéllyel kell rendelkeznie a telepítési végrehajtási naplók feltöltéséhez.

      > [!IMPORTANT]
      > Győződjön meg arról, hogy az SAS URI-ja nem jár le, és az egyéni beállítási erőforrások mindig elérhetők az Azure-SSIS integrációs modul teljes életciklusa során, a létrehozástól a törlésig, különösen akkor, ha az adott időszakban rendszeresen leállítja és elindítja az Azure-SSIS IR-t.

      ![A tároló közös hozzáférési aláírásának előállítása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Másolja és mentse a tároló SAS URI-JÁT.

      ![A megosztott hozzáférés aláírásának másolása és mentése](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Az Azure-SSIS IR Data Factory felhasználói felülettel való üzembe helyezése vagy újrakonfigurálása előtt írja be a tároló SAS URI-JÁT a **Speciális beállítások** panel megfelelő mezőjébe:

      ![Adja meg a közös hozzáférési aláírást](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Az Azure-SSIS IR PowerShell-lel való üzembe helyezése vagy újrakonfigurálása előtt az Azure-SSIS IR elindítása előtt futtassa a `Set-AzDataFactoryV2IntegrationRuntime` parancsmagot a tároló sas URI-jaként az új `SetupScriptContainerSasUri` paraméter értékeként. Példa:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Miután az egyéni telepítés befejeződik, és az Azure-SSIS IR elindul, megkeresheti a szabványos kimenetét `main.cmd` és más végrehajtási naplókat `main.cmd.log` a tárolójának mappájába.

1. Ha más egyéni telepítési példákat szeretne látni, kapcsolódjon a nyilvános előzetes verziójú tárolóhoz Azure Storage Explorer használatával.

   a.  A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok**elemre, válassza a **Kapcsolódás az Azure Storage-hoz**lehetőséget, válassza **a kapcsolati karakterlánc vagy a közös hozzáférési aláírás URI-ja**lehetőséget, majd kattintson a **tovább**gombra.

      ![Kapcsolódás az Azure Storage-hoz a közös hozzáférési aláírással](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Válassza a **sas URI használata** lehetőséget, és adja meg a következő sas URI-t a nyilvános előnézet tárolóhoz. Válassza a **tovább**, majd a **kapcsolat**kiválasztása lehetőséget.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![A tároló közös hozzáférési aláírásának megadása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Válassza ki a csatlakoztatott nyilvános előnézeti tárolót, `CustomSetupScript` és kattintson duplán a mappára. Ebben a mappában a következő elemek találhatók:

      1. Egy `Sample` mappa, amely egy egyéni telepítőt tartalmaz egy alapszintű feladat telepítéséhez az Azure-SSIS IR minden egyes csomópontján. A feladat nem csinál semmit, de aludni néhány másodpercig. A mappa egy `gacutil` mappát is tartalmaz, amely a teljes tartalmat (`gacutil.exe`, `gacutil.exe.config`és `1033\gacutlrc.dll`) is másolhatja a tárolóba. Emellett megjegyzéseket `main.cmd` tartalmaz a fájlmegosztás hozzáférési hitelesítő adatainak megőrzéséhez.

      1. Egy `UserScenarios` mappa, amely több egyéni beállítást tartalmaz a valós felhasználói forgatókönyvekhez.

   ![A nyilvános előzetes verziójú tároló tartalma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Kattintson duplán a `UserScenarios` mappára. Ebben a mappában a következő elemek találhatók:

      1. Egy `.NET FRAMEWORK 3.5` mappa, amely egy egyéni telepítőt tartalmaz a .NET-keretrendszer egy korábbi verziójának telepítéséhez, amelyre szükség lehet az Azure-SSIS integrációs modul minden egyes csomópontján lévő egyéni összetevőkhöz.

      1. Egy `BCP` mappa, amely egy egyéni telepítőt tartalmaz a SQL Server parancssori segédeszközök (`MsSqlCmdLnUtils.msi`) telepítéséhez, beleértve a tömeges másolási programot (`bcp`) az Azure-SSIS IR minden egyes csomópontján.

      1. Egy `EXCEL` mappa, amely egy egyéni telepítőt tartalmaz a nyílt forráskódú szerelvények (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`és `ExcelDataReader.dll`) telepítéséhez az Azure-SSIS integrációs modul minden egyes csomópontján.

      1. Egy `ORACLE ENTERPRISE` mappa, amely egy egyéni telepítési parancsfájlt (`main.cmd`) és csendes telepítési konfigurációs fájlt (`client.rsp`) tartalmaz az Oracle-összekötők és a OCI-illesztőprogram telepítéséhez az Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi az Oracle-Csatlakozáskezelő, a forrás és a cél használatát. Először töltse le a Microsoft Connectors 1.0-s`AttunitySSISOraAdaptersSetup.msi` verzióját `AttunitySSISOraAdaptersSetup64.msi`az Oracle-hez (és) a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=55179) és a `winx64_12102_client.zip` legújabb Oracle-ügyfélprogramból – például az [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)-ből, majd töltse fel őket együtt `main.cmd`akövetkezővel: és`client.rsp` a tárolóba. Ha a TNS használatával csatlakozik az Oracle-hez, le kell töltenie `tnsnames.ora`, szerkesztenie, és fel kell töltenie a tárolóba, hogy a telepítés során átmásolható legyen az Oracle telepítési mappájába.

      1. Egy `ORACLE STANDARD ADO.NET` mappa, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az Oracle ODP.net-illesztőprogram telepítéséhez az Azure-SSIS IR minden egyes csomópontján. Ez a telepítő lehetővé teszi a ADO.NET-Csatlakozáskezelő, a forrás és a cél használatát. Először töltse le a legújabb Oracle ODP.net-illesztőprogramot – például `ODP.NET_Managed_ODAC122cR1.zip` az [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)-ből, majd `main.cmd` töltse fel a tárolóba együtt.
       
      1. Egy `ORACLE STANDARD ODBC` mappa, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az Oracle ODBC-illesztőprogram telepítéséhez és az DSN konfigurálásához az Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi az ODBC-Csatlakozáskezelő/forrás/cél vagy a Power Query Csatlakozáskezelő/forrás ODBC-adatforrással való csatlakoztatását az Oracle-kiszolgálóhoz való kapcsolódáshoz. Először töltse le a legújabb Oracle azonnali ügyfélprogramot (alapszintű csomag vagy alapszintű Lite-csomag) és az ODBC-csomagot – például [](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) a 64 bites csomagokat innen `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`(alapszintű csomag `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`:, alapszintű Lite-csomag:, ODBC-csomag: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) vagy a 32 bites csomagok innen ( [](https://www.oracle.com/technetwork/topics/winsoft-085727.html) alapszintű csomag: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, alapszintű Lite `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`-csomag:, `instantclient-odbc-nt-18.3.0.0.0dbru.zip`ODBC-csomag:), majd feltöltheti őket `main.cmd` a tárolóba.

      1. Egy `SAP BW` mappa, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az SAP .net-összekötő szerelvény (`librfc32.dll`) telepítéséhez az Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a telepítő lehetővé teszi a SAP BW Csatlakozáskezelő, a forrás és a cél használatát. Először töltse fel a 64 bites vagy a 32 bites verzióját `librfc32.dll` az SAP telepítési mappájából a tárolóba, a `main.cmd`következővel együtt:. A szkript ezután az SAP-szerelvényt a `%windir%\SysWow64` vagy `%windir%\System32` mappába másolja a telepítés során.

      1. Egy `STORAGE` mappa, amely egy egyéni telepítőt tartalmaz a Azure PowerShell telepítéséhez az Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy [Az Azure Storage-fiók kezeléséhez PowerShell-parancsfájlokat](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)futtató SSIS-csomagokat telepítsen és futtasson. Másolja `main.cmd`, egy mintát `AzurePowerShell.msi` (vagy telepítse a legújabb verziót) és `storage.ps1` a tárolóba. A PowerShell. dtsx sablonként használhatja a csomagokat. A Package sablon egy [Azure Blob letöltési feladatot](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)egyesít, amely módosítható PowerShell-parancsfájlként töltődik `storage.ps1` le, és egy [végrehajtási folyamat feladata](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , amely végrehajtja a parancsfájlt az egyes csomópontokon.

      1. Egy `TERADATA` mappa, amely tartalmaz egy egyéni telepítési parancsfájlt`main.cmd`(), a hozzá tartozó`install.cmd`fájlt () és a telepítő`.msi`csomagokat (). Ezek a fájlok telepítik a Teradata-összekötőket, a TPT API-t és az Azure-SSIS IR Enterprise Edition minden egyes csomópontján található ODBC-illesztőt. Ez a telepítő lehetővé teszi a Teradata-Csatlakozáskezelő, a forrás és a cél használatát. Először töltse le a Teradata Tools and Utilities (TTU) 15. x zip `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`-fájlt (például:) a [Teradata](http://partnerintelligence.teradata.com)-ből, majd töltse fel a fentiekkel `.cmd` és `.msi` fájlokkal együtt a tárolóba.

   ![A felhasználói forgatókönyvek mappában található mappák](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Az egyéni telepítési minták kipróbálásához másolja és illessze be a tartalmat a kiválasztott mappából a tárolóba. Az Azure-SSIS IR PowerShell-lel való üzembe helyezése vagy újrakonfigurálása után `Set-AzDataFactoryV2IntegrationRuntime` futtassa a parancsmagot a tároló sas URI-jaként az új `SetupScriptContainerSasUri` paraméter értékeként.

## <a name="next-steps"></a>További lépések

-   [Az Azure-SSIS Enterprise kiadása Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Fizetett vagy licencelt egyéni összetevők fejlesztése az Azure-SSIS Integration Runtime számára](how-to-develop-azure-ssis-ir-licensed-components.md)
