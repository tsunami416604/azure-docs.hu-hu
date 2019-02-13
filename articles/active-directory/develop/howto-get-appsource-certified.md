---
title: AppSource minősítés az Azure Active Directory beszerzése |} A Microsoft Docs
description: Részletes információk az alkalmazás AppSource certified for Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6231ab1a2de2428a821ec227733f763812e8778f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163733"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>AppSource Certified beszerzése az Azure Active Directory

[A Microsoft AppSource](https://appsource.microsoft.com/) egy cél az üzleti felhasználók felderítése, próbálja meg és kezelése – üzleti SaaS-alkalmazások (önálló SaaS és bővítményt az meglévő Microsoft SaaS-termékek).

Az alkalmazás egyszeri bejelentkezés a munkahelyi fiókok bármely vállalat vagy szervezet, amely rendelkezik az Azure Active Directory (Azure AD) egy különálló SaaS-alkalmazás az appsource-on listázásához, el kell fogadnia. A bejelentkezési folyamatot kell használnia a [OpenID Connect](v1-protocols-openid-connect-code.md) vagy [OAuth 2.0](v1-protocols-oauth-code.md) protokollokat. SAML-integráció nem fogadja az AppSource-tanúsítvány.

## <a name="guides-and-code-samples"></a>Útmutatók és Kódminták

Ha azt szeretné, hogyan integrálható az alkalmazás az Azure AD szolgáltatással Open ID connect, hajtsa végre az útmutatókat, és Kódminták a a [Azure Active Directory fejlesztői útmutatója](v1-overview.md#get-started "Ismerkedés az Azure AD-hez a fejlesztők").

## <a name="multi-tenant-applications"></a>Több-bérlős alkalmazások

A *több-bérlős alkalmazás* olyan alkalmazás, amely elfogadja a felhasználók bármely vállalat vagy szervezet, amely rendelkezik az Azure AD anélkül, hogy egy külön példányt, konfigurációs vagy telepítési történő bejelentkezések. Appsource-ban azt javasolja, hogy az alkalmazások valósítható meg több-bérlős engedélyezéséhez a *egyetlen kattintással* ingyenes kipróbálására nyújt lehetőséget.

Ahhoz, hogy az alkalmazás több-bérlős, kövesse az alábbi lépéseket:
1. Állítsa be `Multi-Tenanted` tulajdonságot `Yes` a az alkalmazás regisztrációs információit a [az Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Alapértelmezés szerint az Azure Portalon létrehozott alkalmazásokat alkalmazásbeállításait  *[egybérlős](#single-tenant-applications)*.
1. Kérelmek küldése a kód frissítése a `common` végpont. Ehhez a végponthoz való frissítése `https://login.microsoftonline.com/{yourtenant}` való `https://login.microsoftonline.com/common*`.
1. Egyes platformokon, például az ASP .NET is frissíteni szeretné a kód több kiállítók fogadására.

Több-bérlős kapcsolatos további információkért lásd: [Azure Active Directory (Azure AD) felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával hogyan](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Egybérlős alkalmazások

A *egybérlős alkalmazás* olyan alkalmazás, amely csak a meghatározott Azure-felhasználók történő bejelentkezések fogad AD-példányt. Külső felhasználókat (beleértve a személyes, munkahelyi vagy iskolai fiókok más szervezetek) egy egybérlős alkalmazást minden felhasználó, hogy az alkalmazás regisztrálva van az Azure AD-példányt, amely a Vendég fiók hozzáadása után bejelentkezhet. 

Az Azure AD-keresztül, a vendég felhasználók is hozzáadhat a [Azure AD B2B együttműködés](../b2b/what-is-b2b.md) és ehhez [programozott módon](../../active-directory-b2c/code-samples.md). B2B használata esetén a felhasználók egy önkiszolgáló portált, jelentkezzen be a meghívót nem igénylő hozhat létre. További információ: [önkiszolgáló portál az Azure AD B2B együttműködés előfizetési](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Egybérlős alkalmazások engedélyezheti a *kapcsolatfelvételi* tapasztal, de az egyetlen kattintással/ingyenes próbaverziója által AppSource javasolt engedélyezni szeretné, ha több bérlős üzemmód engedélyezése az alkalmazás helyett.

## <a name="appsource-trial-experiences"></a>Próbaverziós élményt appsource-ban

### <a name="free-trial-customer-led-trial-experience"></a>Ingyenes próbaverzió (ügyfél által vezetett kipróbálására nyújt lehetőséget) 

Az ügyfél által irányított próbaverzió, amely az appsource-ban javasol, mert az egy kattintással az alkalmazáshoz való hozzáférést biztosít a számítógép jelenik meg. Az ábra bemutatja, hogyan alatt ez a tapasztalat hasonlóan néz ki:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Felhasználói megkeresi az alkalmazást az AppSource webhelyen</li><li>"Ingyenes" lehetőség kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>Appsource-ban átirányítja a felhasználót, hogy a webhely URL-címet</li><li>A webhely elindul a <i>single-sign-on</i> folyamatát automatikusan (betöltés)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>A Microsoft bejelentkezési oldala a rendszer átirányítja a felhasználót</li><li>A felhasználó megadja hitelesítő adatait, jelentkezzen be</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Felhasználó beleegyezésével az alkalmazáshoz</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Jelentkezzen be befejeződött, és vissza a webhelyet a rendszer átirányítja a felhasználót</li><li>Felhasználó elindítja az ingyenes próbaverzióra</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Megkeresést kérek (partner által irányított kipróbálására nyújt lehetőséget)

A partner kipróbálására nyújt lehetőséget is használhatja, amikor egy manuális vagy hosszú távú műveletet kell rendelkezni kell a felhasználó és vállalat – például az alkalmazás üzembe helyezése a virtuális gépek, az adatbázis-példány vagy a műveletek, amelyek befejezéséhez sok időt vesz igénybe. Ebben az esetben után a felhasználó által a **próbaverzió kérése** gombra, és alapján tölti ki egy űrlapot, AppSource küld Önnek a felhasználó kapcsolattartási adatait. Ha megkapta ezt az információt, ezután kiépítése a környezet és az utasítások küldése a felhasználónak az próbaverziója elérésével:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Felhasználó az alkalmazás az AppSource webhelyen talál.</li><li>"Me Contact" lehetőség kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Kitölt egy űrlapot, a kapcsolattartási adatok</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Felhasználói adatok kap</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Környezet beállítása</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Ügyfél felhasználói próbaverziós információval</td>
        </tr>
        </table><br/><br/>
        <ul><li>Felhasználó információkat és a telepítő próbaverziós példányt kap</li><li>Küldünk, érhetik el az alkalmazást a felhasználó számára, hogy a hivatkozás</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Felhasználó hozzáfér az alkalmazáshoz, és a single-sign-on befejezéséhez</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Felhasználó beleegyezésével az alkalmazáshoz</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Jelentkezzen be befejeződött, és vissza a webhelyet a rendszer átirányítja a felhasználót</li><li>Felhasználó elindítja az ingyenes próbaverzióra</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>További információ

Az appsource-ban próbaverziója kapcsolatos további információkért lásd: [ebben a videóban](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>További lépések

- Az alkalmazásokat, amelyek támogatják az Azure AD bejelentkezési további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Az SaaS-alkalmazását az appsource-ban való információkért látogasson el lásd: [partneradatok appsource-ban](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Támogatás kérése

Az Azure AD-integrációhoz használjuk [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) támogatásához a Közösséggel. 

Először a Stack overflow-ra tesz fel kérdéseket, és keresse meg a meglévő problémák megtekintéséhez, ha valaki kérte a kérdés előtt erősen ajánlott. Győződjön meg arról, hogy a kérdéseit vagy megjegyzéseit címkével ellátott [ `[azure-active-directory]` és `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started


<!--Image references-->
