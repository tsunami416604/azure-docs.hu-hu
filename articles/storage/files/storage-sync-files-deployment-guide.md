---
title: Azure fájlszinkronizálás (előzetes verzió) telepítése |} Microsoft Docs
description: Megtudhatja, hogyan telepítése Azure fájlszinkronizálás, így tovább.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: wgries
ms.openlocfilehash: f1230cbc4d654bfb59bb328ed7d75c6fa76ff10c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268247"
---
# <a name="deploy-azure-file-sync-preview"></a>Azure fájlszinkronizálás (előzetes verzió) telepítése
Sync szolgáltatás használatával Azure fájl (előzetes verzió) központosítása fájlmegosztások a szervezet Azure fájlokban, ugyanakkor változatlanul megőrizze a rugalmasság, a teljesítmény és a kompatibilitási egy helyszíni fájlkiszolgáló. Azure fájlszinkronizálás átalakítja a Windows Server az Azure fájlmegosztás gyors gyorsítótárába. Minden protokoll, amely a Windows Server helyileg, az adatok eléréséhez használhatja, többek között a ftps-t, SMB és NFS. Akkor is annyi gyorsítótárak világszerte szükség szerint.

Határozottan javasoljuk, hogy olvassa el [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md) és [Azure fájlszinkronizálás központi telepítésének tervezésében](storage-sync-files-planning.md) a jelen cikkben ismertetett lépések végrehajtása előtt.

