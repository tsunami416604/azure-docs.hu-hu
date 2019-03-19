---
title: Az Azure File Sync hibaelhárítása |} A Microsoft Docs
description: Az Azure File Sync gyakori hibáinak elhárítása.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: eeda1ed3181b8cc8f641ed731b7f00fac2d3fad6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005826"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure-fájlok szinkronizálásának hibaelhárítása
Az Azure File Sync használatával fájlmegosztásainak a szervezet az Azure Files között, miközben gondoskodik a rugalmasságát, teljesítményét és kompatibilitását a helyszíni fájlkiszolgálók. Az Azure File Sync Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. Helyileg, az adatok eléréséhez a Windows Serveren elérhető bármely protokollt használhatja, beleértve az SMB, NFS és FTPS. Tetszőleges számú gyorsítótárak világszerte igény szerint is rendelkezhet.

Ez a cikk célja, hibakeresésre és az Azure File Sync üzembe helyezéssel előforduló problémák megoldására. Azt is ismertetjük, hogyan fontos naplók gyűjtését a rendszer, ha a probléma egy mélyebb vizsgálatra szükség. Ha nem látja a választ a kérdésére, lépjen kapcsolatba velünk (a eszkalálásáról rendelésben) a következő csatornákon keresztül:

1. [Az Azure Storage-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. A Microsoft ügyfélszolgálata. Hozhat létre egy új támogatási kérelmet az Azure Portalon, az a **súgó** lapon jelölje be a **súgó + támogatás** gombra, és válassza ki **új támogatási kérelem**.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Problémát tapasztalok egy probléma az Azure File Sync (szinkronizálása, felhőalapú rétegezési, stb.) a kiszolgálón. E távolítsa el, majd hozza létre újra a kiszolgálói végpontot?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Az ügynök telepítése és a kiszolgáló regisztrálása
<a id="agent-installation-failures"></a>**Az ügynök telepítési hibáinak elhárítása**  
Ha az Azure File Sync ügynök telepítése sikertelen, egy rendszergazda jogú parancssorba, futtassa a következő parancsot, kapcsolja be a naplózás ügynök telepítése közben:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Tekintse át a telepítési hiba okának megállapításához installer.log.

<a id="agent-installation-on-DC"></a>**Ügynök telepítése sikertelen lesz az Active Directory-tartományvezérlő**  
Ha a szinkronizálási ügynök telepítése egy Active Directory tartományvezérlővel, ahol a PDC-szerepkör tulajdonosa van-e egy Windows Server 2008 R2 verziónál régebbi verziójú operációs rendszer, ütközhet a probléma, ahol a sync-ügynök telepítése sikertelen lesz.

Háríthatja el vigye át az elsődleges szerepkört egy másik tartományvezérlő Windows Server 2012 R2 rendszerű vagy újabb, majd telepítse a szinkronizálást.

<a id="server-registration-missing"></a>**Kiszolgáló nem szerepel a regisztrált kiszolgálókat, az Azure Portalon**  
Ha a kiszolgáló nem szerepel a **regisztrált kiszolgálókat** a Társzinkronizálási szolgáltatás:
1. Jelentkezzen be a regisztrálni kívánt kiszolgálóra.
2. Nyissa meg a Fájlkezelőt, és keresse meg a Storage Sync-ügynök telepítési könyvtárát (az alapértelmezett hely a C:\Program Files\Azure\StorageSyncAgent). 
3. ServerRegistration.exe futnak, és fejezze be a varázslót a Storage Sync Service regisztrálni a kiszolgálót.

<a id="server-already-registered"></a>**Kiszolgáló regisztrálása Azure File Sync ügynök telepítése közben a következő üzenetet jeleníti meg: "A kiszolgáló már regisztrálva van"** 

![A "kiszolgáló már regisztrálva van" hiba miatt a kiszolgáló regisztrálása párbeszédpanel képernyőképe üzenet](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ez az üzenet jelenik meg, ha a kiszolgáló korábban regisztrálva lett az Társzinkronizálási szolgáltatás. Törölje a jelenlegi Storage Sync Service a kiszolgáló regisztrációját, és regisztrálhatja az új Társzinkronizálási szolgáltatás, az ismertetett lépések végrehajtásával [a kiszolgáló regisztrációját az Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Ha a kiszolgáló nem szerepel a **regisztrált kiszolgálókat** a Storage Sync Service, a kiszolgáló regisztrációjának törlése, kívánt futtassa a következő PowerShell-parancsokat:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Ha a kiszolgáló egy fürt tagja, akkor használhatja a választható *alaphelyzetbe állítása – StorageSyncServer - CleanClusterRegistration* paraméter is a fürt regisztrációjának eltávolítása.

<a id="web-site-not-trusted"></a>**Ha a kiszolgáló regisztrálásához számos "webhelyet nem megbízható" válaszok láthatók. Hogy miért?**  
A probléma akkor fordul elő, amikor a **az Internet Explorer fokozott biztonsági** házirend engedélyezve van a kiszolgáló regisztrálása során. Megfelelően letiltásával kapcsolatos további információ a **az Internet Explorer fokozott biztonsági** házirend, lásd: [készítse elő a Windows Server használata az Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) és [üzembe helyezése az Azure File Szinkronizálási](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Szinkronizálási csoportok kezelése
<a id="cloud-endpoint-using-share"></a>**A felhőbeli végpont létrehozása sikertelen, hiba: „A megadott Azure-fájlmegosztást már egy másik felhőbeli végpont használja”**  
Ez a probléma akkor fordul elő, ha az Azure-fájlmegosztás már használja egy másik felhőbeli végpont. 

Ha az üzenet és az Azure-fájlmegosztás jelenleg nem használja a felhőbeli végpont, a következő lépéseket az Azure File Sync metaadatokat az Azure-fájlmegosztás törlése:

> [!Warning]  
> A metaadatokat az Azure-fájlmegosztás, amelyek jelenleg használják a felhőbeli végpont törlése hatására az Azure File Sync műveleteket. 

1. Az Azure Portalon nyissa meg az Azure-fájlmegosztást.  
2. Kattintson a jobb gombbal az Azure-fájlmegosztást, és válassza **metaadatainak szerkesztése**.
3. Kattintson a jobb gombbal **SyncService**, majd válassza ki **törlése**.

<a id="cloud-endpoint-authfailed"></a>**A felhőbeli végpont létrehozása sikertelen, hiba: "AuthorizationFailed"**  
Ez a probléma akkor fordul elő, ha a felhasználó fiókja nem rendelkezik megfelelő jogosultsággal a felhőbeli végpont létrehozásához. 

Felhőbeli végpont létrehozása, a felhasználói fiókot a következő Microsoft Authorization engedélyekkel kell rendelkeznie:  
* Olvasás: Szerepkör-definíció lekérése
* Írás: Egyéni szerepkör-definíció létrehozása vagy módosítása
* Olvasás: Szerepkör-kijelölés lekérése
* Írás: Szerepkör-hozzárendelés létrehozása

A következő beépített szerepkör rendelkezik a szükséges Microsoft Authorization engedélyekkel:  
* Tulajdonos
* Felhasználói hozzáférés rendszergazdája

Az határozza meg, hogy a felhasználói fiók szerepkör rendelkezik-e a szükséges engedélyek:  
1. Az Azure Portalon válassza ki a **erőforráscsoportok**.
2. Az erőforráscsoport, ahol a storage-fiók megtalálható, majd válassza ki és **hozzáférés-vezérlés (IAM)**.
3. Válassza ki a **szerepkör-hozzárendelések** fülre.
4. Válassza ki a **szerepkör** (például a tulajdonos vagy közreműködő) a felhasználói fiókjához.
5. Az a **erőforrás-szolgáltató** listáról válassza ki **Microsoft Authorization**. 
    * **Szerepkör-hozzárendelés** kell **olvasási** és **írási** engedélyeket.
    * **Szerepkör-definíció** kell **olvasási** és **írási** engedélyeket.

<a id="server-endpoint-createjobfailed"></a>**A kiszolgálói végpont létrehozása sikertelen, hiba: "MgmtServerJobFailed" (hibakód:-2134375898)**  
Ez a probléma akkor fordul elő, ha a kiszolgálói végpont elérési útja a rendszerkötet és a felhőbeli rétegezés engedélyezve van. Felhőbeli rétegezés nem támogatott a rendszerköteten. Kiszolgálói végpont létrehozása a rendszerköteten, tiltsa le a felhőbeli rétegezés a kiszolgálói végpont létrehozása során.

<a id="server-endpoint-deletejobexpired"></a>**A kiszolgálói végpont törlése sikertelen, hiba: "MgmtServerJobExpired"**                
Ez a probléma akkor fordul elő, ha a kiszolgáló offline állapotban, vagy nincs hálózati kapcsolat. A kiszolgáló már nem érhető el, ha a kiszolgáló, a portálon, amely törli a kiszolgálói végpontot regisztrációját. Törölje a kiszolgálói végpontot, kövesse az ismertetett lépéseket [a kiszolgáló regisztrációját az Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nem lehet kiszolgálói végpont tulajdonságai lap megnyitásához, vagy a felhőbeli rétegzési szabályzat frissítése**  
A probléma akkor fordulhat elő, ha meghiúsul egy felügyeleti műveletet a kiszolgálói végpontot. Ha a kiszolgálói végpont tulajdonságai lap nem nyitható meg az Azure portal, PowerShell-parancsokkal a kiszolgálóról, a kiszolgálói végpont frissítése lehet, hogy a probléma megoldására. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint `
    -SubscriptionId mysubguid `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint `
    -Id serverendpointid `
    -CloudTiering true `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Kiszolgálói végpont rendelkezik egy "Nincs tevékenység" vagy "Függő" állapotát, és a regisztrált kiszolgálók panelen a kiszolgáló állapota "Offline jelenik meg"**  

A probléma akkor fordulhat elő, ha a tárterület-figyelő szinkronizálási folyamat nem fut, vagy a kiszolgáló nem tud kommunikálni az Azure File Sync szolgáltatás egy proxy vagy tűzfal miatt.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Nyissa meg a Feladatkezelőt a kiszolgálón, és ellenőrizze, hogy fut-e a Storage Sync Monitor-folyamat (AzureStorageSyncMonitor.exe). Ha a folyamat nem fut, először próbálja meg újraindítani a kiszolgálót. Ha a kiszolgáló újraindítása nem oldja meg a probléma, frissítsen a legújabb Azure File Sync [ügynökverzió](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes).
2. Ellenőrizze a tűzfalakról és Proxykról beállításai megfelelően vannak konfigurálva:
    - Ha a kiszolgáló tűzfal mögött van, ellenőrizze, hogy engedélyezve van-e a 443-as port kimenő forgalma. Ha a tűzfal adott tartományokra korlátozza a forgalmat, erősítse meg a tartományokat, a tűzfal szereplő [dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) érhetők el.
    - Ha a kiszolgáló proxy mögött található, a gépre kiterjedő vagy alkalmazásspecifikus Proxybeállítások konfigurálása a proxy szakasz lépéseit követve [dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy).

<a id="endpoint-noactivity-sync"></a>**Kiszolgálói végpont rendelkezik egy "Nincs tevékenység" állapotát, és a regisztrált kiszolgálók panelen a kiszolgáló állapota "Online"**  

Egy kiszolgálói végpont állapotának "Nincs tevékenység" azt jelenti, hogy a kiszolgálói végpont nem naplózott szinkronizálási tevékenység az elmúlt két órában.

A kiszolgálóvégpontok nem lehetséges, hogy jelentkezzen szinkronizálási tevékenység a következő okok miatt:

- Ügynök verziója 4.3.0.0 vagy régebbi van telepítve, és a kiszolgáló rendelkezik egy aktív VSS szinkronizálási munkamenet (SnapshotSync). Aktív a kiszolgálói végpont VSS szinkronizálási munkamenet esetén más kiszolgálói végpontot, ugyanazon a köteten a kezdő szinkronizálási munkamenet a VSS-szinkronizálási munkamenet befejezéséig nem lehet elindítani. A probléma megoldásához telepítse a ügynökverzió 5.0.2.0 vagy újabb verzió szinkronizálása egy köteten, amikor a VSS szinkronizálása munkamenet több kiszolgálói végpontot támogató aktív.

    A kiszolgáló jelenlegi szinkronizálási tevékenység megtekintéséhez [hogyan szinkronizálási munkamenet jelenlegi állapotának figyelése?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- A kiszolgáló elérte az egyidejű szinkronizálási munkamenetek maximális számát. 
    - Ügynök verziója 4.x-es és újabb: Korlát rendelkezésre álló erőforrásoktól függ.
    - Ügynök verziója 3.x: 2 active szinkronizálási munkamenetek processzor vagy server 8 active sync-munkamenetek maximális száma.

> [!Note]  
> Ha a kiszolgáló állapota, a regisztrált kiszolgálók panelen "Jelenik meg a kapcsolat nélküli", hajtsa végre a leírt lépéseket a [kiszolgálói végpont rendelkezik egy "Nincs tevékenység" vagy "Függő" állapotát, és a regisztrált kiszolgálók panelen a kiszolgáló állapota "Offline jelenik meg" ](#server-endpoint-noactivity) szakaszban.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Ha Létrehoztam egy fájlt közvetlenül a saját Azure-fájlmegosztást az SMB-n keresztül, vagy a portálon keresztül, mennyi ideig tart a fájl szinkronizálása a szinkronizálási csoport kiszolgálóira?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Kiszolgáló végpont állapota több órán keresztül függőben van.**  
A probléma várható, ha egy felhőbeli végpont létrehozása és használata az Azure-fájlmegosztás, amely adatokat tartalmaz. A módosítása, a módosítások az Azure-fájlmegosztás enumerálása feladatot kell végeznie, mielőtt fájlokat is szinkronizálni a felhő- és végpontok közötti. A feladat elvégzéséhez szükséges idő szolgáltatás a névteret az Azure-fájlmegosztás a méretétől függ. A kiszolgálói végpont állapotának a módosítás enumerálás feladat befejeződése után frissítenie kell.

### <a id="broken-sync"></a>Hogyan figyelhetem a szinkronizálás állapotát?
# <a name="portaltabportal1"></a>[Portál](#tab/portal1)
Minden egyes szinkronizálási csoportban részletezhető le az egyes kiszolgálói végpontot, az utolsó befejezett szinkronizálási munkamenetek állapotának megjelenítéséhez. Zöld állapot oszlop, és nem szinkronizálja a fájlokat a 0 érték azt jelzik, hogy a várt módon működik-e szinkronizálása. Ha nem ez a helyzet, lentebb leggyakoribb szinkronizálási hibák, és hogyan kezelje a fájlokat, a rendszer nem szinkronizálja. 

![Az Azure portal képernyőképe](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Kiszolgáló](#tab/server)
Nyissa meg a kiszolgáló telemetrianaplók, találja az eseménynaplóban, megjelenítő `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. Esemény 9102 felel meg egy befejezett szinkronizálási munkamenet; a szinkronizálási legújabb állapot keresse meg a legutóbbi esemény azonosítója 9102. SyncDirection arra kéri, ha a munkamenet volt egy fel- vagy letöltést. Ha a HResult 0, a szinkronizálási munkamenet sikeresen befejeződött. Egy nem nulla értékű HResult azt jelenti, hogy hiba történt; szinkronizálás során Lásd az alábbi gyakori hibák listáját. Ha a PerItemErrorCount 0-nál nagyobb, akkor ez azt jelenti, hogy egyes fájlok vagy mappák nem szinkronizált megfelelően. Lehetséges, hogy egy HResult 0, de egy PerItemErrorCount 0-nál nagyobb legyen.

Az alábbi, a sikeres feltöltése egy példát. Az áttekinthetőség minden egyes 9102 eseményben szereplő értékek csak néhányat az alábbiakban láthatók. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Ezzel szemben egy sikertelen feltöltés ehhez hasonló lehet:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Néha szinkronizálási munkamenetek teljes sikertelen, vagy egy nem nulla értékű PerItemErrorCount rendelkezik, de továbbra is meg a folyamat előrehaladását, az egyes fájlok szinkronizálása sikeresen megtörtént. Ez a alkalmazott * mezőket (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes), amely jelzi, hogy mekkora a munkamenet a következő látható. Ha a sor több olyan szinkronizálási munkamenetek, amelyek nem működik, de egy növekvő alkalmazott * szerint, majd adjon szinkronizálási idő, próbálja meg újra a támogatási jegy megnyitása előtt.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hogyan figyelhetem a szinkronizálási munkamenet jelenlegi állapotát?
# <a name="portaltabportal1"></a>[Portál](#tab/portal1)
A szinkronizálási csoport nyissa meg a szóban forgó kiszolgálói végpontot, és tekintse meg a szinkronizálási tevékenység szakaszt a fájlok fel- vagy letölthető a jelenlegi szinkronizálási munkamenet száma. Vegye figyelembe, hogy késni fog ez az állapot szerint körülbelül 5 percet, és ha kellően kicsire ezen az időn belül befejezni a szinkronizálási munkamenet, nem jelenthető-e a portálon. 

# <a name="servertabserver"></a>[Kiszolgáló](#tab/server)
Keresse meg a legutóbbi 9302 telemetriát az Eseménynapló a kiszolgálón (a eseménynaplót, nyissa meg az alkalmazások és szolgáltatások Logs\Microsoft\FileSync\Agent\Telemetry). Az esemény azt jelzi, hogy a szinkronizálási munkamenet jelenlegi állapotát. TotalItemCount azt jelzi, hogy hány fájl van szinkronizálva, hogy AppliedItemCount eddig szinkronizált fájlok és PerItemErrorCount, amelyek nem (lásd lent ez kezelése) szinkronizálási fájlok száma.

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Honnan tudhatom meg, ha a kiszolgálók szinkronban egymással?
# <a name="portaltabportal1"></a>[Portál](#tab/portal1)
Egy adott szinkronizálási csoport minden egyes kiszolgáló esetén ellenőrizze, hogy:
- A feltöltési és letöltési utolsó kísérlet történt szinkronizálás időbélyegei legutóbbi.
- Állapota zöld, a fel- és letöltést.
- A szinkronizálási tevékenység mutatja nagyon kevés vagy a hátralévő szinkronizálási fájlokat.
- A fájlok nem szinkronizálja a mező értéke 0, a fel- és letöltést.

# <a name="servertabserver"></a>[Kiszolgáló](#tab/server)
Tekintse meg a befejezett szinkronizálási munkamenetek, amelyek 9102 esemény, a telemetriai adatok eseménynaplójában kiszolgálónként megjelölve (az eseménynaplót, lépjen a `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Bármelyik megadott kiszolgálón szeretné ellenőrizze, hogy a legújabb feltöltés, és töltse le a munkamenet sikeresen befejeződött. Ehhez ellenőrizze, hogy a HResult és PerItemErrorCount fel- és letöltést a 0 (a SyncDirection mezőben azt jelzi, hogy egy adott munkamenet-fel- vagy letöltést munkamenet). Fontos megjegyezni, hogy ha nem látja a nemrégiben befejezett szinkronizálási munkamenet, valószínűleg egy szinkronizálási munkamenet várható, ha csak hozzáadni vagy módosítani egy nagy mennyiségű adatot folyamatban van.
2. Amikor egy kiszolgáló teljesen naprakészek legyenek a felhőben, és rendelkezik a szinkronizáláshoz mindkét irány módosítása nélkül, látni fogja a üres szinkronizálási munkamenetek. Ezek a feltöltési jelzi, és mely minden a szinkronizálási * (SyncFileCount, SyncDirCount, SyncTombstoneCount és SyncSizeBytes) mezők a következők nulla, ami azt jelenti, nincs semmi szinkronizálása nem események letöltése. Vegye figyelembe, hogy ezek üres szinkronizálási munkamenetek nem tapasztalható magas adatváltozású kiszolgálókon nem mindig valami újat a szinkronizálás. Ha nincs szinkronizálási tevékenység kell bekövetkezésük 30 percenként. 
3. Ha minden kiszolgáló a felhőben, ami azt jelenti, a legutóbbi feltöltés naprakészek legyenek, és letöltési munkamenet üres szinkronizálási munkamenetek, hogy szinkronizálva-e a rendszer egészének ésszerű bizonyossággal mondhatjuk. 
    
Vegye figyelembe, hogy közvetlenül az Azure-fájlmegosztás a módosításokat, ha az Azure File Sync nem fogja észlelni módosítása enumerálás fut le, amíg a módosítás 24 óránként történik, amely. Akkor lehet, hogy egy kiszolgáló tudatja Önnel, akkor naprakészen a felhővel, ha valóban hiányoznak a közvetlenül az Azure-fájlmegosztás a legutóbbi módosításait. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hogyan ellenőrizhetem, hogy vannak-e meghatározott fájlokat vagy mappákat, amelyek nem szinkronizálást?
A portálon a PerItemErrorCount a kiszolgálón, vagy nem szinkronizálja a fájlok száma nagyobb-e, mint 0 bármely adott szinkronizálási munkamenet, amely azt jelenti, hogy az egyes elemek szinkronizálása sikertelen. Fájlok és mappák rendelkezhet, amely megakadályozza, hogy azok szinkronizálása jellemzőit. Ezek a jellemzők is állandó és folytatni a szinkronizálást, például eltávolítja a fájl vagy mappa neve nem támogatott karaktereket explicit műveletet igényel. Lehet is átmeneti, tehát a fájl vagy mappa automatikusan folytatódik a szinkronizálási; Ha például megnyitott kezelőkkel rendelkező fájlok automatikusan folytatódik szinkronizálása a fájl bezárásakor. Az Azure File Sync-motor az ilyen hibát észlel, amikor hibanaplót jön létre, hogy elemezhető legyen jelenleg nem szinkronizálja megfelelően az elemek listázásához.

Ezek a hibák megtekintéséhez futtassa a **FileSyncErrorsReport.ps1** (az Azure File Sync ügynök az ügynök telepítési könyvtárában található) PowerShell-parancsprogram, amely nem sikerült szinkronizálni a megnyitott leíróinak miatt fájlok azonosítása nem támogatott karaktereket vagy egyéb problémákkal. A ItemPath mező arra utasítja a gyökérkönyvtár szinkronizálási viszonyítva a fájl helyét. Tekintse meg a gyakori hibát sorol fel szinkronizálás alatt a javítási lépések végrehajtásához.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Egy fájl vagy címtár-szinkronizálási hibák elhárítása
**ItemResults log - elem szinkronizálási hibák**  

| HRESULT | HRESULT (decimális) | Hibasztring | Probléma | Szervizelés |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Egy fájl vagy címtár módosítása nem szinkronizálható még, mert egy függő mappa szinkronizálása még nem történt. Ez az elem után a rendszer szinkronizálja a függő módosításokat szinkronizálja. | Nincs szükség felhasználói műveletre. |
| 0x7B | 123 | ERROR_INVALID_NAME | A fájl vagy könyvtár neve érvénytelen. | Nevezze át a fájl vagy könyvtár az adott. Lásd: [kezelése nem támogatott karaktereket](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) további információt. |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | A fájl vagy könyvtár neve érvénytelen. | Nevezze át a fájl vagy könyvtár az adott. Lásd: [kezelése nem támogatott karaktereket](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) további információt. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | A fájl nem szinkronizálható, mert az használatban van. Ha már nincs használatban a fájl lesznek szinkronizálva. | Nincs szükség felhasználói műveletre. Az Azure File Sync naponta egyszer létrehoz egy ideiglenes VSS-pillanatkép megnyitott kezelőkkel rendelkező fájlok szinkronizálása a kiszolgálón. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Egy fájl módosult, de a módosítás nem még észlelt szinkronizálás által. Szinkronizálás után ez a változás észlelésekor állítja helyre. | Nincs szükség felhasználói műveletre. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | A fájl nem szinkronizálható, mert az Azure-beli fájlmegosztás korlátot. | A probléma megoldásához tekintse meg [a megosztás Azure fájltárolási korlátot elérte](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) a hibaelhárítási útmutató szakaszát. |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | Ez a hiba akkor fordulhat elő, ha a fájl titkosítva van egy nem támogatott megoldás (például NTFS EFS) által, vagy a fájl rendelkezik törlési a függő állapotú. | A fájl titkosítva van egy nem támogatott megoldás, ha a fájl visszafejtése, és a támogatott titkosítási megoldással. Támogatási megoldások listáját lásd: [titkosítási megoldások](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) az útmutató a tervezési szakaszban. Ha a fájl a függő állapotú törlés, a fájl törlődik, az összes megnyitott fájlleírók bezárásakor. |
| 0x20 | 32 | ÚJRA | A fájl nem szinkronizálható, mert az használatban van. Ha már nincs használatban a fájl lesznek szinkronizálva. | Nincs szükség felhasználói műveletre. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Egy fájl módosult a szinkronizálás közben, ezért a fájlt újra kell szinkronizálni. | Nincs szükség felhasználói műveletre. |

#### <a name="handling-unsupported-characters"></a>Kezelése nem támogatott karaktereket
Ha a **FileSyncErrorsReport.ps1** PowerShell-parancsfájl bemutatja a hiba oka nem támogatott karaktereket (hibakódjai 0x7b és 0x8007007b), érdemes távolítsa el vagy nevezze át a megfelelő fájlnevek a hibás karaktereket. PowerShell valószínűleg nyomtatja ki ezeket a karaktereket a kérdőjelek vagy üres téglalapok, mivel ezek a karakterek a legtöbb nem szabványos kódolással. A [Megoldásértékelési eszköz](storage-sync-files-planning.md#evaluation-tool) segítségével azonosíthatja a nem támogatott karaktereket.

Az alábbi táblázat tartalmazza az összes Azure File Sync jelenleg nem támogatja a unicode-karaktereket.

| Karakterkészlet | Karakterszám |
|---------------|-----------------|
| <ul><li>0x0000009D (osc operációs rendszer parancs)</li><li>0x00000090 (dcs eszköz vezérlő karakterlánc)</li><li>0x0000008F (ss3 egyetlen shift három)</li><li>0x00000081 (magas oktett készlet)</li><li>0x0000007F (del törlése)</li><li>0x0000008D (fenntartott példány fordított soremelés)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (Arab bemutató forms-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (speciális) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (noncharacter)</li><li>0x0002FFFE - 0x0002FFFF = 2 (noncharacter)</li><li>0x0003FFFE - 0x0003FFFF = 2 (noncharacter)</li><li>0x0004FFFE - 0x0004FFFF = 2 (noncharacter)</li><li>0x0005FFFE - 0x0005FFFF = 2 (noncharacter)</li><li>0x0006FFFE - 0x0006FFFF = 2 (noncharacter)</li><li>0x0007FFFE - 0x0007FFFF = 2 (noncharacter)</li><li>0x0008FFFE - 0x0008FFFF = 2 (noncharacter)</li><li>0x0009FFFE - 0x0009FFFF = 2 (noncharacter)</li><li>0x000AFFFE - 0x000AFFFF = 2 (noncharacter)</li><li>0x000BFFFE - 0x000BFFFF = 2 (noncharacter)</li><li>0x000CFFFE - 0x000CFFFF = 2 (noncharacter)</li><li>0x000DFFFE - 0x000DFFFF = 2 (noncharacter)</li><li>0x000EFFFE - 0x000EFFFF = 2 (nem meghatározott)</li><li>0x000FFFFE - 0x000FFFFF = 2 (kiegészítő magánjellegű használatra terület)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Általános szinkronizálási hiba
<a id="-2147023673"></a>**A szinkronizálási munkamenet meg lett szakítva.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimális)** | -2147023673 | 
| **Hibakarakterlánc** | ERROR_CANCELLED |
| **Szervizelés szükséges** | Nem |

Szinkronizálási munkamenetek sikertelen lehet a különböző okok miatt, beleértve a kiszolgáló újraindítása vagy a frissített, a VSS-pillanatképet, és így tovább. Bár ez a hiba tűnik, hogy a követő műveletet igényel, biztonsággal figyelmen kívül hagyhatja ezt a hibát, kivéve, ha továbbra is fennáll, több órán keresztül.

<a id="-2147012889"></a>**Nem sikerült létrehozni a kapcsolatot a szolgáltatással.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimális)** | -2147012889 | 
| **Hibakarakterlánc** | WININET_E_NAME_NOT_RESOLVED |
| **Szervizelés szükséges** | Igen |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**A felhasználói kérelem szabályozta a szolgáltatást.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimális)** | -2134376372 |
| **Hibakarakterlánc** | ECS_E_USER_REQUEST_THROTTLED |
| **Szervizelés szükséges** | Nem |

Semmit nem kell; a kiszolgáló újra fog próbálkozni. Ha ezt a hibát néhány óránál hosszabb ideig továbbra is fennáll, hozzon létre egy támogatási kérést.

<a id="-2134364065"></a>**Szinkronizálás az a felhőbeli végpont a megadott Azure-fájlmegosztás nem érhető el.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimális)** | -2134364065 |
| **Hibakarakterlánc** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Szervizelés szükséges** | Igen |

Ez a hiba oka, hogy az Azure File Sync ügynök az Azure-fájlmegosztást, mert az Azure-fájlmegosztás vagy a storage-fiók futtató, már nem létezik, akkor nem lehet hozzáférni. Ez a hiba elhárításához feldolgozása révén az alábbi lépéseket:

1. [Ellenőrizze, hogy a tárfiók létezik-e.](#troubleshoot-storage-account)
2. [Ellenőrizze, hogy a tárfiók nem tartalmaz hálózati szabályokat.](#troubleshoot-network-rules)
3. [Győződjön meg arról, az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)
4. [Győződjön meg arról, az Azure File Sync hozzáfér a tárfiókhoz.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**A használt tárfiók neve nem oldható fel.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimális)** | -2134364064 |
| **Hibakarakterlánc** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Szervizelés szükséges** | Igen |

1. Ellenőrizze, hogy fel tudja oldani a storage DNS-név a kiszolgálóról.

    ```PowerShell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Ellenőrizze, hogy a tárfiók létezik-e.](#troubleshoot-storage-account)
3. [Ellenőrizze, hogy a tárfiók nem tartalmaz hálózati szabályokat.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**A sync-adatbázis kapcsolatos probléma miatt nem sikerült a szinkronizálást.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimális)** | -1906441138 |
| **Hibakarakterlánc** | JET_errWriteConflict |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, amikor egy probléma az Azure File Sync által használt belső adatbázissal. Ha a probléma akkor fordul elő, hozzon létre egy támogatási kérést, és felvesszük Önnel a probléma megoldásához nyújt segítséget.

<a id="-2134364053"></a>**Az Azure File Sync ügynök verziója telepítve van a kiszolgálón nem támogatott.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimális)** | -2134364053 |
| **Hibakarakterlánc** | ECS_E_AGENT_VERSION_BLOCKED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure File Sync ügynök verziója telepítve van a kiszolgálón nem támogatott. Ez a probléma megoldásához [frissítése]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) , egy [ügynök verziója támogatott]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**A megosztás Azure fájltárolási korlátot elérte.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimális)** | -2134351810 |
| **Hibakarakterlánc** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha a megosztás Azure fájltárolási korlátot elérte, is elő, ha a rendszer alkalmazza a kvótát az Azure-fájlmegosztás, vagy ha a használat túllépi a korlátot, az Azure-fájlmegosztások. További információkért lásd: a [Azure-fájlmegosztások a jelenlegi korlátok](storage-files-scale-targets.md).

1. Keresse meg a Storage Sync Service a szinkronizálási csoportban.
2. Válassza ki a felhőbeli végpont a szinkronizálási csoport belül.
3. Megjegyzés: az Azure-beli megosztási név a megnyitott ablaktáblán.
4. Válassza ki a csatolt tárfiókot. Ez a hivatkozás nem sikerül, ha a hivatkozott tárfiók el lett távolítva.

    ![Egy Képernyőkép a felhőbeli végpont részletek ablaktábláján egy hivatkozást a tárfiókra.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Válassza ki **fájlok** fájlmegosztások listájának megtekintéséhez.
6. Kattintson az Azure fájlmegosztások a felhőbeli végpont által hivatkozott a sor végén található három pontra.
7. Ellenőrizze, hogy a **használati** nem éri el a **kvóta**. Vegye figyelembe, ha egy másik kvóta lett megadva, a kvóta egyezni fog a [az Azure-fájlmegosztás maximális méretének](storage-files-scale-targets.md).

    ![Az Azure-beli tulajdonságok képernyőképe.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Ha a megosztás megtelt, és a kvóta van beállítva, a hiba egyik lehetséges módja az egyes almappájában a jelenlegi kiszolgálói végpontot, a saját kiszolgálói végpontot, a saját külön szinkronizálási csoportok győződjön meg arról. Ezzel a módszerrel minden almappa szinkronizálnak az egyes Azure-fájlmegosztások.

<a id="-2134351824"></a>**Az Azure-fájlmegosztás nem található.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimális)** | -2134351824 |
| **Hibakarakterlánc** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, amikor az Azure-fájlmegosztás nem érhető el. Hibaelhárítás:

1. [Ellenőrizze, hogy a tárfiók létezik-e.](#troubleshoot-storage-account)
2. [Győződjön meg arról, az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)

Ha törölték az Azure-fájlmegosztást, hozzon létre egy új fájlmegosztást, és hozza létre a szinkronizálási csoport szüksége. 

<a id="-2134364042"></a>**Szinkronizálási fel van függesztve, amíg az Azure-előfizetés fel van függesztve.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimális)** | -2134364042 |
| **Hibakarakterlánc** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, amikor az Azure-előfizetés fel van függesztve. Szinkronizálási lesz újra engedélyezve, ha az Azure-előfizetés visszaállítása. Lásd: [miért van az Azure-előfizetésem le van tiltva, és hogyan aktiválja újra azt?](../../billing/billing-subscription-become-disable.md) további információt.

<a id="-2134364052"></a>**A storage-fiók van egy tűzfal vagy a konfigurált virtuális hálózattal.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimális)** | -2134364052 |
| **Hibakarakterlánc** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, amikor a storage-fiók tűzfal miatt az Azure-fájlmegosztás nem érhető el, vagy mert a tárfiók tartozik egy virtuális hálózathoz. Az Azure File Sync még nem rendelkezik a funkció támogatása. Hibaelhárítás:

1. [Ellenőrizze, hogy a tárfiók létezik-e.](#troubleshoot-storage-account)
2. [Ellenőrizze, hogy a tárfiók nem tartalmaz hálózati szabályokat.](#troubleshoot-network-rules)

Ezek a szabályok a probléma megoldásához távolítsa el. 

<a id="-2134375911"></a>**A sync-adatbázis kapcsolatos probléma miatt nem sikerült a szinkronizálást.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimális)** | -2134375911 |
| **Hibakarakterlánc** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Szervizelés szükséges** | Nem |

Ez a hiba általában oldja fel magát, és akkor fordulhat elő, ha vannak:

* Nagy számú fájl módosítását a szinkronizálási csoport a kiszolgálók között.
* Nagy számú hibák az egyes fájlok és könyvtárak.

Ha több mint néhány óra alatt ez a hiba továbbra is fennáll, hozzon létre egy támogatási kérést, és felvesszük Önnel a probléma megoldásához nyújt segítséget.

<a id="-2146762487"></a>**A kiszolgáló nem sikerült biztonságos kapcsolatot létesíteni. A felhőszolgáltatás egy váratlan tanúsítványt kapott.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimális)** | -2146762487 |
| **Hibakarakterlánc** | CERT_E_UNTRUSTEDROOT |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordulhat elő, ha a szervezet az SSL-megszakító proxy, vagy ha egy rosszindulatú entitás elfogja a forgalmat a kiszolgáló és az Azure File Sync szolgáltatás között. Ha biztos benne, hogy ez várható (mivel a szervezet egy leállítása proxy SSL-t használ), akkor egy beállításjegyzék felülbírálással tanúsítvány-ellenőrzés kihagyása.

1. A SkipVerifyingPinnedRootCertificate beállításjegyzék-értéket hoz létre.

    ```PowerShell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Indítsa újra a szinkronizálási szolgáltatást a regisztrált kiszolgálón.

    ```PowerShell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ha beállítja ezt a beállításazonosítót, az Azure File Sync-ügynök minden helyileg megbízhatónak minősülő SSL-tanúsítványt elfogad a kiszolgáló és a felhőszolgáltatás közötti adatátvitel során.

<a id="-2147012894"></a>**Nem sikerült létrehozni a kapcsolatot a szolgáltatással.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimális)** | -2147012894 |
| **Hibakarakterlánc** | WININET_E_TIMEOUT |
| **Szervizelés szükséges** | Igen |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Hitelesítési probléma miatt nem sikerült a szinkronizálást.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimális)** | -2134375680 |
| **Hibakarakterlánc** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Szervizelés szükséges** | Igen |

Ez a hiba oka lehet:

- Helytelen a kiszolgáló ideje
- A kiszolgálói végpont törlése sikertelen volt
- Hitelesítéshez használt tanúsítvány lejárt. 
    Ellenőrizze, hogy ha a tanúsítvány lejárt, hajtsa végre az alábbi lépéseket:  
    1. A Tanúsítványok MMC beépülő modul megnyitásához, válassza ki a számítógép fiókját, és keresse meg \Personal\Certificates tanúsítványok (helyi számítógép).
    2. Ellenőrizze, hogy ha az ügyfél-hitelesítési tanúsítvány lejárt.

Ha a kiszolgáló ideje helyes, hajtsa végre az alábbi lépéseket a probléma megoldásához:

1. Ellenőrizze az Azure File Sync ügynök verziója 4.0.1.0 vagy újabb verziója szükséges.
2. A következő PowerShell-parancsok futtatása a kiszolgálón:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
    Login-AzureRmStorageSync -SubscriptionID <guid> -TenantID <guid>
    Reset-AzureRmStorageSyncServerCertificate -SubscriptionId <guid> -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**A szabad lemezterület a köteten, ahol a kiszolgálói végpont megtalálható értéke alacsony.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimális)** | -1906441711 |
