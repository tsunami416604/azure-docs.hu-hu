---
title: 'Rövid útmutató: az App web API-k elérése – Microsoft Identity platform | Azure'
description: Ebben a rövid útmutatóban egy Microsoft Identity platformmal regisztrált alkalmazást konfigurálhat az átirányítási URI-k, a hitelesítő adatok vagy a webes API-k eléréséhez szükséges engedélyek belefoglalásához.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82082322"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Gyors útmutató: ügyfélalkalmazás konfigurálása a webes API-k eléréséhez

Ebben a rövid útmutatóban átirányítási URI-kat, hitelesítő adatokat vagy engedélyeket ad hozzá az alkalmazás webes API-jai eléréséhez. Egy webes vagy bizalmas ügyfélalkalmazás biztonságos hitelesítő adatokat kell létrehoznia ahhoz, hogy részt vegyen a hitelesítést igénylő engedélyezési folyamatokban. Az Azure Portal által támogatott alapértelmezett hitelesítési módszer egy ügyfél-azonosítót és egy titkos kulcsot igényel. Az alkalmazás egy hozzáférési jogkivonatot kap a folyamat során.

Ahhoz, hogy az ügyfél hozzáférhessen egy erőforrás-alkalmazás által elérhető webes API-hoz (például Microsoft Graph API-hoz), a hozzájárulási keretrendszer biztosítja, hogy az ügyfél megkapja a kért engedélyekhez szükséges engedélyeket. Alapértelmezés szerint az összes alkalmazás engedélyt kérhet a Microsoft Graph API-tól.

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutató befejezése [: az alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md).
* Az engedélyek és a hozzájárulások áttekintése [a Microsoft Identity platform végpontján](v2-permissions-and-consent.md).
* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Bejelentkezés az Azure Portalra és az alkalmazás kiválasztása

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban. Állítsa be a portál munkamenetét a kívánt Azure AD-bérlőre.
1. Keresse meg és válassza ki az **Azure Active Directoryt**. A **kezelés**területen válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kiválasztotta az alkalmazást, megjelenik az alkalmazás **áttekintése** vagy a fő regisztrációs oldal.

Az alábbi eljárások segítségével konfigurálhatja az alkalmazást a webes API-k eléréséhez.

## <a name="add-redirect-uris-to-your-application"></a>Átirányítási URI-k hozzáadása az alkalmazáshoz

Egyéni átirányítási URI-k és javasolt átirányítási URI-k is hozzáadhatók az alkalmazáshoz. Egyéni átirányítási URI hozzáadása webes és nyilvános ügyfélalkalmazások számára:

1. Az alkalmazás **áttekintése** lapon válassza a **hitelesítés**lehetőséget.
1. **Átirányítási URI**-k megkeresése. Előfordulhat, hogy ki kell választania **a váltás a régi felhasználói felületre**lehetőséget.
1. Válassza ki az Ön által felépített alkalmazás típusát: **webes** vagy **nyilvános ügyfél/natív (Mobile & Desktop)**.
1. Adja meg alkalmazása átirányítási URI-ját.

   * Webalkalmazások esetében az alkalmazás alap URL-címét adja meg. A `http://localhost:31544` például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
   * Nyilvános alkalmazások esetében azt az URI-t adja meg, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adja meg az alkalmazáshoz tartozó értéket, például: `https://MyFirstApp`.
1. Kattintson a **Mentés** gombra.

A nyilvános ügyfelek javasolt átirányítási URI-azonosítóinak kiválasztásához kövesse az alábbi lépéseket:

1. Az alkalmazás **áttekintése** lapon válassza a **hitelesítés**lehetőséget.
1. **Javasolt átirányítási URI-k keresése a nyilvános ügyfelekhez (mobil, asztali)**. Előfordulhat, hogy ki kell választania **a váltás a régi felhasználói felületre**lehetőséget.
1. Válasszon ki egy vagy több átirányítási URI-t az alkalmazáshoz. Egyéni átirányítási URI-t is megadhat. Ha nem tudja, mit kell használni, tekintse meg a könyvtár dokumentációját.
1. Kattintson a **Mentés** gombra.

