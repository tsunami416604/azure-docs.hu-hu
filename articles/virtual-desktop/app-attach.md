---
title: Windows rendszerű virtuális asztali MSIX-alkalmazás csatolása – Azure
description: A Windows rendszerű virtuális asztalok MSIX alkalmazásának beállítása.
author: Heidilohr
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3b02be8f35ff33f758aebe03c89287c51c9ffef7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816321"
---
# <a name="set-up-msix-app-attach"></a>MSIX-alkalmazás csatolásának beállítása

> [!IMPORTANT]
> A MSIX-alkalmazás csatolása jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebből a témakörből megtudhatja, hogyan állíthatja be a MSIX-alkalmazások csatlakoztatását egy Windows rendszerű virtuális asztali környezetben.

## <a name="requirements"></a>Követelmények

Mielőtt elkezdené, a következő lépésekkel kell konfigurálnia a MSIX-alkalmazás csatolását:

- Hozzáférés a Windows Insider portálhoz a Windows 10 azon verziójának beszerzéséhez, amely támogatja a MSIX app Attach API-kat.
- Működő Windowsos virtuális asztali telepítés. A Windows rendszerű virtuális asztali gépek (klasszikus) központi telepítésének megismeréséhez tekintse meg [a bérlő létrehozása a Windows rendszerű virtuális asztalon](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)című témakört. A Windows rendszerű virtuális asztali Azure Resource Manager integrációs szolgáltatással való üzembe helyezésével kapcsolatos további információkért lásd: [gazdagép-készlet létrehozása a Azure Portal](./create-host-pools-azure-marketplace.md).
- A MSIX-csomagoló eszköz.
- Hálózati megosztás a Windows rendszerű virtuális asztali környezetben, ahol a MSIX-csomagot tárolja a rendszer.

## <a name="get-the-os-image"></a>Az operációs rendszer rendszerképének beolvasása

Először le kell kérnie az operációs rendszer rendszerképét. Az operációs rendszer rendszerképét az Azure Portalon keresztül érheti el. Ha azonban Ön a Windows Insider program tagja, lehetősége van a Windows Insider portál használatára.

### <a name="get-the-os-image-from-the-azure-portal"></a>Az operációs rendszer rendszerképének beolvasása a Azure Portal

Az operációs rendszer rendszerképének beszerzése a Azure Portalról:

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be.

2. Válassza a **virtuális gép létrehozása**lehetőséget.

3. Az **alapszintű** lapon válassza a **Windows 10 Enterprise multi-session (2004-es verzió**) lehetőséget.