| **Hibakarakterlánc** | JET_errLogDiskFull |
| **Szervizelés szükséges** | Igen |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimális)** | -2134375654 |
| **Hibakarakterlánc** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Szervizelés szükséges** | Igen |

Ez a hiba oka, hogy a kötet megtelt. Ez a hiba gyakran az okozza, hogy a kiszolgálói végpont kívül használ lemezterületet a köteten. Szabadítson fel területet a köteten adja hozzá a további kiszolgálói végpontot, fájlok áthelyezését egy másik kötetre, vagy növelje a kötet a kiszolgálói végpont van.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**A szolgáltatás még nem áll készen a kiszolgálói végpont szinkronizálandó.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimális)** | -2134364145 |
| **Hibakarakterlánc** | ECS_E_REPLICA_NOT_READY |
| **Szervizelés szükséges** | Nem |

Ez a hiba oka, hogy a módosítások vannak az Azure-fájlmegosztás közvetlenül és címváltozásának felderítését folyamatban van. Szinkronizálási címváltozásának felderítését befejezése után kezdődik.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Számos egyéni fájlokkal kapcsolatos problémák miatt nem sikerült a szinkronizálást.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimális)** | -2134364145 |
| **Hibakarakterlánc** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimális)** | -2134375908 |
| **Hibakarakterlánc** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimális)** | -2134375853 |
| **Hibakarakterlánc** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Szervizelés szükséges** | Igen |

