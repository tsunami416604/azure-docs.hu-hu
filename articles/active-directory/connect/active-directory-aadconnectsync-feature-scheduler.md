---
title: 'Azure AD Connect szinkronizálása: ütemező |} Microsoft Docs'
description: Ez a témakör ismerteti az Azure AD Connect szinkronizálási szolgáltatás beépített ütemezési szolgáltatása.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fa99de4aac11e7310085cc37e6ebaee441415c61
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594159"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Azure AD Connect szinkronizálása: a Feladatütemező
Ez a témakör ismerteti az Azure AD Connect szinkronizálási szolgáltatás beépített ütemezési (más néven a szinkronizálási motor).

Ez a funkció a build 1.1.105.0 (dátuma: 2016. februári) ban jelent meg.

## <a name="overview"></a>Áttekintés
Azure AD Connect szinkronizálása a változásokat a Feladatütemező használatával a helyszíni címtárral szinkronizálja. Nincsenek két Feladatütemező folyamatok, jelszó-szinkronizálás egyet, majd egy másikat a szinkronizálás és a karbantartási feladatok eszközobjektum-attribútum. Ez a témakör ismerteti az utóbbi.

A korábbi kiadásokban az ütemező objektumok és attribútumok volt a szinkronizálási motoron külső. Használja azt a Windows Feladatütemező vagy egy különálló Windows-szolgáltatás elindítható a szinkronizálási folyamatot. A szinkronizálni kívánt az 1.1-es kiadásokban beépített van a Feladatütemező motor, és néhány testreszabásának engedélyezése. Az új alapértelmezett szinkronizálási gyakoriság értéke 30 perc.

Az ütemező felelős két feladatot:

* **Szinkronizálási ciklust**. A folyamat, szinkronizálás, exportálására és importálásra módosításokat.
* **Karbantartási feladatok**. Újítsa meg a kulcsoknak és tanúsítványoknak a jelszó alaphelyzetbe állítása és az Eszközregisztrációs szolgáltatás (DRS). Régi bejegyzéseket a műveleti napló kiürítése.

Az ütemező maga mindig fut, de azt beállítható úgy, hogy csak futtatnia kell egy, vagy nincs ezeket a feladatokat. Például ha rendelkezik saját szinkronizálási ciklust folyamat van szüksége, azt is tiltsa le ezt a feladatot az az ütemező azonban továbbra is futtassa a karbantartási feladat.

## <a name="scheduler-configuration"></a>Ütemezési konfiguráció
A jelenlegi konfigurációs beállítások megtekintéséhez lépjen a PowerShell és a Futtatás `Get-ADSyncScheduler`. Jelzi, hogy a kép hasonlót:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Ha látja **a szinkronizálási parancsot vagy a parancsmag nem áll rendelkezésre** Ez a parancsmag futtatásakor, majd a PowerShell-modulja nincs betöltve. Ez a probléma akkor fordulhat elő, ha az Azure AD Connect futtatása egy tartományvezérlőn vagy PowerShell korlátozás magasabb szintű, mint az alapértelmezett beállításokat a kiszolgálón. Ha ezt a hibaüzenetet látja, majd futtassa `Import-Module ADSync` a parancsmag elérhetővé.

