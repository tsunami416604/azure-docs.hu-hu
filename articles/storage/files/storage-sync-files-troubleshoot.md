---
title: Azure file sync hibaelhárítása | Microsoft dokumentumok
description: Az Azure File Sync szolgáltatással kapcsolatos gyakori problémák elhárítása.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: ebe5ddf72e13b1a66ded7a90976e0b6209a26dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060969"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure-fájlok szinkronizálásának hibaelhárítása
Az Azure File Sync használatával központosíthatja a szervezet fájlmegosztásait az Azure Files ban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Server kiszolgálón elérhető bármely protokoll thasználhat az adatok helyi eléréséhez, beleértve az SMB, az NFS és az FTPS protokollt. Annyi gyorsítótára lehet, amennyire szüksége van szerte a világon.

Ez a cikk az Azure File Sync központi telepítésével kapcsolatos problémák elhárítását és megoldását segíti. Azt is leírjuk, hogyan gyűjthet fontos naplókat a rendszerből, ha a probléma mélyebb vizsgálatára van szükség. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. [Azure Storage Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft-támogatás. Új támogatási kérelem létrehozásához az Azure Portal **súgólapján** kattintson a **Súgó + támogatás** gombra, majd az Új támogatási **kérelem**elemre.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Problémám van az Azure File Sync szolgáltatással a kiszolgálómon (szinkronizálás, felhőrétegezés stb.). Eltávolítsam és hozzam létre újra a kiszolgálóvégpontot?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Ügynök telepítése és kiszolgálóregisztrációja
<a id="agent-installation-failures"></a>**Ügynöktelepítési hibák elhárítása**  
Ha az Azure File Sync ügynök telepítése sikertelen, egy rendszergazda jogú parancssorra futtassa a következő parancsot a naplózás bekapcsolására az ügynök telepítése során:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Tekintse át a installer.log fájlt a telepítési hiba okának meghatározásához.

<a id="agent-installation-on-DC"></a>**Az ügynök telepítése sikertelen az Active Directory tartományvezérlőn**  
Ha olyan Active Directory-tartományvezérlőre próbálja telepíteni a szinkronizálási ügynököt, amelyen az elsődleges tartományvezérlő szerepkör tulajdonosa Windows Server 2008 R2 vagy az operációs rendszer alatti verzión található, előfordulhat, hogy a hiba miatt a szinkronizálási ügynök telepítése sikertelen lesz.

A probléma megoldásához vigye át az elsődleges tartományvezérlő szerepkört egy másik, Windows Server 2012 R2 vagy újabb rendszert futtató tartományvezérlőre, majd telepítse a szinkronizálást.

<a id="parameter-is-incorrect"></a>**Egy kötet elérése Windows Server 2012 R2 rendszeren hiba miatt sikertelen: A paraméter helytelen**  
Miután létrehozott egy kiszolgálóvégpontot Windows Server 2012 R2 rendszeren, a következő hiba történik a kötet elérésekor:

meghajtóbetűjel:\ nem érhető el.  
A paraméter helytelen.

A probléma megoldásához telepítse a Windows Server 2012 R2 legújabb frissítéseit, és indítsa újra a kiszolgálót.

<a id="server-registration-missing-subscriptions"></a>**A kiszolgálóregisztráció nem tartalmazza az összes Azure-előfizetést**  
Kiszolgáló regisztrálásakor a ServerRegistration.exe használatával, az előfizetések hiányoznak, amikor az Azure-előfizetés legördülő menüre kattint.

A probléma oka az, hogy a ServerRegistration.exe jelenleg nem támogatja a több-bérlős környezeteket. Ezt a problémát egy későbbi Azure File Sync ügynökfrissítés fogja kijavítani.

A probléma kerülő megoldásához használja az alábbi PowerShell-parancsokat a kiszolgáló regisztrálásához:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**A kiszolgáló regisztrációja a következő üzenetet jeleníti meg: "Az előfeltételek hiányoznak"**  
Ez az üzenet akkor jelenik meg, ha az Az vagy az AzureRM PowerShell-modul nincs telepítve a PowerShell 5.1-es rendszerre. 

> [!Note]  
> A ServerRegistration.exe nem támogatja a PowerShell 6.x-et. A Register-AzStorageSyncServer parancsmag a PowerShell 6.x-en a kiszolgáló regisztrálásához.

Az Az vagy az AzureRM-modul PowerShell 5.1-re való telepítéséhez hajtsa végre az alábbi lépéseket:

1. Írja be **a powershell** parancsot egy emelt szintű parancssorból, és nyomja meg az Enter billentyűt.
2. Telepítse a legújabb Az vagy AzureRM modult a következő dokumentációszerint:
    - [Az modul (.NET 4.7.2 szükséges)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM modul]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Futtassa a ServerRegistration.exe programot, és fejezze be a varázslót a kiszolgáló storage-szinkronizálási szolgáltatással való regisztrálásához.

<a id="server-already-registered"></a>**A kiszolgáló regisztrációja a következő üzenetet jeleníti meg: "Ez a kiszolgáló már regisztrálva van"** 

![Képernyőkép a Kiszolgáló regisztrációs párbeszédpaneléről a "A kiszolgáló már regisztrálva van" hibaüzenettel](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ez az üzenet akkor jelenik meg, ha a kiszolgálót korábban regisztrálták egy tárolószinkronizálási szolgáltatásban. Ha törölni szeretné a kiszolgáló regisztrációját az aktuális storage-szinkronizálási szolgáltatásból, majd regisztrálni szeretne egy új storage-szinkronizálási szolgáltatással, hajtsa végre a [kiszolgáló regisztrációjának megszüntetése](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)az Azure File Sync szolgáltatással című dokumentumban ismertetett lépéseket.

Ha a kiszolgáló nem szerepel a Storage Sync Szolgáltatás **Regisztrált kiszolgálói** között, a regisztrációról törölni kívánt kiszolgálón futtassa a következő PowerShell-parancsokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Ha a kiszolgáló egy fürt része, a választható *Reset-StorageSyncServer -CleanClusterRegistration* paraméterrel eltávolíthatja a fürtregisztrációt is.

<a id="web-site-not-trusted"></a>**Amikor regisztrálok egy szervert, számos "nem megbízható" választ látok. miért?**  
Ez a probléma akkor fordul **elő,** ha a kiszolgáló regisztrációja során engedélyezve van az Internet Explorer fokozott biztonsági házirendje. Az **Internet Explorer továbbfejlesztett biztonsági** házirendjének helyes letiltásáról a Windows Server előkészítése az Azure File Sync [szolgáltatással való használatra](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) és az Azure File [Sync telepítésének előkészítésében](storage-sync-files-deployment-guide.md)című témakörben talál további információt.

<a id="server-registration-missing"></a>**A kiszolgáló nem szerepel az Azure Portal regisztrált kiszolgálói között**  
Ha egy kiszolgáló nem szerepel a Storage Sync Service **regisztrált kiszolgálói** között:
1. Jelentkezzen be a regisztrálni kívánt kiszolgálóra.
2. Nyissa meg a Fájlkezelőt, majd nyissa meg a Storage Sync Agent telepítési könyvtárát (az alapértelmezett hely a C:\Program Files\Azure\StorageSyncAgent). 
3. Futtassa a ServerRegistration.exe programot, és fejezze be a varázslót a kiszolgáló storage-szinkronizálási szolgáltatással való regisztrálásához.

## <a name="sync-group-management"></a>Szinkronizálási csoport kezelése
<a id="cloud-endpoint-using-share"></a>**A felhővégpont létrehozása sikertelen, ezzel a hibával: "A megadott Azure FileShare-t már használja egy másik CloudEndpoint"**  
Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztást már egy másik felhőbeli végpont használja. 

Ha ezt az üzenetet látja, és az Azure-fájlmegosztást jelenleg nem használja egy felhővégpont, hajtsa végre az alábbi lépéseket az Azure File Sync metaadatainak törléséhez az Azure-fájlmegosztáson:

> [!Warning]  
> A metaadatok törlése egy felhővégpont által jelenleg használt Azure-fájlmegosztáson az Azure File Sync-műveletek sikertelenek lesznek. 

1. Az Azure Portalon nyissa meg az Azure-fájlmegosztást.  
2. Kattintson a jobb gombbal az Azure-fájlmegosztásra, és válassza **a Metaadatok szerkesztése parancsot.**
3. Kattintson a jobb gombbal **a SyncService**elemre, majd válassza **a Törlés parancsot.**

<a id="cloud-endpoint-authfailed"></a>**A felhővégpont létrehozása sikertelen, a következő hibával: "AuthorizationFailed"**  
Ez a hiba akkor fordul elő, ha a felhasználói fiók nem rendelkezik megfelelő jogosultságokkal egy felhővégpont létrehozásához. 

Felhővégpont létrehozásához a felhasználói fióknak a következő Microsoft engedélyezési engedélyekkel kell rendelkeznie:  
* Olvassa el: Szerepkör-definíció beolvasása
* Írás: Egyéni szerepkör-definíció létrehozása vagy frissítése
* Olvasás: Szerepkör-hozzárendelés beolvasása
* Írás: Szerepkör-hozzárendelés létrehozása

A következő beépített szerepkörök rendelkeznek a szükséges Microsoft-engedélyezési engedélyekkel:  
* Tulajdonos
* Felhasználói hozzáférés rendszergazdája

Annak megállapítása, hogy a felhasználói fiók szerepköre rendelkezik-e a szükséges engedélyekkel:  
1. Az Azure Portalon válassza az **Erőforráscsoportok**lehetőséget.
2. Jelölje ki azt az erőforráscsoportot, amelyben a tárfiók található, majd válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.**
3. Válassza a **Szerepkör-hozzárendelések** lapot.
4. Válassza ki a **szerepkört** (például tulajdonos vagy közreműködő) a felhasználói fiókjához.
5. Az **Erőforrás-szolgáltató** listában válassza a **Microsoft Authorization (Microsoft Authorization) lehetőséget.** 
    * **A szerepkör-hozzárendelésnek** **olvasási** és írási engedéllyel kell **rendelkeznie.**
    * **A szerepkör-definíciónak** **olvasási** és írási engedéllyel kell **rendelkeznie.**

<a id="-2134375898"></a>**A kiszolgálóvégpont létrehozása sikertelen, a következő hiba a következő hibaüzenettel: "MgmtServerJobFailed" (Hibakód: -2134375898 vagy 0x80c80226)**  
Ez a hiba akkor fordul elő, ha a kiszolgálóvégpont elérési útja a rendszerköteten van, és a felhőbeli rétegezés engedélyezve van. A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.

<a id="-2147024894"></a>**A kiszolgálóvégpont létrehozása sikertelen, a következő hiba történt: "MgmtServerJobFailed" (Hibakód: -2147024894 vagy 0x80070002)**  
Ez a hiba akkor következik be, ha a kiszolgálóvégpont megadott elérési útja nem érvényes. Ellenőrizze, hogy a megadott kiszolgálóvégpont elérési útja helyileg csatlakoztatott NTFS-kötet-e. Megjegyzés: az Azure File Sync nem támogatja a leképezett meghajtókat a kiszolgáló végpontelérési útjaként.

<a id="-2134375640"></a>**A kiszolgálóvégpont létrehozása sikertelen, a következő hiba történt: "MgmtServerJobFailed" (Hibakód: -2134375640 vagy 0x80c80328)**  
Ez a hiba akkor fordul elő, ha a megadott kiszolgálóvégpont elérési útja nem NTFS-kötet. Ellenőrizze, hogy a megadott kiszolgálóvégpont elérési útja helyileg csatlakoztatott NTFS-kötet-e. Megjegyzés: az Azure File Sync nem támogatja a leképezett meghajtókat a kiszolgáló végpontelérési útjaként.

<a id="-2134347507"></a>**A kiszolgálóvégpont létrehozása sikertelen, a következő hiba a következő hibaüzenettel: "MgmtServerJobFailed" (Hibakód: -2134347507 vagy 0x80c8710d)**  
Ez a hiba azért fordult elő, mert az Azure File Sync nem támogatja a kiszolgálóvégpontokat olyan köteteken, amelyeken tömörítve van a rendszerkötet információit tartalmazó mappa. A probléma elhárításához bontsa ki a rendszerkötet információit tartalmazó mappát. Ha a rendszerkötet információit tartalmazó mappa az egyetlen tömörített mappa a köteten, hajtsa végre az alábbi lépéseket:

1. Letöltés [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) eszköz.
2. A következő parancs futtatása emelt szintű parancssorból a rendszerfiók alatt futó parancssor elindításához: **PsExec.exe -i -s -d cmd**
3. A rendszerfiók alatt futó parancssorban írja be a következő parancsot, és nyomja le az Enter billentyűt:   
    **cd /d "meghajtóbetűjel:\Rendszerkötet-információ"**  
    **kompakt /u /s**

<a id="-2134376345"></a>**A kiszolgálóvégpont létrehozása sikertelen, a következő hiba a következő hibaüzenettel: "MgmtServerJobFailed" (Hibakód: -2134376345 vagy 0x80C80067)**  
Ez a hiba a kiszolgálónkénti kiszolgálóvégpontok korlátjának elérésekor következik be. Az Azure File Sync jelenleg legfeljebb 30 kiszolgálóvégpontot támogat kiszolgálónként. További információ: [Azure File Sync scale targets](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**A kiszolgálóvégpont létrehozása sikertelen, a következő hiba történt: "MgmtServerJobFailed" (Hibakód: -2134376427 vagy 0x80c80015)**  
Ez a hiba akkor fordul elő, ha egy másik kiszolgálóvégpont már szinkronizálja a kiszolgálóvégpont megadott elérési útját. Az Azure File Sync nem támogatja, hogy ugyanazt a címtárat vagy kötetet több kiszolgálóvégpont szinkronizálja.

<a id="-2160590967"></a>**A kiszolgálóvégpont létrehozása sikertelen, a következő hiba a következő hibaüzenettel: "MgmtServerJobFailed" (Hibakód: -2160590967 vagy 0x80c80077)**  
Ez a hiba akkor fordul elő, ha a kiszolgálóvégpont elérési útja árva rétegzett fájlokat tartalmaz. Ha egy kiszolgálóvégpontot nemrég távolítottak el, várjon, amíg az árva rétegzett fájlok karbantartása befejeződik. A 6662-es azonosítójú eseményt a rendszer naplózza a Telemetriai eseménynaplóba, miután az árva rétegzett fájlok karbantartása megkezdődött. A 6661-es azonosítójú eseményt a rendszer naplózza, ha az árva rétegzett fájlok karbantartása befejeződött, és a kiszolgálóvégpont újra létrehozható az elérési út használatával. Ha a kiszolgálóvégpont létrehozása a 6661-es azonosítójú esemény naplózása után meghiúsul, távolítsa el az árva rétegzett fájlokat a rétegzett fájlokban dokumentált lépések [végrehajtásával, amelyek nem érhetők el a kiszolgálóvégponti szakasz törlése után a kiszolgálón.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)

<a id="-2134347757"></a>**A kiszolgálóvégpont törlése sikertelen, a következő hibával: "MgmtServerJobExpired" (Hibakód: -2134347757 vagy 0x80c87013)**  
Ez a hiba akkor következik be, ha a kiszolgáló offline állapotban van, vagy nem rendelkezik hálózati kapcsolattal. Ha a kiszolgáló már nem érhető el, törölje a kiszolgáló regisztrációját a portálon, amivel törli a kiszolgálóvégpontokat. A kiszolgálóvégpontok törléséhez kövesse a [Kiszolgáló regisztrációjának megszüntetése](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)az Azure File Sync segítségével című részben leírt lépéseket.

<a id="server-endpoint-provisioningfailed"></a>**Nem lehet megnyitni a kiszolgálóvégpont tulajdonságai lapot, vagy frissíteni a felhőrétegezési házirendet**  
Ez a probléma akkor fordulhat elő, ha a kiszolgáló végpontján egy felügyeleti művelet sikertelen. Ha a kiszolgálóvégpont tulajdonságai lap nem nyílik meg az Azure Portalon, a kiszolgálóvégpont frissítése a kiszolgálópowerShell-parancsaival megoldhatja ezt a problémát. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**A kiszolgálóvégpont állapota "Nincs tevékenység" vagy "Függőben" állapot, és a regisztrált kiszolgálópanelen a kiszolgáló állapota "Offline állapotban jelenik meg"**  

Ez a probléma akkor fordulhat elő, ha a Storage Sync Monitor folyamat (AzureStorageSyncMonitor.exe) nem fut, vagy a kiszolgáló nem tud hozzáférni az Azure File Sync szolgáltatáshoz.

A portálon "Kapcsolat nélküli módban jelenik meg" néven megjelenő kiszolgálón tekintse meg a 9301-es eseményazonosítót a Telemetriai eseménynaplóban (az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában található), és állapítsa meg, hogy a kiszolgáló miért nem tud hozzáférni az Azure File Sync szolgáltatáshoz. Szolgáltatás. 

- Ha **a GetNextJob állapot: 0 a** naplózott állapottal, a kiszolgáló kommunikálhat az Azure File Sync szolgáltatással. 
    - Nyissa meg a Feladatkezelőt a kiszolgálón, és ellenőrizze, hogy fut-e a Storage Sync Monitor-folyamat (AzureStorageSyncMonitor.exe). Ha a folyamat nem fut, először próbálja meg újraindítani a kiszolgálót. Ha a kiszolgáló újraindítása nem oldja meg a problémát, frissítsen az Azure File Sync-ügynök [legújabb verziójára](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes). 

- Ha **a GetNextJob a következő állapottal fejeződött be: -2134347756,** a kiszolgáló tűzfal vagy proxy miatt nem tud kommunikálni az Azure File Sync szolgáltatással. 
    - Ha a kiszolgáló tűzfal mögött van, ellenőrizze, hogy engedélyezve van-e a 443-as port kimenő forgalma. Ha a tűzfal meghatározott tartományokra korlátozza a forgalmat, ellenőrizze, hogy a tűzfal [dokumentációjában](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) felsorolt tartományok elérhetők-e.
    - Ha a kiszolgáló proxy mögött van, konfigurálja a számítógép-szintű vagy alkalmazásspecifikus proxybeállításokat a Proxy [dokumentációjában](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)leírt lépések végrehajtásával.
    - A Test-StorageSyncNetworkConnectivity parancsmag segítségével ellenőrizze a hálózati kapcsolatot a szolgáltatás végpontjaihoz. További információ: [Hálózati kapcsolat tesztelése a szolgáltatásvégpontokhoz.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)

- Ha **a GetNextJob a következő állapottal fejeződött be: -2134347764,** a kiszolgáló lejárt vagy törölt tanúsítvány miatt nem tud kommunikálni az Azure File Sync szolgáltatással.  
    - Futtassa a következő PowerShell parancsot a kiszolgálón a hitelesítéshez használt tanúsítvány alaphelyzetbe állításához:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**A kiszolgálóvégpont állapota "Nincs tevékenység", a regisztrált kiszolgálók panelen pedig "Online" állapotúak.**  

A "Nincs tevékenység" kiszolgálóvégpont állapota azt jelenti, hogy a kiszolgálóvégpont az elmúlt két órában nem naplózta a szinkronizálási tevékenységet.

Az aktuális szinkronizálási tevékenység ellenőrzéséhez a Kiszolgálón végzett aktuális [szinkronizálási tevékenységről a Hogyan figyelhetem az aktuális szinkronizálási munkamenet előrehaladását?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

Előfordulhat, hogy egy kiszolgálóvégpont hiba vagy nem elegendő rendszererőforrás miatt több órán keresztül nem naplózza a szinkronizálási tevékenységet. Ellenőrizze, hogy telepítve van-e az Azure File Sync [ügynök legújabb verziója.](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) Ha a probléma továbbra is fennáll, nyisson meg egy támogatási kérelmet.

> [!Note]  
> Ha a regisztrált kiszolgálók panelen a kiszolgáló állapota "Kapcsolat nélküli módban jelenik meg", hajtsa végre a [kiszolgálóvégpontban dokumentált lépéseket,"Nincs tevékenység" vagy "Függőben" állapotú, és a regisztrált kiszolgálók panelen a "Kapcsolat nélküli módban jelenik meg" szakasz ban található kiszolgálóállapota "Kapcsolat nélküli módban jelenik meg".](#server-endpoint-noactivity)

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Ha közvetlenül az Azure-fájlmegosztásban hoztam létre egy fájlt SMB-n vagy a portálon keresztül, mennyi ideig tart, amíg a fájl szinkronizálódik a szinkronizálási csoport kiszolgálóival?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**A kiszolgáló végpontjának állapota több órán keresztül függőben van**  
Ez a probléma akkor várható, ha létrehoz egy felhővégpontot, és adatokat tartalmazó Azure-fájlmegosztást használ. Az Azure-fájlmegosztás módosításait keresve a módosítási felsorolási feladatnak be kell fejeződnie ahhoz, hogy a fájlok szinkronizálhatók legyenek a felhő és a kiszolgáló végpontjai között. A feladat befejezéséhez szükséges idő az Azure-fájlmegosztásban lévő névtér méretétől függ. A kiszolgálóvégpont állapotának frissítenie kell, amint a változásfelsorolási feladat befejeződik.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Hogyan monitorozhatom a szinkronizálás állapotát?
# <a name="portal"></a>[Portál](#tab/portal1)
Az egyes szinkronizálási csoportokon belül leáshat az egyes kiszolgálóvégpontokba az utolsó befejezett szinkronizálási munkamenetek állapotának megtekintéséhez. A zöld állapot oszlop és a fájlok szinkronizálása 0 érték azt jelzi, hogy a szinkronizálás a várt módon működik. Ha nem ez a helyzet, tekintse meg alább a gyakori szinkronizálási hibák listáját és a nem szinkronizált fájlok kezelését. 

![Képernyőkép az Azure Portalról](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Kiszolgáló](#tab/server)
Nyissa meg a kiszolgáló telemetriai naplóit, amelyek az `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`Eseménynaplóban találhatók a . A 9102-es esemény egy befejezett szinkronizálási munkamenetnek felel meg; a szinkronizálás legutóbbi állapotához keresse meg a 9102-es azonosítójú legutóbbi eseményt. SyncDirection jelzi, ha ez a munkamenet volt a feltöltésvagy letöltés. Ha a HResult 0, akkor a szinkronizálási munkamenet sikeres volt. A nem nulla HResult azt jelenti, hogy hiba történt a szinkronizálás során; a gyakori hibák listáját alább talál. Ha a PerItemErrorCount nagyobb, mint 0, az azt jelenti, hogy egyes fájlok vagy mappák szinkronizálása nem volt megfelelő. Lehetséges, hogy a HResult 0, de a PerItemErrorCount, amely nagyobb, mint 0.

Az alábbiakban egy példa a sikeres feltöltésre mutat. A rövidség kedvéért az egyes 9102-es eseményekben szereplő értékek közül csak néhány szerepel az alábbiakban. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Ezzel szemben egy sikertelen feltöltés így nézhet ki:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Előfordulhat, hogy a szinkronizálási munkamenetek összességében sikertelenek, vagy nem nulla PerItemErrorCount-mal rendelkeznek, de továbbra is előrehaladnak, és néhány fájl szinkronizálása sikeresen befejeződött. Ez az Alkalmazott* mezőkben (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes) látható, amely ből megtudhatja, hogy a munkamenet mekkora része sikeres. Ha több szinkronizálási munkamenetet lát egy sorban, amelyek sikertelenek, de egyre növekvő Alkalmazott* számmal rendelkeznek, akkor a támogatási jegy megnyitása előtt időt kell adnia a szinkronizálásnak, hogy újra próbálkozzon.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hogyan monitorozhatom a jelenlegi szinkronizálási munkamenet állapotát?
# <a name="portal"></a>[Portál](#tab/portal1)
A szinkronizálási csoporton belül nyissa meg a kérdéses kiszolgálóvégpontot, és tekintse meg a Szinkronizálási tevékenység szakaszt az aktuális szinkronizálási munkamenetben feltöltött vagy letöltött fájlok számának megtekintéséhez. Vegye figyelembe, hogy ez az állapot körülbelül 5 percet fog késni, és ha a szinkronizálási munkamenet elég kicsi ahhoz, hogy ebben az időszakban befejeződjön, előfordulhat, hogy nem jelentik a portálon. 

# <a name="server"></a>[Kiszolgáló](#tab/server)
Tekintse meg a kiszolgálótelemetriai napló legutóbbi 9302-es eseményét (az Eseménynaplóban nyissa meg az Alkalmazások és szolgáltatások naplóit\Microsoft\FileSync\Agent\Telemetria). Ez az esemény az aktuális szinkronizálási munkamenet állapotát jelzi. A TotalItemCount azt jelzi, hogy hány fájlt kell szinkronizálni, az AppliedItemCount az eddig szinkronizált fájlok számát, a PerItemErrorCount pedig a nem szinkronizálható fájlok számát (lásd alább, hogy hogyan kell kezelni ezt).

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

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Honnan tudhatom, hogy a kiszolgálók szinkronizálva vannak-e egymással?
# <a name="portal"></a>[Portál](#tab/portal1)
Egy adott szinkronizálási csoport minden kiszolgálója esetében győződjön meg akövetkezőkről:
- Az utoljára megkísérelt szinkronizálás időbélyegei mind a feltöltés, mind a letöltés esetében frissek.
- Az állapot zöld mind a feltöltés, mind a letöltés hez.
- A Szinkronizálási tevékenység mezőben nagyon kevés vagy egyáltalán nem található szinkronizálandó fájl.
- A Fájlok szinkronizálása mező 0 a feltöltéshez és a letöltéshez.

# <a name="server"></a>[Kiszolgáló](#tab/server)
Tekintse meg a befejezett szinkronizálási munkameneteket, amelyeket 9102-es események jelölnek meg az `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`egyes kiszolgálók telemetriai eseménynaplójában (az Eseménynaplóban nyissa meg a). 

1. Bármely kiszolgálón győződjön meg arról, hogy a legutóbbi feltöltési és letöltési munkamenetek sikeresen befejeződtek. Ehhez ellenőrizze, hogy a HResult és a PerItemErrorCount 0-os a feltöltéshez és a letöltéshez is (a SyncDirection mező azt jelzi, hogy egy adott munkamenet feltöltési vagy letöltési munkamenet-e). Vegye figyelembe, hogy ha nem lát egy nemrég befejezett szinkronizálási munkamenetet, akkor valószínűleg egy szinkronizálási munkamenet van folyamatban, amely várhatóan nagy mennyiségű adatot ad hozzá vagy módosít.
2. Ha egy kiszolgáló teljesen naprakész a felhővel, és egyik irányban sem változik a szinkronizálás, üres szinkronizálási munkamenetek jelennek meg. Ezeket olyan feltöltési és letöltési események jelzik, amelyekben az összes Sync* mező (SyncFileCount, SyncDirCount, SyncTombstoneCount és SyncSizeBytes) nulla, ami azt jelenti, hogy nem volt mit szinkronizálni. Vegye figyelembe, hogy ezek az üres szinkronizálási munkamenetek nem fordulhatnak elő a nagy lemorzsolódású kiszolgálókon, mivel mindig van valami új szinkronizálni. Ha nincs szinkronizálási tevékenység, akkor 30 percenként kell történnie. 
3. Ha minden kiszolgáló naprakész a felhővel, ami azt jelenti, hogy a legutóbbi feltöltési és letöltési munkamenetek üres szinkronizálási munkamenetek, akkor ésszerű bizonyossággal mondhatja, hogy a rendszer egésze szinkronban van. 
    
Vegye figyelembe, hogy ha közvetlenül az Azure-fájlmegosztásban végzett módosításokat, az Azure File Sync nem észleli ezt a módosítást, amíg a változás felsorolása nem fut, ami 24 óránként egyszer történik. Lehetséges, hogy a kiszolgáló azt fogja mondani, hogy naprakész a felhővel, amikor valójában hiányzik a legutóbbi módosítások közvetlenül az Azure fájlmegosztásban. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hogyan állapíthatom meg, hogy vannak-e olyan fájlok vagy mappák, amelyek szinkronizálása nem történik meg?
Ha a PerItemErrorCount a kiszolgálón vagy a fájlok nem szinkronizálása a portálon nagyobb, mint 0 egy adott szinkronizálási munkamenetben, az azt jelenti, hogy egyes elemek szinkronizálása sikertelen. A fájlok és mappák olyan jellemzőkkel rendelkezhetnek, amelyek megakadályozzák a szinkronizálást. Ezek a jellemzők lehetnek állandóak, és a szinkronizálás folytatásához explicit beavatkozásra van szükség, például a nem támogatott karakterek eltávolítása a fájl- vagy mappanévből. Átmenetiek is lehetnek, ami azt jelenti, hogy a fájl vagy mappa automatikusan folytatja a szinkronizálást; a megnyitott leírókkal rendelkező fájlok például a fájl bezárásakor automatikusan folytatják a szinkronizálást. Amikor az Azure File Sync motor észleli ezt a problémát, egy hibanapló, amely elemezhető az elemek listázásához jelenleg nem megfelelően szinkronizált.

A hibák megtekintéséhez futtassa a **FileSyncErrorsReport.ps1** PowerShell-parancsfájlt (amely az Azure File Sync ügynök ügynöki telepítési könyvtárában található) a megnyitott leírók, nem támogatott karakterek vagy egyéb problémák miatt nem szinkronizált fájlok azonosításához. Az ItemPath mező jelzi a fájl helyét a gyökérszinkronizálási könyvtárhoz képest. A javítási lépéseket lásd az alábbi gyakori szinkronizálási hibák listájában.

> [!Note]  
> Ha a FileSyncErrorsReport.ps1 parancsfájl a következő értéket adja vissza: "Nem található fájlhiba", vagy nem listázi a szinkronizálási csoport ban lévő elemenkénti hibákat, ennek oka a következő:
>
>- 1. ok: Az utoljára befejezett szinkronizálási munkamenetben nem voltak elemenkénti hibák. A portált hamarosan frissíteni kell, hogy megjelenjen a 0 nem szinkronizált fájlok. 
>   - Ellenőrizze a [9102-es eseményazonosítót](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a Telemetriai eseménynaplóban, és ellenőrizze, hogy a PerItemErrorCount 0-os. 
>
>- 2. ok: Az ItemResults eseménynapló a kiszolgálón a túl sok cikkenkénti hiba miatt burkolva van, és az eseménynapló már nem tartalmaz hibákat ehhez a szinkronizálási csoporthoz.
>   - A probléma elkerülése érdekében növelje az ItemResults eseménynapló méretét. Az ItemResults eseménynapló az Eseménynapló "Alkalmazások és szolgáltatások naplói\Microsoft\FileSync\Agent" elemében található. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Fájlonkénti/könyvtárszinkronizálási hibák elhárítása
**ItemResults napló - tételenkénti szinkronizálási hibák**  

| Hresult | HRESULT (decimális) | Hibasztring | Probléma | Kockázatcsökkentés |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | A kiszolgálón lévő rétegzett fájl nem érhető el. Ez a probléma akkor merül fel, amikor a rétegzett fájl visszahívása nem történik meg a kiszolgálóvégpont törlése előtt. | A probléma megoldásához olvassa el a [Rétegzett fájlok nem érhetők el a kiszolgálón a kiszolgálóvégpont törlése után című témakört.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | A fájl- vagy könyvtármódosítás még nem szinkronizálható, mert egy függő mappa még nincs szinkronizálva. Ez az elem szinkronizálódik a függő módosítások szinkronizálása után. | Nincs szükség beavatkozásra. Ha a hiba több napig fennáll, használja a FileSyncErrorsReport.ps1 PowerShell parancsfájlt annak megállapításához, hogy a függő mappa miért van még szinkronizálva. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | A fájl vagy könyvtármódosítása még nem szinkronizálható, mert egy függő mappa még nincs szinkronizálva, és a szinkronizálási munkamenet nem sikerült. Ez az elem szinkronizálódik a függő módosítások szinkronizálása után. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, vizsgálja meg a szinkronizálási munkamenet hibáját. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | A fájl vagy könyvtár neve érvénytelen. | Nevezze át a kérdéses fájlt vagy könyvtárat. További információt a [Nem támogatott karakterek kezelése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) című témakörben talál. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | A fájl vagy könyvtár neve érvénytelen. | Nevezze át a kérdéses fájlt vagy könyvtárat. További információt a [Nem támogatott karakterek kezelése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) című témakörben talál. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | A fájl nem szinkronizálható, mert használatban van. A fájl szinkronizálása akkor megy végbe, amikor már nem lesz használatban. | Nincs szükség beavatkozásra. Az Azure File Sync egy ideiglenes VSS-pillanatképet hoz létre naponta egyszer a kiszolgálón a megnyitott leírókkal tartalmazó fájlok szinkronizálásához. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | A fájl megváltozott, de a módosítást a szinkronizálás még nem észlelte. A módosítás észlelése után a szinkronizálás helyreáll. | Nincs szükség beavatkozásra. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | A fájlt törölték, és a szinkronizálás nem tud a módosításról. | Nincs szükség beavatkozásra. A szinkronizálás leállítja a hiba naplózását, amint a változásészlelés észleli a fájl törlését. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Egy fájl vagy könyvtár törlése nem szinkronizálható, mert az elem már törölve lett a célhelyen, és a szinkronizálás nem tud a változásról. | Nincs szükség beavatkozásra. A szinkronizálás leállítja a hiba naplózását, amint a változásészlelés lefut a célon, és a szinkronizálás észleli az elem törlését. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | A fájl vagy könyvtár kilett hagyva, de a következő szinkronizálási munkamenet során szinkronizálva lesz. Ha ezt a hibát az elem letöltésekor jelenti a rendszer, a fájl vagy könyvtár neve több mint valószínű, hogy érvénytelen. | Nincs szükség műveletre, ha ezt a hibát a fájl feltöltése során jelenti. Ha a fájl letöltésekor a hibát jelenti, nevezze át a kérdéses fájlt vagy könyvtárat. További információt a [Nem támogatott karakterek kezelése](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) című témakörben talál. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | A fájl vagy könyvtár létrehozása nem szinkronizálható, mert az elem már létezik a célban, és a szinkronizálás nem tud a változásról. | Nincs szükség beavatkozásra. A szinkronizálás leállítja a hiba naplózását, amint a változásészlelés lefut a célon, és a szinkronizálás tudatában van az új elemnek. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | A fájl nem szinkronizálható, mert elérte az Azure-fájlmegosztás korlátját. | A probléma megoldásához olvassa el a Hibaelhárítási útmutató Ban található [Azure-fájlmegosztási tárolási korlát](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) szakaszát. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | A fájlt nem támogatott megoldás (például NTFS EFS) titkosítja. | Fejtse vissza a fájlt, és használjon támogatott titkosítási megoldást. A támogatott megoldások listáját a Tervezési útmutató [Titkosítási megoldások](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption) szakaszában találja. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | A fájl egy DFS-R írásvédett replikációs mappában található. | A fájl egy DFS-R írásvédett replikációs mappában található. Az Azure Files Sync nem támogatja az írásvédett DFS-R replikációs mappákban található kiszolgálói végpontokat. További információt a [tervezési útmutatóban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) talál. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | A fájl törlési függőben lévő állapota van. | Nincs szükség beavatkozásra. A fájl törlődik, amint az összes megnyitott fájlleíró bezárult. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | A fájl nem szinkronizálható, mert a tárfiók tűzfal- és virtuális hálózati beállításai engedélyezve vannak, és a kiszolgáló nem fér hozzá a tárfiókhoz. | Adja hozzá a kiszolgáló IP-címét vagy virtuális hálózatát a központi telepítési útmutató [Tűzfal és virtuális hálózati beállítások konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) szakaszában ismertetett lépések végrehajtásával. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | A fájl nem szinkronizálható, mert a biztonsági leíró mérete meghaladja a 64 kiB-korlátot. | A probléma megoldásához távolítsa el a fájlhoz tartozó hozzáférés-vezérlési bejegyzéseket (ACE) a biztonsági leíró méretének csökkentése érdekében. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | A fájl nem szinkronizálható váratlan hiba miatt. | Ha a hiba több napig fennáll, nyisson meg egy támogatási esetet. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | A fájl nem szinkronizálható, mert használatban van. A fájl szinkronizálása akkor megy végbe, amikor már nem lesz használatban. | Nincs szükség beavatkozásra. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | A fájl szinkronizálás közben módosult, ezért újra szinkronizálni kell. | Nincs szükség beavatkozásra. |
| 0x80070017 | -2147024873 | ERROR_CRC | A fájl CRC-hiba miatt nem szinkronizálható. Ez a hiba akkor fordulhat elő, ha egy rétegzett fájlt nem hívtak vissza a kiszolgálóvégpont törlése előtt, vagy ha a fájl sérült. | A probléma megoldásához olvassa el a [Rétegzett fájlok nem érhetők el a kiszolgálón a kiszolgálóvégpont törlése után](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) az árva rétegzett fájlok eltávolításához című témakört. Ha a hiba továbbra is előfordul az oprhaned rétegzett fájlok eltávolítása után, futtassa a [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) fájlt a köteten. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | A fájl nem szinkronizálható, mert elérte az ütközési fájlok maximális számát. Az Azure File Sync fájlonként 100 ütközési fájlt támogat. Ha többet szeretne megtudni a fájlütközésekről, olvassa el az Azure File Sync [– gyakori kérdések .](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution) | A probléma megoldásához csökkentse az ütközési fájlok számát. A fájl akkor szinkronizálódik, ha az ütközési fájlok száma 100-nál kevesebb. |

#### <a name="handling-unsupported-characters"></a>Nem támogatott karakterek kezelése
Ha a **FileSyncErrorsReport.ps1** PowerShell parancsfájl nem támogatott karakterek (0x8007007b vagy 0x80c80255 hibakód) miatti hibákat jelenít meg, távolítsa el vagy nevezze át a megfelelő fájlnevekből hibás karaktereket. A PowerShell valószínűleg kérdőjelként vagy üres téglalapként nyomtatja ki ezeket a karaktereket, mivel a legtöbb ilyen karakter nem rendelkezik szabványos vizuális kódolással. A [kiértékelő eszköz](storage-sync-files-planning.md#evaluation-cmdlet) a nem támogatott karakterek azonosítására használható.

Az alábbi táblázat tartalmazza az összes unicode karakterek Azure File Sync még nem támogatja.

| Karakterkészlet | Karakterek száma |
|---------------|-----------------|
| <ul><li>0x0000009D (operációs rendszer parancs)</li><li>0x00000090 (dcs eszközvezérlő karakterlánc)</li><li>0x0000008F (ss3 egyszeri műszak három)</li><li>0x00000081 (magas oktettkészlet)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri fordított vonalelőtáplálás)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (arab prezentációs űrlapok-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (akciós) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (nem karakter)</li><li>0x0002FFFE - 0x0002FFFF = 2 (nem karakter)</li><li>0x0003FFFE - 0x0003FFFF = 2 (nem karakter)</li><li>0x0004FFFE - 0x0004FFFF = 2 (nem karakter)</li><li>0x0005FFFE - 0x0005FFFF = 2 (nem karakter)</li><li>0x0006FFFE - 0x0006FFFF = 2 (nem karakter)</li><li>0x0007FFFE - 0x0007FFFF = 2 (nem karakter)</li><li>0x0008FFFE - 0x0008FFFF = 2 (nem karakter)</li><li>0x0009FFFE - 0x0009FFFF = 2 (nem karakter)</li><li>0x000AFFFE - 0x000AFFFF = 2 (nem karakter)</li><li>0x000BFFFE - 0x000BfFFFF = 2 (nem karakter)</li><li>0x000CFFFE - 0x000CFFFF = 2 (nem karakter)</li><li>0x000DFFFE - 0x000DFFFF = 2 (nem karakter)</li><li>0x000EFFFE - 0x000EFFFF = 2 (nem definiált)</li><li>0x000FFFFE - 0x000FFFFF = 2 (kiegészítő magánhasználati terület)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Gyakori szinkronizálási hibák
<a id="-2147023673"></a>**A szinkronizálási munkamenet megszakadt.**  

| | |
|-|-|
| **Hresult** | 0x800704c7 |
| **HRESULT (decimális)** | -2147023673 | 
| **Hibasztring** | ERROR_CANCELLED |
| **Szervizelés szükséges** | Nem |

A szinkronizálási munkamenetek különböző okok miatt sikertelenek lehetnek, például a kiszolgáló újraindítása vagy frissítése, vss pillanatképek stb. Bár ez a hiba úgy néz ki, mint ez szükséges nyomon követés, akkor nyugodtan figyelmen kívül hagyja ezt a hibát, kivéve, ha több órán keresztül fennáll.

<a id="-2147012889"></a>**Nem lehetett kapcsolatot létesíteni a szolgáltatással.**    

| | |
|-|-|
| **Hresult** | 0x80072ee7 |
| **HRESULT (decimális)** | -2147012889 | 
| **Hibasztring** | WININET_E_NAME_NOT_RESOLVED |
| **Szervizelés szükséges** | Igen |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**A szolgáltatás szabályozta a felhasználói kérelmet.**  

| | |
|-|-|
| **Hresult** | 0x80c8004c |
| **HRESULT (decimális)** | -2134376372 |
| **Hibasztring** | ECS_E_USER_REQUEST_THROTTLED |
| **Szervizelés szükséges** | Nem |

Nincs szükség beavatkozásra; a kiszolgáló újra próbálkozik. Ha a hiba több órán keresztül fennáll, hozzon létre támogatási kérést.

<a id="-2134364043"></a>**A szinkronizálás blokkolva van, amíg a változásészlelés befejeződik a visszaállítás után**  

| | |
|-|-|
| **Hresult** | 0x80c83075 |
| **HRESULT (decimális)** | -2134364043 |
| **Hibasztring** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Szervizelés szükséges** | Nem |

Semmit nem kell tenni. Ha egy fájl vagy fájlmegosztás (felhővégpont) az Azure Backup használatával visszaáll, a szinkronizálás blokkolva lesz, amíg az Azure-fájlmegosztás változásészlelése be nem fejeződik. A módosításészlelés a visszaállítás befejezése után azonnal fut, és az időtartam a fájlmegosztásban lévő fájlok számán alapul.

<a id="-2147216747"></a>**A szinkronizálás nem sikerült, mert a szinkronizálási adatbázis memóriájának kiürítése történt.**  

| | |
|-|-|
| **Hresult** | 0x80041295 |
| **HRESULT (decimális)** | -2147216747 |
| **Hibasztring** | SYNC_E_METADATA_INVALID_OPERATION |
| **Szervizelés szükséges** | Nem |

Ez a hiba általában akkor fordul elő, amikor egy biztonsági mentési alkalmazás létrehoz egy VSS-pillanatképet, és a szinkronizálási adatbázis el van távolítva. Ha a hiba több órán keresztül fennáll, hozzon létre támogatási kérést.

<a id="-2134364065"></a>**A szinkronizálás nem érhető el a felhővégpontban megadott Azure-fájlmegosztáshoz.**  

| | |
|-|-|
| **Hresult** | 0x80c8305f |
| **HRESULT (decimális)** | -2134364065 |
| **Hibasztring** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert az Azure File Sync-ügynök nem fér hozzá az Azure-fájlmegosztáshoz, amelynek az lehet az oka, hogy az Azure-fájlmegosztás vagy az azt futtató tárfiók már nem létezik. A hiba elhárításához végezze el a következő lépéseket:

1. [Ellenőrizze, hogy létezik-e tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)
3. [Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz.](#troubleshoot-rbac)
4. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**A szinkronizálás nem sikerült, mert a kérelem nem jogosult a művelet végrehajtására.**  

| | |
|-|-|
| **Hresult** | 0x80c86044 |
| **HRESULT (decimális)** | -2134351804 |
| **Hibasztring** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert az Azure File Sync ügynök nem jogosult az Azure-fájlmegosztás elérésére. A hiba elhárításához végezze el a következő lépéseket:

1. [Ellenőrizze, hogy létezik-e tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)
3. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**A használt tárfiók neve nem oldható fel.**  

| | |
|-|-|
| **Hresult** | 0x80C83060 |
| **HRESULT (decimális)** | -2134364064 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Szervizelés szükséges** | Igen |

1. Ellenőrizze, hogy fel tudja-e oldani a tároló DNS-nevét a kiszolgálóról.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Ellenőrizze, hogy létezik-e tárfiók.](#troubleshoot-storage-account)
3. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ismeretlen hiba történt a tárfiók elérése közben.**  

| | |
|-|-|
| **Hresult** | 0x80c8308a |
| **HRESULT (decimális)** | -2134364022 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Szervizelés szükséges** | Igen |

1. [Ellenőrizze, hogy létezik-e tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**A szinkronizálás nem sikerült, mert a tárfiók zárolva van.**  

| | |
|-|-|
| **Hresult** | 0x80c83092 |
| **HRESULT (decimális)** | -2134364014 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a tárfiók írásvédett [erőforrászárolással rendelkezik.](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) A probléma megoldásához távolítsa el a tárfiók írásvédett erőforrászárolását. 

<a id="-1906441138"></a>**A szinkronizálás nem sikerült, mert probléma van a szinkronizálási adatbázissal.**  

| | |
|-|-|
| **Hresult** | 0x8e5e044e |
| **HRESULT (decimális)** | -1906441138 |
| **Hibasztring** | JET_errWriteConflict |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure File Sync által használt belső adatbázis sal kapcsolatos probléma lép fel. Ha ez a probléma jelentkezik, hozzon létre egy támogatási kérelmet, és felvesszük Önnel a kapcsolatot a probléma megoldása érdekében.

<a id="-2134364053"></a>**A kiszolgálón telepített Azure File Sync ügynök verziója nem támogatott.**  

| | |
|-|-|
| **Hresult** | 0x80C8306B |
| **HRESULT (decimális)** | -2134364053 |
| **Hibasztring** | ECS_E_AGENT_VERSION_BLOCKED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure File Sync-ügynök kiszolgálóra telepített verziója nem támogatott. A probléma megoldásához [frissítsen]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) egy [támogatott ügynökverzióra.]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)

<a id="-2134351810"></a>**Elérte az Azure fájlmegosztási tárolási korlátot.**  

| | |
|-|-|
| **Hresult** | 0x80c8603e |
| **HRESULT (decimális)** | -2134351810 |
| **Hibasztring** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |

Ez a hiba az Azure-fájlmegosztások tárterületi korlátjának elérésekor következik be, ami akkor fordulhat elő, ha az Azure-fájlmegosztásra vonatkozó kvóta van érvényben, vagy ha az Azure-fájlmegosztás használata túllépi a korlátokat. További információt az [Azure-fájlmegosztás jelenlegi korlátaiban](storage-files-scale-targets.md)talál.

1. Keresse meg a szinkronizálási csoportot a Storage Sync szolgáltatáson belül.
2. Válassza ki a felhővégpontot a szinkronizálási csoporton belül.
3. Jegyezze fel az Azure fájlmegosztás nevét a megnyitott ablaktáblában.
4. Válassza ki a társított tárfiókot. Ha ez a kapcsolat sikertelen, a hivatkozott tárfiók el lett távolítva.

    ![A felhővégpont részletes ablaktábláját megjelenítő képernyőkép a tárfiókra mutató hivatkozással.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. A **fájlmegosztások** listájának megtekintéséhez válassza a Fájlok lehetőséget.
6. Kattintson a három pont a sor végén az Azure fájlmegosztás által hivatkozott felhővégpont.
7. Ellenőrizze, hogy a **Használat** értéke a **Kvóta** alatt van-e. Vegye figyelembe, hogy ha nincs megadva egy alternatív kvóta, a kvóta megegyezik [az Azure-fájlmegosztás maximális méretével.](storage-files-scale-targets.md)

    ![Az Azure-fájlmegosztási tulajdonságok képernyőképe.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Ha a megosztás megtelt, és nincs megadva kvóta, a probléma megoldásának egyik lehetséges módja, ha az aktuális kiszolgálói végpont összes almappáját saját szinkronizálási csoportba tartozó, önálló kiszolgálói végponttá alakítja. Ezáltal minden almappa külön Azure-fájlmegosztással fog szinkronizálni.

<a id="-2134351824"></a>**Az Azure-fájlmegosztás nem található.**  

| | |
|-|-|
| **Hresult** | 0x80c86030 |
| **HRESULT (decimális)** | -2134351824 |
| **Hibasztring** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztás nem érhető el. Hibaelhárítás:

1. [Ellenőrizze, hogy létezik-e tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)

Ha az Azure-fájlmegosztás törölve lett, létre kell hoznia egy új fájlmegosztást, majd újra létre kell hoznia a szinkronizálási csoportot. 

<a id="-2134364042"></a>**A szinkronizálás szünetel, amíg ez az Azure-előfizetés felfüggesztésre kerül.**  

| | |
|-|-|
| **Hresult** | 0x80C83076 |
| **HRESULT (decimális)** | -2134364042 |
| **Hibasztring** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure-előfizetés fel van függesztve. A szinkronizálás újból engedélyezve lesz, ha az Azure-előfizetés visszaáll. További információért olvassa [el A miért van letiltva az Azure-előfizetésem, és hogyan aktiválhatom újra?](../../cost-management-billing/manage/subscription-disabled.md)

<a id="-2134364052"></a>**A tárfiók tűzfallal vagy virtuális hálózatokkal rendelkezik.**  

| | |
|-|-|
| **Hresult** | 0x80c8306c |
| **HRESULT (decimális)** | -2134364052 |
| **Hibasztring** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztás nem érhető el egy tárfiók tűzfala miatt, vagy amiatt, hogy a tárfiók egy virtuális hálózathoz tartozik. Ellenőrizze, hogy a tárfiók tűzfal- és virtuálishálózati beállításai megfelelően vannak-e konfigurálva. További információt a [Tűzfal és a virtuális hálózati beállítások konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)című témakörben talál. 

<a id="-2134375911"></a>**A szinkronizálás nem sikerült, mert probléma van a szinkronizálási adatbázissal.**  

| | |
|-|-|
| **Hresult** | 0x80c80219 |
| **HRESULT (decimális)** | -2134375911 |
| **Hibasztring** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Szervizelés szükséges** | Nem |

Ez a hiba általában magától megoldódik, és a következő esetekben fordulhat elő:

* A szinkronizálási csoport kiszolgálói között nagy számú fájlmódosítás történik.
* Számos hiba az egyes fájlokat és könyvtárakat.

Ha ez a hiba néhány óránál hosszabb ideig fennáll, hozzon létre egy támogatási kérelmet, és felvesszük Önnel a kapcsolatot a probléma megoldásában.

<a id="-2146762487"></a>**A kiszolgáló nem tudott biztonságos kapcsolatot létesíteni. A felhőszolgáltatás nem várt tanúsítványt kapott.**  

| | |
|-|-|
| **Hresult** | 0x800b0109 |
| **HRESULT (decimális)** | -2146762487 |
| **Hibasztring** | CERT_E_UNTRUSTEDROOT |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordulhat elő, ha a vállalat SSL-leállítási proxyt használ, illetve ha egy kártevő entitás elfogja a kiszolgáló és az Azure File Sync szolgáltatás közötti adatforgalmat. Ha biztos abban, hogy ez a várt működés (mivel a vállalat SSL-leállítási proxyt használ), hagyja ki a tanúsítvány-ellenőrzést a beállításjegyzék felülbírálásával.

1. Hozza létre a SkipVerifyingPinnedRootCertificate rendszerleíró értéket.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Indítsa újra a szinkronizálási szolgáltatást a regisztrált kiszolgálón.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ha beállítja ezt a beállításazonosítót, az Azure File Sync-ügynök minden helyileg megbízhatónak minősülő SSL-tanúsítványt elfogad a kiszolgáló és a felhőszolgáltatás közötti adatátvitel során.

<a id="-2147012894"></a>**Nem lehetett kapcsolatot létesíteni a szolgáltatással.**  

| | |
|-|-|
| **Hresult** | 0x80072ee2 |
| **HRESULT (decimális)** | -2147012894 |
| **Hibasztring** | WININET_E_TIMEOUT |
| **Szervizelés szükséges** | Igen |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**A szinkronizálás nem sikerült a hitelesítéssel kapcsolatos probléma miatt.**  

| | |
|-|-|
| **Hresult** | 0x80c80300 |
| **HRESULT (decimális)** | -2134375680 |
| **Hibasztring** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Szervizelés szükséges** | Igen |

Ez a hiba általában azért fordul elő, mert a kiszolgáló ideje helytelen. Ha a kiszolgáló virtuális gépen fut, ellenőrizze, hogy az állomáson megfelelő-e az idő.

<a id="-2134364040"></a>**A szinkronizálás a tanúsítvány lejárata miatt nem sikerült.**  

| | |
|-|-|
| **Hresult** | 0x80c83078 |
| **HRESULT (decimális)** | -2134364040 |
| **Hibasztring** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor következik be, ha a hitelesítéshez használt tanúsítvány lejárt.

A tanúsítvány lejártának ellenőrzéséhez hajtsa végre a következő lépéseket:  
1. Nyissa meg a Certificates MMC beépülő modult, válassza a Számítógépfiók lehetőséget, és keresse meg a Tanúsítványok (helyi számítógép)\Személyes\Tanúsítványok mappát.
2. Ellenőrizze, hogy az ügyfél hitelesítési tanúsítványa lejárt-e.

Ha az ügyfél-hitelesítési tanúsítvány lejárt, a probléma megoldásához végezze el az alábbi lépéseket:

1. Ellenőrizze, hogy telepítve van-e az Azure File Sync agent 4.0.1.0-s vagy újabb verziója.
2. Futtassa az alábbi PowerShell-parancsot a kiszolgálón: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**A szinkronizálás nem sikerült, mert a hitelesítési tanúsítvány nem található.**  

| | |
|-|-|
| **Hresult** | 0x80c80228 |
| **HRESULT (decimális)** | -2134375896 |
| **Hibasztring** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor következik be, ha a hitelesítéshez használt tanúsítvány nem található.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Ellenőrizze, hogy telepítve van-e az Azure File Sync agent 4.0.1.0-s vagy újabb verziója.
2. Futtassa az alábbi PowerShell-parancsot a kiszolgálón: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**A szinkronizálás nem sikerült, mert a hitelesítési identitás nem található.**  

| | |
|-|-|
| **Hresult** | 0x80c83079 |
| **HRESULT (decimális)** | -2134364039 |
| **Hibasztring** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert a kiszolgálói végpont törlése sikertelen volt, és a végpont részben törölt állapotban van. A probléma megoldásához próbálja meg ismét törölni a kiszolgálói végpontot.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Az a kötet, amelyen a kiszolgálóvégpont található, kevés a lemezterület.**  

| | |
|-|-|
| **Hresult** | 0x8e5e0211 |
| **HRESULT (decimális)** | -1906441711 |
| **Hibasztring** | JET_errLogDiskFull |
| **Szervizelés szükséges** | Igen |
| | |
| **Hresult** | 0x80c8031a |
| **HRESULT (decimális)** | -2134375654 |
| **Hibasztring** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor következik be, ha a kötet megtelt. Ez a hiba általában azért fordul elő, mert kiszolgálóvégponton kívüli fájlok foglalnak el lemezterületet a köteten. Szabadítson fel helyet a köteten további kiszolgálói végpontok hozzáadásával, fájlok másik kötetre való áthelyezésével vagy a kiszolgálóvégpont által befüggő kötet méretének növelésével.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**A szolgáltatás még nem áll készen a kiszolgálóvégpontkal való szinkronizálásra.**  

| | |
|-|-|
| **Hresult** | 0x80c8300f |
| **HRESULT (decimális)** | -2134364145 |
| **Hibasztring** | ECS_E_REPLICA_NOT_READY |
| **Szervizelés szükséges** | Nem |

Ez a hiba azért fordul elő, mert a felhővégpont az Azure-fájlmegosztáson már meglévő tartalommal jött létre. Az Azure File Sync kell átkozni az Azure fájlmegosztás az összes tartalom, mielőtt a kiszolgáló végpont, hogy folytassa a kezdeti szinkronizálás.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**A szinkronizálás számos egyes fájl miatt nem sikerült.**  

| | |
|-|-|
| **Hresult** | 0x80c8023b |
| **HRESULT (decimális)** | -2134375877 |
| **Hibasztring** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |
| | |
| **Hresult** | 0x80c8021c |
| **HRESULT (decimális)** | -2134375908 |
| **Hibasztring** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Szervizelés szükséges** | Igen |
| | |
| **Hresult** | 0x80c80253 |
| **HRESULT (decimális)** | -2134375853 |
| **Hibasztring** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Szervizelés szükséges** | Igen |

Azokban az esetekben, ahol fájlonként sok szinkronizálási hiba van, a szinkronizálási munkamenetek sikertelenek lehetnek. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Az Azure File Sync egy ideiglenes VSS-pillanatképet hoz létre naponta egyszer a kiszolgálón a megnyitott leírókkal tartalmazó fájlok szinkronizálásához.

<a id="-2134376423"></a>**A szinkronizálás nem sikerült, mert probléma van a kiszolgáló végpontjának elérési útvonalával.**  

| | |
|-|-|
| **Hresult** | 0x80c80019 |
| **HRESULT (decimális)** | -2134376423 |
| **Hibasztring** | ECS_E_SYNC_INVALID_PATH |
| **Szervizelés szükséges** | Igen |

Győződjön meg arról, hogy az elérési út létezik, egy helyi NTFS-köteten található, és nem újraelemzési pont vagy meglévő kiszolgálóvégpont.

<a id="-2134375817"></a>**A szinkronizálás nem sikerült, mert a szűrőillesztő verziója nem kompatibilis az ügynök verziójával**  

| | |
|-|-|
| **Hresult** | 0x80C80277 |
| **HRESULT (decimális)** | -2134375817 |
| **Hibasztring** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha a felhőbeli rétegzési szűrő illesztőprogramjának (StorageSync.sys) betöltött verziója nem kompatibilis a társzinkronizálási ügynök (FileSyncSvc) szolgáltatással. Ha az Azure File Sync-ügynök frissítve lett, indítsa újra a kiszolgálót a telepítés befejezéséhez. Ha a hiba továbbra is fennáll, távolítsa el az ügynököt, indítsa újra a kiszolgálót, majd telepítse újra az Azure File Sync-ügynököt.

<a id="-2134376373"></a>**A szolgáltatás jelenleg nem érhető el.**  

| | |
|-|-|
| **Hresult** | 0x80c8004b |
| **HRESULT (decimális)** | -2134376373 |
| **Hibasztring** | ECS_E_SERVICE_UNAVAILABLE |
| **Szervizelés szükséges** | Nem |

Ez a hiba azért következik be, mert az Azure File Sync szolgáltatás nem érhető el. Ez a hiba automatikusan megoldódik, ha az Azure File Sync szolgáltatás ismét elérhetővé válik.

<a id="-2146233088"></a>**A szinkronizálás kivétel miatt nem sikerült.**  

| | |
|-|-|
| **Hresult** | 0x80131500 |
| **HRESULT (decimális)** | -2146233088 |
| **Hibasztring** | COR_E_EXCEPTION |
| **Szervizelés szükséges** | Nem |

Ez a hiba akkor fordul elő, ha a szinkronizálás kivétel miatt meghiúsult. Ha a hiba több órán keresztül megmarad, hozzon létre egy támogatási kérelmet.

<a id="-2134364045"></a>**A szinkronizálás nem sikerült, mert a tárfiók feladatátvételt kapott egy másik régióba.**  

| | |
|-|-|
| **Hresult** | 0x80c83073 |
| **HRESULT (decimális)** | -2134364045 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért következik be, mert a tárfiók átadta a feladatait egy másik régiónak. Az Azure File Sync nem támogatja a tárfiókok feladatátvételi funkcióját. A Azure File Syncben felhőbeli végpontként használt Azure-fájlmegosztásokat tartalmazó tárfiókokon nem lehet feladatátvételt végezni. Feladatátvétel esetén a szinkronizálás leáll, és az újonnan rétegzett fájlok esetében váratlan adatvesztést is okozhat. A probléma megoldásához helyezze a tárfiókot az elsődleges régióba.

<a id="-2134375922"></a>**A szinkronizálás nem sikerült, mert átmeneti probléma van a szinkronizálási adatbázissal.**  

| | |
|-|-|
| **Hresult** | 0x80c8020e |
| **HRESULT (decimális)** | -2134375922 |
| **Hibasztring** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Szervizelés szükséges** | Nem |

Ez a hiba a szinkronizálási adatbázis belső hibája miatt következik be. A szinkronizálási újrapróbálkozáskor ez a hiba automatikusan megoldódik. Ha a hiba hosszabb ideig fennáll, hozzon létre egy támogatási kérelmet, és felvesszük Önnel a kapcsolatot a probléma megoldásában.

<a id="-2134364024"></a>**A szinkronizálás nem sikerült az Azure Active Directory-bérlő ben bekövetkező változás miatt**  

| | |
|-|-|
| **Hresult** | 0x80c83088 |
| **HRESULT (decimális)** | -2134364024 | 
| **Hibasztring** | ECS_E_INVALID_AAD_TENANT |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert az Azure File Sync jelenleg nem támogatja az előfizetés egy másik Azure Active Directory-bérlőbe való áthelyezését.
 
A probléma az alábbi lehetőségek egyikével oldható meg:

- **1. lehetőség (ajánlott)**: Az előfizetés áthelyezése vissza az eredeti Azure Active Directory-bérlőre
- **2. lehetőség:** Az aktuális szinkronizálási csoport törlése és újbóli létrehozása. Ha a felhőbeli rétegzés engedélyezve volt a kiszolgálóvégponton, törölje a szinkronizálási csoportot, majd végezze el a [Felhőbeli rétegzés szakaszban]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) leírt lépéseket az elárvult rétegezett fájlok eltávolításához a szinkronizálási csoportok újbóli létrehozása előtt. 

<a id="-2134364010"></a>**A szinkronizálás nem sikerült, mert a tűzfal és a virtuális hálózati kivétel nincs konfigurálva**  

| | |
|-|-|
| **Hresult** | 0x80c83096 |
| **HRESULT (decimális)** | -2134364010 | 
| **Hibasztring** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor fordul elő, ha a tűzfal és a virtuális hálózati beállítások engedélyezve vannak a tárfiókban, és a "Megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a tárfiókhoz" kivétel nincs bejelölve. A probléma megoldásához kövesse az üzembehelyezési útmutató [Tűzfal- és virtuális hálózati beállítások konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) című szakaszában szereplő lépéseket.

<a id="-2147024891"></a>**A szinkronizálás nem sikerült, mert a Rendszerkötet adatai mappában lévő engedélyek helytelenek.**  

| | |
|-|-|
| **Hresult** | 0x80070005 |
| **HRESULT (decimális)** | -2147024891 |
| **Hibasztring** | ERROR_ACCESS_DENIED |
| **Szervizelés szükséges** | Igen |

Ez a hiba akkor következhet be, ha az NT AUTHORITY\SYSTEM fiók nem rendelkezik engedélyekkel a rendszerkötet-információkat tartalmazó mappához a kiszolgálóvégpont kötetén. Ne feledje, hogy ha az egyes fájlok nem szinkronizálódnak ERROR_ACCESS_DENIED, hajtsa végre a [Fájl/könyvtár szinkronizálási hibáinak elhárítása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors) című szakaszban ismertetett lépéseket.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Töltse le a [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) eszközt.
2. A következő parancs futtatása emelt szintű parancssorból a parancssor elindításához a rendszerfiók használatával: **PsExec.exe -i -s -d cmd** 
3. A rendszerfiók alatt futó parancssorból futtassa a következő parancsot annak megerősítéséhez, hogy az NT AUTHORITY\SYSTEM fióknak nincs hozzáférése a **cacls "meghajtóbetűjel:\system volume information" /T /C** rendszerkötet-információkat tartalmazó mappához.
4. Ha az NT AUTHORITY\SYSTEM fiók nem rendelkezik hozzáféréssel a rendszerkötet-információkat tartalmazó mappához, futtassa a következő parancsot: **cacls "meghajtóbetűjel:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Ha a 4. lépés a hozzáférés megtagadása miatt meghiúsul, futtassa a következő parancsot a rendszerkötet-információkat tartalmazó mappa tulajdonjogának átvételéhez, majd ismételje meg a 4. lépést: **takeown /A /R /F "meghajtóbetűjel:\System Volume Information"**

<a id="-2134375810"></a>**A szinkronizálás nem sikerült, mert az Azure-fájlmegosztást törölték, és újra létrehozták.**  

| | |
|-|-|
| **Hresult** | 0x80c8027e |
| **HRESULT (decimális)** | -2134375810 |
| **Hibasztring** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert az Azure File Sync nem támogatja az Azure-fájlmegosztások törlését és újbóli létrehozását ugyanazon szinkronizálási csoporton belül. 

A probléma megoldásához törölje és hozza újból létre a szinkronizálási csoportot a következő lépések elvégzésével:

1. Törölje a szinkronizálási csoport összes kiszolgálóvégpontját.
2. Törölje a felhővégpontot. 
3. Törölje a szinkronizálási csoportot.
4. Ha a felhőrétegezés engedélyezve volt egy kiszolgálói végponton, törölje az árva rétegzett fájlokat a kiszolgálón a rétegzett fájlokban dokumentált lépések végrehajtásával [nem érhetők el a kiszolgálón a kiszolgálóvégponti](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) szakasz törlése után.
5. Hozza létre újra a szinkronizálási csoportot.

<a id="-2145844941"></a>**A szinkronizálás nem sikerült, mert a HTTP-kérés át lett irányítva**  

| | |
|-|-|
| **Hresult** | 0x80190133 |
| **HRESULT (decimális)** | -2145844941 |
| **Hibasztring** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Szervizelés szükséges** | Igen |

Ez a hiba azért fordul elő, mert az Azure File Sync nem támogatja a HTTP átirányítást (3xx állapotkód). A probléma megoldásához tiltsa le a HTTP-átirányítást a proxykiszolgálón vagy a hálózati eszközön.

<a id="-2134364027"></a>**Időtúltöltés történt az offline adatátvitel során, de még folyamatban van.**  

| | |
|-|-|
| **Hresult** | 0x80c83085 |
| **HRESULT (decimális)** | -2134364027 |
| **Hibasztring** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Szervizelés szükséges** | Nem |

Ez a hiba akkor fordul elő, ha egy adatbetöltési művelet meghaladja az időtúlértéket. Ez a hiba figyelmen kívül hagyható, ha a szinkronizálás folyamatban van (az AppliedItemCount nagyobb, mint 0). Lásd: [Hogyan figyelhetem az aktuális szinkronizálási munkamenet előrehaladását?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

### <a name="common-troubleshooting-steps"></a>Gyakori hibaelhárítási lépések
<a id="troubleshoot-storage-account"></a>**Ellenőrizze, hogy létezik-e tárfiók.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Keresse meg a szinkronizálási csoportot a Storage Sync szolgáltatáson belül.
2. Válassza ki a felhővégpontot a szinkronizálási csoporton belül.
3. Jegyezze fel az Azure fájlmegosztás nevét a megnyitott ablaktáblában.
4. Válassza ki a társított tárfiókot. Ha ez a kapcsolat sikertelen, a hivatkozott tárfiók el lett távolítva.
    ![A felhővégpont részletes ablaktábláját megjelenítő képernyőkép a tárfiókra mutató hivatkozással.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

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

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Kattintson a bal oldali tartalomjegyzék **Áttekintés gombjára** a fő tárfiók lapra való visszatéréshez.
2. A **fájlmegosztások** listájának megtekintéséhez válassza a Fájlok lehetőséget.
3. Ellenőrizze, hogy a felhővégpont által hivatkozott fájlmegosztás megjelenik-e a fájlmegosztások listájában (ezt a fenti 1. lépésben meg kellett volna jegyeznie).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Kattintson a bal oldali tartalomjegyzék **hozzáférés-vezérlési (IAM)** elemére.
1. Kattintson a **Szerepkör-hozzárendelések** fülre a tárfiókhoz hozzáféréssel rendelkező felhasználók és alkalmazások (*egyszerű szolgáltatás)* listájához.
1. Ellenőrizze, hogy a **Microsoft.StorageSync** vagy **a Hybrid File Sync Service** (régi alkalmazásnév) megjelenik-e a listában a Reader és az **Adatelérési** szerepkörrel. 

    ![A tárfiók hozzáférés-vezérléslapján található hibrid fájlszinkronizálási szolgáltatásegyszerű szolgáltatásának képernyőképe](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Ha a **Microsoft.StorageSync** vagy **hibrid fájlszinkronizálási szolgáltatás** nem jelenik meg a listában, hajtsa végre az alábbi lépéseket:

    - Kattintson a **Hozzáadás** gombra.
    - A **Szerepkör mezőben** válassza az **Olvasó és az adatelérés lehetőséget.**
    - A **Kijelölés** mezőbe írja be a **Microsoft.StorageSync**parancsot, jelölje ki a szerepkört, és kattintson a **Mentés gombra.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hogyan akadályozhatom meg, hogy a felhasználók nem támogatott karaktereket tartalmazó fájlokat hozzanak létre a kiszolgálón?
[A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlképernyőivel](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) letilthatja, hogy a nevükben nem támogatott karakterekkel rendelkező fájlok létrejönjenek a kiszolgálón. Előfordulhat, hogy ezt a PowerShell használatával kell megtennie, mivel a nem támogatott karakterek többsége nem nyomtatható, ezért először a hexadecimális ábrázolásokat kell karakterként leadnia.

Először hozzon létre egy FSRM-fájlcsoportot a [New-FsrmFileGroup parancsmag](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup)használatával. Ez a példa azt határozza meg, hogy a csoport csak két nem támogatott karaktert tartalmazzon, de a szükséges számú karaktert felveheti a fájlcsoportba.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Miután definiáltegy FSRM-fájlcsoportot, az Új-FsrmFileScreen parancsmag használatával létrehozhat egy FSRM-fájlképernyőt.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Vegye figyelembe, hogy a fájlképernyők csak az Azure File Sync által nem támogatott karakterek létrehozásának letiltására használható. Ha más esetekben fájlképernyőket használ, a szinkronizálás folyamatosan megpróbálja letölteni a fájlokat az Azure-fájlmegosztásból a kiszolgálóra, és a fájlképernyő miatt le lesz tiltva, ami magas adatforgalomhoz vezet. 

## <a name="cloud-tiering"></a>Felhőbeli rétegzés 
A felhőrétegezés ben a hibákhoz két elérési út létezik:

- A fájlok nem rétegezhetők, ami azt jelenti, hogy az Azure File Sync sikertelenül kísérel meg egy fájlt rétegezni az Azure Files-ba.
- A fájlok visszahívása sikertelen lehet, ami azt jelenti, hogy az Azure File Sync fájlrendszerszűrő (StorageSync.sys) nem tudja letölteni az adatokat, amikor a felhasználó megpróbál hozzáférni egy rétegzett fájlhoz.

A hibáknak két fő osztálya van, amelyek bármelyik hibaútvonalon keresztül történhetnek:

- Felhőbeli tárolási hibák
    - *Átmeneti tárolási szolgáltatás rendelkezésre állási problémák.* További információt az [Azure Storage szolgáltatásiszint-szerződésében (SLA) talál.](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)
    - *Nem elérhető Azure-fájlmegosztás*. Ez a hiba általában akkor fordul elő, ha törli az Azure-fájlmegosztást, ha az még mindig egy szinkronizálási csoport felhővégpontja.
    - *Nem érhető el a tárfiók.* Ez a hiba általában akkor fordul elő, ha törli a tárfiókot, miközben továbbra is rendelkezik egy Azure-fájlmegosztás, amely egy felhővégpont egy szinkronizálási csoportban. 
- Kiszolgálóhibák 
  - *Az Azure File Sync fájlrendszerszűrő (StorageSync.sys) nincs betöltve.* A rétegezési/visszahívási kérelmek megválaszolásához az Azure File Sync fájlrendszer szűrőbe kell tölteni. A be nem töltődő szűrő több okból is előfordulhat, de a leggyakoribb ok az, hogy a rendszergazda manuálisan távolította el. Az Azure File Sync fájlrendszer szűrő kell betölteni mindenkor az Azure File Sync megfelelő működéséhez.
  - *Hiányzó, sérült vagy más módon megtört újraelemzési pont*. Az újraelemzési pont egy fájl speciális adatszerkezete, amely két részből áll:
    1. Egy újraelemzési címke, amely azt jelzi az operációs rendszer számára, hogy az Azure File Sync fájlrendszer szűrő (StorageSync.sys) előfordulhat, hogy bizonyos műveletet az IO a fájlhoz. 
    2. Újraelemzési adatok, amely azt jelzi, hogy a fájlrendszer szűrő a fájl URI-ját a társított felhővégpont (az Azure-fájlmegosztás). 
        
       Az újraelemzési pont sérülésének leggyakoribb módja az, ha a rendszergazda megpróbálja módosítani a címkét vagy annak adatait. 
  - *Hálózati kapcsolati problémák*. A fájl rétegezéséhez vagy visszahívásához a kiszolgálónak internetkapcsolattal kell rendelkeznie.

A következő szakaszok bemutatják, hogyan háríthatók el a felhőrétegezési problémák, és állapítsa meg, hogy a probléma felhőalapú tárolási vagy kiszolgálói probléma-e.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>A kiszolgáló rétegzési tevékenységének monitorozása  
A kiszolgálón végzett rétegezési tevékenység figyeléséhez használja a 9003-as, 9016-os és 9029-es eseményazonosítójú eseményt a Telemetriai eseménynaplóban (az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent az Eseménynaplóban).

- A 9003-as eseményazonosító hibaeloszlást biztosít a kiszolgáló végpontja számára. Például összes hibaszám, Hibakód stb. Ne feledje, hogy hibakódonként egy eseményt naplóznak.
- A 9016-os eseményesemény szellemkép-jelentéssel jár egy kötethez. Például, Szabad hely százaléka van, Fájlok száma ghosted a munkamenetben, Fájlok száma nem szellem, stb
- A 9029-es eseményazonosító szellemkép-munkamenet-információkat biztosít a kiszolgáló végpontjának. Például a munkamenetben megkísérelt fájlok száma, a munkamenetben rétegzett fájlok száma, a már rétegzett fájlok száma stb.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>A kiszolgáló előhívási tevékenységének monitorozása
A kiszolgálón végzett visszahívási tevékenység figyeléséhez használja a 9005-ös, 9006-os, 9009-es és 9059-es eseményazonosítójú eseményt a Telemetriai eseménynaplóban (az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent az Eseménynaplóban).

- A 9005-ös eseményazonosító biztosítja a kiszolgálóvégpont visszahívási megbízhatóságát. Például, Összes en unique files kitört, Összes egyedi fájlokat -val meghibásodott belépés, stb.
- A 9006-os eseményazonosító visszahívási hibaeloszlást biztosít a kiszolgáló végpontja számára. Például összes sikertelen kérelem, Hibakód stb. Ne feledje, hogy hibakódonként egy eseményt naplóznak.
- A 9009-es eseményazonosító a kiszolgáló végpontjának visszahívási munkamenet-információit tartalmazza. Például DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed stb.
- A 9059-es eseményazonosító alkalmazás-visszahívási terjesztést biztosít a kiszolgáló végpontja számára. Például ShareId, Alkalmazásnév és TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>A rétegezést elmulasztó fájlok hibaelhárítása
Ha a fájlok nem réteg az Azure Files:

1. Az Eseménynaplóban tekintse át az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában található telemetriai, üzemeltetési és diagnosztikai eseménynaplókat. 
   1. Ellenőrizze, hogy a fájlok léteznek-e az Azure-fájlmegosztásban.

      > [!NOTE]
      > A fájlokat szinkronizálni kell egy Azure-fájlmegosztással, mielőtt rétegezhető lenne.

   2. Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolattal. 
   3. Ellenőrizze, hogy futnak-e az Azure File Sync szűrőillesztőprogramok (StorageSync.sys és StorageSyncGuard.sys):
       - A rendszergazda jogú `fltmc`parancssorból futtassa a parancsot. Ellenőrizze, hogy a StorageSync.sys és a StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok szerepelnek-e a listában.

> [!NOTE]
> A 9003-as azonosítójú eseményt óránként egyszer naplózza a rendszer a Telemetriai eseménynaplóban, ha egy fájl nem tud rétegezést (hibakódonként egy eseményt naplóznak). Ellenőrizze a [Rétegezési hibák és a szervizelési](#tiering-errors-and-remediation) szakaszban, hogy a hibakód ban szerepelnek-e javítási lépések.

### <a name="tiering-errors-and-remediation"></a>Rétegezési hibák és szervizelés

| Hresult | HRESULT (decimális) | Hibasztring | Probléma | Kockázatcsökkentés |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | A fájl nem tudott rétegezést, mert használatban van. | Nincs szükség beavatkozásra. A fájl rétegzett lesz, ha már nincs használatban. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | A fájl nem tudott rétegezést, mert a szinkronizálás kizárta. | Nincs szükség beavatkozásra. A szinkronizálási kizárási listában szereplő fájlok nem rétegezhetők. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | A fájl nem tudott rétegezést, mert nem található a kiszolgálón. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze, hogy a fájl létezik-e a kiszolgálón. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | A fájl nem tudott rétegezést, mert törölték az Azure-fájlmegosztásban. | Nincs szükség beavatkozásra. A következő letöltési szinkronizálási munkamenet futtatásakor a fájlt törölni kell a kiszolgálón. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | A fájl nem tudott rétegezést egy hálózati probléma miatt. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze a hálózati kapcsolatot az Azure-fájlmegosztáshoz. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | A fájl nem tudott rétegezést egy hálózati probléma miatt. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze a hálózati kapcsolatot az Azure-fájlmegosztáshoz. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | A fájl nem tudott rétegezést elérni a hozzáférés megtagadva hiba miatt. Ez a hiba akkor fordulhat elő, ha a fájl egy DFS-R írásvédett replikációs mappában található. | Az Azure Files Sync nem támogatja az írásvédett DFS-R replikációs mappákban található kiszolgálói végpontokat. További információt a [tervezési útmutatóban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) talál. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | A fájl nem tudott rétegezést egy hálózati probléma miatt. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze a hálózati kapcsolatot az Azure-fájlmegosztáshoz. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | A fájl nem tudott rétegezést, mert a fájl mérete kisebb, mint a támogatott méret. | Ha az ügynök verziója kisebb, mint 9.0, a minimálisan támogatott fájlméret 64kb. Ha az ügynök verziója 9.0 és újabb, a minimálistámogatott fájlméret a fájlrendszer fürtméretétől függ (dupla fájlrendszer-fürtméret). Ha például a fájlrendszer fürtjének mérete 4 kb, a minimális fájlméret 8 kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | A fájl nem tudott rétegezést egy Azure-tárolási probléma miatt. | Ha a hiba továbbra is fennáll, nyisson meg egy támogatási kérelmet. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | A fájl nem tudott rétegezést, mert egyszerre hívták vissza. | Nincs szükség beavatkozásra. A rendszer rétegezi a fájlt, amikor a visszahívás befejeződik, és a fájl már nincs használatban. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | A fájl nem tudott rétegezést, mert nem szinkronizált az Azure-fájlmegosztással. | Nincs szükség beavatkozásra. A fájl réteg, miután szinkronizálta az Azure-fájlmegosztást. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | A fájl nem tudott rétegezést, mert a felhőrétegezésszűrő-illesztőprogram (storagesync.sys) nem fut. | A probléma megoldásához nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot:`fltmc load storagesync`<br>Ha a storagesync szűrőillesztőprogram nem töltődik be a fltmc parancs futtatásakor, távolítsa el az Azure File Sync ügynököt, indítsa újra a kiszolgálót, és telepítse újra az Azure File Sync ügynököt. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | A fájl nem tudott rétegezést, mert nincs elég lemezterület azon a köteten, amelyen a kiszolgálóvégpont található. | A probléma megoldásához szabadítson fel legalább 100 MB lemezterületet azon a köteten, amelyen a kiszolgáló végpontja található. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | A fájl nem tudott rétegezést, mert nem szinkronizált az Azure-fájlmegosztással. | Nincs szükség beavatkozásra. A fájl réteg, miután szinkronizálta az Azure-fájlmegosztást. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | A fájl nem tudott rétegezést, mert nem támogatott újraelemzési pont. | Ha a fájl egy adatdeduplikációs újraelemzési pont, kövesse a [tervezési útmutató](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication) lépéseit az adatdeduplikációs támogatás engedélyezéséhez. Az adatdeduplikációtól eltérő újraelemzési pontokkal rendelkező fájlok nem támogatottak, és nem lesznek rétegzve.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | A fájl nem tudott rétegezést, mert módosult. | Nincs szükség beavatkozásra. A fájl réteg, ha a módosított fájl szinkronizálása az Azure-fájlmegosztás. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | A fájl nem tudott rétegezést, mert nem szinkronizált az Azure-fájlmegosztással. | Nincs szükség beavatkozásra. A fájl réteg, miután szinkronizálta az Azure-fájlmegosztást. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | A fájl nem tudott rétegezést egy hálózati probléma miatt. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze a hálózati kapcsolatot az Azure-fájlmegosztáshoz. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | A fájl nem tudott rétegezést, mert módosult. | Nincs szükség beavatkozásra. A fájl réteg, ha a módosított fájl szinkronizálása az Azure-fájlmegosztás. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | A fájl nem tudott rétegezést, mert nem volt elegendő a rendszererőforrás. | Ha a hiba továbbra is fennáll, vizsgálja meg, hogy melyik alkalmazás vagy kernel módú illesztőprogram meríti ki a rendszer erőforrásait. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>A nem visszahívott fájlok hibaelhárítása  
Ha a fájlokat nem sikerül visszahívni:
1. Az Eseménynaplóban tekintse át az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában található telemetriai, üzemeltetési és diagnosztikai eseménynaplókat.
    1. Ellenőrizze, hogy a fájlok léteznek-e az Azure-fájlmegosztásban.
    2. Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolattal. 
    3. Nyissa meg a Szolgáltatások MMC beépülő modult, és ellenőrizze, hogy fut-e a Storage Sync Agent szolgáltatás (FileSyncSvc).
    4. Ellenőrizze, hogy futnak-e az Azure File Sync szűrőillesztőprogramok (StorageSync.sys és StorageSyncGuard.sys):
        - A rendszergazda jogú `fltmc`parancssorból futtassa a parancsot. Ellenőrizze, hogy a StorageSync.sys és a StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok szerepelnek-e a listában.

> [!NOTE]
> A 9006-os azonosítójú eseményt óránként egyszer naplózza a rendszer a Telemetriai eseménynaplóban, ha egy fájl visszahívása sikertelen (hibakódonként egy esemény kerül naplózásra). Ellenőrizze a [Visszahívási hibák és a szervizelés](#recall-errors-and-remediation) szakaszt, hogy a hibakódban szerepel-e javítási lépés.

### <a name="recall-errors-and-remediation"></a>Visszahívási hibák és szervizelés

| Hresult | HRESULT (decimális) | Hibasztring | Probléma | Kockázatcsökkentés |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | A fájl i/o-időkiásmiatt nem tudott visszahívni. A probléma több okból is fordulhat elő: kiszolgálói erőforrás-megkötések, gyenge hálózati kapcsolat vagy Azure-tárolási probléma (például szabályozás). | Nincs szükség beavatkozásra. Ha a hiba több órán keresztül fennáll, kérjük, nyisson meg egy támogatási esetet. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | A fájl visszahívása hálózati probléma miatt nem sikerült.  | Ha a hiba továbbra is fennáll, ellenőrizze a hálózati kapcsolatot az Azure-fájlmegosztáshoz. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | A fájl visszahívása nem sikerült, mert a kiszolgáló végpontját törölték. | A probléma megoldásához olvassa el a [Rétegzett fájlok nem érhetők el a kiszolgálón a kiszolgálóvégpont törlése után című témakört.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | A fájl visszahívása nem sikerült, mert a hozzáférés megtagadva hiba történt. Ez a probléma akkor fordulhat elő, ha a tárfiók tűzfal- és virtuálishálózati beállításai engedélyezve vannak, és a kiszolgáló nem fér hozzá a tárfiókhoz. | A probléma megoldásához adja hozzá a kiszolgáló IP-címét vagy virtuális hálózatát a telepítési útmutató [Tűzfal és virtuális hálózati beállítások konfigurálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) szakaszában ismertetett lépések végrehajtásával. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | A fájl nem sikerült visszahívni, mert nem érhető el az Azure-fájlmegosztásban. | A probléma megoldásához ellenőrizze, hogy a fájl létezik-e az Azure-fájlmegosztásban. Ha a fájl megtalálható az Azure-fájlmegosztásban, frissítsen a legújabb Azure File Sync [ügynökverzióra.](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions) |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | A fájl visszahívása a tárfiók engedélyezési hibája miatt nem sikerült. | A probléma megoldásához ellenőrizze, hogy [az Azure File Sync hozzáfér-e a tárfiókhoz.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac) |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | A fájl nem tudta visszahívni, mert az Azure fájlmegosztás nem érhető el. | Ellenőrizze, hogy létezik-e fájlmegosztás, és elérhető-e. Ha a fájlmegosztást törölték, és újra létrehozták, hajtsa végre a szinkronizálás idúrában dokumentált [lépéseket, mert az Azure-fájlmegosztást törölték, és újra létrehozták](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) a szakaszt a szinkronizálási csoport törléséhez és újbóli létrehozásához. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | A fájl visszahívása nem sikerült, mert nem volt elegendő a rendszererőforrás. | Ha a hiba továbbra is fennáll, vizsgálja meg, hogy melyik alkalmazás vagy kernel módú illesztőprogram meríti ki a rendszer erőforrásait. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | A fájl nem tudott visszahívni, mert nem sikerült memória. | Ha a hiba továbbra is fennáll, vizsgálja meg, hogy melyik alkalmazás vagy kernel módú illesztőprogram okozza a kevés memóriát. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | A fájl visszahívása nem sikerült, mert nincs elegendő lemezterület. | A probléma megoldásához szabadítson fel helyet a köteten a fájlok másik kötetre való áthelyezésével, növelje a kötet méretét, vagy kényszerítse a fájlokat rétegre az Invoke-StorageSyncCloudTiering parancsmag használatával. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>A rétegzett fájlokat nem lehet elérni a kiszolgálón a kiszolgáló végpontjának törlését követően
A kiszolgálón lévő rétegzett fájlok elérhetetlenné válnak, ha a kiszolgálóvégpont törlése előtt nem hívják vissza a fájlokat.

A naplózott hibák, ha a rétegzett fájlok nem érhetők el
- Fájl szinkronizálásakor a program az ItemResults eseménynaplóban a -2147942467 (0x80070043 – ERROR_BAD_NET_NAME) hibakódot naplózza.
- Fájl visszahívásakor a -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) hibakód kerül a RecallResults eseménynaplóba

A rétegzett fájlokhoz való hozzáférés visszaállítása a következő feltételek teljesülése esetén lehetséges:
- A kiszolgálóvégpont az elmúlt 30 napban lett törölve
- A felhőbeli végpont nem lett törölve 
- A fájlmegosztás nem lett törölve
- A szinkronizálási csoport nem lett törölve

Ha a fenti feltételek teljesülnek, visszaállíthatja a kiszolgálón található fájlokhoz való hozzáférést, ha 30 napon belül ismételten létrehozza a kiszolgálón a kiszolgálóvégpontot ugyanazzal az útvonallal. 

Ha a fenti feltételek nem teljesülnek, a hozzáférés visszaállítása nem lehetséges, mert a kiszolgálón található rétegzett fájlok már elárvultak. Az árva rétegzett fájlok eltávolításához kövesse az alábbi utasításokat.

**Megjegyzések**
- Ha a rétegzett fájlok nem érhetők el a kiszolgálón, a teljes fájl továbbra is elérhető, ha közvetlenül éri el az Azure-fájlmegosztást.
- Az árva rétegzett fájlok jövőbeni megakadályozásához kövesse a Kiszolgálóvégpont eltávolítása a [kiszolgálóvégpont törlésekor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) című részben ismertetett lépéseket.

<a id="get-orphaned"></a>**Az árva rétegzett fájlok listájának beszerezése** 

1. Ellenőrizze, hogy telepítve van-e az Azure File Sync ügynök verziója az 5.1-es vagy újabb verzióval.
2. Az árva rétegzett fájlok listázásához futtassa az alábbi PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Mentse az OrphanTieredFiles.txt kimeneti fájlt arra az esetre, ha a fájlokat a törlésük után vissza kell állítani a biztonsági mentésből.

<a id="remove-orphaned"></a>**Árva rétegzett fájlok eltávolítása** 

*1. lehetőség: Az árva rétegzett fájlok törlése*

Ez a beállítás törli az árva rétegzett fájlokat a Windows Server kiszolgálón, de el kell távolítani a kiszolgálóvégpontot, ha 30 nap után rekreáció miatt létezik, vagy egy másik szinkronizálási csoporthoz csatlakozik. Fájlütközések akkor fordulnak elő, ha a kiszolgáló végpontjának újbóli létrehozása előtt a Windows Server vagy az Azure fájlmegosztáson frissítik a fájlokat.

1. Ellenőrizze, hogy telepítve van-e az Azure File Sync ügynök verziója az 5.1-es vagy újabb verzióval.
2. Készítsen biztonsági másolatot az Azure-fájlmegosztásról és a kiszolgálóvégpont helyéről.
3. Távolítsa el a kiszolgálóvégpontot a szinkronizálási csoportból (ha van ilyen) a [Kiszolgálóvégpont eltávolítása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)című részben ismertetett lépések végrehajtásával.

> [!Warning]  
> Ha a kiszolgálóvégpontot nem távolítja el az Remove-StorageSyncOrphanedTieredFiles parancsmag használata előtt, az árva rétegzett fájl törlése a kiszolgálón törli a teljes fájlt az Azure-fájlmegosztásban. 

4. Az árva rétegzett fájlok listázásához futtassa az alábbi PowerShell-parancsokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Mentse az OrphanTieredFiles.txt kimeneti fájlt arra az esetre, ha a fájlokat a törlésük után vissza kell állítani a biztonsági mentésből.
6. Az árva rétegzett fájlok törléséhez futtassa a következő PowerShell-parancsokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Megjegyzések** 
- A kiszolgálón módosított, az Azure-fájlmegosztással nem szinkronizált rétegzett fájlok törlődnek.
- A (nem árva) többszintű fájlok nem törlődnek.
- A nem rétegzett fájlok a kiszolgálón maradnak.

7. Nem kötelező: Hozza létre újra a kiszolgálóvégpontot, ha a 3.

*2. lehetőség: Csatlakoztassa az Azure-fájlmegosztást, és másolja a kiszolgálón árván maradt fájlokat helyileg*

Ez a beállítás nem igényli a kiszolgálóvégpont eltávolítását, de elegendő lemezterületet igényel a teljes fájlok helyi másolásához.

1. [Csatlakoztassa](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) az Azure-fájlmegosztást az árva rétegzett fájlokat tartalmazó Windows Server kiszolgálón.
2. Az árva rétegzett fájlok listázásához futtassa az alábbi PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Az OrphanTieredFiles.txt kimeneti fájl segítségével azonosíthatja az árva rétegzett fájlokat a kiszolgálón.
4. Írja felül az árva rétegzett fájlokat az Azure-fájlmegosztás teljes fájljának windows Serverbe másolásával.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>A kiszolgálón váratlanul visszahívott fájlok hibaelhárítása  
A nagy számú fájlt olvasó víruskereső, biztonsági mentés és egyéb alkalmazások nem kívánt visszahívásokat okoznak, kivéve, ha tiszteletben tartják az offline módi attribútumot, és kihagyják a fájlok tartalmának olvasását. Az offline fájlok kihagyását támogató termékek esetében a kihagyás segíthet elkerülni a nem kívánt visszahívásokat a víruskeresések, a biztonsági mentési feladatok és a hasonló műveletek során.

Érdeklődjön a szoftverszállítónál, hogy megtudja, hogyan konfigurálhatja a megoldást az offline fájlok olvasásának kihagyására.

Más esetekben is előfordulhatnak nem kívánt visszahívások, például amikor fájlokat böngész a Fájlkezelőben. Ha a kiszolgálón található Fájlkezelőben felhőalapú rétegzett fájlokat tartalmazó mappákat nyit meg, az nem kívánt visszahívásokat eredményezhet. Ez még gyakrabban előfordul, ha engedélyezve van egy víruskereső megoldás a kiszolgálón.

> [!NOTE]
>A Telemetriai eseménynaplóban a 9059-es eseményazonosító val határozhatja meg, hogy melyik alkalmazás(ok) okozza a visszahívásokat. Ez az esemény biztosítja az alkalmazások visszahívásának terjesztését egy kiszolgálóvégponthoz, és óránként egyszer kerül naplózásra.

## <a name="general-troubleshooting"></a>Általános hibaelhárítás
Ha problémákat tapasztal az Azure File Sync szolgáltatással egy kiszolgálón, kezdje a következő lépésekkel:
1. Az Eseménynaplóban tekintse át a telemetriai, működési és diagnosztikai eseménynaplókat.
    - A szinkronizálási, rétegezési és visszahívási problémák az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában kerülnek naplózásra a telemetriai, diagnosztikai és üzemeltetési eseménynaplókban.
    - A kiszolgáló kezelésével kapcsolatos problémákat (például a konfigurációs beállításokat) az Alkalmazások és szolgáltatások\Microsoft\FileSync\Management mappában az operatív és diagnosztikai eseménynaplók naplózzák.
2. Ellenőrizze, hogy az Azure File Sync szolgáltatás fut-e a kiszolgálón:
    - Nyissa meg a Szolgáltatások MMC beépülő modult, és ellenőrizze, hogy fut-e a Storage Sync Agent szolgáltatás (FileSyncSvc).
3. Ellenőrizze, hogy futnak-e az Azure File Sync szűrőillesztőprogramok (StorageSync.sys és StorageSyncGuard.sys):
    - A rendszergazda jogú `fltmc`parancssorból futtassa a parancsot. Ellenőrizze, hogy a StorageSync.sys és a StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok szerepelnek-e a listában.

Ha a probléma nem oldódott meg, futtassa az AFSDiag eszközt:
1. Hozzon létre egy könyvtárat, ahová az AFSDiag kimenet mentésre kerül (például C:\Kimenet).
    > [!NOTE]
    >Az AFSDiag a naplók gyűjtése előtt törli a kimeneti könyvtár összes tartalmát. Adjon meg egy olyan kimeneti helyet, amely nem tartalmaz adatokat.
2. Nyisson meg egy emelt szintű PowerShell-ablakot, majd futtassa a következő parancsokat (minden parancs után nyomja le az Enter billentyűt):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Az Azure File Sync kernel mód nyomkövetési szint, írja be **az 1** (eltérő rendelkezés hiányában, hogy hozzon létre részletesebb nyomkövetéseket), majd nyomja le az Enter billentyűt.
4. Az Azure File Sync felhasználói mód nyomkövetési szintjéhez írja be **az 1** értéket (eltérő rendelkezés hiányában további részletes nyomkövetések létrehozásához), majd nyomja le az Enter billentyűt.
5. Reprodukálja a problémát. Ha végzett, írja be a **D**.
6. A napló- és nyomkövetési fájlokat tartalmazó .zip fájl a megadott kimeneti könyvtárba kerül.

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
- [Az Azure Files gyakori kérdései](storage-files-faq.md)
- [Azure Files-problémák hibaelhárítása Windowson](storage-troubleshoot-windows-file-connection-problems.md)
- [Az Azure Files hibáinak elhárítása Linux alatt](storage-troubleshoot-linux-file-connection-problems.md)