Azokban az esetekben vannak sok fájl a szinkronizálási hibák száma, ahol szinkronizálási munkamenetek megkezdheti a sikertelen lesz. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Az Azure File Sync naponta egyszer létrehoz egy ideiglenes VSS-pillanatkép megnyitott kezelőkkel rendelkező fájlok szinkronizálása a kiszolgálón.

<a id="-2134376423"></a>**A kiszolgálói végpont elérési útja kapcsolatos probléma miatt nem sikerült a szinkronizálást.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimális)** | -2134376423 |
| **Hibakarakterlánc** | ECS_E_SYNC_INVALID_PATH |
| **Szervizelés szükséges** | Igen |

Győződjön meg arról, hogy az elérési út létezik, helyi NTFS-köteten található, és nem újraelemzési pont vagy meglévő kiszolgálói végpont.

<a id="-2134375817"></a>**A szinkronizálás nem sikerült, mert a szűrő-illesztőprogram verziója nem kompatibilis az ügynök verziója**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimális)** | -2134375817 |
| **Hibakarakterlánc** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Szervizelés szükséges** | Igen |

Ez a hiba oka, hogy a Felhőbeli Rétegezés szűrő illesztőprogram (StorageSync.sys) betöltött verziója nem kompatibilis a Storage Sync-ügynök (FileSyncSvc) szolgáltatás. Ha az Azure File Sync ügynök frissítve lett, indítsa újra a kiszolgálót, hogy a telepítés befejezéséhez. Ha a hiba továbbra is fennáll, az ügynök eltávolítása, indítsa újra a kiszolgálót, majd telepítse újra az Azure File Sync ügynök.

