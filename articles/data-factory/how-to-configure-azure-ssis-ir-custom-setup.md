---
title: Az Azure SSIS Integration Runtime telepítőjének testreszabása
description: Ez a cikk azt ismerteti, hogyan használható az Azure-SSIS Integration Runtime egyéni telepítési felülete további összetevők telepítéséhez vagy a beállítások módosításához
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
ms.date: 12/23/2019
ms.openlocfilehash: ccf7ba2fd27dabdb090be87c5438ad68471996da
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497051"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Az Azure SSIS Integration Runtime telepítőjének testreszabása

A Azure-SSIS Integration Runtime egyéni telepítési felülete felületet biztosít a saját telepítési lépéseinek hozzáadásához a Azure-SSIS IR létesítése vagy újrakonfigurálása során. 

Az egyéni telepítő lehetővé teszi az alapértelmezett működési konfiguráció vagy környezet módosítását (például további Windows-szolgáltatások indításához vagy a fájlmegosztás hozzáférési hitelesítő adatainak megtartásához), vagy további összetevők (például szerelvények, illesztőprogramok vagy bővítmények) telepítésére. a Azure-SSIS IR minden egyes csomópontján.

Az egyéni beállításokat kétféleképpen végezheti el a Azure-SSIS IR: expressz egyéni telepítések parancsfájlok és szabványos egyéni telepítések nélkül parancsfájlok használatával.

Az expressz egyéni beállításokkal néhány gyakori rendszerkonfigurációt vagy Windows-parancsot futtathat, illetve néhány népszerű/ajánlott további összetevőt is telepíthet a parancsfájlok használata nélkül.  

A szokásos egyéni telepítések során elő kell készítenie egy parancsfájlt és a hozzá tartozó fájlokat, és együtt fel kell töltenie őket egy blob-tárolóba az Azure Storage-fiókban. Ezután meg kell adnia egy közös hozzáférési aláírás (SAS) Uniform Resource Identifier (URI) a tárolóhoz a Azure-SSIS IR kiépítésekor vagy újrakonfigurálásakor. A Azure-SSIS IR minden egyes csomópontja letölti a szkriptet és a hozzá tartozó fájlokat a tárolóból, és az egyéni telepítőt emelt szintű jogosultságokkal futtatja. Ha az egyéni telepítés elkészült, minden csomópont feltölti a végrehajtás és más naplók standard kimenetét a tárolóba.

