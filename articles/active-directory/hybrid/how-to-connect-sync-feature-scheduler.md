---
title: 'Azure AD Connect szinkronizálás: Ütemező | Microsoft dokumentumok'
description: Ez a témakör ismerteti a beépített ütemező funkció az Azure AD Connect szinkronizálásában.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309adfbebd4f4b615ac1f4061823ca01f3d3ee15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261072"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Az Azure AD Connect szinkronizálása: ütemező
Ez a témakör ismerteti a beépített ütemező az Azure AD Connect szinkronizálási (szinkronizálási motor).

Ez a funkció az 1.1.105.0 (2016 februárjában jelent meg) buildelésével került bevezetésre.

## <a name="overview"></a>Áttekintés
Az Azure AD Connect szinkronizálási szinkronizálása szinkronizálja a helyszíni címtárban egy ütemező használatával előforduló változásokat. Két ütemező folyamat létezik, az egyik a jelszószinkronizáláshoz, a másik pedig az objektum/attribútum szinkronizálási és karbantartási feladatokhoz. Ez a témakör az utóbbit öleli fel.

A korábbi kiadásokban az objektumok és attribútumok ütemezője a szinkronizálási motoron kívül volt. A windowsos feladatütemezőt vagy egy külön Windows-szolgáltatást használt a szinkronizálási folyamat elindításához. Az ütemező a szinkronizálási motorba beépített 1.1-es kiadásokkal rendelkezik, és lehetővé teszi kondirát. Az új alapértelmezett szinkronizálási gyakoriság 30 perc.

Az ütemező két tevékenységért felelős:

* **Szinkronizálási ciklus**. Az importálási, szinkronizálási és exportálási folyamat megváltozik.
* **Karbantartási feladatok**. A jelszó-visszaállítási és eszközregisztrációs szolgáltatás (DRS) kulcsainak és tanúsítványainak megújítása. Régi bejegyzések törlése a műveleti naplóban.

Maga az ütemező mindig fut, de beállítható úgy, hogy csak egy vagy egyik feladatot is futtatja. Ha például saját szinkronizálási ciklusfolyamatra van szüksége, letilthatja ezt a feladatot az ütemezőben, de továbbra is futtathatja a karbantartási feladatot.

## <a name="scheduler-configuration"></a>Ütemező konfigurációja
Az aktuális konfigurációs beállítások megtekintéséhez nyissa `Get-ADSyncScheduler`meg a PowerShell t, és futtassa a parancsot. Ez azt mutatja, valami ilyesmi ezt a képet:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Ha a parancsmag futtatásakor **a szinkronizálási parancs vagy a parancsmag nem érhető el,** majd a PowerShell-modul nincs betöltve. Ez a probléma akkor fordulhat elő, ha az Azure AD Connect et tartományvezérlőn vagy az alapértelmezett beállításoknál magasabb PowerShell-korlátozási szinttel rendelkező kiszolgálón futtatja. Ha ez a hiba `Import-Module ADSync` jelenik meg, futtassa a parancsmag elérhetővé.

* **AllowedSyncCycleInterval**. Az Azure AD által engedélyezett szinkronizálási ciklusok közötti legrövidebb időintervallum. Ennél a beállításnál nem lehet gyakrabban szinkronizálni, és továbbra is támogatott.
* **JelenlegEffectiveSyncCycleInterval**. A jelenleg érvényben lévő ütemezés. Ugyanaz az értéke, mint a CustomizedSyncInterval értéknek (ha van beállítva), ha nem gyakoribb, mint az AllowedSyncInterval. Ha az 1.1.281 előtt használ egy buildet, és módosítja a CustomizedSyncCycleInterval-ot, ez a módosítás a következő szinkronizálási ciklus után lép érvénybe. Az 1.1.281-es buildből a változás azonnal hatályba lép.
* **CustomizedSyncCycleInterval**. Ha azt szeretné, hogy az ütemező az alapértelmezett 30 perctől eltérő gyakorisággal fusson, akkor konfigurálja ezt a beállítást. A fenti képen az ütemező úgy van beállítva, hogy óránként fusson. Ha ezt a beállítást az AllowedSyncInterval értéknél alacsonyabb értékre állítja be, akkor az utóbbit használja a függvény.
* **NextSyncCyclePolicyType**. Vagy Delta vagy Initial. Azt határozza meg, hogy a következő futtatás csak a különbözeti módosításokat dolgozza-e fel, vagy a következő futtatás nak teljes importálást és szinkronizálást kell végeznie. Ez utóbbi újrafeldolgozna minden új vagy módosított szabályt.
* **NextsyncCycleStarttimeInUTC**. Amikor az ütemező legközelebb elindítja a következő szinkronizálási ciklust.
* **PurgeRunHistoryInterval**. Az időművelet naplóit meg kell őrizni. Ezek a naplók a szinkronizálási szolgáltatás kezelőjében tekinthetők meg. Az alapértelmezett az, hogy ezeket a naplókat 7 napig.
* **SyncCycleEnabled**. Azt jelzi, hogy az ütemező futtatja-e az importálási, szinkronizálási és exportálási folyamatokat a művelet részeként.
* **MaintenanceEnabled**. Megmutatja, hogy a karbantartási folyamat engedélyezve van-e. Frissíti a tanúsítványokat/kulcsokat, és törli a műveleti naplót.
* **StagingModeEnabled**. Megmutatja, hogy engedélyezve van-e az [átmeneti mód.](how-to-connect-sync-staging-server.md) Ha ez a beállítás engedélyezve van, akkor letiltja az exportálás futását, de továbbra is futtatja az importálást és a szinkronizálást.
* **SchedulerSuspended**. A Csatlakozás beállítással a frissítés során ideiglenesen letiltja az ütemező futását.

