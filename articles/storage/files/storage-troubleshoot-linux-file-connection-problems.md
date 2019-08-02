---
title: A Linux Azure Files problémáinak elhárítása | Microsoft Docs
description: A Linux-Azure Files kapcsolatos problémák elhárítása
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 5c501e6c2bc1a30273682352a68565ccc897ff50
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699209"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>A Linux Azure Files problémáinak elhárítása

Ez a cikk a Linux-ügyfelekről való csatlakozáskor Azure Files kapcsolatos gyakori problémákat sorolja fel. Emellett a problémák lehetséges okait és megoldásait is tartalmazza. 

A cikkben található hibaelhárítási lépések mellett a [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) használatával biztosíthatja, hogy a Linux-ügyfél megfelelő előfeltételekkel rendelkezik. A AzFileDiagnostics automatizálja a jelen cikkben említett legtöbb tünet észlelését. Segít az optimális teljesítmény elérésében a környezet beállításában. Ezeket az információkat a [Azure Files shares](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)-hibakeresőben is megtalálhatja. A hibakereső útmutatást nyújt a Azure Files-megosztások csatlakoztatásához, leképezéséhez és csatlakoztatásához szükséges problémák megoldásához.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nem lehet csatlakozni az Azure-fájlmegosztás eléréséhez vagy csatlakoztatásához

### <a name="cause"></a>Ok

A probléma gyakori okai a következők:

- Nem kompatibilis linuxos terjesztési ügyfelet használ. Javasoljuk, hogy az alábbi Linux-disztribúciók használatával kapcsolódjon egy Azure-fájlmegosztást:

|   | SMB 2.1 <br>(Ugyanazon az Azure-régióban lévő virtuális gépekre csatlakoztatások) | SMB 3.0 <br>(A helyszíni és a régiók közötti) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7+ | 7.5 + |
| CentOS | 7+ |  7.5 + |
| Debian | 8+ |   |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3 + |

- A CIFS-segédprogramok (CFS-utils) nincsenek telepítve az ügyfélen.
- Az SMB/CIFS minimális verziója (2,1) nincs telepítve az ügyfélen.
- Az SMB 3,0 titkosítás nem támogatott az ügyfélen. Az előző táblázat olyan Linux-disztribúciókat tartalmaz, amelyek támogatják a helyszíni és régiók közötti, titkosítással történő csatlakoztatást. Az egyéb disztribúciók esetében a 4.11-es vagy újabb kernelverzió szükséges.
- A 445-as TCP-porton keresztül próbál csatlakozni egy Storage-fiókhoz, amely nem támogatott.
- Egy Azure-beli virtuális gépről próbál csatlakozni egy Azure-fájlmegosztáshoz, és a virtuális gép nem ugyanabban a régióban található, mint a Storage-fiók.
- Ha a [biztonságos átvitel szükséges]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás engedélyezve van a Storage-fiókon, Azure Files csak a titkosítást használó SMB 3,0-et használó kapcsolatokat fogja engedélyezni.

### <a name="solution"></a>Megoldás

