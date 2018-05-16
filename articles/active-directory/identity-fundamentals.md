---
title: Azure-identitás kezelése – alapok
description: Felhőalapú Identitások is keresztül irányítást tarthat fenn a legjobban, és betekintést, hogyan és mikor történjen a felhasználók férhetnek hozzá a vállalati alkalmazásokhoz és adatokhoz.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 7c00ccf214cfd683d04a76cc3cfad9bd27080561
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="fundamentals-of-azure-identity-management"></a>Azure-identitás kezelése – alapok

Több vállalati digitális erőforrások élő a felhőben, és az eszközökön a vállalati hálózaton kívül, felhőalapú identitás- és hozzáférés felügyeleti kiválóan áttelepítésére válik. Felhőalapú Identitások is keresztül irányítást tarthat fenn a legjobban, és betekintést, hogyan és mikor történjen a felhasználók férhetnek hozzá a vállalati alkalmazásokhoz és adatokhoz.

Microsoft rendelkezik lett felhőalapú identitásainak keresztül egy évtizedben és védelme, a [Azure Active Directory (AD)](active-directory-whatis.md), ezek azonos védelmi rendszerek nem elérhető. Az Azure ad-vel a vállalati rendszergazdák könnyen arról, hogy felhasználói és rendszergazdai elszámolási kötelezettségéről szóló nagyobb biztonsággal és irányítási eddiginél.