Ezeket a beállításokat `Set-ADSyncScheduler`a segítségével módosíthatja. A következő paraméterek módosíthatók:

* TestreszabvaSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SzinkronizáláscycleEnabled
* MaintenanceEnabled (Karbantartást engedélyezve)

Az Azure AD Connect korábbi buildjeiben **az isStagingModeEnabled** a Set-ADSyncScheduler-ben volt elérhető. A tulajdonság beállítása **nem támogatott.** A **SchedulerSuspended** tulajdonságot csak a Connect nek kell módosítania. Nem **támogatott,** hogy ezt közvetlenül a PowerShell állítsa be.

Az ütemező konfiguráció az Azure AD tárolja. Ha átmeneti kiszolgálóval rendelkezik, az elsődleges kiszolgálón lévő bármely módosítás az átmeneti kiszolgálóra is hatással van (az IsStagingModeEnabled kivételével).

### <a name="customizedsynccycleinterval"></a>TestreszabvaSyncCycleInterval
Szintaxis:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - nap, HH - óra, mm - perc, ss - másodperc

Például: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Az ütemező 3 óránként történő futtatását módosítja.

Például: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
A módosítások megváltoztatják az ütemező napi futtatását.

### <a name="disable-the-scheduler"></a>Az ütemező letiltása  
Ha konfigurációs módosításokat kell végrehajtania, akkor le szeretné tiltani az ütemezőt. Ha például [beállítja a szűrést,](how-to-connect-sync-configure-filtering.md) vagy [módosítja a szinkronizálási szabályokat.](how-to-connect-sync-change-the-configuration.md)

Az ütemező letiltásához futtassa a futtassa a programot. `Set-ADSyncScheduler -SyncCycleEnabled $false`