* **AllowedSyncCycleInterval**. A legrövidebb közötti időközt szinkronizálási ciklust az Azure AD által engedélyezett. Nem lehet szinkronizálni a gyakrabban ezt a beállítást, és továbbra is támogatott.
* **CurrentlyEffectiveSyncCycleInterval**. Az ütemezés jelenleg érvényben. CustomizedSyncInterval megegyező értékűnek rendelkezik (ha beállítása) Ha még nincs mint AllowedSyncInterval. Ha 1.1.281 előtt build használja, és CustomizedSyncCycleInterval módosítja, a módosítás után a következő szinkronizálási ciklusban lép életbe. A build 1.1.281 módosítása azonnal érvénybe lép.
* **CustomizedSyncCycleInterval**. Ha azt szeretné, hogy a Feladatütemező 30 perces gyakorisággal bármely más alapértelmezett futtatásához, majd konfigurálja ezt a beállítást. A fenti kép az ütemező állították be ehelyett minden órában futtatva. Ha bekapcsolja ezt a beállítást értékre AllowedSyncInterval alacsonyabb, majd az utóbbi szolgál.
* **NextSyncCyclePolicyType**. Különbözeti vagy kezdeti. Meghatározza, hogy a következő futtatáskor kell csak a folyamat változási különbözeteket, vagy ha a következő futtatáskor kell tennie egy teljes importálása és szinkronizálás. Az utóbbi akkor is újból feldolgozza bármely új vagy módosított szabályok.
* **NextSyncCycleStartTimeInUTC**. Amikor legközelebb az ütemező elindítja a következő szinkronizálási ciklusban.
* **PurgeRunHistoryInterval**. A műveletnaplók kell tartani idő. Ezek a naplók a synchronization service Managert tekinthetők át. Alapértelmezés szerint ezek a naplók megtartása 7 nap.
* **SyncCycleEnabled**. Azt jelzi, hogy a Feladatütemező fut-e a importálása szinkronizálási és exportálási folyamatok a művelet részeként.
* **MaintenanceEnabled**. Azt jelzi, hogy a karbantartási folyamat engedélyezve van. A tanúsítványok vagy kulcsok frissíti, és a műveleti napló kiürítése.
* **StagingModeEnabled**. Bemutatja, ha [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode) engedélyezve van. Ha engedélyezi ezt a beállítást, majd nem jelenít meg a kivitel futtatását, de továbbra is futnak, importálása és szinkronizálás.
* **SchedulerSuspended**. Csatlakozás a frissítés során ad meg ideiglenesen blokk az ütemező futtatását.

Módosíthatja az egyes beállításokat a `Set-ADSyncScheduler`. A következő paraméterei módosíthatók:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Az Azure AD Connect korábbi buildekben **isStagingModeEnabled** a Set-ADSyncScheduler volt kitéve. Az **nem támogatott** a tulajdonság beállításához. A tulajdonság **SchedulerSuspended** Connect csak kell módosítani. Az **nem támogatott** közvetlenül be a PowerShell használatával.

A Feladatütemező beállítása az Azure AD tárolja. Ha egy átmeneti kiszolgálón, az elsődleges kiszolgáló bármilyen változás is érinti az átmeneti kiszolgálón (kivéve a IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Szintaxis: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - nap, HH - órák, pp - perc, mm - másodperc

Példa: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Módosítja az ütemező 3 óránként futtatásához.

Példa: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
A megváltoztatása az ütemező naponta.

### <a name="disable-the-scheduler"></a>Az ütemező letiltása  
Ha a konfigurációs módosításokat kell, majd le szeretné tiltani a Feladatütemező. Például, amikor Ön [szűrésének beállítása](active-directory-aadconnectsync-configure-filtering.md) vagy [módosítja szinkronizálási szabályok](active-directory-aadconnectsync-change-the-configuration.md).

Az ütemező letiltásához futtassa `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Az ütemező letiltása](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

Ha a módosításokat hajtott végre, akkor ne feledje el engedélyezze újra az ütemező `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>A Feladatütemező indítása
Az ütemező 30 percenként futtatása alapértelmezés szerint ki van. Bizonyos esetekben érdemes futtatni az ütemezett ciklusok Between szinkronizálási ciklust vagy más típusú futtatásához szükséges.

**A különbözeti szinkronizálási ciklusban**  
A különbözeti szinkronizálás ciklus a következő lépésekből áll:

* Különbözeti importálás az összekötők
* Különbözeti szinkronizálás az összekötők
* Az összekötők exportálása

Lehet, hogy rendelkezik olyan sürgős módosítást szinkronizálni kell azonnal, ezért ciklust manuálisan futtatnia kell. Ha manuálisan futtatnia kell a ciklus, majd a PowerShell futási `Start-ADSyncSyncCycle -PolicyType Delta`.

**Teljes szinkronizálás ciklus**  
A következő konfigurációs módosítások történtek, ha egy teljes szinkronizálási ciklust (más néven futtatásához szükséges Kezdeti):

* További objektumokat vagy attribútumok lehet importálni az forráskönyvtárat
* Módosításokat hajtott végre a szinkronizálási szabályok
* Módosított [szűrés](active-directory-aadconnectsync-configure-filtering.md) , a különböző számú objektum tartalmaznia kell

Ha ezek a változások történtek, akkor szüksége egy teljes szinkronizálási ciklust futtatásához, hogy a szinkronizálási motor forrásterületekről összesíteni szeretnénk a összekötőterek lehetőséget. Egy teljes szinkronizálási ciklust a következő lépésekből áll:

* Teljes importálás az összekötők
* Teljes szinkronizálás az összekötők
* Az összekötők exportálása

Ehhez futtassa a következő teljes szinkronizálási ciklust kezdeményez, `Start-ADSyncSyncCycle -PolicyType Initial` egy PowerShell-parancssorból. Ez a parancs elindít egy teljes szinkronizálási ciklust.

## <a name="stop-the-scheduler"></a>A Feladatütemező leállítása
Ha az ütemező jelenleg fut egy szinkronizálási ciklust, szükség lehet állítsa le. Ha például a telepítési varázsló elindításához, és ez a hibaüzenet:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

A szinkronizálási ciklusban futtatásakor konfigurációs módosítások nem hajtható végre. Sikerült megvárni, amíg az ütemező a folyamat befejeződött, de is leállíthatja azt, hogy a módosítások azonnal. Az aktuális ciklus leállítása nincs káros és függőben lévő módosítások vannak dolgozza fel a következő futtatáskor.

1. Első lépésként szólítja fel a Feladatütemező leállítása a PowerShell-parancsmaggal az aktuális ciklus `Stop-ADSyncSyncCycle`.
2. Használata előtt 1.1.281 build, majd leállítja a Feladatütemező nem állítsa le a jelenlegi feladat az aktuális összekötő. Hogy leállítja az összekötő, hajtsa végre a következő műveleteket: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Start **szinkronizálási szolgáltatás** a start menüből. Lépjen **összekötők**, jelölje ki az összekötő állapotának **futtató**, és válassza ki **leállítása** a műveletekből.

A Feladatütemező még mindig aktív, és újra elindul a Tovább lehetőséget.

## <a name="custom-scheduler"></a>Egyéni Feladatütemező
A parancsmagok, ebben a szakaszban leírt csak érhetők el a build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) és újabb verziók.