Prémium szintű Azure AD egy olyan felhőalapú identitás- és hozzáférés felügyeleti megoldás speciális védelem képességekkel bővült, amely lehetővé teszi az összes olyan alkalmazáshoz, identity protection egy biztonságos identitás (továbbfejlesztett által a [Microsoft eszközintelligencia biztonsági diagramot](https://www.microsoft.com/en-us/security/intelligence)), és a Privileged Identity Management. Nem csupán egy másik figyelés vagy jelentéskészítési eszköz, prémium szintű Azure AD a felhasználói identitások valós idejű megvédheti, illetve lehetővé teszik a szervezet adatvédelmi kockázata, adaptív hozzáférési házirendeket hozhat létre.

A rövid videó az Azure AD identity kezelésén és védelmén gyors áttekintést:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

A Microsoft csak nem biztosít az identitás, amely everywhere, hanem eszközöket automatizálását, biztonságossá, és a kezelése informatikai szervezeten belül. A felhőalapú megoldások bevezetése után továbbra is fennáll igény szerinti kezelhetik és szabályozhatják informatikai feladatokat, mint a felhasználói jelszavak alaphelyzetbe állítása, felhasználói csoportok kezelése és alkalmazáskérelmeinek ügyfélszolgálathoz beérkező hívások száma. Dolgot további része, alkalmazottak vannak most, hogy a saját eszközök munka, és azonnal elérhetők legyenek SaaS-alkalmazások használatával. Így az alkalmazások karbantartása szabályozhatják vállalati adatközpontban és a nyilvános felhő platform között jelentős kihívást.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>A helyszíni Active Directory és az Azure AD Connect és az Office 365
A szervezetek, amelyek nagy beruházások történtek a helyszíni Active Directoryban a ezeket a felhőbe beruházások kibővítheti a helyszíni címtárak integrálása az Azure AD- [hibrid Identitáskezelés](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Így a felhasználók hatékonyabb, adja meg a közös identitás függetlenül helyétől erőforrások eléréséhez. Felhasználók és a szervezetek segítségével egyszeri bejelentkezési (SSO) is a helyszíni erőforrásokhoz férnek hozzá, és a felhőalapú szolgáltatások, például az Office 365.

[Az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) le kell töltenie az integráció végzett egyetlen eszköze. Az Azure AD Connect az identitás szinkronizálási igényeinek megfelelő képességeket biztosít, és olyan például a DirSync és Azure AD Sync identitásintegrációs eszközök régebbi verzióit váltja fel. Az Azure AD Connect az Identitáskezelés és a helyszíni és az Azure AD közötti szinkronizálás keresztül engedélyezve van:

- Szinkronizálás – ez az összetevő a felhasználók, csoportok és egyéb objektumok létrehozásáért felelős. Segítségével arról is meggyőződhet, hogy a helyszíni felhasználókhoz és csoportokhoz tartozó identitásadatok megegyeznek a felhőben található hasonló adatokkal. Jelszóvisszaírás is engedélyezhető a helyszíni címtárak szinkronban tartsa a felhasználó frissítését a jelszavát az Azure ad-ben.
- Az AD FS - összevonási egy nem kötelező funkció az Azure AD Connect, amelyek segítségével egy helyszíni használó hibrid környezetet konfigurálhat által biztosított az AD FS infrastruktúra. Összevonási szervezetek összetett üzembe helyezések, például az egyszeri bejelentkezés, AD bejelentkezési házirend, és az intelligens kártya és a harmadik féltől származó MFA használható.
- Állapotfigyelés – [az Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) is hatékony megfigyelési képességgel rendelkezik, és adjon meg egy központi helyen, az Azure portálon az ilyen tevékenységek megtekintéséhez.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>A termelékenységet, és csökkenti az ügyfélszolgálati kiadásokat az önkiszolgáló és az egyszeri bejelentkezést

Az alkalmazottak is hatékonyabb, ha egyetlen felhasználónév és jelszó megjegyzése és egy egységes felhasználói élményt, minden eszközön. Akkor is időmegtakarítás mikor hajthat végre olyan feladatokat, például [elfelejtett jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) vagy alkalmazáshoz való hozzáférés kérése nélkül várakozik a segélyszolgálat segítségét.

Az Azure AD [kiterjeszti a helyszíni Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) kiterjeszti a felhőbe, hogy a felhasználók használhatják a saját elsődleges szervezeti mind a tartományhoz csatlakozó eszközök, a vállalati erőforrásokhoz, és minden a web- és SaaS-alkalmazásokhoz szükségük segítségével a munkájuk elvégzéséhez. Mellett nem hogy meg kellene jegyezni a felhasználónevek és jelszavak több példányban, alkalmazás hozzáférést is lehet automatikusan kiépítve (vagy rendszer leépíti) szervezet csoporttagságot és állapotuk egy alkalmazott alapján. Segítségével ellenőrizheti, hogy gyűjtemény alkalmazások vagy a saját helyszíni alkalmazások, amelyek már fejlesztve és közzétéve, és a [az Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Kezelése és a vállalati erőforrásokhoz való hozzáférés szabályozása
Microsoft identitások és hozzáférések felügyeleti megoldások Súgó informatikai védelme alkalmazásokhoz és erőforrásokhoz való hozzáférést az vállalati adatközpontban és a felhőben, engedélyezése, mint az érvényesítési további szintek [a multi-factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)és [feltételes hozzáférési házirendek](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Gyanús tevékenységek figyelése keresztül speciális biztonsági jelentések, a naplózás és a riasztási segít mérsékelni a potenciális biztonsági problémákat.

Feltételes hozzáférési szabályzatokat az Azure AD Premium adjon meg, a vállalati rendszergazda, képes létrehozni a csoportházirend-alapú hozzáférési szabályok bármely Azure AD-kompatibilis alkalmazás (SaaS-alkalmazásokhoz, a felhő vagy a helyszíni webalkalmazások futó egyéni alkalmazások). Az Azure AD ezek a szabályzatok valós időben értékeli ki, és érvénybe lépteti őket, amikor egy felhasználó próbál csatlakozni egy alkalmazáshoz. Azure-identitás adatvédelmi szabályzatok lehetővé teszik automatikusan hajtsa végre a műveletet, ha gyanús tevékenységet észlel. Ezek a műveletek blokkolja a hozzáférést a felhasználóknak a magas kockázatú, többtényezős hitelesítés kényszerítése, és felhasználói jelszavak alaphelyzetbe állítását, ha úgy tűnik, hitelesítő adatok sérültek lehetnek.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory emelt szintű identitáskezelés

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), az Azure Active Directory Premium P2 elérhető részét képező lehetővé teszi annak megállapításához, korlátozza, és figyelje a rendszergazdai fiókok és az Azure Active Directory és más Microsoft-erőforrásokhoz való hozzáférés Online szolgáltatások. Emellett segítséget nyújt kell pontos időn igény rendszergazdai hozzáférés felügyeletéhez.

A privileged Identity Management kényszerítheti igény rendszergazdai jogosultsággal, hogy a rendszergazdák lekérdezhetik-e többtényezős hitelesített, ideiglenes jogok kiterjesztése az előre megadott időszakokra, mielőtt azok a fiókok térjen vissza a normál felhasználói állapot.

## <a name="benefits-of-azure-identity"></a>Azure-identitás előnyei

Az Azure-identitás-kezelés segítségével:

-   Létrehozásához és kezeléséhez minden felhasználó egy egyetlen identitást a teljes vállalat, felhasználók, csoportok és az eszközök szinkronban tartása [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Adjon meg az egyszeri bejelentkezéses hozzáférést több ezer előre integrált Szolgáltatottszoftver-alkalmazásoknál, beleértve az alkalmazások vagy a helyszíni használó SaaS-alkalmazások biztonságos távoli hozzáférést biztosítanak a [az Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Alkalmazás-hozzáférési biztonsági engedélyezése szabályalapú kényszerítésével [multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) a helyszíni és felhőalapú alkalmazások.

-   A felhasználói termelékenység növelése [önkiszolgáló jelszó-átállítási](https://docs.microsoft.com/azure/active-directory/active-directory-passwords), és a csoport, és a hozzáférési kérelmek történő alkalmazása a [MyApps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Kihasználja a [magas rendelkezésre állása és megbízhatósága](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) egy világszerte, vállalati szintű, a felhőalapú identitás- és hozzáférés felügyeleti megoldás.

## <a name="next-steps"></a>További lépések
[További tudnivalók az Azure identitáskezelési megoldásairól](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)