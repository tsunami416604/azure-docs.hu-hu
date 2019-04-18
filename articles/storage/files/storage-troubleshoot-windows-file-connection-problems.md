---
title: A Windows Azure Files-problémák elhárítása |} A Microsoft Docs
description: A Windows Azure Files-problémák hibaelhárítása
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9849b8209db0a4aa73a80d461b67bda9b0b3656a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049727"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>A Windows Azure Files-problémák hibaelhárítása

Ez a cikk a gyakori problémák a Microsoft Azure Files kapcsolódó Windows-ügyfelek csatlakozáskor sorolja fel. Is biztosít a lehetséges okokért és megoldásokért ezeket a problémákat. A hibaelhárítási lépéseket ebben a cikkben mellett is használhatja [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) , hogy a Windows ügyfél környezet legyen-e a megfelelő előfeltételek. AzFileDiagnostics automatizálja a jelenség a jelen cikkben említett, és a segítségével állítsa be a környezetet az optimális teljesítmény eléréséhez a legtöbb felismerése. Ezt az információt is megtalálhatja a [Azure-fájlmegosztási hibaelhárító](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) , amelyek segítenek a problémák az Azure Files csatlakozás és leképezés/csatlakoztatási fájlmegosztások lépéseit ismerteti.

<a id="error5"></a>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-5-when-you-mount-an-azure-file-share"></a>Ha csatlakoztatja az Azure-fájlmegosztások 5 hiba

Fájlmegosztást megkísérlésekor a következő hiba jelenhet meg:

- 5. rendszerhiba történt. A hozzáférés megtagadva.

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: Nem titkosított kommunikációs csatornát