A probléma megoldásához használja a [hibaelhárítási eszközt a Azure Files csatlakoztatási hibákhoz Linux rendszeren](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Ez az eszköz:

* Segít érvényesíteni a környezetet futtató ügyfelet.
* Észleli a nem kompatibilis ügyfél-konfigurációt, amely Azure Fileshoz való hozzáférési hibát okozna.
* Előírásos útmutatást nyújt az önjavításhoz.
* Gyűjti a diagnosztikai nyomkövetéseket.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>„Csatlakoztatási hiba (13): Engedély megtagadva” egy Azure-fájlmegosztás csatlakoztatása során

### <a name="cause-1-unencrypted-communication-channel"></a>1\. ok: Titkosítatlan kommunikációs csatorna

Biztonsági okokból az Azure-fájlmegosztásokhoz való kapcsolódás le van tiltva, ha a kommunikációs csatorna nincsen titkosítva, vagy ha a csatlakozási kísérlet nem ugyanabból az adatközpontból történik, ahol az Azure-fájlmegosztások találhatók. Az ugyanazon adatközponton belüli titkosítatlan kapcsolatokat akkor is blokkolhatja a rendszer, ha a tárfiókban engedélyezve van a [Biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás. Csak akkor biztosítható titkosított kommunikációs csatorna, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosítást.

További információ: [Azure-fájlmegosztás Linux és a cifs-utils csomag használatával történő csatlakoztatásának előfeltételei](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Kapcsolódjon egy olyan ügyfélhez, amely támogatja az SMB-titkosítást, vagy kapcsolódjon egy olyan virtuális gépről, amely ugyanabban az adatközpontban található, mint az Azure-fájlmegosztás esetében használt Azure Storage-fiók.
2. Győződjön meg arról, hogy a [biztonságos átvitel szükséges](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva a Storage-fiókban, ha az ügyfél nem támogatja az SMB-titkosítást.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2\. ok: A virtuális hálózat vagy a tűzfalszabályok engedélyezve vannak a Storage-fiókon. 

Ha virtuális hálózati (VNET) és tűzfalszabályok vannak konfigurálva a tárfiókhoz, a hálózati forgalom számára blokkolva lesz a hozzáférés, kivéve, ha az ügyfél IP-címe vagy a virtuális hálózat hozzáférést kapott.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[engedély megtagadva] a lemezre vonatkozó kvóta túllépve", amikor megpróbál megnyitni egy fájlt

A Linux rendszerben a következőhöz hasonló hibaüzenet jelenik meg:

**\<Fájlnév > [engedély megtagadva] a lemezkvóta túllépve**

### <a name="cause"></a>Ok

Elérte a fájlokhoz engedélyezett egyidejű nyitott fogópontok felső határát.

A 2 000-as számú nyitott leíró egyetlen fájlon van. Ha a 2 000-es megnyitott kezelővel rendelkezik, hibaüzenet jelenik meg, amely szerint a kvóta elérte a kvótát.

### <a name="solution"></a>Megoldás

Csökkentse az egyidejű megnyitott fogópontok számát néhány leíró bezárásával, majd próbálja megismételni a műveletet.

Egy fájlmegosztás, könyvtár vagy fájl nyitott leíróinak megtekintéséhez használja a [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-parancsmagot.  

Egy fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak bezárásához használja a [AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell-parancsmagot.

> [!Note]  
> A Get-AzStorageFileHandle és a AzStorageFileHandle parancsmag az az PowerShell-modul 2,4-es vagy újabb verziójában szerepel. A legújabb az PowerShell modul telepítéséhez lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Lassú a másolás a Linux rendszerbe és Azure Filesba

- Ha nem rendelkezik meghatározott minimális I/O-mérettel, javasoljuk, hogy az optimális teljesítmény érdekében az 1 MiB-t használja az I/O-mérethez.
- Használja a megfelelő másolási módszert:
    - Használjon [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a két fájlmegosztás közötti átvitelhez.
    - A CP vagy a dd párhuzamosan történő használata a másolási sebesség növelését eredményezheti, a szálak száma a használati esettől és a számítási feladatoktól függ. A következő példák hat-ot használnak: 
    - CP-példa (a CP a fájlrendszer alapértelmezett méretét fogja használni az adathalmaz méretének megfelelően): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - dd-példa (ez a parancs explicit módon beállítja az adatrészlet méretét 1 MiB-re):`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Nyílt forráskódú, külső gyártótól származó eszközök, például:
        - [GNU Parallel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) – fájlokat rendez, és partícióba csomagolja őket.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) – a Fpart és a másolási eszköz használatával több példányt is elindíthat, hogy áttelepítse a src_dir-ről a dst_url-re.
        - [Több](https://github.com/pkolano/mutil) szálat összefűzött CP-és md5sum GNU-coreutils alapján.
- Ha előre állítja be a fájl méretét, ahelyett, hogy minden írási kiterjesztést ír, a segít a másolási sebesség javításában olyan helyzetekben, ahol ismert a fájl mérete. Ha az írások kiterjesztését el kell kerülni, megadhatja a célfájl méretét a `truncate - size <size><file>` paranccsal. Ezt követően a `dd if=<source> of=<target> bs=1M conv=notrunc`parancs a forrásfájl méretének ismételt frissítése nélkül másolja a forrásfájlt. Megadhatja például a célfájl méretét a másolni kívánt összes fájlhoz (tegyük fel, hogy a megosztás a/mnt/Share alatt van csatlakoztatva):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - Ezután másolja a fájlokat a párhuzamos írások kiterjesztése nélkül:`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>„Csatlakoztatási hiba (115): Folyamatban lévő művelet” üzenetet kap az Azure Files az SMB 3.0-val történő csatlakoztatása során

### <a name="cause"></a>Ok

Egyes Linux-disztribúciók még nem támogatják az SMB 3.0 titkosítási szolgáltatásait. Előfordulhat, hogy a felhasználók az Azure Files az SMB 3.0-val történő csatlakoztatásakor egy hiányzó szolgáltatás miatt egy „115”-ös hibaüzenetet kapnak. Az SMB 3.0 teljes körű titkosítását csak az Ubuntu 16.04-es vagy újabb verziói támogatják.

### <a name="solution"></a>Megoldás

A Linux rendszerhez készült SMB 3.0 titkosítási szolgáltatása a 4.11-es kernellel jelent meg. Ez a szolgáltatás lehetővé teszi egy helyszíni vagy egy más Azure régióban tárolt Azure-fájlmegosztás csatlakoztatását. A lehetőség minden Linux-disztribúcióban elérhető, amely szerepel a következőben: [Minimális ajánlott verzió az egyes csatlakoztatási képességekhez (SMB 2.1-es és SMB 3.0-s verzió)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30). Az egyéb disztribúciók esetében a 4.11-es vagy újabb kernelverzió szükséges.

Ha a Linuxos SMB-ügyfél nem támogatja a titkosítást, az Azure Files csatlakoztatásához használja az SMB 2.1-et egy olyan Azure-beli, Linux rendszerű virtuális gépen, amely ugyanabban az adatközpontban található, mint a fájlmegosztás. Győződjön meg arról, hogy [Biztonságos átvitelre van szükség]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva a tárfiókban. 

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

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Lassú teljesítmény egy Linux rendszerű virtuális gépen csatlakoztatott Azure-fájlmegosztás esetében

### <a name="cause-1-caching"></a>1\. ok: Gyorsítótárazás

A lassú teljesítmény egyik lehetséges oka, hogy a gyorsítótárazás le van tiltva. A gyorsítótárazás akkor lehet hasznos, ha többször is hozzáfér egy fájlhoz, ellenkező esetben a terhelés lehet. Ellenőrizze, hogy a gyorsítótárat használja-e a Letiltás előtt.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

Ellenőrizze, hogy a gyorsítótárazás le van-e tiltva, keresse meg a **cache =** bejegyzést.

A **cache = none** érték azt jelzi, hogy a gyorsítótárazás le van tiltva. Csatlakoztassa újra a megosztást az alapértelmezett csatlakoztatási paranccsal, vagy explicit módon adja hozzá a **cache = Strict** kapcsolót a csatlakoztatási parancshoz, és győződjön meg arról, hogy az alapértelmezett gyorsítótárazás vagy a "szigorú" gyorsítótárazási mód engedélyezve van.

Bizonyos esetekben az **serverino** csatlakoztatási lehetőség az **ls** parancs futtatását okozhatja minden címtár-bejegyzésnél. Ez a viselkedés a teljesítmény romlását eredményezi, ha nagyméretű könyvtárat listáz. A csatlakoztatási beállításokat az **/etc/fstab** -bejegyzésben tekintheti meg:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Azt is megtudhatja, hogy a megfelelő beállításokat használja-e a **sudo Mount | grep CIFS** parancs futtatásával és a kimenetének ellenőrzésével. A következő példában a kimenet látható:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Ha a **cache = Strict** vagy a **serverino** beállítás nincs jelen, válassza le és csatlakoztassa újra Azure Files a csatlakoztatás parancsnak a [dokumentációból](../storage-how-to-use-files-linux.md)való futtatásával. Ezután győződjön meg arról, hogy az **/etc/fstab** bejegyzés megfelelő beállításokkal rendelkezik.

### <a name="cause-2-throttling"></a>2\. ok: Szabályozás

Lehetséges, hogy sávszélesség-szabályozást tapasztal, és a kéréseket egy várólistába küldik. Ezt úgy ellenőrizheti, hogy kihasználja [Az Azure Storage-metrikákat Azure monitorban](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Győződjön meg arról, hogy az alkalmazás a [Azure Files méretezési célok](storage-files-scale-targets.md#azure-files-scale-targets)között van.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Az időbélyegek megszakadtak a Windowsról Linuxra való fájlok másolása közben

Linux/Unix platformokon a **CP-p** parancs végrehajtása meghiúsul, ha a különböző felhasználók az 1. és a 2. fájlt.

### <a name="cause"></a>Ok

A COPYFILE Force **jelzője** a következőt eredményezi: **CP-p-f** futtatása UNIX rendszeren. Ez a parancs nem tudja megőrizni a nem a saját fájl időbélyegzőjét.

### <a name="workaround"></a>Áthidaló megoldás

A fájlok másolásához használja a Storage-fiók felhasználóját:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: az "&lt;elérési út&gt;" nem érhető el: Bemeneti/kimeneti hiba

Ha egy Azure-fájlmegosztás fájljait az ls parancs használatával próbálja meg listázni, a parancs lefagy a fájlok listázásakor. A következő hibaüzenetet kapja:

**ls: az "&lt;elérési út&gt;" nem érhető el: Bemeneti/kimeneti hiba**


### <a name="solution"></a>Megoldás
Frissítse a Linux-kernelt a következő verziókra, amelyek a probléma javításával rendelkeznek:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Az összes olyan verzió, amely nagyobb vagy egyenlő, mint 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nem hozható létre szimbolikus hivatkozás – ln: nem sikerült létrehozni a szimbolikus hivatkozást: A művelet nem támogatott

### <a name="cause"></a>Ok
Alapértelmezés szerint az Azure-fájlmegosztás a CIFS használatával történő csatlakoztatása nem teszi lehetővé a szimbolikus hivatkozások (symlink) támogatását. A következőhöz hasonló hibaüzenet jelenik meg:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Megoldás
A Linux CIFS-ügyfél nem támogatja a Windows-stílusú szimbolikus hivatkozások létrehozását az SMB 2 vagy 3 protokollal. A Linux-ügyfél jelenleg a [Minshall + francia symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) néven is támogatja a szimbolikus hivatkozások egy másik stílusát mind a létrehozási, mind a követési műveletekhez. Azok az ügyfelek, akiknek szükségük van szimbolikus hivatkozásokra, használhatják az "mfsymlinks" csatlakoztatási lehetőséget. Javasoljuk, hogy a "mfsymlinks" formátumot használja, mert ez a Mac által használt formátum is.

A symlinkek használatához adja hozzá a következőt a CIFS mount parancs végéhez:

```
,mfsymlinks
```

Így a parancs valahogy így néz ki:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Ezután a wikiben javasolt módon hozhat létre symlinkeket [](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>„Csatlakoztatási hiba (112): A gazdagép nem működik, mert az újracsatlakoztatás időtúllépés miatt megszakadt

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
- [CIFS: A mutex az újracsatlakozás során esetlegesen jelentkező kettős zárolásának javítása (a kernel 4.9-es vagy újabb verziója)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Azonban előfordulhat, hogy ezen módosítások még nem mindegyike lett portolva az összes Linux-disztribúcióba. Az [Az Azure Files használata Linux rendszerrel](storage-how-to-use-files-linux.md) című cikk [Minimális ajánlott verzió az egyes csatlakozási képességekhez (SMB 2.1-es és SMB 3.0-s verzió)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30) szakasza ismerteti ezt a javítást és az egyéb, az újracsatlakozással kapcsolatos javításokat. A javítás az ajánlott kernelverziók egyikére való frissítéssel szerezhető be.

### <a name="workaround"></a>Áthidaló megoldás

Áthidaló megoldásként alkalmazhat ciklikus felcsatolást. A ciklikus felcsatolás arra kényszeríti az ügyfelet, hogy várjon, amíg létrejön a kapcsolat, vagy amíg explicit módon megszakad. Ezzel elkerülheti a hálózati időtúllépésből fakadó hibákat. Ez az áthidaló megoldás azonban végtelen várakozást okozhat. Készüljön fel rá, hogy szükség szerint le kell állítania a kapcsolatokat.

Ha nem tud frissíteni a legújabb kernelverziókra, áthidaló megoldásként létrehozhat egy fájlt az Azure-fájlmegosztásban, amelybe legfeljebb 30 másodpercenként ír. Ennek mindenképpen írási műveletnek kell lennie, például a fájl létrehozási vagy módosítási dátumának átírásának. Ellenkező esetben gyorsítótárazott eredményeket kaphat, így előfordulhat, hogy a művelet nem vált ki újracsatlakozást.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, forduljon az ügyfélszolgálathoz, és [kérje](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) meg a probléma gyors megoldását.
