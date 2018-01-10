---
title: "Ismerkedés az Azure Active Directory |} Microsoft Docs"
description: "Licencet lekérni, tartománynév hozzáadása, hozzon létre egyéni bejelentkezési oldal és vegye fel az önkiszolgáló jelszó-változtatási az Azure Active Directoryban"
keywords: 
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.prod: 
ms.service: active-directory
ms.workload: identity
ms.technology: 
ms.assetid: 
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: 9e1a7337c2477455aa0b56f2147f46f3bf5293a8
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-ad"></a>Ismerkedés az Azure AD szolgáltatással
Az Identitáskezelés a modern szükséges méretezhető, egységes reliablity annak biztosítása érdekében az alkalmazások és szolgáltatások csak a hitelesített felhasználók elérhetőségét. A felhasználók, az informatikai részlegnek identity management igényeinek megfelelően támogatásához oly módon, mint egy szolgáltatott szoftverként (SaaS) alkalmazások jóváhagyott, nyilvános szoftver elérésére, a gazdagép belső üzletági alkalmazások, és a még akkor is, módjairól a helyszíni alkalmazások fejlesztéséhez és használati. Az összes követelménynek mutasson a felhőalapú identitás-kezelési megoldás a szükséges.      

Azure Active Directory (Azure AD) érték a Microsoft által a több-bérlős, a felhő alapú címtár- és identitáskezelési felügyeleti szolgáltatás. Az Azure AD directory alapszolgáltatásokat, speciális identitás irányítás és alkalmazáshozzáférés-kezeléshez egyesíti. A több-bérlős, földrajzilag elosztott, magas rendelkezésre állású kialakítása az Azure AD azt jelenti, hogy támaszkodhat a kritikus fontosságú üzleti igényeknek.

Az Azure AD identitáskezelési funkciói szinkronizálhatók a helyi erőforrás adatai, beleértve a teljes csomag magában foglalja testreszabható vállalati arculat megjelenítése, egyszerű licenckezeléssel és még önkiszolgáló jelszókezelés. Ezek a könnyen telepíthető képességek segítségével Ismerkedés az Azure AD segítségével biztonságos felhőalapú alkalmazások, IT-folyamatok egyszerűsítésére, költségeket, és biztosítja, hogy a vállalati megfelelőség célok teljesülnek-e.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

Ez a cikk többi megtudhatja, első lépések az Azure ad-val. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Prémium szintű Azure Active Directory – Regisztráció
A [Ismerkedés a prémium szintű Azure Active Directory (Azure AD)](active-directory-get-started-premium.md), licencet vásárolt, és rendelje hozzá őket az Azure-előfizetéshez. Ha létrehoz egy új Azure-előfizetéssel, is szüksége lesz aktiválni a licenccsomaghoz és az Azure AD szolgáltatás-hozzáférés. 

A Prémium szintű Active Directory-kiadásra történő regisztrálásra több lehetősége van: 

- Az Azure- vagy Office 365-előfizetés használata
- Enterprise Mobility + Security licenccsomag használata
- Microsoft mennyiségi licenccsomag használata

### <a name="verification-step"></a>Ellenőrzési lépés
Miután aktiválta az előfizetést, győződjön meg arról, hogy be tud jelentkezni a szolgáltatás.

## <a name="add-a-custom-domain-name"></a>Egyéni tartománynév hozzáadása
Minden Azure AD-címtárral rendelkezik egy kezdeti tartománynevet formájában *tartománynév*. onmicrosoft.com. A kezdeti tartománynév nem módosítható vagy törölhető, de is [vállalati tartománynév hozzáadása az Azure AD](add-custom-domain.md). A szervezet például valószínűleg más üzleti és a felhasználók, akik jelentkezzen be vállalati tartománynév használt tartománynevek rendelkezik. Egyéni tartománynevek hozzáadása az Azure AD lehetővé teszi a felhasználóneveket rendeljen a könyvtárat, amelyek a felhasználók számára, mint "alice@contoso.com." Ahelyett, hogy "alice@.onmicrosoft.com". A folyamat egyszerűen végrehajtható:

1. Az egyéni tartománynév hozzáadása a címtárhoz
2. Vegye fel a DNS-bejegyzést a tartománynévhez a tartománynév-regisztrálónál
3. Az egyéni tartománynév ellenőrzése az Azure AD-ben

