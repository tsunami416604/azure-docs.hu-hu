---
title: Útmutató a Windows sablongép beállításához | Microsoft dokumentumok
description: A Windows-sablongépek előkészítésének általános lépései a Lab Services szolgáltatásban.  Ezek a lépések közé tartozik a Windows Update ütemezésének beállítása, a OneDrive telepítése és az Office telepítése.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521189"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Útmutató a Windows-sablongépek beállításához az Azure Lab Servicesben

Ha windows 10-es sablongépet állít be az Azure Lab Services hez, az alábbiakban néhány gyakorlati tanácsra és tippekre van szüksége. Az alábbi konfigurációs lépések mind nem kötelezőek.  Ezek az előkészítő lépések azonban segíthetnek abban, hogy diákjai hatékonyabbak legyenek, minimalizálja az óramegszakításokat, és biztosíthatja, hogy a legújabb technológiákat használják.

>[!IMPORTANT]
>Ez a cikk a PowerShell-kódrészleteket tartalmazza a gépsablon módosítási folyamatának egyszerűsítéséhez.  Az összes megjelenített PowerShell-parancsfájlok, érdemes futtatni őket a Windows PowerShell rendszergazdai jogosultságokkal. A Windows 10-ben ennek gyors módja, hogy a jobb gombbal a Start menüre kattint, és válassza a "Windows PowerShell (Admin)"-t.

## <a name="install-and-configure-onedrive"></a>A OneDrive telepítése és konfigurálása

Annak érdekében, hogy a diákok adatai ne vesszenek el egy virtuális gép alaphelyzetbe állítása esetén, javasoljuk a diákoknak, hogy biztonsági másolatot készítsenek az adataikról a felhőbe.  A Microsoft OneDrive segítségével a diákok megvédhetik az adataikat.  

### <a name="install-onedrive"></a>A OneDrive telepítése

