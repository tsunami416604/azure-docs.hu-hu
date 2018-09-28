---
title: Alkalmazások integrálása az Azure Active Directoryval
description: Ismerje meg, hogyan frissítheti az alkalmazásokat az Azure Active Directoryban (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: f311f951e09e064b8eac779b1082c666fe029479
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977244"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Rövid útmutató: Alkalmazás frissítése az Azure Active Directoryban

Az alkalmazásokat az Azure Active Directoryban (Azure AD) regisztráló nagyvállalati fejlesztőknek és SaaS-szolgáltatóknak (szoftverszolgáltatók) esetenként konfigurálniuk kell az alkalmazásokat, hogy azok egyéb erőforrásokat, például webes API-kat is használhassanak, hogy más szervezetek számára is elérhetőek legyenek.

Ebben a rövid útmutatóban megismerheti a különböző eljárásokat az alkalmazások konfigurálására és frissítésére, hogy azok megfeleljenek az Ön vagy más szervezetek követelményeinek vagy igényeinek.

## <a name="prerequisites"></a>Előfeltételek

Először is bizonyosodjon meg róla, hogy elvégezte a következő lépéseket:

* Elolvasta az [Azure AD hozzájárulási keretrendszer](consent-framework.md) áttekintését, mivel ezt fontos szem előtt tartania az olyan alkalmazások készítésekor, amelyeket más felhasználóknak vagy alkalmazásoknak is használniuk kell.
* Rendelkezik egy olyan Azure AD-bérlővel, amelyen vannak alkalmazások regisztrálva.
  * Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).
  * Ha még nincsenek alkalmazások regisztrálva a bérlőn, [ismerje meg, hogyan vehet fel alkalmazásokat az Azure AD-be](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Ügyfélalkalmazás konfigurálása a webes API-k elérésére

Ahhoz, hogy egy webes/bizalmas ügyfélalkalmazás részt vehessen egy hitelesítést (és hozzáférési jogkivonat beszerzését) előíró engedélyezési folyamatban, biztonságos hitelesítő adatokat kell létesítenie. Az Azure Portal által támogatott alapértelmezett hitelesítési módszer egy ügyfél-azonosítót és egy titkos kulcsot igényel. Ez a szakasz azokat a konfigurációs lépéseket ismerteti, amelyek a titkos kulcs ügyfél-hitelesítő adatokkal együtt való megadásához szükségesek.

Mielőtt az ügyfél hozzáférhet egy erőforrás-alkalmazás webes API-jához (például a Microsoft Graph API-t), a hozzájárulási keretrendszer biztosítja, hogy az ügyfél a kért engedélyek alapján megkapja a szükséges engedélyt. Alapértelmezés szerint minden alkalmazás az **Azure Active Directoryból** (Graph API) és a klasszikus Azure üzemi modellből választhat engedélyeket. A [Graph API „Bejelentkezés és a felhasználói profil olvasása” engedélye](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) alapértelmezés szerint szintén be van jelölve. Ha az ügyfelet egy olyan bérlőre regisztrálja, amely az Office 365-re feliratkozott fiókokkal rendelkezik, a SharePoint és az Exchange Online webes API-jai és engedélyei is választhatók. Kétféle engedély közül választhat minden egyes kívánt API esetében:

- Alkalmazásengedély: Az ügyfélalkalmazásnak a webes API-t közvetlenül a maga nevében kell elérnie (nincs felhasználói környezet). Az ilyen típusú engedély rendszergazdai hozzájárulást igényel, és a natív ügyfélalkalmazások esetében nem alkalmazható.
- Delegált engedély: Az ügyfélalkalmazásnak a webes API-t a bejelentkezett felhasználó nevében, de a kiválasztott engedély által korlátozott hozzáféréssel kell elérnie. Az ilyen típusú engedély megadható a felhasználó által, hacsak nem igényel rendszergazdai hozzájárulást.

  > [!NOTE]
  > A delegált engedély alkalmazáshoz való hozzáadása nem biztosítja automatikusan a hozzájárulást a bérlőn belüli felhasználóknak. A felhasználóknak továbbra is manuálisan kell a futásidőben hozzájárulniuk a delegált engedélyeket, hacsak a rendszergazda nem hagyja azokat jóvá minden felhasználó nevében.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Alkalmazások hitelesítő adatainak vagy engedélyeinek hozzáadása a webes API-k elérésére

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
3. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást és az **Alkalmazásregisztrációk** lehetőséget, majd keresse meg/válassza ki a konfigurálni kívánt alkalmazást.

   ![Alkalmazás regisztrációjának frissítése](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Megnyílik az alkalmazás regisztrációjának főoldala, azon belül pedig az alkalmazás **Beállítások** lapja. Hitelesítő adatok hozzáadása a webalkalmazás számára:
  1. Válassza a **Beállítások** lap **Kulcsok** szakaszát.
  2. Tanúsítvány hozzáadása:
    - Válassza a **Nyilvános kulcs feltöltése** lehetőséget.
    - Válassza ki a feltölteni kívánt fájlt. A fájlnak a következő típusok egyikébe kell tartoznia: .cer, .pem vagy .crt.
  - Jelszó hozzáadása:
    - Adjon meg egy leírást a kulcshoz.
    - Válassza ki az időtartamot.
    - Kattintson a **Mentés** gombra. A konfiguráció módosításainak mentése után a jobb szélső oszlop tartalmazza majd a kulcs értékét. **Mindenképp másolja le a kulcsot** az ügyfélalkalmazás kódolása során való használathoz, mivel a lap elhagyását követően nem lehet majd megtekinteni.

5. Engedélyek hozzáadása az erőforrások API-jainak eléréséhez az ügyfélről
  1. A **Beállítások** lap **Szükséges engedélyek** szakaszában válassza a **Hozzáadás** lehetőséget.
  1. Az **API kiválasztása** gombra kattintva válasza ki az erőforrások típusát, amelyek közül válogatni szeretne.
  1. Az elérhető API-k listájában vagy a keresőmező használatával válasszon a könyvtárban elérhető erőforrás-alkalmazások közül, amelyek webes API-t tesznek közzé. Jelölje ki a kívánt erőforrást, majd kattintson a **Kiválasztás** elemre.
  1. A **Hozzáférés engedélyezése** lapon válassza ki az alkalmazásengedélyeket és/vagy a delegált engedélyeket, amelyekre az alkalmazásnak szüksége lesz az API használatához.
   
  ![Alkalmazás regisztrációjának frissítése – engedélyek api](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

  ![Alkalmazás regisztrációjának frissítése – engedélyek eng.](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Ha végzett, kattintson a **Hozzáférés engedélyezése** lap **Kiválasztás**, majd az **API-hozzáférés hozzáadása** lap **Kész** gombjára. A rendszer visszalép a **Szükséges engedélyek** lapra, ahol az új erőforrás megjelenik az API-k listájában.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Erőforrás-alkalmazás konfigurálása webes API-k közzétételére

Fejleszthet saját webes API-kat, és hozzáférési [hatókörök](developer-glossary.md#scopes) és [szerepkörök](developer-glossary.md#roles) közzétételével elérhetővé teheti azokat az ügyfélalkalmazások számára. A megfelelően konfigurált webes API-k a Microsoft többi webes API-jához hasonlóan érhetők el, mint például a Graph API vagy az Office 365 API-k. A hozzáférési hatókörök és szerepkörök közzététele az [alkalmazás jegyzékfájljában](developer-glossary.md#application-manifest) történik. Ez egy olyan JSON-fájl, amely az alkalmazás identitáskonfigurációját ábrázolja.

A következő szakaszban mutatjuk be, hogyan tehetők közzé a hozzáférési hatókörök az erőforrás-alkalmazás jegyzékfájljával.

### <a name="add-access-scopes-to-your-resource-application"></a>Hozzáférési hatókörök hozzáadása az erőforrás-alkalmazáshoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
3. A bal oldali navigációs panelen válassza az **Azure Active Directory > Alkalmazásregisztrációk** lehetőséget, majd keresse meg/válassza ki a konfigurálni kívánt alkalmazást.

   ![Alkalmazás regisztrációjának frissítése](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Megnyílik az alkalmazás regisztrációjának főoldala, azon belül pedig az alkalmazás **Beállítások** lapja. Az alkalmazás regisztrációjának lapján a **Jegyzékfájl** gombra kattintva váltson a **Jegyzékfájl szerkesztése** lapra. Megnyílik egy webalapú jegyzékfájlszerkesztő, amellyel a portálon **szerkesztheti** a jegyzékfájlt. Másik lehetőségként a **Letöltés** gombra kattintva a helyi gépen is szerkeszthet, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra.
5. Ebben a példában a következő JSON-elem `oauth2Permissions` gyűjteménybe való felvételével egy új hatókört tárunk fel `Employees.Read.All` néven az erőforráson/API-n. A meglévő `user_impersonation` hatókör a regisztrálás során alapértelmezés szerint jött létre. A `user_impersonation` használatával az ügyfélalkalmazások engedélyt kérhetnek az erőforrás a bejelentkezett felhasználó identitásával való elérésére. Mindenképp írjon egy vesszőt a meglévő `user_impersonation` hatókörelem után, és módosítsa a tulajdonságértékeket az erőforrás igényeinek megfelelően. 

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
  > Az `id` értékét programozott módon kell előállítani, vagy egy GUID-előállító eszközzel, amilyen a [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). Az `id` a webes API által közzétett hatókör egyedi azonosítója. Ha az ügyfél megfelelően van konfigurálva a webes API eléréséhez szükséges engedélyekkel, az Azure AD kioszt a számára egy OAuth2.0 hozzáférési jogkivonatot. Amikor az ügyfél meghívja a webes API-t, bemutatja a hozzáférési jogkivonatot, amelyben a hatókör (scp) jogcíme az ügyfél alkalmazásregisztrációjában igényelt engedélyekre van állítva.
  >
  > A későbbiekben igény szerint további hatóköröket is közzétehet. Vegye figyelembe, hogy a webes API több hatókört is közzétehet, amelyek különféle függvényekkel vannak társítva. Az erőforrás a futásidőben a kapott OAuth 2.0 hozzáférési jogkivonatban lévő hatókör (`scp`) jogcímének vagy jogcímeinek értékelésével szabályozhatja a hozzáférést a webes API-hoz.

6. Amikor végzett, kattintson a **Mentés** gombra. A webes API most konfigurálva lett a könyvtárban lévő egyéb alkalmazások általi használatra.

  ![Alkalmazás regisztrációjának frissítése](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Annak ellenőrzése, hogy a webes API közzététele sikerült-e a bérlőn lévő többi alkalmazás számára

1. Lépjen vissza az Azure AD-bérlőre, és válassza ismét az **Alkalmazásregisztrációk** lehetőséget, majd keresse meg/válassza ki a konfigurálni kívánt ügyfélalkalmazást.

   ![Alkalmazás regisztrációjának frissítése](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Hajtsa ismét végre az 5. lépést az [Ügyfélalkalmazás konfigurálása a webes API-k elérésére](#configure-a-client-application-to-access-web-apis) című szakaszból. Az **API kiválasztása** lépéshez érve keressen rá az erőforrás nevére a keresőmezőben az alkalmazás nevének megadásával, majd kattintson a **Kiválasztás** gombra.

3. A **Hozzáférés engedélyezése** lapon meg kell jelennie az új hatókörnek, amely így már elérhető az ügyfelek engedélyigényléseihez.

  ![Az új engedélyek megjelennek](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>További információk az alkalmazásjegyzékről

Az alkalmazásjegyzék egy mechanizmus az Application entitás frissítésére, amely az Azure AD-alkalmazások identitáskonfigurációjának attribútumait határozza meg, beleértve a fent tárgyalt API-hozzáférési hatóköröket. Az Application entitással és annak sémájával kapcsolatos további információkat [a Graph API alkalmazás entitásdokumentációjában](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity) találja. A cikk teljes körű referenciainformációkat tartalmaz az Application entitás API-engedélyek megadásához használt tagjairól, beleértve a következőket:  

- Az appRoles tag, amely egy webes API [alkalmazásengedélyeinek](developer-glossary.md#permissions) meghatározásához használt [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entitások gyűjteménye. 
- Az oauth2Permissions tag, amely egy webes API [delegált engedélyeinek](developer-glossary.md#permissions) meghatározásához használt [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entitások gyűjteménye.

Az alkalmazásjegyzékekkel kapcsolatos fogalmakra vonatkozó további információkért lásd az [Azure AD-alkalmazásjegyzékkel](reference-app-manifest.md) foglalkozó témakört.

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Az Azure AD Graph és az Office 365 elérése a Microsoft Graph API-kon keresztül  

Ahogy korábban említettük, amellett, hogy közzéteheti/elérheti a saját alkalmazásai API-jait, az ügyfélalkalmazása regisztrálásával elérheti a Microsoft-erőforrások által közzétett API-kat is. A Microsoft Graph API (a Portal erőforrás-/API-listájában csak „Microsoft Graph” nevén szerepel) az Azure AD-ben regisztrált összes alkalmazás számára elérhető. Ha az ügyfélalkalmazást egy valamely Office 365-előfizetésre feliratkozott fiókokat tartalmazó bérlőn regisztrálja, a különféle Office 365-erőforrások által közzétett hatóköröket is elérheti.

A Microsoft Graph API által közzétett hatókörök részletes leírásáért olvassa el a [Microsoft Graph-engedélyek referenciáját](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference).

> [!NOTE]
> Egy jelenleg érvényben lévő korlátozás miatt a natív ügyfélalkalmazások csak „A szervezet címtárának elérése” engedély használatával hívhatják meg az Azure AD Graph API-t. Ez a korlátozás a webalkalmazásokra nem vonatkozik.

## <a name="configuring-multi-tenant-applications"></a>Több-bérlős alkalmazások konfigurálása

Az alkalmazások Azure AD-ben való regisztrálásakor esetenként az a cél, hogy az alkalmazást csak a szervezethez tartozó felhasználók érhessék el. Más esetekben azonban esetleg külső szervezetekbe tartozó felhasználók számára is elérhetővé szeretné tenni az alkalmazást. Ezt a két alkalmazástípust egybérlős és több-bérlős alkalmazásnak nevezzük. Ez a szakasz azt mutatja be, hogyan lehet egy egybérlős alkalmazást a konfigurációjának módosításával több-bérlős alkalmazássá alakítani.

Fontos kiemelni az egy- és a több-bérlős alkalmazások közti különbségeket:  

- Az egybérlős alkalmazások egy adott szervezetben való használatra készülnek. Ezek általában nagyvállalati fejlesztők által írt üzletági (LoB) alkalmazások. Ez egybérlős alkalmazásokat csak olyan felhasználók használhatják, akik az alkalmazásregisztrációval azonos bérlőn rendelkeznek fiókkal. Így az ilyen alkalmazásokat csak egy címtárban kell kiosztani.
- A több-bérlős alkalmazások több külön szervezetben való használatra készülnek. Ezeket szolgáltatott szoftverként (SaaS) elérhető webalkalmazásként írhatnánk le, és általában független szoftverfejlesztők (ISV) írják őket. A több-bérlős alkalmazásokat minden bérlőn ki kell osztani, ahol a felhasználóknak el kell tudniuk érni őket. Az alkalmazásregisztráció helyétől eltérő bérlőkön az ilyen alkalmazások regisztrálásához felhasználói vagy rendszergazdai hozzájárulás szükséges. Fontos megjegyezni, hogy a natív ügyfélalkalmazások alapértelmezetten több-bérlősek, mivel az erőforrás-tulajdonos eszközén vannak telepítve. A hozzájárulási keretrendszerrel kapcsolatos információkért lásd [a hozzájárulási keretrendszert áttekintő](#overview-of-the-consent-framework) megelőző szakaszt.

Az alkalmazás több-bérlőssé tételéhez módosítani kell az alkalmazásregisztrációt, valamint magát a webalkalmazást is. Az alábbi szakaszokban mindkettőre kitérünk.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Az alkalmazásregisztráció módosítása a több-bérlős működés támogatására

Ha olyan alkalmazást ír, amelyet a szervezeten kívüli ügyfelei és partnerei számára is elérhetővé szeretne tenni, frissítenie kell az alkalmazás definícióját az Azure Portalon.

> [!IMPORTANT]
> Az Azure AD megköveteli, hogy a több-bérlős alkalmazások alkalmazásazonosító URI-ja (App ID URI) globálisan egyedi legyen. Az alkalmazásazonosító URI egy módszer, amellyel az alkalmazás a protokollüzenetekben azonosítható. Egybérlős alkalmazás esetében az alkalmazásazonosító URI-nak csak a bérlőn belül kell egyedinek lennie. Több-bérlős alkalmazás esetében azonban globálisan egyedinek kell lennie, hogy az Azure AD megtalálja az alkalmazást a különböző bérlők közt.
> A globális egyediség azzal kényszeríthető ki, hogy a rendszer megköveteli, hogy az alkalmazásazonosító URI egy olyan egy gazdanévvel rendelkezzen, amely egyezik az Azure AD-bérlő egy ellenőrzött tartományával. Ha például a bérlő neve contoso.onmicrosoft.com, egy érvényes alkalmazásazonosító URI lehet a https://contoso.onmicrosoft.com/myapp. Ha a bérlő a contoso.com ellenőrzött tartománnyal rendelkezik, az érvényes alkalmazásazonosító URI ugyanígy a https://contoso.com/myapp lehet. Ha az alkalmazásazonosító URI nem ezt a mintát követi, az alkalmazások több-bérlősként való konfigurálása meghiúsul.

Az alkalmazás hozzáférésének engedélyezése külső felhasználók számára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiókja több bérlőhöz is biztosít hozzáférést, kattintson a fiókra az oldal jobb felső sarkában, és állítsa a portál munkamenetét a kívánt Azure AD-bérlőre.
3. A bal oldali navigációs panelen kattintson az **Azure Active Directory** szolgáltatásra, majd az **Alkalmazásregisztrációk** lehetőségre, és keresse meg/válassza a konfigurálni kívánt alkalmazást. Megnyílik az alkalmazás regisztrációjának főoldala, azon belül pedig az alkalmazás **Beállítások** lapja.
4. A **Beállítások** lapon kattintson a **Tulajdonságok** lehetőségre, és állítsa a **Több-bérlős** kapcsolót **Igen** állásba.

Miután elvégezte a módosításokat, a más szervezetekben lévő felhasználók és rendszergazdák engedélyezhetik a felhasználók számára az alkalmazásba való bejelentkezést, így az alkalmazás elérheti az adott bérlők által védett erőforrásokat.

### <a name="changing-the-application-to-support-multi-tenant"></a>Az alkalmazás módosítása a több-bérlős működés támogatására

A több-bérlős alkalmazások támogatása nagy mértékben támaszkodik az Azure AD hozzájárulási keretrendszerére. A hozzájárulás az a mechanizmus, amelynek révén egy másik bérlőn lévő felhasználó engedélyt adhat az alkalmazásnak a felhasználó bérlője által védett erőforrások elérésére. Ezt a folyamatot „felhasználói hozzájárulásnak” nevezzük.

A webalkalmazás emellett lehetővé teheti a következőket:

- A rendszergazdák regisztrálhatják a saját vállalatukat. Ez a folyamatot „rendszergazdai hozzájárulásnak” nevezzük, és a rendszergazda ezáltal a szervezet *összes felhasználója* nevében adhatja meg a hozzájárulást. A rendszergazdai hozzájárulást kizárólag a globális rendszergazdai szerepkörhöz tartozó fiókkal bejelentkezett felhasználó adhatja meg, más felhasználók hibaüzenetet kapnak.
- Egy bejelentkezési folyamatot biztosíthat a felhasználók számára. Várhatóan a felhasználóknál megjelenik egy „regisztrációs” gomb, amely átirányítja a böngészőt az Azure AD OAuth2.0 `/authorize`-végpontra vagy egy OpenID Connect `/userinfo`-végpontra. Ezeken a végpontokon az alkalmazás információkat kérhet le az új felhasználóról az id_token vizsgálatával. A bejelentkezési folyamatot követően a felhasználó egy hozzájárulási kérést kap, amint az [a hozzájárulási keretrendszert áttekintő](#overview-of-the-consent-framework) szakaszban is látható.

Az alkalmazás több-bérlős hozzáféréséhez és a bejelentkezési/kijelentkezési folyamatok támogatásához szükséges módosításaival kapcsolatos további információkért lásd:

- [Az Azure Active Directory (AD) felhasználóinak bejelentkeztetése több-bérlős alkalmazásminta használatával](howto-convert-app-to-be-multi-tenant.md)
- A [több-bérlős kódminták](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) listája.
- [Rövid útmutató: Vállalati arculat megjelenítése az Azure AD bejelentkezési oldalán](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>OAuth 2.0 implicit engedélyezési folyamatának engedélyezése az egyoldalas alkalmazásokon

Az egyoldalas alkalmazások (SPA-k) általában egy böngészőben futó JavaScript-intenzív előtérrel rendelkeznek, amely az alkalmazás webes API-s hátterét hívja meg az üzleti logika végrehajtatására. Az Azure AD-ben üzemeltetett egyoldalas alkalmazások esetében az OAuth 2.0 implicit engedélyezés használatával hitelesítheti a felhasználókat az Azure AD-ben, és szerezheti be a jogkivonatot, amelynek használatával biztosíthatja a hívásokat az alkalmazás JavaScript-ügyfeléről a háttér webes API-jára.

Miután a felhasználó megadta a hozzájárulást, ugyanez a hitelesítési protokoll használható a jogkivonatok beszerzéséhez az ügyfél és az alkalmazáshoz konfigurált egyéb webes API erőforrások közötti hívások biztosításához. Ha többet szeretne megtudni az implicit engedélyezésről és annak alkalmazhatóságáról az egyes alkalmazás-forgatókönyvekben, tekintse át [az Azure Active Directory OAuth2 implicit engedélyezési folyamatát](v1-oauth2-implicit-grant-flow.md) ismertető szakaszt.

Az OAuth 2.0 implicit engedélyezés alapértelmezés szerint le van tiltva az alkalmazásokon. Ha engedélyezni szeretné az OAuth 2.0 implicit engedélyezést az alkalmazáson, állítsa be az `oauth2AllowImplicitFlow` értéket az [alkalmazásjegyzékben](reference-app-manifest.md).

### <a name="to-enable-oauth-20-implicit-grant"></a>Az OAuth 2.0 implicit engedélyezési folyamat engedélyezése

> [!NOTE]
> Az alkalmazásjegyzék szerkesztésével kapcsolatban mindenképp tekintse át [az erőforrás-alkalmazások webes API-k közzétételére való konfigurálását](#configuring-a-resource-application-to-expose-web-apis) ismertető előző szakaszt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiókja több bérlőhöz is biztosít hozzáférést, kattintson a fiókra az oldal jobb felső sarkában, és állítsa a portál munkamenetét a kívánt Azure AD-bérlőre.
3. A bal oldali navigációs panelen kattintson az **Azure Active Directory** szolgáltatásra, majd az **Alkalmazásregisztrációk** lehetőségre, és keresse meg/válassza a konfigurálni kívánt alkalmazást. Megnyílik az alkalmazás regisztrációjának főoldala, azon belül pedig az alkalmazás **Beállítások** lapja.
4. Az alkalmazás regisztrációjának lapján a **Jegyzékfájl** gombra kattintva váltson a **Jegyzékfájl szerkesztése** lapra. Megnyílik egy webalapú jegyzékfájlszerkesztő, amellyel a portálon **szerkesztheti** a jegyzékfájlt. Keresse meg az „oauth2AllowImplicitFlow” elemet, és állítsa „true” (igaz) értékre. Alapértelmezés szerint „false” (hamis) értékre van állítva.
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Mentse a frissített jegyzékfájlt. A mentést követően a webes API most már konfigurálva van az OAuth 2.0 implicit engedélyezés használatára a felhasználók hitelesítése során.

## <a name="next-steps"></a>További lépések

Tekintse át az Azure AD 1.0-s verziójú végpontját használó alkalmazásokra vonatkozó alábbi alkalmazásfelügyeleti útmutatókat:
- [Alkalmazás felvétele az Azure AD-be](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Alkalmazás eltávolítása az Azure AD-ből](quickstart-v1-remove-azure-ad-app.md)

Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

Ha szeretne többet megtudni az Azure Active Directoryt használó alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, tekintse meg az [alkalmazások védjegyzési irányelveit](howto-add-branding-in-azure-ad-apps.md) ismertető szakaszt.