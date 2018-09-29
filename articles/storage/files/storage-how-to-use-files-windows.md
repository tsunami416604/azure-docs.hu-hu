---
title: Azure-fájlmegosztás használata Windowson | Microsoft Docs
description: Az Azure-fájlmegosztások használata Windowson és Windows Serveren.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 3e63a4c05fde570e598ba05c65fb99cec0427711
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226417"
---
# <a name="use-an-azure-file-share-with-windows"></a>Azure-fájlmegosztás használata Windowson
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások zökkenőmentesen használhatóak Windowson és Windows Serveren. Ebben a cikkben az Azure-fájlmegosztások Windowson és Windows Serveren való használatának szempontjairól olvashat.

Ha egy Azure-fájlmegosztást az üzemeltető Azure-régión kívül kíván alkalmazni, például a helyszínen vagy más Azure-régióban, az operációs rendszernek támogatnia kell az SMB 3.0-s verziót. 

Azure-fájlmegosztásokat az Azure-beli virtuális gépeken vagy helyszínen futó Windows-telepítéseken használhat. A következő táblázatban látható, hogy melyik operációsrendszer-verzió melyik környezetekben támogatja a fájlmegosztások elérését:

| Windows-verzió        | SMB-verzió | Azure-beli virtuális gépeken csatlakoztatható | Helyszínen csatlakoztatható |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019 (előzetes verzió)<sup>1</sup> | SMB 3.0 | Igen | Igen |
| Windows 10<sup>2</sup> | SMB 3.0 | Igen | Igen |
| Windows Server félévi csatorna<sup>3</sup> | SMB 3.0 | Igen | Igen |
| Windows Server 2016    | SMB 3.0     | Igen                   | Igen                  |
| Windows 8.1            | SMB 3.0     | Igen                   | Igen                  |
| Windows Server 2012 R2 | SMB 3.0     | Igen                   | Igen                  |
| Windows Server 2012    | SMB 3.0     | Igen                   | Igen                  |
| Windows 7              | SMB 2.1     | Igen                   | Nem                   |
| Windows Server 2008 R2 | SMB 2.1     | Igen                   | Nem                   |

