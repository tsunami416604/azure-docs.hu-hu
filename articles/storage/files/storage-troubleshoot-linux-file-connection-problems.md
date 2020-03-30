---
title: Az Azure Files problémáinak elhárítása Linux alatt | Microsoft dokumentumok
description: Az Azure Files problémáinak elhárítása Linux alatt
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 95e220102cba290664a32cb6bbebef881ae4ffde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159489"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Az Azure Files hibáinak elhárítása Linux alatt

Ez a cikk az Azure-knak, linuxos ügyfelekről való csatlakozáskor az Azure Files-hoz kapcsolódó gyakori problémákat sorolja fel. A probléma lehetséges okait és megoldásait is tartalmazza. 

A cikkben ismertetett hibaelhárítási lépések mellett az [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) segítségével biztosíthatja, hogy a Linux-ügyfél megfelelő előfeltételekkel rendelkezzen. AzFileDiagnostics automatizálja a cikkben említett legtöbb tünet észlelését. Segít a környezet beállításához az optimális teljesítmény érdekében. Ezeket az információkat az [Azure Files megosztások hibaelhárítójában](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)is megtalálhatja. A hibaelhárító lépéseket nyújt az Azure Files-megosztások csatlakoztatásával, leképezésével és csatlakoztatásával kapcsolatos problémák megoldásához.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nem lehet csatlakozni vagy csatlakoztatni egy Azure-fájlmegosztást

### <a name="cause"></a>Ok

A probléma gyakori okai a következők:

- Nem kompatibilis Linux-terjesztési ügyfelet használ. Azt javasoljuk, hogy a következő Linux-disztribúciók segítségével csatlakozzon egy Azure-fájlmegosztáshoz:

|   | SMB 2.1 <br>(Ugyanazon Azure-régión belüli virtuális gépeken való csatlakoztatása) | SMB 3.0 <br>(A helyszíni és a régión átkelő csatolók) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ |   |
| openSUSE | 13,2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- A CIFS segédprogramok (CIFS-utils) nincsenek telepítve az ügyfélre.
- A minimális SMB/CIFS verzió (2.1) nincs telepítve az ügyfélre.
- Az SMB 3.0 titkosításnem támogatott az ügyfélen. Az előző táblázat tartalmazza a Linux-disztribúciók listáját, amelyek támogatják a helyszíni és a régióközi titkosítással történő csatlakoztatást. Az egyéb disztribúciók esetében a 4.11-es vagy újabb kernelverzió szükséges.
- Egy 445-ös TCP-porton keresztül próbál csatlakozni egy tárfiókhoz, amely nem támogatott.
- Egy Azure-beli virtuális gépről próbál csatlakozni egy Azure-fájlmegosztáshoz, és a virtuális gép nem ugyanabban a régióban van, mint a tárfiók.
- Ha a [biztonságos átvitel szükséges]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás engedélyezve van a tárfiókban, az Azure Files csak titkosítással SMB 3.0-s kapcsolatokat engedélyez.

### <a name="solution"></a>Megoldás

