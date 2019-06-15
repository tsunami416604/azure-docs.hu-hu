---
title: Natív ügyfélalkalmazások – Azure ad-ben közzététele |} A Microsoft Docs
description: Bemutatja, hogyan kommunikáljon az Azure AD alkalmazásproxy-összekötő a helyszíni alkalmazások biztonságos távoli elérést biztosíthat a natív ügyfélalkalmazások engedélyezéséhez.
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
ms.openlocfilehash: cb36d6a03da07681db468184a489a79f7f0deab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825487"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Együttműködhet a proxy-alkalmazások natív ügyfélalkalmazások engedélyezése

Azure Active Directory (Azure AD) alkalmazásproxy segítségével webalkalmazásokat tesz közzé, de azt is használható együtt az Azure AD Authentication Library (ADAL) konfigurált natív ügyfél alkalmazásokat tehet közzé. Natív ügyfélalkalmazások eltérnek a web apps, mert vannak telepítve az eszközön, amíg a böngészőn keresztül elért webalkalmazások.

Támogatja a natív ügyfélalkalmazások számára, hogy alkalmazásproxy fogadja el az Azure AD által kiállított jogkivonatokat, amelyeket a fejlécet küld. Az alkalmazásproxy-szolgáltatás végzi a hitelesítést a felhasználók számára. Ez a megoldás nem használ alkalmazás jogkivonatokat a hitelesítéshez.

![A végfelhasználók, az Azure Active Directory és a közzétett alkalmazások közötti kapcsolat](./media/application-proxy-configure-native-client-application/richclientflow.png)

Natív alkalmazásokat közzétenni, használja az Azure AD Authentication Library, amely gondoskodik a hitelesítési és sok ügyfél környezet támogatja. Az alkalmazásproxy hogyan illik bele a [natív alkalmazás webes API-forgatókönyv](../develop/native-app.md).

Ez a cikk végigvezeti az Application Proxy és az Azure AD Authentication Library natív alkalmazás közzététele a négy lépést.

## <a name="step-1-publish-your-proxy-application"></a>1\. lépés: A proxy-alkalmazások közzététele

A proxy-alkalmazások közzététele, mint bármely más alkalmazás, és rendelje hozzá a felhasználók elérhetik az alkalmazást. További információkért lásd: [alkalmazásait közzéteheti az alkalmazásproxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>2\. lépés: Natív alkalmazás regisztrálása

Most már regisztrálnia kell az alkalmazás az Azure AD-ben a következő:

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/). A **irányítópult** számára a **Azure Active Directory felügyeleti központ** jelenik meg.
2. Válassza ki az oldalsávon **Azure Active Directory**. A **Azure Active Directory** áttekintőlapján jelenik meg.
3. Az Azure ad-ben – áttekintés oldalsávon válassza **alkalmazásregisztrációk**. Minden alkalmazásregisztráció listája jelenik meg.
4. Válassza ki **új regisztrációs**. A **alkalmazás regisztrálása** lap jelenik meg.

   ![Hozzon létre egy új alkalmazás regisztrálása](./media/application-proxy-configure-native-client-application/create.png)
5. Az a **neve** fejléc adja meg az alkalmazás felhasználói megjelenített neve.
6. Alatt a **támogatott fióktípusok** szakaszban kattintson egy hozzáférési szintet használja ezeket az irányelveket:
   - Csak azok a fiókok, amelyek a szervezeten belüli célozza meg, válassza ki a **fiókok csak a szervezeti könyvtárban található**.
   - Csak az üzleti vagy oktatási ügyfelek megcélzása, válassza ki a **bármely szervezeti directory fiókok**.
   - Célozza meg a Microsoft identitások legszélesebb készletét, válassza ki a **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
7. Az a **átirányítási URI-t** szakaszban kattintson **(mobil és asztali) nyilvános ügyfél**, majd írja be az alkalmazás átirányítási URI-t.
8. Válassza ki, és olvassa el a **Microsoft Platform házirendek**, majd válassza ki **regisztrálása**. Az új alkalmazás regisztrálása áttekintő oldala, és a jelenik meg.

