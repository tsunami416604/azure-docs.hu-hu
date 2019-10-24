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
ms.openlocfilehash: f7b09dcbd474debc08b79599e9e2dfaaca52285a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754687"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Az Azure SSIS Integration Runtime telepítőjének testreszabása

A Azure-SSIS Integration Runtime egyéni telepítési felülete felületet biztosít a saját telepítési lépéseinek hozzáadásához a Azure-SSIS IR létesítése vagy újrakonfigurálása során. Az egyéni telepítő lehetővé teszi az alapértelmezett működési konfiguráció vagy környezet módosítását (például további Windows-szolgáltatások indításához vagy a fájlmegosztás hozzáférési hitelesítő adatainak megtartásához), vagy további összetevők (például szerelvények, illesztőprogramok vagy bővítmények) telepítésére. a Azure-SSIS IR minden egyes csomópontján.

Az Egyéni telepítést úgy konfigurálja, hogy létrehoz egy parancsfájlt és a hozzá tartozó fájlokat, és feltölti őket egy blob-tárolóba az Azure Storage-fiókban. A tárolóhoz megosztott hozzáférési aláírást (SAS) Uniform Resource Identifier (URI) kell megadni, amikor kiépíti vagy konfigurálja a Azure-SSIS IR. A Azure-SSIS IR mindegyik csomópontja letölti a parancsfájlt és a hozzá tartozó fájlokat a tárolóból, és az Egyéni telepítést emelt szintű jogosultságokkal futtatja. Ha az egyéni telepítés befejeződött, minden egyes csomópont feltölti a végrehajtás és más naplók szabványos kimenetét a tárolóba.

Telepítheti az ingyenes vagy a licenc nélküli összetevőket, valamint a fizetős vagy licencelt összetevőket is. Ha Ön ISV-t használ, tekintse meg [a fizetett vagy licencelt összetevők fejlesztése a Azure-SSIS IR számára](how-to-develop-azure-ssis-ir-licensed-components.md)című témakört.

> [!IMPORTANT]
> A Azure-SSIS IR v2 sorozatú csomópontjai nem alkalmasak az egyéni telepítéshez, ezért használja helyette a v3 sorozatú csomópontokat.  Ha már használja a v2 sorozatú csomópontokat, akkor a lehető leghamarabb váltson át a v3 sorozatú csomópontok használatára.

## <a name="current-limitations"></a>Aktuális korlátozások

-   Ha a szerelvényeket a globális szerelvény-gyorsítótárban (GAC) szeretné használni, `gacutil.exe`t kell meg`gacutil.exe` adnia az egyéni telepítés részeként, vagy a nyilvános előzetes tárolóban megadott másolatot kell használnia.