A probléma megoldásához használja az [Azure Files szerelési hibáinak hibaelhárító eszközét Linux on](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Ez az eszköz:

* Segít az ügyfél futó környezetének érvényesítésében.
* Észleli a nem kompatibilis ügyfélkonfigurációt, amely az Azure Files hozzáférési hibáját okozhatja.
* Előíró útmutatást ad az önrögzítéshez.
* Összegyűjti a diagnosztikai nyomokat.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Csatlakoztatási hiba(13): Az engedély megtagadva" Azure-fájlmegosztás csatlakoztatásakor

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: Titkosítatlan kommunikációs csatorna

Biztonsági okokból az Azure-fájlmegosztásokhoz való kapcsolódás le van tiltva, ha a kommunikációs csatorna nincsen titkosítva, vagy ha a csatlakozási kísérlet nem ugyanabból az adatközpontból történik, ahol az Azure-fájlmegosztások találhatók. Az ugyanazon adatközponton belüli titkosítatlan kapcsolatokat akkor is blokkolhatja a rendszer, ha a tárfiókban engedélyezve van a [Biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás. Csak akkor biztosítható titkosított kommunikációs csatorna, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosítást.

További információ: [Azure-fájlmegosztás Linux és a cifs-utils csomag használatával történő csatlakoztatásának előfeltételei](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Csatlakozzon egy ügyfél, amely támogatja az SMB-titkosítást, vagy csatlakozzon egy virtuális gép ugyanabban az adatközpontban, mint az Azure-tárfiók, amely az Azure-fájlmegosztáshoz használt.
2. Ellenőrizze, hogy a [biztonságos átvitel szükséges](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van-e tiltva a tárfiókban, ha az ügyfél nem támogatja az SMB titkosítást.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2. ok: A virtuális hálózati vagy tűzfalszabályok engedélyezve vannak a tárfiókban 

Ha virtuális hálózati (VNET) és tűzfalszabályok vannak konfigurálva a tárfiókhoz, a hálózati forgalom számára blokkolva lesz a hozzáférés, kivéve, ha az ügyfél IP-címe vagy a virtuális hálózat hozzáférést kapott.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[permission denied] Disk quotexceeded" when you try to open a file

Linux alatt a következőhöz hasonló hibaüzenet jelenik meg:

**\<fájlnév> [engedély megtagadva] A lemezkvóta túllépve**

### <a name="cause"></a>Ok

Elérte a fájlhoz engedélyezett egyidejű nyitott leírók felső határát.

Egy fájlban 2000 megnyitott leíróból álló kvóta található. Ha 2000 megnyitott leírója van, egy hibaüzenet jelenik meg, amely szerint a kvóta elérésekor.

### <a name="solution"></a>Megoldás

Csökkentse az egyidejűnyitott fogópontok számát néhány fogópont bezárásával, majd próbálkozzon újra a művelettel.

Fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak megtekintéséhez használja a [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell parancsmagját.  

Fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak bezárásához használja a [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell parancsmagját.

> [!Note]  
> A Get-AzStorageFileHandle és close-AzStorageFileHandle parancsmagok az Az PowerShell modul 2.4-es vagy újabb verziójában találhatók. A legújabb Az PowerShell-modul telepítéséről az [Azure PowerShell-modul telepítése című témakörben látható.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Lassú fájlmásolás az Azure Files-ba és -ból Linux alatt

- Ha nem rendelkezik egy adott minimális I/O-méretkövetelményrel, javasoljuk, hogy az optimális teljesítmény érdekében 1 MiB-t használjon I/O-méretként.
- Használja a megfelelő másolási módszert:
    - Két fájlmegosztás közötti átvitelhez használja az [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) programot.
    - A cp vagy dd párhuzamos használata javíthatja a másolássebességét, a szálak száma a használati esettől és a munkaterheléstől függ. A következő példák hat példát használnak: 
    - cp példa (a cp a fájlrendszer alapértelmezett blokkméretét `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`fogja használni adattömbméretként): .
    - dd példa (ez a parancs explicit módon 1 MiB-re állítja az adattömbméretet):`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Nyílt forráskódú, harmadik féltől származó eszközök, mint például:
        - [GNU párhuzamos](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) - Rendezi a fájlokat, és csomagokat partíciókat.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - A Fpart és egy másolási eszköz segítségével több példányt hoz létre az adatok src_dir-ról dst_url.
        - [Multi](https://github.com/pkolano/mutil) - Többszálú cp és md5sum gnu coreutils alapján.
- Ha előre beállítja a fájlméretet, ahelyett, hogy minden írást kiterjesztene egy kiterjesztő írásra, az segít javítani a másolási sebességet olyan esetekben, amikor a fájlméret ismert. Ha el kell kerülni az írási műveletek kiterjesztését, `truncate - size <size><file>` a célfájl méretét a paranccsal állíthatja be. Ezután `dd if=<source> of=<target> bs=1M conv=notrunc`a parancs a forrásfájlt anélkül másolja, hogy ismételten frissítenie kellene a célfájl méretét. Beállíthatja például a másolni kívánt összes fájl célfájlméretét (tegyük fel, hogy egy megosztás a /mnt/share alatt van csatlakoztatva):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - majd - fájlok másolása anélkül, hogy párhuzamosan kiterjesztené az írásokat:`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Mount error(115): Operation now in progress" when you mount Azure Files using SMB 3.0

### <a name="cause"></a>Ok

Egyes Linux-disztribúciók még nem támogatják az SMB 3.0 titkosítási szolgáltatásait. Előfordulhat, hogy a felhasználók az Azure Files az SMB 3.0-val történő csatlakoztatásakor egy hiányzó szolgáltatás miatt egy „115”-ös hibaüzenetet kapnak. Az SMB 3.0 teljes körű titkosítását csak az Ubuntu 16.04-es vagy újabb verziói támogatják.

### <a name="solution"></a>Megoldás

A Linux rendszerhez készült SMB 3.0 titkosítási szolgáltatása a 4.11-es kernellel jelent meg. Ez a szolgáltatás lehetővé teszi egy helyszíni vagy egy más Azure régióban tárolt Azure-fájlmegosztás csatlakoztatását. Egyes Linux disztribúciók visszatudták a 4.11 kernelről az általuk fenntartott Linux kernel régebbi verzióira történő módosításokat. Annak meghatározásához, hogy a Linux-verziója támogatja-e az SMB 3.0 titkosítását, forduljon az [Azure Files használata Linuxhoz](storage-how-to-use-files-linux.md)című részéhez. 

Ha a Linuxos SMB-ügyfél nem támogatja a titkosítást, az Azure Files csatlakoztatásához használja az SMB 2.1-et egy olyan Azure-beli, Linux rendszerű virtuális gépen, amely ugyanabban az adatközpontban található, mint a fájlmegosztás. Győződjön meg arról, hogy [Biztonságos átvitelre van szükség]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva a tárfiókban. 

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

### <a name="cause"></a>Ok
Ez a probléma általában akkor fordul elő, ha a fájl vagy könyvtár nyitott leíróval rendelkezik. 

### <a name="solution"></a>Megoldás

Ha az SMB-ügyfelek lezárták az összes nyitott leírót, és a probléma továbbra is fennáll, hajtsa végre a következőket:

- A [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell parancsmag használatával megtekintheti a megnyitott fogópontokat.

- A [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell parancsmag segítségével zárja be a megnyitott fogópontokat. 

> [!Note]  
> A Get-AzStorageFileHandle és close-AzStorageFileHandle parancsmagok az Az PowerShell modul 2.4-es vagy újabb verziójában találhatók. A legújabb Az PowerShell-modul telepítéséről az [Azure PowerShell-modul telepítése című témakörben látható.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux virtuális géphez csatlakoztatott Azure-fájlmegosztás lassú teljesítménye

### <a name="cause-1-caching"></a>1. ok: Gyorsítótárazás

A lassú teljesítmény egyik lehetséges oka a letiltás. A gyorsítótárazás akkor lehet hasznos, ha ismételten hozzáfér egy fájlhoz, ellenkező esetben többletterhelés térhet el. Mielőtt letiltana, ellenőrizze, hogy használja-e a gyorsítótárat.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

Annak ellenőrzéséhez, hogy a gyorsítótárazás le van-e tiltva, keresse meg a **cache=** bejegyzést.

**A Cache=none** azt jelzi, hogy a gyorsítótárazás le van tiltva. Csatlakoztassa újra a megosztást az alapértelmezett csatlakoztatási paranccsal, vagy explicit módon adja hozzá a **cache=strict** opciót a csatlakoztatási parancshoz annak érdekében, hogy az alapértelmezett gyorsítótárazás vagy a "szigorú" gyorsítótárazási mód engedélyezve legyen.

Bizonyos esetekben a **serverino csatlakoztatási** beállítás hatására az **ls** parancs stat fut minden könyvtárbejegyzést. Ez a viselkedés teljesítménycsökkenést eredményez, amikor nagy könyvtárat sorol fel. Az **/etc/fstab** bejegyzésben ellenőrizheti a csatlakoztatási beállításokat:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Azt is ellenőrizheti, hogy a **sudo csatlakoztatás futtatásával és** a kimenet ellenőrzésével a megfelelő beállításokat használja-e. A következő példa kimenet:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Ha a **cache=strict** vagy **serverino** beállítás nincs jelen, válassza le és csatlakoztassa újra az Azure Files-t a csatlakoztatási parancs [dokumentációból](../storage-how-to-use-files-linux.md)való futtatásával. Ezután ellenőrizze újra, hogy az **/etc/fstab** bejegyzés rendelkezik-e a megfelelő beállításokkal.

### <a name="cause-2-throttling"></a>2. ok: Szabályozás

Lehetséges, hogy szabályozást tapasztal, és a kérelmeket egy várólistába küldi. Ezt az [Azure Storage-metrikák](../common/storage-metrics-in-azure-monitor.md)azure monitorban történő kihasználásával ellenőrizheti.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Győződjön meg arról, hogy az alkalmazás az [Azure Files méretezési célokon](storage-files-scale-targets.md#azure-files-scale-targets)belül van.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Időbélyegek elvesztek a fájlok másolása a Windows-ról a Linux

Linux /Unix platformokon a **cp -p** parancs sikertelen, ha a különböző felhasználók saját fájl1 és fájl 2.

### <a name="cause"></a>Ok

A COPYFILE **f** force jelzője a **cp -p -f** unix-on történő végrehajtásához vezet. Ez a parancs nem őrzi meg a nem ön tulajdonában lévő fájl időbélyegzőjét sem.

### <a name="workaround"></a>Áthidaló megoldás

A tárfiók felhasználója a fájlok másolásához:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nem&lt;lehet&gt;hozzáférni az elérési úthoz: Bemeneti/kimeneti hiba

Amikor az ls paranccsal próbál fájlokat listázni egy Azure-fájlmegosztásban, a parancs lefagy a fájlok listázásakor. A következő hibaüzenet jelenik meg:

**ls: nem&lt;érhető&gt;el" elérési út:': Bemeneti/kimeneti hiba**


### <a name="solution"></a>Megoldás
Frissítse a Linux kernelt a következő verziókra, amelyek javították ezt a problémát:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Minden 4,13-nál nagyobb vagy azzal egyenlő verzió

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nem hozható létre szimbolikus hivatkozás - ln: nem sikerült létrehozni a "t" szimbolikus hivatkozást: A művelet nem támogatott

### <a name="cause"></a>Ok
Alapértelmezés szerint az Azure-fájlmegosztások linuxos csatlakoztatása a CIFS használatával nem teszi lehetővé a szimbolikus hivatkozások (symlinks) támogatását. Az ilyen hibaüzenet ekként jelenik meg:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Megoldás
A Linux CIFS-ügyfél nem támogatja a Windows-stílusú szimbolikus hivatkozások létrehozását az SMB 2 vagy 3 protokollon keresztül. Jelenleg a Linux kliens támogatja a szimbolikus linkek egy másik stílusát, a [Minshall+French symlinks-et,](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) mind a műveletek létrehozásához, mind pedig követéséhez. Azok az ügyfelek, akiknek szimbolikus hivatkozásokra van szükségük, használhatják az "mfsymlinks" csatlakoztatási lehetőséget. Javasoljuk az "mfsymlinks"-et, mert ez a Mac formátuma is.

A symlinks használatához adja hozzá az alábbiakat a CIFS csatlakoztatási parancs végéhez:

```
,mfsymlinks
```

Tehát a parancs valahogy így néz ki:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Ezután létrehozhat szimlinkeket a [wikiben](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers)javasolt amint azt a wiki .

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Csatlakoztatási hiba(112): Az állomás nem érhető el" az újrakapcsolódási időtúllépés miatt

A „112”-es csatlakoztatási hiba akkor lép fel a Linux-ügyfélen, ha az ügyfél hosszú ideig tétlen volt. Hosszabb tétlenség esetén az ügyfél lekapcsolódik, és a kapcsolat időtúllépés miatt megszakad.  

### <a name="cause"></a>Ok

A kapcsolat a következő okok miatt lehet tétlen:

-   Hálózati kommunikációs hibák, amelyek az alapértelmezett „kötetlen” csatlakoztatási beállítás használatakor megakadályozhatják, hogy újra létrejöjjön a TCP-kapcsolat a kiszolgálóval.
-   A közelmúltban kiadott újracsatlakozási javítások, amelyek nem szerepelnek a régebbi kernelekben.

### <a name="solution"></a>Megoldás

Ez a Linux kernelben található újracsatlakozási hiba a következő változtatások részeként lett kijavítva:

- [Javítás: a rendszer az újracsatlakozáskor nem késlelteti az smb3-munkamenet újracsatlakozását jóval a szoftvercsatornához való újracsatlakozás utáni időpontra](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Echo szolgáltatás azonnali hívása a szoftvercsatornához való újracsatlakozás után](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Lehetséges memóriasérülés javítása az újracsatlakozás során](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: A mutex esetleges kettős zárolásának javítása az újracsatlakozás során (kernel v4.9-es és újabb verzióihoz)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Azonban előfordulhat, hogy ezen módosítások még nem mindegyike lett portolva az összes Linux-disztribúcióba. Ha egy népszerű Linux-disztribúciót használ, ellenőrizheti az [Azure-fájlok használata Linux-szal](storage-how-to-use-files-linux.md) című témakört, hogy megtekintse, hogy a disztribúció melyik verziója rendelkezik a szükséges kernelmódosításokkal.

### <a name="workaround"></a>Áthidaló megoldás

Áthidaló megoldásként alkalmazhat ciklikus felcsatolást. A ciklikus felcsatolás arra kényszeríti az ügyfelet, hogy várjon, amíg létrejön a kapcsolat, vagy amíg explicit módon megszakad. Ezzel elkerülheti a hálózati időtúllépésből fakadó hibákat. Ez az áthidaló megoldás azonban végtelen várakozást okozhat. Készüljön fel rá, hogy szükség szerint le kell állítania a kapcsolatokat.

Ha nem tud frissíteni a legújabb kernelverziókra, áthidaló megoldásként létrehozhat egy fájlt az Azure-fájlmegosztásban, amelybe legfeljebb 30 másodpercenként ír. Ennek mindenképpen írási műveletnek kell lennie, például a fájl létrehozási vagy módosítási dátumának átírásának. Ellenkező esetben gyorsítótárazott eredményeket kaphat, így előfordulhat, hogy a művelet nem vált ki újracsatlakozást.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: error -22 on ioctl to get interface list" when you mount a Azure file share using SMB 3.0

### <a name="cause"></a>Ok
Ez a hiba a naplód, mert az Azure Files [jelenleg nem támogatja az SMB többcsatornás](https://docs.microsoft.com/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Megoldás
Ez a hiba figyelmen kívül hagyható.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha továbbra is segítségre van szüksége, lépjen kapcsolatba az [ügyfélszolgálattal,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) hogy gyorsan megoldódjon a probléma.
