---
title: Natív ügyfélalkalmazások közzététele – Azure AD | Microsoft Docs
description: Ismerteti, hogyan engedélyezhető a natív ügyfélalkalmazások az Azure AD Application Proxy-összekötővel való kommunikációhoz a helyszíni alkalmazások biztonságos távoli elérésének biztosításához.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77159284"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz

A webalkalmazások közzétételéhez Azure Active Directory (Azure AD) alkalmazásproxy használható, de az Azure AD Authentication Library (ADAL) szolgáltatással konfigurált natív ügyfélalkalmazások közzétételére is használható. A natív ügyfélalkalmazások eltérnek a webalkalmazások, mert azok egy eszközre vannak telepítve, míg a Web Apps böngészőn keresztül érhető el.

A natív ügyfélalkalmazások támogatásához az alkalmazásproxy fogadja az Azure AD által kiadott, a fejlécben eljuttatott jogkivonatokat. Az alkalmazásproxy szolgáltatás a felhasználók hitelesítését végzi. Ez a megoldás nem használ alkalmazás-jogkivonatokat a hitelesítéshez.

![A végfelhasználók, az Azure AD és a közzétett alkalmazások közötti kapcsolat](./media/application-proxy-configure-native-client-application/richclientflow.png)

Natív alkalmazások közzétételéhez használja az Azure AD hitelesítési függvénytárát, amely gondoskodik a hitelesítésről, és számos ügyfél-környezetet támogat. Az alkalmazásproxy illeszkedik a [natív alkalmazáshoz a webes API-forgatókönyvhöz](../azuread-dev/native-app.md).

Ez a cikk végigvezeti a natív alkalmazások alkalmazásproxy és az Azure AD Authentication Library használatával történő közzétételének négy lépésén.

## <a name="step-1-publish-your-proxy-application"></a>1. lépés: a proxy alkalmazás közzététele

Tegye közzé a proxykiszolgálót úgy, mint bármely más alkalmazást, és rendelje hozzá a felhasználókat az alkalmazáshoz való hozzáféréshez. További információ: [alkalmazások közzététele az alkalmazásproxy](application-proxy-add-on-premises-application.md)-ben.

## <a name="step-2-register-your-native-application"></a>2. lépés: a natív alkalmazás regisztrálása

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

Az új alkalmazás-regisztráció létrehozásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>3. lépés: hozzáférés biztosítása a proxy alkalmazáshoz

Most, hogy regisztrálta a natív alkalmazását, hozzáférést biztosíthat más alkalmazásokhoz a címtárban, ebben az esetben a alkalmazásproxy eléréséhez. Annak engedélyezése, hogy a natív alkalmazás elérhető legyen a proxy alkalmazás számára:

1. Az új alkalmazás regisztrálása lap oldalsávján válassza az **API-engedélyek**lehetőséget. Megjelenik az új alkalmazás-regisztráció **API-engedélyek** lapja.
1. Válassza **az engedély hozzáadása**lehetőséget. Megjelenik a **kérelem API-engedélyek** lap.
1. Az **API kiválasztása** beállításnál válassza a **szervezetem által használt API**-k elemet. Megjelenik egy lista, amely tartalmazza az API-kat közzétevő címtárban lévő alkalmazásokat.
1. Írja be a keresőmezőbe, vagy görgessen az [1. lépés: a proxy alkalmazás közzététele](#step-1-publish-your-proxy-application), majd a proxykiszolgáló kiválasztásához közzétett alkalmazásproxy megkereséséhez.
1. Az **alkalmazás milyen típusú engedélyeket igényel?** fejlécben válassza ki az engedély típusát. Ha a natív alkalmazásnak bejelentkezett felhasználóként kell hozzáférnie a proxy Application API-hoz, válassza a **delegált engedélyek**lehetőséget.
1. Az **engedélyek kiválasztása** fejlécben válassza ki a kívánt engedélyt, majd válassza az **engedélyek hozzáadása**lehetőséget. A natív alkalmazás **API-engedélyeinek** lapja mostantól megjeleníti a hozzáadott alkalmazásproxy-alkalmazást és az engedélyek API-ját.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>4. lépés: a Active Directory-hitelesítési tár szerkesztése

Szerkessze a natív alkalmazás kódját a Active Directory-hitelesítési tár (ADAL) hitelesítési környezetében, hogy tartalmazza a következő szöveget:

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
| \<Bérlői azonosító> | **Azure Active Directory** > **Properties**tulajdonságok > **könyvtárának azonosítója** |
| \<A proxy alkalmazás külső URL-címe> | **Vállalati alkalmazások** > *a* > **Application proxy**alkalmazásproxy > **-alkalmazásproxy külső URL-címe** |
| \<A natív alkalmazás alkalmazás-azonosítója> | **Vállalati alkalmazások** > *a natív alkalmazás* > **tulajdonságainak** > **alkalmazás-azonosítója** |
| \<A natív alkalmazás átirányítási URI-ja> | **Azure Active Directory** > **App registrations**Alkalmazásregisztrációk > *a natív alkalmazás* > -**átirányítási URI** -k |
| \<Proxy app API URL-cím> | **Azure Active Directory** > **App registrations**Alkalmazásregisztrációk > *a natív alkalmazás* > **API engedélyek** > **API/engedélyek neve** |

Miután módosította a ADAL ezekkel a paraméterekkel, a felhasználók akkor is hitelesíthetők a natív ügyfélalkalmazások számára, ha a vállalati hálózaton kívül esnek.

## <a name="next-steps"></a>További lépések

További információ a natív alkalmazás folyamatáról: [natív alkalmazások Azure Active Directoryban](../azuread-dev/native-app.md).

Ismerje meg, hogyan állíthat be [egyszeri bejelentkezést az alkalmazásokba Azure Active Directory-ben](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