Telepítheti az ingyenes/nem licencelt és a fizetős/licencelt összetevőket is az expressz/standard egyéni beállításokkal. Ha Ön ISV-t használ, tekintse meg a dokumentációt, [amely bemutatja, hogyan fejlesztheti Azure-SSIS IR fizetett vagy licencelt összetevőit](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> A Azure-SSIS IR v2 sorozatú csomópontjai nem alkalmasak az egyéni telepítéshez, ezért használja helyette a v3 sorozatú csomópontokat.  Ha már használja a v2 sorozatú csomópontokat, akkor a lehető leghamarabb váltson át a v3 sorozatú csomópontok használatára.

## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások csak a szabványos egyéni beállításokra vonatkoznak:

- Ha `gacutil.exe`t szeretne használni a parancsfájlban, hogy a szerelvényeket a globális szerelvény-gyorsítótárban (GAC) telepítse, meg kell adnia `gacutil.exe` az egyéni telepítés részeként, vagy pedig az alábbi nyilvános előzetes tárolóban megadott másolatot kell használnia.

- Ha a parancsfájl egyik almappájára szeretne hivatkozni, `msiexec.exe` nem támogatja a gyökérkönyvtárra hivatkozó `.\` jelölést. `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`helyett olyan parancsot használjon, mint a `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`.

- A Azure-SSIS IR jelenleg nem támogatja a felügyeleti megosztásokat, azaz a Windows által automatikusan létrehozott rejtett hálózati megosztásokat.

- Az IBM iSeries Access ODBC-illesztő nem támogatott a Azure-SSIS IR. Előfordulhat, hogy az egyéni telepítés során telepítési hibák léptek fel. Segítségért forduljon az IBM ügyfélszolgálatához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Azure-SSIS IR testreszabásához a következőkre lesz szüksége:

- [Azure-előfizetés](https://azure.microsoft.com/)

- [A Azure-SSIS IR kiépítés](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Egy Azure Storage-fiók](https://azure.microsoft.com/services/storage/). Az expressz egyéni telepítésekhez nem szükséges. A standard szintű egyéni telepítésekhez feltöltheti és tárolhatja az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat egy blob-tárolóban. Az egyéni telepítési folyamat a végrehajtási naplókat is feltölti ugyanarra a blob-tárolóra.

## <a name="instructions"></a>Utasítások

1. Ha a PowerShell-lel szeretné kiépíteni vagy újrakonfigurálni a Azure-SSIS IRt, töltse le és telepítse [Azure PowerShell](/powershell/azure/install-az-ps). Expressz egyéni telepítések esetén ugorjon a 4. lépésre.

1. Készítse elő az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat (például. bat,. cmd,. exe,. dll,. msi vagy. ps1 fájlok).

   1. Rendelkeznie kell egy `main.cmd`nevű parancsfájllal, amely az egyéni telepítés belépési pontja.

   1. Meg kell győződnie arról, hogy a parancsfájlt csendesen kell végrehajtani, ezért először a helyi gépen kell tesztelni a parancsfájlt.

   1. Ha más eszközök (például `msiexec.exe`) által generált további naplókat szeretne feltölteni a tárolóba, adja meg az előre definiált környezeti változót, `CUSTOM_SETUP_SCRIPT_LOG_DIR` a parancsfájlok napló mappájába (például `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

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

1. Ha Data Factory felhasználói felületen helyezi üzembe vagy konfigurálja újra a Azure-SSIS IR, akkor egyéni beállításokat adhat hozzá vagy távolíthat el, ha az integrációs modul telepítési paneljének **Speciális beállítások** szakaszában a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet jelöli be. 

   Ha szabványos egyéni beállításokat szeretne felvenni, adja meg a tároló SAS URI-JÁT az **egyéni telepítési tároló sas URI** -mezőjében. 
   
   Ha expressz egyéni beállításokat szeretne hozzáadni, válassza az **új** lehetőséget az **expressz egyéni telepítés hozzáadása** panel megnyitásához, majd válassza az **expressz egyéni telepítés típusa** legördülő menü bármelyik típusát:

   1. Ha bejelöli a **cmdkey futtatása parancsot** , a fájlmegosztás/Azure Files hozzáférési hitelesítő adatait megtarthatja Azure-SSIS IR a megadott számítógép/tartománynév, fióknév/Felhasználónév és fiók kulcs/jelszó megadásával az **/Add**, **/User**és **/pass** mezőkben. Ez hasonló a Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) parancs futtatásához a helyi gépen.
   
   1. Ha bejelöli a **környezeti változó hozzáadása** lehetőséget, akkor a (z) Azure-SSIS IR futtatott csomagjaiban a környezeti változó nevét és értékét adja meg a **változó neve** és a **változó értéke** mezőkben. Ez hasonló a Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) parancs futtatásához a helyi gépen.

   1. Ha bejelöli a **licencelt összetevő** típusának telepítése lehetőséget, az **összetevő neve** legördülő menüben kiválaszthatja az ISV-partnereinktől származó összes integrált összetevőt:

      1. Ha kijelöli a **szoftverével Task Factory** összetevőjét, telepítheti az összetevők szoftverével [-csomagját](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) a Azure-SSIS IR a **licenckulcs** mezőben megadott licenckulcs megadásával. Az aktuálisan integrált verzió a **2019.4.3**.

      1. Ha a **OH22'S HEDDA választja. I/o** -összetevő, telepítheti a [HEDDA. ](https://hedda.io/ssis-component/)A szolgáltatás megvásárlása után IO-adatminőség/-tisztító összetevő a Azure-SSIS IR oh22. Az aktuálisan integrált verzió a **1.0.13**.

      1. Ha kijelöli a **oh22's SQLPhonetics.net** összetevőt, a [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) adatminőség/megfelelő összetevőjét a oh22-ből telepítheti a Azure-SSIS IR. Ehhez írja be a **licenc** kulcsa mezőben megvásárolt termékkulcsot. Az aktuálisan integrált verzió a **1.0.43**.
   
   A hozzáadott expressz egyéni beállítások a **Speciális beállítások** szakaszban fognak megjelenni. Az eltávolításhoz jelölje be a jelölőnégyzeteket, majd kattintson a **Törlés**lehetőségre.

   ![Speciális beállítások egyéni telepítésekkel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

   Az Azure-SSIS IR PowerShell-lel való üzembe helyezése vagy újrakonfigurálása során az Azure-SSIS IR elindítása előtt a `Set-AzDataFactoryV2IntegrationRuntime` parancsmag futtatásával egyéni beállításokat adhat hozzá vagy távolíthat el.
   
   A normál egyéni telepítések esetében a tároló SAS URI-JÁT a `SetupScriptContainerSasUri` paraméter értékeként adhatja meg. Példa:

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                         -Name $MyAzureSsisIrName `
                                         -ResourceGroupName $MyResourceGroupName `
                                         -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                           -Name $MyAzureSsisIrName `
                                           -ResourceGroupName $MyResourceGroupName
   ```
   
   A normál egyéni telepítés befejezése és a Azure-SSIS IR elindítása után megtalálhatja `main.cmd` és más végrehajtási naplók standard kimenetét a tároló `main.cmd.log` mappájába.

1. Ha szeretné megtekinteni a szabványos egyéni beállítások egyes mintáit, a Azure Storage Explorer használatával csatlakozhat a nyilvános előzetes verziójú tárolóhoz.

   1. A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok**elemre, válassza a **Kapcsolódás az Azure Storage-hoz**lehetőséget, válassza **a kapcsolati karakterlánc vagy a közös hozzáférési aláírás URI-ja**lehetőséget, majd kattintson a **tovább**gombra.

      ![Kapcsolódás az Azure Storage-hoz a közös hozzáférési aláírással](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Válassza a **sas URI használata** lehetőséget, és adja meg a következő sas URI-t a nyilvános előnézet tárolóhoz. Válassza a **tovább**, majd a **kapcsolat**lehetőséget.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![A tároló közös hozzáférési aláírásának megadása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Válassza ki a csatlakoztatott nyilvános előnézeti tárolót, és kattintson duplán a `CustomSetupScript` mappára. Ebben a mappában a következő elemek találhatók:

      1. Egy `Sample` mappa, amely egy egyéni telepítőt tartalmaz egy alapszintű feladat telepítéséhez a Azure-SSIS IR egyes csomópontjain. A feladat nem csinál semmit, de aludni néhány másodpercig. A mappa egy `gacutil` mappát is tartalmaz, amelynek teljes tartalma (`gacutil.exe`, `gacutil.exe.config`és `1033\gacutlrc.dll`) a tárolóba másolható.

      1. Egy `UserScenarios` mappa, amely a valós felhasználói forgatókönyvekben számos egyéni telepítési mintát tartalmaz.

      ![A nyilvános előzetes verziójú tároló tartalma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. A következő elemek megkereséséhez kattintson duplán a `UserScenarios` mappára:

      1. Egy `.NET FRAMEWORK 3.5` mappa, amely egy egyéni telepítőt tartalmaz a .NET-keretrendszer korábbi verziójának telepítéséhez, amelyre szükség lehet a Azure-SSIS IR egyes csomópontjain található egyéni összetevőkhöz.

      1. Egy `BCP` mappát, amely egy egyéni telepítőt tartalmaz a SQL Server parancssori segédeszközök (`MsSqlCmdLnUtils.msi`) telepítéséhez, beleértve a tömeges másolási programot (`bcp`) a Azure-SSIS IR egyes csomópontjain.

      1. Egy `EXCEL` mappa, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az C# olyan szerelvények/könyvtárak telepítéséhez, amelyeket parancsfájl-feladatokban használhat a Azure-SSIS IR egyes csomópontjain lévő Excel-fájlok dinamikus olvasásához/írásához. Először töltse le [`ExcelDataReader.dll` innen, és `DocumentFormat.OpenXml.dll`](https://www.nuget.org/packages/ExcelDataReader/) innen [, majd](https://www.nuget.org/packages/DocumentFormat.OpenXml/)töltse fel őket együtt `main.cmd` a tárolóba. Ha csak a szabványos Excel-Csatlakozáskezelő/forrás/cél beállítást szeretné használni, a szükséges hozzáférés-újraterjeszthetőség már előre telepítve van a Azure-SSIS IR, így nincs szükség egyéni telepítésre.
      
      1. Egy `MYSQL ODBC` mappa, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz a MySQL ODBC-illesztőprogramok telepítéséhez a Azure-SSIS IR egyes csomópontjain. Ez a beállítás lehetővé teszi, hogy az ODBC-Csatlakozáskezelő/forrás/cél használatával kapcsolódjon a MySQL-kiszolgálóhoz. Először töltse le a MySQL ODBC illesztőprogram-telepítők legújabb 64 bites és 32 bites verzióit – például: `mysql-connector-odbc-8.0.13-winx64.msi` és `mysql-connector-odbc-8.0.13-win32.msi` – [MySQL](https://dev.mysql.com/downloads/connector/odbc/)-ről, majd töltse fel őket együtt `main.cmd` a tárolóba.

      1. Egy `ORACLE ENTERPRISE` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) és csendes telepítési konfigurációs fájlt (`client.rsp`) tartalmaz az Oracle-összekötők és a OCI-illesztőprogram telepítéséhez az Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy az Oracle-Csatlakozáskezelő/forrás/cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. Először töltse le az Oracle-hez készült Microsoft Connectors v 5.0-s verzióját (`AttunitySSISOraAdaptersSetup.msi` és `AttunitySSISOraAdaptersSetup64.msi`) a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=55179) és a legújabb Oracle-ügyfélprogramból – például `winx64_12102_client.zip` [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)-ből, majd töltse fel őket a tárolóba a `main.cmd` és `client.rsp`ével együtt. Ha a TNS-t használja az Oracle-hez való csatlakozáshoz, le kell töltenie `tnsnames.ora`, szerkesztenie, és fel kell töltenie a tárolóba, hogy a telepítés során átmásolható legyen az Oracle telepítési mappájába.

      1. Egy `ORACLE STANDARD ADO.NET` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az Oracle ODP.NET-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a ADO.NET Csatlakozáskezelő/forrás/cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. Először töltse le a legújabb Oracle ODP.NET-illesztőprogramot – például `ODP.NET_Managed_ODAC122cR1.zip` az [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)-ből, majd töltse fel a `main.cmd`ba együtt a tárolóba.
       
      1. Egy `ORACLE STANDARD ODBC` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az Oracle ODBC-illesztőprogram telepítéséhez és az DSN konfigurálásához a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi az ODBC-Csatlakozáskezelő/forrás/cél vagy a Power Query Csatlakozáskezelő/forrás ODBC-adatforrással való csatlakoztatását az Oracle-kiszolgálóhoz való kapcsolódáshoz. Először töltse le a legújabb Oracle azonnali ügyfélprogramot (alapszintű csomag vagy alapszintű Lite-csomag) és az ODBC-csomagot – például: a 64 bites csomagok (alapszintű csomag: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, alapszintű [Lite-csomag](https://www.oracle.com/technetwork/topics/winsoft-085727.html) : `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC-csomag: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) vagy a 32 bites [csomagok innen (](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) alapszintű csomag: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, alapszintű Lite-csomag: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC-csomag: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), majd töltse fel őket együtt a tárolóba `main.cmd`.

      1. Egy `ORACLE STANDARD OLEDB` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az Oracle OLEDB-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy az OLEDB Csatlakozáskezelő/forrás/cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. Először töltse le a legújabb Oracle OLEDB-illesztőprogramot – például `ODAC122010Xcopy_x64.zip` [Oracle](https://www.oracle.com/partners/campaign/index-090165.html)-ből, majd töltse fel a `main.cmd` a tárolóba.

      1. Egy `POSTGRESQL ODBC` mappa, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz a PostgreSQL ODBC-illesztőprogramok telepítéséhez a Azure-SSIS IR egyes csomópontjain. Ezzel a beállítással a PostgreSQL-kiszolgálóhoz való kapcsolódáshoz használhatja az ODBC-Csatlakozáskezelő/forrás/cél lehetőséget. Először töltse le a PostgreSQL ODBC illesztőprogram-telepítők legújabb 64 bites és 32 bites verzióit – például `psqlodbc_x64.msi` és `psqlodbc_x86.msi` a [PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/)-ből, majd töltse fel őket a `main.cmd` a tárolóba.

      1. Egy `SAP BW` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`) tartalmaz az SAP .NET-összekötő szerelvényének (`librfc32.dll`) a Azure-SSIS IR Enterprise Edition minden egyes csomópontján történő telepítéséhez. Ez a beállítás lehetővé teszi, hogy a SAP BW Csatlakozáskezelő/forrás/cél használatával kapcsolódjon SAP BW-kiszolgálóhoz. Először töltse fel az 64-bites vagy a 32-bites `librfc32.dll` verzióját az SAP telepítési mappájából, és `main.cmd` a tárolóba. A szkript ezután átmásolja az SAP-szerelvényt a `%windir%\SysWow64` vagy `%windir%\System32` mappába a telepítés során.

      1. Egy `STORAGE` mappa, amely egy egyéni telepítőt tartalmaz, amely a Azure-SSIS IR minden egyes csomópontján Azure PowerShell telepítésére vonatkozik. Ez a beállítás lehetővé teszi, hogy [Az Azure Storage-fiók kezeléséhez PowerShell-parancsfájlokat](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)futtató SSIS-csomagokat telepítsen és futtasson. Másolja `main.cmd`, egy mintát `AzurePowerShell.msi` (vagy használja a legújabb verziót), és `storage.ps1` a tárolóba. A PowerShell. dtsx sablonként használhatja a csomagokat. A Package sablon kombinálja az [Azure Blob letöltési feladatát](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), amely letölti `storage.ps1` módosítható PowerShell-parancsfájlként, valamint egy [végrehajtási folyamat feladatot](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , amely végrehajtja a parancsfájlt az egyes csomópontokon.

      1. Egy `TERADATA` mappát, amely egy egyéni telepítési parancsfájlt (`main.cmd`), a hozzá tartozó fájlt (`install.cmd`) és a telepítő csomagokat (`.msi`) tartalmaz. Ezek a fájlok telepítik a Teradata-összekötőket, a TPT API-t és az ODBC-illesztőt a Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a Teradata Csatlakozáskezelő/forrás/cél használatával kapcsolódjon a Teradata-kiszolgálóhoz. Először töltse le a Teradata Tools and Utilities 15. x zip-fájlt (például `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) a [Teradata](http://partnerintelligence.teradata.com), majd töltse fel a fenti `.cmd` és `.msi` fájlokat a tárolóba.

      1. Egy `ZULU OPENJDK` mappa, amely tartalmaz egy egyéni telepítési parancsfájlt (`main.cmd`) és egy PowerShell-fájlt (`install_openjdk.ps1`) a Zulu OpenJDK telepítéséhez a Azure-SSIS IR egyes csomópontjain. Ez a beállítás lehetővé teszi, hogy Azure Data Lake Store/rugalmas fájl-összekötőket használjon az ork/parketta-fájlok feldolgozásához, további információért lásd [itt](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) . Először töltse le a legújabb Zulu-OpenJDK (például `zulu8.33.0.1-jdk8.0.192-win_x64.zip` [), majd](https://www.azul.com/downloads/zulu/zulu-windows/)töltse fel a `main.cmd` és `install_openjdk.ps1` a tárolóba.

      ![A felhasználói forgatókönyvek mappában található mappák](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Az egyéni telepítési minták kipróbálásához másolja és illessze be a tartalmat a kiválasztott mappából a tárolóba.
   
      Ha Data Factory felhasználói felülettel állítja be vagy konfigurálja újra a Azure-SSIS IRt, jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációk/összetevő-telepítések** esetén jelölőnégyzetet a **Speciális beállítások** szakaszban, és adja meg a tároló sas URI-ját az **egyéni telepítési tároló sas URI** -mezőjében.
   
      Ha az Azure-SSIS IR PowerShell-lel való üzembe helyezése vagy újrakonfigurálása során futtatja a `Set-AzDataFactoryV2IntegrationRuntime` parancsmagot a tároló SAS URI-ja `SetupScriptContainerSasUri` paraméter értékeként.

## <a name="next-steps"></a>Következő lépések

-   [A Azure-SSIS Integration Runtime Enterprise kiadása](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Fizetett vagy licencelt egyéni összetevők fejlesztése az Azure-SSIS Integration Runtime számára](how-to-develop-azure-ssis-ir-licensed-components.md)
