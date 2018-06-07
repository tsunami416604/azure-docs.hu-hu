---
title: 'Az Azure AD Connect: Automatikus frissítés |} Microsoft Docs'
description: Ez a témakör ismerteti a beépített automatikus frissítési szolgáltatás az Azure AD Connect szinkronizálási szolgáltatás.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: adfce1195a6a322e0225ee09a45148001d0167c8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592129"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatikus frissítés
Ez a funkció a build 1.1.105.0 (dátuma: 2016. februári) ban jelent meg.

## <a name="overview"></a>Áttekintés
Így biztosítva, az Azure AD Connect telepítése mindig naprakész soha nem volt egyszerűbbé teheti a **automatikus frissítés** szolgáltatás. Ezt a szolgáltatást alapértelmezés szerint az Expressz telepítés és a DirSync frissítés. Amikor megjelenik egy új verziója, a rendszer automatikusan frissíti a telepítést.
Az alábbi alapértelmezés szerint engedélyezve van a automatikus frissítését:

* A telepítést és a DirSync frissítéseinek Express.
* SQL Express LocalDB használata, amely a mi expressz beállításokat mindig használjon. DirSync és az SQL Express LocalDB is használhatja.
* Az AD-fiókot az expressz beállításokat és a DirSync által létrehozott alapértelmezett MSOL_ fiók.
* A metaverzumban kisebb, mint 100 000 objektummal rendelkezik.

Automatikus frissítés aktuális állapotának megtekinthetők a PowerShell-parancsmaggal `Get-ADSyncAutoUpgrade`. A következő állapotok rendelkezik:

| Állapot | Megjegyzés |
| --- | --- |
| Engedélyezve |Automatikus frissítés engedélyezve van. |
| Felfüggesztve |A rendszer csak állítja be. A rendszer már nem jogosult az automatikus frissítések fogadására. |
| Letiltva |Automatikus frissítés le van tiltva. |

Között módosíthatja **engedélyezve** és **letiltott** rendelkező `Set-ADSyncAutoUpgrade`. Csak a rendszer-et kell beállítania a állapot **felfüggesztett**.