### <a name="verification-step"></a>Ellenőrzési lépés
Egyéni tartomány hozzáadása után győződjön meg arról, hogy a **ellenőrizve** állapot jelenik meg a **egyéni tartománynevek** panel az Azure AD portálon.

## <a name="add-company-branding-to-your-sign-in-page"></a>Vállalati arculat megjelenítése a bejelentkezési oldal 
A félreértések elkerülése végett számos vállalat igyekszik egységes megjelenést adni az általa kezelt összes webhelynek és szolgáltatásnak. Azure Active Directory (Azure AD) nyújt ez a funkció azáltal, hogy [szabja testre a bejelentkezési lapot a vállalat emblémájának elhelyezésével és egyéni színsémák](customize-branding.md). A bejelentkezési oldal az Office 365 vagy más webes alkalmazásokat az identitás-szolgáltatóként az Azure AD-t használó bejelentkezéskor megjelenő lap. Ön a szolgáltatóosztályokkal ezen a lapon adja meg a hitelesítő adatokat.

### <a name="verification-step"></a>Ellenőrzési lépés
Jelentkezzen be az Azure-portálon, és győződjön meg arról, hogy a testreszabott bejelentkezési oldalára, és minden további nyelvi testre szabott elem van-e beállítva megfelelően. 

## <a name="add-new-users"></a>Új felhasználók hozzáadása
Is [új felhasználók hozzáadása a szervezet Azure AD](add-users-azure-active-directory.md) egyet az Azure portál használatával egyszerre, vagy a helyi Windows Server AD erőforrás adatok szinkronizálása. Közvetlenül az Azure AD-portálhoz a felhőalapú felhasználók hozzáadása, vagy a helyi felhasználói adatok szinkronizálása.

Annak engedélyezéséhez, hogy a helyszíni identitás szinkronizálhasson az Azure AD-val, telepítenie és konfigurálnia kell az [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) programot szervezete egyik kiszolgálójára. Ez az alkalmazás kezeli a felhasználók és csoportok szinkronizálását a létező identitásforrásról az Azure AD-bérlővel.

### <a name="verification-step"></a>Ellenőrzési lépés
Miután létrehozott vagy az új felhasználók szinkronizálása, ellenőrizze, hogy jelenjenek meg az Azure AD-e.

## <a name="assign-licenses"></a>Licencek hozzárendelése
Bár megszerezni egy előfizetés fizetett képességek konfigurálnia kell az összes, Ön továbbra is kell [felhasználói licencek hozzárendelése](license-users-groups.md) szolgáltatások fizetős Azure AD Premium. Minden olyan felhasználó, aki kell rendelkezik hozzáféréssel, vagy ki kezeli, az Azure AD a szolgáltatás fizetős hozzá kell rendelni a licencet. A licenc-hozzárendelést a felhasználó és a megvásárolt szolgáltatás, például az Azure AD Premium, a Basic, vagy a nagyvállalati mobilitási + biztonsági közötti társítás.

A csoport-alapú licenc-hozzárendelést segítségével többek között a következő példákban szabályok beállítása:

- A címtárban szereplő összes felhasználó automatikusan licenc beszerzése
- Rendelkezik a megfelelő beosztás lekérdezi a licenc
- A szervezet más kezelői döntési delegálhatja (önkiszolgáló csoportok használatával)

### <a name="verification-step"></a>Ellenőrzési lépés
Hozzárendelt áttekintése és a rendelkezésre álló licencek **Azure Active Directory** > **licencek** > **összes**.

## <a name="configure-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás konfigurálása
[Az önkiszolgáló jelszó-változtatási (SSPR)](active-directory-passwords-getting-started.md) nyújt egy egyszerű azt jelenti, hogy a rendszergazdák engedélyezése a felhasználók visszaállíthassák vagy a jelszavak és a fiókok zárolásának feloldásához. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhasználatáról, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre.

### <a name="verification-step"></a>Ellenőrzési lépés
Tekintse át az önkiszolgáló jelszó-Változtatási tulajdonságok engedélyezve **Azure Active Directory** > **jelszó-átállítási** annak érdekében, hogy a megfelelő felhasználói és a csoport-hozzárendelések lettek bevezetve. 


## <a name="next-steps"></a>További lépések
[Az Azure Active Directory szolgáltatás lap](https://azure.microsoft.com/services/active-directory/)

[Az Azure Active Directory árképzési adatai lap](https://azure.microsoft.com/pricing/details/active-directory/)