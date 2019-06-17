---
title: Hozzáférés helyszíni API-k az Azure AD-alkalmazásproxyval
description: Az Azure Active Directory alkalmazásproxy lehetővé teszi, hogy a natív alkalmazások biztonságosan férjenek hozzá az API-k és az üzleti logikát üzemelteti a helyszíni vagy felhőbeli VM-eken.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c2b99525e3d0a61c02dc502fcd0927ea65993e5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108616"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Biztonságos hozzáférés a helyszíni API-k az Azure AD-alkalmazásproxy

Előfordulhat, hogy a helyszínen futó API-k üzleti logikát, vagy virtuális gépeken a felhőben üzemeltetett. A natív Android, iOS, Mac vagy Windows-alkalmazások használatával kommunikálhat az adatok használatával, vagy adja meg a felhasználói beavatkozás során a API-végpontokat kell. Az Azure AD-alkalmazásproxy és a [Azure Active Directory Authentication Libraries (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) tegye lehetővé a natív alkalmazások biztonságosan hozzáférhessenek a helyszíni API-k. Az Azure Active Directory Application Proxy a gyorsabb és biztonságosabb megoldás, mint a tűzfal portjainak megnyitása, és a hitelesítés és engedélyezés az alkalmazás rétegben ellenőrzés alatt tartásával. 

Ez a cikk végigvezeti egy Azure AD-alkalmazásproxy megoldás üzemeltetéséhez egy webes API-szolgáltatás, amely natív alkalmazások hozzáférhetnek-beállítása. 

## <a name="overview"></a>Áttekintés

Az alábbi ábrán egy hagyományos módon helyszíni API-k közzétételét. Ezt a megközelítést igényel, 80-as és 443-as porton bejövő portok megnyitása.

![Hagyományos API-hozzáférés](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Az alábbi ábrán látható, hogyan használhatja az Azure AD-alkalmazásproxy az API-k biztonságos közzétételét, minden bejövő portok megnyitása nélkül:

![Az Azure AD Application Proxy API-hozzáférés](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Az Azure AD-alkalmazásproxy forms gerincét a megoldást, és a egy nyilvános végpontot API-hozzáférés megfelelően működik, valamint a hitelesítési és engedélyezési magas. A keresztül elérhető API-k platformok hatalmas választékának használatával a [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) kódtárakat. 

Mivel az Azure AD-alkalmazásproxy-hitelesítés és engedélyezés az Azure AD épülnek, használhatja az Azure AD feltételes hozzáférés annak érdekében, hogy csak megbízható eszközök érhessék el a alkalmazásproxyn keresztül közzétett API-k. Az Azure AD Join vagy Azure AD hibrid csatlakoztatott asztali számítógépeken, és az Intune által felügyelt eszközök esetében. Azure Active Directory Premium-szolgáltatások, mint az Azure multi-factor Authentication szolgáltatás előnyeit, és a machine learning-alapú biztonságát is számos [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató követéséhez lesz szüksége:

- Rendszergazdai hozzáférés egy Azure-címtárhoz, egy olyan fiókkal, amely létrehozása és alkalmazások regisztrálása
- A mintául szolgáló webes API-t és a natív ügyfélalkalmazások [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Az API-t a proxyn keresztül történő alkalmazás közzététele

Az intranet proxyn keresztül történő alkalmazás-en kívül API közzététele, kövesse ugyanazt a mintát, mint a webes alkalmazások közzététele. További információkért lásd: [oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure Active Directoryban](application-proxy-add-on-premises-application.md).

A proxyn keresztül történő alkalmazás SecretAPI webes API közzététele:

1. Hozhat létre, és a egy ASP.NET-webalkalmazást, a helyi számítógépen vagy intranetes a mintaprojekt SecretAPI közzététele. Ellenőrizze, hogy elérheti a webalkalmazást helyileg. 
   
1. Az a [az Azure portal](https://portal.azure.com), jelölje be **Azure Active Directory** a bal oldali navigációs menüben. Ezután a a **áttekintése** lapon jelölje be **vállalati alkalmazások**.
   
1. Felső részén a **nagyvállalati alkalmazások – minden alkalmazás** lapon jelölje be **új alkalmazás**.
   
1. Az a **alkalmazás hozzáadása** lap **saját alkalmazás hozzáadása**válassza **helyszíni alkalmazás**. 
   
1. Ha nem rendelkezik egy alkalmazásproxy-összekötő telepítve van, kérni fogja a telepítéshez. Válassza ki **Application Proxy Connector letöltése** töltse le és telepítse az összekötőt. 
   
1. Miután telepítette az alkalmazásproxy-összekötő, az a **saját helyszíni alkalmazás hozzáadása** oldalon:
   
   1. Adja meg *SecretAPI* melletti **neve**.
      
   1. Adja meg az URL-cím, az API-t az intraneten belüli melletti eléréséhez használt **belső URL-cím**. 
      
   1. Győződjön meg arról, hogy **előhitelesítés** értékre van állítva **Azure Active Directory**. 
      
   1. Válassza ki **Hozzáadás** a lapot, és várja meg a létrehozandó alkalmazás tetején.
   
   ![API-alkalmazás hozzáadása](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Az a **nagyvállalati alkalmazások – minden alkalmazás** lapon válassza ki a **SecretAPI** alkalmazást. 
   
1. A a **SecretAPI – áttekintés** lapon jelölje be **tulajdonságok** a bal oldali navigációs menüben.
   
1. Nem szeretné, hogy a végfelhasználók számára elérhető API-k a **MyApps** panelen, ezért **a felhasználók számára látható** való **nem** alján a **tulajdonságok**oldalra, és kattintson **mentése**.
   
   ![Nem látják a felhasználók](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
A webes API Azure AD-alkalmazásproxyn keresztül közzétett. Ezután adja hozzá az alkalmazáshoz hozzáférő felhasználók. 

1. Az a **SecretAPI – áttekintés** lapon jelölje be **felhasználók és csoportok** a bal oldali navigációs menüben.
   
1. Az a **felhasználók és csoportok** lapon jelölje be **felhasználó hozzáadása**.  
   
1. Az a **-hozzárendelés hozzáadása** lapon jelölje be **felhasználók és csoportok**. 
   
1. Az a **felhasználók és csoportok** lapon keresse meg és válassza a felhasználók, akik hozzáférhetne az alkalmazáshoz, beleértve a legalább saját magának. Miután kijelölte az összes felhasználó, jelölje ki **kiválasztása**. 
   
   ![Válassza ki, és rendelje hozzá felhasználót](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Lépjen vissza a **hozzárendelés hozzáadása** lapon jelölje be **hozzárendelése**. 

> [!NOTE]
> Integrált Windows-hitelesítést használó API-k szükség lehet [további lépéseket](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>A natív alkalmazás regisztrálása és hozzáférést biztosít az API-hoz

A natív alkalmazások olyan platformon vagy eszközön használatára kifejlesztett programok. Mielőtt a natív alkalmazás képes csatlakozni és API-k elérésére, regisztrálnia kell azt az Azure ad-ben. A következő lépések bemutatják, hogyan natív alkalmazás regisztrálása és adjon neki hozzáférést a webes alkalmazásproxyn keresztül közzétett API-hoz.

A AppProxyNativeAppSample natív alkalmazás regisztrálása:

1. Az Azure Active Directoryban **áttekintése** lapon jelölje be **alkalmazásregisztrációk**, és a felső részén a **alkalmazásregisztrációk** ablaktáblán válassza **új regisztrációs** .
   
1. Az a **alkalmazás regisztrálása** oldalon:
   
   1. A **neve**, adja meg *AppProxyNativeAppSample*. 
      
   1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**. 
      
   1. A **átirányítási URL-cím**, a legördülő listára, és válassza ki **(mobil és asztali) nyilvános ügyfél**, majd adja meg *https:\//appproxynativeapp*. 
      
   1. Válassza ki **regisztrálása**, és várjon, amíg az alkalmazás regisztrálása sikeres volt. 
      
      ![Új alkalmazásregisztráció](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Most már regisztrálta az AppProxyNativeAppSample alkalmazást az Azure Active Directoryban. A natív alkalmazás-hozzáférés biztosítása a SecretAPI webes API-hoz:

1. Az Azure Active Directoryban **áttekintése** > **Alkalmazásregisztrációk** lapon válassza ki a **AppProxyNativeAppSample** alkalmazást. 
   
1. Az a **AppProxyNativeAppSample** lapon jelölje be **API-engedélyek** a bal oldali navigációs menüben. 
   
1. Az a **API-engedélyek** lapon jelölje be **adjon hozzá egy engedélyt**.
   
1. Az első **kérelem API-engedélyek** lapon válassza ki a **API-k saját szervezete** fülre, és keressen rá, és válassza ki **SecretAPI**. 
   
1. A következő **kérelem API-engedélyek** lapra, jelölje be a jelölőnégyzetet a **user_impersonation**, majd válassza ki **engedélyek hozzáadása**. 
   
    ![API kiválasztása](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Lépjen vissza a **API-engedélyek** lapon kiválaszthatja **biztosítson rendszergazdai jóváhagyás contoso** meg, hogy más felhasználók nem kell külön-külön jóváhagyhatják az alkalmazást. 

## <a name="configure-the-native-app-code"></a>Konfigurálja a natív alkalmazás kódját

Az utolsó lépés, hogy a natív alkalmazás konfigurálása. A következő kódrészlet a a *Form1.cs* fájlt a NativeClient mintaalkalmazás hatására az ADAL könyvtár beszerezni a jogkivonatot az API-hívás kérelmezési és tulajdonosi, csatlakoztassa azt az alkalmazás fejléce. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
A natív alkalmazás csatlakoztatása az Azure Active Directoryhoz, és hívja az API App Proxy konfigurálása, frissítse a szereplő helyőrző értékeket az *App.config* fájlt a NativeClient mintaalkalmazásról az Azure AD-ből származó értékekkel: 

- Illessze be a **(bérlő) címtár-azonosító** a a `<add key="ida:Tenant" value="" />` mező. Keresse meg, és másolja a (egy GUID azonosítója) értékét a **áttekintése** oldalán az alkalmazások valamelyike. 
  
- Illessze be a AppProxyNativeAppSample **Alkalmazásazonosítót (ügyfél)** a a `<add key="ida:ClientId" value="" />` mező. Keresse meg, és másolja ezt az értéket (GUID), a AppProxyNativeAppSample **áttekintése** lapot.
  
- Illessze be a AppProxyNativeAppSample **átirányítási URI-t** a a `<add key="ida:RedirectUri" value="" />` mező. Keresse meg, és másolja a értékét (URI) a AppProxyNativeAppSample **hitelesítési** lapot. 
  
- Illessze be a SecretAPI **Alkalmazásazonosító URI-ja** a a `<add key="todo:TodoListResourceId" value="" />` mező. Keresse meg, és másolja a értékét (URI) a SecretAPI **közzé API-k** lap.
  
- Illessze be a SecretAPI **kezdőlap URL-címe** a a `<add key="todo:TodoListBaseAddress" value="" />` mező. Keresse meg, és másolja a értékét (URL) a SecretAPI **Branding** lapot.

Miután konfigurálta a paramétereket, hozhat létre, és futtassa a natív alkalmazást. Amikor kiválasztja a **bejelentkezés** gombra, az alkalmazás lehetővé teszi a bejelentkezés, és ezután jeleníti meg egy sikert jelző képernyő, győződjön meg arról, hogy sikeresen csatlakozik a SecretAPI.

![Siker](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure Active Directoryban](application-proxy-add-on-premises-application.md)
- [Rövid útmutató: Webes API-k eléréséhez ügyfélalkalmazás konfigurálása](../develop/quickstart-configure-app-access-web-apis.md)
- [Együttműködhet a proxy-alkalmazások natív ügyfélalkalmazások engedélyezése](application-proxy-configure-native-client-application.md)