<a id="-2134376373"></a>**A szolgáltatás jelenleg nem érhető el.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimális)** | -2134376373 |
| **Hibakarakterlánc** | ECS_E_SERVICE_UNAVAILABLE |
| **Szervizelés szükséges** | Nem |

Ez a hiba oka, hogy az Azure File Sync szolgáltatás nem érhető el. Ez a hiba fog automatikus feloldása amikor az Azure File Sync szolgáltatás, mivel használható újra.

<a id="-2134375922"></a>**Szinkronizálás a sync-adatbázis átmeneti probléma miatt nem sikerült.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimális)** | -2134375922 |
| **Hibakarakterlánc** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Szervizelés szükséges** | Nem |

Ez a hiba akkor fordul elő, a szinkronizálási adatbázishoz belső hiba miatt. Ez a hiba fog automatikus feloldása mikor az Azure File Sync, amikor a szinkronizálási újrapróbálkozik. Ha ezt a hibát egy kiterjesztése ideig továbbra is fennáll, hozzon létre egy támogatási kérést, és felvesszük Önnel a probléma megoldásához nyújt segítséget.

### <a name="common-troubleshooting-steps"></a>Gyakori hibaelhárítási lépéseket
<a id="troubleshoot-storage-account"></a>**Ellenőrizze, hogy a tárfiók létezik-e.**  
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Keresse meg a Storage Sync Service a szinkronizálási csoportban.
2. Válassza ki a felhőbeli végpont a szinkronizálási csoport belül.
3. Megjegyzés: az Azure-beli megosztási név a megnyitott ablaktáblán.
4. Válassza ki a csatolt tárfiókot. Ez a hivatkozás nem sikerül, ha a hivatkozott tárfiók el lett távolítva.
    ![Egy Képernyőkép a felhőbeli végpont részletek ablaktábláján egy hivatkozást a tárfiókra.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables for you to populate based on your configuration
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# Log into the Azure account and put the returned account information
# in a reference variable.
$acctInfo = Connect-AzAccount

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzureRmStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.DisplayName
}

