---
title: 'Rövid útmutató: Webes API-k elérése az alkalmazáshoz – Microsoft identity platform | Azure'
description: Ebben a rövid útmutatóban konfiguráljon egy, a Microsoft identitásplatformmal regisztrált alkalmazást úgy, hogy tartalmazza az átirányítási URI-kat, a hitelesítő adatokat vagy a webes API-k elérésére vonatkozó engedélyeket.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 5e628626f2db49ff67d6d7ab425a3a19870b1ebd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240897"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Rövid útmutató: Ügyfélalkalmazás konfigurálása webes API-k eléréséhez

Ebben a rövid útmutatóban átirányítási URI-kat, hitelesítő adatokat vagy engedélyeket adhat hozzá az alkalmazás webes API-jainak eléréséhez. Egy webes vagy bizalmas ügyfélalkalmazásnak biztonságos hitelesítő adatokat kell létrehoznia a hitelesítést igénylő engedélyezési engedélyezési folyamatban való részvételhez. Az Azure Portal által támogatott alapértelmezett hitelesítési módszer egy ügyfél-azonosítót és egy titkos kulcsot igényel. Az alkalmazás a folyamat során egy hozzáférési jogkivonatot kap.

Mielőtt egy ügyfél hozzáférhetne egy erőforrás-alkalmazás, például a Microsoft Graph API által elérhetővé tett webes API-hoz, a jóváhagyási keretrendszer biztosítja, hogy az ügyfél megkapja a kért engedélyekhez szükséges engedélytámogatást. Alapértelmezés szerint minden alkalmazás kérhet engedélyt a Microsoft Graph API-tól.

## <a name="prerequisites"></a>Előfeltételek

* Rövid [útmutató befejezése: Alkalmazás regisztrálása a Microsoft identitásplatformján.](quickstart-register-app.md)
* Engedélyek és hozzájárulás áttekintése [a Microsoft identity platform végpontján.](v2-permissions-and-consent.md)
* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Bejelentkezés az Azure Portalra és az alkalmazás kiválasztása

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiók több bérlőhöz is hozzáférést biztosít, válassza ki a fiókját a jobb felső sarokban. Állítsa be a portálmunkamenetet a kívánt Azure AD-bérlőre.
1. Keresse meg és válassza ki az **Azure Active Directoryt**. A **Kezelés csoportban**válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kiválasztotta az alkalmazást, megjelenik az alkalmazás **áttekintése** vagy a fő regisztrációs oldal.

Az alábbi eljárásokkal konfigurálhatja az alkalmazást a webes API-k eléréséhez.

## <a name="add-redirect-uris-to-your-application"></a>Átirányítási URI-k hozzáadása az alkalmazáshoz

Egyéni átirányítási URI-kat és javasolt átirányítási URI-kat adhat hozzá az alkalmazáshoz. Egyéni átirányítási URI hozzáadása webes és nyilvános ügyfélalkalmazásokhoz:

1. Az alkalmazás **áttekintése** lapon válassza a **Hitelesítés**lehetőséget.
1. Keresse **meg az átirányítási URI-kat.** Előfordulhat, hogy a Váltás a régi élményre lehetőséget kell **választania.**
1. Válassza ki az általad épített alkalmazás típusát: **Webes** vagy **nyilvános ügyfél/natív (mobil & asztali számítógép)**.
1. Adja meg alkalmazása átirányítási URI-ját.

   * Webalkalmazások esetében az alkalmazás alap URL-címét adja meg. A `http://localhost:31544` például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
   * Nyilvános alkalmazások esetében azt az URI-t adja meg, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg egy, az alkalmazásra `https://MyFirstApp`jellemző értéket, például: . .
1. Kattintson a **Mentés** gombra.

A nyilvános ügyfelek javasolt átirányítási URI-i közül az alábbi lépésekkel választhat:

1. Az alkalmazás **áttekintése** lapon válassza a **Hitelesítés**lehetőséget.
1. Keresse meg **a javasolt átirányítási URI-kat nyilvános ügyfelek (mobil, asztali) számára.** Előfordulhat, hogy a Váltás a régi élményre lehetőséget kell **választania.**
1. Válasszon ki egy vagy több átirányítási URI-t az alkalmazáshoz. Egyéni átirányítási URI-t is megadhat. Ha nem tudja biztosan, hogy mit használjon, tekintse meg a könyvtár dokumentációját.
1. Kattintson a **Mentés** gombra.

