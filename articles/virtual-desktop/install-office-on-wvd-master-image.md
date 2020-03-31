---
title: Az Office telepítése fő virtuális merevlemez-lemezképre – Azure
description: Az Office telepítése és testreszabása Windows virtuális asztal főlemezlemezen az Azure-ba.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127849"
---
# <a name="install-office-on-a-master-vhd-image"></a>Az Office telepítése egy fő virtuálisgép-rendszerképre

Ebből a cikkből megtudhatja, hogyan telepítheti az Office 365 ProPlust, a OneDrive-ot és más gyakori alkalmazásokat egy fő virtuális merevlemez-lemezképre az Azure-ba való feltöltéshez. Ha a felhasználóknak hozzá kell férniük bizonyos üzletági (LOB) alkalmazásokhoz, javasoljuk, hogy telepítse őket a cikkben található utasítások végrehajtása után.

Ez a cikk feltételezi, hogy már létrehozott egy virtuális gépet (VM). Ha nem, olvassa el [a Fő virtuális merevlemez-lemezkép előkészítése és testreszabása című témakört.](set-up-customize-master-image.md#create-a-vm)

Ez a cikk azt is feltételezi, hogy emelt szintű hozzáféréssel rendelkezik a virtuális gép, függetlenül attól, hogy az Azure-ban vagy a Hyper-V Manager. Ha nem, olvassa el [a Hozzáférés elévülése az összes Azure-előfizetési és felügyeleti csoport kezeléséhez.](../role-based-access-control/elevate-access-global-admin.md)

>[!NOTE]
>Ezek az utasítások a Windows virtuális asztal-specifikus konfiguráció, amely használható a szervezet meglévő folyamatok.

## <a name="install-office-in-shared-computer-activation-mode"></a>Az Office telepítése megosztott számítógép-aktiválási módban

A megosztott számítógép-aktiválás lehetővé teszi az Office 365 ProPlus telepítését a szervezet olyan számítógépére, amelyhez több felhasználó is hozzáfér. A megosztott számítógép-aktiválásról az [Office 365 ProPlus megosztott számítógép-aktiválásának áttekintése](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/)című témakörben olvashat bővebben.

Az Office telepítéséhez használja az [Office telepítőeszközét.](https://www.microsoft.com/download/details.aspx?id=49117) A Windows 10 Enterprise többmunkamenetes verziói csak az Office következő verzióit támogatják:
- Office 365 ProPlus
- Microsoft 365 Vállalati verziós előfizetéshez kapcsolódó Office 365 Vállalati verzió

Az Office központi telepítési eszközéhez konfigurációs XML-fájl szükséges. Az alábbi minta testreszabásához olvassa el [az Office központi telepítési eszközének konfigurációs beállításai című témakört.](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)

Ez a minta konfigurációs XML, amelyet biztosítottunk, a következő eket fogja tenni:

- Telepítse az Office-t a havi csatornáról, és a havi csatornáról kézbesítse a frissítéseket, amikor végrehajtják őket.
- Használja az x64 architektúrát.
- Tiltsa le az automatikus frissítéseket.
- Távolítsa el az Office meglévő telepítéseit, és telepítse át a beállításokat.
- A megosztott számítógép aktiválásának engedélyezése.

>[!NOTE]
>Előfordulhat, hogy a Visio rajzsablon-keresési szolgáltatása nem a várt módon működik a Windows virtuális asztalon.

A mintakonfigurációs XML a következőket teszi:

- A Skype Vállalati verzió telepítése
- Telepítse a OneDrive-ot felhasználónkénti módban. További információ: [OneDrive telepítése számítógépenként módban](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>A megosztott számítógép aktiválása csoportházirend-objektumok (GSO- k) vagy beállításjegyzék-beállítások on keresztül állítható be. A csoportházirend-csoportházirend a **Microsoft\\Office 2016 (gép)\\licencelési beállításainak felügyeleti\\\\sablonjaiban** található

Az Office telepítőeszköze tartalmazza a setup.exe programot. Az Office telepítéséhez futtassa a következő parancsot egy parancssorban:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Mintakonfiguráció.xml

A következő XML-minta telepíti a havi kiadást.

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
>Az Office csapata 64 bites telepítést javasol az **OfficeClientEdition** paraméterhez.

Az Office telepítése után frissítheti az Office alapértelmezett viselkedését. A viselkedés frissítéséhez futtassa a következő parancsokat egyenként vagy kötegfájlban.

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

A OneDrive általában felhasználónként van telepítve. Ebben a környezetben gépenként kell telepíteni.

A OneDrive-ot a következőképpen telepítheti számítógépenként:

1. Először hozzon létre egy helyet a OneDrive telepítőjének színpadra hozásához. A helyi lemezmappa\\\\vagy a [unc] (file://unc) hely rendben van.

2. Töltse le a OneDriveSetup.exe programot a szakaszos helyére ezzel a hivatkozással:<https://aka.ms/OneDriveWVD-Installer>

3. Ha az Office-t a OneDrive-val telepítette ** \<az ExcludeApp\>ID="OneDrive" /** elhagyásával, távolítsa el a meglévő OneDrive felhasználónkénti telepítéseket egy rendszergazda jogú parancssorból a következő parancs futtatásával:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Futtassa ezt a parancsot egy rendszergazda jogú parancssorból az **AllUsersInstall** beállításérték beállításértékének beállításához:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. A parancs futtatásával gépenként telepítheti a OneDrive-ot:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. A parancs futtatásával konfigurálhatja a OneDrive-ot bejelentkezéskor az összes felhasználó számára:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. A **felhasználói fiók csendes konfigurálásának** engedélyezése a következő parancs futtatásával.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. A Windows ismert mappáinak átirányítása és áthelyezése a OneDrive-ra a következő parancs futtatásával.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Csapatok és Skype

A Windows Virtuális asztal nem támogatja a Skype Vállalati verziót és a Teamst.

## <a name="next-steps"></a>További lépések

Most, hogy hozzáadta az Office-t a képhez, folytathatja a fő virtuális merevlemez-lemezkép testreszabását. Lásd: [Fő virtuális merevlemez-lemezkép előkészítése és testreszabása](set-up-customize-master-image.md).
