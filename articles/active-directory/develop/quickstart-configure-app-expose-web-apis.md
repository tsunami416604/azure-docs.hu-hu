---
title: Alkalmazás konfigurálása webes API-k közzétételére (előzetes verzió) | Azure
description: Megtudhatja, hogyan konfigurálhat alkalmazást új engedély/hatókör és szerepkör közzétételére, hogy elérhetővé tegye az alkalmazást az ügyfélalkalmazások számára.
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
ms.openlocfilehash: 5b72bd45299f636a208ab39be5b7eaade2730183
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814014"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis-preview"></a>Gyors útmutató: Egy alkalmazást úgy teszi közzé a webes API-k (előzetes verzió)

Fejleszthet saját webes API-kat,és [engedélyek/hatókörök](developer-glossary.md#scopes) és [szerepkörök](developer-glossary.md#roles) közzétételével elérhetővé teheti azokat az ügyfélalkalmazások számára. A megfelelően konfigurált webes API-k a Microsoft többi webes API-jához hasonlóan érhetők el, mint például a Graph API vagy az Office 365 API-k.

Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhat alkalmazást új hatókör közzétételére, hogy elérhetővé tegye ügyfélalkalmazások számára.

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
1. Válassza ki az új hatókör közzétételéhez használni kívánt módszert (felhasználói felület vagy alkalmazásjegyzék):
    * [Új hatókör közzététele a felhasználói felületen](#expose-a-new-scope-through-the-ui)
    * [Új hatókör vagy szerepkör közzététele az alkalmazásjegyzékben](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Új hatókör közzététele a felhasználói felületen

[![API közzététele a felhasználói felületen](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Ha a felhasználói felületen kíván közzétenni új hatókört:

1. Az alkalmazás **Áttekintés** lapján válassza az **API közzététele** szakaszt.

1. Válassza a **Hatókör hozzáadása** lehetőséget.

1. Ha nem állított be **Alkalmazásazonosító URI-t**, erre vonatkozó kérés jelenik meg. Adja meg alkalmazásazonosító URI-ját, vagy használja a megadottat, majd válassza a **Mentés és folytatás** lehetőséget.

1. A megjelenő **Hatókör hozzáadása** lapon az alábbiak szerint adja meg a hatókör adatait:

    | Mező | Leírás |
    |-------|-------------|
    | **Hatókör neve** | Adjon kifejező nevet a hatókörnek.<br><br>Például: `Employees.Read.All`. |
    | **Ki adhat hozzájárulást?** | Válassza ki, hogy ehhez a hatókörhöz hozzájárulhatnak a felhasználók, vagy rendszergazdai jóváhagyásra van szükség. Magasabb jogosultsági szintű engedélyeknél válassza a **Csak rendszergazdák** lehetőséget. |
    | **Rendszergazdai hozzájárulás megjelenítendő neve** | Adjon meg kifejező leírást a hatókörhöz a rendszergazdák számára.<br><br>Például: `Read-only access to Employee records` |
    | **Rendszergazdai jóváhagyás leírása** | Adjon meg kifejező leírást a hatókörhöz a rendszergazdák számára.<br><br>Például: `Allow the application to have read-only access to all Employee data.` |

    Ha a felhasználók jóváhagyhatják a hatókört, akkor az alábbi mezőket is töltse ki:

    | Mező | Leírás |
    |-------|-------------|
    | **Felhasználói jóváhagyás megjelenítendő neve** | Adjon kifejező nevet a hatókörnek a felhasználók számára.<br><br>Például: `Read-only access to your Employee records` |
    | **Felhasználói jóváhagyás leírása** | Adjon meg kifejező leírást a hatókörhöz a felhasználók számára.<br><br>Például: `Allow the application to have read-only access to your Employee data.` |

1. Állítsa be az **Állapotot**, és ha kész, válassza a **Hatókör hozzáadása** lehetőséget.

1. A megadott lépéseket követve [ellenőrizze, hogy a webes API közzététele sikerült-e a többi alkalmazás számára](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Új hatókör vagy szerepkör közzététele az alkalmazásjegyzékben

[![Új hatókör közzététele az oauth2Permissions gyűjtemény használatával az alkalmazásjegyzékben](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Ha az alkalmazásjegyzékben kívánja közzétenni új hatókört:

1. Az alkalmazás **Áttekintés** lapján válassza az **Alkalmazásjegyzék** szakaszt. Megnyílik egy webalapú jegyzékfájlszerkesztő, amellyel a portálon **szerkesztheti** a jegyzékfájlt. Másik lehetőségként a **Letöltés** lehetőséget választva a helyi gépen is szerkesztheti az alkalmazásjegyzéket, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra.
    
    Ebben a példában bemutatjuk, hogyan teheti közzé az `Employees.Read.All` nevű új hatókört az erőforrásban/API-ban az alábbi JSON-elem `oauth2Permissions` gyűjteménybe való felvételével.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

  > [!NOTE]
  > Az `id` értéket programozott módon vagy GUID-előállító eszköz, például a [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) használatával kell előállítani. Az `id` a webes API által közzétett hatókör egyedi azonosítója. Ha az ügyfél megfelelően van konfigurálva a webes API eléréséhez szükséges engedélyekkel, az Azure AD kioszt a számára egy OAuth 2.0 hozzáférési jogkivonatot. Amikor az ügyfél meghívja a webes API-t, bemutatja a hozzáférési jogkivonatot, amelyben a hatókör (scp) jogcíme az ügyfél alkalmazásregisztrációjában igényelt engedélyekre van állítva.
  >
  > A későbbiekben igény szerint további hatóköröket is közzétehet. Vegye figyelembe, hogy a webes API több hatókört is közzétehet, amelyek különféle függvényekkel vannak társítva. Az erőforrás a futásidőben a kapott OAuth 2.0 hozzáférési jogkivonatban lévő hatókör (`scp`) jogcímének vagy jogcímeinek értékelésével szabályozhatja a hozzáférést a webes API-hoz.

1. Amikor végzett, kattintson a **Mentés** gombra. A webes API most konfigurálva lett a könyvtárban lévő egyéb alkalmazások általi használatra.
1. A megadott lépéseket követve [ellenőrizze, hogy a webes API közzététele sikerült-e a többi alkalmazás számára](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>A webes API többi alkalmazás számára való közzétételének ellenőrzése

1. Lépjen vissza az Azure AD-bérlőre, válassza az **Alkalmazásregisztrációk** lehetőséget, majd keresse meg és válassza ki a konfigurálni kívánt ügyfélalkalmazást.
1. Ismételje meg a konfigurálása egy webes API-k eléréséhez ügyfélalkalmazás leírt lépéseket.
1. Amikor az **API kiválasztása** lépéshez ér, válassza ki az erőforrást. Meg kell jelennie az új hatókörnek, amely így már elérhető az ügyfelek engedélykéréseihez.

## <a name="more-on-the-application-manifest"></a>További információk az alkalmazásjegyzékről

Az alkalmazásjegyzék az alkalmazásentitás frissítésére szolgáló mechanizmus, amely az Azure AD-alkalmazások identitáskonfigurációjának attribútumait határozza meg. Az Application entitással és annak sémájával kapcsolatos további információkat [a Graph API alkalmazás entitásdokumentációjában](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity) találja. A cikk teljes körű referenciainformációkat tartalmaz az Application entitás API-engedélyek megadásához használt tagjairól, beleértve a következőket:  

* Az appRoles tag, amely egy webes API [alkalmazásengedélyeinek](developer-glossary.md#permissions) meghatározásához használt [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entitások gyűjteménye.
* Az oauth2Permissions tag, amely egy webes API [delegált engedélyeinek](developer-glossary.md#permissions) meghatározásához használt [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entitások gyűjteménye.

Az alkalmazásjegyzékekkel kapcsolatos fogalmakról általános felvilágosítást nyújt [Az Azure Active Directory-alkalmazásjegyzék megismerése](reference-app-manifest.md) című cikk.

## <a name="next-steps"></a>További lépések

Tekintse meg az alkalmazásokra vonatkozó alábbi rövid alkalmazásfelügyeleti útmutatókat is:

* [Alkalmazás regisztrálása a Microsoft Identity Platformon](quickstart-register-app.md)
* [Ügyfélalkalmazás konfigurálása a webes API-k elérésére](quickstart-configure-app-access-web-apis.md)
* [Alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)
* [Microsoft Identity Platformon regisztrált alkalmazás eltávolítása](quickstart-remove-app.md)

Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

Ha szeretne többet megtudni az Azure Active Directoryt használó alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, tekintse meg az [alkalmazások védjegyzési irányelveit](howto-add-branding-in-azure-ad-apps.md) ismertető szakaszt.
