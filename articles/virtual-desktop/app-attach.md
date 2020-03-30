---
title: Windows Virtual Desktop MSIX alkalmazás csatolása – Azure
description: Az MSIX alkalmazás csatolása a Windows Virtual Desktop rendszerhez beállítása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128316"
---
# <a name="set-up-msix-app-attach"></a>MSIX-alkalmazás csatolásának beállítása

> [!IMPORTANT]
> Az MSIX alkalmazáscsatolás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ez a témakör bemutatja, hogyan állíthatja be az MSIX alkalmazáscsatolást Windows virtuális asztali környezetben.

## <a name="requirements"></a>Követelmények

Mielőtt elkezdene, az alábbiakat kell konfigurálnia az MSIX alkalmazáscsatoláshoz:

- A Windows Insider portálhoz való hozzáférés hez a Windows 10 verziójának beszerzéséhez az MSIX alkalmazás API-k csatolásával.
- A Windows virtuális asztal működő központi telepítése. További információt a Bérlő létrehozása a Windows virtuális asztalon című [témakörben talál.](tenant-setup-azure-active-directory.md)
- Az MSIX csomagolóeszköz
- Hálózati megosztás a Windows virtuális asztal központi telepítésében, ahol az MSIX csomag tárolásra kerül

## <a name="get-the-os-image"></a>Az operációs rendszer lemezképének beszereznie

Először is be kell szereznie az MSIX alkalmazáshoz használni kívánt operációsrendszer-lemezképet. Az operációs rendszer lemezképének beszerezése:

1. Nyissa meg a [Windows Insider portált,](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) és jelentkezzen be.

     >[!NOTE]
     >A Windows Insider portál eléréséhez a Windows Insider program tagjának kell lennie. Ha többet szeretne megtudni a Windows Insider programról, tekintse meg a [Windows Insider dokumentációját.](/windows-insider/at-home/)

2. Görgessen le a **Select edition** szakaszhoz, és válassza a **Windows 10 Insider Preview Enterprise (FAST) – Build 19035** vagy újabb lehetőséget.

3. Válassza **a Megerősítés**lehetőséget, majd jelölje ki a használni kívánt nyelvet, majd kattintson ismét **a Megerősítés** gombra.
    
     >[!NOTE]
     >Jelenleg az angol az egyetlen nyelv, amelyet teszteltek a funkcióval. Más nyelveket is választhat, de előfordulhat, hogy azok nem jelennek meg a kívánt nak.
    
4. A letöltési hivatkozás létrehozásakor válassza a **64 bites letöltés lehetőséget,** és mentse a helyi merevlemezre.

## <a name="prepare-the-vhd-image-for-azure"></a>A VHD-lemezkép előkészítése az Azure-hoz 

Mielőtt elkezdene, létre kell hoznia egy fő virtuális merevlemez-lemezképet. Ha még nem hozta létre a fő virtuális merevlemez-lemezképet, nyissa [meg a Fő virtuális merevlemez-lemezkép előkészítése és testreszabása című](set-up-customize-master-image.md) területet, és kövesse az utasításokat. 

Miután létrehozta a fő Virtuális merevlemez-lemezképet, le kell tiltania az MSIX alkalmazáscsatolási alkalmazások automatikus frissítéseit. Az automatikus frissítések letiltásához a következő parancsokat kell futtatnia egy rendszergazda jogú parancssorban:

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

Ezután készítse elő a virtuális gép virtuális merevlemezét az Azure-hoz, és töltse fel az eredményül kapott Virtuálismerevlemez-lemezt az Azure-ba. További információ: [Mester virtuális merevlemez-lemezkép előkészítése és testreszabása](set-up-customize-master-image.md).

Miután feltöltötte a virtuális merevlemezt az Azure-ba, hozzon létre egy gazdakészletet, amely ezen az új lemezképen alapul, kövesse a [Host Pool létrehozása az Azure Marketplace-oktatóanyag használatával.](create-host-pools-azure-marketplace.md)

## <a name="prepare-the-application-for-msix-app-attach"></a>Az alkalmazás előkészítése az MSIX alkalmazáscsatoláshoz 

