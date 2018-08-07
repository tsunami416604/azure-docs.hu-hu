---
title: A Linux Azure Files-problémák elhárítása |} A Microsoft Docs
description: A Linux Azure Files-problémák hibaelhárítása
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: 0f99913ab252b94d475f920bd734e68ff5f3b3d3
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525120"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>A Linux Azure Files-problémák hibaelhárítása

Ez a cikk a Linux-ügyfelek csatlakozáskor a Microsoft Azure Files kapcsolatos gyakori problémák sorolja fel. Is biztosít a lehetséges okokért és megoldásokért ezeket a problémákat.

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
- Ha ismeri a végleges mérethez egy fájl, amely írási műveletek használatával kiterjesztése, és a szoftver nem kompatibilitási problémák jelentkezhetnek, ha a fájl egy íratlan tail nullák tartalmazza, majd állítsa be a fájl mérete előzetesen már nem kell minden írási egy kiterjesztésének írási.
- A jobb oldali másolási módszert használják:
    - Használat [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) bármely két fájlmegosztások közötti átvitel céljából.
    - Használat [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) egy helyszíni számítógépen található fájlmegosztások között.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Error(112) csatlakoztatási: állomás leállt" újrakapcsolódási időtúllépés miatt

Ha az ügyfél hosszú ideig inaktív volt a Linux-ügyfél egy "112" csatlakoztatási hiba lép fel. A kiterjesztett üresjárati idő után az ügyfél kapcsolata megszakad, és a kapcsolat időtúllépés miatt.  

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

Azonban ezeket a módosításokat előfordulhat, hogy nem lehet már még a Linux-disztribúció. A következő népszerű Linux kernelek végzett javítás és egyéb újracsatlakozás javításai: 4.4.40 4.8.16 és 4.9.1. Ez a javítás kérheti le az alábbi ajánlott kernel-verziók valamelyikét.

### <a name="workaround"></a>Áthidaló megoldás

A probléma megkerüléséhez kötött megadása. Ez kényszeríti, az ügyfél vár, amíg létrejön a kapcsolat, vagy explicit módon megszakad, és a hibák megelőzése érdekében hálózati időtúllépések miatt is használható. Ez a megoldás azonban befejezésére való határozatlan idejű vár okozhatja. Fel kell készülni kapcsolatok szükség szerint állítsa le.

Ha nem tudja frissíteni a legújabb kernel-verzióknál, használhatja a probléma megoldásához a fájl tartja, az Azure-fájlmegosztás vagy kevesebb mint 30 másodpercenként írt. Írási művelet, például a létrehozott vagy módosított dátum újraírását fájlon kell lennie. Ellenkező esetben a gyorsítótárazott eredményeket kaphat, és a művelet nem válthat ki az újracsatlakozás.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Error(115) csatlakoztatási: a művelet folyamatban van" Amikor csatlakoztatja az Azure Files SMB 3.0-ás

### <a name="cause"></a>Ok

Egyes Linux-disztribúciókon egyelőre nem támogatják a titkosítási szolgáltatások az SMB 3.0-s és a felhasználók előfordulhat, hogy "115" hibaüzenetet kap, ha az Azure Files csatlakoztatása az SMB 3.0 miatt egy hiányzó szolgáltatás használatával próbálják. Jelenleg csak támogatott SMB 3.0-s teljes titkosítással Ubuntu 16.04 vagy újabb használatakor.

### <a name="solution"></a>Megoldás

Linuxos SMB 3.0 titkosítás funkciót a 4.11 kernel jelent meg. Ez a funkció lehetővé teszi a helyszíni vagy más Azure-régióban az Azure-fájlmegosztás csatlakoztatása. Ez a funkció a közzététel időpontjában backported Ubuntu 17.04 és Ubuntu 16.10 volt. Ha a Linuxos SMB-ügyfél nem támogatja a titkosítást, a csatlakoztatási Azure fájlok, amely ugyanabban az adatközpontban, a File storage-fiók Azure Linux virtuális gép az SMB 2.1 használatával.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Lassú teljesítmény az Azure-fájlmegosztás csatlakoztatása egy Linux rendszerű virtuális gépen

### <a name="cause"></a>Ok

Lassú teljesítmény az egyik lehetséges oka le van tiltva a gyorsítótárazás.

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy gyorsítótárazás le van tiltva, keresse meg a **gyorsítótár =** bejegyzés. 

**Gyorsítótár = none** azt jelzi, hogy gyorsítótárazás le van tiltva.  Az alapértelmezett csatlakoztatási parancs használatával, vagy explicit módon hozzáadásával, csatlakoztassa újra a megosztást a **gyorsítótár = szigorú** a mount parancs használatával győződjön meg arról, hogy alapértelmezett gyorsítótárazási vagy a "szigorú" gyorsítótárazási mód engedélyezve van.

