---
title: Natív ügyfélalkalmazások közzététele – Azure AD | Microsoft dokumentumok
description: Bemutatja, hogyan engedélyezheti a natív ügyfélalkalmazások számára az Azure AD Alkalmazásproxy-összekötővel való kommunikációt, hogy biztonságos távoli hozzáférést biztosítson a helyszíni alkalmazásokhoz.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159284"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>A natív ügyfélalkalmazások proxyalkalmazásokkal való interakciójának engedélyezése

Az Azure Active Directory (Azure AD) alkalmazásproxy használatával közzéteheti a webalkalmazásokat, de az Azure AD-hitelesítési kódtárral (ADAL) konfigurált natív ügyfélalkalmazások közzétételére is használható. A natív ügyfélalkalmazások különböznek a webalkalmazásoktól, mivel egy eszközre vannak telepítve, míg a webalkalmazások böngészőn keresztül érhetők el.

Natív ügyfélalkalmazások támogatása érdekében az alkalmazásproxy elfogadja az Azure AD által kiadott jogkivonatokat, amelyek a fejlécben vannak elküldve. Az alkalmazásproxy szolgáltatás végzi el a felhasználók hitelesítését. Ez a megoldás nem használ alkalmazásjogkivonatokat a hitelesítéshez.

![A végfelhasználók, az Azure AD és a közzétett alkalmazások közötti kapcsolat](./media/application-proxy-configure-native-client-application/richclientflow.png)

Natív alkalmazások közzétételéhez használja az Azure AD hitelesítési könyvtár, amely gondoskodik a hitelesítésés számos ügyfélkörnyezetben támogatja. Az alkalmazásproxy beleillik a [natív alkalmazás-web API-ba.](../azuread-dev/native-app.md)

Ez a cikk bemutatja a négy lépést egy natív alkalmazás közzététele alkalmazásproxyval és az Azure AD hitelesítési könyvtár.

## <a name="step-1-publish-your-proxy-application"></a>1. lépés: A proxyalkalmazás közzététele

Tegye közzé a proxyalkalmazást úgy, mint bármely más alkalmazást, és rendelje hozzá a felhasználókat az alkalmazás eléréséhez. További információt az [Alkalmazások közzététele alkalmazásproxyval című témakörben talál.](application-proxy-add-on-premises-application.md)

## <a name="step-2-register-your-native-application"></a>2. lépés: Regisztrálja natív alkalmazását

Most regisztrálnia kell az alkalmazást az Azure AD-ben, az alábbiak szerint:

1. Jelentkezzen be az [Azure Active Directory portálra.](https://aad.portal.azure.com/) Megjelenik az **Azure Active Directory felügyeleti központ** **irányítópultja.**
1. Az oldalsávon válassza az **Azure Active Directory**lehetőséget. Megjelenik **az Azure Active Directory** áttekintő lapja.
1. Az Azure AD áttekintése oldalsávon válassza **az Alkalmazásregisztrációk**lehetőséget. Megjelenik az összes alkalmazásregisztráció listája.
1. Válassza **az Új regisztráció lehetőséget.** Megjelenik **a Jelentkezés regisztrálása** lap.

   ![Új alkalmazásregisztráció létrehozása az Azure Portalon](./media/application-proxy-configure-native-client-application/create.png)

1. A **Név** fejlécben adja meg az alkalmazás felhasználó felé néző megjelenítendő nevét.
1. A **Támogatott fióktípusok** fejlécalatt válasszon egy hozzáférési szintet az alábbi irányelvek szerint:

   - Ha csak a szervezeten belüli fiókokat szeretné megcélozni, válassza **a Fiókok lehetőséget ebben a szervezeti címtárban.**
   - Ha csak üzleti vagy oktatási ügyfeleket szeretne megcélozni, válassza **a Fiókok lehetőséget bármely szervezeti címtárban.**
   - A Microsoft-identitások legszélesebb körének megcélzásához válassza **a Fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban.**

1. Az **Átirányítás** URI-fejlécben válassza a **Nyilvános ügyfél (mobil & asztali)** lehetőséget, majd írja be az alkalmazás átirányítási URI-ját.
1. Jelölje ki és olvassa el a **Microsoft Platform szabályzatait,** majd válassza a **Regisztráció**lehetőséget. Létrejön és megjelenik az új alkalmazásregisztráció áttekintő oldala.

Az új alkalmazásregisztráció létrehozásáról az [Alkalmazások integrálása](../develop/quickstart-register-app.md)az Azure Active Directoryval című témakörben talál további információt.

## <a name="step-3-grant-access-to-your-proxy-application"></a>3. lépés: Hozzáférés megadása a proxy alkalmazáshoz

Most, hogy regisztrálta a natív alkalmazást, hozzáférést adhat a címtárban lévő más alkalmazásokhoz, ebben az esetben a proxyalkalmazás eléréséhez. A natív alkalmazás proxyalkalmazással való kapcsolatának engedélyezése:

1. Az új alkalmazásregisztrációs lap oldalsávján válassza az **API-engedélyek lehetőséget.** Megjelenik az új alkalmazásregisztráció **API-engedélyeinek** lapja.
1. Válassza **az Engedély hozzáadása**lehetőséget. Megjelenik **az API-engedélyek kérése** lap.
1. Az **API kiválasztása** beállítás csoportban válassza a **szervezet által használt API-k lehetőséget.** Megjelenik egy lista, amely az API-kat elérhető alkalmazásokat tartalmazza a címtárban.
1. Írja be a keresőmezőbe, vagy görgessen, hogy megtalálja a proxy alkalmazás, amely megjelent [az 1.](#step-1-publish-your-proxy-application)
1. A **Milyen típusú engedélyeket igényel az alkalmazás?** Ha a natív alkalmazásnak a proxyalkalmazás API-ját kell elérnie bejelentkezett felhasználóként, válassza a **Delegált engedélyek lehetőséget.**
1. Az **Engedélyek kiválasztása** fejlécben jelölje ki a kívánt engedélyt, és válassza az **Engedélyek hozzáadása**lehetőséget. A natív alkalmazás **API-engedélyek** lapja most már a hozzáadott proxyalkalmazást és engedélyAPI-t jeleníti meg.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>4. lépés: Az Active Directory hitelesítési tár szerkesztése

Szerkessze a natív alkalmazáskódot az Active Directory hitelesítési könyvtár (ADAL) hitelesítési környezetében a következő szöveggel:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

A mintakódban szükséges adatok az Azure AD portálon találhatók, az alábbiak szerint:

| Szükséges információ | Hogyan található meg az Azure AD portálon |
| --- | --- |
| \<Bérlői azonosító> | **Az Azure Active Directory** > **tulajdonságainak címtárának** > **azonosítója** |
| \<A Proxy alkalmazás külső url-címe> | **Vállalati alkalmazások** > *a proxyalkalmazás* > **alkalmazásproxykülső** > **url-címe** |
| \<A natív alkalmazás> alkalmazásazonosítója | **Vállalati alkalmazások** > *a natív alkalmazás* > tulajdonságai**alkalmazásazonosítója** **Properties** >  |
| \<A natív alkalmazás> ÁTirányítása | **Az Azure Active Directory** > **alkalmazás regisztrációk** > *a natív alkalmazás* > **átirányítási URI-k** |
| \<Proxy alkalmazás API-> | **Az Azure Active Directory** > **Alkalmazás regisztrációk** > *a natív alkalmazás* > **API-engedélyek** > API / ENGEDÉLYEK**NAME** |

Miután ezekkel a paraméterekkel szerkesztette az ADAL-t, a felhasználók akkor is hitelesíthetik magukat a natív ügyfélalkalmazások számára, ha azok a vállalati hálózaton kívül vannak.

## <a name="next-steps"></a>További lépések

A natív alkalmazásfolyamatról további információt az [Azure Active Directory natív alkalmazásai című témakörben talál.](../azuread-dev/native-app.md)

Megtudhatja, hogy miként lehet egyszeri bejelentkezést létrehozni [az alkalmazásokra az Azure Active Directoryban.](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
