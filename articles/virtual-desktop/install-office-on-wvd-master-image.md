---
title: Az Office telepíteni egy fő VHD-rendszerképet – Azure
description: Hogyan telepítse és szabja testre az Office egy Windows virtuális asztal előzetes fő képre az Azure-bA.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: cb9edbb508ddd993dcefbf69eb06b4f0d4156485
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742554"
---
# <a name="install-office-on-a-master-vhd-image"></a>Az Office telepítése egy fő virtuálisgép-rendszerképre

Ez a cikk bemutatja, hogyan telepítse az Office 365 ProPlus, a onedrive vállalati verzió és az egyéb gyakori alkalmazások-fő virtuális merevlemez (VHD) Rendszerkép feltöltése az Azure-bA. Ha bizonyos sor üzletági (LOB) alkalmazások a felhasználók kell, javasoljuk a jelen cikkben lévő utasítások végrehajtása után telepítse őket.

Ez a cikk feltételezi, hogy már létrehozott egy virtuális gépet (VM). Ha nincs engedélyezve, [előkészítése és a egy fő VHD-lemezkép testreszabása](set-up-customize-master-image.md#create-a-vm)

Ez a cikk is feltételezi, hogy emelt szintű hozzáférés a virtuális gépen, hogy annak kiépítése az Azure vagy Hyper-V kezelőjét. Ha nincs engedélyezve, [jogosultságszintjének emelése az összes Azure előfizetéssel, és a felügyeleti csoportok kezelése](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Ezeket az utasításokat, amelyek a szervezet meglévő folyamatok használható Windows virtuális asztal előzetes jellemző konfiguráció esetén is.

## <a name="install-office-in-shared-computer-activation-mode"></a>Telepítse az Office megosztott számítógép aktiválási módban

Megosztott aktiválás lehetővé teszi, hogy egy számítógépre a szervezet több felhasználó által az Office 365 ProPlus üzembe helyezése. További információ a megosztott aktiválás: [Office 365 Proplus megosztott aktiválás áttekintése](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Használja a [Office-telepítő eszköz](https://www.microsoft.com/download/details.aspx?id=49117) Office telepítéséhez. Windows 10 Enterprise több munkamenet csak az Office következő verzióit támogatja:
- Office 365 ProPlus
- A Microsoft 365 üzleti előfizetési az Office 365 üzleti

Az Office-telepítő eszköz egy konfigurációs XML-fájl szükséges. Testre szabhatja a következő mintát, tekintse meg a [beállítási lehetőségei az Office-telepítő eszköz](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Megadtuk az XML konfigurációs rendszer tegye a következőket:

- A Insiders csatornáról telepíti az Office, és juttathatja a Insiders csatornáról vagyunk végrehajtásakor.
- Használja a x64 architektúra.
- Az automatikus frissítések letiltásához.
- A Visio és a projekt telepítése.
- Távolítsa el minden meglévő telepítéseit, Office, és azok a beállítások áttelepítéséhez.
- Megosztott aktiválás engedélyezése.

>[!NOTE]
>A Visio programban rajzsablonon keresési funkció nem működik Windows virtuális asztali előzetes konfigurálása során.

Itt nem ez a minta XML konfiguráció nem:

- Telepítse a Skype for Business
- Onedrive vállalati verzió telepítését felhasználónkénti módban. További tudnivalókért lásd: [onedrive vállalati verzió telepítéséhez a gépenkénti módban](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Megosztott aktiválás csoportházirend-objektumok (GPO) vagy a beállításjegyzék-beállítások állíthat be. A csoportházirend-objektum a következő helyen található **számítógép konfigurációja\\házirendek\\felügyeleti sablonok\\a Microsoft Office 2016 (számítógép)\\licencelési beállítások**

Az Office-telepítő eszköz setup.exe tartalmazza. Office telepítéséhez futtassa a következő parancsot a parancssorban:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Sample configuration.xml

A következő XML-minta telepíti az Insider-kiadás.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Az Office-csapattól érkezik a 64 bites telepítése használatát javasolja a **OfficeClientEdition** paraméter.

Miután telepítette az Office, Office alapértelmezés frissítheti. Futtassa az alábbi parancsokat egyenként, vagy frissíteni a viselkedés fájlt egy kötegfájlban.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Onedrive vállalati verzió telepítését a gépenkénti módban

Onedrive vállalati verzió felhasználónként megfelelően telepítve. Ebben a környezetben kell lennie a gépenkénti telepítve.

Íme a onedrive vállalati verzió telepítését a gépenkénti módban:

1. Először hozzon létre egy helyet a onedrive vállalati verzió telepítő előkészítéséhez. Egy helyi mappát vagy [\\\\unc] (file://unc) helye nem okoz gondot.

2. Töltse le a OneDriveSetup.exe a manuálisan előkészített helyre a következő hivatkozást: <https://aka.ms/OneDriveWVD-Installer>

3. Ha az office a onedrive vállalati verziója, kihagyva  **\<ExcludeApp ID = "Onedrive vállalati verzió" /\>** , távolítsa el az összes meglévő onedrive vállalati verzió felhasználói telepítés a következő futtatásával egy rendszergazda jogú parancssorból a parancs:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Futtassa a következő parancsot egy rendszergazda jogú parancssorból beállítása a **AllUsersInstall** beállításazonosító:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Onedrive vállalati verzió telepítését a gépenkénti módban a következő parancs futtatásával:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Konfigurálása a onedrive vállalati verzió indítás bejelentkezés az összes felhasználó számára a következő parancs futtatásával:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Engedélyezése **csendes módban a felhasználói fiók konfigurálása** a következő parancs futtatásával.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Átirányítási, és helyezze át a Windows ismert mappák a onedrive-ra a következő parancs futtatásával.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Csoportok és Skype

Windows virtuális asztal nem támogatja a Skype, az üzleti és a csapatok számára.

## <a name="next-steps"></a>További lépések

Most, hogy az Office a lemezképhez hozzáadott, továbbra is a fő VHD-lemezkép testreszabását. Lásd: [előkészítése és a egy fő VHD-rendszerképet testreszabása](set-up-customize-master-image.md).
