---
title: A Linux Azure Files-problémák elhárítása |} A Microsoft Docs
description: A Linux Azure Files-problémák hibaelhárítása
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: ca7e89f4b7f976633d5f097b7e335fe9b7631985
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477743"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>A Linux Azure Files-problémák hibaelhárítása

Ez a cikk a Linux-ügyfelek csatlakozáskor az Azure Files kapcsolatos gyakori problémák sorolja fel. Is biztosít a lehetséges okokért és megoldásokért ezeket a problémákat. 

A hibaelhárítási lépéseket ebben a cikkben mellett használhatja [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) , győződjön meg arról, hogy a Linux-ügyfél rendelkezik-e a megfelelő előfeltételek. AzFileDiagnostics automatizálja az ebben a cikkben említett jelenségeket a legtöbb észlelését. Az optimális teljesítmény eléréséhez állítsa be a környezetet nyújt segítséget. Ezt az információt is megtalálhatja a [Azure-fájlmegosztási hibaelhárító](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). A hibaelhárító segíti a kapcsolódással, hozzárendelése és az Azure-fájlmegosztások csatlakoztatására problémákat ismerteti.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Error(13) csatlakoztatása: A hozzáférés megtagadva"Amikor egy Azure-fájlmegosztás csatlakoztatása

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: Nem titkosított kommunikációs csatornát

