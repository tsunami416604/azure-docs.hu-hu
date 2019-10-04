---
title: Enterprise State Roaming engedélyezése a Azure Active Directoryban | Microsoft Docs
description: Gyakori kérdések a Windows-eszközök Enterprise State Roaming beállításairól.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f4a260bb52fb10147f6d6b9e74aa5cd4fd0e1a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562145"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>A vállalati állapothordozás engedélyezése az Azure Active Directoryban
Enterprise State Roaming bármely szervezet számára elérhető prémium szintű Azure AD vagy Enterprise Mobility + Security (EMS) licenccel. Az Azure AD-előfizetés beszerzésével kapcsolatos további információkért tekintse meg az [Azure ad termékét ismertető oldalt](https://azure.microsoft.com/services/active-directory).

A Enterprise State Roaming engedélyezésekor a szervezet automatikusan ingyenes, korlátozott használati licencet kap az Azure Rights Management Protection számára a Azure Information Protectionból. Ez az ingyenes előfizetés a vállalati beállítások és az Enterprise State Roaming által szinkronizált alkalmazásadatok titkosítására és visszafejtésére korlátozódik. Az Azure Rights Management [](https://azure.microsoft.com/pricing/details/information-protection/) szolgáltatás teljes képességeinek használatához fizetős előfizetéssel kell rendelkeznie.

## <a name="to-enable-enterprise-state-roaming"></a>A Enterprise State Roaming engedélyezése

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com/)központba.
1. Válassza ki **Azure Active Directory** &gt; **eszközök** &gt; **Enterprise State roaming**.
1. Válassza ki **, hogy a felhasználók szinkronizálják a beállításokat és az alkalmazásadatok az eszközök között**. További információ: [az eszközbeállítások konfigurálása](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![a felhasználók által címkézett eszközbeállítások képe szinkronizálhatja a beállításokat és az alkalmazásadatok az eszközök között](./media/enterprise-state-roaming-enable/device-settings.png)
  
Ahhoz, hogy egy Windows 10-es eszköz használhassa a Enterprise State Roaming szolgáltatást, az eszköznek az Azure AD-identitás használatával kell hitelesítenie magát. Az Azure AD-hez csatlakozó eszközök esetében a felhasználó elsődleges bejelentkezési identitása az Azure AD-identitás, így nincs szükség további konfigurálásra. A helyszíni Active Directoryt használó eszközökön a rendszergazdának [be kell állítania a hibrid Azure Active Directory csatlakoztatott eszközöket](hybrid-azuread-join-manual-steps.md). 

## <a name="data-storage"></a>Adattárolás
Enterprise State Roaming az adatai egy vagy több olyan [Azure-régióban](https://azure.microsoft.com/regions/) vannak tárolva, amelyek a legjobban megfelelnek a Azure Active Directory példányban beállított ország/régió értéknek. Enterprise State Roaming az adatai három fő földrajzi régió alapján vannak particionálva: Észak-Amerika, EMEA és APAC. Enterprise State Roaming a bérlői adatai helyileg találhatók a földrajzi régióval, és nem replikálódnak a régiók között.  Példa:

| Ország/régió érték | a szolgáltatásban tárolt |
| -------------------- | ------------------------ |
| EMEA ország/régió, például Franciaország vagy Zambia | Egy vagy több Európában található Azure-régió |
| Egy észak-amerikai ország/régió, például Egyesült Államok vagy Kanada | Egy vagy több Azure-régió az Egyesült Államok területén |
| Egy APAC ország/régió, például Ausztrália vagy Új-Zéland | Egy vagy több, az Ázsiában található Azure-régió |
| Dél-amerikai és Antarktisz-régiók | Egy vagy több Azure-régió az Egyesült Államokon belül |

Az ország/régió érték az Azure AD-címtár létrehozási folyamatának részeként van beállítva, ezért nem módosítható. Ha további információra van szüksége az adattárolási helyéről, az [Azure](https://azure.microsoft.com/support/options/)-támogatással rendelkező jegyet kell bemutatnia.

## <a name="view-per-user-device-sync-status"></a>Felhasználónkénti eszköz szinkronizálási állapotának megtekintése
Az alábbi lépéseket követve megtekintheti a felhasználónkénti eszköz szinkronizálási állapotáról szóló jelentést.

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com/)központba.
1. Válassza **Azure Active Directory** &gt; minden&gt; **felhasználó**lehetőséget.
1. Válassza ki a felhasználót, majd válassza az **eszközök**lehetőséget.
1. A **Megjelenítés**területen válassza ki a **beállítások és alkalmazásadatok szinkronizálása** a szinkronizálási állapot megjelenítéséhez eszközt.
  
   ![az eszköz-szinkronizálási adatok beállításának képe](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Ha a felhasználóhoz eszközök vannak szinkronizálva, az itt látható módon látja az eszközöket.
  
   ![eszköz szinkronizálási oszlopos adatok képe](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Adatmegőrzés
A Microsoft Cloud-hoz a Enterprise State Roaming használatával szinkronizált adat megmarad mindaddig, amíg manuálisan nem törlik, vagy amíg a kérdéses adat elavultnak nem lett meghatározva. 

### <a name="explicit-deletion"></a>Explicit törlés
Az explicit törlés akkor történik meg, amikor egy Azure-rendszergazda töröl egy felhasználót vagy könyvtárat, vagy ha explicit módon kéri az adattörlést.

* **Felhasználó törlése**: Ha töröl egy felhasználót az Azure AD-ben, a rendszer 90 – 180 nap után törli a felhasználói fiók barangolási szolgáltatását. 
* **Könyvtár törlése**: Egy teljes címtár törlése az Azure AD-ben azonnali művelet. A címtárhoz társított összes beállítási adattal 90 és 180 nap után törlődnek. 
* **Kérelem törlésekor**: Ha az Azure AD-rendszergazda manuálisan szeretné törölni egy adott felhasználó adatelemeit vagy beállításait, a rendszergazda az [Azure](https://azure.microsoft.com/support/)-támogatással jegyet tud beküldeni. 

### <a name="stale-data-deletion"></a>Elavult adattörlés
Az egy évig nem elért adatok ("a megőrzési időszak") elavultnak minősülnek, és törölhetők a Microsoft Cloud-ból. A megőrzési időtartam változhat, de nem lesz kevesebb, mint 90 nap. Az elavult adatmennyiség lehet a Windows/Alkalmazásbeállítások vagy a felhasználó összes beállítása. Példa:

* Ha egyetlen eszköz sem fér hozzá egy adott beállítási gyűjteményhez (például egy alkalmazás el lesz távolítva az eszközről, vagy egy beállításcsoport, például a "téma" le van tiltva az összes felhasználó eszközén), akkor a gyűjtemény elavult lesz a megőrzési időszak és a törlés után. . 
* Ha a felhasználó minden eszközén kikapcsolta a beállítások szinkronizálását, akkor a rendszer nem fér hozzá a beállításokhoz, és az adott felhasználóhoz tartozó összes beállítási információ elavult lesz, és a megőrzési időszak után törölhető. 
* Ha az Azure AD-címtár rendszergazdája kikapcsolja Enterprise State Roaming a teljes címtárban, akkor az abban a könyvtárban lévő összes felhasználó leállítja a beállítások szinkronizálását, és az összes felhasználóra vonatkozó összes beállítás elavult lesz, és a megőrzési időszak után törölve lehet. 

### <a name="deleted-data-recovery"></a>Az Adathelyreállítás törölve
Az adatmegőrzési szabályzat nem konfigurálható. Az adattörlést követően nem lehet helyreállítani az adatvesztést. A beállítások adatait azonban csak a Microsoft-felhőből, nem pedig a végfelhasználói eszközről törli a rendszer. Ha bármelyik eszköz később újracsatlakozik a Enterprise State Roaming szolgáltatáshoz, a beállítások újra szinkronizálhatók és tárolódnak a Microsoft Cloud-ban.

## <a name="next-steps"></a>További lépések

* [Enterprise State Roaming áttekintése](enterprise-state-roaming-overview.md)
* [Beállítások és adatroaming – gyakori kérdések](enterprise-state-roaming-faqs.md)
* [A beállítások szinkronizálásának Csoportházirend és MDM beállításai](enterprise-state-roaming-group-policy-settings.md)
* [A Windows 10 barangolási beállításainak ismertetése](enterprise-state-roaming-windows-settings-reference.md)
* [Hibaelhárítás](enterprise-state-roaming-troubleshooting.md)