Bizonyos korlátozások vonatkoznak az átirányítási URI-kra. További információt az [URI/reply URL-címek átirányítása és korlátozása című](https://docs.microsoft.com/azure/active-directory/develop/reply-url)témakörben talál.

> [!NOTE]
> Próbálja ki az új **hitelesítési** beállítások at, ahol konfigurálhatja az alkalmazás beállításait a megcélozni kívánt platform vagy eszköz alapján.
>
> A nézet megtekintéséhez válassza **az Új felület kipróbálása** lehetőséget a **Hitelesítés** lapon.
>
> ![Kattintson az "Próbálja ki az új felületet" elemre a Platform konfigurációs nézetének megtekintéséhez](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Ezzel az [új **Platform konfigurációs** lapra](#configure-platform-settings-for-your-application)kerül.

### <a name="configure-advanced-settings-for-your-application"></a>Az alkalmazás speciális beállításainak konfigurálása

A regisztrálandó alkalmazástól függően néhány további beállítást is konfigurálnia kell, például:

* **Kijelentkezési URL-cím**.
* Az egyoldalas alkalmazások esetében engedélyezheti az **implicit támogatást,** és kiválaszthatja azokat a jogkivonatokat, amelyeket az engedélyezési végpontnak ki szeretne adni.
* Az on desktop apps that acquire tokens by Integrated Windows Authentication, device code flow, or username/password in the **Default client type** section, set the Treat application as public **client** setting to **Yes**.
* A Live SDK-t a Microsoft-fiókszolgáltatással való integrációhoz használt örökölt alkalmazások esetében konfigurálja a **Live SDK-támogatást.** Az új alkalmazásoknak nincs szükségük erre a beállításra.
* **Alapértelmezett ügyféltípus**.
* **Támogatott fióktípusok**.

### <a name="modify-supported-account-types"></a>Támogatott fióktípusok módosítása

A **támogatott fióktípusok** határozzák meg, hogy ki használhatja az alkalmazást, vagy ki férhet hozzá az API-hoz.

Ha az alkalmazás regisztrálásakor konfigurálta a támogatott fióktípusokat, akkor csak akkor módosíthatja ezt a beállítást az alkalmazásjegyzék-szerkesztő használatával, ha:

* A fióktípusokat **azureADMyOrg** vagy **AzureADMultipleOrgs** rendszerről **AzureAD andPersonalMicrosoftAccount-ra,** vagy fordítva módosítja, vagy
* A fióktípusokat **AzureADMyOrg-ról** **AzureADMultipleOrgs-ra**vagy fordítva módosíthatja.

Meglévő alkalmazásregisztráció támogatott fióktípusainak módosításához `signInAudience` frissítse a kulcsot. További információt [az Alkalmazásjegyzék konfigurálása című témakörben talál.](reference-app-manifest.md#configure-the-app-manifest)

## <a name="configure-platform-settings-for-your-application"></a>Platformbeállítások konfigurálása az alkalmazáshoz

![Az alkalmazás beállításainak konfigurálása a platform vagy az eszköz alapján](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Ha az alkalmazásbeállításokat a platform vagy az eszköz alapján szeretné konfigurálni, a következőket kell megcéloznia:

1. A **Platform konfigurációk** lapján válassza **a Platform hozzáadása** lehetőséget, és válasszon a rendelkezésre álló lehetőségek közül.

   ![A Platformok konfigurálása lap megjelenítése](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Adja meg a beállítások adatait a kiválasztott platform alapján.

   | Platform                | Konfigurációs beállítások            |
   |-------------------------|-----------------------------------|
   | **Web**              | Adja meg az alkalmazás **átirányítási** URI-ját. |
   | **iOS / macOS**              | Adja meg az alkalmazás **Bundle ID**- amely megtalálható az XCode az Info.plist, vagy Build Beállítások. A kötegazonosító hozzáadása automatikusan létrehoz egy átirányítási URI-t az alkalmazásszámára. |
   | **Android**          | Adja meg az alkalmazás **csomag nevét**, amely megtalálható az AndroidManifest.xml fájlban.<br/>Az aláírási kivonat létrehozása és **beírása.** Az aláírás-kivonat hozzáadása automatikusan létrehoz egy átirányítási URI-t az alkalmazásszámára.  |
   | **Mobil- és asztali alkalmazások**  | Választható. Válassza ki az ajánlott **átirányítási URI-k** egyikét, ha asztali és eszközalkalmazásokat hoz létre.<br/>Választható. Adjon meg egy **egyéni átirányítási URI-t,** amely az a hely, ahol az Azure AD a hitelesítési kérelmekre válaszul átirányítja a felhasználókat. Ha például a .NET Core alkalmazásokhoz, ahol interakciót szeretne használni, használja a használatát. `https://localhost` |

   > [!IMPORTANT]
   > Azon mobilalkalmazások esetében, amelyek nem a legújabb Microsoft Authentication Library (MSAL) könyvtárat vagy nem közvetítőt használnak, az asztali + eszközök ben konfigurálnia kell az átirányítási URI-kat ezekhez az **alkalmazásokhoz.**

A választott platformtól függően további beállítások at is konfigurálhat. **Webalkalmazások** esetén a következőket teheti:

* További átirányítási URI-k hozzáadása
* Konfigurálja az **implicit támogatást** az engedélyezési végpont által kiadni kívánt jogkivonatok kiválasztásához:

  * Egyoldalas alkalmazások esetén válassza ki az **Access-jogkivonatokat** és **az Azonosító-jogkivonatokat is.**
  * Webalkalmazások esetén válassza az **Azonosítótokenek lehetőséget**

## <a name="add-credentials-to-your-web-application"></a>Hitelesítő adatok hozzáadása a webalkalmazáshoz

Hitelesítő adatok hozzáadásához a webalkalmazáshoz, vagy adjon hozzá egy tanúsítványt, vagy hozzon létre egy ügyféltitkos kulcsot. Tanúsítvány hozzáadása:

1. Az alkalmazás **áttekintése** lapon válassza a **Tanúsítványok & titkos kulcsok** szakaszt.
1. Válassza a **Tanúsítvány feltöltése** lehetőséget.
1. Válassza ki a feltölteni kívánt fájlt. A fájlnak a következő típusok egyikébe kell tartoznia: .cer, .pem vagy .crt.
1. Válassza a **Hozzáadás** lehetőséget.

Ügyféltitok hozzáadása:

1. Az alkalmazás **áttekintése** lapon válassza a **Tanúsítványok & titkos kulcsok** szakaszt.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg titkos ügyfélkódja leírását.
1. Válassza ki az időtartamot.
1. Válassza a **Hozzáadás** lehetőséget.

> [!NOTE]
> A konfiguráció módosításainak mentése után a jobb szélső oszlop tartalmazza majd a titkos ügyfélkód értékét. **Mindenképp másolja le az értéket** az ügyfélalkalmazás kódolása során való használathoz, mivel a lap elhagyását követően nem lehet majd megtekinteni.

## <a name="add-permissions-to-access-web-apis"></a>Webes API-k elérésére vonatkozó engedélyek hozzáadása

A [Graph API bejelentkezési és olvasási felhasználói profil engedély](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) alapértelmezés szerint be van jelölve. Az egyes webes [API-khoz kétféle engedély közül](developer-glossary.md#permissions) választhat:

* **Alkalmazásengedélyek**. Az ügyfélalkalmazásnak közvetlenül, felhasználói környezet nélkül kell hozzáférnie a webes API-hoz. Az ilyen típusú engedélyhez rendszergazdai hozzájárulás szükséges. Ez az engedély asztali és mobilügyfél-alkalmazások esetében nem érhető el.
* **Delegált engedélyek**. Az ügyfélalkalmazásnak hozzá kell férnie a webes API-hoz, mint bejelentkezett felhasználónak, de a kijelölt engedély által korlátozott hozzáféréssel. Az ilyen típusú engedély megadható a felhasználó által, hacsak nem igényel rendszergazdai hozzájárulást.

  > [!NOTE]
  > A delegált engedély alkalmazáshoz való hozzáadása nem biztosítja automatikusan a hozzájárulást a bérlőn belüli felhasználóknak. A felhasználóknak továbbra is manuálisan kell a futásidőben hozzájárulniuk a delegált engedélyeket, hacsak a rendszergazda nem hagyja azokat jóvá minden felhasználó nevében.

Az ügyfél erőforrás-API-k eléréséhez szükséges engedélyek hozzáadása:

1. Az alkalmazás **áttekintése** lapon válassza az **API-engedélyek lehetőséget.**
1. A **Konfigurált engedélyek**csoportban válassza **az Engedély hozzáadása**lehetőséget.
1. Alapértelmezés szerint ebben a nézetben választhat a **Microsoft API-k** közül. Válassza ki az Önt érdeklő API-k szakaszát:

    * **Microsoft API-k**. Lehetővé teszi a Microsoft API-k, például a Microsoft Graph engedélyeinek kiválasztását.
    * **A szervezet által használt API-k.** Lehetővé teszi, hogy olyan API-kengedélyeit válasszon ki, amelyeket a szervezet elérhetővé tett, vagy olyan API-kat, amelyekkel a szervezet integrált.
    * **Saját API-k**. Lehetővé teszi a elérhető vétek engedélyeinek kiválasztását.

1. Miután kiválasztotta az API-kat, **Az API-engedélyek kérése** lap jelenik meg. Ha az API delegált és alkalmazásengedélyeket egyaránt közzétesz, válassza ki, milyen típusú engedélyre van szüksége az alkalmazásának.
1. Miután végzett, válassza az **Engedélyek hozzáadása** lehetőséget.

Visszatér az **API-engedélyek** lapra. Az engedélyek et mentette, és hozzáadta a táblához.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Az API-engedélyek és a rendszergazdai hozzájárulás felhasználói felületének ismertetése

### <a name="configured-permissions"></a>Konfigurált engedélyek

Ez a szakasz az alkalmazásobjektumon explicit módon konfigurált engedélyeket mutatja be. Ezek az engedélyek az alkalmazás szükséges erőforrás-hozzáférési listájának részét képezik. A táblából engedélyeket adhat hozzá vagy távolíthat el. Rendszergazdaként rendszergazdaként rendszergazdai jóváhagyást is adhat vagy visszavonhat egy API-engedélyhez vagy egyéni engedélyekhez.

### <a name="other-permissions-granted"></a>Egyéb megadott engedélyek

Ha az alkalmazás regisztrálva van egy bérlőben, egy további szakasz jelenhet meg a **Bérlő számára megadott Egyéb engedélyek**címmel. Ez a szakasz azokat a bérlőhöz megadott engedélyeket jeleníti meg, amelyek nincsenek explicit módon konfigurálva az alkalmazásobjektumon. Ezeket az engedélyeket dinamikusan kérték és hagyták abba. Ez a szakasz csak akkor jelenik meg, ha legalább egy érvényes engedély van.

Az API-engedélyek vagy az ebben a szakaszban megjelenő egyéni engedélyek készletét hozzáadhatja a **Konfigurált engedélyek** szakaszhoz. Rendszergazdaként ebben a szakaszban visszavonhatja az egyes API-k vagy engedélyek rendszergazdai beleegyezését is.

### <a name="admin-consent-button"></a>Rendszergazdai hozzájárulás gomb

Ha az alkalmazás regisztrálva van egy bérlőben, megjelenik egy **Grant rendszergazdai hozzájárulás a bérlő** gombra. Le van tiltva, ha nem rendszergazda, vagy ha nincs enek engedélyek konfigurálva az alkalmazáshoz.
Ez a gomb lehetővé teszi, hogy a rendszergazda engedélyt adjon az alkalmazáshoz konfigurált engedélyekhez. A rendszergazdai hozzájárulás gombra kattintva egy új ablak indul el, amelyen megjelenik az összes konfigurált engedély.

> [!NOTE]
> Az alkalmazáshoz konfigurált engedélyek és a hozzájárulási parancsban megjelenő engedélyek között késés áll fenn. Ha nem látja az összes konfigurált engedélyt a jóváhagyási parancssorban, zárja be, és indítsa el újra.

Ha rendelkezik a megadott, de konfigurált engedélyekkel, a rendszergazdai hozzájárulás gomb kéri, hogy kezelje ezeket az engedélyeket. Hozzáadhatja őket a konfigurált engedélyekhez, vagy eltávolíthatja őket.

A hozzájárulási parancs lehetővé teszi az **Elfogadás** vagy **a Mégse beállítást.** Válassza **az Elfogadás lehetőséget** a rendszergazdai jóváhagyás megadásához. Ha a **Mégse lehetőséget választja,** a rendszergazdai jóváhagyás nem adható meg. Egy hibaüzenet azt írja, hogy a hozzájárulás elutasítva.

> [!NOTE]
> A rendszergazdai jóváhagyás megadása között késés áll fenn a hozzájárulás **imasszolóparancs** ának kiválasztásával, és a rendszergazdai hozzájárulás állapota megjelenik a portálon.

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan teheti elérhetővé a webes API-kat.
> [!div class="nextstepaction"]
> [Rövid útmutató: Egy alkalmazás konfigurálása webes API-k felfedésére](quickstart-configure-app-expose-web-apis.md)

* Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

* Ha szeretne többet megtudni az Azure Active Directoryt használó alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, tekintse meg az [alkalmazások védjegyzési irányelveit](howto-add-branding-in-azure-ad-apps.md) ismertető szakaszt.

* [Rövid útmutató: Alkalmazás regisztrálása a Microsoft identitásplatformmal](quickstart-register-app.md)

* [Rövid útmutató: Az alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)

* [Rövid útmutató: A Microsoft identitásplatformján regisztrált alkalmazás eltávolítása](quickstart-remove-app.md)