Biztonsági okokból az Azure-fájlmegosztások kapcsolatok le lesznek tiltva, ha a kommunikációs csatornát nincs titkosítva, és ha a csatlakozási kísérlet nem ugyanabban az adatközpontban az Azure-fájlmegosztások-ket. Titkosítatlan kapcsolat az adatközpontokon belül is blokkolhatók, ha a [biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás engedélyezve van a tárfiókon. Egy titkosított kommunikációs csatornát csak akkor, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosításra van megadva.

A Windows 8, Windows Server 2012 és egyes rendszerek újabb verzióit egyeztetni az SMB 3.0-s, amely támogatja a titkosítást tartalmazó kérelmeket.

### <a name="solution-for-cause-1"></a>1 OK megoldás

1. Csatlakozás ügyfélről, amely támogatja az SMB-titkosítás (a Windows 8, Windows Server 2012 vagy újabb), vagy csatlakozzon a virtuális gépről az Azure-fájlmegosztás használt Azure storage-fiókban, ugyanabban az adatközpontban.
2. Ellenőrizze a [biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva a tárfiókban, ha az ügyfél nem támogatja az SMB-titkosítás.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2. ok: Virtuális hálózat vagy a tűzfal-szabályok engedélyezve vannak a storage-fiók 

Ha a virtuális hálózat (VNET) és -tűzfalszabályok konfigurálása a storage-fiók, hálózati forgalom megtagadja a hozzáférést, kivéve, ha az ügyfél IP-cím vagy a virtuális hálózati hozzáférés engedélyezett.

### <a name="solution-for-cause-2"></a>Megoldás ok 2

Ellenőrizze a virtuális hálózat és tűzfal-szabályok megfelelően van-e beállítva a tárfiókban. Tesztelése Amennyiben virtuális hálózat vagy a tűzfal-szabályok okozza a problémát, ideiglenesen módosíthatja a beállítás a tárfiók **engedélyezze a hozzáférést minden hálózatból elérhető**. További tudnivalókért lásd: [konfigurálása az Azure Storage-tűzfalak és virtuális hálózatok](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>53-as hiba, a 67-es hiba vagy a hiba 87 csatlakoztatása vagy leválasztása az Azure-fájlmegosztások

Amikor egy fájlmegosztás csatlakoztatása a helyszíni, illetve egy másik adatközpontban oldja meg, a következő hibákat kaphat:

- 53-as rendszerhiba történt. A hálózati elérési út nem található.
- 67-es rendszerhiba történt. A hálózatnév nem található.
- 87 rendszerhiba történt. A paraméter helytelen.

### <a name="cause-1-port-445-is-blocked"></a>1. ok: 445-ös port le van tiltva.

Rendszerhiba: 53-as vagy 67-es rendszerhiba akkor fordulhat elő, ha az Azure Files-adatközpont a kimenő kommunikáció 445-ös port le van tiltva. Tekintse meg, hogy a 445-ös port elérésének engedélyezése vagy letiltása az internetszolgáltatók összegzését, lépjen a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Ha a tűzfal- vagy Internetszolgáltatói blokkolja a 445-ös ellenőrzéséhez használja a [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) eszköz vagy `Test-NetConnection` parancsmagot. 

Használatához a `Test-NetConnection` parancsmag, az Azure PowerShell modul telepítve kell lennie, lásd: [Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) további információt. Ne felejtse el kicserélni a `<your-storage-account-name>` és a `<your-resource-group-name>` elemet a tárfiók vonatkozó neveivel.

   
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

### <a name="solution-for-cause-1"></a>1 OK megoldás

#### <a name="solution-1---use-azure-file-sync"></a>1 – használja az Azure File Sync megoldás
Az Azure File Sync is alakítja át a helyszíni Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá. Helyileg, az adatok eléréséhez a Windows Serveren elérhető bármely protokollt használhatja, beleértve az SMB, NFS és FTPS. Az Azure File Sync 443-as porton keresztül működik, és így használható áthidaló elérni az Azure Filest ügyfelektől, amelyek a 445-ös blokkolva van. [Ismerje meg, hogyan állíthatja be az Azure File Sync](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>2. VPN használatát megoldás
A megadott tárfiókhoz VPN beállításával a forgalom fog áthaladni egy biztonságos alagúton, nem pedig az interneten keresztül. Kövesse a [utasításokat követve VPN Hálózatának beállítása](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) a Windows Azure-fájlok eléréséhez.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>3 - megoldás segítségével az Internetszolgáltató 445-ös port tiltásának feloldása / rendszergazda
Az IT-részleg vagy megnyitni a 445-ös kimenő Internetszolgáltató [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>4 – megoldás-alapú eszközök például a Storage Explorer/Powershell REST API használata
Az Azure Files SMB mellett REST is támogatja. REST-alapú elérését a 443-as (szabványos tcp-)-en keresztül működik. Nincsenek különböző eszközöket REST API használatával írt, amelyek lehetővé teszik a felhasználói felület gazdag. [Storage Explorer](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) az egyik közülük. [Letöltés és a Storage Explorer telepítése](https://azure.microsoft.com/en-us/features/storage-explorer/) és csatlakozni a fájlmegosztáshoz, az Azure Files alapját. Is [PowerShell](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-powershell) amely is felhasználó REST API-t.


### <a name="cause-2-ntlmv1-is-enabled"></a>2. ok: NTLMv1 engedélyezve van

Rendszerhiba: 53-as vagy rendszerhiba: 87 akkor fordulhat elő, ha NTLMv1 kommunikáció engedélyezve van az ügyfélen. Az Azure Files csak NTLMv2-alapú hitelesítést támogatja. A kevésbé biztonságos ügyfél engedélyezett NTLMv1 kellene hoz létre. Ezért kommunikációja blokkolva van az Azure Files számára. 

Annak megállapításához, hogy ez-e a hiba okának, győződjön meg arról, hogy az alábbi beállításjegyzékbeli alkulcs 3 értékre van állítva:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

További információkért lásd: a [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) a témakör a TechNet webhelyén.

### <a name="solution-for-cause-2"></a>Megoldás ok 2

Visszaállítás a **LmCompatibilityLevel** érték 3 az alábbi beállításjegyzékbeli alkulcs alapértelmezett értéke:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Hiba 1816 "nincs elegendő kvótája nem érhető el, a parancs végrehajtásához" Ha az Azure-fájlmegosztás másolása

### <a name="cause"></a>Ok

1816 hiba akkor fordul elő, ha egyenlege eléri egy fájlt a számítógépen, ahol a fájlmegosztás csatlakoztatva engedélyezett egyidejű megnyitott leíróinak felső határát.

### <a name="solution"></a>Megoldás

Egyidejű megnyitott leírók számának csökkentése zárja be az egyes kezeli, és próbálkozzon újra. További információkért lásd: [a Microsoft Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>"Hozzáférés megtagadva" hiba történik az Azure-fájlmegosztások a portálon

Ha a felhasználó Azure-fájlmegosztások a portálon, a következő hiba jelenhet meg:

Hozzáférés megtagadva  
Nincs hozzáférése  
Úgy tűnik, nincs hozzáférése a tartalomhoz. Érhet el, forduljon a tulajdonosa.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>1. ok: A felhasználói fiók nem rendelkezik a tárfiókhoz való hozzáférést

### <a name="solution-for-cause-1"></a>1 OK megoldás

Tallózással keresse meg a tárfiókot, ahol az Azure-fájlmegosztás, kattintson a **hozzáférés-vezérlés (IAM)** , és ellenőrizze a felhasználói fiók rendelkezik-e a tárfiókhoz való hozzáférést. További tudnivalókért lásd: [hogyan tegye biztonságossá tárfiókját, a szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2. ok: Virtuális hálózat vagy a tűzfal-szabályok engedélyezve vannak a storage-fiók

### <a name="solution-for-cause-2"></a>Megoldás ok 2

Ellenőrizze a virtuális hálózat és tűzfal-szabályok megfelelően van-e beállítva a tárfiókban. Tesztelése Amennyiben virtuális hálózat vagy a tűzfal-szabályok okozza a problémát, ideiglenesen módosíthatja a beállítás a tárfiók **engedélyezze a hozzáférést minden hálózatból elérhető**. További tudnivalókért lásd: [konfigurálása az Azure Storage-tűzfalak és virtuális hálózatok](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Lassú fájl másolása és a Windows Azure-fájlokból

Lassú teljesítmény vinni a fájlokat az Azure File Storage-szolgáltatás megkísérlésekor jelenhet meg.

- Ha nem rendelkezik egy adott minimális i/o vonatkozó méretbeli követelményt, ajánlott, hogy 1 MiB, az i/o-mérete az optimális teljesítmény érdekében.
-   Ha ismeri a végleges mérethez, amely bővíti az írási műveletek egy fájl, és a szoftver nem rendelkezik kompatibilitási problémákat, ha a következő fájlon íratlan végéről nullát tartalmaz, majd állítsa be a fájl mérete előzetesen már nem kell minden írási egy kiterjesztésének írási.
-   A jobb oldali másolási módszert használják:
    -   Használat [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) bármely két fájlmegosztások közötti átvitel céljából.
    -   Használat [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) egy helyszíni számítógépen található fájlmegosztások között.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Szempontok a Windows 8.1 vagy Windows Server 2012 R2 rendszerben

Windows 8.1 vagy Windows Server 2012 R2 rendszert futtató ügyfelek győződjön meg arról, hogy a [KB3114025](https://support.microsoft.com/help/3114025) gyorsjavítás telepítve van. Ez a gyorsjavítás javítja a létrehozás, és zárja be a kezeli.

Ellenőrizze, hogy a gyorsjavítás telepítve van-e a következő szkriptet futtathatja:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Ha a gyorsjavítás telepítve van, a következő kimenet jelenik meg:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Azure Marketplace-ről Windows Server 2012 R2-rendszerképeket gyorsjavítás KB3114025 alapértelmezés szerint telepítve van a 2015 decemberében indítása rendelkezik.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Nincs mappát a meghajtó betűjelére **Sajátgép**

Ha az Azure-fájlmegosztások rendszergazdaként hálózathasználati használatával, a megosztás úgy tűnik, hogy nem érhető el.

### <a name="cause"></a>Ok

Alapértelmezés szerint a Windows Fájlkezelőben nem futtassa rendszergazdaként. Ha hálózathasználati parancsot egy rendszergazdai parancssorból futtatja, akkor a hálózati meghajtó csatlakoztatása rendszergazdaként. Mivel a csatlakoztatott meghajtók felhasználó-központú, a felhasználói fiókkal van bejelentkezve, nem jelennek meg a meghajtók ha csatlakoztatva vannak egy másik felhasználói fiókkal.

### <a name="solution"></a>Megoldás
A megosztás nem rendszergazdai parancssorból történő csatlakoztatásához. Másik lehetőségként kövesse [a TechNet-témakör](https://technet.microsoft.com/library/ee844140.aspx) konfigurálása a **EnableLinkedConnections** beállításazonosítót.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Net use parancs sikertelen lesz, ha a tárfiók perjelet tartalmaz

### <a name="cause"></a>Ok

A net use parancs perjellel (/) kezeli, mivel a parancssori kapcsolót. Ha a felhasználói fiók nevét perjellel kezdődik, a meghajtó hozzárendelése sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megoldásához használhatja az alábbi lépések valamelyikét:

- Futtassa az alábbi PowerShell-parancsot:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A fájlt egy kötegfájlban futtathatja a parancsot ezzel a módszerrel:

  `Echo new-smbMapping ... | powershell -command –`

- Helyezze a kulcsot – a probléma megkerüléséhez körüli dupla idézőjelekkel együtt, kivéve, ha a perjel első karaktere. Ha igen, az interaktív mód használja, és külön adja meg a jelszót, vagy nem perjellel kezdődik a kulcs lekérése a kulcsok újragenerálása.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Alkalmazás vagy szolgáltatás nem férhetnek hozzá a csatlakoztatott Azure Files-meghajtóhoz

### <a name="cause"></a>Ok

Csatlakoztatott meghajtók felhasználónként. Ha az alkalmazás vagy szolgáltatás, amely nem a meghajtó csatlakoztatva van egy másik felhasználói fiók alatt fut, az alkalmazás nem jelenik meg a meghajtót.

### <a name="solution"></a>Megoldás

Használja az alábbi megoldások valamelyikét:

-   Csatlakoztassa a meghajtó ugyanazzal a fiókkal, amely tartalmazza az alkalmazást. Használhatja például a PsExec eszköz.
- A tárfiók nevét és a kulcsot adja meg a felhasználói nevet és a jelszó paramétereit a háló parancs használata.
- A cmdkey paranccsal adja hozzá a hitelesítő adatok hitelesítőadat-kezelő be. A szolgáltatás fiók környezetében, interaktív bejelentkezést vagy futtató használatával parancssorból hajtsa végre ezt.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Képezze le a megosztást közvetlenül csatlakoztatott meghajtóbetűjel nélkül. Egyes alkalmazások előfordulhat, hogy nem újracsatlakozás a meghajtó betűjeléhez megfelelően, így a teljes UNC elérési út használatával megbízhatóbb lehet. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Kövesse ezeket az utasításokat, miután a rendszer és a hálózati szolgáltatás fiók hálózathasználati futtatásakor kaphat a következő hibaüzenetet kapja: "A 1312 rendszerhiba történt. A megadott bejelentkezési munkamenet nem létezik. Azt is, hogy már befejeződött." Ha ez történik, ügyeljen arra, hogy a felhasználónév, amelyet hálózathasználati átadott tartományadatokat (például: "[tárfiók neve]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Hiba történt ", a fájl másolásának célhelye nem támogatja a titkosítást"

A hálózaton keresztül másolja a fájlt, ha a fájl visszafejtése a forrásoldali számítógépen, egyszerű szövegként továbbított adatok köre, és újra titkosítja a célhelyen. Láthatja, a következő hiba, ha megpróbál egy titkosított fájl másolása: ", A fájl másolásának célhelye nem támogatja a titkosítást."

### <a name="cause"></a>Ok
Ez a probléma akkor fordulhat elő, ha titkosított fájlrendszer (EFS) használja. BitLocker-titkosítású fájlokat az Azure Files lehet másolni. Azure Files azonban nem támogatja a titkosított NTFS fájlrendszer.

### <a name="workaround"></a>Áthidaló megoldás
Fájl másolása a hálózaton keresztül, akkor először vissza kell fejtenie azt. Az alábbi módszerek valamelyikével:

- Használja a **/d másolása** parancsot. Lehetővé teszi a titkosított fájlokat, és a visszafejtett fájlokként a célhelyen.
- Állítsa be a következő beállításkulcsot:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Érték típusa = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Érték = 1

Vegye figyelembe, hogy a beállításkulcs megadása hatással van minden, hálózati megosztások végzett másolási művelet.

## <a name="slow-enumeration-of-files-and-folders"></a>A fájlok és mappák lassú enumerálása

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha nincs elegendő gyorsítótárazás nagy méretű könyvtárak ügyfélszámítógépen.

### <a name="solution"></a>Megoldás

Ez a probléma megoldásához módosításával a **DirectoryCacheEntrySizeMax** beállításazonosítót a gyorsítótárba helyezése az ügyfélszámítógépen a nagyobb könyvtárlistákon:

- Hely: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Érték mane: DirectoryCacheEntrySizeMax 
- Típus: DWORD érték
 
 
Például beállíthatja azt a 0x100000, és tekintse meg, ha a teljesítmény jobb válnak.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Hiba AadDsTenantNotFound az Azure Files "Nem található az aad-tenant-id azonosító bérlő aktív bérlők" Azure Active Directory-hitelesítés engedélyezése

### <a name="cause"></a>Ok

AadDsTenantNotFound hiba akkor fordul elő, amikor megpróbálja [engedélyezése az Azure Active Directory (AAD) hitelesítés az Azure Files számára](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) a storage-fiók ahol [AAD tartományi Service(AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) nem jön létre az aad-ben a társított előfizetés bérlő.  

### <a name="solution"></a>Megoldás

AAD tartományi szolgáltatások engedélyezése az AAD-bérlő az előfizetés üzembe helyezett storage-fiókjában található. Az AAD-bérlő létrehozása egy felügyelt tartomány rendszergazdai jogosultságok szükségesek. Ha nem az Azure AD-bérlő rendszergazdája, forduljon a rendszergazdához, és kövesse a részletes útmutatónkat [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.
