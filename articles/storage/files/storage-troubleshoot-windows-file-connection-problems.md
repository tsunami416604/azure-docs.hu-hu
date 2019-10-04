---
title: Azure Files problémák elhárítása a Windows rendszerben | Microsoft Docs
description: A Windows problémáinak elhárítása Azure Files
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: f36d3bcb16876f080f780658bc59afd794e3431e
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699183"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>A Windows Azure Files problémáinak elhárítása

Ez a cikk a Windows-ügyfelekről való csatlakozáskor Microsoft Azure fájlokkal kapcsolatos gyakori problémákat sorolja fel. Emellett a problémák lehetséges okait és megoldásait is tartalmazza. A cikkben található hibaelhárítási lépések mellett a [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) is használható annak biztosítására, hogy a Windows-ügyfél környezete megfelelő előfeltételekkel rendelkezik. A AzFileDiagnostics automatizálja a jelen cikkben említett legtöbb tünet észlelését, és segít az optimális teljesítmény érdekében a környezet beállításában. Ezeket az információkat a [Azure Files shares](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) -hibakeresőben is megtalálhatja, amely segítséget nyújt a Azure Files-megosztások csatlakoztatása/leképezése/csatlakoztatása vagy csatlakoztatása terén.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>5\. hiba az Azure-fájlmegosztás csatlakoztatásakor

Amikor megpróbál csatlakoztatni egy fájlmegosztást, a következő hibaüzenet jelenhet meg:

- Az 5-ös rendszerhiba fordult elő. A hozzáférés megtagadva.

### <a name="cause-1-unencrypted-communication-channel"></a>1\. ok: Titkosítatlan kommunikációs csatorna

