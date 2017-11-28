---
title: "Azure-fájlok kapcsolatos problémák elhárítása a Linux |} Microsoft Docs"
description: "Linux Azure fájlok problémák hibaelhárítása"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: genli
ms.openlocfilehash: b6eb37ef4e836bd33a77180ef5aa8589ca5139af
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Azure-fájlok kapcsolatos problémák elhárítása a Linux

A cikk ismerteti a gyakori problémák kapcsolódó fájlok a Microsoft Azure Linux-ügyfelek a csatlakozáskor. Is biztosít lehetséges okokért és megoldásokért ezeket a problémákat.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[engedély megtagadva] lemez kvóta túllépve" amikor próbálja megnyitni a fájlt

A Linux az alábbihoz hasonló hibaüzenetet kap:

**<filename>[a hozzáférés megtagadva] Lemez kvótája túllépve**

### <a name="cause"></a>Ok

Elérte az egyidejű megnyitott leíróinak számára engedélyezett a fájl felső határát.

### <a name="solution"></a>Megoldás

Adjon meg kevesebb egyidejű megnyitott leíróinak néhány leírók bezárásával, és próbálkozzon újra a művelettel. További információkért lásd: [Microsoft Azure Storage teljesítményére és méretezhetőségére ellenőrzőlista](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Lassú fájl másolása és Linux rendszerű Azure-fájlokból

-   Ha nem rendelkezik egy adott minimális i/o vonatkozó méretbeli követelményt, ajánlott, hogy 1 MB, az I/O méretéről az optimális teljesítmény érdekében.
-   Ha ismeri a végleges mérethez, amelyet az írási műveletek használatával kiterjesztése, és a szoftver nem kompatibilitási problémákat tapasztal, ha a fájl egy unwritten utóhívás nullákat tartalmaz, majd állítsa be a fájlméret, így minden egyes egy kibővítése írási helyett előre.
-   A jobb oldali copy metódust használja:
    -   Használjon [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) bármely két fájlmegosztások közötti átvitel céljából.
    -   Használjon [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) közötti fájlmegosztások a helyi számítógépen.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Error(112) csatlakoztatni: az állomás nem működik" újracsatlakozás időtúllépés miatt

A "112" csatlakozási hiba akkor lép fel a Linux-ügyfél, amikor az ügyfél hosszú ideig inaktív volt. Kiterjesztett üresjárati idő után az ügyfél kapcsolata megszakad, és a kapcsolat időtúllépés miatt.  

### <a name="cause"></a>Ok

A kapcsolat üresjáratban lehet, a következő okok miatt:

-   Hálózati kommunikációs hibák, amelyek megakadályozzák a TCP-kapcsolat a kiszolgálóval újbóli bekapcsolását. az alapértelmezett beállítás a "soft" csatlakoztatási használata esetén
-   Legutóbbi újracsatlakozás tartalmaz, amelyek nem szerepelnek a régebbi kernelek

### <a name="solution"></a>Megoldás

Ez lehet újracsatlakozni a Linux kernel most problémát részeként a következő módosításokat:

- [Javítás kapcsolódjon újra késleltetésének mellőzése smb3 munkamenet hosszú feldolgozása után szoftvercsatorna újracsatlakozás](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
-   [Hívó echo szoftvercsatorna újracsatlakozás után azonnal](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
-   [CIFS: Javítsa ki a lehetséges memóriasérülést újracsatlakozás során](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
-   [CIFS: Javítsa ki a lehetséges dupla zárolását mutex során újracsatlakozás (kernel v4.9 és újabb)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Azonban ezek a változások előfordulhat, hogy nem használatát. még a Linux-disztribúció. A következő népszerű Linux kernelek végzett javítás és egyéb újracsatlakozás javításai: 4.4.40 4.8.16 és 4.9.1. Ez a javítás kaphat ilyen ajánlott kernel verziójú frissítése.

### <a name="workaround"></a>Megkerülő megoldás

Ez a probléma megkerüléséhez kötött megadásával. Ez arra kényszeríti az ügyfél számára, várjon, amíg létrejön a kapcsolat, vagy explicit módon megszakad, és a hálózati időtúllépések miatt megelőzése érdekében használható. Ez a megoldás azonban határozatlan ideig vár okozhatja. Készüljön kapcsolatok szükség szerint állítsa le.

A legújabb kernel verziói nem frissíthetők, ha meg a probléma úgy kerülheti megőrzi a fájl található Azure fájlmegosztás 30 másodpercenként vagy kisebb írást. Írási művelet, például a létrehozott vagy módosított dátum újraírását fájlon kell lennie. Ellenkező esetben kaphat gyorsítótárazott eredményt, és a művelet nem válthat ki az újracsatlakozás.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Error(115) csatlakoztatni: most a folyamatban lévő művelet" Azure fájlok csatlakoztatásakor SMB 3.0 használatával

### <a name="cause"></a>Ok

Egyes Linux terjesztésekről még nem támogatják a titkosítási szolgáltatások az SMB 3.0 és a felhasználók előfordulhat, hogy "115" hibaüzenetet kap, ha megpróbálják csatlakoztatási Azure fájlok SMB 3.0 használata miatt a hiányzó szolgáltatás. Jelenleg csak akkor támogatott a teljes titkosítás SMB 3.0 használata Ubuntu 16.04 vagy újabb verzió esetén.

### <a name="solution"></a>Megoldás

Az SMB 3.0 Linux titkosítási szolgáltatás 4.11 kernel jelent meg. Ez a funkció lehetővé teszi, hogy az Azure fájlmegosztás regisztrációját a helyi vagy egy másik Azure-régiót csatlakoztatását. A közzététel időpontjában ez a funkció le lett Ubuntu 17.04 és Ubuntu 16.10 backported. Ha a Linux SMB-ügyfél nem támogatja a titkosítást, a csatlakoztatási Azure fájlok SMB 2.1 egy Azure Linux virtuális gép, amely ugyanabban az adatközpontban, a fájl tárolási fiók használatával.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>A Linux virtuális gép csatlakoztatott lassú teljesítmény az Azure fájlmegosztások

### <a name="cause"></a>Ok

Egy lehetséges oka a lassú teljesítmény le van tiltva, gyorsítótárazás.

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy gyorsítótárazás le van tiltva, keresse meg a **gyorsítótár =** bejegyzés. 

**Gyorsítótár = none** azt jelzi, hogy gyorsítótárazás le van tiltva.  Csatlakoztassa újra a megosztás, az alapértelmezett csatlakoztatási parancs használatával, vagy explicit módon adja hozzá a **gyorsítótár = szigorú** a csatlakoztatási parancs használatával győződjön meg arról, hogy alapértelmezett gyorsítótárazását, vagy "strict" gyorsítótárazási mód engedélyezve van.

Bizonyos esetekben a **serverino** csatlakoztatási beállítás hatására a **ls** parancsot kell futtatni minden könyvtárbejegyzés elleni stat. Ezt a viselkedést eredményezi teljesítménycsökkenés nagy könyvtár listázásakor van. Ellenőrizheti a csatlakoztatási lehetőségek a **/etc/fstab** bejegyzést:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Azt is ellenőrizheti, hogy a helyes beállításokat használják futtatásával a **sudo csatlakoztatási |} grep cifs** parancs és a kimenetet, például a következő egy példa a kimenetre ellenőrzése:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Ha a **gyorsítótár = szigorú** vagy **serverino** elem nem jelent-e, válassza le a lemezképet, és Azure fájlokat újra csatlakoztatni a csatlakoztatási parancs futtatásával a [dokumentáció](../storage-how-to-use-files-linux.md). Ezt követően újbóli ellenőrzéséhez, hogy a **/etc/fstab** bejegyzés rendelkezik a megfelelő beállításokat.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>A fájlok másolása a Windows Linux időbélyegeket elvesztek

Linux/Unix platformokon a **cp -p** parancs sikertelen lesz, ha a különböző felhasználók 1 és 2 fájlt a tulajdonosa.

### <a name="cause"></a>Ok

A force jelzést **f** a COPYFILE eredményezi végrehajtása **cp -p -f** UNIX. Ez a parancs is sikertelen lesz, a fájlt, amely nem Ön a tulajdonosa időbélyegzőjét megőrzése érdekében.

### <a name="workaround"></a>Megkerülő megoldás

A tárolási fiók felhasználójának használ a fájlok másolása:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Nem képes csatlakozni vagy egy Azure fájlmegosztás csatlakoztatása

### <a name="cause"></a>Ok

A probléma gyakori okai a következők:


- Egy nem kompatibilis Linux terjesztési ügyfelet kell használnia. Azt javasoljuk, hogy használja a következő Linux Terjesztésekről Azure-fájlmegosztáshoz való csatlakozáshoz:

    - Ubuntu Server 14.04 + 
    - RHEL 7 + 
    - CentOS 7 + 
    - Debian 8 
    - openSUSE 13.2 + 
    - SUSE Linux Enterprise Server 12

- CIFS-utils nincsenek telepítve az ügyfélen.
- A minimális SMB/CIFS 2.1-es verziója nem telepítette az ügyfelet.
- Az SMB 3.0-titkosítás nem támogatott az ügyfélen. SMB 3.0-titkosítás Ubuntu 16.4 és újabb verzió, SUSE 12.3 és újabb verziója érhető el. Más terjesztési kernel 4.11 és újabb verzió szükséges.
- TCP-porton keresztül 445-ös nem támogatott a tárfiók kapcsolódni próbál.
- Próbálja meg egy Azure virtuális gép az Azure fájlmegosztások csatlakozni próbált, és a virtuális gép nem található a tárfiók ugyanabban a régióban.

### <a name="solution"></a>Megoldás

A probléma megoldásához használja a [hibaelhárító eszközt hibák csatlakoztatni a Linux Azure fájlok](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Ez az eszköz segítségével környezetben futó ügyfél, amely Azure fájlok hozzáférési hiba miatt nyújt segítséget felhőcímtárakkal önálló javítsa ki, és a diagnosztikai nyomkövetési adatokat gyűjt a nem kompatibilis ügyfél-konfiguráció észleléséhez.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: nem érhető el "&lt;elérési&gt;": bemeneti/kimeneti hiba

Fájlok listázása a az Azure fájlmegosztások ls paranccsal megkísérlésekor a következő hibaüzenet jelenik meg:

**ls: nem érhető el "&lt;elérési&gt;": bemeneti/kimeneti hiba**


### <a name="solution"></a>Megoldás
A Linux kernel frissítsen a következő verzióit, amelyeken ez a hiba javítása:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Összes verzió, amely nagyobb vagy egyenlő 4.13.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a gyorsan megoldott probléma eléréséhez.
