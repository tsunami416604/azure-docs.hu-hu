---
title: Egyéni telepítés az az Azure-SSIS-integrációs futásidejű |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan használható az Azure-SSIS-integrációs futásidejű az egyéni telepítés felület
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: cce41a7529367d2e26b89a40593f9564d7e539b6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619587"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS-integrációs futásidejű egyéni beállítása

Az Azure-SSIS-integrációs futásidejű az egyéni telepítés kezelőfelület lehetővé teszi az alapértelmezett konfiguráció vagy a környezetben (például további Windows szolgáltatások indítása) működő alter vagy további összetevők (például a szerelvények, az illesztőprogramokat, vagy bővítmények) az Azure-SSIS infravörös minden egyes csomópontjára Általában a saját telepítő lépések hozzáadása a kiépítés vagy az Azure-SSIS infravörös újrakonfigurálása során felületet biztosít

Az egyéni telepítés előkészítése a parancsfájl és az ahhoz tartozó fájlokat, és feltöltheti az Azure Storage-fiók egy blob tárolóba konfigurálásához. Egy közös hozzáférésű Jogosultságkód (SAS) egységes erőforrás-azonosító (URI) a tároló megadnia, amikor kiépítése, vagy konfigurálja újra az Azure-SSIS infravörös Az Azure-SSIS-IR minden egyes csomópontjára majd tölti le a parancsfájlt és a hozzá kapcsolódó fájlok a tárolóból, és futtatja az egyéni telepítés emelt szintű jogosultságokkal. Ha egyéni telepítés befejeződött, minden csomópont feltölti a normál a kimenetbe végrehajtása és a többi naplófájlt a tárolóba.

