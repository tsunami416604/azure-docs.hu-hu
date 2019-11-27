---
title: Az Office telepítése fő VHD-lemezképre – Azure
description: Az Office telepítése és testreszabása egy Windows rendszerű virtuális asztali főrendszerképen az Azure-ban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
ms.openlocfilehash: 059748f6f08b1c73d56aa3a127aa785f55eb63ee
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539134"
---
# <a name="install-office-on-a-master-vhd-image"></a>Az Office telepítése egy fő virtuálisgép-rendszerképre

Ebből a cikkből megtudhatja, hogyan telepítheti az Office 365 ProPlus, OneDrive és más gyakori alkalmazásokat egy fő virtuális merevlemezre (VHD) szolgáló lemezképre az Azure-ba való feltöltéshez. Ha a felhasználóknak bizonyos üzletági (LOB) alkalmazásokat kell elérniük, azt javasoljuk, hogy a jelen cikkben szereplő utasítások elvégzése után telepítse őket.

Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális gépet (VM). Ha nem, tekintse meg [a fő VHD-lemezkép előkészítése és testreszabása](set-up-customize-master-image.md#create-a-vm) című témakört.

A cikk azt is feltételezi, hogy emelt szintű hozzáféréssel rendelkezik a virtuális gépen, függetlenül attól, hogy az Azure-ban vagy a Hyper-V kezelőjében van-e kiépítve. Ha nem, tekintse [meg az Azure-előfizetések és-felügyeleti csoportok kezelésének megemelt hozzáférési](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)jogosultságát.

>[!NOTE]
>Ezek az utasítások egy olyan Windows rendszerű virtuális asztali konfigurációra vonatkoznak, amelyet a szervezet meglévő folyamataihoz használhat.

## <a name="install-office-in-shared-computer-activation-mode"></a>Az Office telepítése megosztott számítógép-aktiválási módban

A megosztott számítógép aktiválása lehetővé teszi az Office 365 ProPlus üzembe helyezését a szervezet egyik számítógépén, amelyet több felhasználó is elérhet. A megosztott számítógép aktiválásával kapcsolatos további információkért lásd: [az Office 365 ProPlus megosztott számítógép-aktiválásának áttekintése](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Az Office telepítéséhez használja az [Office üzembehelyezési eszközét](https://www.microsoft.com/download/details.aspx?id=49117) . A Windows 10 Enterprise multi-session csak az Office következő verzióit támogatja:
- Office 365 ProPlus
- Microsoft 365 Vállalati verzió-előfizetéssel rendelkező Office 365 Business

Az Office-telepítő eszköznek konfigurációs XML-fájlt kell megadnia. Az alábbi minta testreszabásához tekintse meg az [Office-telepítési eszköz konfigurációs beállításait](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

A példaként megadott konfigurációs XML-fájl a következő műveleteket végzi el:

- Telepítse az Office-t a havi csatornáról, és adja meg a frissítéseket a havi csatornáról, amikor azok végrehajtása folyamatban van.
- Használja az x64 architektúrát.
- Az automatikus frissítések letiltása.
- Távolítsa el az Office meglévő példányait, és telepítse át a beállításait.
- A megosztott számítógép aktiválásának engedélyezése.

>[!NOTE]
>Előfordulhat, hogy a Visio rajzsablon-keresési funkciója nem a várt módon működik a Windows Virtual Desktopban.

A minta konfigurációs XML-fájl nem fog megjelenni:

- A Skype vállalati verzió telepítése
- Telepítse a OneDrive-t felhasználónkénti módban. További információ: [a OneDrive telepítése számítógépenkénti módban](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>A megosztott számítógép aktiválása Csoportházirend objektumok (GPO-k) vagy beállításjegyzék-beállítások használatával állítható be. A csoportházirend-objektum a **Számítógép konfigurációja\\szabályzatok\\Felügyeleti sablonok\\Microsoft Office 2016 (gép)\\licencelési beállítások** között található.

Az Office-telepítő eszköz tartalmazza a Setup. exe fájlt. Az Office telepítéséhez futtassa a következő parancsot egy parancssorban:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Példa a Configuration. XML fájlra

A következő XML-minta fogja telepíteni a havi kiadást.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Az Office-csapat a 64 bites telepítés használatát javasolja a **OfficeClientEdition** paraméterhez.

Az Office telepítése után frissítheti az alapértelmezett Office-viselkedést. Futtassa a következő parancsokat egyenként, vagy egy batch-fájlban a viselkedés frissítéséhez.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>A OneDrive telepítése számítógépenkénti módban

A OneDrive telepítése általában felhasználónként történik. Ebben a környezetben telepíteni kell egy gépenként.

A következőképpen telepítheti a OneDrive-t gépi módban:

1. Először hozzon létre egy helyet a OneDrive-telepítő előkészítéséhez. A helyi lemez mappája vagy [\\\\UNC] (file://unc) helye nem megfelelő.

2. Töltse le a OneDriveSetup. exe fájlt a szakaszos helyre a következő hivatkozással: <https://aka.ms/OneDriveWVD-Installer>

3. Ha az Office-t az OneDrive-mel telepítette **\<EXCLUDEAPP id = "OneDrive"/\>** kihagyva, a következő parancs futtatásával távolítsa el a meglévő OneDrive felhasználónkénti telepítéseit egy emelt szintű parancssorból:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Futtassa ezt a parancsot egy rendszergazda jogú parancssorból a **AllUsersInstall** beállításjegyzék értékének megadásához:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Futtassa ezt a parancsot a OneDrive telepítéséhez számítógépenkénti módban:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Futtassa ezt a parancsot a OneDrive konfigurálásához az összes felhasználó bejelentkezésének megkezdéséhez:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. A következő parancs futtatásával engedélyezze a **felhasználói fiók csendes konfigurálását** .

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. A következő parancs futtatásával átirányíthatja és áthelyezheti a Windows ismert mappáit a OneDrive.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Csapatok és Skype

A Windows rendszerű virtuális asztal nem támogatja a Skype vállalati és munkacsoportok használatát.

## <a name="next-steps"></a>Következő lépések

Most, hogy hozzáadta az Office-t a lemezképhez, továbbra is testreszabhatja a fő VHD-lemezképet. Lásd: [a fő VHD-lemezkép előkészítése és testreszabása](set-up-customize-master-image.md).