Bizonyos esetekben a **serverino** csatlakoztatási beállítás hatására a **ls** parancs futtatása stat minden könyvtárbejegyzés ellen. Ezt a viselkedést eredményezi teljesítménycsökkenés big Data típusú könyvtár listázásakor használ. A csatlakoztatási lehetőségeket ellenőrizheti a **/etc/fstab** bejegyzés:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Ellenőrizheti azt is, hogy a megfelelő beállításokat használ futtatásával a **sudo mount |} grep cifs** parancs, és a kimenetét, mint például az alábbi példa kimenetében ellenőrzése:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Ha a **gyorsítótár = szigorú** vagy **serverino** lehetőség jelen, válassza le és csatlakoztassa az Azure Files újra a csatlakoztatási parancsot futtatja a [dokumentáció](../storage-how-to-use-files-linux.md). Ezt követően újbóli ellenőrzése ennyi idő, amely a **/etc/fstab** bejegyzés rendelkezik a megfelelő beállításokat.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>A Windows a fájlok másolása Linux időbélyegeket elvesztek

A Linux-/ Unix-platformokon a **cp -p** parancs sikertelen lesz, ha 1 és 2 fájl másik felhasználó tulajdonában.

### <a name="cause"></a>Ok

A Force **f** a COPYFILE eredményez végrehajtása **cp -p -f** UNIX. Ez a parancs is sikertelen, a fájlt, amely nem Ön a tulajdonosa időbélyegzőjét megőrzése érdekében.

### <a name="workaround"></a>Áthidaló megoldás

A storage-fiók felhasználó használja a fájlok másolása:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Nem lehet csatlakozni, vagy egy Azure-fájlmegosztás csatlakoztatása

### <a name="cause"></a>Ok

A probléma gyakori okai a következők:


- Egy Linux-terjesztési nem kompatibilis ügyfél használ. Azt javasoljuk, hogy a következő Linux-Disztribúciók szeretne csatlakozni az Azure-fájlmegosztást használja:

* **Minimális ajánlott verzió a megfelelő csatlakozási képességeit (2.1 vagy SMB 3.0-s verziójú SMB-verzió)**    
    
    |   | SMB 2.1 <br>(Csatlakoztatása a virtuális gépek ugyanazon Azure-régióban) | SMB 3.0 <br>(Csatlakoztatása a helyszíni és a régiók közötti) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04-es + | 16.04 + |
    | RHEL | 7 + | 7.5 + |
    | CentOS | 7 + |  7.5 + |
    | Debian | 8 + |   |
    | openSUSE | 13.2 + | 42.3 + |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 + |

- CIFS-utils nincsenek telepítve az ügyfélre.
- A minimális SMB/CIFS 2.1-es verziója nincs telepítve az ügyfélre.
- SMB 3.0-titkosítás nem támogatott az ügyfélen. Az SMB 3.0 titkosítás Ubuntu 16.4 és újabb verzióra, a SUSE 12.3 és újabb verziója érhető el. Más disztribúciók kernel 4.11 és újabb verzió szükséges.
- Csatlakozás egy tárfiókhoz TCP-porton keresztül 445-ös, amely nem támogatott kívánt.
- Szeretne csatlakozni egy Azure virtuális gép Azure-fájlmegosztás próbálja ki, és a virtuális gép nem található, és a Storage-fiók ugyanabban a régióban.

### <a name="solution"></a>Megoldás

A probléma megoldásához használja a [hibaelhárítási eszköz csatlakoztatási hibákhoz való a linuxon futó Azure Files](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Az eszköz segítségével, hogy a környezetét az ügyfél ellenőrizze, hogy észlelje a nem kompatibilis ügyfél-konfigurációt, amely az Azure Files számára hozzáférési hibát okoz, előírásszerű útmutató lehetővé teszi helyi javítása és a diagnosztikai nyomkövetéseket gyűjt.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nem érhető el "&lt;elérési út&gt;": bemeneti/kimeneti hiba

Ha megpróbál egy Azure-fájlt a fájlok megosztása ls paranccsal listát, ls parancsot lefagy listaelem fájlok, a következő hibaüzenetet kapja:

**ls: nem érhető el "&lt;elérési út&gt;": bemeneti/kimeneti hiba**


### <a name="solution"></a>Megoldás
A Linux kernel váltson a következő verziók, amelyek a probléma javítása:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Összes verzió, amely nagyobb vagy egyenlő 4.13.

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Nem hozható létre szimbolikus hivatkozások – ln: nem sikerült létrehozni a szimbolikus hivatkozást sikerült ": a művelet nem támogatott

### <a name="cause"></a>Ok
Alapértelmezés szerint a linuxon futó Azure-fájlmegosztások csatlakoztatására használata CIFS nem engedélyezi symlinks támogatása. Összekapcsolás hiba jelenik meg:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Megoldás
A Linux CIFS-ügyfél nem támogatja a Windows típusú szimbolikus hivatkozások létrehozása SMB2/3 protokollon keresztül. Jelenleg a Linux-ügyfél támogatja a szimbolikus hivatkozások nevű másik stílusát [Mishall + francia symlinks] (https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) is létrehozni, és hajtsa végre a műveleteket. Szimbolikus hivatkozások igénylő ügyfelek "mfsymlinks" mount parancs segítségével. "mfsymlinks" használata általában akkor javasolt, mert is, amely a Mac számítógépek által használt formátum.

Tudják symlinks használni, a CIFS mount parancs végéhez adja hozzá a következőket:

```
,mfsymlinks
```

Így a parancs a következőhöz hasonlóan kell kinéznie:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsynlinks
```

Miután hozzáadta, akkor fogja tudni symlinks hozzon létre a gyakran a [Wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.
