---
title: Az 1.0-s részben lévő alkalmazástípusok | Azure
description: Az Azure Active Directory 2.0-s futópontja által támogatott alkalmazások és forgatókönyvek típusait ismerteti.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c290cbf36fd53d5afb5fd805cda896fb6879bb4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154950"
---
# <a name="application-types-in-v10"></a>Alkalmazástípusok az 1.0-s alkalmazásban

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az Azure Active Directory (Azure AD) számos modern alkalmazásarchitektúra hitelesítését támogatja, amelyek mindegyike az OAuth 2.0 vagy az OpenID Connect szabványnak megfelelő protokollokon alapul.

Az alábbi ábra bemutatja az eseteket és az alkalmazástípusokat, valamint a különböző összetevők hozzáadásának módját:

![Alkalmazástípusok és forgatókönyvek](./media/authentication-scenarios/application-types-scenarios.png)

Ezek az Azure AD által támogatott öt elsődleges alkalmazásforgatókönyv:

- **[Egyoldalas alkalmazás (SPA)](single-page-application.md)**: A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít.
- **[Webböngésző a webalkalmazás:](web-app.md)** A felhasználónak be kell jelentkeznie egy webalkalmazás, amely az Azure AD által védett.
- **[Natív alkalmazás webes API:](native-app.md)** Egy natív alkalmazás, amely fut a telefon, táblagép vagy pc kell hitelesíteni a felhasználót, hogy az erőforrásokat egy webes API-t, amely az Azure AD által védett.
- **[Webalkalmazás web API-hoz:](web-api.md)** Egy webalkalmazás kell erőforrásokat beszerezni egy webes API-t az Azure AD által védett.
- **[Démon vagy kiszolgálóalkalmazás web API-](service-to-service.md)**: Egy démon-alkalmazás vagy egy webes felhasználói felület nélküli kiszolgálóalkalmazás nak kell erőforrásokat beszereznie az Azure AD által védett webes API-ból.

A hivatkozásokra kattintva többet tudhat meg az egyes alkalmazástípusokról, és megismerheti a magas szintű forgatókönyveket, mielőtt elkezdené a kódot. Azt is megtudhatja, hogy milyen különbségeket kell tudnia egy adott alkalmazás írásakor, amely a v1.0-végpontdal vagy a 2.0-s végponttal működik.

