---
title: "Azure fájlszinkronizálás (előzetes verzió) hibaelhárítása |} Microsoft Docs"
description: "Az Azure fájlszinkronizálás kapcsolatos gyakori hibák elhárítása."
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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 506781ac83e75d558badbd3a8842533e314a8dfa
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Hibaelhárítás az Azure fájlszinkronizálás (előzetes verzió)
Sync szolgáltatás használatával Azure fájl (előzetes verzió) központosítása fájlmegosztások a szervezet Azure fájlokban, ugyanakkor változatlanul megőrizze a rugalmasság, a teljesítmény és a kompatibilitási egy helyszíni fájlkiszolgáló. Azure fájlszinkronizálás átalakítja a Windows Server az Azure fájlmegosztás gyors gyorsítótárába. Minden protokoll, amely a Windows Server helyileg, az adatok eléréséhez használhatja, többek között a ftps-t, SMB és NFS. Akkor is annyi gyorsítótárak világszerte szükség szerint.

Ez a cikk célja, és esetleg előforduló Azure fájlszinkronizálás telepítés megoldását. Azt is ismertetik fontos naplóinak gyűjtése a rendszer, ha a probléma mélyebb vizsgálatra szükség. Ha nem látja a választ a kérdésére, lépjen kapcsolatba velünk (a növekvő sorrendben) a következő csatornákon keresztül:

