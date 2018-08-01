---
title: Alkalmazások kezelése az Azure Active Directory |} A Microsoft Docs
description: Ez a cikk az Azure Active Directory integrálása a helyszíni, felhőbeli és a SaaS-alkalmazások előnyeit.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: bf53829a2d2578132f9a3595c0bac5e8eb588916
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366771"
---
# <a name="managing-applications-with-azure-active-directory"></a>Alkalmazások kezelése az Azure Active Directory
A tényleges munkafolyamatot vagy a tartalom túli vállalkozások követelményei két alapvető minden alkalmazás:

1. A hatékonyság növelésére, alkalmazások könnyedén felderíthetők és hozzáférési kell lennie
2. Biztonsági és cégirányítási engedélyezéséhez a szervezet igényeinek és igény keresztül férhet hozzá, és ténylegesen hozzáférő mindegyik alkalmazás

A világon, felhőalapú alkalmazások, Ez leginkább tehető vezérlési személyazonosságát "*akik számára engedélyezett mi*."

A számítástechnikai szakkifejezéseket tartalmazó:

* *Akik* néven *identitás* – a felhasználók és csoportok kezelése
* *Mi* néven *hozzáférés-kezelés* – a védett erőforrásokhoz való hozzáférés kezelése

Mindkét összetevő együtt nevezzük *identitás- és hozzáférés-kezelés (IAM)*, amely úgy van definiálva a [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) csoportot "*, amely lehetővé teszi a megfelelő személyek számára, hogy a biztonsági fegyelem a jobb oldalon a megfelelő erőforrások eléréséhez a megfelelő okokból alkalommal*".

Oké, tehát mi is a probléma? Ha a IAM *nem felügyelt* egy helyen az integrált megoldást:

* Identitáskezelő adminisztrátorokat külön-külön létrehozni, és külön-külön frissíteni a felhasználói fiókok a minden alkalmazás rendelkezik egy redundáns és időigényes feladatnak tevékenységet.
* Felhasználók részről hozzáférni az alkalmazásokhoz való együttműködéshez szükséges több hitelesítő adatokkal rendelkeznek. Ennek eredményeképpen felhasználók írja le a jelszavukat, vagy használjon más jelszót felügyeleti megoldások általában. Ezek az alternatívák vezeti be más adatok biztonsági kockázatokat.
* Redundáns, időigényes tevékenységek csökkentse a felhasználók, és a rendszergazdák csak a működő az üzleti tevékenységeket, amelyek növelik a vállalat nyereségét.

Tehát mi általában megakadályozza, hogy a szervezetek az IAM által szállított integrált megoldások bevezetése?

* Legtöbb technikai megoldásokat kell igazítani a saját alkalmazások minden egyes szervezet és telepített szoftverek platformokon alapulnak.
* A felhőalkalmazások gyakran, mint az IT-szervezet integrálható a meglévő IAM-megoldások gyorsabb ütemben fogadnak el.
* Biztonsággal és figyeléssel eszközök további testreszabási és integrációs átfogó E2E forgatókönyvek eléréséhez van szükség.

## <a name="azure-active-directory-integrated-with-applications"></a>Az Azure Active Directory integrált alkalmazásokat
Azure Active Directory egy átfogó identitás a Microsoft szolgáltatási (IDaaS) megoldás, amely:

* Lehetővé teszi, hogy az IAM felhőszolgáltatásként 
* Központi hozzáférési felügyeleti, egyszeri bejelentkezés (SSO) és jelentéskészítés 
* Támogatja az integrált hozzáférés-kezelés [ezer alkalmazáshoz](https://azure.microsoft.com/marketplace/active-directory/) alkalmazáskatalógusában, többek között a Salesforce, a Google Apps, Box, beleértve és egyéb. 

Az Azure Active Directoryval minden alkalmazás közzététele a partnerek és ügyfelek (üzleti és fogyasztói) azonosító, és hozzáférés-kezelési képességeket.<br> Ez lehetővé teszi, hogy jelentősen csökkentheti a működési költségeket.

Mi történik, ha meg kell valósítania egy alkalmazás, amely nem szerepel még az alkalmazás-katalógusban? Bár ez egy kicsit több időt vesz igénybe, mint az egyszeri bejelentkezés konfigurálása az alkalmazásokat az alkalmazáskatalógusból, Azure ad-ben tartalmaz, amelyek egy varázsló, amely segítséget nyújt a konfigurációval.

Az érték az Azure AD "csak" a felhőalapú alkalmazások túllép. Emellett használhatja a helyszíni alkalmazások biztonságos távoli hozzáférés biztosítása révén. Biztonságos távoli hozzáférést akkor szükségtelenné teszi a VPN-EK vagy más hagyományos távelérési felügyeleti esetében.

Az alkalmazások a központi felügyeletet és egyszeri bejelentkezés (SSO) megadásával az Azure AD a megoldást a főbb az adatbiztonsággal és a termelékenység problémák biztosít.

* Felhasználók egy bejelentkezéssel több időt biztosítva jövedelem kész létrehozni vagy üzleti műveletek tevékenységek érhetik el több alkalmazást.
* Identitás-rendszergazdák kezelhetik egy helyen alkalmazásokhoz való hozzáférés.

A felhasználó és a vállalat előnye nyilvánvaló. Nézzük az előnyöket közelebbről identitás rendszergazda és a szervezet számára.

## <a name="integrated-application-benefits"></a>Integrált alkalmazás előnyei
Az egyszeri bejelentkezési folyamat két lépésből áll:

* Hitelesítés, a felhasználó identitásának érvényesítése során.
* Engedélyezési, a döntést az erőforrás-hozzáférési házirend engedélyezése vagy letiltása a hozzáférést.

Ha az Azure AD-vel történő alkalmazások kezelésére és az egyszeri bejelentkezés engedélyezése:

* Hitelesítés történik, a felhasználó helyi (pl. AD) vagy az Azure AD-fiókot.
* Engedélyezési végrehajtása az Azure AD-hozzárendelés és védelmi szabályzat konzisztens végfelhasználói élmény biztosítása, és lehetővé téve bármilyen alkalmazás, függetlenül a belső képességei hozzárendelés, helyek és az MFA feltételek hozzáadása.

Fontos megérteni, hogy az engedélyezési végrehajtása meg a célalkalmazás módja hogyan lett integrálva az alkalmazás Azure AD-vel függően változik.

* **Szolgáltató által előre integrált alkalmazások** például Office 365 és az Azure, ezek a közvetlenül az Azure AD épül, és a rajta az átfogó identitás- és hozzáférés-kezelési szolgáltatásokat a függő alkalmazásokat. Ezekhez az alkalmazásokhoz való hozzáférés engedélyezve van a címtáradatokat és a kiállítási keresztül.
* **A Microsoft és az egyéni alkalmazások előre integrált alkalmazások** ezek a független felhőalapú alkalmazások, amelyek az alkalmazás belső könyvtár amelyet az Azure ad-ben függetlenül működhet. Ezekhez az alkalmazásokhoz való hozzáférés engedélyezve van, egy alkalmazásspecifikus hitelesítőadat-alkalmazás fiókhoz hozzárendelt kiállításával. Az alkalmazások képességei a hitelesítő adatokat egy összevonási token vagy felhasználónév és jelszó, amely korábban lett üzembe helyezve, az alkalmazás is lehet.
* **A helyszíni alkalmazások** elsősorban a helyi alkalmazásokhoz való hozzáférés engedélyezése az Azure AD-alkalmazásproxyn keresztül közzétett alkalmazásokat. Ezeket az alkalmazásokat egy központi helyszíni címtár, például a Windows Server Active Directory támaszkodnak. Ezekhez az alkalmazásokhoz való hozzáférést, hogy az alkalmazás tartalmát a végfelhasználó során a helyszíni bejelentkezési követelmény teljesítésére engedélyezve van a proxy elindításával.

Például ha egy felhasználó csatlakozik a szervezet, meg kell bejelentkezés elsődleges műveleteket az Azure AD-ben hozzon létre egy fiókot a felhasználóhoz. Ha a felhasználó egy felügyelt alkalmazást, például a Salesforce-hoz való hozzáférésre van szüksége, is kell a felhasználói fiók létrehozása a Salesforce-ban, és azt, hogy az egyszeri bejelentkezés használata az Azure-fiók összekapcsolása. Amikor a felhasználó távozik a szervezettől, akkor tanácsos az Azure AD-fiók törlése, és az IAM összes tartozó fiók tárolja a felhasználó hozzáfért az alkalmazások.

## <a name="access-detection"></a>Hozzáférés észlelése
A korszerű vállalatok az IT-részlegek számára gyakran nem kapnak információt a használ felhőalapú alkalmazásokat. A Cloud App Discovery együtt az Azure AD biztosít egy megoldást, ezek az alkalmazások észlelésére.

## <a name="account-management"></a>Fiókkezelés
Hagyományosan a különböző alkalmazások kezelésére manuális folyamat során a rendszer által végrehajtott informatikai vagy a támogatási személyzetnek a szervezetben. Az Azure AD teljes mértékben automatizálja a fiókkezelés szolgáltatók által integrált alkalmazások és a Microsoft támogat automatizált felhasználókiépítése vagy SAML engedélyezték kiépítés előre integrált alkalmazások.

## <a name="automated-user-provisioning"></a>Felhasználók automatikus átadása
Egyes alkalmazások létrehozása és eltávolítása (vagy deaktiválása) fiókok automation felületek adja meg. A szolgáltató ilyen illesztőfelületet biztosít, ha az Azure AD által t használja. Ez csökkenti a működési költségeket, mivel a felügyeleti feladatok automatikusan történik, és javítja a környezet biztonságát, mivel így csökken az esélye, hogy jogosulatlan hozzáférést.

## <a name="access-management"></a>Hozzáférés-kezelés
Az Azure ad-vel kezelheti hozzáférést használó egyéni alkalmazások vagy hozzárendelések alapuló szabály. Delegálhatja hozzáférés-kezelés a szervezet biztosítja a legjobb felügyeletet és a segélyszolgálat terhelésének csökkentése a megfelelő személyeknek.

## <a name="on-premises-applications"></a>Helyszíni alkalmazások
A beépített proxy lehetővé teszi, hogy tegye közzé a helyszíni alkalmazások a felhasználók számára, így mindkettő konzisztens felhasználói élményt a modern felhőalapú alkalmazások és az előnyök érheti el a figyelési, jelentéskészítési és biztonsági funkciókat az Azure AD.

## <a name="reporting-and-monitoring"></a>Jelentéskészítés és a monitorozás
Az Azure AD kínál előre integrált jelentéskészítési és figyelési képességek, amelyek lehetővé teszik, hogy tudja, aki hozzáfér az alkalmazásokhoz, és ténylegesen használják őket.

## <a name="related-capabilities"></a>Kapcsolódó képességek
Az Azure ad-vel előre integrált többtényezős hitelesítés és a részletes hozzáférési szabályzatok az alkalmazások védelmének biztosítása. További információ az Azure MFA lásd [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Első lépések
A kezdéshez alkalmazások integrálása az Azure ad-ben, vessen egy pillantást a [integrálása az Azure Active Directory és az alkalmazások első lépések útmutató](plan-an-application-integration.md).

## <a name="see-also"></a>Lásd még
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)
* [Részletes üzembe helyezés tervezése egyszeri bejelentkezés SaaS-alkalmazásba](http://aka.ms/ssodeploymentplan)