4. A virtuális gép létrehozásának befejezéséhez kövesse a további utasításokat.

     >[!NOTE]
     >A virtuális gép használatával közvetlenül tesztelheti a MSIX-alkalmazás csatolását. További információért ugorjon a [virtuális merevlemez-vagy VHDX-csomag MSIX való létrehozásához](#generate-a-vhd-or-vhdx-package-for-msix). Ellenkező esetben folytassa a szakasz olvasásával.

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>Az operációs rendszer rendszerképének beszerzése a Windows Insider portálról

Az operációs rendszer rendszerképének beszerzése a Windows Insider portálról:

1. Nyissa meg a [Windows Insider portált](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) , és jelentkezzen be.

     >[!NOTE]
     >A Windows Insider portálhoz való hozzáféréshez a Windows Insider program tagjának kell lennie. Ha többet szeretne megtudni a Windows Insider programról, tekintse meg a [Windows Insider dokumentációját](/windows-insider/at-home/).

2. Görgessen le a **Select Edition (kiadás kiválasztása** ) szakaszhoz, és válassza a **Windows 10 Insider Preview Enterprise (gyors) – build 19041** vagy újabb lehetőséget.

3. Válassza a **megerősítés**lehetőséget, majd válassza ki a használni kívánt nyelvet, majd kattintson ismét a **Confirm (megerősítés** ) gombra.

     >[!NOTE]
     >Jelenleg az angol az egyetlen olyan nyelv, amelyet teszteltek a szolgáltatással. Kijelölhet más nyelveket is, de előfordulhat, hogy nem a kívánt módon jelennek meg.

4. A letöltési hivatkozás létrehozása után válassza ki a **64 bites letöltést** , és mentse a helyi merevlemezre.

## <a name="prepare-the-vhd-image-for-azure"></a>A VHD-rendszerkép előkészítése az Azure-hoz

Ezután létre kell hoznia egy fő VHD-lemezképet. Ha még nem hozta létre a fő VHD-lemezképet, ugorjon a [fő VHD-rendszerkép előkészítése és testreszabása](set-up-customize-master-image.md) című témakörre, és kövesse az itt található utasításokat.

A fő VHD-rendszerkép létrehozása után le kell tiltania az MSIX alkalmazáshoz tartozó alkalmazások automatikus frissítését. Az automatikus frissítések letiltásához futtatnia kell a következő parancsokat egy rendszergazda jogú parancssorban:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Miután letiltotta az automatikus frissítéseket, engedélyeznie kell a Hyper-V-t, mert a Mount-VHD parancsot fogja használni a kilépéshez és a virtuális merevlemez leválasztásához.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>Ehhez a változáshoz újra kell indítani a virtuális gépet.

Ezután készítse elő az Azure-beli virtuális gép virtuális merevlemezét, és töltse fel az eredményül kapott VHD-lemezt az Azure-ba További információ: [Master VHD-lemezkép előkészítése és testreszabása](set-up-customize-master-image.md).

Miután feltöltötte a VHD-t az Azure-ba, hozzon létre egy, az új rendszerképen alapuló alkalmazáskészletet az Azure Marketplace oktatóanyagának használatával, a [gazdagép létrehozása](create-host-pools-azure-marketplace.md) című témakör utasításait követve.

## <a name="prepare-the-application-for-msix-app-attach"></a>Az alkalmazás előkészítése MSIX-alkalmazás csatolásához

Ha már rendelkezik MSIX-csomaggal, ugorjon előre a [Windows rendszerű virtuális asztali infrastruktúra konfigurálásához](#configure-windows-virtual-desktop-infrastructure). Ha az örökölt alkalmazásokat szeretné tesztelni, kövesse az [MSIX-csomag létrehozása egy virtuális gépen asztali telepítőből](/windows/msix/packaging-tool/create-app-package-msi-vm/) című témakör utasításait, hogy az örökölt alkalmazást egy MSIX-csomagba alakítsa át.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>VHD-vagy VHDX-csomag létrehozása a MSIX

A csomagok VHD-vagy VHDX-formátumúak a teljesítmény optimalizálása érdekében. A MSIX VHD-vagy VHDX-csomagok megfelelő működéséhez szükségesek.

VHD-vagy VHDX-csomag létrehozása a MSIX-hez:

1. [Töltse le a msixmgr eszközt](https://aka.ms/msixmgr) , és mentse a. zip mappát egy, a munkamenet-gazda virtuális gépen található mappába.

2. Bontsa ki a msixmgr Tool. zip mappát.

3. Helyezze a forrás MSIX-csomagot ugyanabba a mappába, ahová kicsomagolta a msixmgr eszközt.

4. Futtassa a következő parancsmagot a PowerShellben a virtuális merevlemez létrehozásához:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Győződjön meg arról, hogy a virtuális merevlemez mérete elég nagy a kibontott MSIX tárolásához. *

5. Futtassa az alábbi parancsmagot az újonnan létrehozott virtuális merevlemez csatlakoztatásához:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Futtassa ezt a parancsmagot a virtuális merevlemez inicializálásához:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Futtassa ezt a parancsmagot egy új partíció létrehozásához:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Futtassa ezt a parancsmagot a partíció formázásához:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Hozzon létre egy szülő mappát a csatlakoztatott virtuális merevlemezen. Ez a lépés kötelező, mivel a MSIX-alkalmazás csatolásához szülőmappa szükséges. A szülőmappa tetszőleges módon elnevezhető.

### <a name="expand-msix"></a>MSIX kibontása

Ezt követően ki kell bontania a MSIX-rendszerképet a kicsomagolásával. A MSIX rendszerkép kicsomagolása:

1. Nyisson meg egy parancssort rendszergazdaként, és navigáljon ahhoz a mappához, ahová letöltötte és kicsomagolta a msixmgr eszközt.

2. A következő parancsmag futtatásával csomagolja ki a MSIX a létrehozott és az előző szakaszban csatlakoztatott virtuális merevlemezre.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A kicsomagolás befejezése után a következő üzenetnek kell megjelennie:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Ha a hálózaton belüli üzleti (vagy oktatási) Microsoft Store csomagokat használ, vagy olyan eszközökön, amelyek nem csatlakoznak az internethez, be kell szereznie a csomag licenceit az áruházból, és telepítenie kell őket az alkalmazás sikeres futtatásához. Lásd: [kapcsolat nélküli csomagok használata](#use-packages-offline).

3. Navigáljon a csatlakoztatott virtuális merevlemezhez, és nyissa meg az alkalmazás mappáját, és ellenőrizze, hogy megtalálható-e a csomag tartalma.

4. Válassza le a virtuális merevlemezt.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>A Windows rendszerű virtuális asztali infrastruktúra konfigurálása

A tervezés szerint egyetlen MSIX kibontott csomag (az előző szakaszban létrehozott VHD) több munkamenet-gazda virtuális gép között is megosztható, mivel a virtuális merevlemezek csak olvasható módban vannak csatolva.

Mielőtt elkezdené, győződjön meg arról, hogy a hálózati megosztás megfelel a következő követelményeknek:

- A megosztás SMB-kompatibilis.
- A munkamenet-címkészlet részét képező virtuális gépek NTFS-jogosultságokkal rendelkeznek a megosztáshoz.

### <a name="set-up-an-msix-app-attach-share"></a>MSIX-alkalmazás csatlakoztatása megosztás létrehozása

A Windows rendszerű virtuális asztali környezetben hozzon létre egy hálózati megosztást, és helyezze át a csomagot.

>[!NOTE]
> A MSIX hálózati megosztások létrehozásához ajánlott eljárás az, ha a hálózati megosztást NTFS írásvédett engedélyekkel állítja be.

## <a name="install-certificates"></a>Tanúsítványok telepítése

Ha az alkalmazás olyan tanúsítványt használ, amely nem nyilvánosan megbízható vagy önaláírt, a következő módon telepítheti:

1. Kattintson a jobb gombbal a csomagra, és válassza a **Tulajdonságok**lehetőséget.
2. A megjelenő ablakban válassza a **digitális aláírások** fület. A lapon csak egy elemnek kell szerepelnie a listán, ahogy az az alábbi képen is látható. Válassza ki az elemet az elem kiemeléséhez, majd válassza a **részletek**lehetőséget.
3. Amikor megjelenik a digitális aláírás részletei ablak, válassza az **általános** lapot, majd válassza a **Tanúsítvány megtekintése**lehetőséget, majd válassza a **tanúsítvány telepítése**lehetőséget.
4. A telepítő megnyitásakor válassza a **helyi gép** lehetőséget a tárolási helyként, majd kattintson a **tovább**gombra.
5. Ha a telepítő megkérdezi, hogy engedélyezni szeretné-e az alkalmazásnak az eszköz módosítását, válassza az **Igen**lehetőséget.
6. Válassza **a minden tanúsítvány tárolása a következő tárolóban**lehetőséget, majd válassza a **Tallózás**lehetőséget.
7. Amikor megjelenik a tanúsítványtároló kiválasztása ablak, válassza a **Megbízható személyek**lehetőséget, majd kattintson **az OK gombra**.
8. Válassza a **tovább** , majd a **Befejezés**lehetőséget.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>PowerShell-parancsfájlok előkészítése MSIX-alkalmazás csatolásához

A MSIX-alkalmazás csatolása négy különböző fázist tartalmaz, amelyeket a következő sorrendben kell végrehajtani:

1. Fázis
2. Regisztráció
3. Képes
4. Kilépés

Minden fázis létrehoz egy PowerShell-szkriptet. Az egyes fázisok mintául szolgáló parancsfájljai [itt](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)érhetők el.

### <a name="stage-powershell-script"></a>Szakasz PowerShell-parancsfájl

A PowerShell-parancsfájlok frissítése előtt ellenőrizze, hogy rendelkezik-e a kötet GUID azonosítóval a VHD-ben. A kötet GUID-azonosítójának lekérése:

1.  Nyissa meg azt a hálózati megosztást, ahol a VHD abban a virtuális gépen található, amelyen futtatni fogja a parancsfájlt.

2.  Kattintson a jobb gombbal a virtuális merevlemezre, és válassza a **Csatlakoztatás**lehetőséget. Ezzel csatlakoztatja a virtuális merevlemezt egy meghajtóbetűjelhez.

3.  Miután csatlakoztatta a VHD-t, megnyílik a **fájlkezelő** ablak. A szülőmappa rögzítése és a **$parentFolder** változó frissítése

    >[!NOTE]
    >Ha nem lát szülő mappát, az azt jelenti, hogy a MSIX nem lett megfelelően kibontva. Ismételje meg az előző szakaszt, és próbálkozzon újra.

4.  Nyissa meg a szülőmappa mappát. Ha a megfelelően ki van bontva, egy olyan mappa jelenik meg, amelynek a neve megegyezik a csomag nevével. Frissítse a **$packageName** változót, hogy megegyezzen a mappa nevével.

    Például: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Nyisson meg egy parancssort, és adja meg a **mountvol**parancsot. A parancs megjeleníti a kötetek listáját és a GUID azonosítókat. Másolja ki annak a kötetnek a GUID azonosítóját, amelyben a meghajtóbetűjel megegyezik azzal a meghajtóval, amellyel a virtuális merevlemezt csatlakoztatta a 2. lépésben.

    Például a mountvol parancs kimenetében, ha a virtuális merevlemezt a C meghajtóhoz csatlakoztatta, a fenti értéket át kell másolnia `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Frissítse a **$volumeGuid** változót az imént másolt kötet GUID azonosítóval.

7. Nyisson meg egy rendszergazdai PowerShell-parancssort, és frissítse a következő PowerShell-parancsfájlt a környezetre vonatkozó változókkal.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell-parancsfájl regisztrálása

A regisztrálási parancsfájl futtatásához futtassa a következő PowerShell-parancsmagokat a helyőrző értékekkel, amelyek az adott környezetre érvényes értékekkel lesznek lecserélve.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>PowerShell-parancsfájl regisztrációjának törlése

Ehhez a parancsfájlhoz cserélje le a **$packageName** helyőrzőjét a tesztelni kívánt csomag nevére.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>PowerShell-parancsfájl lefázisa

Ehhez a parancsfájlhoz cserélje le a **$packageName** helyőrzőjét a tesztelni kívánt csomag nevére. Éles környezetben a legjobb megoldás a leállításkor.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Szimulációs parancsfájlok beállítása a MSIX alkalmazáshoz csatolható ügynökhöz

A parancsfájlok létrehozása után a felhasználók manuálisan futtathatják azokat, vagy beállíthatja, hogy a rendszer automatikusan futtassa az indítási, bejelentkezési, kijelentkezési és leállítási parancsfájlok futtatását. Az ilyen típusú parancsfájlokkal kapcsolatos további információkért lásd: [indítási, leállítási, bejelentkezési és kijelentkezési parancsfájlok használata Csoportházirendban](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Ezen automatikus parancsfájlok mindegyike az alkalmazás egy fázisát futtatja a parancsfájlok csatolásához:

- Az indítási parancsfájl futtatja a szakasz parancsfájlját.
- A bejelentkezési parancsfájl a regisztrálási parancsfájlt futtatja.
- A kijelentkezési parancsfájl a deregister szkriptet futtatja.
- A leállítási parancsfájl futtatja a kilépési parancsfájlt.

## <a name="use-packages-offline"></a>Csomagok használata kapcsolat nélküli üzemmódban

Ha olyan csomagokat használ a [Microsoft Store for Business](https://businessstore.microsoft.com/) vagy a [Microsoft Store for Education](https://educationstore.microsoft.com/) szolgáltatásban a hálózaton vagy az internethez nem csatlakozó eszközökön, le kell kérnie a csomag licenceit a Microsoft Store, és telepítenie kell őket az eszközre az alkalmazás sikeres futtatásához. Ha az eszköz online állapotban van, és kapcsolódni tud a vállalati Microsoft Storehoz, a szükséges licenceket automatikusan le kell tölteni, de ha offline állapotban van, manuálisan kell beállítania a licenceket.

A licencfájl telepítéséhez egy PowerShell-szkriptet kell használnia, amely meghívja a MDM_EnterpriseModernAppManagement_StoreLicenses02_01 osztályt a WMI Bridge-szolgáltatóban.

A következő módon állíthatja be a licenceket offline használatra:

1. Töltse le az alkalmazáscsomag, a licencek és a szükséges keretrendszereket a vállalati Microsoft Store. A kódolt és a titkosítatlan licencek is szükségesek. Részletes letöltési utasítások [itt](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)találhatók.
2. Frissítse a következő változókat a 3. lépéshez tartozó parancsfájlban:
      1. `$contentID` a ContentID értéke a nem kódolt licencfájl (. xml). A licencfájl megnyitható egy tetszőleges szövegszerkesztőben.
      2. `$licenseBlob` a a titkosított licencfájl (. bin) teljes karakterlánca a licenc blobjának. A kódolt licencfájl megnyitható egy tetszőleges szövegszerkesztőben.
3. Futtassa a következő parancsfájlt egy rendszergazdai PowerShell-parancssorból. A licencek telepítésének megfelelő helye az [átmeneti parancsfájl](#stage-powershell-script) végén található, amelyet rendszergazdai parancssorból is futtatni kell.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Következő lépések

Ez a funkció jelenleg nem támogatott, de kérdéseket tehet fel a Közösségnek a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

A Windows rendszerű virtuális asztalok visszajelzéseit a [Windows Virtual Desktop visszajelzési központja](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)is elhagyhatja.