Ha a beépített ütemezési nem felel meg a követelményeknek, majd ütemezheti az összekötők PowerShell használatával.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
A profil egy összekötő ily módon megkezdése:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

A nevek [összekötő nevek](active-directory-aadconnectsync-service-manager-ui-connectors.md) és [futtassa a profilnév](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) itt található: a [Synchronization Service Manager felhasználói felületén](active-directory-aadconnectsync-service-manager-ui.md).

![Futtatási profil meghívása](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

A `Invoke-ADSyncRunProfile` szinkron parancsmag, ez azt jelenti, hogy azt nem ad vissza vezérlő csak az összekötő befejezése után a művelet sikeresen vagy hibával.

Ütemezheti az összekötők, az ajánlás esetén meg kell ütemezni őket a következő sorrendben:

1. (Teljes vagy különbözeti) A helyszíni címtárakat, például az Active Directory importálása
2. (Teljes vagy különbözeti) Az Azure AD importálása
3. (Teljes vagy különbözeti) A helyszíni címtárakat, például az Active Directory-szinkronizálás
4. (Teljes vagy különbözeti) Az Azure AD szinkronizálási
5. Az Azure AD exportálása
6. A helyszíni címtárakat, például az Active Directory exportálása

A sorrendje hogyan a beépített ütemezési fut-e az adott összekötőt.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Ugyanígy figyelheti a szinkronizálási motor meg, ha foglalt vagy inaktív. Ez a parancsmag üres értéket ad vissza, ha a szinkronizálási motor üresjáratban, és nem fut egy összekötőt. Ha egy összekötő fut, az összekötő nevét adja vissza.

```
Get-ADSyncConnectorRunStatus
```

![Összekötő állapota futtatása](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
A fenti kép az első sor olyan állapotban, amelyen a szinkronizálási motor üresjárati származik. Ha fut az Azure AD-összekötőt a másik sor.

## <a name="scheduler-and-installation-wizard"></a>Ütemező és a telepítési varázsló
Ha először a telepítési varázsló, a Feladatütemező ideiglenesen fel van függesztve. Ez a viselkedés oka, hogy a rendszer feltételezi, hogy a konfigurációs módosításokat, és ezeket a beállításokat nem lehet alkalmazni, ha a szinkronizálási motor aktívan fut. Ezért ne hagyja a telepítési varázsló megnyitása leállítja a szinkronizálási motor az egyetlen szinkronizálási műveletek végrehajtása óta.

## <a name="next-steps"></a>További lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
