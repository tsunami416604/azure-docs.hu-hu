---
title: Helyszíni API-k elérése az Azure AD alkalmazásproxyval
description: Az Azure Active Directory alkalmazásproxyja lehetővé teszi a natív alkalmazások biztonságos elérését a helyszíni vagy a felhőbeli virtuális gépeken üzemeltetett API-k és üzleti logika között.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166018"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Biztonságos hozzáférés a helyszíni API-khoz az Azure AD alkalmazásproxyval

Előfordulhat, hogy a helyszíni üzleti logikai API-k, vagy a felhőben üzemeltetett virtuális gépeken. A natív Android-, iOS-, Mac- vagy Windows-alkalmazásoknak az adatok használatához vagy felhasználói beavatkozáshoz kell együttműködniük az API-végpontokkal. Az Azure AD-alkalmazásproxy és az [Azure Active Directory hitelesítési kódtárak (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) lehetővé teszik a natív alkalmazások biztonságos hozzáférést biztosít a helyszíni API-k. Az Azure Active Directory alkalmazásproxy gyorsabb és biztonságosabb megoldás, mint a tűzfalportok megnyitása és a hitelesítés és engedélyezés vezérlése az alkalmazásrétegen. 

Ez a cikk bemutatja egy Azure AD alkalmazásproxy-megoldás beállítását egy webes API-szolgáltatás üzemeltetéséhez, amelyhez natív alkalmazások hozzáférhetnek. 

## <a name="overview"></a>Áttekintés

Az alábbi ábrán a helyszíni API-k közzétételének hagyományos módja látható. Ehhez a megközelítéshez meg kell nyitni a 80-as és 443-as bejövő portokat.

![Hagyományos API-hozzáférés](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Az alábbi ábra bemutatja, hogyan használhatja az Azure AD alkalmazásproxyt az API-k biztonságos közzétételére a bejövő portok megnyitása nélkül:

![Azure AD alkalmazásproxy API-hozzáférés](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Az Azure AD alkalmazásproxy képezi a megoldás gerincét, az API-hozzáférés nyilvános végpontjaként dolgozik, és hitelesítést és engedélyezést biztosít. Az API-k at a platformok széles skálájáról érheti el az [ADAL-kódtárak](/azure/active-directory/develop/active-directory-authentication-libraries) használatával. 

Mivel az Azure AD-alkalmazásproxy-hitelesítés és -engedélyezés az Azure AD-n alapul, az Azure AD feltételes hozzáféréshasználatával biztosíthatja, hogy csak a megbízható eszközök férhessenek hozzá az Alkalmazásproxyn keresztül közzétett API-khoz. Használja az Azure AD Join vagy az Azure AD hybrid Joined asztali számítógépekhez, és az Intune felügyelt eszközökre. Emellett kihasználhatja az Azure Active Directory Premium funkcióit, például az Azure többtényezős hitelesítést és az [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)gépi tanulásáltal támogatott biztonságát.

## <a name="prerequisites"></a>Előfeltételek

A forgatókönyv követéséhez a következőkre van szükség:

- Rendszergazdai hozzáférés egy Azure-címtárhoz olyan fiókkal, amely alkalmazásokat hozhat létre és regisztrálhat
- A minta webes API és a natív ügyfélalkalmazások[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Az API közzététele alkalmazásproxyn keresztül

Ha az alkalmazásproxyn keresztül az intraneten kívül szeretne közzétenni egy API-t, ugyanazt a mintát kell követnie, mint a webalkalmazások közzétételénél. További információ: [Oktatóanyag: Egy helyszíni alkalmazás hozzáadása a távoli eléréshez az Azure Active Directory alkalmazásproxyn keresztül című témakörben.](application-proxy-add-on-premises-application.md)

A SecretAPI webes API közzététele alkalmazásproxyn keresztül:

1. A minta SecretAPI-projektet ASP.NET webalkalmazásként hozhatja létre és teheti közzé a helyi számítógépen vagy intraneten. Győződjön meg arról, hogy helyileg is elérheti a webalkalmazást. 
   
1. Az [Azure Portalon](https://portal.azure.com)válassza az **Azure Active Directory**lehetőséget. Ezután válassza **a Vállalati alkalmazások lehetőséget**.
   
1. A **Vállalati alkalmazások – Minden alkalmazás** lap tetején válassza az Új **alkalmazás**lehetőséget.
   
1. Az **Alkalmazás hozzáadása** lapon válassza a **Helyszíni alkalmazások**lehetőséget. Megjelenik **a Saját helyszíni alkalmazás** hozzáadása lap.
   
1. Ha nincs telepítve alkalmazásproxy-összekötő, a rendszer kérni fogja annak telepítését. Válassza **az Alkalmazásproxy-összekötő letöltéséhez** és telepítéséhez válassza az Alkalmazásproxy-összekötő letöltését és telepítését. 
   
1. Miután telepítette az alkalmazásproxy-összekötőt, a **Saját helyszíni alkalmazás hozzáadása** lapon:
   
   1. A **Név**mezőbe írja be a *SecretAPI*értéket.
      
   1. A **Belső URL csoportban**adja meg az API intraneten belüli eléréséhez használt URL-címet.
      
   1. Győződjön meg arról, hogy az **előhitelesítés** az **Azure Active Directoryra**van állítva. 
      
   1. Válassza a Lap tetején a **Hozzáadás** lehetőséget, és várja meg az alkalmazás létrehozását.
   
   ![API-alkalmazás hozzáadása](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. A **Vállalati alkalmazások – Minden alkalmazás** lapon válassza a **SecretAPI** alkalmazást. 
   
1. A **SecretAPI – Áttekintés** lapon válassza a **Tulajdonságok lehetőséget** a bal oldali navigációs sávon.
   
1. Nem szeretné, hogy az API-k elérhetők legyenek a végfelhasználók számára a **MyApps** panelen, ezért állítsa a **Látható a felhasználók számára látható** t a Tulajdonságok lap alján lévő Nem **(Nem)** beállításra, majd a Mentés **lehetőséget.** **Properties**
   
   ![A felhasználók számára nem látható](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Közzétette a webes API-t az Azure AD alkalmazásproxyn keresztül. Most adjon hozzá olyan felhasználókat, akik hozzáférhetnek az alkalmazáshoz. 

1. A **SecretAPI – Áttekintés** lapon válassza a **Felhasználók és csoportok** lehetőséget a bal oldali navigációs sávon.
   
1. A **Felhasználók és csoportok** lapon válassza a **Felhasználó hozzáadása lehetőséget.**  
   
1. A **Hozzárendelés hozzáadása** lapon válassza a **Felhasználók és csoportok**lehetőséget. 
   
1. A **Felhasználók és csoportok** lapon keresse meg és válassza ki azokat a felhasználókat, akik hozzáférhetnek az alkalmazáshoz, beleértve legalább saját magát is. Az összes felhasználó kijelölése után válassza a **Kijelölés lehetőséget.** 
   
   ![Felhasználó kijelölése és hozzárendelése](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. A **Hozzárendelés hozzáadása** lapon kattintson a **Hozzárendelés gombra.** 

> [!NOTE]
> Az integrált Windows-hitelesítést használó API-k [további lépéseket](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)igényelhetnek.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Regisztrálja a natív alkalmazást, és adjon hozzáférést az API-hoz

A natív alkalmazások olyan programok, amelyeket egy adott platformon vagy eszközön való használatra fejlesztettek ki. Mielőtt a natív alkalmazás csatlakozhatna, és hozzáférhetne egy API-hoz, regisztrálnia kell azt az Azure AD-ben. A következő lépések bemutatják, hogyan regisztrálhat egy natív alkalmazást, és hogyan adhat hozzáférést az alkalmazásproxyn keresztül közzétett webes API-hoz.

Az AppProxyNativeAppSample natív alkalmazás regisztrálása:

1. Az Azure Active Directory **áttekintése** lapon válassza az **Alkalmazásregisztrációk**lehetőséget, és az **alkalmazásregisztrációk** ablaktábla tetején válassza az **Új regisztráció**lehetőséget.
   
1. A **Jelentkezés regisztrálása** lapon:
   
   1. A **Név mezőbe**írja be az *AppProxyNativeAppSample értéket.* 
      
   1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.** 
      
   1. Az **URL átirányítása csoportban**válassza a **Nyilvános ügyfél (mobil & asztali)** lehetőséget, majd írja be *a https:\//appproxynativeapp parancsot.* 
      
   1. Válassza **a Regisztráció**lehetőséget, és várja meg, amíg az alkalmazás sikeresen regisztrálva lesz. 
      
      ![Új alkalmazásregisztráció](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Most már regisztrálta az AppProxyNativeAppSample alkalmazást az Azure Active Directoryban. A natív alkalmazás hozzáférésének a SecretAPI webes API-hoz való hozzáférése:

1. Az Azure Active Directory **áttekintő** > **alkalmazásregisztrációk** lapon válassza az **AppProxyNativeAppSample** alkalmazást. 
   
1. Az **AppProxyNativeAppSample** lapon válassza az **API-engedélyeket** a bal oldali navigációs sávon. 
   
1. Az **API-engedélyek** lapon válassza az **Engedély hozzáadása**lehetőséget.
   
1. Az első **API-engedélyek kérése** lapon jelölje ki a **szervezet által használt API-kat,** majd keresse meg a **SecretAPI parancsot.** 
   
1. A következő **API-engedélyek kérése** lapon jelölje be a **user_impersonation**melletti jelölőnégyzetet, majd az **Engedélyek hozzáadása**lehetőséget. 
   
    ![API kiválasztása](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Az **API-engedélyek** lapon kiválaszthatja a **Contoso rendszergazdai hozzájárulásának megadását,** hogy megakadályozza, hogy más felhasználóknak egyénileg kelljen hozzájárulniuk az alkalmazáshoz. 

## <a name="configure-the-native-app-code"></a>A natív alkalmazáskód konfigurálása

Az utolsó lépés a natív alkalmazás konfigurálása. A nativeclient mintaalkalmazás *Form1.cs* fájljából következő kódrészlet hatására az ADAL-függvénytár beszerzi az API-hívás kéréséhez a jogkivonatot, és csatolja az alkalmazásfejléchez. 
   
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
   
Ha úgy szeretné konfigurálni a natív alkalmazást, hogy csatlakozzon az Azure Active Directoryhoz, és hívja meg az API-alkalmazásproxyt, frissítse a NativeClient mintaalkalmazás *App.config* fájljában lévő helyőrző értékeket az Azure AD értékeivel: 

- Illessze be a **címtár (bérlői) azonosítóját** a `<add key="ida:Tenant" value="" />` mezőbe. Ezt az értéket (GUID azonosítót) bármelyik alkalmazás **Áttekintés lapján** találhatja meg és másolhatja. 
  
- Illessze be az AppProxyNativeAppSample alkalmazás `<add key="ida:ClientId" value="" />` **(ügyfél) azonosítóját** a mezőbe. Ezt az értéket (GUID) az AppProxyNativeAppSample **áttekintése** lapon találhatja meg és másolhatja.
  
- Illessze be az AppProxyNativeAppSample `<add key="ida:RedirectUri" value="" />` **átirányítási URI-t** a mezőbe. Ezt az értéket (URI)-t az AppProxyNativeAppSample **hitelesítés** lapról keresheti meg és másolhatja. 
  
- Illessze be a SecretAPI `<add key="todo:TodoListResourceId" value="" />` **alkalmazásazonosító URI-ját** a mezőbe. Ezt az értéket (URI-t) a SecretAPI **API-nak elérhetővé teszi lapon** találhatja meg és másolhatja.
  
- Illessze be a SecretAPI `<add key="todo:TodoListBaseAddress" value="" />` **kezdőlapjának URL-címét** a mezőbe. Ezt az értéket (URL-címet) a SecretAPI **márkajelzési** lapjáról keresheti meg és másolhatja.

A paraméterek konfigurálása után hozzon létre és futtassa a natív alkalmazást. Ha a **Bejelentkezés** gombra kattint, az alkalmazás lehetővé teszi a bejelentkezést, majd megjelenít egy sikeres képernyőt annak megerősítéséhez, hogy sikeresen csatlakozott-e a SecretAPI-hoz.

![Sikeres](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure Active Directory alkalmazásproxyn keresztüli távoli eléréshez helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md)
- [Rövid útmutató: Ügyfélalkalmazás konfigurálása webes API-k eléréséhez](../develop/quickstart-configure-app-access-web-apis.md)
- [A natív ügyfélalkalmazások proxyalkalmazásokkal való interakciójának engedélyezése](application-proxy-configure-native-client-application.md)