1. Ez a cikk megjegyzéseket tartalmazó részét.
2. [Az Azure Storage fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure-fájlokat UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft támogatási szolgálatához. Az Azure portálon, egy új támogatási kérelem létrehozásához a **súgó** lapon jelölje be a **súgó + támogatás** gombra, és válassza **új támogatja a kérelem**.

## <a name="agent-installation-and-server-registration"></a>Az ügynök telepítése és a kiszolgáló regisztrálása
<a id="agent-installation-failures"></a>**Ügynök telepítési hibáinak elhárítása**  
Ha az Azure fájlszinkronizálás telepítése meghiúsul, egy rendszergazda jogú parancssorba a következő paranccsal bekapcsolása naplózás ügynök telepítése közben:

```
StorageSyncAgent.msi /l*v Installer.log
```

Tekintse át a telepítési hiba okának megállapításához installer.log. 

> [!Note]  
> Az ügynök telepítése sikertelen lesz, ha a számítógép be van állítva a Microsoft Update segítségével, és nem fut a Windows Update szolgáltatás.

<a id="agent-installation-on-DC"></a>**Az ügynök telepítése sikertelen lesz, az Active Directory tartományvezérlőn lévő** próbálja, és a sync-ügynök telepítése egy Active Directory tartományvezérlőn, ahol a PDC-szerepkör tulajdonosával van-e a Windows Server 2008R2 operációsrendszer-verzió alatt, ha a probléma esetleg kattint ahol a szinkronizálás ügynök telepítése sikertelen lesz.

Oldja meg, az elsődleges tartományvezérlő szerepkör átvitele egy másik tartomány a tartományvezérlő futó Windows Server 2012 R2 rendszerben vagy újabb, majd szinkronizáló telepítése.

<a id="agent-installation-websitename-failure"></a>**Az ügynök telepítése sikertelen, és ez a hiba: "Tároló szinkronizálási ügynök varázsló megszakadt"**  
A probléma akkor fordulhat elő, ha az IIS-webhely alapértelmezett neve megváltozott. A probléma megoldásához nevezze át az IIS alapértelmezett webhelye mint a "Default Web Site", majd próbálja megismételni a telepítést. A problémát egy jövőbeli frissítéssel, az ügynök javítja. 

<a id="server-registration-missing"></a>**Kiszolgáló nem szerepel a regisztrált kiszolgálókat, az Azure-portálon**  
Ha a kiszolgáló nem szerepel a **regisztrálva kiszolgálók** egy tárolási szinkronizálási szolgáltatás:
1. Jelentkezzen be a regisztrálni kívánt kiszolgálóra.
2. Nyissa meg a Fájlkezelőt, és keresse meg a tároló szinkronizálási Ügynöktelepítési könyvtár (az alapértelmezett hely: C:\Program Files\Azure\StorageSyncAgent). 
3. Futtassa a ServerRegistration.exe, és fejezze be a varázslót a tároló szinkronizálási szolgáltatás regisztrálni a kiszolgálót.



<a id="server-already-registered"></a>**Kiszolgáló regisztrálása Azure fájl Sync-ügynök telepítése közben a következő üzenet jelenik meg: "a kiszolgáló már regisztrálva van"** 

![A "kiszolgáló már regisztrálva van" hiba miatt a kiszolgáló regisztrálása párbeszédpanel képernyőfelvételének üzenet](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ez az üzenet jelenik meg, ha a kiszolgáló korábban regisztrálva volt a tároló szinkronizálási szolgáltatás. Szüntesse meg a kiszolgáló és az aktuális tároló szinkronizálási szolgáltatás, és regisztrálja újra a számítógépet egy új tároló szinkronizálási szolgáltatással, végezze el a leírt lépéseket [a kiszolgáló regisztrációját az Azure fájlszinkronizálás](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Ha a kiszolgáló nem szerepel a **regisztrálva kiszolgálók** a tároló szinkronizálási szolgáltatás a kiszolgálón, amelyet szeretne regisztrációját, futtassa a következő PowerShell-parancsokat:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Ha a kiszolgáló egy fürt része, akkor használhatja az opcionális *alaphelyzetbe állítása-StorageSyncServer - CleanClusterRegistration* paraméter is a fürt regisztrációjának eltávolítására.

<a id="web-site-not-trusted"></a>**A kiszolgáló regisztrálásához a számos "nem megbízható web site" válaszok jelenik meg. Hogy miért?**  
A probléma akkor fordul elő, amikor a **Internet Explorer fokozott biztonsági** házirend engedélyezve van a kiszolgáló regisztrálása során. Megfelelően letiltásáról további információt a **Internet Explorer fokozott biztonsági** házirend, lásd: [készítse elő a Windows Server használata Azure fájlszinkronizálás](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) és [Azure fájl központi telepítése Szinkronizálási (előzetes verzió)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Szinkronizálási csoportok kezelése
<a id="cloud-endpoint-using-share"></a>**Felhő-végpont létrehozása sikertelen, hiba: "A megadott Azure fájlmegosztás már használatban van egy másik CloudEndpoint"**  
Ez a probléma akkor fordul elő, ha Azure fájlmegosztás már használja egy másik felhőben végpont. 

Ha ez az üzenet megjelenik, és Azure fájlmegosztás jelenleg nincs használatban a felhőbeli végpont, végezze el a Azure fájlszinkronizálás metaadatokat az Azure fájlmegosztás jelet az alábbi lépéseket:

> [!Warning]  
> Az Azure fájlmegosztások, amely jelenleg a felhővégpontnak használja a metaadatokat törli azt eredményezi, Azure fájlszinkronizálás műveleteit. 

1. Az Azure portálon lépjen az Azure-fájlmegosztáshoz.  
2. Kattintson a jobb gombbal az Azure-fájlmegosztáshoz, és válassza ki **metaadatok szerkesztése**.
3. Kattintson a jobb gombbal **SyncService**, majd válassza ki **törlése**.

<a id="cloud-endpoint-authfailed"></a>**Felhő-végpont létrehozása sikertelen, hiba: "AuthorizationFailed"**  
Ez a probléma akkor fordul elő, ha a felhasználói fiók nem rendelkezik elegendő jogosultsága a felhővégpontnak létrehozásához. 

Felhő-végpont létrehozása, a felhasználói fiókot a következő Microsoft Authorization engedélyekkel kell rendelkeznie:  
* Olvassa el: A szerepkör-definíció beolvasása
* Írási: Létrehozása, vagy egyéni szerepkör-definíció frissítése
* Olvassa el: A szerepkör-hozzárendelést kapnak
* Írási: A szerepkör-hozzárendelés létrehozása

A következő beépített szerepkörök rendelkezik a szükséges Microsoft Authorization engedélyekkel:  
* Tulajdonos
* Felhasználói hozzáférés adminisztrátora annak meghatározásához, hogy a felhasználói fiók szerepkör rendelkezik-e a szükséges engedélyekkel:  
1. Válassza ki az Azure-portálon **erőforráscsoportok**.
2. Az erőforráscsoport, ahol a tárfiók, majd válassza ki és **hozzáférés-vezérlés (IAM)**.
3. Válassza ki a **szerepkör** (például a tulajdonos vagy közreműködő) a felhasználói fiókjához.
4. Az a **erőforrás-szolgáltató** listáról válassza ki **Microsoft Authorization**. 
    * **Szerepkör-hozzárendelés** kell **olvasási** és **írási** engedélyek.
    * **Szerepkör-definíció** kell **olvasási** és **írási** engedélyek.

<a id="server-endpoint-createjobfailed"></a>**Kiszolgáló-végpont létrehozása sikertelen, hiba: "MgmtServerJobFailed" (hibakód:-2134375898)**                                                                                                                    
Ez a probléma akkor fordul elő, ha a kiszolgáló végpont elérési útja a rendszerkötet és a felhő rétegezéséhez engedélyezve van. Felhő rétegezéséhez nem támogatott a rendszerköteten. Kiszolgáló-végpont létrehozása a rendszerköteten, tiltsa le a felhő rétegezéséhez, amikor a kiszolgáló-végpont létrehozása.

<a id="server-endpoint-deletejobexpired"></a>**Kiszolgáló-végpont törlése sikertelen, hiba: "MgmtServerJobExpired"**                
Ez a probléma akkor fordul elő, ha a kiszolgáló offline állapotban, vagy nem rendelkezik hálózati kapcsolattal. Ha a kiszolgáló már nem érhető el, akkor szüntesse meg a kiszolgáló a portálon, amely törli a kiszolgáló végpontok. A kiszolgáló végpontok törléséhez kövesse a ismertetett [a kiszolgáló regisztrációját az Azure fájlszinkronizálás](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Nem sikerült server endpoint Tulajdonságok lapjának megnyitásához, vagy frissíteni a felhő rétegzési házirendet**

A probléma akkor fordulhat elő, ha a kiszolgáló végpont egy ügynökfelügyeleti művelet sikertelen. Ha a kiszolgáló végpont tulajdonságai lap nem nyitható meg az Azure-portálon, a kiszolgáló PowerShell-parancsok használatával server végpont frissítése lehet, hogy probléma elhárításához. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint -SubscriptionId mysubguid -ResourceGroupName myrgname -StorageSyncServiceName storagesvcname -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint -Id serverendpointid -CloudTiering true -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Ha egy fájl létrehozott közvetlenül a saját Azure fájlmegosztások SMB protokollon keresztül vagy a portálon keresztül, mennyi ideig tart a fájl szinkronizálása a szinkronizálási csoport kiszolgálójára?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Egy kiszolgálón szinkronizálása sikertelen**  
Ha a szinkronizálás nem sikerül, a kiszolgálón:
1. Győződjön meg arról, hogy létezik-e a kiszolgáló végpont a szinkronizálni kívánt az Azure fájlmegosztások kívánt címtár az Azure portálon:
    
    ![Egy felhőszolgáltatás végpont és a kiszolgáló végpont az Azure portálon is szinkronizálási csoport képernyőképe](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. Az Eseménynaplóban tekintse át a működési és diagnosztikai eseménynaplóit, alkalmazások és Services\Microsoft\FileSync\Agent alatt.
    1. Győződjön meg arról, hogy a kiszolgáló rendelkezik-e internetkapcsolat.
    2. Győződjön meg arról, hogy az Azure fájl szinkronizálási szolgáltatás fut-e a kiszolgálón. Ehhez nyissa meg a szolgáltatások beépülő MMC-modulban, és ellenőrizze, hogy fut-e a tárolási szinkronizálási ügynök szolgáltatás (FileSyncSvc).

<a id="replica-not-ready"></a>**Szinkronizálás sikertelen, hiba: "0x80c8300f - a replika nem áll készen a szükséges művelet"**  
A probléma várható, ha egy felhő-végpont létrehozása és az Azure fájlmegosztások használja, amely adatokat tartalmaz. A módosítás észlelési feladat befejezése után (akár 24 órát vehet igénybe) Azure fájlmegosztás futó Szinkronizáló kell kezdődnie, megfelelően működik-e.


    > [!NOTE]
    > Azure File Sync periodically takes VSS snapshots to sync files that have open handles.

Jelenleg nem támogatjuk erőforrás áthelyezése egy másik előfizetés vagy, áthelyezése egy másik Azure ad bérlői.  Ha az előfizetés helyezi át a különböző bérlők, Azure fájlmegosztás tulajdonjog változásáról alapján szolgáltatás elérhetetlenné válik. A bérlő módosul, ha szüksége lesz a kiszolgáló végpontok és a felhőbeli végpont törlése (lásd: a szinkronizálási csoportok kezelése szakasz utasításokat újra használható Azure fájlmegosztás tisztítása) és a szinkronizálási csoport újbóli létrehozása.

<a id="doesnt-have-enough-free-space"></a>**Ez a számítógép nem rendelkezik elegendő szabad helyének hibája**  
Ha a portál állapotát jeleníti meg a "Számítógép nem rendelkezik elegendő szabad lemezterület" a probléma oka az lehet, hogy kevesebb mint 1 GB szabad lemezterület a köteten marad.  Például ha egy 1,5 GB kötet, szinkronizálási csak tudnak .5GB használatára, ha a probléma kattint bontsa ki a kiszolgáló végpont használja a kötet méretét.

## <a name="cloud-tiering"></a>Felhőbeli rétegzés 
Nincsenek hibák, a felhőben két elérési utak rétegezéséhez:

- Fájlok sikertelen lehet a réteg, ami azt jelenti, hogy Azure fájlszinkronizálás sikertelenül próbálja réteg egy fájlt az Azure Fileshoz.
- Fájlok meghiúsulhatnak visszaírási, ami azt jelenti, hogy az Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) nem sikerül letölteni az adatokat, amikor egy felhasználó kísérlet az adott próbál hozzáférni egy fájlhoz, amely rendelkezik lett rétegzett.

Nincsenek hibák, amely akkor fordulhat elő, vagy hiba elérési útján két fő osztályok:

- Felhőalapú tárolási hibák
    - *Átmeneti tárolási szolgáltatások elérhetőségi problémáinak*. Lásd: [szolgáltatási szint szerződés (SLA) az Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) további információt.
    - *Nem érhető el az Azure fájlmegosztás*. Ez a hiba általában akkor fordul elő, törlésekor az Azure fájlmegosztások esetén továbbra is a felhővégpontnak szinkronizálási csoportban.
    - *Nem érhető el a tárfiók*. Ez a hiba általában akkor fordul elő, amikor törölni a tárfiókot, amíg még az Azure fájlmegosztások, amely a felhővégpontnak szinkronizálási csoportban. 
- Kiszolgálóhibák 
    - *Nincs betöltve az Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys)*. Ahhoz, hogy a kérelmek rétegezéséhez/visszaírási válaszol, az Azure fájlszinkronizálás fájlrendszerszűrő kell betölteni. Nincs betöltve a szűrő több okból fordulhat elő, de a leggyakoribb oka, hogy a rendszergazda a memóriából azt manuálisan. Az Azure fájlszinkronizálás fájlrendszerszűrő kell betölteni minden alkalommal Azure fájlszinkronizálás megfelelően működjön.
    - *Hiányzik, sérült vagy más módon sérült újraelemzési pont*. Újraelemzési pont olyan speciális adatstruktúra egy fájl, amely két részből áll:
        1. Újraelemzési címke, amely megadja, hogy az operációs rendszer, hogy az Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) el néhány művelet IO a fájl esetleg. 
        2. Újraelemzési adatokat, amelyek határozza meg, hogy a fájlrendszer szűrőjének URI-azonosítója a felhőbeli végpont (Azure fájlmegosztás) fájl. 
        
        A leggyakoribb módja egy újraelemzési pont megsérülhetnek, ha a rendszergazda módosíthatja a címke vagy a hozzá kapcsolódó adatokat megkísérli. 
    - *A hálózati csatlakozási problémák*. Ahhoz, hogy a réteg, vagy a fájl visszahívásának, a kiszolgáló internetkapcsolattal kell rendelkeznie.

A következő szakaszok felhő rétegezési problémák elhárításához, és megállapítja, hogy probléma egy felhőalapú tárolási hiba vagy probléma a kiszolgáló jelzi.

<a id="files-fail-tiering"></a>**Fájlok, amelyek nem réteg hibaelhárítása**  
Ha a fájlokat nem sikerült az Azure Fileshoz réteg:

1. Győződjön meg arról, hogy a fájlok az Azure fájlmegosztás léteznek-e.

    > [!NOTE]
    > A fájl kell szinkronizálva az Azure fájlmegosztások előtt is helyezhető.
2. Az Eseménynaplóban tekintse át a működési és diagnosztikai eseménynaplóit, alkalmazások és Services\Microsoft\FileSync\Agent alatt.
    1. Győződjön meg arról, hogy a kiszolgáló rendelkezik-e internetkapcsolat. 
    2. Győződjön meg arról, hogy fut-e a Azure fájlszinkronizálás fájlrendszerszűrő-illesztőprogramok (StorageSync.sys és StorageSyncGuard.sys):
        - Egy rendszergazda jogú parancssorban futtassa `fltmc`. Győződjön meg arról, hogy látható-e a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok.

<a id="files-fail-recall"></a>**Fájlok, amelyek nem hívhatók hibaelhárítása**  
Ha a fájlok nem hívhatók vissza:
1. Az Eseménynaplóban tekintse át a működési és diagnosztikai eseménynaplóit, alkalmazások és Services\Microsoft\FileSync\Agent alatt.
    1. Győződjön meg arról, hogy a fájlok az Azure fájlmegosztás léteznek-e.
    2. Győződjön meg arról, hogy a kiszolgáló rendelkezik-e internetkapcsolat. 
    3. Győződjön meg arról, hogy fut-e a Azure fájlszinkronizálás fájlrendszerszűrő-illesztőprogramok (StorageSync.sys és StorageSyncGuard.sys):
        - Egy rendszergazda jogú parancssorban futtassa `fltmc`. Győződjön meg arról, hogy látható-e a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok.

<a id="files-unexpectedly-recalled"></a>**A kiszolgáló váratlanul visszaírt fájlok hibaelhárítása**  
Víruskereső, biztonsági mentés és más alkalmazásokat, amelyek nagy számú fájl olvasása miatt nem kívánt visszahívások, kivéve, ha a Kihagyás offline attribútum tiszteletben, és hagyja ki ezeket a fájlokat tartalmának olvasása. Ez a beállítás segít támogató termékek kapcsolat nélküli fájlok kihagyása műveletek, például víruskereső vizsgálat vagy a biztonsági mentési feladatok során ne nem kívánt visszahívások.

Vegye fel a kapcsolatot a szoftver gyártójához annak megismerése, hogyan konfigurálhatja a kapcsolat nélküli fájlok olvasásának kihagyását megoldás.

Nem kívánt visszahívások is akkor fordulhat elő, más esetekben, például a Fájlkezelőben fájlok böngészésekor. Egy mappa, amely felhő rétegű a Fájlkezelőben a kiszolgálón lévő fájlok megnyitása a nem kívánt visszahívások eredményezheti. Ez általában akkor is, ha egy víruskereső megoldás engedélyezve van a kiszolgálón.

## <a name="general-troubleshooting"></a>Általános hibaelhárítási
Ha hibát tapasztal Azure fájl szinkronizálási kiszolgálóra, indítsa el az alábbi lépések végrehajtásával:
1. Az Eseménynaplóban tekintse át a működési és diagnosztikai eseménynaplóit.
    - A diagnosztikai és működési eseménynaplóit Applications and Services\Microsoft\FileSync\Agent szinkronizálási rétegezéséhez és visszaírási problémák jelentkezett be.
    - A kiszolgáló (például a konfigurációs beállítások) felügyeletével kapcsolatos problémákat a működési és diagnosztikai eseménynaplók Applications and Services\Microsoft\FileSync\Management jelentkezett be.
2. Győződjön meg arról, hogy az Azure fájl szinkronizálási szolgáltatás fut-e a kiszolgálón:
    - Nyissa meg a szolgáltatások beépülő MMC-modulban, és ellenőrizze, hogy fut-e a tárolási szinkronizálási ügynök szolgáltatás (FileSyncSvc).
3. Győződjön meg arról, hogy fut-e a Azure fájlszinkronizálás fájlrendszerszűrő-illesztőprogramok (StorageSync.sys és StorageSyncGuard.sys):
    - Egy rendszergazda jogú parancssorban futtassa `fltmc`. Győződjön meg arról, hogy látható-e a StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok.

Ha a hiba nem szűnik meg, futtassa a AFSDiag eszköz:
1. Hozzon létre egy könyvtárat, ahová a AFSDiag kimeneti (például C:\Output) menti.
2. Nyisson meg egy emelt szintű PowerShell ablakot, és futtassa a következő parancsokat (nyomja le az ENTER billentyűt minden parancs után):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Adja meg az Azure fájlszinkronizálás kernel mód nyomkövetési szint **1** (hacsak másként nincs megadva, részletesebb nyomkövetések létrehozásához), és nyomja le az ENTER billentyűt.
4. Adja meg az Azure fájlszinkronizálás felhasználói mód nyomkövetési szint **1** (hacsak másként nincs megadva, részletesebb nyomkövetések létrehozásához), és nyomja le az ENTER billentyűt.
5. Reprodukálja a hibát. Amikor végzett, adja meg **D**.
6. A megadott kimeneti könyvtár naplókat tartalmazó .zip fájl és nyomkövetési fájlok kerülnek.

## <a name="see-also"></a>Lásd még
- [Gyakori kérdések az Azure Files](storage-files-faq.md)
- [A Windows Azure fájlok problémák megoldásához](storage-troubleshoot-windows-file-connection-problems.md)
- [Azure-fájlok kapcsolatos problémák elhárítása a Linux](storage-troubleshoot-linux-file-connection-problems.md)