Bizonyos korlátozások vonatkoznak az átirányítási URI-k használatára. További információ: az [átirányítási URI/válasz URL-címekre vonatkozó korlátozások és korlátozások](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Próbálja ki az új **hitelesítési** beállításokat, ahol konfigurálhatja az alkalmazás beállításait a célként használni kívánt platform vagy eszköz alapján.
>
> A nézet megtekintéséhez válassza **az új felület kipróbálása** a **hitelesítés** lapról lehetőséget.
>
> ![A platform-konfiguráció nézet megjelenítéséhez kattintson az "próbálja ki az új felület" lehetőséget.](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Ekkor megjelenik az [új platform- **konfigurációk** oldal](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Speciális beállítások konfigurálása az alkalmazáshoz

A regisztrálni kívánt alkalmazástól függően szükség lehet néhány további beállításra, például a következőkre:

* **Kijelentkezési URL-cím**.
* Egyoldalas alkalmazások esetében engedélyezheti az **implicit hozzáférést** , és kiválaszthatja azokat a jogkivonatokat, amelyeket az engedélyezési végpontnak ki szeretne állítani.
* Az integrált Windows-hitelesítéssel, az eszköz kódjának folyamatával vagy a username/Password értékkel rendelkező asztali alkalmazások esetében az **alapértelmezett ügyfél típusa** szakaszban állítsa az **alkalmazás kezelése nyilvános ügyfélként** beállítást **Igen**értékre.
* Az élő SDK-val a Microsoft-fiók szolgáltatással való integrációhoz használt örökölt alkalmazások esetében konfigurálja az **élő SDK-támogatást**. Az új alkalmazásokhoz nincs szükség erre a beállításra.
* **Alapértelmezett ügyfél típusa**.
* **Támogatott fióktípus**.

### <a name="modify-supported-account-types"></a>Támogatott fióktípus módosítása

A **támogatott fióktípus** határozza meg, hogy ki használhatja az alkalmazást, vagy hogyan férhet hozzá az API-hoz.

Ha a támogatott fióktípus konfigurálását az alkalmazás regisztrálása során állította be, akkor csak akkor módosíthatja ezt a beállítást az Application manifest Editor használatával, ha:

* A **AzureADMyOrg** vagy **AzureADMultipleOrgs** típusú fiókokat a **AzureADandPersonalMicrosoftAccount**, vagy fordítva, vagy
* A **AzureADMyOrg** a **AzureADMultipleOrgs**, a másikat pedig a következő módon változtathatja meg.

Ha módosítani szeretné egy meglévő alkalmazás regisztrált fiókjának típusát, frissítse a `signInAudience` kulcsot. További információ: [az alkalmazás jegyzékfájljának konfigurálása](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>A platform beállításainak konfigurálása az alkalmazáshoz

![Az alkalmazás beállításainak konfigurálása a platform vagy az eszköz alapján](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Az Alkalmazásbeállítások a platform vagy az eszköz alapján történő konfigurálásához célozza meg a következőt:

1. A **platform-konfigurációk** lapon válassza a **platform hozzáadása** lehetőséget, és válasszon az elérhető lehetőségek közül.

   ![A platformok konfigurálása lap megjelenítése](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Adja meg a beállítások információit a kiválasztott platform alapján.

   | Platform                | Konfigurációs beállítások            |
   |-------------------------|-----------------------------------|
   | **Web**              | Adja meg az alkalmazás **átirányítási URI-ját** . |
   | **iOS/macOS**              | Adja meg az alkalmazáscsomag **azonosítóját**, amelyet az info. plist fájlban találhat, vagy létrehozhat beállításokat a Xcode. A köteg-azonosító hozzáadása automatikusan létrehoz egy átirányítási URI-t az alkalmazáshoz. |
   | **Android**          | Adja meg az **alkalmazáscsomag nevét**, amelyet a AndroidManifest. xml fájlban talál.<br/>Adja meg és írja be az **aláírási kivonatot**. Az aláírási kivonat hozzáadásakor a rendszer automatikusan létrehoz egy átirányítási URI-t az alkalmazáshoz.  |
   | **Mobil-és asztali alkalmazások**  | Választható. Válassza a javasolt **átirányítási URI** -k egyikét, ha asztali és eszközökhöz készült alkalmazásokat készít.<br/>Választható. Adjon meg egy **Egyéni átirányítási URI**-t, amely azt a helyet használja, ahol az Azure ad átirányítja a felhasználókat a hitelesítési kérésekre adott válaszként. Például olyan .NET Core-alkalmazásokhoz, ahol az interakciót szeretné `http://localhost`használni, használja a következőt:. |

   > [!NOTE]
   > Active Directory összevonási szolgáltatások (AD FS) (AD FS) és Azure AD B2C esetén a portszámot is meg kell adni.  Például: `http://localhost:1234`. 

   > [!IMPORTANT]
   > Olyan mobileszközök esetében, amelyek nem a legújabb Microsoft-hitelesítési függvénytárat (MSAL) használják, vagy nem használ közvetítőt, konfigurálnia kell az alkalmazások átirányítási URI-azonosítóit az asztali és az **eszközökön**.

A kiválasztott platformtól függően előfordulhat, hogy további beállításokat is konfigurálhat. A **web** apps esetében a következőket teheti:

* További átirányítási URI-k hozzáadása
* Az **implicit engedélyezés** konfigurálásával kiválaszthatja az engedélyezési végpont által kiállított jogkivonatokat:

  * Egyoldalas alkalmazások esetében válassza a **hozzáférési tokenek** és az **azonosító tokenek** lehetőséget.
  * Webalkalmazások esetében válassza az **azonosító tokenek** lehetőséget.

## <a name="add-credentials-to-your-web-application"></a>Hitelesítő adatok hozzáadása a webalkalmazáshoz

Ha hitelesítő adatokat szeretne felvenni a webalkalmazásba, adjon hozzá egy tanúsítványt, vagy hozzon létre egy ügyfél-titkot. Tanúsítvány hozzáadása:

1. Az alkalmazás **áttekintése** lapon válassza a **tanúsítványok & titkok** szakaszt.
1. Válassza a **Tanúsítvány feltöltése** lehetőséget.
1. Válassza ki a feltölteni kívánt fájlt. A fájlnak a következő típusok egyikébe kell tartoznia: .cer, .pem vagy .crt.
1. Válassza a **Hozzáadás** lehetőséget.

Ügyfél titkos kulcsának hozzáadása:

1. Az alkalmazás **áttekintése** lapon válassza a **tanúsítványok & titkok** szakaszt.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg titkos ügyfélkódja leírását.
1. Válassza ki az időtartamot.
1. Válassza a **Hozzáadás** lehetőséget.

> [!NOTE]
> A konfiguráció módosításainak mentése után a jobb szélső oszlop tartalmazza majd a titkos ügyfélkód értékét. **Mindenképp másolja le az értéket** az ügyfélalkalmazás kódolása során való használathoz, mivel a lap elhagyását követően nem lehet majd megtekinteni.

## <a name="add-permissions-to-access-web-apis"></a>Webes API-k elérésére vonatkozó engedélyek hozzáadása

Alapértelmezés szerint a [Graph API bejelentkezés és a felhasználói profil olvasása engedély](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) van kiválasztva. Az egyes webes API-k esetében [két típusú engedély](developer-glossary.md#permissions) közül választhat:

* **Alkalmazás engedélyei**. Az ügyfélalkalmazás a webes API-t közvetlenül saját maga, felhasználói környezet nélkül kell elérnie. Az ilyen típusú engedélyekhez rendszergazdai hozzájárulás szükséges. Ez az engedély asztali és mobil ügyfélalkalmazások esetén nem érhető el.
* **Delegált engedélyek**. Az ügyfélalkalmazás a webes API-t a bejelentkezett felhasználóként kell elérnie, de a hozzáférést a kiválasztott engedély korlátozza. Az ilyen típusú engedély megadható a felhasználó által, hacsak nem igényel rendszergazdai hozzájárulást.

  > [!NOTE]
  > A delegált engedély alkalmazáshoz való hozzáadása nem biztosítja automatikusan a hozzájárulást a bérlőn belüli felhasználóknak. A felhasználóknak továbbra is manuálisan kell a futásidőben hozzájárulniuk a delegált engedélyeket, hacsak a rendszergazda nem hagyja azokat jóvá minden felhasználó nevében.

Az erőforrás-API-khoz való hozzáféréshez szükséges engedélyek hozzáadása az ügyfélről:

1. Az alkalmazás **áttekintése** lapon válassza az **API-engedélyek**lehetőséget.
1. A **konfigurált engedélyek**területen válassza **az engedély hozzáadása**elemet.
1. Alapértelmezés szerint ebben a nézetben választhat a **Microsoft API-k** közül. Válassza ki az Önt érdeklő API-k szakaszát:

    * **Microsoft API**-k. Lehetővé teszi a Microsoft API-k, például a Microsoft Graph engedélyeinek kiválasztását.
    * **A szervezet által használt API**-k. Lehetővé teszi a szervezet által közzétett API-k engedélyeinek kiválasztását, vagy a szervezete által integrált API-kat.
    * **Saját API**-k. Lehetővé teszi az elérhető API-k engedélyeinek kiválasztását.

1. Miután kiválasztotta az API-kat, **Az API-engedélyek kérése** lap jelenik meg. Ha az API delegált és alkalmazásengedélyeket egyaránt közzétesz, válassza ki, milyen típusú engedélyre van szüksége az alkalmazásának.
1. Miután végzett, válassza az **Engedélyek hozzáadása** lehetőséget.

Térjen vissza az **API-engedélyek** oldalára. Az engedélyek el lettek mentve, és hozzá lettek adva a táblához.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Az API-engedélyek és a rendszergazdai beleegyezési felhasználói felület ismertetése

### <a name="configured-permissions"></a>Konfigurált engedélyek

Ez a szakasz az Application objektumon explicit módon konfigurált engedélyeket mutatja. Ezek az engedélyek az alkalmazás szükséges erőforrás-hozzáférési listájának részét képezik. A táblából hozzáadhat vagy eltávolíthat engedélyeket is. Rendszergazdaként a rendszergazda beleegyezését is megadhatja vagy visszavonhatja egy API engedélyeinek vagy egyéni engedélyeinek egy készlete számára.

### <a name="other-permissions-granted"></a>Egyéb megadott engedélyek

Ha az alkalmazás regisztrálva van egy bérlőben, akkor további, **a bérlőnek biztosított engedélyek**című szakasza is megjelenik. Ez a szakasz az Application objektumon explicit módon nem konfigurált bérlők számára biztosított engedélyeket jeleníti meg. Ezeket az engedélyeket dinamikusan kérték és fogadták. Ez a szakasz csak akkor jelenik meg, ha legalább egy engedély vonatkozik rá.

Hozzáadhat egy API-k engedélyeit vagy az ebben a szakaszban megjelenő egyéni engedélyeket a **konfigurált engedélyek** szakaszhoz. Rendszergazdaként az egyes API-k vagy engedélyek rendszergazdai beleegyezését is visszavonhatja ebben a szakaszban.

### <a name="admin-consent-button"></a>Rendszergazdai beleegyezett gomb

Ha az alkalmazás regisztrálva van egy bérlőben, megjelenik a **jogosultság megadása a bérlő számára** gomb. A szolgáltatás le van tiltva, ha Ön nem rendszergazda, vagy ha nincs jogosultsága konfigurálva az alkalmazáshoz.
Ez a gomb lehetővé teszi, hogy a rendszergazda rendszergazdai jóváhagyást adjon az alkalmazáshoz konfigurált engedélyekhez. A rendszergazdai jóváhagyás gombra kattintva megnyílik egy új ablak, amely az összes konfigurált engedélyt megjeleníti.

> [!NOTE]
> Az alkalmazáshoz konfigurált engedélyek és a hozzájárulási kérések között késés van. Ha nem látja az összes konfigurált engedélyt a hozzájárulási kérésben, akkor zárd be, majd indítsa el újra.

Ha rendelkezik olyan engedélyekkel, amelyek meg lettek adva, de nincs konfigurálva, a rendszergazdai jóváhagyás gomb megkéri, hogy kezelje ezeket az engedélyeket. Ezeket hozzáadhatja a konfigurált engedélyekhez, vagy eltávolíthatja őket.

A beleegyezés kérése lehetőséget biztosít az **elfogadás** vagy a **Visszavonás**lehetőségre. A rendszergazdai jóváhagyás engedélyezéséhez válassza az **elfogadás** lehetőséget. Ha a Mégse gombot választja, a rendszergazdai **Belefoglalás**nem adható meg. Egy hibaüzenet azt jelzi, hogy a rendszer elutasította a beleegyezett üzenetet.

> [!NOTE]
> A rendszergazdai jóváhagyás megadása a jóváhagyás megadásával, valamint a portálon megjelenő rendszergazdai jóváhagyás állapotával **kapcsolatos késleltetés** .

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan teheti elérhetővé a webes API-kat.
> [!div class="nextstepaction"]
> [Gyors útmutató: alkalmazás konfigurálása webes API-k megjelenítéséhez](quickstart-configure-app-expose-web-apis.md)

* Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

* Ha szeretne többet megtudni az Azure Active Directoryt használó alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, tekintse meg az [alkalmazások védjegyzési irányelveit](howto-add-branding-in-azure-ad-apps.md) ismertető szakaszt.

* [Gyors útmutató: alkalmazás regisztrálása a Microsoft Identity platformmal](quickstart-register-app.md)

* [Gyors útmutató: alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)

* [Rövid útmutató: a Microsoft Identity platformmal regisztrált alkalmazások eltávolítása](quickstart-remove-app.md)
