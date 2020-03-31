---
title: Azure File Sync telepítése | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az Azure File Sync-et az elejétől a végéig.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268066"
---
# <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése
Az Azure File Sync használatával központosíthatja a szervezet fájlmegosztásait az Azure Files ban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Server kiszolgálón elérhető bármely protokoll thasználhat az adatok helyi eléréséhez, beleértve az SMB, az NFS és az FTPS protokollt. Annyi gyorsítótára lehet, amennyire szüksége van szerte a világon.

Azt javasoljuk, hogy olvassa el [az Azure-fájlok üzembe helyezésének megtervezése](storage-files-planning.md) és [az Azure-fájlszinkronizálás iüzembe helyezésének megtervezése](storage-sync-files-planning.md) című olvasni, mielőtt végrehajtaná a cikkben ismertetett lépéseket.

## <a name="prerequisites"></a>Előfeltételek
* Egy Azure-fájlmegosztás ugyanabban a régióban, amely az Azure File Sync üzembe helyezéséhez. További információ:
    - Az Azure File Sync [régióelérhetősége.](storage-sync-files-planning.md#azure-file-sync-region-availability)
    - [Fájlmegosztás létrehozása](storage-how-to-create-file-share.md) a fájlmegosztás létrehozásának lépésről lépésre ismertetéséhez.
* Az Azure File Sync szolgáltatással való szinkronizáláshoz legalább egy támogatott Windows Server vagy Windows Server fürt példánya. A Windows Server támogatott verzióiról a [Windows Server rel való együttműködés](storage-sync-files-planning.md#windows-file-server-considerations)című témakörben talál további információt.
* Az Az PowerShell-modul a PowerShell 5.1 vagy a PowerShell 6+-hoz használható. Az Azure File Sync Az PowerShell modult bármely támogatott rendszeren használhatja, beleértve a nem Windows rendszereket is, azonban a kiszolgáló regisztrációs parancsmagját mindig a regisztrálandó Windows Server-példányon kell futtatnia (ez közvetlenül vagy a PowerShellen keresztül is elvégezhető a moting). Windows Server 2012 R2 rendszeren ellenőrizheti, hogy legalább a PowerShell 5.1-es rendszert futtatja-e. \* a **$PSVersionTable** objektum **PSVersion** tulajdonságának értékével:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Ha a PSVersion értéke kisebb, mint 5.1. \*, csakúgy, mint a Windows Server 2012 R2 legtöbb friss telepítése, a [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)letöltésével és telepítésével könnyedén frissíthet. A Windows Server 2012 R2 rendszerhez letölthető és telepíthető csomag a **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    A PowerShell 6+ bármely támogatott rendszerrel használható, és a [GitHub-oldalán](https://github.com/PowerShell/PowerShell#get-powershell)keresztül tölthető le. 

    > [!Important]  
    > Ha azt tervezi, hogy a Server Registration UI, ahelyett, hogy közvetlenül a PowerShell, a PowerShell 5.1-es használata kell használnia.

* Ha a PowerShell 5.1 használata mellett döntött, győződjön meg arról, hogy legalább a .NET 4.7.2 telepítve van. További információ a [.NET Framework verzióiról és](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) a rendszerfüggőségeiről.

    > [!Important]  
    > Ha a .NET 4.7.2+ rendszert telepíti a Windows `quiet` Server `norestart` Core rendszerre, akkor a és a jelzőkkel kell telepítenie, különben a telepítés sikertelen lesz. A .NET 4.8 telepítésekor például a parancs a következőkre néz ki:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Az Az PowerShell-modul, amely az alábbi utasításokat követve telepíthető: [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Az Az.StorageSync modul automatikusan települ az Az PowerShell modul telepítésekor.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>A Windows Server előkészítése az Azure File Sync használatára
Az Azure File Sync szolgáltatással használni kívánt kiszolgálók esetében, beleértve a feladatátvevő fürt minden kiszolgálócsomópontját, tiltsa le **az Internet Explorer fokozott biztonsági beállításait.** Ez csak a kiszolgáló kezdeti regisztrációhoz szükséges. A kiszolgáló regisztrációja után újra engedélyezheti.

# <a name="portal"></a>[Portál](#tab/azure-portal)
> [!Note]  
> Ezt a lépést kihagyhatja, ha az Azure File Sync szolgáltatást windows Server Core rendszeren telepíti.

1. Nyissa meg a Kiszolgálókezelőt.
2. Kattintson **a Helyi kiszolgáló elemre:**  
    ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. A **Tulajdonságok** lapon, válassza ki az **Internet Explorer fokozott biztonsági beállításai** hivatkozást.  
    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Az **Internet Explorer fokozott biztonsági beállításai** párbeszédpanelen válassza a Ki **lehetőséget** **rendszergazdáknak** és **felhasználóknak**:  
    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Az Internet Explorer fokozott biztonsági beállításainak letiltásához hajtsa végre az alábbi beállításokat emelt szintű PowerShell-munkamenetből:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>A Társzinkronizálási szolgáltatás üzembe helyezése 
Az Azure File Sync üzembe helyezése azzal kezdődik, hogy egy **Storage Sync Service** erőforrást helyez a kiválasztott előfizetés erőforráscsoportjába. Azt javasoljuk, hogy szükség szerint csak néhányat kell kiépíteni. Megbízhatósági kapcsolatot hoz létre a kiszolgálók és az erőforrás között, és a kiszolgáló csak egy tárolószinkronizálási szolgáltatáshoz regisztrálható. Ennek eredményeképpen ajánlott annyi tárolási szinkronizálási szolgáltatást telepíteni, amennyi a kiszolgálócsoportok elkülönítéséhez szükséges. Ne feledje, hogy a különböző tárolószinkronizálási szolgáltatásokkiszolgálói nem szinkronizálhatók egymással.

> [!Note]
> A Storage Sync Service örökli a hozzáférési engedélyeket az előfizetésés erőforráscsoport, amelybe telepítve van. Javasoljuk, hogy gondosan ellenőrizze, hogy ki férhet hozzá. Az írási hozzáféréssel rendelkező entitások elindíthatják a tárolószinkronizálási szolgáltatásba regisztrált kiszolgálókról származó új fájlkészletek szinkronizálását, és az adatok áramlását az Azure storage-ba, amely elérhető számukra.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Storage Sync Service üzembe helyezéséhez nyissa meg az [Azure Portalon,](https://portal.azure.com/)kattintson az erőforrás létrehozása elemre, és keresse meg az Azure File Sync.To deploy a Storage Sync Service, go to the Azure Portal , click *Create an resource* and search for Azure File Sync. A keresési eredmények között válassza az **Azure File Sync**lehetőséget, majd a **Létrehozás** lehetőséget a **Tárterület-szinkronizálás telepítéséhez** lapon.

A megnyíló panelen adja meg a következőket:

- **Név**: Egyedi név (előfizetésenként) a Storage Sync Szolgáltatáshoz.
- **Előfizetés**: Az az előfizetés, amelyben létre szeretné hozni a Storage Sync Szolgáltatást. A szervezet konfigurációs stratégiájától függően előfordulhat, hogy egy vagy több előfizetéshez is hozzáférhet. Az Azure-előfizetés az egyes felhőszolgáltatások (például az Azure Files) számlázásának legalapvetőbb tárolója.
- **Erőforráscsoport:** Az erőforráscsoport az Azure-erőforrások logikai csoportja, például egy tárfiók vagy egy storage-szinkronizálási szolgáltatás. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévő erőforráscsoportot az Azure File Sync számára. (Azt javasoljuk, hogy erőforráscsoportokat használjon tárolóként a szervezet erőforrásainak logikai elkülönítéséhez, például hr-erőforrások vagy erőforrások csoportosításához egy adott projekthez.)
- **Hely:** Az a régió, amelyben telepíteni szeretné az Azure File Sync-et. Ebben a listában csak támogatott régiók érhetők el.

Ha végzett, válassza a **Létrehozás** lehetőséget a Storage Sync szolgáltatás központi telepítéséhez.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Cserélje `<Az_Region>` `<RG_Name>`le `<my_storage_sync_service>` a , és a saját értékeit, majd használja a következő parancsokat, hogy hozzon létre és telepítsen egy Storage Sync Service:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
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
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az ügynököt letöltheti a [Microsoft letöltőközpontból.](https://go.microsoft.com/fwlink/?linkid=858257) Amikor a letöltés befejeződött, kattintson duplán az MSI-csomagra az Azure File Sync ügynök telepítésének elindításához.

> [!Important]  
> Ha az Azure File Sync egy feladatátvevő fürttel kívánja használni, az Azure File Sync ügynök kell telepíteni a fürt minden csomópontján. A fürt minden csomópontját regisztrálni kell az Azure File Sync használatával való munkához.

Javasoljuk, hogy tegye a következőket:
- Hagyja meg az alapértelmezett telepítési útvonalat (C:\Program Files\Azure\StorageSyncAgent), hogy egyszerűsítse a hibaelhárítást és a kiszolgáló karbantartását.
- Engedélyezze a Microsoft Update szolgáltatást, hogy az Azure File Sync naprakész maradjon. Az Azure File Sync ügynök összes frissítése, beleértve a szolgáltatásfrissítéseket és a gyorsjavításokat, a Microsoft Update szolgáltatásból származik. Javasoljuk, hogy telepítse a legújabb frissítést az Azure File Sync. További információt az [Azure File Sync frissítési szabályzata című témakörben talál.](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)

Amikor az Azure File Sync ügynök telepítése befejeződött, a kiszolgáló regisztrációs felhasználói felületautomatikusan megnyílik. A regisztráció előtt storage-szinkronizálási szolgáltatással kell rendelkeznie; A Storage Sync Service létrehozásáról szóló következő szakaszban.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Hajtsa végre a következő PowerShell-kódot az operációs rendszerhez az Azure File Sync ügynök megfelelő verziójának letöltéséhez, és telepítse azt a rendszerre.

> [!Important]  
> Ha az Azure File Sync egy feladatátvevő fürttel kívánja használni, az Azure File Sync ügynök kell telepíteni a fürt minden csomópontján. A fürt minden csomópontjának regisztrálnia kell az Azure File Sync használatával való munkához.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows-kiszolgáló regisztrálása a Társzinkronizálási szolgáltatással
A Windows Server regisztrálásával a Társzinkronizálási szolgáltatásra megbízhatósági kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a Társzinkronizálási szolgáltatás között. A kiszolgáló csak egy Társzinkronizálási szolgáltatásra regisztrálható és az ugyanahhoz a Társzinkronizálási szolgáltatáshoz társított kiszolgálókkal és Azure-fájlmegosztásokkal képes szinkronizálni.

> [!Note]
> A kiszolgálóregisztráció az Azure-hitelesítő adatok alapján hoz létre megbízhatósági kapcsolatot a Storage Sync Service és a Windows Server között, azonban ezt követően a kiszolgáló saját identitást hoz létre és használ, amely mindaddig érvényes, amíg a kiszolgáló regisztrálva marad, és a az aktuális megosztott hozzáférésű aláírási jogkivonat (Storage SAS) érvényes. Új SAS-jogkivonat nem adható ki a kiszolgálónak, ha a kiszolgáló nincs regisztrálva, így eltávolítja a kiszolgáló azon képességét, hogy hozzáférjen az Azure-fájlmegosztásokhoz, és leállítja a szinkronizálást.

# <a name="portal"></a>[Portál](#tab/azure-portal)
A kiszolgáló regisztrációs felhasználói felülete automatikusan meg nyílik az Azure File Sync ügynök telepítése után. Ha nem, akkor manuálisan is megnyithatja, a fájl helye: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Amikor megnyílik a kiszolgálóregisztrációs felhasználói felület, válassza **a Bejelentkezés** lehetőséget a kezdéshez.

A bejelentkezés után a rendszer a következő információkat kéri:

![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés**: A Storage Sync szolgáltatást tartalmazó előfizetés [(lásd: A storage sync szolgáltatás telepítése).](#deploy-the-storage-sync-service) 
- **Erőforráscsoport**: A Storage Sync szolgáltatást tartalmazó erőforráscsoport.
- **Storage Sync Service**: Annak a tárolószinkronizálási szolgáltatásnak a neve, amelyhez regisztrálni szeretne.

Miután kiválasztotta a megfelelő adatokat, válassza a **Regisztráció** lehetőséget a kiszolgáló regisztrációjának befejezéséhez. A regisztrációs folyamat részeként a rendszer újabb bejelentkezésre kéri fel.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Szinkronizálási csoport és felhő végpont létrehozása
A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. A szinkronizálási csoportnak tartalmaznia kell egy felhőbeli végpontot, amely Azure-fájlmegosztást és egy vagy több kiszolgáló végpontot jelöl. A kiszolgálóvégpont egy regisztrált kiszolgáló elérési útját jelöli. A kiszolgálók több szinkronizálási csoportban is rendelkezhetnek kiszolgálóvégpontokkal. Annyi szinkronizálási csoportot hozhat létre, amennyi a kívánt szinkronizálási topológia megfelelő leírásához szükséges.

A felhővégpont egy Azure-fájlmegosztásra mutató mutató. Az összes kiszolgálóvégpont szinkronizálódik egy felhővégpontdal, így a felhővégpont lesz a hub. Az Azure-fájlmegosztás tárfiókjának ugyanabban a régióban kell lennie, mint a Storage Sync Szolgáltatás. Az Azure-fájlmegosztás teljes egészében szinkronizálva lesz, egy kivétellel: Egy speciális mappa, amely összehasonlítható az NTFS-kötet rejtett "Rendszerkötet-információ" mappával, ki lesz építve. Ezt a könyvtárat ". SystemShareInformation ". Fontos szinkronizálási metaadatokat tartalmaz, amelyek nem szinkronizálódnak más végpontokkal. Ne használja vagy törölje!

> [!Important]  
> A szinkronizálási csoport bármely felhővégpontját vagy kiszolgálóvégpontját módosíthatja, és a fájlokat szinkronizálhatja a szinkronizálási csoport többi végpontjával. Ha közvetlenül módosítja a felhővégpontot (Azure-fájlmegosztás), a módosításokat először egy Azure File Sync változásészlelési feladatnak kell felderítenie. A változásészlelési feladat csak 24 óránként egyszer indul el egy felhővégponthoz. További információt az [Azure Files gyakori kérdései című témakörben talál.](storage-files-faq.md#afs-change-detection)

# <a name="portal"></a>[Portál](#tab/azure-portal)
Szinkronizálási csoport létrehozásához az [Azure Portalon](https://portal.azure.com/)nyissa meg a Storage Sync Service webhelyet, és válassza a **+ Szinkronizálás csoport lehetőséget:**

![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

A megnyíló panelen adja meg a következő információkat a szinkronizálási csoport létrehozásához egy felhőbeli végponttal:

- **Szinkronizálási csoport neve**: A létrehozandó szinkronizálási csoport neve. A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név.
- **Előfizetés**: Az az előfizetés, amelyen a Storage Sync Szolgáltatást a [Storage Sync Szolgáltatás telepítése korosított a szolgáltatás üzembe helyezésében](#deploy-the-storage-sync-service)helyezte üzembe.
- **Tárfiók:** Ha **a Tárfiók kiválasztása lehetőséget választja,** egy másik ablaktábla jelenik meg, amelyben kiválaszthatja azt a tárfiókot, amely rendelkezik az Azure-fájlmegosztással, amellyel szinkronizálni szeretne.
- **Azure-fájlmegosztás:** Annak az Azure-fájlmegosztásnak a neve, amellyel szinkronizálni szeretne.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
A szinkronizálási csoport létrehozásához hajtsa végre a következő PowerShell. Ne felejtse el lecserélni `<my-sync-group>` a szinkronizálási csoport kívánt nevére.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

A szinkronizálási csoport sikeres létrehozása után létrehozhatja a felhővégpontot. Ügyeljen arra, `<my-storage-account>` `<my-file-share>` hogy cserélje ki, és a várt értékeket.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Kiszolgálói végpont létrehozása
A kiszolgálói végpont a regisztrált kiszolgálón egy konkrét helyet jelöl, például egy mappát egy kiszolgálói köteten. A kiszolgálóvégpontnak egy regisztrált kiszolgálón lévő elérési útnak kell lennie (nem csatlakoztatott megosztáson), és a felhőrétegezés használatához az elérési útnak nem rendszerköteten kell lennie. A hálózati csatlakoztatott tároló (NAS) nem támogatott.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Kiszolgálóvégpont hozzáadásához lépjen az újonnan létrehozott szinkronizálási csoportra, és válassza **a Kiszolgálóvégpont hozzáadása lehetőséget.**

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

A **Kiszolgálói végpont felvétele** panelen adja meg a következő információkat a kiszolgálói végpont létrehozásához:

- **Regisztrált kiszolgáló**: Annak a kiszolgálónak vagy fürtnek a neve, amelyhez a kiszolgálóvégpontot létre kívánja hozni.
- **Elérési út**: A szinkronizálási csoport részeként szinkronizálandó Windows Server elérési út.
- **Felhőrétegezés**: A felhőrétegezés engedélyezésére vagy letiltására irányuló kapcsoló. A felhőrétegezés, ritkán használt vagy használt fájlok at lehet rétegzett Azure Files.
- **Kötetszabad terület:** Azon a köteten, amelyen a kiszolgálóvégpont található, lefoglalandó szabad terület. Ha például a kötet szabad területe 50%, egy olyan köteten, amely egyetlen kiszolgálóvégpontot tartalmaz, az Azure Files-hoz tartozó adatok nagyjából fele. Függetlenül attól, hogy a felhőrétegezés engedélyezve van-e, az Azure-fájlmegosztás mindig rendelkezik a szinkronizálási csoportban lévő adatok teljes másolatával.

A kiszolgálóvégpont hozzáadásához válassza a **Létrehozás gombot.** A fájlok szinkronizálása az Azure-fájlmegosztásban és a Windows Server rendszerben. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Hajtsa végre a következő PowerShell-parancsokat a `<your-server-endpoint-path>` kiszolgálóvégpont létrehozásához, és győződjön meg róla, hogy lecseréli és `<your-volume-free-space>` a kívánt értékeket.

```powershell
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
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Tűzfal- és virtuális hálózati beállítások konfigurálása

### <a name="portal"></a>Portál
Ha úgy szeretné konfigurálni az Azure-fájlszinkronizálást, hogy az működjön a tűzfal- és a virtuálishálózati beállításokkal, tegye a következőket:

1. Az Azure Portalon keresse meg a biztonságostárfiókot.
1. Válassza a bal oldali menü **Tűzfalak és virtuális hálózatok** gombját.
1. Válassza a **Kijelölt hálózatok** lehetőséget a Hozzáférés engedélyezése **innen csoportban.**
1. Győződjön meg arról, hogy a kiszolgálók IP-címe vagy virtuális hálózata a megfelelő szakaszban szerepel.
1. Győződjön meg **arról, hogy a megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a tárfiókhoz** jelölőnégyzet be van jelölve.
1. A beállítások mentéséhez válassza a **Mentés** gombot.

![Tűzfal- és virtuálishálózati beállítások konfigurálása az Azure File sync használatával](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Bevezetés az Azure File Sync segítségével
Az Azure File Sync alkalmazásának ajánlott lépései az első nulla állásidővel, a teljes fájlhűség és a hozzáférés-vezérlési lista (ACL) megőrzése mellett a következők:
 
1. Storage Sync szolgáltatás üzembe helyezése.
2. Hozzon létre egy szinkronizálási csoportot.
3. Telepítse az Azure File Sync agent a kiszolgálón a teljes adatkészlettel.
4. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálóvégpontot a megosztáson. 
5. Hagyja, hogy a szinkronizálás végezze el a teljes feltöltést az Azure-fájlmegosztásra (felhővégpont).  
6. A kezdeti feltöltés befejezése után telepítse az Azure File Sync-ügynököt a többi kiszolgálóra.
7. Hozzon létre új fájlmegosztásokat a többi kiszolgálón.
8. Szükség esetén hozzon létre kiszolgálóvégpontokat az új fájlmegosztásokon a felhőrétegezési szabályzattal. (Ehhez a lépéshez további tárhelyre van szükség a kezdeti telepítéshez.)
9. Hagyja, hogy az Azure File Sync ügynök gyorsan visszaállítsa a teljes névteret a tényleges adatátvitel nélkül. A teljes névtér szinkronizálása után a szinkronizálási motor a kiszolgálóvégpont felhőrétegezési házirendje alapján tölti ki a helyi lemezterületet. 
10. Győződjön meg arról, hogy a szinkronizálás befejeződött, és tesztelje a topológiát a kívánt módon. 
11. Irányítsa át a felhasználókat és az alkalmazásokat erre az új megosztásra.
12. A kiszolgálókon törölheti az ismétlődő megosztásokat.
 
Ha nem rendelkezik extra tárhellyel a kezdeti bevezetéshez, és szeretné csatolni a meglévő megosztások, előre üzembe az adatokat az Azure-fájlok megosztások. Ez a megközelítés akkor és csak akkor javasolt, ha elfogadja az állásidőt, és teljes mértékben garantálja, hogy a kezdeti bevezetési folyamat során a kiszolgáló megosztásokon ne változzon adatváltozás. 
 
1. Győződjön meg arról, hogy a kiszolgálók on-kon lévő adatok nem módosíthatók a bevezetési folyamat során.
2. Az Azure-fájl megosztások előre a kiszolgáló idatain keresztül bármely adatátviteli eszköz segítségével az SMB-n keresztül, például Robocopy, közvetlen SMB-példány. Mivel az AzCopy nem tölt fel adatokat az SMB-n keresztül, így nem használható előre vetéshez.
3. Hozzon létre Azure File Sync topológiát a kívánt kiszolgálói végpontokkal a meglévő megosztásokra mutatva.
4. Hagyja, hogy a szinkronizálás az egyeztetési folyamat befejezése legyen az összes végponton. 
5. Az egyeztetés befejezése után megnyithatja a megosztásokat a módosításokhoz.
 
Jelenleg az elővetési megközelítésnek néhány 
- A fájlok teljes hűsége nem őrződik meg. A fájlok például elveszítik az ACL-eket és az időbélyegeket.
- A kiszolgálón a szinkronizálási topoológia teljes működése előtt végrehajtott adatmódosítások ütközéseket okozhatnak a kiszolgáló végpontjain.  
- A felhővégpont létrehozása után az Azure File Sync egy folyamatot futtat a felhőben lévő fájlok észlelésére a kezdeti szinkronizálás megkezdése előtt. A folyamat befejezéséhez szükséges idő a különböző tényezőktől, például a hálózati sebességtől, a rendelkezésre álló sávszélességtől, valamint a fájlok és mappák számától függ. A durva becslés az előzetes kiadásban, észlelési folyamat fut körülbelül 10 fájl/mp.  Ezért még akkor is, ha az elővetés gyorsan fut, a teljes munkaidős rendszer betöltéséhez szükséges teljes idő jelentősen hosszabb lehet, ha az adatok előre vannak elvetve a felhőben.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Önkiszolgáló visszaállítás a korábbi verziókon és a VSS-en (Kötet árnyékmásolata szolgáltatás)

> [!IMPORTANT]
> A következő információk csak a storage sync ügynök 9-es (vagy újabb) verziójával használhatók. A 9-nél alacsonyabb verziók nem rendelkeznek a StorageSyncSelfService parancsmagokkal.

A korábbi verziók egy Windows-szolgáltatás, amely lehetővé teszi, hogy egy kötet kiszolgálóoldali VSS pillanatképeit használja a fájl helyreállítható verzióinak egy SMB-ügyfélnek történő bemutatásához.
Ez lehetővé teszi, hogy egy hatékony forgatókönyv, közkeletű nevén önkiszolgáló visszaállítás, közvetlenül az információs dolgozók helyett attól függően, hogy a visszaállítás egy informatikai rendszergazda.

A VSS-pillanatképek és a korábbi verziók az Azure File Sync-től függetlenül működnek. A felhőrétegezést azonban kompatibilis módra kell állítani. Sok Azure File Sync kiszolgálói végpont létezhet ugyanazon a köteten. A következő PowerShell-hívást kell kezdeményeznie olyan kötetenként, amely még egy kiszolgálói végpontot is rendelkezik, ahol felhőrétegezést tervez vagy használ.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-pillanatképek egy teljes kötetet. Alapértelmezés szerint legfeljebb 64 pillanatképek létezhetnek egy adott kötethez, feladva van elég hely a pillanatképek tárolására. A VSS ezt automatikusan kezeli. Az alapértelmezett pillanatkép-ütemezés naponta két pillanatképet készít, hétfőtől péntekig. Ez az ütemezés egy Windows ütemezett feladaton keresztül konfigurálható. A fenti PowerShell-parancsmag két dolgot tesz:
1. Úgy konfigurálja az Azure File Syncs felhőrétegezésa a megadott köteten, hogy kompatibilis a korábbi verziók, és garantálja, hogy a fájl visszaállítható egy korábbi verzió, akkor is, ha a felhőbe a kiszolgálón. 
2. Engedélyezi az alapértelmezett VSS-ütemezést. Ezután később dönthet úgy, hogy módosítja. 

> [!Note]  
> Két fontos dolgot kell megjegyezni:
>- Ha a -Force paramétert használja, és a VSS jelenleg engedélyezve van, akkor felülírja az aktuális VSS pillanatkép-ütemezést, és lecseréli az alapértelmezett ütemezésre. Győződjön meg arról, hogy mentse az egyéni konfigurációt a parancsmag futtatása előtt.
> - Ha ezt a parancsmamot fürtcsomóponton használja, akkor azt a fürt összes többi csomópontján is futtatnia kell! 

Annak érdekében, hogy az önkiszolgáló visszaállítás kompatibilitásengedélyezve van-e, futtathatja a következő parancsmast.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Felsorolja az összes kötetet a kiszolgálón, valamint a felhőrétegezéskompatibilis napok számát. Ez a szám automatikusan kiszámításra a kötetenkénti maximális pillanatképek és az alapértelmezett pillanatkép-ütemezés alapján történik. Így alapértelmezés szerint az összes korábbi verziók egy információs dolgozó lehet használni a visszaállításhoz. Ugyanez igaz, ha módosítja az alapértelmezett ütemezést, hogy több pillanatképet.
Ha azonban úgy módosítja az ütemezést, hogy az elérhető pillanatkép jelenik meg a köteten, amely régebbi, mint a kompatibilis napok értéke, akkor a felhasználók nem fogják tudni használni ezt a régebbi pillanatképet (előző verzió) a visszaállításhoz.

> [!Note]
> Az önkiszolgáló visszaállítás engedélyezése hatással lehet az Azure-tárhely felhasználására és számlájára. Ez a hatás a kiszolgálón jelenleg rétegzett fájlokra korlátozódik. A szolgáltatás engedélyezése biztosítja, hogy van egy fájlverzió elérhető a felhőben, amely egy korábbi verziók (VSS pillanatkép) bejegyzésen keresztül hivatkozhat.
>
> Ha letiltja a funkciót, az Azure-tárhely felhasználás a kompatibilis napok ablaka elteltéig lassan csökkenni fog. Ezt nem lehet felgyorsítani. 

A vss-pillanatképek alapértelmezett maximális száma kötetenként (64), valamint az alapértelmezett ütemezés, hogy azokat, eredményeképpleg legfeljebb 45 nappal a korábbi verziók egy információs feldolgozó visszaállíthatja, attól függően, hogy hány VSS pillanatképek tárolhatja a köteten.

Ha max. 64 VSS pillanatképek kötetenként nem a megfelelő beállítás az Ön számára, meg lehet [változtatni, hogy](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)az érték egy rendszerleíró kulcs .
Az új korlát érvénybe léptetéséhez újra kell futtatnia a parancsmast, hogy a korábbi verziókompatibilitás tegyék lehetővé minden olyan köteten, amely korábban engedélyezve volt, a -Force jelzővel, hogy figyelembe vegye a vss-pillanatképek új maximális számát kötetenként. Ez a kompatibilis napok újonnan kiszámított számát eredményezi. Kérjük, vegye figyelembe, hogy ez a módosítás csak az újonnan rétegzett fájlokra lép érvénybe, és felülírja az esetlegesen végrehajtott VSS-ütemezés testreszabásait.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Elosztott fájlrendszer-replikációs szolgáltatás (DFS-R) telepítésének áttelepítése az Azure File Sync szolgáltatásba
DFS-R központi telepítésének áttelepítése az Azure File Sync szolgáltatásba:

1. Hozzon létre egy szinkronizálási csoportot, amely a lecserélendő DFS-R topológiát ábrázolja.
2. Indítsa el azon a kiszolgálón, amely a DFS-R topológiában lévő összes adatot áttelepíti. Telepítse az Azure File Sync-et a kiszolgálóra.
3. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálóvégpontot az első telepítendő kiszolgálóhoz. Ne engedélyezze a felhőrétegezést.
4. Hagyja, hogy az összes adat szinkronizálja az Azure-fájlmegosztás (felhővégpont).
5. Telepítse és regisztrálja az Azure File Sync ügynök minden fennmaradó DFS-R-kiszolgálókon.
6. Tiltsa le az ELHAszn-r letiltását. 
7. Hozzon létre egy kiszolgálóvégpontot az egyes DFS-R kiszolgálókon. Ne engedélyezze a felhőrétegezést.
8. Győződjön meg arról, hogy a szinkronizálás befejeződött, és tesztelje a topológiát a kívánt módon.
9. Dfs-R nyugdíjba vonulása.
10. Felhőrétegezés most már engedélyezve lehet bármely kiszolgálói végponton igény szerint.

További információ: [Azure File Sync interop with Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>További lépések
- [Azure File Sync Server endpoint hozzáadása vagy eltávolítása](storage-sync-files-server-endpoint.md)
- [Kiszolgáló regisztrálása vagy regisztrációjának megszüntetése az Azure File Sync segítségével](storage-sync-files-server-registration.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