Automatikus frissítés a frissítési infrastruktúra az Azure AD Connect Health használja. Automatikus frissítés működik, győződjön meg arról, hogy megnyitott URL-címek a proxykiszolgáló **az Azure AD Connect Health** dokumentált módon [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Ha a **Synchronization Service Managert** felhasználói felület fut a kiszolgálón, majd a frissítés erre az időre szünetel a felhasználói felület le van zárva.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a Connect telepítés nem frissülnek magát az elvárásoknak megfelelően, majd kövesse ezeket a lépéseket, hogy megtudja, mi lehet a hiba.

Először nem fognak kísérli meg az első napja megjelent egy új verzióra történő automatikus frissítését. Nincs olyan szándékos annak véletlenszerű előtt frissítés kísérlet történik, így nem kell alarmed, ha a telepítés azonnal nincs frissítve.

Ha úgy gondolja, hogy valami nem megfelelő, először futtassa `Get-ADSyncAutoUpgrade` annak érdekében, hogy engedélyezve van az automatikus frissítés.

Ellenőrizzük, hogy a proxy vagy tűzfal nyitotta meg a szükséges URL-címek. Automatikus frissítés használ az Azure AD Connect Health leírtak szerint a [áttekintése](#overview). Ha a proxyt használ, győződjön meg arról, egészségügyi használatára van konfigurálva egy [proxykiszolgáló](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Is tesztelni a [állapotfigyelő kapcsolat](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) az Azure ad Szolgáltatásba.

A kapcsolat az Azure AD ellenőrizte az Eventlog megismerhetők idő. Indítsa el az eseménynaplót, és keresse meg a **alkalmazás** az eseménynaplóban talál. A forrás az eseménynaplóban szűrőket hozzá **az Azure AD Connect frissítése** és az esemény Adatazonosító-tartomány **300-399**.  
![Automatikus frissítés szűrőt az eseménynaplóban talál](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Most már megtekintheti az Eventlog társított az automatikus frissítés állapotát.  
![Automatikus frissítés szűrőt az eseménynaplóban talál](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

A eredménykódja állapotát áttekintést előtag van.

| Eredmény kódja előtagja | Leírás |
| --- | --- |
| Sikeres |A telepítés sikeresen frissítve. |
| UpgradeAborted |Egy ideiglenes állapot le a frissítést. Újból megpróbálkozik, és az általános gyakorlat, hogy később sikeres-e. |
| UpgradeNotSupported |A rendszer rendelkezik a konfigurációkat, amelyek az automatikus frissítés alatt a rendszer blokkolja. Megpróbálkozik megjelenítéséhez, ha az állapot változik, de az elvárás, hogy a rendszer manuálisan kell frissíteni. |

Ez egy lista a leggyakrabban használt üzenetek találja. Nem található összes van, de az eredmény üzenet törlése, mely a probléma van.

| Eredmény üzenet | Leírás |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nem írható a beállításjegyzékben. |
| UpgradeAbortedInsufficientDatabasePermissions |A beépített Rendszergazdák csoport nem rendelkezik engedéllyel kell rendelkeznie az adatbázishoz. Frissítsen kézzel a probléma megoldásához az Azure AD Connect legújabb verzióját. |
| UpgradeAbortedInsufficientDiskSpace |Nincs elegendő lemezterület a frissítés támogatásához. |
| UpgradeAbortedSecurityGroupsNotPresent |Nem található és nem oldható fel a szinkronizálási motor által használt összes biztonsági csoportokat. |
| UpgradeAbortedServiceCanNotBeStarted |Az NT-szolgáltatás **Microsoft Azure AD Sync** nem indult el. |
| UpgradeAbortedServiceCanNotBeStopped |Az NT-szolgáltatás **Microsoft Azure AD Sync** leállítása nem sikerült. |
| UpgradeAbortedServiceIsNotRunning |Az NT-szolgáltatás **Microsoft Azure AD Sync** nem működik. |
| UpgradeAbortedSyncCycleDisabled |A SyncCycle beállítás a [Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md) le van tiltva. |
| UpgradeAbortedSyncExeInUse |A [synchronization service Managert felhasználói felület](active-directory-aadconnectsync-service-manager-ui.md) meg nyitva a kiszolgálón. |
| UpgradeAbortedSyncOrConfigurationInProgress |A telepítési varázsló fut-e, vagy szinkronizálási kívül az ütemező lett ütemezve. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | A kijelölt AD FS bejelentkezési módszert. | 
| UpgradeNotSupportedCustomizedSyncRules |A konfigurációs felvett el saját egyéni szabályait. |
| UpgradeNotSupportedDeviceWritebackEnabled |Engedélyezte a [eszközvisszaíró](active-directory-aadconnect-feature-device-writeback.md) szolgáltatás. |
| UpgradeNotSupportedGroupWritebackEnabled |Engedélyezte a [csoportvisszaírásról](active-directory-aadconnect-feature-preview.md#group-writeback) szolgáltatás. |
| UpgradeNotSupportedInvalidPersistedState |A telepítés nincs egy expressz beállításokat, vagy a DirSync frissítését. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Több mint 100 000 objektummal rendelkezik a metaverzumban. |
| UpgradeNotSupportedMultiForestSetup |Több erdő csatlakozik. A gyorstelepítés csak egy erdő csatlakozik. |
| UpgradeNotSupportedNonLocalDbInstall |Nem használ egy SQL Server Express LocalDB adatbázisban. |
| UpgradeNotSupportedNonMsolAccount |A [AD-összekötő fiókhoz](active-directory-aadconnect-accounts-permissions.md#active-directory-account) már nem az alapértelmezett MSOL_ fiókot. |
| UpgradeNotSupportedNotConfiguredSignInMethod | AAD-csatlakozás beállítása, során úgy döntött, hogy *ne konfiguráljon* a bejelentkezési módszer kiválasztásakor. | 
| UpgradeNotSupportedPtaSignInMethod | A kijelölt áteresztő hitelesítés bejelentkezési módszert. |
| UpgradeNotSupportedStagingModeEnabled |A kiszolgáló van beállítva a [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Engedélyezte a [felhasználó-visszaírás](active-directory-aadconnect-feature-preview.md#user-writeback) szolgáltatás. |

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