Az ingyenes vagy a nem licencelt, és fizetett vagy licencelt összetevői is telepítheti. Ha egy független, lásd: [hogyan fejleszthet fizetett vagy az Azure-SSIS-IR-összetevők licenccel](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Aktuális korlátozások

-   Ha a használni kívánt `gacutil.exe` szerelvényeket a globális szerelvény gyorsítótárban (GAC) a telepítéséhez adja meg az egyéni telepítés részeként, vagy a nyilvános előzetes verzió-tárolóban megadott másolási szüksége.

-   Ha egy Vnetet az Azure-SSIS-IR egyéni telepítés csatlakoztatni kell, csak az Azure Resource Manager hálózatok esetén támogatott. Klasszikus virtuális hálózat nem támogatott.

-   Rendszergazdai megosztás jelenleg nem támogatott az Azure-SSIS infravörös

-   Ha azt szeretné, hogy egy fájlmegosztás hozzárendelése az egyéni beállítás, a meghajtó a `net use` parancs jelenleg nem támogatott. Ennek eredményeképpen, például a parancs nem használható `net use d: \\fileshareserver\sharename`. Ehelyett használja a `cmdkey` paranccsal – például `cmdkey /add:fileshareserver /user:yyy /pass:zzz` - hozzáférés a `\\fileshareserver\folder` közvetlenül a csomagokban.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-SSIS-IR testreszabásához kell a következőket:

-   [Azure-előfizetés](https://azure.microsoft.com/)

-   [Egy Azure SQL adatbázis vagy példány kezelt kiszolgáló](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Az Azure-SSIS-IR kiépítése](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Egy Azure Storage-fiók](https://azure.microsoft.com/services/storage/). Az egyéni telepítő töltse fel, és a egyéni telepítési parancsfájlt és a hozzá kapcsolódó fájlok tárolása egy blob-tároló. Az egyéni telepítő folyamat is fájlfeltöltések végrehajtási naplók blob tárolóhoz.

## <a name="instructions"></a>Utasítások

2.  Töltse le és telepítse [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (5.4 vagy újabb verzió).

3.  Készítse elő a egyéni telepítési parancsfájlt és a hozzá kapcsolódó fájlok (például, .bat, .cmd, .exe, .dll, .msi vagy .ps1 fájlok).

    1.  Rendelkeznie kell egy parancsfájlt nevű `main.cmd`, amely a belépési pont az egyéni beállítás.

    2.  Ha azt szeretné, hogy egyéb eszközök által létrehozott további naplók (például `msiexec.exe`) lehet feltölteni a tárolóba, adja meg az előre definiált környezeti változó `CUSTOM_SETUP_SCRIPT_LOG_DIR` a parancsfájlokat a napló-mappaként (például `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Letöltése, telepítése és elindítása [Azure Tártallózó](http://storageexplorer.com/).

    1.  A **(helyi és kapcsolódó)**, jobb választás **Storage-fiókok** válassza ki **csatlakozás az Azure storage**.

       ![Csatlakozás az Azure Storage-hoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Válassza ki **használja a tárfiók nevét és a kulcs** válassza **következő**.

       ![Tárfióknév és -kulcs használata](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Adja meg az Azure Storage-fiók nevét és a kulcsot, jelölje be **következő**, majd válassza ki **Connect**.

       ![Adja meg a tároló nevére és kulcsára](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  A csatlakoztatott Azure Storage-fiókjában, kattintson a jobb gombbal a **Blobtárolók**, jelölje be **Blob-tároló létrehozása**, és az új tároló neve.

       ![Blobtároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Jelölje ki az új tárolót, és töltse fel a egyéni telepítési parancsfájlt és a hozzá kapcsolódó fájlok. Győződjön meg arról, hogy töltsön `main.cmd` legfelső szintjén a tároló nem bármely mappában. 

       ![Fájlok feltöltése a blob-tároló](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Kattintson a jobb gombbal a tárolóra, és válassza ki **közös hozzáférésű Jogosultságkód beolvasása**.

       ![A közös hozzáférésű Jogosultságkód beolvasni a tároló](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Hozzon létre a tároló SAS URI-JÁNAK elég hosszú-lejárati idővel rendelkező és az írást + írási + engedélyek listában. Töltse le és futtassa a egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat, ha bármelyik csomópontját az Azure-SSIS-IR rendszerképének SAS URI van szüksége. Telepítő végrehajtási naplók feltöltése engedélyt kell írni.

       ![A közös hozzáférésű Jogosultságkód a tároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Másolja ki és mentse a tároló SAS URI.

       ![Másolja ki és mentse a közös hozzáférésű Jogosultságkód](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  Amikor kiépítése, vagy konfigurálja újra az Azure-SSIS-IR a PowerShell használatával, az Azure-SSIS-IR megkezdése előtt, futtassa a `Set-AzureRmDataFactoryV2IntegrationRuntime` parancsmag új értékeként a tároló SAS URI-azonosítójú `SetupScriptContainerSasUri` paraméter. Példa:

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  Miután az egyéni telepítő befejeződése és az Azure-SSIS-IR elindul, a szabványos kimeneti található `main.cmd` és egyéb végrehajtási jelentkezik be a `main.cmd.log` mappában található a tároló.

2.  Példák az egyéb egyéni telepítés, a nyilvános előzetes tároló a Azure Tártallózó csatlakozni.

    a.  A **(helyi és kapcsolódó)**, kattintson a jobb gombbal **Tárfiókok**, jelölje be **csatlakozás az Azure storage**, jelölje be **használja a kapcsolati karakterlánc vagy egy közös hozzáférésű aláírás URI**, majd válassza ki **következő**.

       ![Az Azure storage közös hozzáférésű Jogosultságkód való csatlakozás](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Válassza ki **SAS URI-használ** , és írja be a következő SAS URI-t a nyilvános előzetes tároló. Válassza ki **következő**, majd válassza **Connect**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Adja meg a megosztott hozzáférési aláírást ahhoz a tárolóhoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Válassza ki a csatlakoztatott Public Preview tárolót, és kattintson duplán a `CustomSetupScript` mappát. Ez a mappa a következő elemek a következők:

       1. A `Sample` mappa, amely tartalmaz egy egyéni telepítendő alapvető feladat minden egyes csomópontjára az Azure-SSIS infravörös A tevékenység nincs hatása, de néhány másodpercre alvó. A mappa is tartalmaz egy `gacutil` mappát, amelybe `gacutil.exe`.

       2. A `UserScenarios` mappát, amelybe a valós forgatókönyvekben több egyéni beállításokat.

    ![A nyilvános előzetes tároló tartalmának](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Kattintson duplán a `UserScenarios` mappát. Ez a mappa a következő elemek a következők:

       1. A `.NET FRAMEWORK 3.5` mappa, amely tartalmazza a .NET-keretrendszer, amely az Azure-SSIS infravörös minden egyes csomópontjára egyéni összetevők szükség lehet egy korábbi telepítése egyéni telepítés

       2. A `BCP` mappába, amely tartalmaz egy egyéni telepítendő SQL Server parancssori segédeszközök (`MsSqlCmdLnUtils.msi`), beleértve a tömeges másolási funkciójával (`bcp`), az Azure-SSIS infravörös minden egyes csomópontjára

       3. Egy `EXCEL` mappába, amely tartalmaz egy egyéni telepítendő nyílt forráskódú szerelvények (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, és `ExcelDataReader.dll`) az Azure-SSIS infravörös minden egyes csomópontjára

       4. Egy `MSDTC` mappa, amely tartalmazza a hálózati és biztonsági beállításait a Microsoft elosztott tranzakciók koordinátora (MSDTC) példány minden egyes csomóponton az Azure-SSIS infravörös módosítani egy egyéni telepítés

       5. Egy `ORACLE ENTERPRISE` mappa, amely tartalmaz egy egyéni beállítási parancsfájlt (`main.cmd`) és a csendes telepítést a konfigurációs fájl (`client.rsp`) az Oracle OCI illesztőprogram telepítéséhez az Azure-SSIS-IR Enterprise Edition minden egyes csomóponton. Ez a beállítás lehetővé teszi a Csatlakozáskezelő Oracle, a forrás és a cél. Első lépésként töltse le a legfrissebb Oracle ügyfél - például `winx64_12102_client.zip` – a [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) , majd töltse fel együtt `main.cmd` és `client.rsp` a tárolóba. TNS csatlakozni az Oracle használatakor is kell letölteni `tnsnames.ora`, szerkesztheti, majd töltse fel a tárolóba, akkor lehet másolni az Oracle telepítési mappájába a telepítés során.

       6. Egy `ORACLE STANDARD` mappa, amely tartalmaz egy egyéni beállítási parancsfájlt (`main.cmd`) az Oracle ODP.NET illesztőprogram telepítéséhez az Azure-SSIS infravörös minden egyes csomópontjára Ez a beállítás lehetővé teszi a ADO.NET Connection Manager, a forrás és a cél. Először töltse le a legfrissebb Oracle ODP.NET illesztőprogram - például `ODP.NET_Managed_ODAC122cR1.zip` – a [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), majd töltse fel együtt `main.cmd` a tárolóba.

       7. Egy `SAP BW` mappa, amely tartalmaz egy egyéni beállítási parancsfájlt (`main.cmd`) telepítéséhez a SAP .NET összekötő szerelvény (`librfc32.dll`) az Azure-SSIS-IR Enterprise Edition minden egyes csomóponton. Ez a beállítás lehetővé teszi a SAP BW Connection Manager, a forrás és a cél. Első lépésként töltse fel a 64 bites vagy 32 bites verziója `librfc32.dll` az SAP telepítési mappájából a tárolóba, együtt `main.cmd`. Ezután másolja a SAP szerelvényből a `%windir%\SysWow64` vagy `%windir%\System32` mappa a telepítés során.

       8. A `STORAGE` mappa, amely tartalmazza az Azure PowerShell telepítése az Azure-SSIS infravörös minden egyes csomópontjára egyéni telepítés Ez a beállítás lehetővé teszi a központi telepítése és futtatása SSIS-csomagok futtató [PowerShell-parancsfájlok segítségével kezelheti az Azure Storage-fiók](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Másolás `main.cmd`, egy minta `AzurePowerShell.msi` (vagy telepítse a legújabb verziót), és `storage.ps1` a tárolóhoz. A csomagok PowerShell.dtsx sablonként használni. A csomagot a sablon egyesíti egy [Azure Blob letöltési feladat](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), mely letöltések `storage.ps1` módosíthatóvá PowerShell-parancsfájlként, és egy [folyamat feladat végrehajtása](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , amely végrehajtja a parancsfájl minden egyes csomóponton.

       9. A `TERADATA` mappa, amely tartalmaz egy egyéni beállítási parancsfájlt (`main.cmd)`, az adott fájl (`install.cmd`), és csomagok (`.msi`). Ezeket a fájlokat a Teradata összekötők, az Tpt-vel API-t és az ODBC-illesztőprogram az Azure-SSIS-IR Enterprise Edition minden egyes csomópontjára telepítse. Ez a beállítás lehetővé teszi a Teradata Connection Manager, a forrás és a cél. Első lépésként töltse le a Teradata-eszközök és segédprogramok (TTU) 15.x zip-fájl (például `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) a [Teradata](http://partnerintelligence.teradata.com), majd töltse fel a fenti együtt `.cmd` és `.msi` fájlokat a tárolóba.

    ![A felhasználó forgatókönyvek mappában található mappák](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Próbálja meg a következő egyéni telepítés mintákat, másolja be a kijelölt mappa tartalmát a tárolóba. Amikor kiépítése, vagy konfigurálja újra az Azure-SSIS-IR a PowerShell-lel, futtassa a `Set-AzureRmDataFactoryV2IntegrationRuntime` parancsmag új értékeként a tároló SAS URI-azonosítójú `SetupScriptContainerSasUri` paraméter.

## <a name="next-steps"></a>További lépések

-   [Azure-SSIS integrációs futásidejű Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Hogyan fejleszthet a fizetős, vagy az Azure-SSIS-integrációs futásidejű egyéni összetevők licence](how-to-develop-azure-ssis-ir-licensed-components.md)
