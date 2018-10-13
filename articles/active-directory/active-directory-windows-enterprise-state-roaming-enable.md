---
title: Vállalati Állapothordozás engedélyezése az Azure Active Directoryban |} A Microsoft Docs
description: Gyakori kérdések Enterprise State Roaming beállítások a Windows-eszközökön. Enterprise State Roaming nyújt a felhasználók számára egységes felületet a Windows-eszközök között, és csökkenti az új eszköz konfigurálásához szükséges időt.
services: active-directory
keywords: Vállalati állapothordozás, a windows-felhő, vállalati állapothordozás engedélyezése
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: 71d8bfa116fd4d0e824d80da26df7271121a595c
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311371"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>A vállalati állapothordozás engedélyezése az Azure Active Directoryban
Enterprise State Roaming egy prémium szintű Azure AD vagy Enterprise Mobility + Security (EMS) licenccel rendelkező minden szervezet számára érhető el. Az Azure AD-előfizetés beszerzése További információkért lásd: a [Azure ad-ben termékoldalán](https://azure.microsoft.com/services/active-directory).

Vállalati Állapothordozás engedélyezése esetén a szervezete automatikusan kap egy ingyenes, korlátozott használatú licencet az Azure Rights Management-védelem az Azure Information Protection. Ez az ingyenes előfizetés titkosítása és visszafejtése a vállalati beállítások és alkalmazások adatainak Enterprise State Roaming által szinkronizált korlátozódik. Rendelkeznie kell [fizetős](https://azure.microsoft.com/pricing/details/information-protection/) használata az Azure Rights Management szolgáltatás összes funkciójáról.

## <a name="to-enable-enterprise-state-roaming"></a>Vállalati Állapothordozás engedélyezése

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com/).

1. Válassza ki **az Azure Active Directory** &gt; **eszközök** &gt; **Enterprise State Roaming**.

1. Válassza ki **felhasználók szinkronizálhatják a beállításokat és alkalmazásadatokat különböző eszközökön**. További információkért lásd: [eszközbeállítások konfigurálása](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Felhasználók feliratú eszközbeállítást képe szinkronizálhatják a beállításokat és alkalmazásadatokat különböző eszközökön](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Az Enterprise State Roaming szolgáltatás használata Windows 10 rendszerű eszköz az eszközt kell hitelesítenie, az Azure AD-identitás segítségével. Az Azure ad-hez csatlakoztatott eszközök esetében a felhasználó elsődleges bejelentkezési identitását az Azure AD identity, így további konfiguráció nélkül nem szükséges. A helyszíni Active Directoryt használó eszközök esetében a rendszergazda kell [konfigurálása a hibrid Azure Active Directory-hez csatlakoztatott eszközök](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual-steps). 

## <a name="data-storage"></a>Adattárolás
Enterprise State Roaming adatokat egy vagy több üzemeltetett [Azure-régiók](https://azure.microsoft.com/regions/) , hogy ajánlott zárás ország/régió értékre van állítva, az Azure Active Directory-példányban. Enterprise State Roaming adatok particionálása három fő földrajzi régió alapján: Észak-Amerikában, az EMEA-régióban és az APAC. A bérlő Enterprise State Roaming adatok helyben található, és a földrajzi régió, és nem replikál a rendszer régiók.  Példa:

Ország/régió érték | a data-ban üzemeltetett rendelkezik
---------------------|-------------------------
Franciaország vagy Zambia például az EMEA ország | Egy vagy több Azure-régiót Európán 
Észak-amerikai országot, például az Egyesült Államok és Kanada | egy vagy több Azure-régiót belül az Egyesült Államok
Az APAC ország, például az ausztráliai vagy Új-Zéland | egy vagy több Azure-régiót Ázsia belül
Dél-amerikai és Antarktisz régiók | egy vagy több Azure-régióban az Egyesült Államok belül

Ország/régió értékét az Azure AD-címtár létrehozása folyamat részeként van beállítva, és ezt követően nem módosítható. Ha további részleteket az adatok tárolási helye, küldjön el egy jegyet az [az Azure-támogatás](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Felhasználó eszköz szinkronizálási állapot megtekintése
Kövesse az alábbi lépéseket egy felhasználó eszköz szinkronizálása állapotjelentése megtekintéséhez.

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com/).

1. Válassza ki **Azure Active Directory** &gt; **felhasználók** &gt; **minden felhasználó**.

1. Válassza ki a felhasználót, és válassza **eszközök**.

1. A **megjelenítése**válassza **beállításokat és alkalmazásadatokat Szinkronizáló eszközöket** szinkronizálási állapotának megjelenítéséhez.
  
  ![Eszközbeállítás szinkronizálási adatokat ábrázoló kép](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
1. Ha a felhasználó Szinkronizáló eszközöket, az eszközök láthatja itt látható módon.
  
  ![Eszközadatok szinkronizálása Oszlopalapú képe](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Adatmegőrzés
A Microsoft cloud, Enterprise State Roaming használatával szinkronizált adatok megmarad, amíg manuálisan nem törli vagy addig, amíg a szóban forgó adatokat kell elavult határozza meg. 

### <a name="explicit-deletion"></a>Közvetlen törlése
Közvetlen törlése esetén az Azure-rendszergazdai törli egy felhasználó vagy egy könyvtárat vagy más módon kér explicit módon, hogy adatokat a rendszer törli.

* **Felhasználó törlése**: amikor egy felhasználó Azure AD-ben törölnek, a felhasználói fiók roaming-adatforgalom 90, 180 nap után törlődik. 
* **Könyvtár törlése**: egy teljes könyvtár törlése az Azure ad-ben egy azonnali műveletet. A beállítások adatainak társított, az adott címtár 90, 180 nap után törlődik. 
* **Törlési kérés**: Ha az Azure AD-rendszergazda így manuálisan törölheti az egy adott felhasználó adatok vagy beállítások adatainak fájlvédelemre, a rendszergazda is küldjön el egy jegyet az [az Azure-támogatás](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Elavult adatok törlése
Adatok, amelyet nem használtak egy évig ("a megőrzési időszak"), elavult lesznek kezelve, és törölheti a Microsoft-felhőből. A megőrzési időtartam változhat, de legalább 90 nappal nem. Az elavult adatokat lehet Windows-/ Alkalmazásbeállítások és az összes beállítás egy felhasználó egy adott készletét. Példa:

* Ha eszközöket nem egy meghatározott beállítások gyűjtemény elérni (például egy alkalmazás törlődik az eszköz vagy egy csoport például a "Téma" le van tiltva, az összes felhasználó eszközeinek), majd a gyűjteménynek a megőrzési időszak után elavulttá válik, és előfordulhat, hogy törölhető . 
* Ha a felhasználó kikapcsolta azt eszközökön szinkronizálását, majd a beállítások adatainak egyike fogják elérni, és az adott felhasználó összes beállítások adatainak elavulttá fog válni, és előfordulhat, hogy a megőrzési időszak után lehet törölni. 
* Ha az Azure Active directory-rendszergazda kikapcsolja az Enterprise State Roaming a teljes címtárra, majd az összes felhasználó számára, hogy könyvtárat a beállítások szinkronizálása leáll, és az összes felhasználó összes beállítások adatainak elavulttá váltak, és előfordulhat, hogy a megőrzési időszak után lehet törölni. 

### <a name="deleted-data-recovery"></a>A törölt adatok helyreállítása
Az adatmegőrzési házirend érték nem módosítható. Az adatok végleges törlése után már nem állítható helyre. A beállítások adatainak azonban csak a Microsoft cloud-a végfelhasználói eszközről nem törlődik. Az Enterprise State Roaming szolgáltatás később újracsatlakozik bármilyen eszközről, ha a beállítások ismét szinkronizálva és a Microsoft-felhőben tárolt.

## <a name="related-topics"></a>Kapcsolódó témakörök
* [Vállalati Állapothordozás áttekintése](active-directory-windows-enterprise-state-roaming-overview.md)
* [Beállítások és adatroaming GYIK](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Beállítások szinkronizálása tartozó házirend- és mobileszköz-kezelési beállítások](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [A Windows 10 roaming beállítások referenciája](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [hibaelhárítással](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