if ($storageSyncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzureRmStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.DisplayName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzureRmStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $storageSyncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    Write-Host "The storage account referenced in the cloud endpoint does not exist."
}
```
---

<a id="troubleshoot-network-rules"></a>**Ellenőrizze, hogy a tárfiók nem tartalmaz hálózati szabályokat.**  
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Egyszer a storage-fiókban lévő kiválasztása **tűzfalak és virtuális hálózatok** , a storage-fiókban a bal oldalon.
2. A storage-fiókban található a **engedélyezze a hozzáférést minden hálózatból elérhető** választógomb van kiválasztva.
    ![Egy Képernyőkép a tárolási fiók tűzfal és a hálózati szabályok le van tiltva.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    Write-Host ("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Győződjön meg arról, az Azure-fájlmegosztás létezik.**  
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Kattintson a **áttekintése** a fő tárfiók oldalának térjen vissza a bal oldali tartalomjegyzékben.
2. Válassza ki **fájlok** fájlmegosztások listájának megtekintéséhez.
3. Ellenőrizze, hogy a fájlmegosztás a felhőbeli végpont által hivatkozott (kell rendelkeznie feljegyzett a fenti 1. lépés) fájlmegosztások listájában megjelenik-e.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.StorageAccountShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    Write-Host "The Azure file share referenced by the cloud endpoint does not exist"
}
```
---