<sup>1</sup>A Windows Server 2019 előzetes verziója elérhető a [Windows Server Insiders programon](https://insider.windows.com/for-business-getting-started-server/) keresztül. Bár a Windows Server 2019 éles környezetben való használata még nem támogatott, lépjen kapcsolatba velünk, ha a [Windows hibaelhárítási útmutató](storage-troubleshoot-windows-file-connection-problems.md) részben leírtakon kívüli problémát tapasztal az Azure-fájlmegosztásokhoz való csatlakozással kapcsolatban.  
<sup>2</sup>A Windows 10 1507-es, 1607-es, 1703-as, 1709-es és 1803-as verziói.  
<sup>3</sup>A Windows Server 1709-es és 1803-as verziói.

> [!Note]  
> Javasoljuk, hogy mindig a Windows-verziójához legutóbb kiadott frissítést használja.

## <a name="prerequisites"></a>Előfeltételek 
* **Tárfiók neve**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz a tárfiók nevére.

* **Tárfiók kulcsa**: Az Azure-fájlmegosztások csatlakoztatásához szüksége lesz az elsődleges (vagy másodlagos) tárkulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, hogy a 445-ös port nyitva van**: Az SMB protokollhoz szükséges, hogy a 445-ös TCP port nyitva legyen; a csatlakozás nem sikerül, ha a 445-ös port blokkolva van. Ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös portot a `Test-NetConnection` parancsmaggal. Ne felejtse el kicserélni a `your-storage-account-name` elemet a tárfiók vonatkozó nevével.

    ```PowerShell
    Test-NetConnection -ComputerName <your-storage-account-name>.core.windows.net -Port 445
    
    ```

    Sikeres csatlakozás esetén a következő kimenetet kell látnia:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > A fenti parancs visszaadja a tárfiók aktuális IP-címét. Nem garantált, hogy ez az IP-cím ugyanaz marad, és bármikor megváltozhat. Ne rögzítse szoftveresen az IP-címet egy szkriptben vagy egy tűzfal-konfigurációban sem. 

## <a name="using-an-azure-file-share-with-windows"></a>Az Azure-fájlmegosztások használata Windowson
Az Azure-fájlmegosztások Windowson való használatához csatlakoztatnia kell azokat, azaz hozzájuk kell rendelnie egy meghajtó betűjelét vagy egy csatlakoztatási pont elérési útját, vagy pedig az [UNC-útvonalukon](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx) keresztül érheti el azokat. 

Ellentétben más, például a Windows Serveren, Linux Samba Serveren vagy NAS-eszközön szolgáltatott SMB-megosztásokkal, az Azure-fájlmegosztások jelenleg nem támogatják a Kerberos-hitelesítést az Active Directory (AD) vagy Azure Active Directory (AAD) identitás használatával, a funkció megvalósítása ugyanakkor [folyamatban van](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles). Ehelyett az Azure-fájlmegosztás annak a tárfióknak a tárfiókkulcsával érhető el, amelyik tartalmazza az Azure-fájlmegosztást. A tárfiókkulcs egy tárfiókhoz tartozó rendszergazdai kulcs, amely magába foglal a fájlmegosztáson belül minden fájlhoz és mappához, valamint a tárfiókban tárolt összes fájlmegosztáshoz és más tárolási erőforráshoz (blob, üzenetsor, tábla stb.) tartozó rendszergazdai engedélyt. Ha ez nem elegendő a számítási feladatokhoz, az [Azure File Sync](storage-files-planning.md#data-access-method) megoldhatja a Kerberos-hitelesítés és az ACL-támogatás hiányát, amíg az AAD-alapú Kerberos-hitelesítés és az ACL-támogatás nyilvánosan elérhetővé nem válik.

Az SMB-fájlmegosztást váró üzletági (LOB) alkalmazások Azure-ba való áthelyezése esetén gyakori megoldás az Azure-fájlmegosztások használata a dedikált Windows-fájlkiszolgálók Azure-beli virtuális gépeken történő futtatása helyett. Az üzletági alkalmazások egy Azure-fájlmegosztás használatára való sikeres migrálása érdekében fontos figyelembe venni, hogy számos üzletági alkalmazás, egy korlátozott rendszerengedélyekkel rendelkező dedikált szolgáltatásfiók környezetében fut a virtuális gép rendszergazdai fiókja helyett. Ezért győződjön meg róla, hogy az Azure-fájlmegosztáshoz szükséges hitelesítő adatokat a szolgáltatásfiók helyett a rendszergazdai fiókon keresztül csatlakoztatja/menti.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Az Azure-fájlmegosztások hitelesítő adatainak megőrzése Windowson  
A [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) segédprogram lehetővé teszi a tárfiókok hitelesítő adatainak tárolását Windowson. Ez azt jelenti, hogy nem szükséges megadnia a hitelesítő adatokat az Azure-fájlmegosztások csatlakoztatáskor vagy UNC-útvonalon keresztül történő elérésekor. A tárfiók hitelesítő adatainak mentéséhez futtassa a következő PowerShell-parancsokat, és cserélje le értelemszerűen a `<your-storage-account-name>` és `<your-resoure-group-name>` elemeket.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command "cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)"
```

A list paraméter segítségével ellenőrizheti, hogy a cmdkey segédprogram tárolta-e a tárfiók hitelesítő adatait:

```PowerShell
cmdkey /list
```

Ha a rendszer sikeresen tárolta az Azure-fájlmegosztáshoz tartozó hitelesítő adatokat, a következő kimenetnek kell megjelennie (előfordulhat, hogy további kulcsok is tárolva vannak a listában):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Most már további hitelesítő adatok megadása nélkül is tudnia kell csatlakozni vagy hozzáférni a megosztáshoz.

#### <a name="advanced-cmdkey-scenarios"></a>Speciális cmdkey-forgatókönyvek
Két további esetet kell figyelembe venni a cmdkey segédprogrammal kapcsolatban: a hitelesítő adatok tárolása egy másik felhasználó számára a gépen, például egy szolgáltatásfiók esetében, és a hitelesítő adatok tárolása távoli gépen PowerShell távoli eljáráshívással.

A hitelesítő adatok tárolása egy másik felhasználó számára a gépen nagyon egyszerű: amikor be van jelentkezve a fiókjába, egyszerűen hajtsa végre a következő PowerShell-parancsot:

```PowerShell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Ekkor megnyílik egy új PowerShell-ablak a szolgáltatásfiók (vagy felhasználói fiók) felhasználói környezetében. Ezután használhatja a cmdkey segédprogramot a [fenti](#persisting-azure-file-share-credentials-in-windows) leírás szerint.

A hitelesítő adatok tárolása egy távoli gépen a PowerShell távoli eljáráshívás használatával azonban nem lehetséges, mivel a cmdkey nem engedélyezi a hozzáférést a hitelesítőadat-tárolóhoz még hozzáadás esetére sem, amikor a felhasználó PowerShell távoli eljáráshíváson keresztül van bejelentkezve. Javasoljuk a [Távoli asztallal](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows) történő bejelentkezést a gépre.

### <a name="mount-the-azure-file-share-with-powershell"></a>Az Azure-fájlmegosztás csatlakoztatása a PowerShell-lel
Futtassa a következő parancsokat egy normál (azaz nem emelt szintű) PowerShell-munkamenetből az Azure-fájlmegosztás csatlakoztatásához. Ne felejtse el kicserélni a `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` és `<desired-drive-letter>` elemeket a vonatkozó információval.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> A `-Persist` opció a `New-PSDrive` parancsmagban csak akkor teszi lehetővé a fájlmegosztás újracsatlakoztatását a rendszerindításkor, ha a hitelesítő adatok mentve vannak. A hitelesítő adatokat a [korábbiakban leírtak](#persisting-azure-file-share-credentials-in-windows) szerint tudja menteni a cmdkey segédprogram használatával. 

A következő PowerShell-parancsmag használatával szükség esetén leválasztható az Azure-fájlmegosztás.

```PowerShell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Az Azure-fájlmegosztás csatlakoztatása a Fájlkezelővel
> [!Note]  
> Vegye figyelembe, hogy a következő példák a Windows 10-re vonatkoznak, és a régebbi kiadásokban eltérhetnek. 

1. Nyissa meg a Fájlkezelőt. Ezt a Start menüből vagy a Win+E billentyűkombináció lenyomásával teheti meg.

2. Keresse meg az **Ez a gép** elemet az ablak bal oldalán. Ez módosítja a szalagon elérhető menüket. A Számítógép menüben válassza a **Hálózati meghajtó csatlakoztatása** elemet.
    
    ![A Hálózati meghajtó csatlakoztatása legördülő menü képernyőképe](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Másolja ki az UNC-útvonalat az Azure Portal **Csatlakozás** ablaktáblájáról. 

    ![Az UNC-útvonal az Azure Files Csatlakozás oldaláról](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Válassza ki a meghajtó betűjelét, és írja be az UNC-útvonalat. 
    
    ![A Hálózati meghajtó csatlakoztatása párbeszédpanel képernyőképe](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Felhasználónévként használja a tárfiók `AZURE\` előtaggal kiegészített nevét, jelszóként pedig egy tárfiókkulcsot.
    
    ![A hálózati hitelesítő adatok párbeszédpanelének képernyőképe](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Használja az Azure-fájlmegosztást igény szerint.
    
    ![Az Azure-fájlmegosztás most már csatlakoztatva van](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Amikor készen áll az Azure-fájlmegosztás leválasztására, kattintson a jobb gombbal a megosztás bejegyzésére a Fájlkezelő **Hálózati helyek** területén, és válassza a **Leválasztás** parancsot.

### <a name="accessing-share-snapshots-from-windows"></a>Megosztási pillanatképek elérése a Windowsban
A valamely szkript vagy szolgáltatás, például az Azure Backup használatával manuálisan vagy automatikusan létrehozott megosztási pillanatképek segítségével megtekintheti a megosztások, a könyvtárak, illetve a fájlmegosztásokban vagy a Windows rendszeren lévő adott fájlok korábbi verzióit. Megosztási pillanatképeket az [Azure Portal](storage-how-to-use-files-portal.md), az [Azure PowerShell](storage-how-to-use-files-powershell.md) és az [Azure CLI](storage-how-to-use-files-cli.md) használatával készíthet.

#### <a name="list-previous-versions"></a>Előző verziók listázása
Tallózással keresse meg a visszaállítani kívánt elemet vagy szülőelemet. Duplán rákattintva lépjen a kívánt könyvtárra. Kattintson a jobb gombbal, majd válassza a menü **Tulajdonságok** elemét.

![Kijelölt könyvtár helyi menüje](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Válassza az **Előző verziók** lehetőséget a könyvtár megosztási pillanatképeinek listázásához. A lista betöltése a hálózat sebességétől és a könyvtárban lévő megosztási pillanatképek számától függően néhány másodpercet is igénybe vehet.

![Előző verziók lap](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

A **Megnyitás** elemre kattintva nyithatja meg az egyes pillanatképeket. 

![Megnyitott pillanatkép](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Visszaállítás korábbi verzióról
A **Visszaállítás** elemre kattintva visszaállíthatja a teljes könyvtár tartalmát az eredeti helyre a megosztási pillanatkép készítésének időpontjában érvényes állapotra.
 ![Visszaállítás gomb a figyelmeztető üzenetben](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>A Windows/Windows Server védelme
Az Azure-fájlmegosztás Windowson való csatlakoztatásához a 445-ös portnak elérhetőnek kell lennie. Számos szervezet blokkolja a 445-ös portot az SMB 1 eredendő biztonsági kockázatai miatt. Az SMB-1, más néven CIFS (Common Internet File System), egy korábbi fájlrendszerprotokoll, amelyet a Windows és a Windows Server tartalmaz. Az SMB-1 egy elavult, nem hatékony és legfőképpen nem biztonságos protokoll. A jó hír az, hogy az Azure Files nem támogatja az SMB 1-et, és a Windows és Windows Server összes támogatott verziója lehetővé teszi az SMB 1 eltávolítását vagy letiltását. Mindig [erősen ajánlott](https://aka.ms/stopusingsmb1) az SMB 1 ügyfél és kiszolgáló eltávolítása vagy letiltása a Windowsban az Azure-fájlmegosztások használata előtt.

A következő táblázat részletes leírást ad az SMB 1 állapotáról minden Windows-verzión:

| Windows-verzió                           | SMB 1 alapértelmezett állapota | Letiltási/eltávolítási módszer       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019 (előzetes verzió)             | Letiltva             | Eltávolítás Windows-funkcióval |
| Windows Server, 1709+ verziók            | Letiltva             | Eltávolítás Windows-funkcióval |
| Windows 10, 1709+ verziók                | Letiltva             | Eltávolítás Windows-funkcióval |
| Windows Server 2016                       | Engedélyezve              | Eltávolítás Windows-funkcióval |
| Windows 10, 1507-es, 1607-es és 1703-as verziók | Engedélyezve              | Eltávolítás Windows-funkcióval |
| Windows Server 2012 R2                    | Engedélyezve              | Eltávolítás Windows-funkcióval | 
| Windows 8.1                               | Engedélyezve              | Eltávolítás Windows-funkcióval | 
| Windows Server 2012                       | Engedélyezve              | Letiltás a beállításjegyzékkel       | 
| Windows Server 2008 R2                    | Engedélyezve              | Letiltás a beállításjegyzékkel       |
| Windows 7                                 | Engedélyezve              | Letiltás a beállításjegyzékkel       | 

### <a name="auditing-smb-1-usage"></a>Az SMB 1 használatának naplózása
> Érintett kiadások: Windows Server 2019 (előzetes verzió), Windows Server félévi csatorna (1709-es és 1803-as verzió), Windows Server 2016, Windows 10 (1507-es, 1607-es, 1703-as, 1709-es és 1803-as verzió), Windows Server 2012 R2 és Windows 8.1

Mielőtt eltávolítja az SMB 1-et a környezetből, naplózhatja az SMB 1 használatát, hogy nyomon tudja követni, károsodik-e bármelyik ügyfél a változtatás miatt. Ha kérelem merül fel az SMB 1-gyel rendelkező SMB-megosztásokkal kapcsolatban, a rendszer egy naplózási eseményt rögzít az eseménynaplóba az `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` útvonalon. 

> [!Note]  
> A Windows Server 2012 R2-es és a Windows 8.1-es verzión való naplózási támogatás engedélyezéséhez telepítse legalább a [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720) frissítést.

A naplózás engedélyezéséhez hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```PowerShell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Az SMB 1 eltávolítása a Windows Serverről
> Érintett kiadások: Windows Server 2019 (előzetes verzió), Windows Server félévi csatorna (1709-es és 1803-as verzió), Windows Server 2016, Windows Server 2012 R2

Az SMB 1 Windows Server-példányról történő eltávolításához hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```PowerShell
Remove-WindowsFeature -Name FS-SMB1
```

Az eltávolítási folyamat befejezéséhez indítsa újra a kiszolgálót. 

> [!Note]  
> A Windows 10 és a Windows Server 1709-es verziótól kezdődően az SMB 1 alapértelmezés szerint nincs telepítve, és az SMB 1 ügyfél és az SMB 1 kiszolgáló különálló Windows-funkciókkal rendelkezik. Azt javasoljuk, hogy ne telepítse az SMB 1 kiszolgálót (`FS-SMB1-SERVER`) és az SMB 1 ügyfelet (`FS-SMB1-CLIENT`).

### <a name="removing-smb-1-from-windows-client"></a>Az SMB 1 eltávolítása a Windows-ügyfélről
> Érintett kiadások: Windows 10 (1507-es, 1607-es, 1703-as, 1709-es és 1803-as verziók) és Windows 8.1

Az SMB 1 Windows-ügyfélről történő eltávolításához hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```PowerShell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Az eltávolítási folyamat befejezéséhez indítsa újra a számítógépet.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Az SMB 1 letiltása a Windows/Windows Server korábbi verzióin
> Érintett kiadások: Windows Server 2012, Windows Server 2008 R2 és Windows 7

Az SMB 1 nem távolítható el teljesen a Windows/Windows Server korábbi verzióin, a beállításjegyzék segítségével azonban letiltható. Az SMB-1 letiltásához hozzon létre egy új, `DWORD` típusú `SMB1` beállításkulcsot `0` értékkel a `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` útvonalon.

Ezt a következő PowerShell-parancsmaggal egyszerűen megteheti:

```PowerShell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Miután létrehozta a beállításkulcsot, indítsa újra a kiszolgálót az SMB 1 letiltásához.

### <a name="smb-resources"></a>SMB-erőforrások
- [Az SMB 1 használatának leállítása](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 termék adategyeztető központ](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Az SMB 1 felfedezése a környezetben DSCEA használatával](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [Az SMB 1 letiltása csoportházirend használatával](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
* [GYIK](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