## <a name="prerequisites"></a>Előfeltételek
* Egy Azure storage-fiókot és egy Azure fájlmegosztás, amely Azure fájlszinkronizálás telepíteni szeretné ugyanabban a régióban. További információkért lásd:
    - [Régiónkénti elérhetőség](storage-sync-files-planning.md#region-availability) az Azure-fájl szinkronizálás.
    - [Hozzon létre egy tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a storage-fiók létrehozásáról részletes leírását.
    - [Fájlmegosztás létrehozása](storage-how-to-create-file-share.md) fájlmegosztás létrehozásáról részletes leírását.
* Legalább egy támogatott Windows Server vagy a Windows Server-fürt Azure fájlszinkronizálás-vel való szinkronizálásának példányát. Windows Server támogatott verzióival kapcsolatos további információkért lásd: [együttműködés a Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).
* Győződjön meg arról, a Windows Server telepítve van a PowerShell 5.1. Windows Server 2012 R2 használatakor győződjön meg arról, hogy futtatja legalább PowerShell 5.1. \*. Biztonságosan kihagyhatja ezt az ellenőrzést, a Windows Server 2016, mert a PowerShell 5.1 az alapértelmezett verzió out-of-box. A Windows Server 2012 R2 ellenőrizheti, hogy futnak-e PowerShell 5.1. \* értékének megnézzük a **PSVersion** tulajdonsága a **$PSVersionTable** objektum:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Ha a PSVersion érték kisebb, mint 5.1. \*, mint lesz a legtöbb friss telepítés a Windows Server 2012 R2 esetében, ha letölti és telepíti könnyedén frissíthet [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). A megfelelő csomag letöltése és telepítése a Windows Server 2012 R2 **Win8.1AndW2K12R2 KB-os\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Az Azure fájlszinkronizálás egyelőre nem támogatják a PowerShell 6 + Windows Server 2012 R2 vagy Windows Server 2016.
* Az Azure-fájl szinkronizálással használni kívánt kiszolgálókon AzureRM PowerShell-modult. A AzureRM PowerShell-modul telepítéséről további információkért lásd: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Mindig ajánlott az Azure PowerShell-modulok legújabb verzióját használja. 

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Windows Server használata Azure fájlszinkronizálás előkészítése
Minden olyan kiszolgálón, az Azure-fájl szinkronizálással, többek között az egyes kiszolgáló-csomópont egy feladatátvevő fürtben használni kívánt letiltása **Internet Explorer fokozott biztonsági beállításai**. Ez azért szükséges, csak a kezdeti kiszolgáló regisztrálása. Engedélyezheti újra azt követően a kiszolgáló regisztrálva van.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
1. Nyissa meg a Kiszolgálókezelőt.
2. Kattintson a **helyi kiszolgáló**:  
    !["Helyi kiszolgáló" a kiszolgáló-kezelő felhasználói felületén bal oldalán](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Az a **tulajdonságok** subpane, jelölje ki a kapcsolatot a **Internet Explorer fokozott biztonsági beállítások**.  
    ![A Kiszolgálókezelő felhasználói felületén a "Internet Explorer fokozott biztonsági beállítások" ablak](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Az a **Internet Explorer fokozott biztonsági beállításai** párbeszédpanelen jelölje ki **ki** a **rendszergazdák** és **felhasználók**:  
    ![Az Internet Explorer fokozott biztonsági beállításai pop-ablak "Ki" a kijelölt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Az Internet Explorer fokozott biztonsági beállításai letiltásához hajtsa végre az alábbiakat egy rendszergazda jogú PowerShell-munkamenetben:

```PowerShell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="install-the-azure-file-sync-agent"></a>Az Azure fájl Sync-ügynök telepítése
Az Azure fájlszinkronizálás ügynök csomag egy letölthető, amely lehetővé teszi a Windows Server az Azure fájlmegosztások szinkronizálhatóak. 

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Az ügynököt a programot letöltheti a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). A letöltés befejeződése után kattintson duplán az MSI-csomagot az Azure fájlszinkronizálás ügynök telepítésének indításához.

> [!Important]  
> Ha szeretne használni az Azure fájlszinkronizálás feladatátvevő fürttel, az Azure fájlszinkronizálás ügynököt telepíteni kell a fürt minden csomópontján.

Azt javasoljuk, tegye a következőket:
- Hagyja meg az alapértelmezett telepítési útvonal (C:\Program Files\Azure\StorageSyncAgent), egyszerűbbé teheti a hibaelhárítás és a kiszolgáló.
- Engedélyezheti a Microsoft Update Azure fájlszinkronizálás naprakészen tartásához. Minden frissítés Azure fájlszinkronizálás ügynökkel, beleértve a szolgáltatás-frissítéseket és gyorsjavításokat, fordulhat elő, a Microsoft Update szolgáltatásból. Azt javasoljuk, hogy a legújabb frissítés telepítése az Azure fájl Sync. További információkért lásd: [Azure fájlszinkronizálás házirend](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Ha az Azure fájl Sync-ügynök telepítése befejeződött, a kiszolgáló regisztrációs felhasználói felülete automatikusan megnyílik. Rendelkeznie kell egy tároló szinkronizálási szolgáltatás előtt registrating; egy tároló szinkronizálási szolgáltatás létrehozása a következő szakaszban olvashat.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Futtassa a következő PowerShell-kódot töltse le a megfelelő verzióját a fájlszinkronizálás Azure-ügynököt, hogy az operációs rendszer, és telepítse a rendszeren.

```PowerShell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875004 `
        -OutFile "StorageSyncAgent.exe" 
}
elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875002 `
        -OutFile "StorageSyncAgent.exe" 
}
else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2 and Windows Server 2016")
}

# Extract the MSI from the install package
$tempFolder = New-Item -Path "afstemp" -ItemType Directory
Start-Process -FilePath ".\StorageSyncAgent.exe" -ArgumentList "/C /T:$tempFolder" -Wait

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "$($tempFolder.FullName)\StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.exe", ".\afstemp" -Recurse -Force
```

---

## <a name="deploy-the-storage-sync-service"></a>A tároló szinkronizálási szolgáltatás telepítése 
A központi telepítés Azure fájlszinkronizálás kezdődik helyezi el a **tároló szinkronizálási szolgáltatás** be egy erőforráscsoport a kijelölt előfizetés erőforrás. Azt javasoljuk, ezek kevés kiépítés igény szerint. A kiszolgálók és az erőforrás közötti megbízhatósági kapcsolat fog létrehozni, és egy is csak regisztrálni a kiszolgálót egy tároló szinkronizálási szolgáltatás. Ennek eredményeképpen javasoljuk, hogy annyi tárolási szinkronizálási szolgáltatásokat, kiszolgálócsoportok kell telepíteni. Ne feledje, hogy a kiszolgálók, különböző tároló szinkronizálási szolgáltatásokból egymással nem tudja szinkronizálni.

> [!Note]
> A tároló szinkronizálási szolgáltatás hozzáférési engedélyek öröklődik a előfizetésben és erőforráscsoportban történő üzembe helyezéséig. Azt javasoljuk, hogy alaposan ellenőrizze ki férhet hozzá. Írási hozzáféréssel rendelkező entitások megkezdheti a kiszolgálók regisztrálva az új fájlcsoport szinkronizálása a tároló a szinkronizálási szolgáltatás, és a számukra elérhető az Azure storage áramló adatok okozzák.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Tárolási szinkronizálási szolgáltatás telepítéséhez keresse fel a [Azure-portálon](https://portal.azure.com/), kattintson a *új* és az Azure-fájl szinkronizálás kereshet. A keresési eredmények között, válassza ki **Azure fájlszinkronizálás (előzetes verzió)**, majd válassza ki **létrehozása** megnyitásához a **tárolási szinkronizáló telepítése** lapon.

A megjelenő ablakban adja meg a következő adatokat:

- **Név**: a tároló szinkronizálási szolgáltatást (előfizetésenként) egy egyedi nevet.
- **Előfizetés**: az előfizetés, amelyen létrehozásához a tároló szinkronizálási szolgáltatást. Attól függően, hogy a szervezet konfigurációs stratégia lehetséges, hogy egy vagy több előfizetés elérésére. Azure-előfizetés számlázási minden felhőszolgáltatás (például az Azure-fájlokat) a legalapvetőbb tárolója.
- **Erőforráscsoport**: erőforráscsoport egy olyan logikai csoport az Azure-erőforrások, például a storage-fiók vagy egy tároló szinkronizálási szolgáltatás. Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az Azure-fájl szinkronizálás. (Javasoljuk erőforráscsoportok tárolóként különítenie erőforrásokat logikailag a szervezete számára, például a HR-erőforrások vagy egy adott projekt erőforrások csoportosítása.)
- **Hely**: A régió, ahol telepítendő Azure fájlszinkronizálás. Csak a támogatott régiók érhetők el ebben a listában.

Ha elkészült, válassza ki a **létrehozása** a tároló szinkronizálási szolgáltatás telepítése.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Az Azure fájlszinkronizálás parancsmagokat való interakció, előtt kell importálni a dll-fájl, és hozzon létre egy Azure fájlszinkronizálás felügyeleti környezetben. Ez azért szükséges, mert a Azure fájlszinkronizálás parancsmagokat még nincsenek a AzureRM PowerShell modulja részét.

> [!Note]  
> A StorageSync.Management.PowerShell.Cmdlets.dll csomag, amely tartalmazza az Azure fájlszinkronizálás parancsmagokat, (szándékosan) tartalmazza a parancsmag egy jóváhagyott műveletet (`Login`). A név `Login-AzureRmStorageSync` egyező választása a `Login-AzureRmAccount` a AzureRM PowerShell modul aliasát. A hibaüzenet (és a parancsmag) törlődni fognak az Azure fájlszinkronizálás ügynök hozzáadódik a AzureRM PowerShell-modult.

```PowerShell
$acctInfo = Login-AzureRmAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzureRmLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzureRmResourceGroup -Name $resourceGroup -Location $region
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

Miután létrehozta az Azure fájlszinkronizálás keretében a `Login-AzureRmStorageSync` parancsmaggal hozhat létre a tároló szinkronizálási szolgáltatást. Ügyeljen arra, hogy a csere `<my-storage-sync-service>` a tároló szinkronizálási szolgáltatás kívánt nevét.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server regisztrálása tároló szinkronizálási szolgáltatás
A Windows-kiszolgáló regisztrálása a tárolási szinkronizálási szolgáltatás kapcsolatot hoz létre megbízhatósági kapcsolat a kiszolgáló (vagy a fürt) és a tároló szinkronizálási szolgáltatást. A kiszolgáló csak egy tároló szinkronizálási szolgáltatással regisztrálhatók és szinkronizálhatják más kiszolgálók és az Azure fájlmegosztások a azonos tárolási szinkronizálási szolgáltatással kapcsolatos.

> [!Note]
> Kiszolgáló regisztrálása az Azure hitelesítő adatokat használ a tárolási szinkronizálási szolgáltatás és a Windows Server, azonban ezt követően a kiszolgáló hoz létre, és hogy saját identitást használja, amely érvényes, amíg a kiszolgáló marad regisztrált közötti megbízhatósági kapcsolat létrehozásához és a aktuális közös hozzáférésű Jogosultságkód (SAS-tároló) token érvényes. Egy új SAS-jogkivonat nem adható ki számára a kiszolgálón a kiszolgáló regisztrációjának törlése, így az a kiszolgáló képes az Azure fájlmegosztások elérésére, a szinkronizálás eltávolítása után.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
A kiszolgáló regisztrációs felhasználói felülete automatikusan meg kell az Azure fájl Sync-ügynök telepítése után. Ha nem, manuálisan is megnyithatja, a fájl helyéről: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Amikor megnyílik a regisztrációs kiszolgáló felhasználói Felületét, válassza ki a **bejelentkezési** megkezdéséhez.

Miután bejelentkezik, a következő információkat kéri:

![Egy a kiszolgáló regisztrációs felhasználói felületének képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés**: az előfizetés, amely tartalmazza a tároló szinkronizálási szolgáltatást (lásd: [a tároló szinkronizálási szolgáltatás telepítése](#deploy-the-storage-sync-service)). 
- **Erőforráscsoport**: a tároló szinkronizálási szolgáltatást tartalmazó erőforráscsoportot.
- **Tároló szinkronizálási szolgáltatás**: a tároló szinkronizálási szolgáltatás, amellyel a regisztrálni kívánt nevét.

Miután kiválasztotta a megfelelő adatokat, válassza ki a **regisztrálása** befejezéséhez a kiszolgáló regisztrálása. A regisztrációs folyamat részeként kéri egy további bejelentkezés.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>A szinkronizálás csoport és a felhőbeli végpont létrehozása
A szinkronizálás csoport adja meg a sync-topológiát a fájlokat. A szinkronizálási csoporton belüli végpontok tartják szinkronban vannak egymással. Szinkronizálási csoport tartalmaznia kell legalább egy felhő végpont, amely jelöli az Azure fájlmegosztások és az egyik vagy a kiszolgáló végpontok. A kiszolgáló végpont egy regisztrált kiszolgáló elérési útját jelöli. Egy kiszolgáló több szinkronizálási csoportok server-végpontokat is lehet. Szükség van a megfelelő ismertetik a kívánt szinkronizálási topológia annyi szinkronizálási csoportokat hozhat létre.

A felhő-végpont esetében az Azure fájlmegosztások mutató hivatkozások. Minden kiszolgáló végpontok egy felhőalapú végponttal, így a felhőbeli végpont a központ szinkronizálja. A tárfiók Azure fájlmegosztás a tároló szinkronizálási szolgáltatás ugyanabban a régióban kell lennie. A teljes Azure fájlmegosztás szinkronizálja, egy kivétellel: speciális mappa, a rejtett "System Volume Information" mappa NTFS-köteten, hasonló lesz üzembe helyezve. Ez a könyvtár neve ". SystemShareInformation". Fontos szinkronizálási metaadatok között, a többi végpont szinkronizálva nem lesz tartalmaz. Ne használjon, vagy törölni!

> [!Important]  
> Felhő endpoint vagy a szinkronizálási csoport végpontját módosításokat végezheti el, és a fájlok szinkronizálta már a sync csoport végpontja. Ha módosítja a felhő végpontnak (Azure fájlmegosztás) közvetlenül, módosítások először kell egy Azure fájlszinkronizálás módosítás észlelése észlelhetők. A módosítás észlelése a felhőbeli végpont 24 óránként csak egyszer indítható. További információkért lásd: [Azure fájlok gyakran ismételt kérdések](storage-files-faq.md#afs-change-detection).

# <a name="portaltabportal"></a>[Portal](#tab/portal)
A szinkronizálás csoport létrehozásakor a [Azure-portálon](https://portal.azure.com/)nyissa meg a tároló szinkronizálási szolgáltatással, és válassza ki **+ szinkronizálású csoport**:

![Hozzon létre egy új szinkronizálási csoportot az Azure-portálon](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

A megnyíló párbeszédpanelen írja be a felhő végponttal szinkronizálási csoport létrehozása a következő információkat:

- **Szinkronizálási csoportnév**: létrehozni a szinkronizálási csoport nevét. Ez a név a tároló szinkronizálási szolgáltatás belül egyedieknek kell lenniük, de bármilyen nevet, amely logikai meg.
- **Előfizetés**: az előfizetést, amelybe telepítette a tároló szinkronizálási szolgáltatás a [a tároló szinkronizálási szolgáltatás telepítése](#deploy-the-storage-sync-service).
- **A tárfiók**: Ha **válasszon tárfiókot**, egy másik ablak jelenik meg, ahol kiválaszthatja a tárfiókot, amely rendelkezik a szinkronizálni kívánt Azure fájlmegosztás.
- **Azure-fájlmegosztáshoz**:, amellyel szinkronizálni kívánt Azure fájlmegosztás nevét.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
A szinkronizálási csoport létrehozásához hajtsa végre a következő PowerShell. Ne felejtse el lecserélni `<my-sync-group>` a kívánt néven a szinkronizálási csoport.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

Miután a szinkronizálás csoport sikeresen létrejött, a felhőbeli végpont hozhatók létre. Ügyeljen arra, hogy a csere `<my-storage-account>` és `<my-file-share>` a várt értékkel.

```PowerShell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzureRmStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzureStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Kiszolgáló-végpont létrehozása
Egy kiszolgáló végpont képviseli regisztrált kiszolgálón, például egy mappát egy olyan kiszolgáló köteten egy konkrét helyre. Egy kiszolgáló végpont egy regisztrált kiszolgáló (ahelyett, hogy a csatlakoztatott megosztást) az elérési utat kell, és a felhő rétegezéséhez használatához az elérési út egy nem rendszer-köteten kell lennie. Csatlakoztatott hálózati tárolóeszközök (NAS) nem támogatott.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Vegyen fel egy kiszolgáló végpontot, nyissa meg az újonnan létrehozott szinkronizálási csoporthoz, és válassza ki **server végpont hozzáadása**.

![Új kiszolgáló végpont hozzáadása a szinkronizálási ablakban](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Az a **server végpont hozzáadása** panelen adja meg a kiszolgáló-végpont létrehozása a következő információkat:

- **Regisztrált kiszolgáló**: a kiszolgáló vagy fürt, ahol szeretne létrehozni a kiszolgáló végpont nevét.
- **Elérési út**: A Windows Server elérési útját a szinkronizálási csoport részeként szinkronizálva.
- **A felhő Tiering**: engedélyezni vagy letiltani a felhő egy kapcsoló rétegezéséhez. A felhőalapú rétegezéséhez, a ritkán használt vagy nem érhető el fájlok is helyezhető el a az Azure Fileshoz.
- **Szabad hely**: a kiszolgáló végpont tároló kötet tartalékként lefoglalni kívánt szabad terület mennyisége. Például ha a kötet szabad terület értéke 50 %-os köteten, amely egy önálló kiszolgáló végponttal rendelkezik, körülbelül fél adatmennyiség többszintű az Azure Fileshoz. Függetlenül attól, hogy a felhő rétegezéséhez engedélyezve van, az Azure fájlmegosztások mindig rendelkezik az adatok teljes másolata a szinkronizálási csoportban.

A kiszolgáló végpont hozzáadásához válassza **létrehozása**. A fájlok vannak most szinkronban legyen az Azure fájlmegosztás és a Windows Server. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
A következő PowerShell-parancsok a kiszolgáló végpont létrehozásához, és cserélje le kell `<your-server-endpoint-path>` és `<your-volume-free-space>` a kívánt értékekkel.

```PowerShell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering $true `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
}
else {
    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Bevezetés az Azure File-szinkronizálással
A javasolt lépéseket érheti az Azure fájlszinkronizálás az első az állásidő nulla teljes fájl rögzített adatainak megőrzése mellett, és hozzáférés-vezérlési lista (ACL) a következők:
 
1. A tároló szinkronizálási szolgáltatás telepítése.
2. Hozzon létre egy szinkronizálási csoportot.
3. Azure fájl Sync-ügynök telepítése a kiszolgálón, a teljes adatkészlet.
4. Regisztrálja a kiszolgálót, és a kiszolgáló-végpont létrehozása a megosztáson. 
5. Tegye a teljes feltöltése az Azure fájlmegosztások (felhővégpontnak) szinkronizálás segítségével.  
6. A kezdeti feltöltés befejeződése után Azure fájlszinkronizálás ügynök telepítése minden egyes többi kiszolgálóját.
7. Új fájlmegosztásokat hozhat létre mindegyik a többi kiszolgálón.
8. Szükség esetén hozzon létre server végpontok felhő rétegzési házirendet, az új fájlmegosztások. (Ez a lépés szükséges további tárhely szükséges a kezdeti beállítás.)
9. A teljes névtér nélkül a tényleges adatátvitel gyors helyreállításával Azure fájlszinkronizálás ügynök segítségével. Névtér teljes szinkronizálása után a szinkronizálási motor betelik a helyi lemezterületet a felhő rétegzési házirendet a kiszolgáló végpont alapján. 
10. Győződjön meg arról, szinkronizálás befejeződött, és tesztelje a topológia a kívánt módon működjenek. 
11. Átirányítás a felhasználók és az alkalmazások erre a megosztásra.
12. Opcionálisan törölheti bármely ismétlődő megosztások a kiszolgálókon.
 
Ha nem rendelkezik a kezdeti bevezetése – megnövelt tárhely és a meglévő megosztások csatlakoztatni szeretne, akkor is előre feltöltheti az adatokat az Azure fájlmegosztásokat. Javasolt ezt a módszert használja, csak ha fogadja el az állásidőt, és feltétlenül garantálja az adatok változzon, a kiszolgálómegosztásokon a kezdeti bevezetési folyamat során. 
 
1. Győződjön meg arról, hogy bármely kiszolgálón lévő adatok a bevezetési folyamat során nem módosítható.
2. Előre vetőmag Azure fájlmegosztások bármely átviteli eszköz például az SMB protokollt használó kiszolgáló-adatokkal Robocopy, a közvetlen SMB-másolásra. Mivel az AzCopy nem feltölteni az adatokat az SMB-n keresztül, az előre összehangolása nem használható.
3. Hozzon létre Azure fájlszinkronizálás topológia a kívánt kiszolgálóhoz végpontok a meglévő megosztások mutat.
4. Egyeztetés folyamat összes végponton Befejezés szinkronizálás segítségével. 
5. Egyeztetés befejezése után megnyithatja a megosztások változásait.
 
Felhívjuk a figyelmét arra, hogy jelenleg előzetes összehangolása megközelítés korlátai néhány- 
- A fájlok teljes visszaadása nem őrződik meg. Például a fájlok ACL-EK és időbélyeg helyi időre megszakad.
- Adatok módosításait a kiszolgálóra, mielőtt a szinkronizálási topológia teljes körűen működik-e és fut a kiszolgáló végpontok az ütközéseket okozhat.  
- A felhőbeli végpont létrehozását követően a Azure fájlszinkronizálás futtat egy folyamatot, a fájlok észleléséhez a felhőben a kezdeti szinkronizálás indítása előtt. A folyamat befejezéséhez szükséges idő függ a számos tényező befolyásolja, például a hálózat sebességétől, a rendelkezésre álló sávszélesség és a fájlok és mappák száma. Az előzetes kiadásban nyers becsléséhez észlelési folyamat során lefuttat körülbelül 10 fájlok/mp.  Ezért, akkor is, ha előre összehangolása gyors fut, a teljes idő lekérni a teljes futó rendszer jelentősen hosszabb lehet a felhőben előzetesen kiemelt nem használt adatok.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) központi telepítés Azure fájlszinkronizálás áttelepítése
A DFS-R-telepítés át Azure fájlszinkronizálás:

1. Hozzon létre egy szinkronizálási lecseréli a DFS-R-topológia ábrázolására.
2. Indítsa el a kiszolgálónak a teljes adatkészletet a DFS-R topológia áttelepítéséhez. Telepítse az Azure fájlszinkronizálás ezen a kiszolgálón.
3. Regisztrálja a kiszolgálót, és át kell az első kiszolgáló kiszolgáló-végpont létrehozása. Ne engedélyezze a felhő rétegezéséhez.
4. Minden az adatok szinkronizálása az Azure fájlmegosztásba (felhővégpontnak) segítségével.
5. Telepíti és regisztrálja az Azure fájlszinkronizálás ügynököt minden olyan a többi DFS-R-kiszolgálójára.
6. Tiltsa le a DFS-R. 
7. Kiszolgáló-végpont létrehozása minden olyan a DFS-R-kiszolgálójára. Ne engedélyezze a felhő rétegezéséhez.
8. Győződjön meg arról, szinkronizálás befejeződött, és tesztelje a topológia a kívánt módon működjenek.
9. A DFS-r kivonása
10. A felhő rétegezési előfordulhat, hogy minden kiszolgáló végponton tetszés szerint engedélyezni kell.

További információkért lásd: [Azure fájlszinkronizálás együttműködési az elosztott fájlrendszer (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>További lépések
- [Hozzáadni vagy eltávolítani egy Azure-beli fájl szinkronizáló kiszolgáló végpont](storage-sync-files-server-endpoint.md)
- [Regisztrálja, vagy a kiszolgáló regisztrációját az Azure fájlszinkronizálás](storage-sync-files-server-registration.md)