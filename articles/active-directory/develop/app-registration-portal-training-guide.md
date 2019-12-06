---
title: Váltás az alkalmazásregisztrációs portálról az új Alkalmazásregisztrációk felületre az Azure Portalon
titleSuffix: Microsoft identity platform
description: Bevezetés az új alkalmazás regisztrációs felületére a Azure Portal
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: aragra
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93af32e99e725a72c20c05690b2a96c74082e2ff
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844516"
---
# <a name="transitioning-from-application-registration-portal-to-the-new-app-registrations-experience-in-the-azure-portal"></a>Váltás az alkalmazásregisztrációs portálról az új Alkalmazásregisztrációk felületre az Azure Portalon

A Azure Portal új [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felületének számos újdonsága van. Ha még jobban ismeri az alkalmazás-regisztrációs portál (apps.dev.microsoft.com) használatát a konvergens alkalmazások regisztrálásához vagy kezeléséhez, amelyet a régi felhasználói élménynek nevezünk, az útmutató segítségével megkezdheti az új felhasználói élmény megszerzését.

## <a name="whats-not-changing"></a>Mi nem változik?

-   Az alkalmazások és a kapcsolódó konfigurációk az új felületén is megtalálhatók. Nem kell újból regisztrálnia az alkalmazásokat, és az alkalmazások felhasználóinak újra be kell jelentkezniük.

    > [!NOTE]
    > Be kell jelentkeznie azzal a fiókkal, amelyet az alkalmazások regisztrálásához használt a Azure Portalban való kereséshez. Javasoljuk, hogy tekintse meg a bejelentkezett felhasználót a Azure Portal az alkalmazás regisztrációs portálra bejelentkezett felhasználóval egyeztetve az e-mail-cím a profilból való összehasonlításával.
    > 
    > Bizonyos esetekben, különösen akkor, ha személyes Microsoft-fiókokat (például Outlook, Live, Xbox stb.) használ egy Azure AD-beli e-mail-címmel, azt találtuk, hogy amikor a régi felhasználói felületről bejelentkezik a Azure Portalba, egy másik fiókba jelentkezik be. e-mail-cím az Azure AD-bérlőben. Ha továbbra is úgy gondolja, hogy az alkalmazásai hiányoznak, jelentkezzen ki, és jelentkezzen be a megfelelő fiókkal.

-   A személyes Microsoft-fiókokkal létrehozott élő SDK-alkalmazások még nem támogatottak a Azure Portalban, és a közeljövőben továbbra is a régi élményben maradnak.

## <a name="key-changes"></a>Legfontosabb változások

-   A régi tapasztalatok szerint az alkalmazások alapértelmezés szerint olyan konvergens alkalmazásokként lettek regisztrálva, amelyek támogatják az összes szervezeti fiókot (több-bérlős) és a személyes Microsoft-fiókokat. Ez a régi felülettel nem módosítható, így megnehezíti a csak a szervezeti fiókok (több-bérlős vagy egyetlen bérlő) által támogatott alkalmazások létrehozását.
    Az új felület lehetővé teszi, hogy regisztrálja az összes lehetőséget támogató alkalmazásokat. [További információ az alkalmazások típusairól](active-directory-v2-registration-portal.md).

-   Abban az esetben, ha a személyes Microsoft-fiók egy Azure AD-bérlőben is található, három lapot fog látni – a bérlő összes alkalmazását, a bérlőn belüli alkalmazásokat, valamint a személyes fiókban lévő alkalmazásokat. Ha úgy gondolja, hogy a személyes Microsoft-fiók regisztrált alkalmazások hiányoznak, ellenőrizze az **alkalmazásokat a személyes fiók** lapján.

-   Az új felhasználói élményben egyszerűen válthat a bérlők között a profiljához való navigálással, majd a váltási könyvtár lehetőség választásával.

## <a name="list-of-applications"></a>Alkalmazások listája

-   Az új alkalmazások listája az Azure Portal (csak az Azure AD-fiókokat bejelentkező alkalmazások) és az [alkalmazás-regisztrációs portál](https://apps.dev.microsoft.com/) (az Azure ad-t és a személyes Microsoft-fiókokat egyaránt bejelentkező alkalmazások) által a korábbi alkalmazás-regisztráció során regisztrált alkalmazásokat jeleníti meg.

-   Az új alkalmazás listán két további oszlop található: az oszlop és a **tanúsítványok & Secrets** oszlop, amely megjeleníti az alkalmazásban regisztrált hitelesítő adatok állapotát (aktuális, hamarosan lejáró vagy lejárt).

## <a name="new-app-registration"></a>Új alkalmazás regisztrálása

A régi felhasználói felületen egy átszervezett alkalmazás regisztrálásához csak a nevet kell megadni. A létrehozott alkalmazások olyan konvergens alkalmazásokként lettek regisztrálva, amelyek támogatják az összes szervezeti könyvtárat (több-bérlős) és a személyes Microsoft-fiókokat.  Ez a régi felülettel nem módosítható, így megnehezíti a csak a szervezeti fiókok (több-bérlős vagy egyetlen bérlő) által támogatott alkalmazások létrehozását. [További információ a támogatott fióktípus](v2-supported-account-types.md)

Az új felhasználói felületen meg kell adnia egy nevet az alkalmazásnak, és ki kell választania a támogatott fióktípus lehetőséget. Igény szerint átirányítási URI-t is megadhat.
Ha átirányítási URI-t ad meg, meg kell adnia, hogy webes vagy nyilvános (natív/mobil és asztali). Ha további információt szeretne arról, hogyan regisztrálhat egy alkalmazást az új alkalmazás-regisztráció használatával, tekintse meg [ezt](quickstart-register-app.md)a rövid útmutatót.

## <a name="app-management-page"></a>Alkalmazás-kezelés lap

A régi élmény egyetlen alkalmazás-felügyeleti oldal volt az átszervezett alkalmazások számára a következő részekben: tulajdonságok, alkalmazás-titkok, platformok, tulajdonosok, Microsoft Graph engedélyek, profil és speciális beállítások.

A Azure Portal új felülete ezeket a funkciókat külön lapokon jeleníti meg. Itt találja a megfelelő funkciót:

-   Tulajdonságok – a név és az alkalmazás azonosítója az Áttekintés oldalon található.

-   Az alkalmazás titkai a tanúsítványok & Secrets oldalon találhatóak.

-   A platformok konfigurációja a hitelesítés oldalon található.

-   Microsoft Graph engedélyek az API-engedélyek lapon, más engedélyekkel együtt

-   A profil a branding oldalon található

-   Speciális beállítás – az élő SDK támogatása a hitelesítés oldalon található.

## <a name="application-secretscertificates--secrets"></a>Alkalmazás-titkok/tanúsítványok & titkos kódok

Az új felhasználói élményben az **alkalmazás-titkok** átnevezve lettek a **tanúsítványok & Secrets**névre. Emellett a **nyilvános kulcsokat** **tanúsítványoknak** és **jelszavaknak** nevezik, az **ügyfél titkos**kulcsaként. Úgy döntöttünk, hogy ezt a funkciót az új biztonsági okokból nem tudjuk elérni, ezért nem lehet új kulcspárt kiállítani.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformok/hitelesítés: válasz URL-címek/átirányítási URI-k
A régi felületen a webes, natív és webes API-k platformokkal foglalkozó szakasza az átirányítási URL-címek, a kijelentkezési URL-cím és az implicit folyamat konfigurálására használható.

Az új felhasználói felületen a válasz URL-címek a\'s hitelesítési szakaszban találhatók. Emellett a rendszer átirányítási URI-ként is hivatkozik rá, és módosult az átirányítási URI-k formátuma. Egy alkalmazás típusához (webes vagy nyilvános ügyfél – mobil és asztali) kell társítani őket. [További információ](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

A webes API-k API-lapokon vannak konfigurálva.

> [!NOTE] 
> Próbálja ki az új hitelesítési beállításokat, ahol konfigurálhatja az alkalmazás beállításait a célként használni kívánt platform vagy eszköz alapján. [További információ](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Engedélyek/API-engedélyek Microsoft Graph

-   Amikor kijelöl egy API-t a régi felületen, csak Microsoft Graph API-k közül választhat. Az új élményben számos Microsoft API közül választhat, többek között Microsoft Graph, API-kat a szervezettől és az API-kkal, ezt három lapon tekintheti meg: Microsoft API-k, a szervezet által használt API-k vagy saját API-k. A szervezet által használt API-k keresési sávján a szolgáltatás a bérlőn keresztüli egyszerű keresést végez.
    
    > [!NOTE] 
    > Ez a lap nem jelenik meg, ha az alkalmazás nincs bérlőhöz társítva. Ha további információt szeretne arról, hogyan kérhet engedélyeket az új felhasználói felülettel, tekintse meg [ezt](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)a rövid útmutatót.

-   A régi felület nem rendelkezik **engedélyezési engedélyekkel** gomb. Az új felhasználói felületen az alkalmazás API-engedélyei szakaszának **engedélyekkel** kapcsolatos engedélyezési szakasza szerepel. Csak rendszergazda adhat meg beleegyező jogosultságot, és ez a gomb csak rendszergazdák számára engedélyezett. Ha egy rendszergazda kiválasztja a **rendszergazdai jóváhagyás megadása** gombot, a rendszer rendszergazdai jóváhagyást kap az összes kért engedélyhez.

## <a name="profile"></a>Profil
A régi élményben a profilhoz embléma, Kezdőlap URL-címe, szolgáltatási URL-cím és adatvédelmi nyilatkozat URL-konfigurációja található. Az új élményben ezek a branding oldalon találhatók.

## <a name="application-manifest"></a>Alkalmazásjegyzék
Az új felhasználói felületen a manifest oldalon szerkesztheti és frissítheti az alkalmazás attribútumait. További információ: [Application manifest](reference-app-manifest.md).

## <a name="new-ui"></a>Új felhasználói felület
A korábban csak a manifest Editor vagy az API használatával beállított tulajdonságok új felhasználói felülettel rendelkeznek, vagy nem létezett.

-   Az implicit engedélyezési folyamat (oauth2AllowImplicitFlow) a hitelesítés oldalon található. A régi felülettől eltérően engedélyezheti a hozzáférési jogkivonatokat vagy azonosító jogkivonatokat, vagy mindkettőt.

-   Az API (oauth2Permissions) és a jóváhagyott ügyfélalkalmazások (preAuthorizedApplications) által definiált hatókörök az API-k közzététele lapon konfigurálhatók. További információ az alkalmazások webes API-ként való konfigurálásáról és az engedélyek/hatókörök elérhetővé tétele: [ebben](quickstart-configure-app-expose-web-apis.md)a rövid útmutatóban.

-   Közzétevő tartomány (amely megjelenik az [alkalmazás\'s jóváhagyásának kérése](application-consent-experience.md)a felhasználók számára) a branding (védjegyezés) panel oldalán található. A közzétevő tartomány konfigurálásával kapcsolatos további információkért tekintse meg [ezt a útmutató](howto-configure-publisher-domain.md)című témakört.

## <a name="limitations"></a>Korlátozások

Az új felhasználói élmény a következő korlátozásokkal jár:

-   Az új felület még nem támogatja a Alkalmazásregisztrációk Azure AD B2C bérlők számára.

-   Az új felhasználói felület még nem támogatja a személyes Microsoft-fiókokkal létrehozott élő SDK-alkalmazásokat.

-   A támogatott fiókok értékének módosítása nem támogatott a felhasználói felületen. Az alkalmazás-jegyzékfájlt csak akkor kell használnia, ha\'az Azure AD egybérlős és a több-bérlő közötti váltást.

   > [!NOTE]
   > Ha Ön személyes Microsoft-fiók felhasználó az Azure AD-bérlőben, és a bérlői rendszergazdának korlátozott hozzáférése van a Azure Portalhoz, akkor a hozzáférés megtagadva lesz. Ha azonban a parancsikonon a Alkalmazásregisztrációk beírásával, vagy a rögzítéssel megadhatja azt, akkor hozzáférhet az új felülethez.