<a id="troubleshoot-rbac"></a>**Győződjön meg arról, az Azure File Sync hozzáfér a tárfiókhoz.**  
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
1. Kattintson a **hozzáférés-vezérlés (IAM)** a bal oldali tartalomjegyzékben.
1. Kattintson a **szerepkör-hozzárendelések** lap listájához, a felhasználók és alkalmazások (*egyszerű szolgáltatások*), amely rendelkezik a tárfiókhoz való hozzáférést.
1. Győződjön meg arról **hibrid File Sync szolgáltatásbeli** a listában megjelenik a **olvasó és adatelérés** szerepkör. 

    ![A hibrid File Sync szolgáltatásbeli szolgáltatásnév az a tárfiók a hozzáférés-vezérlési lap képernyőképe](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Ha **hibrid File Sync szolgáltatásbeli** nem szerepelnek a listán, hajtsa végre az alábbi lépéseket:

    - Kattintson a **Hozzáadás** parancsra.
    - Az a **szerepkör** mezőben válassza **olvasó és adatelérés**.
    - Az a **kiválasztása** mezőbe írja be a **hibrid File Sync szolgáltatásbeli**, válassza ki a szerepkört, és kattintson a **mentése**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell    
$foundSyncPrincipal = $false
Get-AzRoleAssignment -Scope $storageAccount.Id | ForEach-Object { 
    if ($_.DisplayName -eq "Hybrid File Sync Service") {
        $foundSyncPrincipal = $true
        if ($_.RoleDefinitionName -ne "Reader and Data Access") {
            Write-Host ("The storage account has the Azure File Sync " + `
                "service principal authorized to do something other than access the data " + `
                "within the referenced Azure file share.")
        }

        break
    }
}

if (!$foundSyncPrincipal) {
    Write-Host ("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hogyan akadályozható meg, hogy felhasználók létrehozása a kiszolgálón nem támogatott karaktereket tartalmazó fájlok?
Használhat [File Server Resource Manager (FSRM) fájlszűrők](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) blokk fájlok a nevükben nem támogatott karaktereket az a kiszolgáló létrehozása folyamatban. Előfordulhat, hogy ehhez, mivel az nem támogatott karaktereket a legtöbb nem nyomtatható, és így hexadecimális nézeteik először karakter között kell a PowerShell használatával.

Először hozzon létre egy FSRM fájlcsoport a [New-FsrmFileGroup parancsmag](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Ebben a példában csak két a nem támogatott karaktereket tartalmaz a csoport határozza meg, de a karaktereket a fájl csoport szükség szerint több is felvehet.

```PowerShell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Miután beállított egy FSRM fájlcsoport, egy FSRM fájlszűrést, a New-FsrmFileScreen parancsmaggal hozhat létre.

```PowerShell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Megjegyezni, hogy az fájlszűrők csak használható blokkolása létrehozása az Azure File Sync nem támogatott karaktereket. Fájlszűrők használnak más forgatókönyvek esetében, ha a szinkronizálási megpróbálja folyamatosan töltse le a fájlokat az Azure-fájlmegosztást a kiszolgáló, és a fájlszűrő eredményez a nagy régiós adatmozgás miatt le lesz tiltva. 

## <a name="cloud-tiering"></a>Felhőbeli rétegzés 
Két út hibák felhőbeli rétegezés:

- Fájlok Tier, is sikertelen, ami azt jelenti, hogy az Azure File Sync sikertelenül próbál egy fájlt az Azure Files szinten.
- Fájlok meghiúsulhat visszaírási, ami azt jelenti, hogy az Azure File Sync fájlrendszerszűrő (StorageSync.sys) nem sikerül letölteni az adatokat, amikor egy felhasználó megpróbál hozzáférni egy fájlhoz, amely tartozik lett rétegzett.

Nincsenek hibák, amely akkor fordulhat elő, vagy hiba elérési útján két fő osztályok:

- Felhőalapú tárolási hibák
    - *Átmeneti tárolási szolgáltatás rendelkezésre állási problémák*. További információkért lásd: a [szolgáltatói szerződés (SLA) az Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Nem elérhető Azure-fájlmegosztás*. Ez a hiba általában akkor fordul elő, amikor még a felhőbeli végpont az egy szinkronizálási csoportban az Azure-fájlmegosztás törlésekor.
    - *A tárfiók nem érhető el*. Ez a hiba általában akkor fordul elő, a storage-fiók törlésekor, miközben továbbra is rendelkezik egy Azure-fájlmegosztás, amely egy felhőbeli végpont az egy szinkronizálási csoportban. 
- Kiszolgálóhibák 
  - *Az Azure File Sync fájlrendszerszűrő (StorageSync.sys) nincs betöltve*. Annak érdekében, hogy a kérelmek rétegezést/visszaírási válaszol, az Azure File Sync fájlrendszerszűrő kell betölteni. A szűrő nem töltődött több okból is fordulhat elő, de a leggyakoribb oka, hogy a rendszergazda a memóriából, manuálisan. Az Azure File Sync fájlrendszerszűrő kell betölteni mindig az Azure File Sync megfelelő működéséhez.
  - *Hiányzik, sérült vagy más módon sérült újraelemzési pont*. Újraelemzési pont olyan speciális adatstruktúra egy fájlon, amely két részből áll:
    1. Egy újraelemzési címkét, ami azt jelzi, hogy az operációs rendszer, hogy az Azure File Sync fájlrendszerszűrő (StorageSync.sys) kell előfordulhat, hogy néhány művelet a fájl i/o tegye. 
    2. Újraelemzési adatokat, ami azt jelzi, hogy a fájlrendszerszűrőnek URI-ját a fájlt a felhőbeli végpont (az Azure-fájlmegosztás). 
        
       Újraelemzési pont megsérülhetnek leggyakoribb módja, ha a rendszergazda megpróbálja módosítani a címkét vagy annak adatai. 
  - *Hálózati kapcsolati problémák*. Annak érdekében, hogy szolgáltatásiréteg, vagy visszahívás egy fájlt, a kiszolgáló internetkapcsolattal kell rendelkeznie.

Az alábbi szakaszok azt jelzik, hogy felhőalapú rétegezési hibáinak elhárítása és, hogy a problémát egy felhőalapú tárolási probléma vagy egy kiszolgálóhiba.

<a id="monitor-tiering-activity"></a>**Egy kiszolgálói rétegezési tevékenység figyelése**  
Egy kiszolgálói rétegezési tevékenység monitorozásához használja Event ID 9003, 9016 és 9029 a telemetriai adatok eseménynaplóban (alkalmazások és az eseménynaplóban Services\Microsoft\FileSync\Agent alatt található).

- 9003-es Azonosítójú esemény a kiszolgálói végpont hibaeloszlás biztosít. Ha például összesített hibaszám, ErrorCode, stb. Vegye figyelembe, hogy egy eseményt a rendszer naplózza hibakód.
- Eseményazonosító 9016 másolatkészítési eredményeket biztosít egy köteten. Például a szabad terület százalékos értéke, a fájlok száma mögé munkamenetben, több fájlt nem sikerült ghost stb.
- Eseményazonosító 9029 kiszolgálói végpont másolatkészítési munkamenet információkat nyújt. Például a munkamenet, a fájlok száma a megkísérelt fájlok száma rétegzett a munkamenetben, a fájlok száma már rétegzett, stb.

<a id="monitor-recall-activity"></a>**Egy kiszolgálói visszaírási tevékenység figyelése**  
A kiszolgálói tevékenység visszaírási monitorozásához használja Event ID 9005, 9006, 9009 és 9059 a telemetriai adatok eseménynaplóban (alkalmazások és az eseménynaplóban Services\Microsoft\FileSync\Agent alatt található).

- 9005-es Azonosítójú esemény a kiszolgálói végpont visszaírási megbízhatóságot biztosít. Például teljes egyedi fájlok elérni, egyedi fájlok száma és a sikertelen hozzáférés, stb.
- Eseményazonosító 9006 visszaírási hibaeloszlás a kiszolgálói végpont biztosít. Például összes sikertelen kérelem ErrorCode, stb. Vegye figyelembe, hogy egy eseményt a rendszer naplózza hibakód.
- Eseményazonosító 9009 kiszolgálói végpont visszaírási munkamenet információkat nyújt. Például DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, stb.
- Eseményazonosító 9059 alkalmazás visszaírási terjesztési a kiszolgálói végpont biztosít. Ha például ShareId, alkalmazás neve és TotalEgressNetworkBytes.

<a id="files-fail-tiering"></a>**A nem induló réteg-fájlok hibaelhárítása**  
Ha az Azure Files Tier fájloknál:

1. Az Eseménynaplóban tekintse át a telemetriai adatokat, a üzemeltetési és diagnosztikai az eseménynaplókat, alkalmazások és Services\Microsoft\FileSync\Agent alatt található. 
   1. Ellenőrizze, hogy a fájlokat az Azure-fájlmegosztás léteznek-e.

      > [!NOTE]
      > Egy fájl szinkronizálnia kell az Azure-fájlmegosztás, mielőtt lesz rétegzett.

   2. Ellenőrizze, hogy a kiszolgáló rendelkezik internetkapcsolattal. 
   3. Ellenőrizze a fájlrendszerszűrő-illesztőprogramok (StorageSync.sys és StorageSyncGuard.sys) az Azure File Sync futnak:
       - Egy rendszergazda jogú parancssorból futassa `fltmc`. Győződjön meg arról, hogy a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok szerepel.

> [!NOTE]
> Egy Event ID 9003 a telemetriai adatok eseménynaplójában óránként egyszer kerül, ha egy fájl sikertelen Tier (egy eseményt a rendszer naplózza hibakód). Az operatív és diagnosztikai eseménynaplók kell használható, ha további információra van szüksége a probléma diagnosztizálása érdekében.

<a id="files-fail-recall"></a>**Fájlok, amelyek nem hívhatók vissza hibaelhárítása**  
Ha a fájlok nem hívhatók vissza:
1. Az Eseménynaplóban tekintse át a telemetriai adatokat, a üzemeltetési és diagnosztikai az eseménynaplókat, alkalmazások és Services\Microsoft\FileSync\Agent alatt található.
    1. Ellenőrizze, hogy a fájlokat az Azure-fájlmegosztás léteznek-e.
    2. Ellenőrizze, hogy a kiszolgáló rendelkezik internetkapcsolattal. 
    3. A szolgáltatások MMC beépülő modul megnyitásához, és ellenőrizze a Storage Sync-ügynök szolgáltatás (FileSyncSvc) fut-e.
    4. Ellenőrizze a fájlrendszerszűrő-illesztőprogramok (StorageSync.sys és StorageSyncGuard.sys) az Azure File Sync futnak:
        - Egy rendszergazda jogú parancssorból futassa `fltmc`. Győződjön meg arról, hogy a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok szerepel.

> [!NOTE]
> Egy esemény azonosítója 9006 óránként egyszer a telemetriai adatok eseménynaplóban naplózza, ha egy fájl sikertelen visszaírásához (egy eseményt a rendszer naplózza hibakód). Az operatív és diagnosztikai eseménynaplók kell használható, ha további információra van szüksége a probléma diagnosztizálása érdekében.

<a id="files-unexpectedly-recalled"></a>**A kiszolgáló váratlanul visszahívásra-fájlok hibaelhárítása**  
A víruskereső, a backup és az egyéb alkalmazásokhoz, amelyek nagy mennyiségű fájlt olvasása miatt nem kívánt visszahívások, ha nem veszik figyelembe a skip offline attribútumot, és a mellőzik ezen fájlok tartalmának olvasását. Az offline fájlok kihagyását támogató termékek esetében a kihagyás segíthet elkerülni a nem kívánt visszahívásokat a víruskeresések, a biztonsági mentési feladatok és a hasonló műveletek során.

Érdeklődjön a szoftverszállítónál, hogy megtudja, hogyan konfigurálhatja a megoldást az offline fájlok olvasásának kihagyására.

Nem kívánt visszahívások is fordulhat elő, az egyéb forgatókönyvek, például fájlokat a Fájlkezelőben böngészésekor. Ha a kiszolgálón található Fájlkezelőben felhőalapú rétegzett fájlokat tartalmazó mappákat nyit meg, az nem kívánt visszahívásokat eredményezhet. Ez még gyakrabban előfordul, ha engedélyezve van egy víruskereső megoldás a kiszolgálón.

> [!NOTE]
>A telemetriai adatok eseménynaplójában Event ID 9059 segítségével meghatározhatja, mely alkalmazását vagy alkalmazásait visszahívások okozza. Ez az esemény alkalmazás visszaírási terjesztési a kiszolgálói végpont biztosít, és óránként egyszer naplózza.

## <a name="general-troubleshooting"></a>Általános hibaelhárítási tippek
Ha egy kiszolgálón az Azure File Sync problémák merülnek fel, indítsa el az alábbi lépések végrehajtásával:
1. Az Eseménynaplóban tekintse át a telemetriai adatokat, a üzemeltetési és a diagnosztikai eseménynaplóban.
    - Szinkronizálni, rétegzést, és a visszaírási problémák van bejelentkezve a telemetriai adatok, alkalmazás-és Services\Microsoft\FileSync\Agent diagnosztikai és a működési eseménynaplóit.
    - A kiszolgáló (például konfigurációs beállítások) felügyeletével kapcsolatos problémák a működési és diagnosztikai eseménynaplóit Applications and Services\Microsoft\FileSync\Management jelentkezett be.
2. Ellenőrizze, hogy az Azure File Sync szolgáltatás fut a kiszolgálón:
    - A szolgáltatások MMC beépülő modul megnyitásához, és győződjön meg arról, hogy fut-e a Storage Sync-ügynök szolgáltatás (FileSyncSvc).
3. Ellenőrizze a fájlrendszerszűrő-illesztőprogramok (StorageSync.sys és StorageSyncGuard.sys) az Azure File Sync futnak:
    - Egy rendszergazda jogú parancssorból futassa `fltmc`. Győződjön meg arról, hogy a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok szerepel.

Ha a probléma továbbra is fennáll, futtassa a AFSDiag eszköz:
1. Hozzon létre egy könyvtárat, ahol a AFSDiag kimeneti menti a rendszer (például C:\Output).
2. Nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő parancsokat (nyomja le az Enter minden parancs után):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Adja meg az Azure File Sync kernel mód nyomkövetési szint **1** (amennyiben másként nem szerepel, a részletesebb nyomkövetések létrehozásához), és nyomja le az ENTER billentyűt.
4. Adja meg az Azure File Sync felhasználói mód nyomkövetési szint **1** (amennyiben másként nem szerepel, a részletesebb nyomkövetések létrehozásához), és nyomja le az ENTER billentyűt.
5. Reprodukálja a problémát. Ha elkészült, adja meg a **D**.
6. Egy .zip fájlt, amely tartalmazza a naplók és a nyomkövetési fájlok a megadott kimeneti könyvtárba menti.

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
- [Az Azure Files – gyakori kérdések](storage-files-faq.md)
- [Azure Files-problémák hibaelhárítása Windowson](storage-troubleshoot-windows-file-connection-problems.md)
- [A Linux Azure Files-problémák hibaelhárítása](storage-troubleshoot-linux-file-connection-problems.md)
