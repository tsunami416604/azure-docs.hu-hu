---
title: Az Azure Files problémáinak elhárítása a Windows rendszerben | Microsoft dokumentumok
description: Az Azure Files hibáinak elhárítása a Windows rendszerben
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: b4e1ef4fbc3ade38b55fc06f8e4e9a119938581b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383907"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Azure Files-problémák hibaelhárítása Windowson

Ez a cikk a Microsoft Azure-fájlokkal kapcsolatos gyakori problémákat sorolja fel, amikor Windows-ügyfelekről csatlakozik. A probléma lehetséges okait és megoldásait is tartalmazza. A cikkben ismertetett hibaelhárítási lépéseken kívül az [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) segítségével is biztosíthatja, hogy a Windows ügyfélkörnyezet megfelelő előfeltételekkel rendelkezzen. AzFileDiagnostics automatizálja a jelen cikkben említett tünetek többségének észlelését, és segít a környezet beállításában az optimális teljesítmény elérése érdekében. Ezeket az információkat az [Azure Files megosztások hibaelhárítójában](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) is megtalálhatja, amely lépéseket nyújt az Azure Files-megosztások csatlakoztatásával/leképezésével/csatlakoztatásával kapcsolatos problémák megoldásához.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>5-ös hiba Azure-fájlmegosztás csatlakoztatásakor

Fájlmegosztás csatlakoztatásakor a következő hibaüzenet jelenhet meg:

- Az 5-ös rendszerhiba fordult elő. A hozzáférés megtagadva.

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: Titkosítatlan kommunikációs csatorna

