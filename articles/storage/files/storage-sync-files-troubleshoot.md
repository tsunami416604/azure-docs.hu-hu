---
title: "Azure fájlszinkronizálás (előzetes verzió) hibaelhárítása |} Microsoft Docs"
description: "Az Azure fájlszinkronizálás kapcsolatos gyakori hibák elhárítása"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Hibaelhárítás az Azure fájlszinkronizálás (előzetes verzió)
Az Azure File Sync (előzetes verzió) lehetővé teszi a szervezet Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy átalakítja a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

Ez a cikk célja, és a Azure fájlszinkronizálás telepítés észlelt megoldását. Ennek hiányában ez az útmutató bemutatja, hogyan gyűjteni a fontos a rendszer a problémák mélyebb vizsgálatra segítése érdekében. Ha a választ a kérdésére itt nem látható, ne habozzon, kapcsolatba velünk a következő csatornákon (a növekvő sorrendben):

1. Ez a cikk megjegyzések szakaszában.
2. [Az Azure Storage-fórum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Az Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft Support: Hozzon létre egy új támogatási esetet, lépjen a "Súgó és támogatás" fülre az Azure portálon, és kattintson a "New támogatja a kérelem".

## <a name="agent-installation-and-server-registration"></a>Az ügynök telepítése és a kiszolgáló regisztrálása
<a id="agent-installation-failures"></a>**Ügynök telepítési hibáinak elhárítása**  
Ha az Azure fájl Sync-ügynök telepítése sikertelen, a következő parancsot a az ügynök telepítése közben naplózásának engedélyezése egy rendszergazda jogú parancssorból:

```
StorageSyncAgent.msi /l*v Installer.log
```

Miután a telepítés sikertelen lesz, tekintse át a hiba okának megállapításához installer.log. 

> [!Note]  
> Az ügynök telepítése sikertelen lesz, ha a Microsoft Update használatát választotta, és nem fut a Windows Update szolgáltatás.

<a id="server-registration-missing"></a>**Kiszolgáló nem szerepel a kiszolgálók regisztrálva az Azure-portálon**  
Ha a kiszolgáló nem szerepel a regisztrált kiszolgálókat egy tárolási szinkronizálási szolgáltatás, hajtsa végre az alábbi lépéseket:
1. Bejelentkezés a regisztrálni kívánt kiszolgálóra.
2. Nyissa meg a Fájlkezelőt, és keresse meg a tároló szinkronizálási Ügynöktelepítési könyvtár (alapértelmezett hely a következő `C:\Program Files\Azure\StorageSyncAgent`). 
3. ServerRegistration.exe futtassa, és kövesse a varázsló regisztrálni a kiszolgálót egy tároló szinkronizálási szolgáltatás.

<a id="server-already-registered"></a>**Kiszolgáló regisztrálása az Azure fájl Sync-ügynök telepítése után a következő üzenet jelenik meg: "a kiszolgáló már regisztrálva van"**  
![A "kiszolgáló már regisztrálva van" hiba miatt a kiszolgáló regisztrálása párbeszédpanel képernyőfelvételének üzenet](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ez az üzenet jelenik meg, ha a kiszolgáló korábban regisztrálva volt a tároló szinkronizálási szolgáltatás. A kiszolgáló, az aktuális tároló szinkronizálási szolgáltatással és regisztrálni egy új tároló szinkronizálási szolgáltatás regisztrációjának törléséhez kövesse a lépéseket [a kiszolgáló regisztrációját az Azure fájlszinkronizálás](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Ha a kiszolgáló nem szerepel a kiszolgálók regisztrálva a tároló szinkronizálási szolgáltatást, futtassa a következő PowerShell-parancsokat a kiszolgáló regisztrációjának törlése kívánt:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Ha a kiszolgáló egy fürt része, nincs egy nem kötelező `Reset-StorageSyncServer -CleanClusterRegistration` paraméter, amely a fürt regisztrációs is eltávolítja.

<a id="web-site-not-trusted"></a>**Miért kapok "nem megbízható web site" válaszok számos, a kiszolgáló regisztrálásakor?**  
Ez a hiba akkor fordul elő, mert a **Internet Explorer fokozott biztonsági** házirend engedélyezve van a kiszolgáló regisztrálása során. Megfelelően letiltásának módjáról további információ a **Internet Explorer fokozott biztonsági** házirend, lásd: [előkészítése Windows kiszolgálók Azure-fájl szinkronizálással](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) és [Azure fájl központi telepítése Szinkronizálási (előzetes verzió)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Szinkronizálási csoportok kezelése
<a id="cloud-endpoint-using-share"></a>**Felhő végpont létrehozásához a következő hiba miatt sikertelen: "A megadott Azure fájlmegosztás már használatban van egy másik CloudEndpoint"**  
Ez a hiba akkor fordul elő, ha az Azure fájlmegosztás már használja egy másik Felhővégpontnak. 

Ha ezt a hibát kap, és az Azure fájlmegosztás jelenleg nem használja a Felhővégpontnak, hajtsa végre a Azure fájlszinkronizálás metaadatokat az Azure fájlmegosztás törölje az alábbi lépésekkel:

> [!Warning]  
> Egy Azure fájlmegosztás, amely jelenleg a felhővégpontnak használja a metaadatokat az törlése sikertelen Azure fájl szinkronizálási műveletek miatt. 

1. Nyissa meg az Azure fájlmegosztás az Azure portálon.  
2. Az Azure fájlmegosztás kattintson jobb gombbal, majd válassza ki **metaadatok szerkesztése**
3. SyncService kattintson jobb gombbal, majd válassza ki **törlése**.

<a id="cloud-endpoint-authfailed"></a>**Felhő végpont létrehozásához hibaüzenettel meghiúsul: AuthorizationFailed**  
Ez a probléma akkor fordul elő, ha a felhasználói fiók nem rendelkezik elegendő jogosultsága a Felhővégpontnak létrehozásához. 

Felhő-végpont létrehozása, a felhasználói fiókot a következő Microsoft Authorization engedélyekkel kell rendelkeznie:  
* Olvassa el: A szerepkör-definíció beolvasása
* Írási: Létrehozása, vagy egyéni szerepkör-definíció frissítése
* Olvassa el: A szerepkör-hozzárendelést kapnak
* Írási: A szerepkör-hozzárendelés létrehozása

A következő beépített szerepkörök a megfelelő Microsoft Authorization engedélyekkel rendelkezik:  
* Tulajdonos
* Felhasználói hozzáférés rendszergazdája

Határozza meg, ha a felhasználói fiók szerepkör rendelkezik a megfelelő engedélyekkel, hajtsa végre a következő:  
* Kattintson a **erőforráscsoportok** az Azure-portálon
* Válassza ki az erőforráscsoportot, ahol a tárfiók, és kattintson a **hozzáférés-vezérlés (IAM)**
* Kattintson a **szerepkör** (például tulajdonos, közreműködő) a felhasználói fiókjához.
* Az a **erőforrás-szolgáltató** listáról válassza ki **Microsoft Authorization** 
* **Szerepkör-hozzárendelés** kell **olvasási** és **írási engedélyekkel**
* **Szerepkör-definíció** kell **olvasási** és **írási engedélyekkel**

<a id="cloud-endpoint-deleteinternalerror"></a>**Végpont törlése hibaüzenettel meghiúsul: MgmtInternalError**  
A probléma akkor fordulhat elő, ha az Azure File megosztás vagy tárolási fiókot törölték a Felhőbeli végpont törlése előtt. A probléma az egy későbbi kiadásban lesz kijavítva, és a Felhőbeli végpont törölhetők.

Érdekében, hogy a probléma lépett fel, törölje a felhő végpont Azure File megosztásra vagy a storage-fiók törlése előtt.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**A fájl létrehozott közvetlenül a saját Azure fájlmegosztás SMB protokollon keresztül vagy a portálon keresztül. Mennyi ideig, amíg a fájl szinkronizálva van a szinkronizálási csoportban lévő kiszolgálók?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**A szinkronizálás nem működik a kiszolgálón hibaelhárítása**  
Ha a szinkronizálási kiszolgálón nem működik, tegye az alábbiakat:
- Ellenőrizze, hogy az Azure-portálon a címtár-szinkronizáláshoz az Azure-fájlmegosztáshoz egy kiszolgáló végpont létezik-e:
    
    ![A képernyőfelvétel egy felhő- és a kiszolgáló végpont rendelkező szinkronizálási csoport az Azure-portálon](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Tekintse át a működési és diagnosztikai eseménynaplók az eseménynaplóban, alatt `Applications and Services\Microsoft\FileSync\Agent`.
- Ellenőrizze, hogy a kiszolgáló rendelkezik internetkapcsolattal.
- Ellenőrizze a Azure fájl szinkronizáló szolgáltatás által a szolgáltatások MMC beépülő modul megnyitása a kiszolgálón fut, és ellenőrizze a tároló szinkronizálási ügynök szolgáltatás (FileSyncSvc) fut-e.

<a id="replica-not-ready"></a>**Szinkronizálási hibaüzenettel meghiúsul: 0x80c8300f - a replika nem áll készen a szükséges művelet**  
Ez a hiba várható, ha a felhő-végpont létrehozása és egy Azure fájlmegosztást, amely adatokat tartalmaz. Miután címváltozásának felderítését az Azure-fájlmegosztáson (akár 24 óráig is eltarthat) befejeződött, szinkronizálási kell kezdődnie, megfelelően működik.

<a id="broken-sync-files"></a>**Nem sikerült szinkronizálni a fájlokat hibaelhárítása**  
Ha egyes fájlok szinkronizálása nem működnek, tegye az alábbiakat:
- Tekintse át a működési és diagnosztikai esemény alapján `Applications and Services\Microsoft\FileSync\Agent` az eseménynaplóban
- Ellenőrizze a fájl nincs megnyitott leíróinak vannak
    - Megjegyzés: A Azure fájlszinkronizálás rendszeres időközönként eltarthat a VSS-pillanatképeket megnyitott kezelőkkel rendelkező fájlok

## <a name="cloud-tiering"></a>Felhőbeli rétegzés 
<a id="files-fail-tiering"></a>**Fájlok, amelyek nem réteg hibaelhárítása**  
Ha nem sikerül az Azure Fileshoz réteg (oka) t, hajtsa végre az alábbi lépéseket:

- Ellenőrizze, hogy az (oka) t található Azure fájlmegosztás
    - Megjegyzés: A fájl kell szinkronizálva az Azure fájlmegosztások előtt is helyezhető
- Tekintse át a működési és diagnosztikai eseménynaplóit, alatt `Applications and Services\Microsoft\FileSync\Agent` az eseménynaplóban
- Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolat 
- Ellenőrizze az Azure fájlszinkronizálás szűrő illesztőprogramok (StorageSync.sys & StorageSyncGuard.sys) fut-e
    - Nyissa meg egy rendszergazda jogú parancssort, és futtassa `fltmc` , és ellenőrizze, hogy a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok találhatók.

<a id="files-fail-recall"></a>**Fájlok, amelyek nem hívhatók hibaelhárítása**  
Ha a fájlok nem hívhatók, hajtsa végre az alábbi lépéseket:
- Tekintse át a működési és diagnosztikai eseménynaplóit, alatt `Applications and Services\Microsoft\FileSync\Agent` az eseménynaplóban
- Ellenőrizze az (oka) t az Azure fájlmegosztás létezik-e
- Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolat 
- Ellenőrizze az Azure fájlszinkronizálás szűrő illesztőprogramok (StorageSync.sys & StorageSyncGuard.sys) fut-e
    - Nyissa meg egy rendszergazda jogú parancssort, és futtassa `fltmc` , és ellenőrizze, hogy a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok találhatók.

<a id="files-unexpectedly-recalled"></a>**Folyamatban egy kiszolgálón váratlanul visszaírt fájlok hibaelhárítása**  
Víruskereső, biztonsági mentés és más alkalmazások, olvassa el a nagyszámú fájlt, akkor nemkívánatos visszahívások kivéve, ha a kapcsolat nélküli attribútum tiszteletben, és hagyja ki ezeket a fájlokat tartalmának olvasása. Ezen termékek, amelyek támogatják ezt az offline fájlok kihagyása segít elkerülni a nemkívánatos visszahívások, műveletek, például víruskereső vizsgálat vagy a biztonsági mentési feladat végrehajtása során.

Vegye fel a kapcsolatot a szoftvergyártó, a megoldás konfigurálása vonatkozó kapcsolat nélküli fájlok olvasásának kihagyását.

További nemkívánatos visszahívások más helyzetekben, például a Fájlkezelőben fájlok tallózása a fordulhat elő. A mappa megnyitása a felhő rétegű a Fájlkezelőben a kiszolgálón lévő fájlok azt eredményezheti, nemkívánatos visszahívások inkább, ha víruskereső engedélyezve van a kiszolgálón.

## <a name="general-troubleshooting"></a>Általános hibaelhárítási
Ha hibát tapasztal Azure fájl szinkronizálási kiszolgálóra, indítsa el a következő lépések végrehajtásával:
- Diagnosztika és műveleti eseménynaplójában keresse meg az eseménynaplóban
    - Szinkronizálás, Rétegezését és visszaírási problémák a diagnosztikai és működési eseménynaplóit alatt vannak bejelentkezve.`Applications and Services\Microsoft\FileSync\Agent`
    - A diagnosztikai és működési eseménynaplóit alatt naplózása problémákat (például a konfigurációs beállítások) kiszolgáló kezelése`Applications and Services\Microsoft\FileSync\Management`
- Ellenőrizze a Azure fájl szinkronizálási szolgáltatás fut-e a kiszolgálón
    - Nyissa meg a szolgáltatások beépülő MMC-modulban, és ellenőrizze a tároló szinkronizálási ügynök szolgáltatás (FileSyncSvc) fut-e
- Ellenőrizze az Azure fájlszinkronizálás szűrő illesztőprogramok (StorageSync.sys & StorageSyncGuard.sys) fut-e
    - Nyisson meg egy rendszergazda jogú parancssort, fltmc futtatni, és ellenőrizze a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok szerepel

Ha a hiba nem szűnik meg a fenti lépések végrehajtása után, futtassa a AFSDiag eszközt az alábbi lépések elvégzésével:
1. Hozzon létre egy könyvtárat, a AFSDiag kimenetének (például c:\output) mentéséhez használandó.
2. Nyisson meg egy emelt szintű PowerShell ablakot, és futtassa a következő parancsokat (minden parancs után nyomja le meg):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Az Azure fájlszinkronizálás kernel mód nyomkövetési szint (kivéve, ha a megadott részletesebb nyomkövetések létrehozásához), adjon meg 1 és találati aránya írjon be.
4. Az Azure fájlszinkronizálás felhasználói mód nyomkövetési szint (kivéve, ha a megadott részletesebb nyomkövetések létrehozásához), adjon meg 1 és találati aránya írjon be.
5. Reprodukálja a hibát, majd nyomja meg a D végzett.
6. Naplófájlok és nyomkövetési fájlok tartalmazó .zip fájl szerepelni fog a megadott kimeneti könyvtár.

## <a name="see-also"></a>Lásd még:
- [Gyakori kérdések az Azure Files](storage-files-faq.md)
- [A Windows Azure fájlok problémák megoldásához](storage-troubleshoot-windows-file-connection-problems.md)
- [Azure-fájlok kapcsolatos problémák elhárítása a Linux](storage-troubleshoot-linux-file-connection-problems.md)
