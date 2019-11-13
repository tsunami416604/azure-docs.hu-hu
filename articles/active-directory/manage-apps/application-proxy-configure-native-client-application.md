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
ms.openlocfilehash: 2cbee6bfcca3ddb356abe9dceab2fca07c152b07
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961808"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz

A webalkalmazások közzétételéhez Azure Active Directory (Azure AD) alkalmazásproxy használható, de az Azure AD Authentication Library (ADAL) szolgáltatással konfigurált natív ügyfélalkalmazások közzétételére is használható. A natív ügyfélalkalmazások eltérnek a webalkalmazások, mert azok egy eszközre vannak telepítve, míg a Web Apps böngészőn keresztül érhető el.

A natív ügyfélalkalmazások támogatásához az alkalmazásproxy fogadja az Azure AD által kiadott, a fejlécben eljuttatott jogkivonatokat. Az alkalmazásproxy szolgáltatás a felhasználók hitelesítését végzi. Ez a megoldás nem használ alkalmazás-jogkivonatokat a hitelesítéshez.

![A végfelhasználók, az Azure AD és a közzétett alkalmazások közötti kapcsolat](./media/application-proxy-configure-native-client-application/richclientflow.png)

Natív alkalmazások közzétételéhez használja az Azure AD hitelesítési függvénytárát, amely gondoskodik a hitelesítésről, és számos ügyfél-környezetet támogat. Az alkalmazásproxy hogyan illik bele a [natív alkalmazás webes API-forgatókönyv](../develop/native-app.md).

Ez a cikk végigvezeti az Application Proxy és az Azure AD Authentication Library natív alkalmazás közzététele a négy lépést.

## <a name="step-1-publish-your-proxy-application"></a>1\. lépés: a proxy alkalmazás közzététele

A proxy-alkalmazások közzététele, mint bármely más alkalmazás, és rendelje hozzá a felhasználók elérhetik az alkalmazást. További információkért lásd: [alkalmazásait közzéteheti az alkalmazásproxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>2\. lépés: a natív alkalmazás regisztrálása

Most regisztrálnia kell az alkalmazást az Azure AD-ben az alábbiak szerint:

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/). Megjelenik a **Azure Active Directory felügyeleti központ** **irányítópultja** .
1. Az oldalsávon válassza a **Azure Active Directory**lehetőséget. Megjelenik a **Azure Active Directory** áttekintés oldal.
1. Az Azure AD áttekintő oldalsávján válassza a **Alkalmazásregisztrációk**lehetőséget. Megjelenik az összes alkalmazás-regisztráció listája.
1. Válassza az **új regisztráció**lehetőséget. Megjelenik az **alkalmazás regisztrálása** lap.

   ![Új alkalmazás regisztrációjának létrehozása a Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. A **név** fejlécben adja meg az alkalmazás felhasználó által megjelenített nevét.
1. A **támogatott fióktípus** fejléc alatt válassza ki a hozzáférési szintet a következő irányelvek használatával:

   - Ha csak a szervezeten belüli fiókokat kívánja megcélozni, **csak a szervezeti címtárban**válassza a fiókok lehetőséget.
   - Ha csak üzleti vagy oktatási ügyfeleket szeretne megcélozni, válassza a **fiókok lehetőséget bármely szervezeti címtárban**.
   - A Microsoft-identitások legszélesebb körének megcélzásához válassza a fiókok lehetőséget a **szervezeti címtárban és a személyes Microsoft-fiókokban**.

1. Az **átirányítási URI** fejlécben válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget, majd írja be az alkalmazás átirányítási URI-ját.
1. Válassza ki és olvassa el a **Microsoft-Platform házirendjeit**, majd kattintson a **regisztráció**elemre. Létrejön és megjelenik az új alkalmazás regisztrálásának áttekintő lapja.

Az új alkalmazás-regisztráció létrehozásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>3\. lépés: hozzáférés biztosítása a proxy alkalmazáshoz

Most, hogy regisztrálta a natív alkalmazását, hozzáférést biztosíthat más alkalmazásokhoz a címtárban, ebben az esetben a alkalmazásproxy eléréséhez. Annak engedélyezése, hogy a natív alkalmazás elérhető legyen a proxy alkalmazás számára:

1. Az új alkalmazás regisztrálása lap oldalsávján válassza az **API-engedélyek**lehetőséget. Megjelenik az új alkalmazás-regisztráció **API-engedélyek** lapja.
1. Válassza ki **adjon hozzá egy engedélyt**. Megjelenik a **kérelem API-engedélyek** lap.
1. Az **API kiválasztása** beállításnál válassza a **szervezetem által használt API**-k elemet. Megjelenik egy lista, amely tartalmazza az API-kat közzétevő címtárban lévő alkalmazásokat.
1. Írja be a keresőmezőbe, vagy görgessen az [1. lépés: a proxy alkalmazás közzététele](#step-1-publish-your-proxy-application), majd a proxykiszolgáló kiválasztásához közzétett alkalmazásproxy megkereséséhez.
1. Az **alkalmazás milyen típusú engedélyeket igényel?** fejlécben válassza ki az engedély típusát. Ha a natív alkalmazásnak bejelentkezett felhasználóként kell hozzáférnie a proxy Application API-hoz, válassza a **delegált engedélyek**lehetőséget.
1. Az **engedélyek kiválasztása** fejlécben válassza ki a kívánt engedélyt, majd válassza az **engedélyek hozzáadása**lehetőséget. A natív alkalmazás **API-engedélyeinek** lapja mostantól megjeleníti a hozzáadott alkalmazásproxy-alkalmazást és az engedélyek API-ját.

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

A mintakód szükséges adatai az Azure AD portálon találhatók, a következőképpen:

| Szükséges információk | Az Azure AD-portálon való keresés |
| --- | --- |
| \<bérlő azonosítója > | **Azure Active Directory** > **Tulajdonságok** > **könyvtár azonosítója** |
| \<a proxy alkalmazás külső URL-címe > | **Vállalati alkalmazások** > *a alkalmazásproxy* > **alkalmazásproxy** > **külső URL-cím** |
| \<a natív alkalmazás alkalmazás-AZONOSÍTÓját > | **Vállalati alkalmazások** > *a natív alkalmazás* > **tulajdonságai** > **alkalmazás azonosítója** |
| \<a natív alkalmazás átirányítási URI-ja > | **Azure Active Directory** > **Alkalmazásregisztrációk** > *natív alkalmazása* > **átirányítási URI** -k |
| \<proxy app API URL-címe > | **Azure Active Directory** > **Alkalmazásregisztrációk** > *natív alkalmazása* > **API-engedélyek** > **API/engedélyek neve** |

Miután módosította a ADAL ezekkel a paraméterekkel, a felhasználók akkor is hitelesíthetők a natív ügyfélalkalmazások számára, ha a vállalati hálózaton kívül esnek.

## <a name="next-steps"></a>Következő lépések

További információ a natív alkalmazás folyamatáról: [natív alkalmazások Azure Active Directoryban](../develop/native-app.md).

Ismerje meg, hogyan állíthat be [egyszeri bejelentkezést az alkalmazásokba Azure Active Directory-ben](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
