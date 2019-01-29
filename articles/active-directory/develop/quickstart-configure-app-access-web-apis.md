---
title: Alkalmazás konfigurálása webes API-k elérésére (előzetes verzió) | Azure
description: Megtudhatja, hogyan konfigurálhatja a Microsoft Identity Platformon regisztrált alkalmazást úgy, hogy átirányítási URI-t vagy URI-kat, hitelesítő adatokat vagy webes API-k elérésére vonatkozó engedélyeket is tartalmazzon.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: a2f0d97d6b7040f874fc03ffe19f247cdc742c77
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103649"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>Gyors útmutató: Webes API-k (előzetes verzió) eléréséhez ügyfélalkalmazás konfigurálása

Ahhoz, hogy egy webes/bizalmas ügyfélalkalmazás részt vehessen egy hitelesítést (és hozzáférési jogkivonat beszerzését) előíró engedélyezési folyamatban, biztonságos hitelesítő adatokat kell létesítenie. Az Azure Portal által támogatott alapértelmezett hitelesítési módszer egy ügyfél-azonosítót és egy titkos kulcsot igényel.

Ezenkívül mielőtt az ügyfél hozzáférhet egy erőforrás-alkalmazás webes API-jához (például a Microsoft Graph API-t), a hozzájárulási keretrendszer biztosítja, hogy az ügyfél a kért engedélyek alapján megkapja a szükséges engedélyt. Alapértelmezés szerint minden alkalmazás választhat engedélyeket a Microsoft Graph API-ból. A [Graph API „Bejelentkezés és a felhasználói profil olvasása” engedélye](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) alapértelmezés szerint be van jelölve. [Kétféle engedély](developer-glossary.md#permissions) közül választhat minden egyes kívánt API esetében:

* **Alkalmazásengedély**: Az ügyfélalkalmazásnak a webes API-t közvetlenül a maga nevében kell elérnie (nincs felhasználói környezet). Az ilyen típusú engedély rendszergazdai hozzájárulást igényel, és nyilvános (asztali vagy mobil) ügyfélalkalmazások esetében nem alkalmazható.
* **Delegált engedély**: Az ügyfélalkalmazásnak a webes API-t a bejelentkezett felhasználó nevében, de a kiválasztott engedély által korlátozott hozzáféréssel kell elérnie. Az ilyen típusú engedély megadható a felhasználó által, hacsak nem igényel rendszergazdai hozzájárulást.

  > [!NOTE]
  > A delegált engedély alkalmazáshoz való hozzáadása nem biztosítja automatikusan a hozzájárulást a bérlőn belüli felhasználóknak. A felhasználóknak továbbra is manuálisan kell a futásidőben hozzájárulniuk a delegált engedélyeket, hacsak a rendszergazda nem hagyja azokat jóvá minden felhasználó nevében.

Ebben a rövid útmutatóban bemutatjuk, hogyan konfigurálhatja alkalmazását az alábbi célokra:

* [Átirányítási URI-k hozzáadása az alkalmazáshoz](#add-redirect-uris-to-your-application)
* [Hitelesítő adatok hozzáadása a webalkalmazáshoz](#add-credentials-to-your-web-application)
* [Webes API-k elérésére vonatkozó engedélyek hozzáadása](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Előfeltételek

Először is győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

* Megismerte a támogatott [engedélyekkel és hozzájárulással](v2-permissions-and-consent.md) kapcsolatos tudnivalókat, mivel fontos szem előtt tartania őket olyan alkalmazások készítésekor, amelyeket más felhasználóknak vagy alkalmazásoknak is használniuk kell.
* Rendelkezik olyan bérlővel, amelyhez regisztrált alkalmazások tartoznak.
  * Ha nincsenek regisztrált alkalmazásai, [ismerje meg, hogyan regisztrálhat alkalmazásokat a Microsoft Identity Platformon](quickstart-register-app.md).
* Hagyja jóvá az előzetes verzió használatát az alkalmazások Azure Portalon való regisztrálására. E rövid útmutató lépései az új felhasználói felületen alapulnak, és csak akkor működnek, ha Ön jóváhagyta az előzetes verzió használatát.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Bejelentkezés az Azure Portalra és az alkalmazás kiválasztása

Mielőtt konfigurálhatná az alkalmazást, végre kell hajtania az alábbi lépéseket:

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiókjával.
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást, majd az **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget.
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kijelölte az alkalmazást, az alkalmazás **Áttekintés** lapja vagy regisztrációs főoldala jelenik meg.
1. Az alkalmazás webes API-k elérésére való konfigurálásához kövesse az alábbi lépéseket: 
    * [Átirányítási URI-k hozzáadása az alkalmazáshoz](#add-redirect-uris-to-your-application)
    * [Hitelesítő adatok hozzáadása a webalkalmazáshoz](#add-credentials-to-your-web-application)
    * [Webes API-k elérésére vonatkozó engedélyek hozzáadása](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Átirányítási URI(-k) hozzáadása az alkalmazáshoz

[![Webes és nyilvános ügyfélalkalmazások egyéni átirányítási URI-jának hozzáadása](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Ha átirányítási URI-t szeretne hozzáadni alkalmazásához:

1. Az alkalmazás **Áttekintés** lapján válassza a **Hitelesítés** szakaszt.

1. Webes és nyilvános ügyfélalkalmazások egyéni átirányítási URI-jának hozzáadásához kövesse az alábbi lépéseket:

    1. Keresse meg a **Átirányítási URI** szakaszt.
    1. Válassza ki a létrehozni kívánt alkalmazás típusát: **webes** vagy **nyilvános (mobil és asztali) ügyfél**.
    1. Adja meg alkalmazása átirányítási URI-ját.
        * Webalkalmazások esetében az alkalmazás alap URL-címét adja meg. A http://localhost:31544 például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
        * Nyilvános alkalmazások esetében azt az URI-t adja meg, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg az alkalmazásához tartozó értéket, például: https://MyFirstApp.

1. Ha a nyilvános (mobil, asztali) ügyfelekhez javasolt átirányítási URI-k közül szeretne választani, kövesse az alábbi lépéseket:

    1. Keresse meg a **Javasolt átirányítási URI-k nyilvános ügyfelekhez (mobil, asztali)** szakaszt.
    1. A jelölőnégyzetekkel válassza ki a megfelelő átirányítási URI-t vagy URI-kat az alkalmazása számára.

## <a name="add-credentials-to-your-web-application"></a>Hitelesítő adatok hozzáadása a webalkalmazáshoz

[![Tanúsítványok és titkos ügyfélkódok megadása](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Ha hitelesítő adatokat szeretne hozzáadni webalkalmazásához:

1. Az alkalmazás **Áttekintés** lapján válassza a **Tanúsítványok és titkos kódok** szakaszt.

1. Tanúsítvány hozzáadásához kövesse az alábbi lépéseket:

    1. Válassza a **Tanúsítvány feltöltése** lehetőséget.
    1. Válassza ki a feltölteni kívánt fájlt. A fájlnak a következő típusok egyikébe kell tartoznia: .cer, .pem vagy .crt.
    1. Válassza a **Hozzáadás** lehetőséget.

1. Titkos ügyfélkód hozzáadásához kövesse az alábbi lépéseket:

    1. Válassza az **Új titkos ügyfélkód** lehetőséget.
    1. Adja meg titkos ügyfélkódja leírását.
    1. Válassza ki az időtartamot.
    1. Válassza a **Hozzáadás** lehetőséget.

> [!NOTE]
> A konfiguráció módosításainak mentése után a jobb szélső oszlop tartalmazza majd a titkos ügyfélkód értékét. **Mindenképp másolja le az értéket** az ügyfélalkalmazás kódolása során való használathoz, mivel a lap elhagyását követően nem lehet majd megtekinteni.

## <a name="add-permissions-to-access-web-apis"></a>Webes API-k elérésére vonatkozó engedélyek hozzáadása

[![API-engedélyek hozzáadása](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Ha erőforrás-API-k ügyfélről való elérésére vonatkozó engedélyt vagy engedélyeket kíván hozzáadni:

1. Az alkalmazás **Áttekintés** lapján válassza az **API-engedélyek** lehetőséget.
1. Nyomja meg **Az engedély hozzáadása** gombot.
1. Alapértelmezés szerint ebben a nézetben választhat a **Microsoft API-k** közül. Válassza ki az Önt érdeklő API-k szakaszát:
    * **Microsoft API-k** – itt a Microsoft API-kra, például a Microsoft Graph-ra vonatkozó engedélyeket választhatja ki.
    * **Saját szervezet által használt API-k** – itt a szervezete által közzétett vagy integrált API-kra vonatkozó engedélyeket választhatja ki.
    * **Saját API-k** – itt az Ön által közzétett API-kra vonatkozó engedélyeket választhatja ki.
1. Miután kiválasztotta az API-kat, **Az API-engedélyek kérése** lap jelenik meg. Ha az API delegált és alkalmazásengedélyeket egyaránt közzétesz, válassza ki, milyen típusú engedélyre van szüksége az alkalmazásának.
1. Miután végzett, válassza az **Engedélyek hozzáadása** lehetőséget. Újra megjelenik az **API-engedélyek** lap, ahol az engedélyek a mentést követően bekerülnek a táblázatba.

## <a name="next-steps"></a>További lépések

Tekintse meg az alkalmazásokra vonatkozó alábbi rövid alkalmazásfelügyeleti útmutatókat is:

* [Alkalmazás regisztrálása a Microsoft Identity Platformon](quickstart-register-app.md)
* [Alkalmazás konfigurálása webes API-k közzétételére](quickstart-configure-app-expose-web-apis.md)
* [Alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)
* [Microsoft Identity Platformon regisztrált alkalmazás eltávolítása](quickstart-remove-app.md)

Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

Ha szeretne többet megtudni az Azure Active Directoryt használó alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, tekintse meg az [alkalmazások védjegyzési irányelveit](howto-add-branding-in-azure-ad-apps.md) ismertető szakaszt.