Biztonsági okokból az Azure-fájlmegosztásokhoz való kapcsolódás le van tiltva, ha a kommunikációs csatorna nincsen titkosítva, vagy ha a csatlakozási kísérlet nem ugyanabból az adatközpontból történik, ahol az Azure-fájlmegosztások találhatók. Az ugyanazon adatközponton belüli titkosítatlan kapcsolatokat akkor is blokkolhatja a rendszer, ha a tárfiókban engedélyezve van a [Biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás. Csak akkor biztosítható titkosított kommunikációs csatorna, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosítást.

A Windows 8, a Windows Server 2012 és újabb verzióik olyan kéréseket egyeztetnek, amelyek magukban foglalják a titkosítást támogató SMB 3.0-t.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Csatlakozzon egy ügyfél, amely támogatja az SMB-titkosítás (Windows 8, Windows Server 2012 vagy újabb), vagy csatlakozzon egy virtuális gép ugyanabban az adatközpontban, mint az Azure-alapú tárfiók, amely az Azure-fájlmegosztáshoz használt.
2. Ellenőrizze, hogy a [biztonságos átvitel szükséges](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van-e tiltva a tárfiókban, ha az ügyfél nem támogatja az SMB titkosítást.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2. ok: A virtuális hálózati vagy tűzfalszabályok engedélyezve vannak a tárfiókban 

Ha virtuális hálózati (VNET) és tűzfalszabályok vannak konfigurálva a tárfiókhoz, a hálózati forgalom számára blokkolva lesz a hozzáférés, kivéve, ha az ügyfél IP-címe vagy a virtuális hálózat hozzáférést kapott.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>3. ok: A megosztásszintű engedélyek helytelenek az identitásalapú hitelesítés használatakor

Ha a felhasználók az Azure-fájlmegosztást Active Directory (AD) vagy Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) hitelesítésével érik el, a fájlmegosztáshoz való hozzáférés sikertelen lesz a "Hozzáférés megtagadva" hibaüzenettel, ha a megosztási szintű engedélyek helytelenek. 

### <a name="solution-for-cause-3"></a>Megoldás az ok 3

A megosztásszintű engedélyek frissítéséhez olvassa el a [Hozzáférési engedélyek hozzárendelése identitáshoz](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity)című témakört.

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>53-as, 67-es vagy 87-es hiba az Azure-fájlmegosztás csatlakoztatásakor vagy leválasztásakor

Amikor egy fájlmegosztást helyszíni vagy másik adatközpontból próbál csatlakoztatni, a következő hibaüzenetek jelenhetnek meg:

- Az 53-as rendszerhiba fordult elő. A hálózati elérési út nem található.
- A 67-es rendszerhiba fordult elő. A hálózatnév nem található.
- A 87-es rendszerhiba fordult elő. A paraméter helytelen.

### <a name="cause-1-port-445-is-blocked"></a>1. ok: A 445-ös port le van tiltva

Az 53-as vagy a 67-es rendszerhiba akkor fordulhat elő, ha a 445-ös port kimenő kommunikációja egy Azure Files adatközpontba blokkolva van. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Annak ellenőrzéséhez, hogy a tűzfal vagy az internetszolgáltató blokkolja-e `Test-NetConnection` a 445-ös portot, használja az [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) eszközt vagy parancsmacst. 

A `Test-NetConnection` parancsmag használatához az Azure PowerShell-modult telepíteni kell, további információ: [Azure PowerShell-modul telepítése.](/powershell/azure/install-Az-ps) Ne felejtse el kicserélni a `<your-storage-account-name>` és a `<your-resource-group-name>` elemet a tárfiók vonatkozó neveivel.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Sikeres csatlakozás esetén a következő kimenetet kell látnia:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> A fenti parancs visszaadja a tárfiók aktuális IP-címét. Nem garantált, hogy ez az IP-cím ugyanaz marad, és bármikor megváltozhat. Ne rögzítse szoftveresen az IP-címet egy szkriptben vagy egy tűzfal-konfigurációban sem.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

#### <a name="solution-1---use-azure-file-sync"></a>1. megoldás – az Azure File Sync használata
Az Azure File Sync a helyszíni Windows Server t az Azure-fájlmegosztás gyors gyorsítótárává alakíthatja. A Windows Server kiszolgálón elérhető bármely protokoll thasználhat az adatok helyi eléréséhez, beleértve az SMB, az NFS és az FTPS protokollt. Az Azure File Sync a 443-as porton keresztül működik, és így kerülő megoldásként használható a 445-ös porttal rendelkező ügyfelek Azure Files-fájljainak eléréséhez. [További információ az Azure File Sync beállításáról.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers)

#### <a name="solution-2---use-vpn"></a>2. megoldás – VPN használata
A VPN beállítása az adott tárfiókhoz, a forgalom megy keresztül egy biztonságos alagút, szemben az interneten keresztül. Kövesse az utasításokat a [VPN beállításához](storage-files-configure-p2s-vpn-windows.md) az Azure Files Windows-ból való eléréséhez.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>3. megoldás – A 445-ös port blokkolásának feloldása az internetszolgáltató vagy a rendszergazda segítségével
Az Azure [IP-tartományokba](https://www.microsoft.com/download/details.aspx?id=41653)kimenő 445-ös port megnyitásához az informatikai részleggel vagy az isp-vel együttműködve nyissa meg a 445-ös portot.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>4. megoldás – REST API-alapú eszközök, például a Storage Explorer és a Powershell használata
Az Azure Files az SMB mellett támogatja a REST szolgáltatást is. A REST-hozzáférés a 443-as porton (szabványos tcp) működik. Vannak különböző eszközök, amelyek a REST API használatával vannak megírva, amelyek lehetővé teszik a gazdag felhasználói felületi élményt. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) az egyik közülük. [Töltse le és telepítse](https://azure.microsoft.com/features/storage-explorer/) a Storage Explorert, és csatlakozzon az Azure Files által támogatott fájlmegosztáshoz. [A PowerShell,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) amely szintén felhasználói REST API-t is használhatja.

### <a name="cause-2-ntlmv1-is-enabled"></a>2. ok: Az NTLMv1 engedélyezve van

Az 53-as vagy a 87-es rendszerhiba akkor fordulhat elő, ha az NTLMv1 kommunikáció engedélyezve van az ügyfélen. Az Azure Files kizárólag az NTLMv2-hitelesítést támogatja. Az NTLMv1 engedélyezése csökkenti az ügyfél biztonságát. Ezért kerül sor az Azure Files kommunikációjának blokkolására. 

Ha meg szeretne bizonyosodni arról, hogy valóban ez a hiba oka, ellenőrizze, hogy a következő, beállításjegyzékbeli alkulcs értéke 3-e:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

További információért tekintse meg a TechNet [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) témakörét.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Állítsa vissza az **LmCompatibilityLevel** értékét az alapértelmezett 3-ra a következő beállításjegyzékbeli alkulcsban:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>1816-os hiba: "Nincs elég kvóta a parancs feldolgozásához" – amikor Azure-fájlmegosztásba másol

### <a name="cause"></a>Ok

A 1816-os hiba akkor fordul elő, ha eléri az egyidejűnyitott leírók felső korlátját, amelyek a fájlmegosztást tartalmazó számítógépen lévő fájlszámára engedélyezettek.

### <a name="solution"></a>Megoldás

Csökkentse az egyidejűnyitott fogópontok számát néhány fogópont bezárásával, majd próbálkozzon újra. További információt a [Microsoft Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistájában](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)talál.

Fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak megtekintéséhez használja a [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell parancsmagját.  

Fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak bezárásához használja a [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell parancsmagját.

> [!Note]  
> A Get-AzStorageFileHandle és close-AzStorageFileHandle parancsmagok az Az PowerShell modul 2.4-es vagy újabb verziójában találhatók. A legújabb Az PowerShell-modul telepítéséről az [Azure PowerShell-modul telepítése című témakörben látható.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Hiba "Nincs hozzáférés", amikor megpróbál hozzáférni vagy törölni egy Azure-fájlmegosztást  
Amikor megpróbál hozzáférni vagy törölni egy Azure-fájlmegosztást a portálon, a következő hibaüzenet jelenhet meg:

Nincs hozzáférés  
Hibakód: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>1. ok: A virtuális hálózati vagy tűzfalszabályok engedélyezve vannak a tárfiókban

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>2. ok: A felhasználói fiók nem fér hozzá a tárfiókhoz

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Tallózással keresse meg azt a tárfiókot, ahol az Azure-fájlmegosztás található, kattintson a **hozzáférés-vezérlés (IAM)** elemre, és ellenőrizze, hogy a felhasználói fiók rendelkezik-e hozzáféréssel a tárfiókhoz. További információ: [A tárfiók védelme szerepköralapú hozzáférés-vezérléssel (RBAC).](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nem sikerült törölni egy fájlt vagy könyvtárt valamelyik Azure-fájlmegosztásban
Amikor megpróbál törölni egy fájlt, a következő hibaüzenet jelenhet meg:

A megadott erőforrást egy SMB-ügyfél törlésre jelöli meg.

### <a name="cause"></a>Ok
Ez a probléma általában akkor fordul elő, ha a fájl vagy könyvtár nyitott leíróval rendelkezik. 

### <a name="solution"></a>Megoldás

Ha az SMB-ügyfelek lezárták az összes nyitott leírót, és a probléma továbbra is fennáll, hajtsa végre a következőket:

- A [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell parancsmag használatával megtekintheti a megnyitott fogópontokat.

- A [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell parancsmag segítségével zárja be a megnyitott fogópontokat. 

> [!Note]  
> A Get-AzStorageFileHandle és close-AzStorageFileHandle parancsmagok az Az PowerShell modul 2.4-es vagy újabb verziójában találhatók. A legújabb Az PowerShell-modul telepítéséről az [Azure PowerShell-modul telepítése című témakörben látható.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>A fájlok az Azure Files szolgáltatásba vagy onnan máshová történő lassú másolása Windows rendszeren

Előfordulhat, hogy lassú teljesítményt, amikor megpróbálja átvinni a fájlokat az Azure File szolgáltatásba.

- Ha nem rendelkezik egy adott minimális I/O-méretkövetelményrel, javasoljuk, hogy az optimális teljesítmény érdekében 1 MiB-t használjon I/O-méretként.
-   Ha ismeri az írással kiterjeszthető fájl végső méretét, és a szoftvernek nincskompatibilitási problémája, ha a fájl íratlan farka nullákat tartalmaz, akkor állítsa be előre a fájlméretet ahelyett, hogy minden írást kiterjesztene egy kiterjesztő írásra.
-   Használja a megfelelő másolási módszert:
    -   Két fájlmegosztás közötti átvitelhez használja az [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) programot.
    -   [A Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) használata a helyszíni számítógépen lévő fájlmegosztások között.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>A Windows 8.1 vagy a Windows Server 2012 R2 rendszerrel kapcsolatos szempontok

A Windows 8.1 vagy Windows Server 2012 R2 rendszert futtató ügyfelek esetében győződjön meg arról, hogy a [KB3114025](https://support.microsoft.com/help/3114025) gyorsjavítás telepítve van. Ez a gyorsjavítás javítja a leírók létrehozásának és bezárásának teljesítményét.

A következő parancsfájl futtatásával ellenőrizheti, hogy a gyorsjavítás telepítve van-e:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Ha a gyorsjavítás telepítve van, a következő kimenet jelenik meg:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Az Azure Marketplace-en a Windows Server 2012 R2 rendszerképek en alapértelmezés szerint telepítve van a KB3114025 gyorsjavítás, amely 2015 decemberében kezdődik.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nincs olyan mappa, amelynek meghajtóbetűjele van a "Sajátgép" vagy az "Ez a számítógép" mappában

Ha egy Azure-fájlmegosztást leképez rendszergazdaként a nettó használat használatával, úgy tűnik, hogy a megosztás hiányzik.

### <a name="cause"></a>Ok

Alapértelmezés szerint a Windows Fájlkezelő nem rendszergazdaként fut. Ha a hálózati használatot egy felügyeleti parancssorból futtatja, a hálózati meghajtót rendszergazdaként kell leképeznie. Mivel a csatlakoztatott meghajtók felhasználóközpontúak, a bejelentkezett felhasználói fiók nem jeleníti meg a meghajtókat, ha más felhasználói fiók alatt vannak csatlakoztatva.

### <a name="solution"></a>Megoldás
Csatlakoztassa a megosztást nem rendszergazdai parancssorból. Másik lehetőségként kövesse [ezt a TechNet témakört](https://technet.microsoft.com/library/ee844140.aspx) az **EnableLinkedConnections** beállításérték konfigurálásához.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>A Nettó használat parancs sikertelen, ha a tárfiók perjelet tartalmaz

### <a name="cause"></a>Ok

A netuse parancs parancs parancssori kapcsolóként értelmezi a perjelet (/). Ha a felhasználói fiók neve perjellel kezdődik, a meghajtó leképezése sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megkerüléséhez az alábbi lépések egyikével is megoldhatja a problémát:

- Futtassa az alábbi PowerShell-parancsot:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A parancs így futtatható kötegfájlból:

  `Echo new-smbMapping ... | powershell -command –`

- A probléma megkerüléséhez tegye a dupla idézőjeleket a kulcs köré – kivéve, ha az elővágás az első karakter. Ha ez így van, használja az interaktív módot, és adja meg a jelszavát külön-külön, vagy hozza létre újra a kulcsokat, hogy olyan kulcsot kapjon, amely nem perjellel kezdődik.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Az alkalmazás vagy szolgáltatás nem tud hozzáférni egy csatlakoztatott Azure Files meghajtóhoz

### <a name="cause"></a>Ok

A meghajtók felhasználónként vannak csatlakoztatva. Ha az alkalmazás vagy szolgáltatás a meghajtót csatlakoztatótól eltérő felhasználói fiók alatt fut, az alkalmazás nem fogja látni a meghajtót.

### <a name="solution"></a>Megoldás

Használja az alábbi megoldások egyikét:

-   Csatlakoztassa a meghajtót ugyanarról a felhasználói fiókról, amely az alkalmazást tartalmazza. Használhatja az eszköz, mint a PsExec.
- Adja meg a tárfiók nevét és a kulcsot a net use parancs felhasználónevében és jelszavában.
- A cmdkey paranccsal adja hozzá a hitelesítő adatokat a Hitelesítő adatok kezelőjéhez. Ezt a parancssorból, a szolgáltatásfiók környezetében hajtsa `runas`végre, akár interaktív bejelentkezéssel, akár a használatával.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- A megosztást közvetlenül, leképezett meghajtóbetűjel használata nélkül képezze le. Előfordulhat, hogy egyes alkalmazások nem csatlakoznak megfelelően a meghajtóbetűjelhez, ezért a teljes UNC-elérési út használata megbízhatóbb lehet. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Miután követte ezeket az utasításokat, a rendszer/hálózat szolgáltatásfiók nettó használata során a következő hibaüzenet jelenhet meg: "1312-es rendszerhiba történt. A megadott bejelentkezési munkamenet nem létezik. Lehet, hogy már megszüntették." Ebben az esetben győződjön meg arról, hogy a netes használatra átadott felhasználónév tartalmazza a tartományadatait (például: "[tárfiók neve].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Hiba: "Olyan helyre másolja a fájlt, amely nem támogatja a titkosítást"

Amikor egy fájlt a hálózaton keresztül másol, a fájl visszafejtése a forrásszámítógépen történik, egyszerű szöveges úton továbbítódik, és újra titkosítja a célhelyen. Titkosított fájl másolásakor azonban a következő hibaüzenet jelenhet meg: "A fájlt olyan helyre másolja, amely nem támogatja a titkosítást."

### <a name="cause"></a>Ok
Ez a probléma akkor fordulhat elő, ha titkosított fájlrendszert (EFS) használ. A BitLocker-titkosítású fájlok átmásolhatók az Azure Files szolgáltatásba. Az Azure Files azonban nem támogatja az NTFS EFS szolgáltatást.

### <a name="workaround"></a>Áthidaló megoldás
Ha egy fájlt a hálózaton keresztül szeretne másolni, először vissza kell fejtenie azt. Használja az alábbi módszerek egyikét:

- Használja a **copy /d** parancsot. Ez lehetővé teszi, hogy a titkosított fájlokat kell menteni visszafejteni fájlokat a cél.
- Állítsa be a következő beállításkulcsot:
  - Elérési út = HKLM\Software\Policies\Microsoft\Windows\System
  - Érték típusa = Duplaszó
  - Név = CopyFileAllowDecryptedRemoteDestination
  - Érték = 1

Ne feledje, hogy a beállítási kulcs beállítása hatással van a hálózati megosztásokon végzett összes másolási műveletre.

## <a name="slow-enumeration-of-files-and-folders"></a>A fájlok és mappák lassú számbavétele

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha nincs elég gyorsítótár az ügyfélgépen a nagy könyvtárakszámára.

### <a name="solution"></a>Megoldás

A probléma megoldásához módosítsa a **DirectoryCacheEntrySizeMax** rendszerleíró értéket úgy, hogy az ügyfélgépen nagyobb könyvtárlisták gyorsítótárazhassanak:

- Helyszín: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Érték mane: DirectoryCacheEntrySizeMax 
- Érték típusa:Duplaszó
 
 
Beállíthatja például 0x100000-re, és láthatja, hogy a teljesítmény jobb lesz-e.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Hiba AadDsTenantNotFound engedélyezése Az Azure Active Directory tartományi szolgáltatás (AAD DS) hitelesítésének az Azure Files "Nem található az aktív bérlők bérlői azonosítóval ad aad-tenant-id"

### <a name="cause"></a>Ok

Hiba AadDsTenantNotFound történik, amikor megpróbálja engedélyezni az [Azure Active Directory tartományi szolgáltatások (Azure AD DS) hitelesítést az Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) egy tárfiókban, ahol [AAD tartományi szolgáltatás (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) nem jön létre az AAD-bérlő a társított előfizetés.  

### <a name="solution"></a>Megoldás

Engedélyezze az AAD DS-t annak az előfizetésnek az AAD-bérlőjében, amelybe a tárfiók telepítve van. Felügyelt tartomány létrehozásához rendszergazdai jogosultságokra van szüksége az AAD-bérlőhöz. Ha nem ön az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse a lépésről lépésre mutató útmutatást [az Azure Active Directory tartományi szolgáltatások engedélyezéséhez az Azure Portalon](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)keresztül.

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Hiba: '1359 rendszerhiba történt. Belső hiba érkezett az SMB-hozzáférésből az Azure Active Directory tartományi szolgáltatás (AAD DS) hitelesítésével engedélyezve

### <a name="cause"></a>Ok

Hiba: '1359 rendszerhiba történt. Belső hiba történik, ha olyan AAD DS-hitelesítéssel próbál csatlakozni a fájlmegosztáshoz, amely egy numerikus karakterrel kezdődő tartományDNS-névvel rendelkező AAD DS-en keresztül engedélyezett. Ha például az AAD DS tartomány DNS-neve "1domain", akkor ez a hibaüzenet jelenik meg, amikor a fájlmegosztást AAD hitelesítő adatokkal próbálja csatlakoztatni. 

### <a name="solution"></a>Megoldás

Jelenleg érdemes lehet újratelepíteni az AAD DS egy új domain DNS-név, amely az alábbi szabályokkal:
- A nevek nem kezdődhetnek numerikus karakterrel.
- A nevek nek 3 és 63 karakter között kell lenniük.

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Nem lehet csatlakoztatni az Azure Files-t AD-hitelesítő adatokkal 

### <a name="self-diagnostics-steps"></a>Öndiagnosztika lépései
Először győződjön meg arról, hogy mind a négy lépést végigkövette az [Azure Files AD-hitelesítés engedélyezéséhez.](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable)

Másodszor próbálja meg [az Azure-fájlmegosztást a tárfiók kulcsával.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) Ha nem sikerült csatlakoztatni, töltse le [az AzFileDiagnostics.ps1](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) fájlt, hogy segítsen az ügyfél futó környezetének érvényesítésében, észlelje az inkompatibilis ügyfélkonfigurációt, amely az Azure Files hozzáférési hibáját okozhatja, előíró útmutatást ad az önjavításhoz, és gyűjtse össze a diagnosztikai nyomkövetéseket.

Harmadszor, futtathatja a Debug-AzStorageAccountAuth parancsmag, hogy végezzen egy sor alapvető ellenőrzéseket az AD-konfiguráció a bejelentkezett AD-felhasználó. Ez a parancsmag az [AzFilesHybrid v0.1.2+ verzióban](https://github.com/Azure-Samples/azure-files-samples/releases)támogatott. Ezt a parancsmacélt egy olyan AD-felhasználóval kell futtatnia, amely tulajdonosi engedéllyel rendelkezik a céltárfiókhoz.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
A parancsmag az alábbi ellenőrzéseket sorrendben hajtja végre, és útmutatást ad a hibákhoz:
1. CheckPort445Connectivity: ellenőrizze, hogy a 445-ös port meg van-e nyitva az SMB-kapcsolathoz
2. CheckDomainJoined: ellenőrizze, hogy az ügyfélszámítógép tartomány csatlakozik-e az AD-hez
3. CheckADObject: ellenőrizze, hogy a bejelentkezett felhasználó érvényes reprezentációval rendelkezik-e abban az AD-tartományban, amelyhez a tárfiók társítva van.
4. CheckGetKerberosTicket: kerberos jegy et próbál szerezni a tárfiókhoz való csatlakozáshoz 
5. CheckADObjectPasswordIsCorrect: győződjön meg arról, hogy a tárfiókot jelképező AD-identitáson konfigurált jelszó megfelel a tárfiók szegélykulcsának.
6. CheckSidHasAadUser: ellenőrizze, hogy a bejelentkezett AD-felhasználó szinkronizálva van-e az Azure AD-vel

Aktívan dolgozunk a diagnosztikai parancsmag kiterjesztésén, hogy jobb hibaelhárítási útmutatást nyújtsunk.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.
Ha továbbra is segítségre van szüksége, lépjen kapcsolatba az [ügyfélszolgálattal,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) hogy gyorsan megoldódjon a probléma.