> [!NOTE]
> A v2.0-s végpont nem támogatja az összes Azure AD-forgatókönyvek és funkciók. Annak megállapításához, hogy a 2.0-s végpontot kell-e használnia, olvassa el a [2.0-s korlátozásokat.](../develop/active-directory-v2-limitations.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

Az itt leírt alkalmazások és forgatókönyvek bármelyikét különböző nyelvek és platformok használatával fejlesztheti. Ezek mindegyike teljes kódmintákat tartalmaz a kódminták útmutatójában: [1.0-s kódminták forgatókönyv szerint](sample-v1-code.md) és [2.0-s kódminták forgatókönyv szerint](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). A kódmintákat közvetlenül a megfelelő [GitHub-mintatárolókból](https://github.com/Azure-Samples?q=active-directory)is letöltheti.

Emellett ha az alkalmazásnak egy adott darabra vagy szegmensre van szüksége egy végpontok közötti forgatókönyvből, a legtöbb esetben ez a funkció egymástól függetlenül is hozzáadható. Ha például egy natív alkalmazás, amely meghívja a webes API-t, könnyen hozzáadhat egy webes alkalmazást, amely a webes API-t is meghívja.

## <a name="app-registration"></a>Alkalmazásregisztráció

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Az Azure AD v1.0-s végpontot használó alkalmazás regisztrálása

Minden olyan alkalmazás, amely kiszervezi a hitelesítést az Azure AD-hez, egy címtárban kell regisztrálnia. Ez a lépés magában foglalja az Azure AD értesítését az alkalmazásról, beleértve az URL-címet, ahol található, az URL-címet a válaszok küldéséhez a hitelesítés után, az URI-t az alkalmazás azonosítására, és így tovább. Ez az információ néhány fő okból szükséges:

* Az Azure AD-nek kommunikálnia kell az alkalmazással a bejelentkezés vagy a jogkivonatok cseréje során. Az Azure AD és az alkalmazás között átadott információk a következőket tartalmazzák:
  
  * **Alkalmazásazonosító URI** – egy alkalmazás azonosítója. Ezt az értéket a hitelesítés során elküldi az Azure AD-nek annak jelzésére, hogy a hívó melyik alkalmazáshoz szeretne jogkivonatot. Emellett ez az érték szerepel a jogkivonatban, hogy az alkalmazás tudja, hogy a kívánt cél volt.
  * **Válasz URL-címe** és **átirányítása URI** – Egy webes API vagy webes alkalmazás, a válasz URL-cím az a hely, ahol az Azure AD elküldi a hitelesítési választ, beleértve a jogkivonatot, ha a hitelesítés sikeres volt. Natív alkalmazás esetén az átirányítási URI egy egyedi azonosító, amelyre az Azure AD átirányítja a felhasználói ügynököt egy OAuth 2.0-s kérelemben.
  * **Alkalmazásazonosító** – Egy alkalmazás azonosítója, amelyet az Azure AD hoz létre, ha az alkalmazás regisztrálva van. Engedélyezési kód vagy jogkivonat kérésekor az alkalmazásazonosító és a kulcs a hitelesítés során az Azure AD-nek kerül.
  * **Kulcs** – A kulcs, amely egy alkalmazásazonosítóval együtt kerül elküldésre az Azure AD-n való hitelesítéskor egy webes API-hívásához.
* Az Azure AD-nek biztosítania kell, hogy az alkalmazás rendelkezzen a címtáradatok, a szervezet más alkalmazásai és így tovább eléréséhez szükséges engedélyekkel.

A részletekért megtudhatja, hogyan [regisztrálhat egy alkalmazást.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

## <a name="single-tenant-and-multi-tenant-apps"></a>Egy- és több-bérlős alkalmazások

A kiépítés egyértelműbbé válik, ha tisztában van azzal, hogy az Azure AD-vel fejleszthető és integrálható alkalmazások két kategóriája van:

* **Egyetlen bérlői alkalmazás** – egy egyetlen bérlői alkalmazás egyetlen szervezetben való használatra szolgál. Ezek általában egy vállalati fejlesztő által írt üzletági (LoB) alkalmazások. Egyetlen bérlői alkalmazást csak egy könyvtárban lévő felhasználóknak kell elérniük, és ennek eredményeképpen csak egy könyvtárban kell kiépíteni. Ezeket az alkalmazásokat általában a szervezet egyik fejlesztője regisztrálja.
* **Több-bérlős alkalmazás** – Egy több-bérlős alkalmazás számos szervezetben, nem csak egy szervezetben használható. Ezek általában egy független szoftvergyártó (ISV) által írt szoftver-szolgáltatásként (SaaS) alkalmazások. Több-bérlős alkalmazásokat kell kiépíteni minden könyvtárban, ahol használni fogják őket, amely megköveteli a felhasználó vagy a rendszergazda beleegyezését a regisztrációhoz. Ez a jóváhagyási folyamat akkor kezdődik, ha egy alkalmazás regisztrálva van a címtárban, és hozzáférést kap a Graph API-hoz vagy esetleg egy másik webes API-hoz. Amikor egy másik szervezet felhasználója vagy rendszergazdája regisztrál az alkalmazás használatára, egy párbeszédpanel jelenik meg, amely megjeleníti az alkalmazás által igényelt engedélyeket. A felhasználó vagy a rendszergazda ezután beleegyezhet az alkalmazásba, amely hozzáférést biztosít az alkalmazásnak a megadott adatokhoz, és végül regisztrálja az alkalmazást a címtárban. További információt [a Hozzájárulási keretrendszer áttekintése című témakörben talál.](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>További szempontok az egy- vagy többbérlős alkalmazások fejlesztése során

Néhány további szempontok merülnek fel, amikor egy több-bérlős alkalmazás fejlesztése helyett egyetlen bérlői alkalmazás. Ha például az alkalmazást több könyvtárban teszi elérhetővé a felhasználók számára, szüksége van egy mechanizmusra annak meghatározásához, hogy melyik bérlőben vannak. Egy egyetlen bérlői alkalmazás csak meg kell nézni a saját könyvtárban a felhasználó, míg a több-bérlős alkalmazás kell azonosítania egy adott felhasználó az Azure AD könyvtárakból. A feladat végrehajtásához az Azure AD egy közös hitelesítési végpontot biztosít, ahol bármely több-bérlős alkalmazás közvetlen bejelentkezési kérelmeket, ahelyett, hogy egy bérlő-specifikus végpont. Ez a `https://login.microsoftonline.com/common` végpont az Azure AD összes könyvtárára vonatkozik, míg `https://login.microsoftonline.com/contoso.onmicrosoft.com`egy bérlő-specifikus végpont lehet. A közös végpont különösen fontos, hogy fontolja meg az alkalmazás fejlesztése során, mert szükség lesz a szükséges logika kezelésére több bérlő bejelentkezés, kijelentkezés és jogkivonat érvényesítése során.

Ha jelenleg egyetlen bérlői alkalmazást fejleszt, de azt számos szervezet számára elérhetővé szeretné tenni, egyszerűen módosíthatja az alkalmazást és annak konfigurációját az Azure AD-ben, hogy több-bérlős képes legyen. Emellett az Azure AD ugyanazt az aláíró kulcsot használja az összes könyvtár összes jogkivonatjához, függetlenül attól, hogy egyetlen bérlőben vagy több-bérlős alkalmazásban biztosít-e hitelesítést.

A dokumentumban felsorolt minden egyes forgatókönyv tartalmaz egy alszakaszt, amely leírja a kiépítési követelményeket. Az azure-beli AD-ben való alkalmazáskiépítéséről, valamint az egy- és több-bérlős alkalmazások közötti különbségekről további információt az [Alkalmazások integrálása](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) az Azure Active Directoryval című témakörben talál. Folytatódik olvasó, hogy megismerjék a közös alkalmazás-forgatókönyvek az Azure AD-ben.

## <a name="next-steps"></a>További lépések

- További információ az Azure AD-hitelesítés egyéb [alapjairól](v1-authentication-scenarios.md)
