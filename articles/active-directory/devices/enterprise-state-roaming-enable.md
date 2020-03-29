---
title: A vállalati állapothordozás engedélyezése az Azure Active Directoryban
description: Gyakori kérdések a Vállalati állapotroaming beállításairól a Windows-eszközökön.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d6266f5ea8cdd4f75d0449cb49852e71c905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672393"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>A vállalati állapothordozás engedélyezése az Azure Active Directoryban

A vállalati állapotroaming minden Olyan szervezet számára elérhető, amely rendelkezik Azure AD Premium vagy Enterprise Mobility + Security (EMS) licenccel. Az Azure AD-előfizetés betöltésével kapcsolatos további információkért tekintse meg az [Azure AD termékoldalát.](https://azure.microsoft.com/services/active-directory)

Ha engedélyezi a vállalati szintű barangolást, a szervezet automatikusan kap egy ingyenes, korlátozott használatú licencet az Azure Rights Management azure-beli tartalomvédelmi védelméhez az Azure Information Protection szolgáltatással szemben. Ez az ingyenes előfizetés a vállalati beállítások és az Enterprise State Roaming által szinkronizált alkalmazásadatok titkosítására és visszafejtésére korlátozódik. Az Azure Rights Management szolgáltatás teljes képességeinek használatához [fizetős előfizetéssel](https://azure.microsoft.com/pricing/details/information-protection/) kell rendelkeznie.

> [!NOTE]
> Ez a cikk a 2015 júliusában Windows 10 rendszerrel indított Microsoft Edge Legacy HTML-alapú böngészőre vonatkozik. A cikk nem vonatkozik az új Microsoft Edge Chromium-alapú böngésző re január 15-én, 2020. Az új Microsoft Edge szinkronizálási viselkedéséről további információt a Microsoft Edge Sync című cikkben [talál.](/deployedge/microsoft-edge-enterprise-sync)

## <a name="to-enable-enterprise-state-roaming"></a>Vállalati állapotroaming engedélyezése

1. Jelentkezzen be az [Azure AD felügyeleti központba.](https://aad.portal.azure.com/)
1. Válassza az **Azure Active Directory** &gt; **eszközök** &gt; **vállalati szintű központi szolgáltatásának kiválasztása**lehetőséget.
1. A Felhasználók a **beállításokat és az alkalmazásadatokat az eszközök között szinkronizálhatják.** További információt az [eszközbeállítások konfigurálása című](/azure/active-directory/device-management-azure-portal)témakörben talál.
  
   ![Az eszközbeállítás képe: A felhasználók szinkronizálhatják a beállításokat és az alkalmazásadatokat az eszközök között](./media/enterprise-state-roaming-enable/device-settings.png)
  
Ahhoz, hogy egy Windows 10-es eszköz használhassa a vállalati állapotroaming szolgáltatást, az eszköznek hitelesítenie kell magát egy Azure AD-identitás használatával. Az Azure AD-hez csatlakozott eszközök esetében a felhasználó elsődleges bejelentkezési identitása az Azure AD-identitás, így nincs szükség további konfigurációra. A helyszíni Active Directoryt használó eszközök esetében a rendszergazdának [konfigurálnia kell a hibrid Azure Active Directoryhoz csatlakozó eszközöket.](hybrid-azuread-join-manual-steps.md) 

## <a name="data-storage"></a>Adattárolás

A vállalati állapotú barangolásos adatok egy vagy több [Azure-régióban](https://azure.microsoft.com/regions/) vannak tárolva, amelyek a legjobban illeszkednek az Azure Active Directory-példányban beállított ország-/régió értékhez. A vállalati állami barangolásos adatok particionálva három fő földrajzi régió alapján történik: Észak-Amerika, Az EMEA és az APAC. A bérlő vállalati állapotszerinti barangolási adatai helyileg találhatók a földrajzi régióval, és nem replikálódik a régiók között.  Példa:

| Ország/régió értéke | adataik a |
| -------------------- | ------------------------ |
| Az EMEA olyan országa/régiója, mint Franciaország vagy Zambia | Egy vagy több Azure-régió Európán belül |
| Észak-amerikai ország/régió, például Egyesült Államok vagy Kanada | Egy vagy több Azure-régió az Egyesült Államokban |
| Egy APAC ország/régió, például Ausztrália vagy Új-Zéland | Egy vagy több Azure-régió Ázsián belül |
| Dél-amerikai és antarktiszi régiók | Egy vagy több Azure-régió az Egyesült Államokban |

Az ország/régió értéke az Azure AD könyvtár létrehozási folyamat részeként van beállítva, és később nem módosítható. Ha további részletekre van szüksége az adattárolási helyről, nyújtson be egy jegyet az [Azure-támogatással.](https://azure.microsoft.com/support/options/)

## <a name="view-per-user-device-sync-status"></a>Felhasználónkénti eszköz szinkronizálási állapotának megtekintése

Az alábbi lépésekkel megtekintheti a felhasználónkénti eszközszinkronizálási állapotjelentést.

1. Jelentkezzen be az [Azure AD felügyeleti központba.](https://aad.portal.azure.com/)
1. Válassza az **Azure Active Directory** &gt; **felhasználói minden** &gt; **felhasználó lehetőséget.**
1. Jelölje ki a felhasználót, majd válassza **az Eszközök**lehetőséget.
1. A **Megjelenítés**csoportban válassza **az Eszközök szinkronizálási beállításait és az alkalmazásadatokat** a szinkronizálási állapot megjelenítéséhez.
  
   ![Eszközszinkronizálási adatbeállítás képe](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Ha vannak olyan eszközök szinkronizálása, a felhasználó, akkor az eszközök az itt látható módon.
  
   ![eszköz szinkronizálási oszlopadatok képe](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Adatmegőrzés

A Microsoft-felhőbe az Enterprise State Roaming használatával szinkronizált adatok mindaddig megmaradnak, amíg manuálisan nem törlik őket, vagy amíg a kérdéses adatok elavultnak nem minősülnek. 

### <a name="explicit-deletion"></a>Explicit törlés

Explicit törlés, ha egy Azure-rendszergazda töröl egy felhasználót vagy egy könyvtárat, vagy más módon kéri kifejezetten, hogy az adatokat törölni kell.

* **Felhasználó törlése:** Ha egy felhasználó törlődik az Azure AD-ben, a felhasználói fiók roaming adatok törlődnek 90-180 nap után. 
* **Címtár törlése:** Egy teljes könyvtár törlése az Azure AD-ben azonnali művelet. Az adott könyvtárhoz társított összes beállítási adat 90–180 nap elteltével törlődik. 
* **Kérésre törlés:** Ha az Azure AD-rendszergazda manuálisan szeretné törölni egy adott felhasználó adatait vagy beállítási adatait, a rendszergazda benyújthat egy jegyet az [Azure-támogatással.](https://azure.microsoft.com/support/) 

### <a name="stale-data-deletion"></a>Elavult adatok törlése

Az egy éve nem elérhető adatokat ("a megőrzési időszakot") a rendszer elavultként kezeli, és törlődhet a Microsoft-felhőből. A megőrzési időszak változhat, de nem lehet kevesebb, mint 90 nap. Az elavult adatok lehetnek a Windows/alkalmazás beállításainak egy adott készlete, vagy a felhasználó összes beállítása. Példa:

* Ha egyetlen eszköz sem fér hozzá egy adott beállításgyűjteményhez (például egy alkalmazást eltávolítanak az eszközről, vagy egy beállítási csoport, például a "Téma" le van tiltva a felhasználó összes eszközén), akkor a gyűjtemény a megőrzési időszak után elavulttá válik, és törölhető. . 
* Ha egy felhasználó kikapcsolta a beállítások szinkronizálását az összes eszközén, akkor a rendszer nem éri el a beállítási adatokat, és az adott felhasználó összes beállítási adatai elavulttá válnak, és az adatmegőrzési időszak után törlődhetnek. 
* Ha az Azure AD címtár rendszergazdája kikapcsolja a vállalati állapotroamingot a teljes címtárban, akkor a címtárban lévő összes felhasználó leállítja a beállítások szinkronizálását, és az összes felhasználó összes beállítási adata elavulttá válik, és az adatmegőrzési időszak után törlődhet. 

### <a name="deleted-data-recovery"></a>Törölt adathelyreállítás

Az adatmegőrzési házirend nem konfigurálható. Az adatok végleges törlése után nem állíthatók helyre. A beállítási adatok azonban csak a Microsoft-felhőből törlődnek, a végfelhasználói eszközről nem. Ha később bármelyik eszköz újra csatlakozik a vállalati állapotroaming szolgáltatáshoz, a beállítások szinkronizálása és tárolása a Microsoft-felhőben történik.

## <a name="next-steps"></a>További lépések

* [Vállalati állapothordozás áttekintése](enterprise-state-roaming-overview.md)
* [Beállítások és adatroaming GYIK](enterprise-state-roaming-faqs.md)
* [Csoportházirend és MDM-beállítások a beállítások szinkronizálásához](enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 roaming beállítások referenciája](enterprise-state-roaming-windows-settings-reference.md)
* [hibaelhárítással](enterprise-state-roaming-troubleshooting.md)