A OneDrive manuális letöltéséhez és telepítéséhez tekintse meg a [OneDrive](https://onedrive.live.com/about/download/) vagy a [OneDrive Vállalati verzió](https://onedrive.live.com/about/business/) letöltési lapjait.

A következő PowerShell-parancsfájlt is használhatja.  Automatikusan letölti és telepíti a OneDrive legújabb verzióját.  A OneDrive-ügyfél telepítése után futtassa a telepítőt.  Példánkban a kapcsolósegítségével telepítjük a `/allUsers` OneDrive-ot a számítógép összes felhasználója számára. A kapcsolót `/silent` a OneDrive csendes telepítéséhez is használjuk.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>A OneDrive testreszabásai

A [OneDrive-on számos testreszabásvégezhető](https://docs.microsoft.com/onedrive/use-group-policy)el. Tegyünk le néhány, a leggyakoribb testreszabások.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>A Windows ismert mappáinak csendes áthelyezése a OneDrive-ra

Az olyan mappákat, mint a Dokumentumok, letöltések és képek gyakran használják a diákok fájljainak tárolására. Annak érdekében, hogy ezekről a mappákról biztonsági másolatot készítsen a OneDrive-ba, javasoljuk, hogy helyezze át ezeket a mappákat a OneDrive-ra.

Ha olyan számítógépen van, amely nem használja az Active Directoryt, a felhasználók manuálisan is áthelyezhetik ezeket a mappákat a OneDrive-ra, miután hitelesítik magukat a OneDrive-on.

1. Fájlkezelő megnyitása
2. Kattintson a jobb gombbal a Dokumentumok, letöltések vagy Képek mappára.
3. Nyissa meg a Tulajdonságok > hely.  Helyezze át a mappát egy új mappába a OneDrive könyvtárában.

Ha a virtuális gép csatlakozik az Active Directoryhoz, beállíthatja, hogy a sablongép automatikusan kérje a diákokat, hogy az ismert mappákat helyezze át a OneDrive-ra.  

Először le kell kérnie az Office-bérlőazonosítóját.  További útmutatást [az Office 365-ös bérlői azonosító jave talál.](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)  Az Office 365-ös bérlői azonosítót a következő PowerShell használatával is beszerezheti.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Miután rendelkezik az Office 365-ös bérlői azonosítójával, állítsa be a OneDrive-ot úgy, hogy az ismert mappákat a következő PowerShell használatával helyezze át a OneDrive-ra.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>OneDrive-fájlok igény szerinti használata

Előfordulhat, hogy a diákok nak sok fájlja van a OneDrive-fiókjukban. A számítógép helymegtakarítása és a letöltési idő csökkentése érdekében javasoljuk, hogy a diákok OneDrive-fiókjában tárolt összes fájlt igény szerint tegye elérhetővé.  Az igény szerinti fájlok csak akkor töltődnek le, ha a felhasználó hozzáfér a fájlhoz.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>A felhasználók csendes bejelentkezése a OneDrive-ra

A OneDrive beállítható úgy, hogy automatikusan jelentkezzen be a bejelentkezett felhasználó Windows-hitelesítő adataival.  Az automatikus bejelentkezés olyan osztályok esetében hasznos, ahol a diák az Office 365 iskolai hitelesítő adataival jelentkezik be.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>A OneDrive telepítésének végén megjelenő oktatóanyag letiltása

Ezzel a beállítással megakadályozhatja, hogy az oktatóanyag elinduljon a OneDrive telepítőjének végén lévő webböngészőben.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Az automatikusan letöltendő fájl maximális méretének beállítása

Ez a beállítás a OneDrive szinkronizálási ügyfélalkalmazásán a Windows-hitelesítő adataikkal együtt használatos az olyan eszközökön, amelyeken nincs engedélyezve a OneDrive-fájlok igény szerinti engedélyezése. Minden olyan felhasználó, aki a megadott küszöbértéknél nagyobb OneDrive-val rendelkezik (MB-ban) a rendszer kéri, hogy válassza ki azokat a mappákat, amelyeket szinkronizálni szeretne, mielőtt a OneDrive szinkronizálási ügyfélprogram (OneDrive.exe) letölti a fájlokat.  Példánkban az "1111-2222-3333-4444" az Office 365-ös bérlői azonosító, a 0005000 pedig 5 GB-os küszöbértéket állít be.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>A Microsoft Office 365 telepítése és konfigurálása

### <a name="install-microsoft-office-365"></a>A Microsoft Office 365 telepítése

Ha a sablongépnek szüksége van az Office-ra, javasoljuk, hogy az Office telepítését az [Office deployment tool (ODT) eszközsegítségével](https://www.microsoft.com/download/details.aspx?id=49117 )telepítse. Az [Office 365 Ügyfélkonfigurációs szolgáltatás](https://config.office.com/) segítségével újrafelhasználható konfigurációs fájlt kell létrehoznia, hogy megdöntse, mely architektúrára, milyen funkciókra lesz szüksége az Office-ból, és milyen gyakran frissül.

1. Nyissa meg az [Office 365 Ügyfélkonfigurációs szolgáltatást,](https://config.office.com/) és töltse le saját konfigurációs fájlját.
2. Az [Office-telepítő eszköz letöltése](https://www.microsoft.com/download/details.aspx?id=49117).  Letöltött fájl `setup.exe`lesz .
3. Futtassa `setup.exe /download configuration.xml` az Office-összetevők letöltéséhez.
4. Futtassa `setup.exe /configure configuration.xml` az Office-összetevők telepítéséhez.

### <a name="change-the-microsoft-office-365-update-channel"></a>A Microsoft Office 365 frissítési csatornájának módosítása

Az Office konfigurációs eszközsegítségével beállíthatja, hogy az Office milyen gyakran kapjon frissítéseket. Ha azonban módosítania kell, hogy az Office milyen gyakran kapja meg a frissítéseket a telepítés után, módosíthatja a frissítési csatorna URL-címét. A csatorna URL-címeinek frissítése [az Office 365 ProPlus frissítési csatornájának módosítása a szervezetben lévő eszközökhöz](https://docs.microsoft.com/deployoffice/change-update-channels)című részben található. Az alábbi példa bemutatja, hogyan állíthatja be az Office 365-öt a havi frissítési csatorna használatára.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Frissítések telepítése és konfigurálása

### <a name="install-the-latest-windows-updates"></a>A legújabb Windows-frissítések telepítése

Javasoljuk, hogy a sablon virtuális gépközzététele előtt telepítse a legújabb Microsoft-frissítéseket a sablongépre biztonsági okokból.  Azt is potenciálisan elkerüli a diákok, hogy megzavarta a munkájukat, amikor a frissítések futnak váratlan időpontokban.

1. Indítási **beállítások** a Start menüből
2. Kattintson a **Frissítés** & Biztonság
3. Kattintson **a Frissítések keresése gombra**
4. A frissítések letöltése és telepítése.

A PowerShell segítségével is frissítheti a sablongép.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Egyes frissítések esetében szükség lehet a számítógép újraindítására.  A rendszer megkérdezi, hogy szükség van-e újraindításra.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>A Microsoft Store-alkalmazások legújabb frissítéseinek telepítése

Javasoljuk, hogy az összes Microsoft Store-alkalmazást frissítsük a legújabb verzióikra.  Az alábbiakban az alkalmazások Microsoft Store-ból történő manuális frissítésére vonatkozó utasításokat találja.  

1. Indítsa el a **Microsoft Store** alkalmazást.
2. Kattintson az alkalmazás felső sarkában lévő felhasználói fénykép melletti ellipszisre (...).
3. Válassza a **Legördülő** menü Letöltés és frissítések parancsát.
4. Kattintson **a Frissítés beszerezni** gombra.

A PowerShell segítségével is frissítheti a már telepített Microsoft Store-alkalmazásokat.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Az automatikus Windows-frissítések leállítása

A Windows legújabb verziójára való frissítése után érdemes lehet leállítani a Windows-frissítéseket.  Az automatikus frissítések potenciálisan zavarhatják az ütemezett óraidőt.  Ha a tanfolyam már fut, megkérheti a diákokat, hogy manuálisan ellenőrizzék a frissítéseket, vagy adjanak automatikus frissítéseket az ütemezett órákon kívül.  A Windows Update testreszabási lehetőségeiről a [Windows Update további beállításainak kezelése](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)című témakörben olvashat bővebben.

Előfordulhat, hogy az automatikus Windows-frissítések leállnak a következő PowerShell-parancsfájl használatával.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Idegen nyelvi csomagok telepítése

Ha további nyelvekre van szüksége a virtuális gépen, hozzáadhatja őket a Microsoft Store-on keresztül.

1. A Microsoft Store elindítása
2. Keresés a "nyelvi csomag"
3. A telepíteni kívánt nyelv kiválasztása

Ha már be van jelentkezve a sablon virtuális gép, használja a ["Nyelvi csomag telepítése" parancsikont,](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) hogy közvetlenül a megfelelő beállítások lapra.

## <a name="remove-unneeded-built-in-apps"></a>Szükségtelen beépített alkalmazások eltávolítása

A Windows 10 számos beépített alkalmazást tartalmaz, amelyekre nem feltétlenül van szükség az adott osztályhoz. A számítógép lemezképének diákok számára történő egyszerűsítése érdekében érdemes lehet eltávolítani néhány alkalmazást a sablongépről.  A telepített alkalmazások listájának megtekintéséhez `Get-AppxPackage` használja a PowerShell-parancsmag.  Az alábbi példa az összes eltávolítható telepített alkalmazást mutatja be.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Alkalmazás eltávolításához használja az Eltávolítás-Appx parancsmagát.  Az alábbi példa bemutatja, hogyan lehet eltávolítani mindent XBox kapcsolatos.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Általános tanítással kapcsolatos alkalmazások telepítése

Telepítsen más alkalmazásokat, amelyeket általában a Windows Áruházbeli alkalmazáson keresztül tanítanak. A javaslatok közé tartoznak az olyan alkalmazások, mint a [Microsoft Rajztábla alkalmazás,](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)a [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)és a Minecraft [Education Edition](https://education.minecraft.net/). Ezeket az alkalmazásokat manuálisan kell telepíteni a Windows Áruházon keresztül vagy a megfelelő webhelyeken keresztül a sablon virtuális gépén.

## <a name="conclusion"></a>Összegzés

Ez a cikk azt mutatja, hogy választható lépéseket, hogy előkészítse a Windows sablon virtuális gép egy hatékony osztály.  A lépések közé tartozik a OneDrive telepítése és az Office 365 telepítése, a Windows-frissítések telepítése és a Microsoft Store-alkalmazások frissítéseinek telepítése.  Azt is megvitattuk, hogyan állíthatod be a frissítéseket az osztályod számára legmegfelelőbb ütemezéshez.  

## <a name="next-steps"></a>További lépések
A Windows leállítási viselkedésének szabályozásáról szóló cikk a költségek kezelésének elősegítésére: [Útmutató a Windows leállítási viselkedésének szabályozásához](how-to-windows-shutdown.md)
