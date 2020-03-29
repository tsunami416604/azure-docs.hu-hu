---
title: 'Azure AD Connect: Automatikus frissítés | Microsoft dokumentumok'
description: Ez a témakör az Azure AD Connect-szinkronizálás beépített automatikus frissítési funkcióját ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd61b78ca3027ade1f2f48dec33e0a8ed508d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60349844"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatikus frissítés
Ez a funkció az [1.1.105.0 (2016 februárjában jelent meg)](reference-connect-version-history.md#111050)buildelésével került bevezetésre.  Ez a funkció frissítve lett az [1.1.561-es buildben,](reference-connect-version-history.md#115610) és most már támogatja a korábban nem támogatott további forgatókönyveket.

## <a name="overview"></a>Áttekintés
Az **automatikus frissítési** funkcióval még soha nem volt ilyen egyszerű, hogy az Azure AD Connect telepítése mindig naprakész legyen. Ez a funkció alapértelmezés szerint engedélyezve van expressz telepítések és DirSync frissítések. Új verzió megjelenésekor a telepítés automatikusan frissül.
Az automatikus frissítés alapértelmezés szerint engedélyezve van a következőkesetében:

* Expressz beállítások telepítése és DirSync frissítések.
* Az SQL Express LocalDB használatával, amit az Express beállítások mindig használnak. A DirSync az SQL Express programmal a LocalDB-t is használja.
* Az AD-fiók az alapértelmezett MSOL_ az Express-beállítások és a DirSync által létrehozott fiókhoz.
* Kevesebb, mint 100 000 objektum van a metaverzumban.

Az automatikus frissítés jelenlegi állapota a PowerShell-parancsmaggal `Get-ADSyncAutoUpgrade`tekinthető meg. Ez a következő államok:

| Állapot | Megjegyzés |
| --- | --- |
| Engedélyezve |Az automatikus frissítés engedélyezve van. |
| Felfüggesztve |Csak a rendszer állítja be. A rendszer **jelenleg nem** jogosult automatikus frissítésekfogadására. |
| Letiltva |Az automatikus frissítés le van tiltva. |

Az **Engedélyezett** és a `Set-ADSyncAutoUpgrade` **Letiltás** között válthat. Csak a rendszer állítsa be az állam **Felfüggesztett**.  Az 1.1.750.0 előtt a Set-ADSyncAutoUpgrade parancsmag blokkolja az automatikus frissítést, ha az automatikus frissítési állapot felfüggesztésre van állítva. Ez a funkció most megváltozott, így nem blokkolja az automatikus frissítést.

Az automatikus frissítés az Azure AD Connect Health szolgáltatást használja a frissítési infrastruktúrához. Az automatikus frissítés működéséhez győződjön meg arról, hogy megnyitotta az URL-címeket az **Azure AD Connect Health** proxykiszolgálóján az Office [365 URL-címeiben és IP-címtartományokban](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)dokumentált módon.


Ha a **Szinkronizálási szolgáltatáskezelő** felhasználói felülete fut a kiszolgálón, akkor a frissítés a felhasználói felület bezárásáig felfüggesztésre kerül.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a Connect telepítése nem a várt módon frissíti magát, kövesse az alábbi lépéseket, hogy megtudja, mi lehet a baj.

Először is, ne számítson arra, hogy az automatikus frissítés megkísérli az új verzió kiadásának első napján. A frissítés megkísérlése előtt szándékos véletlenszerűség történik, ezért ne ijedjen meg, ha a telepítés nem frissül azonnal.

Ha úgy gondolja, hogy valami `Get-ADSyncAutoUpgrade` nincs rendben, akkor először futtassa az automatikus frissítés engedélyezéséhez.

Ezután győződjön meg arról, hogy megnyitotta a szükséges URL-címeket a proxyban vagy a tűzfalban. Az automatikus frissítés az Azure AD Connect Health szolgáltatást használja az [áttekintésben leírtak szerint.](#overview) Ha proxyt használ, győződjön meg arról, hogy az Állapot szolgáltatás [proxykiszolgáló](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)használatára van konfigurálva. Tesztelje az [Azure AD-hez való állapotkapcsolatot](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) is.

Az Azure AD-vel való kapcsolat ellenőrzése után itt az ideje, hogy vizsgálja meg az eseménynaplókat. Indítsa el az eseménynaplót, és tekintse meg az **Alkalmazás** eseménynaplójában. Adjon hozzá egy eseménynapló-szűrőt az **Azure AD Connect frissítéshez** és a **300-399-es**eseményazonosító-tartományhoz.  
![Eseménynapló-szűrő az automatikus frissítéshez](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Most már láthatja az automatikus frissítés állapotához társított eseménynaplókat.  
![Eseménynapló-szűrő az automatikus frissítéshez](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Az eredménykód előtaggal rendelkezik, amely áttekintést ad az állapotról.

| Eredménykód előtagja | Leírás |
| --- | --- |
| Sikeres |A telepítés frissítése sikeresen megtörtént. |
| FrissítésMegszakadt |Egy ideiglenes állapot leállította a frissítést. Ez lesz újra próbált újra és a elvárás van amit ez sikerül később. |
| UpgradeNotSupported |A rendszer olyan konfigurációval rendelkezik, amely megakadályozza a rendszer automatikus frissítését. A rendszer újra megpróbálja látni, hogy az állapot változik-e, de az elvárás az, hogy a rendszert manuálisan kell frissíteni. |

Az alábbiakban a leggyakrabban használt üzenetek listáját találja. Nem tartalmazza az összeset, de az eredményüzenetnek egyértelműnek kell lennie azzal kapcsolatban, hogy mi a probléma.

| Eredményüzenet | Leírás |
| --- | --- |
| **FrissítésMegszakadt** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nem lehet írni a rendszerleíró adatbázisba. |
| UpgradeAbortedInsufficientDatabaseEngedélyek |A beépített rendszergazdák csoport nem rendelkezik az adatbázishoz szükséges engedélyekkel. Manuálisan frissítsen az Azure AD Connect legújabb verziójára a probléma megoldásához. |
| UpgradeAbortedInsufficientDiskSpace |Nincs elég lemezterület a frissítés támogatásához. |
| UpgradeAbortedSecurityGroupsNotPresent |Nem található és nem oldható fel a szinkronizálási motor által használt összes biztonsági csoport. |
| UpgradeAbortedServiceCanNotBeStartedStarted |Az NT szolgáltatás **Microsoft Azure AD Sync** nem tudott elindulni. |
| UpgradeAbortedServiceCanNotBeStopped |Az NT szolgáltatás **Microsoft Azure AD sync** nem tudta leállítani. |
| UpgradeAbortedServiceIsNotRunning |Az NT szolgáltatás **Microsoft Azure AD Sync** nem fut. |
| UpgradeAbortedSyncCycleDisabled |Az [ütemező](how-to-connect-sync-feature-scheduler.md) Szinkronizálási ciklus lehetősége le van tiltva. |
| UpgradeAbortedSyncExeInUse |A [szinkronizálási szolgáltatáskezelő felhasználói felülete](how-to-connect-sync-service-manager-ui.md) meg van nyitva a kiszolgálón. |
| UpgradeAbortedSyncOrConfigurationInProgress |A telepítővarázsló fut, vagy szinkronizálás volt ütemezve az ütemezőn kívül. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Az Adfs-t választotta bejelentkezési módszerként. |
| UpgradeNotSupportedCustomizedSyncRules |Saját egyéni szabályokat adott hozzá a konfigurációhoz. |
| UpgradeNotSupportedDeviceWritebackEnabled |Engedélyezte az [eszköz visszaírási](how-to-connect-device-writeback.md) funkcióját. |
| UpgradeNotSupportedGroupWritebackEnabled |Engedélyezte a [csoportvisszaírási](how-to-connect-preview.md#group-writeback) funkciót. |
| UpgradeNotSupportedInvalidPersistedState |A telepítés nem Expressz vagy DirSync-frissítés. |
| UpgradeNotSupportedMetaverseSizeExceeededed |Több mint 100 000 objektum van a metaverzumban. |
| UpgradeNotSupportedMultiForestSetup |Több erdőhöz csatlakozik. Az expressz beállítás csak egy erdőhöz kapcsolódik. |
| UpgradeNotSupportedNonLocalDbInstall |Nem SQL Server Express LocalDB adatbázist használ. |
| UpgradeNotSupportedNonMsolAccount |Az [AD DS Connector-fiók](reference-connect-accounts-permissions.md#ad-ds-connector-account) már nem az alapértelmezett MSOL_ fiók. |
| UpgradeNotSupportednotconfiguredsigninmethod | Az AAD Connect beállításakor a *Nincs konfigurálás* lehetőséget választotta a bejelentkezési módszer kiválasztásakor. |
| UpgradeNotSupportedPtaSignInMethod | Az átmenő hitelesítést választotta bejelentkezési módszerként. |
| UpgradeNotSupportedStagingModeEnabled |A kiszolgáló [átmeneti üzemmódban](how-to-connect-sync-staging-server.md)van beállítva. |
| UpgradeNotSupportedUserWritebackEnabled |Engedélyezte a [felhasználói visszaírási](how-to-connect-preview.md#user-writeback) funkciót. |

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