-   Ha a parancsfájl egyik almappájára szeretne hivatkozni, `msiexec.exe` nem támogatja a gyökérkönyvtárra hivatkozó `.\` jelölést. @No__t_1 helyett olyan parancsot használjon, mint a `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`.

-   Ha a Azure-SSIS IRt egyéni telepítéssel kell csatlakoztatni egy virtuális hálózathoz, csak Azure Resource Manager virtuális hálózat támogatott. A klasszikus virtuális hálózat nem támogatott.

-   A felügyeleti megosztás jelenleg nem támogatott a Azure-SSIS IRon.

-   Az IBM iSeries Access ODBC-illesztő nem támogatott a Azure-SSIS IR. Előfordulhat, hogy az egyéni telepítés során telepítési hiba jelenik meg. Segítségért forduljon az IBM ügyfélszolgálatához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Azure-SSIS IR testreszabásához a következőkre lesz szüksége:

-   [Azure-előfizetés](https://azure.microsoft.com/)

-   [Egy Azure SQL Database vagy felügyelt példány kiszolgálója](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [A Azure-SSIS IR kiépítése](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Egy Azure Storage-fiók](https://azure.microsoft.com/services/storage/). Az egyéni telepítéshez fel kell töltenie és tárolnia kell az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat egy blob-tárolóban. Az egyéni telepítési folyamat a végrehajtási naplókat is feltölti ugyanarra a blob-tárolóra.

## <a name="instructions"></a>Utasítások

1. [Azure PowerShell](/powershell/azure/install-az-ps)letöltése és telepítése.

1. Készítse elő az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat (például. bat,. cmd,. exe,. dll,. msi vagy. ps1 fájlok).

   1.  Rendelkeznie kell egy `main.cmd` nevű parancsfájllal, amely az egyéni telepítés belépési pontja.

   1.  Meg kell győződnie arról, hogy a parancsfájlt csendesen kell végrehajtani, ezért először a helyi gépen kell tesztelni a parancsfájlt.

   1.  Ha más eszközök (például `msiexec.exe`) által generált további naplókat szeretne feltölteni a tárolóba, adja meg az előre definiált környezeti változót, `CUSTOM_SETUP_SCRIPT_LOG_DIR` a parancsfájlok napló mappájába (például `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. [Azure Storage Explorer](https://storageexplorer.com/)letöltése, telepítése és elindítása.

   1. A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok** elemre, majd válassza **a Kapcsolódás az Azure Storage-hoz**lehetőséget.

      ![Csatlakozás az Azure Storage-hoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Válassza **a Storage-fiók nevének és kulcsának használata** lehetőséget, és kattintson a **tovább**gombra.

      ![Tárfióknév és -kulcs használata](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Adja meg az Azure Storage-fiók nevét és kulcsát, válassza a **tovább**, majd a **kapcsolat**lehetőséget.

      ![Tár fiók nevének és kulcsának megadása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. A csatlakoztatott Azure Storage-fiókban kattintson a jobb gombbal a **blob**-tárolók elemre, válassza a **blob-tároló létrehozása**elemet, és nevezze el az új tárolót.

      ![Blobtároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Válassza ki az új tárolót, és töltse fel az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat. Ügyeljen arra, hogy a tároló legfelső szintjén töltse fel `main.cmd`, nem pedig egyetlen mappában sem. Győződjön meg arról is, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza, így a Azure-SSIS IR később nem fog hosszú időt igénybe venni. Az egyéni telepítés maximális időtartama jelenleg 45 percen belül megtörténik, és ez magában foglalja az összes fájl letöltésének időpontját a tárolóból, és telepíti őket a Azure-SSIS IR. Ha hosszabb időszakra van szükség, vegyen fel egy támogatási jegyet.

      ![Fájlok feltöltése a blob-tárolóba](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Kattintson a jobb gombbal a tárolóra, és válassza a **közös hozzáférési aláírás beolvasása**elemet.

      ![A tároló közös hozzáférési aláírásának beolvasása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Hozza létre a tároló SAS URI-JÁT elég hosszú lejárati idővel és olvasási + írási és listázási engedélyekkel. Az egyéni telepítési parancsfájl és a hozzá tartozó fájlok letöltéséhez és futtatásához szükség van az SAS URI-ra, amikor a Azure-SSIS IR bármely csomópontja rendszerképe vagy újraindítása megtörténik. Írási engedéllyel kell rendelkeznie a telepítési végrehajtási naplók feltöltéséhez.

      > [!IMPORTANT]
      > Győződjön meg arról, hogy a SAS URI-ja nem jár le, és az egyéni beállítási erőforrások mindig elérhetők a Azure-SSIS IR teljes életciklusa során, a létrehozástól a törlésig, különösen akkor, ha az adott időszakban rendszeresen leállítja és elindítja a Azure-SSIS IR.

      ![A tároló közös hozzáférési aláírásának előállítása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Másolja és mentse a tároló SAS URI-JÁT.

      ![A megosztott hozzáférés aláírásának másolása és mentése](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Ha Data Factory felhasználói felületen helyezi üzembe vagy konfigurálja újra a Azure-SSIS IR, a Azure-SSIS IR elindítása előtt adja meg a tároló SAS URI-JÁT a **Speciális beállítások** panel megfelelő mezőjében:

      ![Adja meg a közös hozzáférési aláírást](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Ha a Azure-SSIS IRt a PowerShell-lel kiépíti vagy konfigurálja újra, a Azure-SSIS IR elindítása előtt futtassa az `Set-AzDataFactoryV2IntegrationRuntime` parancsmagot a tároló SAS URI-JAként az új `SetupScriptContainerSasUri` paraméter értékeként. Példa:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Az egyéni telepítés befejezése és a Azure-SSIS IR elindítása után megtalálhatja a `main.cmd` és más végrehajtási naplók standard kimenetét a tároló `main.cmd.log` mappájába.

1. Ha más egyéni telepítési példákat szeretne látni, kapcsolódjon a nyilvános előzetes verziójú tárolóhoz Azure Storage Explorer használatával.

   a.  A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok**elemre, válassza a **Kapcsolódás az Azure Storage-hoz**lehetőséget, válassza **a kapcsolati karakterlánc vagy a közös hozzáférési aláírás URI-ja**lehetőséget, majd kattintson a **tovább**gombra.

      ![Kapcsolódás az Azure Storage-hoz a közös hozzáférési aláírással](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Válassza a **sas URI használata** lehetőséget, és adja meg a következő sas URI-t a nyilvános előnézet tárolóhoz. Válassza a **tovább**, majd a **kapcsolat**kiválasztása lehetőséget.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![A tároló közös hozzáférési aláírásának megadása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Válassza ki a csatlakoztatott nyilvános előnézeti tárolót, és kattintson duplán a `CustomSetupScript` mappára. Ebben a mappában a következő elemek találhatók:

      1. Egy `Sample` mappa, amely egy egyéni telepítőt tartalmaz egy alapszintű feladat telepítéséhez a Azure-SSIS IR egyes csomópontjain. A feladat nem csinál semmit, de aludni néhány másodpercig. A mappa egy `gacutil` mappát is tartalmaz, amelynek teljes tartalma (`gacutil.exe`, `gacutil.exe.config` és `1033\gacutlrc.dll`) a tárolóba másolható. Emellett `main.cmd` megjegyzéseket is tartalmaz a fájlmegosztás hozzáférési hitelesítő adatainak megőrzéséhez.

      1. Egy `UserScenarios` mappa, amely a valós felhasználói forgatókönyvek számos egyéni beállítását tartalmazza.

   ![A nyilvános előzetes verziójú tároló tartalma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Kattintson duplán a `UserScenarios` mappára. Ebben a mappában a következő elemek találhatók:

      1. Egy `.NET FRAMEWORK 3.5` mappa, amely egy egyéni telepítőt tartalmaz a .NET-keretrendszer korábbi verziójának telepítéséhez, amelyre szükség lehet a Azure-SSIS IR egyes csomópontjain található egyéni összetevőkhöz.

      1. Egy `BCP` mappát, amely egy egyéni telepítőt tartalmaz a SQL Server parancssori segédeszközök (`MsSqlCmdLnUtils.msi`) telepítéséhez, beleértve a tömeges másolási programot (`bcp`) a Azure-SSIS IR egyes csomópontjain.

      1. Egy `EXCEL` mappa, amely egy egyéni telepítőt tartalmaz a nyílt forráskódú szerelvények (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` és `ExcelDataReader.dll`) telepítéséhez a Azure-SSIS IR egyes csomópontjain.

      1. Egy `ORACLE ENTERPRISE` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) és csendes telepítési konfigurációs fájlt (`client.rsp`) tartalmaz az Oracle-összekötők és a OCI-illesztőprogram telepítéséhez az Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi az Oracle-Csatlakozáskezelő, a forrás és a cél használatát. Először töltse le az Oracle-hez készült Microsoft Connectors v 5.0-s verzióját (`AttunitySSISOraAdaptersSetup.msi` és `AttunitySSISOraAdaptersSetup64.msi`) a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=55179) és a legújabb Oracle-ügyfélprogramból – például `winx64_12102_client.zip` [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)-ből, majd töltse fel őket együtt `main.cmd` és `client.rsp` tároló. Ha a TNS-t használja az Oracle-hez való csatlakozáshoz, le kell töltenie `tnsnames.ora`, szerkesztenie, és fel kell töltenie a tárolóba, hogy a telepítés során átmásolható legyen az Oracle telepítési mappájába.

      1. Egy `ORACLE STANDARD ADO.NET` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az Oracle ODP.NET-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a telepítő lehetővé teszi a ADO.NET-Csatlakozáskezelő, a forrás és a cél használatát. Először töltse le a legújabb Oracle ODP.NET-illesztőprogramot – például `ODP.NET_Managed_ODAC122cR1.zip` [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)-ből, majd töltse fel a `main.cmd` a tárolóba.
       
      1. Egy `ORACLE STANDARD ODBC` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az Oracle ODBC-illesztőprogram telepítéséhez és az DSN konfigurálásához a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi az ODBC-Csatlakozáskezelő/forrás/cél vagy a Power Query Csatlakozáskezelő/forrás ODBC-adatforrással való csatlakoztatását az Oracle-kiszolgálóhoz való kapcsolódáshoz. Először töltse le a legújabb Oracle azonnali ügyfélprogramot (alapszintű csomag vagy alapszintű Lite-csomag) és az ODBC-csomagot – például a 64 bites [csomagokat (](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) alapszintű csomag: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, alapszintű Lite-csomag: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC-csomag: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) vagy a 32 bites csomagokat innen [(](https://www.oracle.com/technetwork/topics/winsoft-085727.html) alapszintű csomag: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, alapszintű Lite-csomag: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC-csomag: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), majd töltse fel őket együtt `main.cmd` a tárolóba.

      1. Egy `SAP BW` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az SAP .NET-összekötő szerelvényének (`librfc32.dll`) a Azure-SSIS IR Enterprise Edition minden egyes csomópontján történő telepítéséhez. Ez a telepítő lehetővé teszi a SAP BW Csatlakozáskezelő, a forrás és a cél használatát. Először töltse fel az 64-bites vagy a 32-bites `librfc32.dll` verzióját az SAP telepítési mappájából a tárolóba, valamint a `main.cmd`. A szkript ezután átmásolja az SAP-szerelvényt a `%windir%\SysWow64` vagy `%windir%\System32` mappába a telepítés során.

      1. Egy `STORAGE` mappa, amely egy egyéni telepítőt tartalmaz, amely a Azure-SSIS IR minden egyes csomópontján Azure PowerShell telepítésére vonatkozik. Ez a beállítás lehetővé teszi, hogy [Az Azure Storage-fiók kezeléséhez PowerShell-parancsfájlokat](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)futtató SSIS-csomagokat telepítsen és futtasson. Másolja `main.cmd`, egy mintát `AzurePowerShell.msi` (vagy telepítse a legújabb verziót), és `storage.ps1` a tárolóba. A PowerShell. dtsx sablonként használhatja a csomagokat. A Package sablon kombinálja az [Azure Blob letöltési feladatát](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), amely letölti `storage.ps1` módosítható PowerShell-parancsfájlként, valamint egy [végrehajtási folyamat feladatot](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , amely végrehajtja a parancsfájlt az egyes csomópontokon.

      1. Egy `TERADATA` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`), a hozzá tartozó fájlt (`install.cmd`) és a telepítő csomagokat (`.msi`) tartalmaz. Ezek a fájlok a Teradata-összekötőket, a TPT API-t és az ODBC-illesztőt telepítik a Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a telepítő lehetővé teszi a Teradata-Csatlakozáskezelő, a forrás és a cél használatát. Először töltse le a Teradata Tools and Utilities (TTU) 15. x zip-fájlt (például `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) a [Teradata](http://partnerintelligence.teradata.com)-ból, majd töltse fel a fenti `.cmd` és `.msi` fájlokat a tárolóba.

   ![A felhasználói forgatókönyvek mappában található mappák](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Az egyéni telepítési minták kipróbálásához másolja és illessze be a tartalmat a kiválasztott mappából a tárolóba. A Azure-SSIS IR PowerShell-lel való üzembe helyezése vagy újrakonfigurálása során az új `SetupScriptContainerSasUri` paraméter értékeként futtassa a `Set-AzDataFactoryV2IntegrationRuntime` parancsmagot a tároló SAS URI-JAként.

## <a name="next-steps"></a>Következő lépések

-   [A Azure-SSIS Integration Runtime Enterprise kiadása](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Fizetett vagy licencelt egyéni összetevők fejlesztése az Azure-SSIS Integration Runtime számára](how-to-develop-azure-ssis-ir-licensed-components.md)
