---
title: Vállalati Állapothordozás engedélyezése az Azure Active Directoryban |} Microsoft Docs
description: Gyakori kérdések a Windows-eszközökön a vállalati Állapothordozás beállításait. A vállalati Állapothordozás nyújt a felhasználók számára egy egységes élmény a Windows-eszközön, és csökkenti az új eszköz konfigurálásához szükséges időt.
services: active-directory
keywords: a vállalati állapothordozás, a windows-felhő, a vállalati állapothordozás engedélyezése
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.openlocfilehash: dba749b6d85898e6438ce1160b9bf6eaff6f4ac9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>A vállalati állapothordozás engedélyezése az Azure Active Directoryban
A vállalati Állapothordozás érhető el a szervezet az Azure AD Premium vagy Enterprise Mobility + Security (EMS) licencet. Egy Azure AD-előfizetés beszerzésére további információkért lásd: a [az Azure AD, termékoldala](https://azure.microsoft.com/services/active-directory).

Ha engedélyezi a vállalati Állapothordozás, a szervezet automatikusan kapnak Azure Rights Management-védelem ingyenes, korlátozott használatú licencet az Azure Information Protectiontől. Ez az ingyenes előfizetés titkosítása és visszafejtése a vállalati beállítások és a vállalati Állapothordozás által szinkronizált alkalmazásadatok korlátozódik. Rendelkeznie kell [a szolgáltatás fizetős](https://azure.microsoft.com/pricing/details/information-protection/) is használja az Azure Rights Management szolgáltatás teljes lehetőségeit.

## <a name="to-enable-enterprise-state-roaming"></a>A vállalati Állapothordozás engedélyezése

1. Jelentkezzen be [az Azure AD felügyeleti központban](https://aad.portal.azure.com/).

2. Válassza ki **Azure Active Directory** &gt; **eszközök** &gt; **eszközbeállítások**.

3. Válassza ki **felhasználók is szinkronizálásának beállítások és alkalmazások adatainak eszközök**. További információkért lásd: [eszközbeállítások konfigurálása](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Felhasználók nevű eszköz beállítás képe előfordulhat, hogy szinkronizálásának beállítások és alkalmazások adatainak eszközök](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Az a vállalati Állapothordozás szolgáltatás használata Windows 10-es eszközök az eszközt kell hitelesítenie, az Azure AD identity használatával. Az Azure AD-tartományhoz csatlakoztatott eszközök esetén a felhasználó elsődleges bejelentkezési azonosító az Azure AD identity, így nincs szükség további konfigurációra. A helyszíni Active Directoryt használó eszközök esetében az informatikai rendszergazda kell [a tartományhoz csatlakozó eszközök csatlakoztatása az Azure AD, a Windows 10 észlel](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Adattárolás
A vállalati Állapothordozás adatok található egy vagy több [Azure-régiók](https://azure.microsoft.com/regions/) , hogy ajánlott igazodnak az ország vagy régió érték beállítása az Azure Active Directory-példányban. A vállalati Állapothordozás adatok particionálása három fő földrajzi régió alapján: Észak-Amerikában, az EMEA és APAC. A bérlői vállalati Állapothordozás adatok helyben található, és a földrajzi régió, és nem replikálódnak régiók.  Példa:
Ország vagy régió érték | a birtokolt adataikat
---------------------|-------------------------
"Franciaország" vagy "Zambia" például az EMEA ország | egy vagy az Azure-régiók Európa 
Észak-amerikai ország például "Az Amerikai Egyesült Államok" vagy "Kanada" | egy vagy több Azure-régiókban belül az Egyesült Államok
Egy APAC ország, például "Ausztrália" vagy "Új-Zéland" | egy vagy több Azure-régiókban Ázsia belül
Dél-amerikai és Antarktisz régiók | egy vagy több Azure-régiók belül az Egyesült Államok

Az ország vagy régió érték az Azure Active directory létrehozási folyamat részeként van beállítva, és ezt követően nem módosítható. Ha további részleteket az adatok tárolási helye, a fájl igénylést [az Azure támogatási](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Felhasználó eszköz szinkronizálási állapot megtekintése
Kövesse az alábbi lépéseket egy felhasználó eszköz szinkronizálási állapotáról készült jelentés megtekintéséhez.

1. Jelentkezzen be [az Azure AD felügyeleti központban](https://aad.portal.azure.com/).

2. Válassza ki **Azure Active Directory** &gt; **felhasználók és csoportok** &gt; **minden felhasználó**.

3. A felhasználó, majd válassza ki és **eszközök**.

4. A **megjelenítése**, jelölje be **eszközök beállításainak és az alkalmazásadatok szinkronizálása** szinkronizálási állapot megjelenítéséhez.
  
  ![eszköz szinkronizálási adatokra vonatkozó beállításával képe](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. Ha a felhasználó szinkronizálása eszközök, az eszközök megjelenik itt látható módon.
  
  ![eszköz oszlopos adatokat képe](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Adatmegőrzés
Szinkronizált az Azure-ban, a vállalati Állapothordozás adatok megmarad, amíg le nem manuálisan, vagy amíg a szóban forgó adatok határozza meg, hogy elavult. 

### <a name="explicit-deletion"></a>Közvetlen törlése
Közvetlen törlése esetén az Azure rendszergazda törli a felhasználó vagy egy könyvtár, vagy egyéb kérelmek explicit módon, hogy adatokat a rendszer törli.

* **Felhasználó törlése**: a felhasználó törlésekor az Azure ad-ben a felhasználói fiók barangolási adatait 90-180 nap után törlődik. 
* **Directory törlésre**: egy teljes könyvtár törlése az Azure ad-ben egy azonnali művelet. A beállítások adatainak társított vele directory 90-180 nap után törlődik. 
* **Törlési kérés**: Ha az Azure AD admin szeretné egy adott felhasználó adatok vagy beállítások adatainak törölje kézzel, a rendszergazda is fájl igénylést [az Azure támogatási](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Régi adatok törlése
Egy évig ("a megőrzési időtartam") nem használt adatok elavult tekinteni, és az Azure-ból törlődhet. A megőrzési időtartam változhat, de csak akkor 90 napon belül. Az elavult adatokat lehet egy meghatározott Windows-alkalmazás beállításai vagy a felhasználó az összes beállítás. Példa:

* Ha egy adott beállításokkal gyűjteménye egyetlen eszköz sem érhető el (például egy alkalmazást távolítja el az eszközről vagy egy csoport például a "Theme" le van tiltva, az összes a felhasználó eszközeinek), majd a gyűjteménynek a megőrzési időszak letelte után elavulttá válik, és előfordulhat, hogy törölve . 
* Ha a felhasználó képes eszközök szinkronizálása ki van kapcsolva, majd a beállítások adatainak egyike fogják elérni, és az adott felhasználó összes beállítások adatainak elavult fog válni, és előfordulhat, hogy a megőrzési időszak letelte után lehet törölni. 
* Ha az Azure Active directory-rendszergazda kikapcsolja a vállalati Állapothordozás a teljes címtár, majd az összes felhasználót, hogy a könyvtár a beállítások szinkronizálása leáll, az összes felhasználó számára minden beállítások adatainak elavult fog válni, és előfordulhat, hogy a megőrzési időszak letelte után lehet törölni. 

### <a name="deleted-data-recovery"></a>A törölt adatok helyreállítása
Az adatmegőrzési házirend nem lehet konfigurálni. Az adat véglegesen törlődik, ha nincs helyreállítható. A beállítások adatainak azonban csak az Azure, a végfelhasználói eszközről nem törlődik. A vállalati Állapothordozás szolgáltatás később újracsatlakozik bármely olyan eszközről, ha a beállítások újra szinkronizálva és az Azure-ban tárolja.

## <a name="related-topics"></a>Kapcsolódó témakörök
* [A vállalati Állapothordozás áttekintése](active-directory-windows-enterprise-state-roaming-overview.md)
* [Beállítások és adatok hordozása – gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Szinkronizálási beállítások házirendet és az MDM-beállításai](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 központi beállításainak ismertetése](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [hibaelhárítással](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