Biztonsági okokból az Azure-fájlmegosztásokhoz való kapcsolódás le van tiltva, ha a kommunikációs csatorna nincsen titkosítva, vagy ha a csatlakozási kísérlet nem ugyanabból az adatközpontból történik, ahol az Azure-fájlmegosztások találhatók. Az ugyanazon adatközponton belüli titkosítatlan kapcsolatokat akkor is blokkolhatja a rendszer, ha a tárfiókban engedélyezve van a [Biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás. Csak akkor biztosítható titkosított kommunikációs csatorna, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosítást.

A Windows 8, a Windows Server 2012 és újabb verzióik olyan kéréseket egyeztetnek, amelyek magukban foglalják a titkosítást támogató SMB 3.0-t.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Kapcsolódjon egy olyan ügyfélhez, amely támogatja az SMB-titkosítást (Windows 8, Windows Server 2012 vagy újabb), vagy kapcsolódjon egy olyan virtuális gépről, amely az Azure-fájlmegosztás által használt Azure Storage-fiókkal azonos adatközpontban található.
2. Győződjön meg arról, hogy a [biztonságos átvitel szükséges](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva a Storage-fiókban, ha az ügyfél nem támogatja az SMB-titkosítást.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2\. ok: A virtuális hálózat vagy a tűzfalszabályok engedélyezve vannak a Storage-fiókon. 

Ha virtuális hálózati (VNET) és tűzfalszabályok vannak konfigurálva a tárfiókhoz, a hálózati forgalom számára blokkolva lesz a hozzáférés, kivéve, ha az ügyfél IP-címe vagy a virtuális hálózat hozzáférést kapott.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>53 hiba, hiba 67 vagy hiba 87 az Azure-fájlmegosztás csatlakoztatásakor vagy leválasztásakor

Amikor a helyi vagy egy másik adatközpontból próbál meg fájlmegosztást csatlakoztatni, a következő hibaüzenetek jelenhetnek meg:

- Az 53-as rendszerhiba fordult elő. A hálózati elérési út nem található.
- A 67-es rendszerhiba fordult elő. A hálózatnév nem található.
- A 87-es rendszerhiba fordult elő. A paraméter helytelen.

### <a name="cause-1-port-445-is-blocked"></a>1\. ok: Az 445-es port blokkolva van

Rendszerhiba 53 vagy a 67 rendszerhiba akkor fordulhat elő, ha a port 445 Azure Files adatközpontba való kimenő kommunikációja le van tiltva. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Annak vizsgálatához, hogy a tűzfal vagy az internetszolgáltató blokkolja-e a [](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) 445-es `Test-NetConnection` portot, használja a AzFileDiagnostics eszközt vagy a parancsmagot. 

A `Test-NetConnection` parancsmag használatához telepíteni kell a Azure PowerShell modult. További információért lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps) . Ne felejtse el kicserélni a `<your-storage-account-name>` és a `<your-resource-group-name>` elemet a tárfiók vonatkozó neveivel.

   
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

#### <a name="solution-1---use-azure-file-sync"></a>1\. megoldás – Azure File Sync használata
Azure File Sync átalakíthatja a helyszíni Windows Servert az Azure-fájlmegosztás gyors gyorsítótárba. A Windows Serveren elérhető bármely protokoll használatával helyileg férhet hozzá az adataihoz, beleértve az SMB-t, az NFS-t és a FTPS is. Azure File Sync a 443-es porton keresztül működik, ezért a 445-es porttal rendelkező ügyfelektől megkerülő megoldásként használható a Azure Files eléréséhez. [Útmutató a Azure file Sync telepítéséhez](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>2\. megoldás – VPN használata
A VPN az adott Storage-fiókhoz való beállításával a forgalom egy biztonságos alagúton halad át, szemben az interneten keresztül. Az utasításokat [követve beállíthatja](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) a VPN-t a Windows rendszerű Azure Files eléréséhez.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>3\. megoldás – a 445-es port feloldása az INTERNETSZOLGÁLTATÓ/rendszergazda segítségével
Az IT-részleggel vagy az INTERNETSZOLGÁLTATÓval együttműködve nyissa meg az 445-es portot az [Azure IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>4\. megoldás – REST API-alapú eszközök, például a Storage Explorer/PowerShell használata
A Azure Files az SMB mellett a REST-t is támogatja. A REST-hozzáférés a 443-as porton (standard TCP) keresztül működik. Számos olyan eszköz van, amely REST API, amely lehetővé teszi a kezelőfelület gazdag felhasználói élményét. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) az egyikük. [Töltse le és telepítse](https://azure.microsoft.com/features/storage-explorer/) a Storage Explorert, és kapcsolódjon a fájlmegosztás Azure Files által támogatott megosztáshoz. Használhatja a PowerShellt [](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) is, amely a felhasználó REST API is.

### <a name="cause-2-ntlmv1-is-enabled"></a>2\. ok: A NTLMv1 engedélyezve van

Rendszerhiba 53 vagy a 87 rendszerhiba akkor fordulhat elő, ha a NTLMv1-kommunikáció engedélyezve van az ügyfélen. Az Azure Files kizárólag az NTLMv2-hitelesítést támogatja. Az NTLMv1 engedélyezése csökkenti az ügyfél biztonságát. Ezért kerül sor az Azure Files kommunikációjának blokkolására. 

Ha meg szeretne bizonyosodni arról, hogy valóban ez a hiba oka, ellenőrizze, hogy a következő, beállításjegyzékbeli alkulcs értéke 3-e:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

További információért tekintse meg a TechNet [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) témakörét.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Állítsa vissza az **LmCompatibilityLevel** értékét az alapértelmezett 3-ra a következő beállításjegyzékbeli alkulcsban:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>1816-es hiba: "nincs elegendő kvóta a parancs feldolgozásához" az Azure-fájlmegosztás másolásakor

### <a name="cause"></a>Ok

1816-as hiba történik, amikor eléri az egyidejű nyitott fogópontok felső határát, amelyek a fájlmegosztás csatlakoztatása esetén a számítógépen lévő fájl számára megengedettek.

### <a name="solution"></a>Megoldás

Csökkentse az egyidejű megnyitott fogópontok számát néhány leíró bezárásával, majd próbálkozzon újra. További információ: [Microsoft Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlista](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Egy fájlmegosztás, könyvtár vagy fájl nyitott leíróinak megtekintéséhez használja a [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-parancsmagot.  

Egy fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak bezárásához használja a [AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell-parancsmagot.

> [!Note]  
> A Get-AzStorageFileHandle és a AzStorageFileHandle parancsmag az az PowerShell-modul 2,4-es vagy újabb verziójában szerepel. A legújabb az PowerShell modul telepítéséhez lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Hiba az "engedélyezési hiba" esetén, amikor egy Azure-fájlmegosztást keres a portálon

Amikor egy Azure-fájlmegosztást keres a portálon, a következő hibaüzenetet kaphatja:

Engedélyezési hiba  
Nincs hozzáférése 

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>1\. ok: A felhasználói fiók nem rendelkezik hozzáféréssel a Storage-fiókhoz

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

Keresse meg azt a Storage-fiókot, ahol az Azure-fájlmegosztás található, kattintson a **hozzáférés-vezérlés (iam)** elemre, és ellenőrizze, hogy a felhasználói fiókja rendelkezik-e hozzáféréssel a Storage-fiókhoz. További információt a [Storage-fiók biztonságossá tétele szerepköralapú Access Control (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac)című témakörben talál.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2\. ok: A virtuális hálózat vagy a tűzfalszabályok engedélyezve vannak a Storage-fiókon.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nem lehet törölni egy fájlt vagy könyvtárat egy Azure-fájlmegosztás

### <a name="cause"></a>Ok
Ez a probléma általában akkor fordul elő, ha a fájl vagy könyvtár nyitott leíróval rendelkezik. 

### <a name="solution"></a>Megoldás

Ha az SMB-ügyfelek lezárták az összes nyitott leírót, és a probléma továbbra is fennáll, hajtsa végre a következőket:

- A [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-parancsmag használatával tekintheti meg a nyitott leírókat.

- A [AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell-parancsmag használatával zárhatja be a megnyitott leírókat. 

> [!Note]  
> A Get-AzStorageFileHandle és a AzStorageFileHandle parancsmag az az PowerShell-modul 2,4-es vagy újabb verziójában szerepel. A legújabb az PowerShell modul telepítéséhez lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Lassú fájlmásolás a Windows rendszerbe és Azure Filesba

Előfordulhat, hogy a lassú teljesítmény jelenik meg, amikor fájlokat próbál továbbítani az Azure file Service-be.

- Ha nem rendelkezik meghatározott minimális I/O-mérettel, javasoljuk, hogy az optimális teljesítmény érdekében az 1 MiB-t használja az I/O-mérethez.
-   Ha ismeri az írásokkal kiterjeszthető fájl végső méretét, és a szoftver nem rendelkezik kompatibilitási problémákkal, ha a fájl íratlan farka nulla értéket tartalmaz, a fájl méretét előre állítsa be ahelyett, hogy minden írási kibővítést ír.
-   Használja a megfelelő másolási módszert:
    -   Használjon [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a két fájlmegosztás közötti átvitelhez.
    -   Egy helyszíni számítógépen lévő fájlmegosztás esetén használja a [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) szolgáltatást.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>A Windows 8,1 vagy a Windows Server 2012 R2 szempontjai

Windows 8,1 vagy Windows Server 2012 R2 rendszerű ügyfelek esetén győződjön meg arról, hogy a [KB3114025](https://support.microsoft.com/help/3114025) gyorsjavítás telepítve van. Ez a gyorsjavítás javítja a létrehozási és a bezárási fogópontok teljesítményét.

A következő parancsfájl futtatásával ellenőrizhető, hogy telepítve van-e a gyorsjavítás:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Ha a gyorsjavítás telepítve van, a következő kimenet jelenik meg:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> A Windows Server 2012 R2 rendszerképek az Azure Marketplace-en alapértelmezés szerint a gyorsjavítások KB3114025 települnek, a 2015-es verziótól kezdődően.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nincs "Sajátgép" vagy "Ez a számítógép" betűjelű mappa

Ha egy Azure-fájlmegosztást rendszergazdaként rendel a net use használatával, akkor a megosztás hiányzik.

### <a name="cause"></a>Ok

Alapértelmezés szerint a Windows file Explorer nem rendszergazdaként fut. Ha a net használatát rendszergazdai parancssorból futtatja, a hálózati meghajtót rendszergazdaként kell leképezni. Mivel a csatlakoztatott meghajtók felhasználó-központú, a bejelentkezett felhasználói fiók nem jeleníti meg a meghajtókat, ha egy másik felhasználói fiókhoz vannak csatlakoztatva.

### <a name="solution"></a>Megoldás
Csatlakoztassa a megosztást egy nem rendszergazdai parancssorból. Azt is megteheti, hogy [ezt a TechNet](https://technet.microsoft.com/library/ee844140.aspx) -témakört követve konfigurálja a **EnableLinkedConnections** beállításazonosító értékét.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>A net use parancs végrehajtása meghiúsul, ha a Storage-fiók perjelet tartalmaz

### <a name="cause"></a>Ok

A net use parancs parancssori kapcsolóként értelmezi a továbbítási perjelet (/). Ha a felhasználói fiók neve egy továbbítási perjeltel kezdődik, a meghajtó megfeleltetése sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megkerüléséhez a következő lépések bármelyikét használhatja:

- Futtassa az alábbi PowerShell-parancsot:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A Batch-fájlból a következő módon futtathatja a parancsot:

  `Echo new-smbMapping ... | powershell -command –`

- A probléma megkerüléséhez dupla idézőjelek közé kell tenni a kulcsokat – kivéve, ha a továbbítás perjele az első karakter. Ha igen, használja az interaktív módot, és adja meg a jelszót külön vagy a kulcsok újragenerálása egy olyan kulcs lekéréséhez, amely nem a továbbítás perjelével kezdődik.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Az alkalmazás vagy szolgáltatás nem fér hozzá csatlakoztatott Azure Files meghajtóhoz

### <a name="cause"></a>Ok

A meghajtók felhasználónként vannak csatlakoztatva. Ha az alkalmazás vagy szolgáltatás egy másik felhasználói fiók alatt fut, mint amelyik a meghajtót csatlakoztatta, az alkalmazás nem fogja látni a meghajtót.

### <a name="solution"></a>Megoldás

Használja az alábbi megoldások valamelyikét:

-   Csatlakoztassa a meghajtót ugyanahhoz a felhasználói fiókhoz, amely tartalmazza az alkalmazást. Használhat olyan eszközt is, mint például a PsExec.
- Adja át a Storage-fiók nevét és kulcsát a net use parancs Felhasználónév és jelszó paraméterében.
- A cmdkey parancs használatával adja hozzá a hitelesítő adatokat a hitelesítőadat-kezelőhöz. Ezt egy interaktív bejelentkezéssel vagy a runas használatával hajtsa végre a szolgáltatásfiók környezetében.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Rendelje hozzá a megosztást közvetlenül a csatlakoztatott meghajtóbetűjel használata nélkül. Előfordulhat, hogy egyes alkalmazások nem csatlakoznak megfelelően a meghajtóbetűjelhez, így a teljes UNC elérési út megbízhatóbb lehet. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Miután elvégezte ezeket az utasításokat, a következő hibaüzenet jelenhet meg, amikor a rendszer-/hálózati szolgáltatásfiók hálózati használatát futtatja: "A 1312-es rendszerhiba történt. Egy megadott bejelentkezési munkamenet nem létezik. Lehetséges, hogy már meg lett szakítva. " Ha ez történik, győződjön meg arról, hogy a net use szolgáltatásnak átadott Felhasználónév tartományi adatokat tartalmaz (például: "[Storage Account name]. file. Core. Windows. net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Hiba: "a fájl másolása olyan célhelyre történik, amely nem támogatja a titkosítást"

Ha egy fájlt a hálózaton keresztül másol a rendszer, a rendszer visszafejti a fájlt a forrásszámítógépen, amely egyszerű szöveges formátumban lesz továbbítva és újra titkosítva van a célhelyen. A titkosított fájlok másolásakor azonban a következő hiba jelenhet meg: "A fájlt olyan célhelyre másolja, amely nem támogatja a titkosítást."

### <a name="cause"></a>Ok
Ez a probléma akkor fordulhat elő, ha titkosított fájlrendszert (EFS) használ. A BitLocker által titkosított fájlok átmásolhatók Azure Filesba. A Azure Files azonban nem támogatja az NTFS EFS használatát.

### <a name="workaround"></a>Áthidaló megoldás
Ha egy fájlt hálózaton keresztül szeretne másolni, először vissza kell fejtenie azt. Használja az alábbi módszerek egyikét:

- Használja a **copy/d** parancsot. Lehetővé teszi a titkosított fájlok mentését visszafejtett fájlként a célhelyen.
- Állítsa be a következő beállításkulcsot:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Értéktípus = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Érték = 1

Vegye figyelembe, hogy a beállításkulcs beállítása hatással van a hálózati megosztásokon végrehajtott összes másolási műveletre.

## <a name="slow-enumeration-of-files-and-folders"></a>Fájlok és mappák lassú számbavétele

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha nincs elegendő gyorsítótár az ügyfélszámítógépen a nagyméretű könyvtárakhoz.

### <a name="solution"></a>Megoldás

A probléma megoldásához módosítsa a **DirectoryCacheEntrySizeMax** beállításazonosító értékét, hogy engedélyezze a nagyobb könyvtár-listák gyorsítótárazását az ügyfélszámítógépen:

- Hely: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Érték Mane: DirectoryCacheEntrySizeMax 
- Érték típusa: DWORD
 
 
Beállíthatja például a 0x100000, és megtekintheti, hogy a teljesítmény jobb legyen.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Hiba történt a (z) Azure Files "Azure Active Directory hitelesítésének engedélyezésekor a (z)" nem találja a (z) HRE bérlői azonosítóval rendelkező aktív bérlőket "AadDsTenantNotFound

### <a name="cause"></a>Ok

Hiba történt abban az esetben, ha a AadDsTenantNotFound [Azure Active Directory (HRE) hitelesítését](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) kísérli meg Azure Files egy olyan Storage-fiókon, ahol a [HRE tartományi szolgáltatás (HRE DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) nem jön létre a társított előfizetés HRE-bérlője számára.  

### <a name="solution"></a>Megoldás

Engedélyezze a HRE DS szolgáltatást annak az előfizetésnek a HRE-bérlőn, amelyre a Storage-fiók telepítve van. Felügyelt tartomány létrehozásához rendszergazdai jogosultsággal kell rendelkeznie a HRE-bérlőhöz. Ha nem az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse a lépésenkénti útmutatót, amely [lehetővé teszi Azure Active Directory Domain Services használatát a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha továbbra is segítségre van szüksége, forduljon az ügyfélszolgálathoz, és [kérje](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) meg a probléma gyors megoldását.