További részletes információ egy új alkalmazás regisztrálása létrehozásával kapcsolatban lásd: [alkalmazások integrálása az Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>3\. lépés: Az alkalmazásproxy-alkalmazáshoz való hozzáférés biztosítása

Most, hogy a natív alkalmazás regisztrálása, hozzáférést biztosíthat más alkalmazások számára a címtárban, ebben az esetben az alkalmazásproxy-alkalmazás elérésére. A proxy-alkalmazások ki vannak téve a natív alkalmazás engedélyezése:

1. Az új alkalmazás regisztrálása lap oldalsávon válassza **API-engedélyek**. A **API-engedélyek** oldala jelenik meg, az új alkalmazás regisztrálása.
2. Válassza ki **adjon hozzá egy engedélyt**. A **kérelem API-engedélyek** lap jelenik meg.
3. Alatt a **API kiválasztása** beállításnál válassza **API-k saját szervezete**. Megjelenik egy lista, amely tartalmazza a teszi közzé az API-k az alkalmazások a címtárban.
4. Írja be a keresőmezőbe vagy görgessen található a proxy a közzétett alkalmazást [1. lépés: A proxy-alkalmazások közzététele](#step-1-publish-your-proxy-application), majd válassza ki az alkalmazásproxy-alkalmazáshoz.
5. Az a **milyen engedélyeket igényel az alkalmazás?** szakaszban kattintson a engedély típusa. Ha natív alkalmazása a bejelentkezett felhasználó API application proxy hozzáférésre van szüksége, válassza a **delegált engedélyek**. Ha a natív alkalmazás fut a háttérben szolgáltatásként vagy démon nincs bejelentkezve felhasználó, válassza a **Alkalmazásengedélyek**.
6. Az a **engedélyek kiválasztása** fejléc, válassza ki a kívánt engedéllyel, és válassza ki **engedélyek hozzáadása**. A **API-engedélyek** mostantól a natív alkalmazás jeleníti meg a proxy, alkalmazás- és engedély API, hozzáadott oldala.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>4\. lépés: Az Active Directory Authentication Library szerkesztése

A hitelesítési környezetet, az Active Directory Authentication Library (ADAL) az alábbi szöveget a natív alkalmazás kód szerkesztése:

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

A mintakód a szükséges adatokat a következő található az Azure AD-portálon:

| Adatokra van szükség | Hogyan találhatja meg azt az Azure AD portálon |
| --- | --- |
| \<Bérlőazonosító > | **Az Azure Active Directory** > **tulajdonságok** > **címtár-azonosító** |
| \<Proxy alkalmazás külső URL-cím > | **Vállalati alkalmazások** > *proxy alkalmazását* > **alkalmazásproxy** > **külső URL-cím** |
| \<A natív alkalmazás az App-ID > | **Vállalati alkalmazások** > *a natív alkalmazás* > **tulajdonságok** > **alkalmazás azonosítója** |
| \<Átirányítási URI-t a natív alkalmazás > | **Az Azure Active Directory** > **alkalmazásregisztrációk** > *a natív alkalmazás* > **átirányítási URI azonosítója** |
| \<Proxy App API URL-címe > | **Az Azure Active Directory** > **alkalmazásregisztrációk** > *a natív alkalmazás* > **API-engedélyek**  >  **API / engedélyek neve** |

Után szerkesztheti az adal-t ezekkel a paraméterekkel, még ha a vállalati hálózaton kívül a felhasználók hitelesíthetők natív ügyfélalkalmazások számára.

## <a name="next-steps"></a>További lépések

A natív alkalmazás folyamat kapcsolatos további információkért lásd: [natív alkalmazások az Azure Active Directoryban](../develop/native-app.md).

Ismerje meg beállítása [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
