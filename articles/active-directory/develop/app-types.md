---
title: Az 1.0-s verziójú alkalmazástípusok |} Az Azure
description: Ismerteti az alkalmazások és az Azure Active Directory v2.0-végpont által támogatott forgatókönyveket.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 3ac97ac5e9ae0a7059d9c74c5918212f1ce02790
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959954"
---
# <a name="application-types-in-v10"></a>Alkalmazástípusok az 1.0-s verzió

Az Active Directory (Azure AD) támogatja a hitelesítés az Azure számos különböző modern alkalmazásarchitektúrához, mindegyiket szabványos protokollok, az OAuth 2.0 vagy OpenID Connect alapján.

A következő ábra szemlélteti a forgatókönyvek és az alkalmazástípusok, és hogyan adhatók hozzá különböző összetevők:

![Alkalmazástípusok és forgatókönyvek](./media/authentication-scenarios/application_types_and_scenarios.png)

Az Azure AD által támogatott öt elsődleges alkalmazás-forgatókönyvek a következők:

- **[Egyoldalas alkalmazás (SPA)](single-page-application.md)**: egy felhasználónak szüksége van a bejelentkezni egy egyoldalas alkalmazás Azure AD által védett.
- **[Webes alkalmazás webböngészőben](web-app.md)**: egy felhasználónak jelentkezzen be az Azure AD által védett webalkalmazás van szüksége.
- **[Webes API a natív alkalmazás](native-app.md)**: egy natív alkalmazás egy telefonon, táblagépen vagy számítógépen futó kell hitelesíteni a felhasználót, hogy a webes API-k az Azure AD által védett erőforrások beolvasása.
- **[Webes API-webalkalmazás](web-api.md)**: webalkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.
- **[Webes API-hoz démon vagy a server application](service-to-service.md)**: egy démon, alkalmazás vagy a webes felhasználói felület nélkül kiszolgálói alkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.

Kövesse a hivatkozásokat tudjon meg többet a különböző típusú alkalmazás, és ismerje meg a magas szintű forgatókönyveket, a kód használatának megkezdése előtt. Emellett tudhat meg kell tudni az egy adott alkalmazást, amely az 1.0-s verziójú végpont vagy a v2.0-végpont írásakor különbségekről.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure AD-forgatókönyvek és funkciók. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).

