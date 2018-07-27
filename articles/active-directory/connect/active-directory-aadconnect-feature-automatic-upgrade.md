---
title: 'Az Azure AD Connect: Automatikus frissítés |} A Microsoft Docs'
description: Ez a témakör ismerteti a beépített, automatikus frissítési funkció az Azure AD Connect szinkronizálása.
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
ms.date: 06/16/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cfb09718c545d55b31250070fed0c8adff9d3fed
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263946"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatikus frissítés
Ez a funkció jelent meg a build [(kiadás dátuma: 2016. február) 1.1.105.0](active-directory-aadconnect-version-history.md#111050).  Ez a szolgáltatás frissítve lett a [1.1.561 összeállítása](active-directory-aadconnect-version-history.md#115610) és mostantól támogatja a további forgatókönyvek, amelyek korábban nem támogatottak.

## <a name="overview"></a>Áttekintés
Gondoskodik róla, hogy az Azure AD Connect telepítése mindig naprakész minden eddiginél egyszerűbbé teszi a **automatikus frissítés** funkció. Ez a funkció a express telepítések alapértelmezés szerint engedélyezve van, és a frissítés a DirSync. Amikor egy új verziója, a rendszer automatikusan frissíti a telepítést.
Az alábbi alapértelmezés szerint engedélyezve van a automatikus frissítését:

* Expressz telepítési beállítások és a DirSync frissítése.
* Az SQL Express LocalDB, amelyek használata a mi gyorsbeállításokat mindig használja. DirSync és az SQL Express LocalDB is használhatja.
* Az AD egy a gyorsbeállítások és a DirSync által létrehozott alapértelmezett MSOL_ fiók.
* Kevesebb mint 100 000 objektummal rendelkezik a metaverzumban.

Automatikus frissítés aktuális állapotának tekinthet meg a PowerShell-parancsmaggal `Get-ADSyncAutoUpgrade`. A következő állapotok rendelkezik:

| Állapot | Megjegyzés |
| --- | --- |
| Engedélyezve |Automatikus frissítés engedélyezve van. |
| Felfüggesztve |A rendszer csak állítja be. A rendszer már nem jogosult az automatikus frissítéseket. |
| Letiltva |Automatikus frissítés le van tiltva. |

Között módosíthatja **engedélyezve** és **letiltott** a `Set-ADSyncAutoUpgrade`. A rendszer kell beállítania az állapot csak **felfüggesztett**.

Automatikus frissítés a frissítési infrastruktúra használ az Azure AD Connect Health. Az automatikus frissítés működik, győződjön meg arról, hogy megnyitotta az URL-címeket a proxykiszolgálót a **az Azure AD Connect Health** leírtak szerint [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Ha a **Synchronization Service Managert** felhasználói felület a kiszolgálón fut, akkor a frissítés fel van függesztve, amíg a felhasználói felület le van zárva.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a Connect-telepítésre nem frissíti magát várt módon, majd kövesse az alábbi lépéseket, ismerje meg, milyen helytelen lehet.

Először történik kísérlet az első napján egy új verziója, az automatikus frissítés nem fognak. Egy szándékos véletlenszerűségre azért van előtt frissítés kísérlet történik, így nem kell alarmed, ha a telepítés azonnal nincs frissítve.

Ha úgy gondolja, hogy valami nem megfelelő, először futtassa `Get-ADSyncAutoUpgrade` annak érdekében, hogy engedélyezve van az automatikus frissítés.

Ezután ellenőrizze a proxy vagy tűzfal nyitotta meg a szükséges URL-címeket. Az automatikus frissítést használ az Azure AD Connect Health leírtak szerint a [áttekintése](#overview). Ha a proxy használatát választja, győződjön meg arról, egészségügyi használatára van konfigurálva egy [proxykiszolgáló](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Is tesztelheti a [egészségügyi kapcsolat](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) az Azure ad-hez.

Az Azure AD ellenőrizte kapcsolatokkal, amelyekkel megismerhetők az Eventlog ideje. Indítsa el az eseménynaplót, és tekintse meg a **alkalmazás** az eseménynaplóban. A forrás egy Eseménynapló-szűrő hozzáadása **az Azure AD Connect frissítés** és az event id tartomány **300-399**.  
![Automatikus frissítés szűrőt az eseménynaplóban](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Most már megtekintheti az automatikus frissítés állapota társított Eventlog.  
![Automatikus frissítés szűrőt az eseménynaplóban](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

A eredménykódot tartalmaz egy előtagot a állapotának áttekintése.

| Eredmény kódja előtagja | Leírás |
| --- | --- |
| Sikeres |A telepítés sikeresen frissült. |
| UpgradeAborted |Egy ideiglenes állapot le a frissítést. Az elvárás, hogy később sikeres-e, és újból próbálkozunk. |
| UpgradeNotSupported |A rendszer, amely blokkolja a rendszer automatikusan frissítendő konfigurációval rendelkezik. Tekintse meg, ha az állapot módosul, de az elvárás, hogy a rendszer manuálisan kell frissíteni a rendszer megpróbálja. |

Ez egy lista a leggyakrabban használt üzenetek találja. Nem tartalmazza az összes, de az eredménye üzenet kell lennie, törölje a jelet, mely a probléma van.

| Eredményét jelző üzenet | Leírás |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nem sikerült írni a beállításjegyzékben. |
| UpgradeAbortedInsufficientDatabasePermissions |A beépített Rendszergazdák csoport nincs engedélye az adatbázishoz. Manuálisan frissítse a probléma megoldása érdekében az Azure AD Connect legújabb verzióját. |
| UpgradeAbortedInsufficientDiskSpace |Nem áll elég lemezterület a frissítés támogatása. |
| UpgradeAbortedSecurityGroupsNotPresent |Nem sikerült keresse meg és oldja meg a szinkronizálási motor által használt összes biztonsági csoportokat. |
| UpgradeAbortedServiceCanNotBeStarted |Az NT-szolgáltatás **a Microsoft Azure AD Sync** nem indult el. |
| UpgradeAbortedServiceCanNotBeStopped |Az NT-szolgáltatás **a Microsoft Azure AD Sync** leállítása nem sikerült. |
| UpgradeAbortedServiceIsNotRunning |Az NT-szolgáltatás **a Microsoft Azure AD Sync** nem működik. |
| UpgradeAbortedSyncCycleDisabled |A SyncCycle opcióhoz a [scheduler](active-directory-aadconnectsync-feature-scheduler.md) le van tiltva. |
| UpgradeAbortedSyncExeInUse |A [synchronization service Managert felhasználói felület](active-directory-aadconnectsync-service-manager-ui.md) meg nyitva a kiszolgálón. |
| UpgradeAbortedSyncOrConfigurationInProgress |A telepítővarázsló fut, vagy az ütemező kívül ütemezett szinkronizálást. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | A kijelölt AD FS bejelentkezési módszert. | 
| UpgradeNotSupportedCustomizedSyncRules |A konfigurációhoz hozzáadott el saját egyéni szabályait. |
| UpgradeNotSupportedDeviceWritebackEnabled |Engedélyezte a [eszközvisszaírás](active-directory-aadconnect-feature-device-writeback.md) funkció. |
| UpgradeNotSupportedGroupWritebackEnabled |Engedélyezte a [csoportvisszaírás](active-directory-aadconnect-feature-preview.md#group-writeback) funkció. |
| UpgradeNotSupportedInvalidPersistedState |A telepítés még nem egy gyorsbeállításokat vagy a DirSync frissítése. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Több mint 100 000 objektummal rendelkezik a metaverzumban. |
| UpgradeNotSupportedMultiForestSetup |Egynél több erdő csatlakozik. Expressz telepítés csak egy erdőben csatlakozik. |
| UpgradeNotSupportedNonLocalDbInstall |SQL Server Express LocalDB adatbázis nem használ. |n
| UpgradeNotSupportedNonMsolAccount |A [AD DS-összekötő fiók](active-directory-aadconnect-accounts-permissions.md#ad-ds-connector-account) már nem az alapértelmezett MSOL_ fiókot. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Állítson be az AAD Connect, választott *ne konfiguráljon* a bejelentkezési módszer kiválasztásakor. | 
| UpgradeNotSupportedPtaSignInMethod | Az átmenő hitelesítés kijelölt bejelentkezési módszert. |
| UpgradeNotSupportedStagingModeEnabled |A kiszolgáló beállítása legyen [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Engedélyezte a [felhasználó-visszaírás](active-directory-aadconnect-feature-preview.md#user-writeback) funkció. |

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