![Az ütemező letiltása](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Ha végrehajtotta a módosításokat, ne felejtse el `Set-ADSyncScheduler -SyncCycleEnabled $true`újra engedélyezni az ütemezőt a segítségével.

## <a name="start-the-scheduler"></a>Az ütemező indítása
Az ütemező alapértelmezés szerint 30 percenként fut. Bizonyos esetekben előfordulhat, hogy az ütemezett ciklusok között szeretne szinkronizálási ciklust futtatni, vagy egy másik típust kell futtatnia.

### <a name="delta-sync-cycle"></a>Különbözeti szinkronizálási ciklus
A különbözeti szinkronizálási ciklus a következő lépéseket tartalmazza:


- Különbözeti importálás az összes összekötőn
- Különbözeti szinkronizálás az összes összekötőn
- Exportálás az összes összekötőn

### <a name="full-sync-cycle"></a>Teljes szinkronizálási ciklus
A teljes szinkronizálási ciklus a következő lépéseket tartalmazza:

- Teljes importálás az összes összekötőn
- Teljes szinkronizálás az összes csatlakozón
- Exportálás az összes összekötőn

Lehet, hogy sürgős változása van, amelyet azonnal szinkronizálni kell, ezért manuálisan kell futtatnia egy ciklust. 

Ha manuálisan kell futtatnia egy szinkronizálási `Start-ADSyncSyncCycle -PolicyType Delta`ciklust, majd a PowerShell futtatása.

Teljes szinkronizálási ciklus kezdeményezéséhez futtassa `Start-ADSyncSyncCycle -PolicyType Initial` a PowerShell-parancssorból.   

A teljes szinkronizálási ciklus futtatása nagyon időigényes lehet, olvassa el a következő szakaszt a folyamat optimalizálásához.

### <a name="sync-steps-required-for-different-configuration-changes"></a>A különböző konfigurációs módosításokhoz szükséges szinkronizálási lépések
A különböző konfigurációs módosítások különböző szinkronizálási lépéseket igényelnek annak biztosításához, hogy a módosítások at minden objektumra megfelelően alkalmazzák.

- További objektumok vagy attribútumok hozzáadása forráskönyvtárból (a szinkronizálási szabályok hozzáadásával/módosításával)
    - Az adott forráskönyvtár hoz szükséges teljes importálás az összekötőn
- A szinkronizálási szabályok módosítása
    - A módosított szinkronizálási szabályokhoz teljes szinkronizálás szükséges az összekötőn
- Módosított [szűrés,](how-to-connect-sync-configure-filtering.md) így különböző számú objektumot kell belefoglalni
    - Minden AD-összekötő összekötőjének teljes importálása szükséges, kivéve, ha attribútumalapú szűrést használ a szinkronizálási motorba már importált attribútumok alapján.

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>A szinkronizálási ciklus testreszabása a Delta és a Teljes szinkronizálás idák megfelelő kombinációját futtatja
A teljes szinkronizálási ciklus futtatásának elkerülése érdekében megjelölheti a teljes lépés futtatását a következő parancsmagok használatával.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Példa: Ha az "AD A" összekötő szinkronizálási szabályait módosította, amelyek nem igényelnek új attribútumokat az importáláshoz, akkor a következő parancsmagokat futtatná egy különbözeti szinkronizálási ciklus futtatásához, amely az adott összekötő teljes szinkronizálási lépését is végrehajtotta.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Példa: Ha módosította az "AD A" összekötő szinkronizálási szabályait, hogy most egy új attribútumot kell importálni, akkor a következő parancsmagokat futtatja a különbözeti szinkronizálási ciklus futtatásához, amely az adott összekötő teljes importálási, teljes szinkronizálási lépését is végrehajtotta.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Az ütemező leállítása
Ha az ütemező jelenleg szinkronizálási ciklust futtat, lehet, hogy le kell állítania. Ha például elindítja a telepítővarázslót, és ezt a hibaüzenetet kapja:

![SyncCycleRunningHiba](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Szinkronizálási ciklus futása kor nem módosíthatja a konfigurációt. Megvárhatja, amíg az ütemező befejezi a folyamatot, de le is állíthatja, így azonnal elvégezheti a módosításokat. Az aktuális ciklus leállítása nem ártalmas, és a függőben lévő módosítások feldolgozása a következő futtatással történik.

1. Először mondja meg az ütemezőnek, hogy állítsa `Stop-ADSyncSyncCycle`le az aktuális ciklust a PowerShell-parancsmaggal.
2. Ha az 1.1.281 előtt használ egy buildet, akkor az ütemező leállítása nem állítja le az aktuális összekötőt az aktuális feladatból. Az összekötő leállításának kényszerítéséhez tegye ![a következő műveleteket: StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Indítsa el **a szinkronizálási szolgáltatást** a start menüből. Nyissa meg **az Összekötők**lehetőséget, jelölje ki az összekötőt **futás**állapottal, és válassza a **Leállítás** lehetőséget a Műveletek között.

Az ütemező még mindig aktív, és a következő lehetőségnél újraindul.

## <a name="custom-scheduler"></a>Egyéni ütemező
Az ebben a szakaszban dokumentált parancsmagok csak [az 1.1.130.0-s](reference-connect-version-history.md#111300) és újabb buildben érhetők el.

Ha a beépített ütemező nem felel meg a követelményeknek, majd ütemezheti az összekötők a PowerShell használatával.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncrunProfile
Az összekötők profilját így indíthatja el:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Az [összekötőnevekhez](how-to-connect-sync-service-manager-ui-connectors.md) és [a futtatási profilnevekhez](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) használandó nevek a [Szinkronizálási szolgáltatáskezelő felhasználói felületén](how-to-connect-sync-service-manager-ui.md)találhatók.

![Futtatási profil meghívása](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

A `Invoke-ADSyncRunProfile` parancsmag szinkron, azaz nem adja vissza a vezérlést, amíg az összekötő nem fejezte be a műveletet, akár sikeresen, akár egy hibával.

Az összekötők ütemezésekénél a következő sorrendben kell ütemezni őket:

1. (Teljes/Delta) Importálás helyszíni könyvtárakból, például az Active Directoryból
2. (Teljes/Delta) Importálás az Azure AD-ből
3. (Teljes/Delta) Szinkronizálás a helyszíni könyvtárakból, például az Active Directoryból
4. (Teljes/Delta) Szinkronizálás az Azure AD-ből
5. Exportálás az Azure AD-be
6. Exportálás helyszíni könyvtárakba, például az Active Directoryba

Ez a sorrend az, ahogy a beépített ütemező futtatja az összekötők.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorrunstatus
A szinkronizálási motor figyelése is figyelheti, hogy foglalt-e vagy nem működik-e. Ez a parancsmag üres eredményt ad vissza, ha a szinkronizálási motor üresjárati, és nem összekötőt futtat. Ha egy összekötő fut, az összekötő nevét adja vissza.

```
Get-ADSyncConnectorRunStatus
```

![Összekötő futásának állapota](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
A fenti képen az első sor olyan állapotból származik, amelyben a szinkronizálási motor tétlen. A második sor, amikor az Azure AD-összekötő fut.

## <a name="scheduler-and-installation-wizard"></a>Ütemező és telepítő varázsló
Ha elindítja a telepítővarázslót, az ütemező ideiglenesen felfüggesztésre kerül. Ez a viselkedés azért van, mert feltételezhető, hogy konfigurációs módosításokat hajt végre, és ezek a beállítások nem alkalmazhatók, ha a szinkronizálási motor aktívan fut. Ezért ne hagyja nyitva a telepítővarázslót, mert megakadályozza, hogy a szinkronizálási motor bármilyen szinkronizálási műveletet hajtson végre.

## <a name="next-steps"></a>További lépések
További információ az [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációjáról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