Az alkalmazások és a leírt forgatókönyvek ide használatával a különböző nyelvekhez és platformokhoz is fejleszthet. Ezek vannak mindehhez a szilárd hátteret a kód minták útmutatóban elérhető teljes körű Kódminták: [1.0-s verziójú Kódminták forgatókönyv szerint](sample-v1-code.md) és [2.0-s verziójú Kódminták forgatókönyv szerint](sample-v2-code.md). A közvetlenül a megfelelő is letölteni a mintakódokat [minta GitHub-adattárak](https://github.com/Azure-Samples?q=active-directory).

Emellett, ha az alkalmazásnak egy adott adatrészletet, vagy a szegmens egy végpontok közötti forgatókönyv a legtöbb esetben a funkció is hozzáadhatók egymástól függetlenül. Például ha egy natív alkalmazást, amely meghívja a webes API-k, egyszerűen hozzáadhat egy webalkalmazást, a webes API meghívásához.

## <a name="app-registration"></a>Appok regisztrálása

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Az Azure AD-1.0-s verziójú végpont használó alkalmazás regisztrálása

Minden olyan alkalmazás, amely az Azure AD-hitelesítés outsources regisztrálni kell egy könyvtárban. Ennek a lépésnek része az Azure ad-ben mondanunk az alkalmazásról, beleértve az URL-címet, hogy hol található, küldje a választ az URI-t az alkalmazás és más azonosíthatja a hitelesítés után az URL-cím. Ez az információ akkor szükséges, néhány fő okok miatt:

* Azure ad-ben kell kommunikálnia a az alkalmazás bejelentkezési vagy cserélő jogkivonatok kezelésekor. Az Azure AD között továbbított információk és az alkalmazás a következőket tartalmazza:
  
  * **Alkalmazásazonosító URI-ja** -alkalmazás azonosítója. Ez az érték jelzi, hogy melyik alkalmazás a hívó szeretne egy tokent az Azure AD-hitelesítés során zajlik. Ezenkívül ezt az értéket tartalmazza a tokent, hogy az alkalmazás tudja, hogy ez volt a szándékolt cél.
  * **Válasz URL-cím** és **átirányítási URI-t** – egy webes API-t vagy webes alkalmazás, a válasz-URL az a hely, ahol az Azure AD elküldi a hitelesítési választ, beleértve egy jogkivonatot, ha a hitelesítés sikerült. Natív alkalmazás esetén az átirányítási URI az az egyedi azonosítója, amelyre az Azure AD átirányítja a felhasználói ügynököt az OAuth 2.0-kérelmek.
  * **Alkalmazásazonosító** -alkalmazáshoz, ha az alkalmazás regisztrálva van az Azure AD által generált az azonosító. Amikor kér az engedélyezési kód vagy a jogkivonatot, az alkalmazás Azonosítóját és kulcsát kapnak az Azure AD-hitelesítés során.
  * **Kulcs** -együtt küldött egy Alkalmazásazonosítót hitelesítéséhez az Azure AD a webes API-hívás a kulcsot.
* Az Azure AD meg kell győződnie arról az alkalmazás rendelkezik a szükséges engedélyekkel hozzáférhet a címtár adataihoz, más alkalmazásokat a szervezeten belül, és így tovább.

Részletekért megtudhatja, hogyan [alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont](quickstart-v1-add-azure-ad-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Egybérlős és több-bérlős alkalmazások

Kiépítés válik világosabb, miután megértette, hogy az alkalmazásokat, amelyek az alkalmazáskódok fejlesztésének és Azure AD-vel integrált két kategóriába sorolhatók:

* **Egyetlen bérlő alkalmazás** – egy egybérlős alkalmazást egy szervezeten belüli használatra szolgál. Ezek a általában az üzletági (LoB) alkalmazásokat egy vállalati fejlesztők által írt. Egy egybérlős alkalmazást csak kell a felhasználók egy címtárban lehet elérni, és ennek eredményeképpen csak kell egy címtárban ki kell építeni. Ezek az alkalmazások általában egy fejlesztő a szervezetben vannak regisztrálva.
* **Több-bérlős alkalmazás** – több-bérlős alkalmazás használatra szánt szervezetekben, nem csak egyetlen szervezet. Ezek a független szoftverszállító (ISV-k) által írt általában-as-szoftverszolgáltatások (SaaS) alkalmazások. Több-bérlős alkalmazásokhoz szükség lesz összes könyvtárban, ahol használni fogják őket, amelyhez szükség van a felhasználó vagy rendszergazda hozzájárulását, regisztrálja őket. A jóváhagyási folyamat akkor kezdődik, amikor egy alkalmazás regisztrálva van a címtárban, és a Graph API vagy akár egy másik webes API-hoz való hozzáféréssel kap. Amikor az alkalmazás regisztrál egy felhasználó vagy egy másik szervezet rendszergazdájának, jelenjenek meg ezek egy párbeszédpanel, amely megjeleníti az alkalmazáshoz szükséges engedélyeket. A felhasználó vagy rendszergazda ezután jóváhagyhatja az alkalmazáshoz, amely az alkalmazás hozzáférést biztosít a megadott adatokat, és végül regisztrálja az alkalmazást a címtárban. További információkért lásd: [a hozzájárulási keretrendszer áttekintése](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>További szempontok fejlesztése során egyetlen bérlő vagy a több-bérlős alkalmazások

Néhány további szempontot helyett egy egybérlős alkalmazást egy több-bérlős alkalmazások fejlesztése során merülnek fel. Például, ha az alkalmazás elérhető a felhasználók számára több címtár, kell meghatározni, melyik bérlőhöz egy olyan mechanizmust is a. Egy egybérlős alkalmazást kell csak egy felhasználó a saját címtárban keresse meg, amíg egy több-bérlős alkalmazást az Azure ad-ben egy adott felhasználó összes címtárakban azonosítania kell. Ennek a feladatnak, az Azure AD biztosít egy közös hitelesítési végpont, ahol minden olyan több-bérlős alkalmazás irányíthatók a bejelentkezési kérelmek, a bérlő-specifikus végpont helyett. Ez a végpont https://login.microsoftonline.com/common az Azure AD-ben minden címtár esetében, mivel lehet, hogy egy bérlő-specifikus végpont https://login.microsoftonline.com/contoso.onmicrosoft.com. A közös végpontot különösen fontos figyelembe venni, amikor az alkalmazás fejlesztése, mert a bejelentkezési, kijelentkezési, és a jogkivonat érvényesítése során több bérlő kezeléséhez szükséges logikát kell.

Ha éppen fejleszt egy egybérlős alkalmazást, de győződjön meg a rendelkezésre álló számos szervezet hasonló, egyszerűen módosításokat végezheti el az alkalmazást és annak konfigurációját az Azure ad-ben, hogy több-bérlős képes. Emellett az Azure AD használja az ugyanazon aláírókulcsot összes jogkivonat található összes, hogy meg van adva egy egybérlős vagy több-bérlős alkalmazásban hitelesítést.

A jelen dokumentumban szereplő minden egyes forgatókönyv egy alszakasz, amely leírja annak üzembe helyezési követelményeket tartalmazza. Részletesebb információ a kiépítése az alkalmazások az Azure AD és a egy vagy több-bérlős alkalmazások közötti különbségekről: [alkalmazások integrálása az Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) további információt. Az Azure ad-ben a gyakori alkalmazási helyzet megértéséhez olvassa tovább.

## <a name="next-steps"></a>További lépések

- További tudnivalók a más Azure ad-ben [hitelesítés alapjai](authentication-scenarios.md)