Ha már rendelkezik MSIX csomaggal, ugorjon előre [a Windows virtuális asztali infrastruktúra konfigurálása](#configure-windows-virtual-desktop-infrastructure)című verzióra. Ha szeretné tesztelni a régebbi alkalmazásokat, kövesse az [MSIX-csomag létrehozása egy virtuális gép asztali telepítőjéből](/windows/msix/packaging-tool/create-app-package-msi-vm/) című témakör utasításait az örökölt alkalmazás MSIX-csomaggá alakításához.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Virtuális merevlemez- vagy VHDX-csomag létrehozása az MSIX-hez

A csomagok VHD vagy VHDX formátumúak a teljesítmény optimalizálása érdekében. Az MSIX megfelelő működéséhez Virtuális merevlemez- vagy VHDX-csomagokszükségesek.

Virtuális merevlemez- vagy VHDX-csomag létrehozása az MSIX-hez:

1. [Töltse le a msixmgr eszközt,](https://aka.ms/msixmgr) és mentse a .zip mappát egy munkamenetgazda virtuális gépén belüli mappába.

2. Csomagolja ki az msixmgr eszköz .zip mappáját.

3. Helyezze a forrás MSIX csomagot ugyanabba a mappába, ahol kicsomagolta a msixmgr eszközt.

4. Virtuális merevlemez létrehozásához futtassa a következő parancsmast a PowerShellben:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Győződjön meg arról, hogy a virtuális merevlemez mérete elég nagy ahhoz, hogy a kibővített MSIX-et megtartsa.*

5. Az újonnan létrehozott virtuális merevlemez csatlakoztatásához futtassa a következő parancsmast:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Futtassa ezt a parancsmast a virtuális merevlemez inicializálásához:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Futtassa ezt a parancsmast egy új partíció létrehozásához:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. A partíció formázásához futtassa ezt a parancsmamot:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Hozzon létre egy szülőmappát a csatlakoztatott virtuális merevlemezen. Ez a lépés kötelező, mivel az MSIX alkalmazás csatolása szülőmappát igényel. A szülőmappát tetszés szerint elnevezheti.

### <a name="expand-msix"></a>MSIX kibontása

Ezt követően ki kell "bővítenie" az MSIX-képet a kicsomagolással. Az MSIX-kép kicsomagolása:

1. Nyisson meg egy parancssort rendszergazdaként, és keresse meg azt a mappát, amelyben letöltötte és kicsomagolta a msixmgr eszközt.

2. Futtassa a következő parancsmast az MSIX kicsomagolásához az előző szakaszban létrehozott és csatlakoztatott virtuális merevlemezbe.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A kicsomagolás után a következő üzenetnek kell megjelennie:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Ha a Hálózaton belül a Microsoft Store vállalati verzióból (vagy oktatási programból) vagy az internethez nem kapcsolódó eszközökön használja a csomagokat, be kell szereznie a csomaglicenceket az Áruházból, és telepítenie kell őket az alkalmazás sikeres futtatásához. Lásd: [Csomagok használata kapcsolat nélküli módban.](#use-packages-offline)

3. Nyissa meg a csatlakoztatott virtuális merevlemezt, nyissa meg az alkalmazásmappát, és ellenőrizze, hogy a csomag tartalma jelen van-e.

4. Válassza le a virtuális merevlemezt.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>A Windows virtuális asztali infrastruktúra konfigurálása

A tervezés szerint egyetlen MSIX kibővített csomag (az előző szakaszban létrehozott virtuális merevlemez) több munkamenetgazda virtuális gép között osztható meg, mivel a virtuális merevlemezek csak olvasható módban vannak csatolva.

Mielőtt elkezdené, győződjön meg arról, hogy a hálózati megosztás megfelel az alábbi követelményeknek:

- A megosztás SMB-kompatibilis.
- A munkamenetgazda-készlet részét használó virtuális gépek NTFS-engedélyekkel rendelkeznek a megosztáshoz.

### <a name="set-up-an-msix-app-attach-share"></a>MSIX-alkalmazás csatolási megosztásának beállítása 

A Windows virtuális asztal környezetben hozzon létre egy hálózati megosztást, és helyezze át oda a csomagot.

>[!NOTE]
> Az MSIX hálózati megosztások létrehozásának ajánlott gyakorlata a hálózati megosztás beállítása az NTFS írásvédett engedélyekkel.

## <a name="install-certificates"></a>Tanúsítványok telepítése

Ha az alkalmazás olyan tanúsítványt használ, amely nem megbízható, vagy önaláírt, a következőképpen telepítheti:

1. Kattintson a jobb gombbal a csomagra, és válassza **a Tulajdonságok parancsot.**
2. A megjelenő ablakban válassza a **Digitális aláírások** lapot. A lap listájában csak egy elem lehet, ahogy az az alábbi képen látható. Jelölje ki az elemet az elem kiemeléséhez, majd válassza a **Részletek lehetőséget.**
3. Amikor megjelenik a digitális aláírás részletei ablak, válassza az **Általános** lapot, majd a **Tanúsítvány telepítése**lehetőséget.
4. Amikor a telepítő megnyílik, válassza ki a **helyi gépet** tárolási helyként, majd válassza a **Tovább**gombot.
5. Ha a telepítő megkérdezi, hogy engedélyezi-e az alkalmazásnak az eszköz módosítását, válassza az **Igen**lehetőséget.
6. Válassza **az Összes tanúsítvány helye a következő tárolóban**lehetőséget, majd a **Tallózás**gombot.
7. Amikor megjelenik a tanúsítványtároló kijelölése ablak, válassza **a Megbízható személyek**lehetőséget, majd az **OK**gombot.
8. Válassza a **Finish** (Befejezés) elemet.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>PowerShell-parancsfájlok előkészítése az MSIX alkalmazáscsatoláshoz

Az MSIX alkalmazáscsatolás négy különböző fázissal rendelkezik, amelyeket a következő sorrendben kell végrehajtani:

1. Fázis
2. Regisztráljon
3. Regisztráció megszüntetése
4. Destage

Minden fázis létrehoz egy PowerShell-parancsfájlt. Az egyes fázisok mintaparancsfájljai [itt](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)érhetők el.

### <a name="stage-the-powershell-script"></a>A PowerShell-parancsfájl szakasza

A PowerShell-parancsfájlok frissítése előtt győződjön meg arról, hogy a kötet GUID a virtuális merevlemezen. A kötet GUID azonosítójának beszerezése:

1.  Nyissa meg a hálózati megosztást, ahol a virtuális merevlemez található a virtuális gép, ahol futtatni fogja a parancsfájlt.

2.  Kattintson a jobb gombbal a virtuális merevlemezre, és válassza a **Csatlakoztatás parancsot.** Ezzel a virtuális merevlemezt egy meghajtóbetűjelhez csatlakoztatja.

3.  A virtuális merevlemez csatlakoztatása után megnyílik a **Fájlkezelő** ablak. A szülőmappa rögzítése és a **$parentFolder** változó frissítése

    >[!NOTE]
    >Ha nem látható szülőmappa, az azt jelenti, hogy az MSIX nem volt megfelelően kibontva. Ismételje meg az előző szakaszt, majd próbálkozzon újra.

4.  Nyissa meg a szülőmappát. Ha helyesen van kibontva, megjelenik egy mappa, amelynek neve megegyezik a csomag nevével. Frissítse a **$packageName** változót, hogy megegyezzen a mappa nevével.

    Például: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Nyisson meg egy parancssort, és írja be **a mountvol parancsot.** Ez a parancs megjeleníti a kötetek listáját és guid azonosítóit. Másolja annak a kötetnek a GUID azonosítóját, amelyhez a meghajtóbetűjel megegyezik azzal a meghajtóval, amelyhez a virtuális merevlemezt csatlakoztatta a 2.

    Ha például ebben a példában a mountvol parancs kimenetét adja, a virtuális merevlemezt a `C:\`C meghajtóhoz csatlakoztatta, akkor a fenti értéket szeretné másolni:

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

7. Nyisson meg egy rendszergazdai PowerShell-kérdést, és frissítse a következő PowerShell-parancsfájlt a környezetére vonatkozó változókkal.

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

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell-parancsfájl regisztrálása

A regiszterparancsfájl futtatásához futtassa a következő PowerShell-parancsmagokat a környezetre vonatkozó értékekre cserélt helyőrző értékekkel.

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

### <a name="deregister-powershell-script"></a>A PowerShell-parancsfájl regisztrációjának megszüntetése

Ehhez a parancsfájlhoz cserélje le **a $packageName** helyőrzőjének nevét a tesztelt csomag nevére.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>PowerShell-parancsfájl destage-jának lealacsonyulása

Ehhez a parancsfájlhoz cserélje le **a $packageName** helyőrzőjének nevét a tesztelt csomag nevére.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Szimulációs parancsfájlok beállítása az MSIX alkalmazás csatolási ügynökhöz

A parancsfájlok létrehozása után a felhasználók manuálisan futtathatják őket, vagy beállíthatják, hogy automatikusan fussanak indítási, bejelentkezési, kijelentkezési és leállítási parancsfájlként. Az ilyen típusú parancsfájlokról a [Csoportházirend indítási, leállítási, bejelentkezési és kijelentkezési parancsfájljainak használata című témakörben olvashat bővebben.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)

Az automatikus parancsfájlok mindegyike az alkalmazás csatolási parancsfájljainak egy fázisát futtatja:

- Az indítási parancsfájl futtatja a színpadi parancsfájlt.
- A bejelentkezési parancsfájl futtatja a regiszterparancsfájlt.
- A kijelentkezési parancsfájl futtatja a regisztráció törlését parancsfájl.
- A leállítási parancsfájl futtatja a destage parancsfájlt.

## <a name="use-packages-offline"></a>Csomagok használata kapcsolat nélküli módban

Ha a [Hálózaton](https://businessstore.microsoft.com/) vagy a Microsoft [Store for Education](https://educationstore.microsoft.com/) áruházból vagy a hálózathoz nem kapcsolódó eszközökön használja a csomagokat, be kell szereznie a csomaglicenceket a Microsoft Store-ból, és telepítenie kell őket az eszközre az alkalmazás sikeres futtatásához. Ha az eszköz online állapotban van, és csatlakozni tud a Microsoft Store vállalati verzióhoz, a szükséges licencek nek automatikusan le kell töltődniük, de ha offline állapotban van, manuálisan kell beállítania a licenceket. 

A licencfájlok telepítéséhez olyan PowerShell-parancsfájlt kell használnia, amely a WMI-hídszolgáltató MDM_EnterpriseModernAppManagement_StoreLicenses02_01 osztályát hívja meg.  

A licenceket a következőképpen állíthatja be kapcsolat nélküli használatra: 

1. Töltse le az alkalmazáscsomagot, a licenceket és a szükséges keretrendszereket a Microsoft Store vállalati verzióból. Mind a kódolt, mind a kódolatlan licencfájlokra szükség van. Részletes letöltési útmutató [itt](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)található .
2. Frissítse a következő változókat a parancsfájlban a 3.
      1. `$contentID`A Nem kódolt licencfájl (.xml) ContentID értéke. A licencfájlt megnyithatja egy ön által választott szövegszerkesztőben.
      2. `$licenseBlob`A licencblob teljes karakterlánca a kódolt licencfájlban (.bin). A kódolt licencfájlt megnyithatja egy általa döngistált szövegszerkesztőben. 
3. Futtassa a következő parancsfájlt egy felügyeleti PowerShell-parancssorból. A licenctelepítés végrehajtásához jó hely az [átmeneti parancsfájl](#stage-the-powershell-script) végén található, amelyet egy rendszergazdai parancsból is futtatni kell.

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

## <a name="next-steps"></a>További lépések

Ez a szolgáltatás jelenleg nem támogatott, de kérdéseket tehet fel a közösségnek a [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)rendszerben.

A Windows virtuális asztalhoz visszajelzést is hagyhat a [Windows virtuális asztal visszajelzési központjában,](https://aka.ms/MRSFeedbackHub)vagy az MSIX alkalmazás- és csomagolási eszközhöz visszajelzést is hagyhat az [MSIX alkalmazás visszajelzési központjában](https://aka.ms/msixappattachfeedback) és az [MSIX csomagolóeszköz visszajelzési központjában.](https://aka.ms/msixtoolfeedback)
