---
title: Új alkalmazásregisztráció az Azure Portalon – felület
titleSuffix: Microsoft identity platform
description: Bevezetés az új alkalmazásregisztrációs élménybe az Azure Portalon
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154593"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Az Azure Portal új alkalmazásregisztrációs élménye

Az Azure Portalon számos fejlesztés továbbfejlesztettaz új [alkalmazásregisztrációs](https://go.microsoft.com/fwlink/?linkid=2083908) élmény. Ha jobban ismeri az alkalmazásregisztrációs portál (apps.dev.microsoft.com) tapasztalt konvergens alkalmazások regisztrálását vagy kezelését, a továbbiakban a régi élmény, ez a képzési útmutató segítségével megkezdheti az új felület használatát.

## <a name="whats-not-changing"></a>Mi nem változik?

- Az alkalmazások és a kapcsolódó konfigurációk megtalálhatók az új élményben. Nem kell újra regisztrálnia az alkalmazásokat, és az alkalmazások felhasználóinak nem kell újra bejelentkezniük.

    > [!NOTE]
    > Be kell jelentkeznie azzal a fiókkal, amelyet az alkalmazások regisztrálásához használt, hogy megtalálja őket az Azure Portalon. Azt javasoljuk, hogy ellenőrizze a bejelentkezett felhasználó az Azure Portalon megegyezik a felhasználó, aki bejelentkezett az alkalmazás regisztrációs portálon összehasonlítva az e-mail-címet a profilból.
    >
    > Bizonyos esetekben, különösen akkor, ha személyes Microsoft-fiókokkal (pl. Outlook, Live, Xbox stb.) jelentkezik be egy Azure AD e-mail címmel, kiderült, hogy amikor a régi élményből az Azure Portalra lép, akkor egy másik fiókba jelentkezik be ugyanazzal a e-mail az Azure AD-bérlőben. Ha továbbra is úgy gondolja, hogy a jelentkezések hiányoznak, jelentkezzen ki, és jelentkezzen be a megfelelő fiókkal.

- A személyes Microsoft-fiókok használatával létrehozott élő SDK-alkalmazások még nem támogatottak az Azure Portalon, és a közeljövőben is a régi élményben maradnak.

## <a name="key-changes"></a>Főbb változások

-   A régi tapasztalatok szerint az alkalmazások alapértelmezés szerint konvergens alkalmazásokként voltak regisztrálva, amelyek minden szervezeti fiókot (több-bérlős) és személyes Microsoft-fiókokat támogatnak. Ez nem módosítható a régi felületen keresztül, ami megnehezíti a csak szervezeti fiókokat (több-bérlős vagy egybérlős) alkalmazások létrehozását.
    Az új felület lehetővé teszi, hogy regisztráljon alkalmazásokat támogató mindezeket a lehetőségeket. [További információ az alkalmazástípusokról.](active-directory-v2-registration-portal.md)

-   Az új felület, ha a személyes Microsoft-fiók is egy Azure AD-bérlő, látni fogja a három lap - az összes alkalmazás a bérlőben, a bérlő tulajdonában lévő alkalmazások, valamint a személyes fiókalkalmazások. Ha tehát úgy gondolja, hogy a személyes Microsoft-fiókjával regisztrált alkalmazások hiányoznak, ellenőrizze az **Alkalmazások a személyes fiókjában** lapot.

-   Az új felületen könnyedén válthat a bérlők között a profilra való navigálással és a címtárváltás kiválasztásával.

## <a name="list-of-applications"></a>Alkalmazások listája

-   Az új alkalmazáslista azokat az alkalmazásokat jeleníti meg, amelyeket az azure-portálon (csak az Azure AD-fiókokban bejelentkező alkalmazásokban) az örökölt alkalmazásregisztrációk on keresztül regisztráltak, valamint az [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/) (az Azure AD-ben és a személyes Microsoft-fiókokban is bejelentkező alkalmazásokat).

-   Az új alkalmazáslista két további oszlopot **tartalmaz: Az oszlopon és a** Tanúsítványok & titkos **kulcsok** oszlopban, amely az alkalmazásban regisztrált hitelesítő adatok állapotát mutatja (aktuális, hamarosan lejáró vagy lejárt).

## <a name="new-app-registration"></a>Új alkalmazás regisztrálása

A régi élményben az egy konvergens alkalmazás regisztrálásához csak egy nevet kellett megadnia. A létrehozott alkalmazások konvergens alkalmazásokként lett regisztrálva, amelyek támogatják az összes szervezeti címtárat (több-bérlős) valamint a személyes Microsoft-fiókokat.  Ez nem módosítható a régi felületen keresztül, ami megnehezíti a csak szervezeti fiókokat (több-bérlős vagy egybérlős) alkalmazások létrehozását. [További információ a támogatott fióktípusokról](v2-supported-account-types.md)

Az új felületen meg kell adnia az alkalmazás nevét, és ki kell választania a Támogatott fióktípusokat. Opcionálisan megadhat átirányítási URI-t.
Ha átirányítási URI-t ad meg, meg kell adnia, hogy az web/nyilvános (natív/mobil és asztali). Ha többet szeretne tudni arról, hogy miként regisztrálhat egy alkalmazást az új alkalmazásregisztrációk használatával, olvassa el [ezt a rövid útmutatót.](quickstart-register-app.md)

## <a name="app-management-page"></a>Alkalmazáskezelési lap

A régi élmény egyetlen alkalmazáskezelési lapot tartalmazott a következő szakaszokat tartalmazó konvergens alkalmazásokhoz: Tulajdonságok, Alkalmazástitkok, Platformok, Tulajdonosok, Microsoft Graph-engedélyek, Profil és Speciális beállítások.

Az Azure Portalon található új felület ezeket a funkciókat külön lapokra jelöli. Itt találja az egyenértékű funkciókat:

-   Tulajdonságok – A név és az alkalmazásazonosító az Áttekintés lapon található.

-   Az alkalmazástitkok a Tanúsítványok & titkos kulcsok lapon találhatók

-   A platformok konfigurációja a Hitelesítés lapon található

-   A Microsoft Graph engedélyei az API-engedélyek lapon és más engedélyeken találhatók

-   A profil a Márkajelzés oldalon található

-   Speciális beállítás – Az Élő SDK-támogatás a Hitelesítés lapon található.

## <a name="application-secretscertificates--secrets"></a>Alkalmazástitkok/tanúsítványok & titkos kulcsok

Az új felületen az **alkalmazástitkos kulcsok** at **tanúsítványokra & titkos kulcsokra nevezték**át. Ezenkívül **a nyilvános kulcsokat** **tanúsítványoknak** **nevezik, a jelszavakat** pedig **ügyféltitoknak**nevezzük. Úgy döntöttünk, hogy biztonsági okokból nem hozzuk be ezt a funkciót az új élménybe, ezért már nem hozhat létre új kulcspárt.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformok/hitelesítés: Válasz URL-ek/átirányítási URI-k
A régi tapasztalatok, egy alkalmazás rendelkezett Platformok szakasz webes, natív és webes API-t beállítani átirányítási URL-címek, kijelentkezési URL-cím és implicit folyamat konfigurálása.

Az új felületen a Válasz URL-címek\'az alkalmazás hitelesítése szakaszban találhatók. Ezenkívül átirányítási URI-knak is nevezik őket, és az átirányítási URI-k formátuma megváltozott. Alkalmazástípushoz kell őket társozni (webes vagy nyilvános ügyfél - mobil és asztali). [További információ](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

A webes API-k api-k api-k elérhetővé teszik az API-kat.

> [!NOTE]
> Próbálja ki az új hitelesítési beállítások at, ahol konfigurálhatja az alkalmazás beállításait a megcélozni kívánt platform vagy eszköz alapján. [További információ](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph engedélyek/API-engedélyek

-   Api-t a régi él, csak a Microsoft Graph API-k közül választhat. Az új felületen számos Microsoft API közül választhat, beleértve a Microsoft Graph-ot, a szervezet API-jait és az API-kat, ez három lapon jelenik meg: Microsoft API-k, a szervezet által használt API-k vagy saját API-k. A keresési sáv az API-k a szervezet használ lap keresések a bérlőszolgáltatás-egyszerű szolgáltatás.

    > [!NOTE]
    > Ez a lap nem jelenik meg, ha az alkalmazás nincs bérlőhöz társítva. Ha többet szeretne tudni arról, hogy miként kérhet engedélyeket az új felület használatával, olvassa el [ezt a rövid útmutatót.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)

-   A régi élménynem rendelkezett **Grant engedélyek** gombbal. Az új felületen van egy Grant consent szakasz egy **Grant admin hozzájárulásgombbal** az alkalmazás API-engedélyeinek szakaszán. Csak egy rendszergazda adhat hozzájárulást, és ez a gomb csak rendszergazdák számára engedélyezett. Amikor egy rendszergazda kiválasztja a **Rendszergazda hozzájárulásának megadása** gombot, a rendszergazdai hozzájárulás az összes kért engedélyhez meglesz adva.

## <a name="profile"></a>Profil
A régi tapasztalatok, Profil volt Logo, honlap URL-t, szolgáltatási feltételek URL-t és adatvédelmi nyilatkozat URL-konfiguráció. Az új élményben ezek a Márkajelzés oldalon találhatók.

## <a name="application-manifest"></a>Alkalmazásjegyzék
Az új felületen a Manifest oldal lehetővé teszi az alkalmazás attribútumainak szerkesztését és frissítését. További információ: [Application manifest](reference-app-manifest.md).

## <a name="new-ui"></a>Új felhasználói felület
Van új felhasználói felület a tulajdonságok, amelyek korábban csak a jegyzékszerkesztő vagy az API használatával állítható be, vagy nem létezik.

-   Implicit támogatási folyamat (oauth2AllowImplicitFlow) megtalálható a hitelesítési oldalon. A régi élménnyel ellentétben engedélyezheti a hozzáférési jogkivonatokat vagy az azonosító jogkivonatokat, vagy mindkettőt.

-   Az API által meghatározott hatókörök (oauth2Permissions) és az Engedélyezett ügyfélalkalmazások (preAuthorizedApplications) konfigurálhatók az API-megjelenítése lapon keresztül. Ha többet szeretne tudni arról, hogy miként konfigurálhat egy alkalmazást webes API-ként, és hogyan teheti elérhetővé az engedélyeket/hatóköröket, olvassa el [ezt a rövid útmutatót.](quickstart-configure-app-expose-web-apis.md)

-   A Publisher-tartomány (amely az [\'alkalmazás hozzájárulási parancsában](application-consent-experience.md)jelenik meg a felhasználók számára) a Márkajelzés panellapon található. A közzétevői tartomány konfigurálásáról az [útmutatóban](howto-configure-publisher-domain.md)talál további információt.

## <a name="limitations"></a>Korlátozások

Az új felület a következő korlátozásokkal rendelkezik:

-   Az új felület még nem támogatja az Azure AD B2C-bérlők alkalmazásregisztrációit.

-   Az új felület még nem támogatja a személyes Microsoft-fiókokkal létrehozott Live SDK-alkalmazásokat.

-   A támogatott fiókok értékének módosítása nem támogatott a felhasználói felületen. Az alkalmazásjegyzéket használnia\'kell, kivéve, ha az egybérlős És a több-bérlős Azure AD között vált.

   > [!NOTE]
   > Ha Ön személyes Microsoft-fiók-felhasználó az Azure AD-bérlőben, és a bérlői rendszergazda korlátozott hozzáféréssel rendelkezik az Azure Portalhoz, előfordulhat, hogy a hozzáférés megtagadva. Ha azonban úgy jut át a parancsikonon, hogy beírja az alkalmazásregisztrációkat a keresősávba, vagy kitűzi azt, akkor hozzáférhet az új élményhez.