Biztonsági okokból az Azure-fájlmegosztások kapcsolatok le lesznek tiltva, ha a kommunikációs csatornát nincs titkosítva, és ha a csatlakozási kísérlet nem ugyanabban az adatközpontban az Azure-fájlmegosztások-ket. Titkosítatlan kapcsolat az adatközpontokon belül is blokkolhatók, ha a [biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás engedélyezve van a tárfiókon. Egy titkosított kommunikációs csatornát csak akkor, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosításra van megadva.

További tudnivalókért lásd: [egy az Azure file történő csatlakoztatásának előfeltételei Linux és a cifs-utils csomag megoszthatja](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>1 OK megoldás

1. Csatlakozás ügyfélről, amely támogatja az SMB-titkosítás vagy csatlakozás a virtuális gép ugyanabban az adatközpontban, az Azure storage-fiókot, amely az Azure-fájlmegosztás szolgál.
2. Ellenőrizze a [biztonságos átvitelre van szükség](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva a tárfiókban, ha az ügyfél nem támogatja az SMB-titkosítás.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2. ok: Virtuális hálózat vagy a tűzfal-szabályok engedélyezve vannak a storage-fiók 

Ha a virtuális hálózat (VNET) és -tűzfalszabályok konfigurálása a storage-fiók, hálózati forgalom megtagadja a hozzáférést, kivéve, ha az ügyfél IP-cím vagy a virtuális hálózati hozzáférés engedélyezett.

### <a name="solution-for-cause-2"></a>Megoldás ok 2

Ellenőrizze a virtuális hálózat és tűzfal-szabályok megfelelően van-e beállítva a tárfiókban. Tesztelése Amennyiben virtuális hálózat vagy a tűzfal-szabályok okozza a problémát, ideiglenesen módosíthatja a beállítás a tárfiók **engedélyezze a hozzáférést minden hálózatból elérhető**. További tudnivalókért lásd: [konfigurálása az Azure Storage-tűzfalak és virtuális hálózatok](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[engedély megtagadva] meghaladta lemezkvótát" amikor próbálja megnyitni a fájlt

A Linux az alábbihoz hasonló hibaüzenetet kapja:

**<filename> [engedély megtagadva] Meghaladta lemezkvótát**

### <a name="cause"></a>Ok

Egy fájl engedélyezett egyidejű megnyitott leíróinak felső korlátot elérte.

### <a name="solution"></a>Megoldás

Egyidejű megnyitott leírók számának csökkentése zárja be az egyes kezeli, és próbálkozzon újra a művelettel. További információkért lásd: [a Microsoft Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Lassú fájl másolása és a Linux Azure-fájlokból

- Ha nem rendelkezik egy adott minimális i/o vonatkozó méretbeli követelményt, ajánlott, hogy 1 MiB, az i/o-mérete az optimális teljesítmény érdekében.
- Ha tudja, éppen kiterjesztése az írási műveletek használatával egy fájl a végleges mérethez, és a szoftver nem kompatibilitási problémák jelentkezhetnek, ha a fájl egy íratlan tail nullák tartalmazza, majd állítsa be a fájl mérete előzetesen már nem kell minden írási egy kiterjesztésének írási.
- A jobb oldali másolási módszert használják:
    - Használat [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) bármely két fájlmegosztások közötti átvitel céljából.
    - Használat [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) egy helyszíni számítógépen található fájlmegosztások között.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Error(112) csatlakoztatása: Gazdagép miatt nem működik "újrakapcsolódási időtúllépés

Ha az ügyfél hosszú ideig inaktív volt a Linux-ügyfél egy "112" csatlakoztatási hiba lép fel. Egy kiterjesztett üresjárati idő után az ügyfél kapcsolata megszakad, és a kapcsolat időtúllépés miatt.  

### <a name="cause"></a>Ok

A kapcsolat üresjárati lehet a következő okok miatt:

-   Hálózati kommunikációs hibákkal szemben, amelyek megakadályozzák, hogy a kiszolgáló TCP-kapcsolatot újbóli bekapcsolását, ha az alapértelmezett beállítás a "soft" csatlakoztatási használják
-   Legutóbbi újracsatlakozás javítja, amelyek nem szerepelnek a régebbi kernelekkel

### <a name="solution"></a>Megoldás

A Linux kernel újracsatlakozás a probléma már rögzített részeként a következő módosításokat:

- [Javítás újracsatlakozás nem késlelteti az smb3-munkamenetben csatlakoztassa újra a szoftvercsatorna újracsatlakozás után](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Echo szolgáltatás hívása szoftvercsatorna újracsatlakozás után azonnal](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Javítsa ki a lehetséges memóriasérülést reconnect során](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Javítsa ki a lehetséges dupla zárolását mutex során reconnect (kernel v4.9 és újabb)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Azonban ezeket a módosításokat előfordulhat, hogy nem lehet már még a Linux-disztribúció. Ez a javítás és a más újracsatlakozás javítja a következő népszerű Linux-kernelek vannak is: 4.4.40 4.8.16 és 4.9.1. Ez a javítás kérheti le az alábbi ajánlott kernel-verziók valamelyikét.

### <a name="workaround"></a>Áthidaló megoldás

A probléma megkerüléséhez kötött megadása. Kötött kényszeríti a ügyfelet, várjon, amíg létrejön a kapcsolat, vagy explicit módon megszakad. Hálózati időtúllépések miatt hibák elkerülése érdekében használhatja azt. Ez a megoldás azonban befejezésére való határozatlan idejű vár okozhatja. Fel kell készülni kapcsolatok szükség szerint állítsa le.

Ha nem tudja frissíteni a legújabb kernel-verzióknál, használhatja a probléma megoldásához a fájl tartja, az Azure-fájlmegosztás vagy kevesebb mint 30 másodpercenként írt. Írási művelet, például a létrehozott vagy módosított dátum újraírását fájlon kell lennie. Ellenkező esetben a gyorsítótárazott eredményeket kaphat, és a művelet nem válthat ki az újracsatlakozás.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Error(115) csatlakoztatása: A művelet folyamatban van"Amikor csatlakoztatja az Azure Files SMB 3.0-ás

### <a name="cause"></a>Ok

Egyes Linux-disztribúciókon még nem támogatják a titkosítási szolgáltatások SMB 3.0-ban. Felhasználók előfordulhat, hogy "115" hibaüzenetet kap, ha megpróbálnak csatlakoztatása az Azure Files SMB 3.0 használata miatt a hiányzó szolgáltatás. Az SMB 3.0 a teljes titkosítás támogatott csak akkor, amikor Ubuntu 16.04 vagy újabb verzióját használja.

### <a name="solution"></a>Megoldás

A titkosítási szolgáltatás az SMB 3.0-s Linux rendszeren a 4.11 kernel jelent meg. Ez a funkció lehetővé teszi, hogy az Azure-fájlmegosztások a helyszínen vagy más Azure-régióban csatlakoztatását. Ez a funkció a közzététel időpontjában backported Ubuntu 17.04 és Ubuntu 16.10 volt. 

Ha a Linuxos SMB-ügyfél nem támogatja a titkosítást, csatlakoztatási Azure fájlok, az az Azure Linux VM, amely ugyanabban az adatközpontban, mivel a fájlmegosztás SMB 2.1 használatával. Ellenőrizze, hogy a [biztonságos átvitelre van szükség]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva, a tárfiókban. 

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

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Lassú teljesítmény az Azure-fájlmegosztás csatlakoztatása egy Linux rendszerű virtuális gépen

### <a name="cause"></a>Ok

Lassú teljesítmény az egyik lehetséges oka le van tiltva a gyorsítótárazás.

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy gyorsítótárazás le van tiltva, keresse meg a **gyorsítótár =** bejegyzés. 

**Gyorsítótár = none** azt jelzi, hogy gyorsítótárazás le van tiltva. Az alapértelmezett csatlakoztatási parancs használatával, vagy explicit módon hozzáadásával, csatlakoztassa újra a megosztást a **gyorsítótár = szigorú** a mount parancs használatával győződjön meg arról, hogy alapértelmezett gyorsítótárazási vagy a "szigorú" gyorsítótárazási mód engedélyezve van.

Bizonyos esetekben a **serverino** csatlakoztatási beállítás hatására a **ls** parancs futtatása stat minden könyvtárbejegyzés ellen. Ezt a viselkedést eredményezi teljesítménycsökkenés big Data típusú könyvtár listázásakor használ. A csatlakoztatási lehetőségeket ellenőrizheti a **/etc/fstab** bejegyzés:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Ellenőrizheti azt is, hogy a megfelelő beállításokat használ futtatásával a **sudo mount |} grep cifs** parancs, és a kimenetét ellenőrzése. A következő egy példa a kimenetre:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Ha a **gyorsítótár = szigorú** vagy **serverino** lehetőség jelen, válassza le és csatlakoztassa az Azure Files újra a csatlakoztatási parancsot futtatja a [dokumentáció](../storage-how-to-use-files-linux.md). Ezt követően újbóli ellenőrzése ennyi idő, amely a **/etc/fstab** bejegyzés rendelkezik a megfelelő beállításokat.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>A Windows a fájlok másolása Linux időbélyegeket elvesztek

A Linux-/ Unix-platformokon a **cp -p** parancs sikertelen lesz, ha a különböző felhasználók a saját 1 és 2 fájlt.

### <a name="cause"></a>Ok

A Force **f** a COPYFILE eredményez végrehajtása **cp -p -f** UNIX. Ez a parancs is sikertelen, a fájlt, amely nem Ön a tulajdonosa időbélyegzőjét megőrzése érdekében.

### <a name="workaround"></a>Áthidaló megoldás

A storage-fiók felhasználó használja a fájlok másolása:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Nem lehet kapcsolódni, illetve egy Azure-fájlmegosztás csatlakoztatása

### <a name="cause"></a>Ok

Ez a probléma gyakori okai a következők:


- Egy Linux-terjesztési nem kompatibilis ügyfél használ. Javasoljuk, hogy a következő Linux-disztribúció használatával kapcsolódhat az Azure-fájlmegosztások:

    |   | SMB 2.1 <br>(Csatlakoztatása a virtuális gépek ugyanazon Azure-régióban) | SMB 3.0 <br>(Csatlakoztatása a helyszíni és a régiók közötti) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 + |

- CIFS segédprogramok (cfs-utils) nincsenek telepítve az ügyfélre.
- A minimális SMB/CIFS, 2.1-es verzió, az ügyfél nincs telepítve.
- Az SMB 3.0 titkosítás nem támogatott az ügyfélen. Az SMB 3.0 titkosítás Ubuntu 16.4 vagy újabb, valamint SUSE 12.3 és az újabb verzió érhető el. Más disztribúciók kernel 4.11 és újabb verzió szükséges.
- TCP-porton keresztül 445-ös, ami nem támogatott egy storage-fiókot kíván csatlakozni.
- Próbál csatlakozni egy Azure-fájlmegosztás, az Azure virtuális, és a virtuális gép nem a storage-fiók ugyanabban a régióban.
- Ha a [biztonságos átvitelre van szükség]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás engedélyezve van a tárfiókon, az Azure Files lehetővé teszi a csak az SMB 3.0-s titkosítással használó kapcsolatok.

### <a name="solution"></a>Megoldás

A probléma megoldásához használja a [eszköz hibaelhárítása az Azure Filesban hibák linuxon](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Ezzel az eszközzel:

* Megismerheti, hogyan ellenőrzése a környezetben futtató ügyfélen.
* Észleli a nem kompatibilis ügyfél-konfiguráció hozzáférési hibát okoz az Azure Files számára.
* Helyi rögzítés részletes útmutatást biztosít.
* A diagnosztikai nyomkövetéseket gyűjt.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nem érhető el "&lt;elérési út&gt;": Bemeneti/kimeneti hiba

Fájlok listázása az Azure-fájlmegosztás meg az "ls" paranccsal, ha a parancs a fájlok listázása során lefagy. A következő hibaüzenet jelenik meg:

**ls: nem érhető el "&lt;elérési út&gt;": Bemeneti/kimeneti hiba**


### <a name="solution"></a>Megoldás
A Linux kernel váltson a következő verziók, amelyek a probléma megoldását:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Összes verzió, amely nagyobb vagy egyenlő 4.13.

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nem hozható létre szimbolikus hivatkozások – ln: nem sikerült létrehozni a szimbolikus hivatkozást sikerült ": A művelet nem támogatott

### <a name="cause"></a>Ok
Alapértelmezés szerint Azure-fájlmegosztások csatlakoztatására linuxon CIFS használatával nem teszi lehetővé a szimbolikus hivatkozások (symlinks) támogatása. Ehhez hasonló hibaüzenet jelenik meg:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Megoldás
A Linux CIFS-ügyfél létrehozása a Windows-stílusú szimbolikus hivatkozások keresztül az SMB 2 vagy 3 protokoll nem támogatja. Jelenleg a Linux-ügyfél támogatja-e egy másik stílusát nevű szimbolikus hivatkozások [Minshall + francia symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) is létrehozni, és hajtsa végre a műveleteket. Szimbolikus hivatkozások igénylő ügyfelek tekintetében is használja a "mfsymlinks" csatlakozási lehetőséget. Javasoljuk, hogy "mfsymlinks", mert még a formátum, amely a Mac számítógépek használja.

Symlinks használja, a CIFS mount parancs végéhez adja hozzá a következőket:

```
,mfsymlinks
```

Így a parancs a következőhöz hasonló lesz:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Ezután symlinks hozhat létre a gyakran a [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.
