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
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 9c2140d0e482089be632d9a21560349a6381968e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495237"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>AppSource Certified beszerzése az Azure Active Directory
[A Microsoft AppSource](https://appsource.microsoft.com/) egy cél az üzleti felhasználók felderítése, próbálja meg és kezelése – üzleti SaaS-alkalmazások (önálló SaaS és bővítményt az meglévő Microsoft SaaS-termékek).

Az alkalmazás egyszeri bejelentkezés a munkahelyi fiókok bármely vállalat vagy szervezet, amely rendelkezik az Azure Active Directory egy különálló SaaS-alkalmazás az appsource-on listázásához, el kell fogadnia. A bejelentkezési folyamatot kell használnia a [OpenID Connect](./active-directory-protocols-openid-connect-code.md) vagy [OAuth 2.0](./active-directory-protocols-oauth-code.md) protokollokat. SAML-integráció nem fogadja az AppSource-tanúsítvány.

## <a name="guides-and-code-samples"></a>Útmutatók és Kódminták
Ha azt szeretné, hogyan integrálható az Azure Active Directory-azonosítójával nyissa meg az alkalmazás csatlakoztatása, hajtsa végre az útmutatókat, és Kódminták a a [Azure Active Directory fejlesztői útmutatója](azure-ad-developers-guide.md#get-started "első lépései az Azure-ral A fejlesztők AD").

## <a name="multi-tenant-applications"></a>Több-bérlős alkalmazások

A felhasználók bármely vállalat vagy szervezet, amely rendelkezik az Azure Active Directory anélkül, hogy egy külön példányt, konfigurációs vagy telepítési bejelentkezések elfogadó alkalmazás más néven egy *több-bérlős alkalmazás*. Appsource-ban azt javasolja, hogy az alkalmazások valósítható meg több-bérlős engedélyezéséhez a *egyetlen kattintással* ingyenes kipróbálására nyújt lehetőséget.

Annak érdekében, hogy az alkalmazás több bérlős üzemmód engedélyezése:
- Állítsa be `Multi-Tenanted` tulajdonságot `Yes` a az alkalmazás regisztrációs információit a [az Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (alapértelmezés szerint az Azure Portalon létrehozott alkalmazásokat-kként vannak konfigurálva *egybérlős*)
- Kérelmek küldése a kód frissítése a "`common`" végpont (az a végpont frissítéséhez *https://login.microsoftonline.com/{yourtenant}* való *https://login.microsoftonline.com/common*)
- Egyes platformokon, például az ASP.NET is frissíteni kell a kódot úgy, hogy fogadja el a több kiállítók

Több-bérlős kapcsolatos további információkért lásd: [Azure Active Directory (AD) felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával hogyan](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Egybérlős alkalmazások
Az alkalmazásokat, amelyek csak fogadja el a felhasználók egy meghatározott Azure Active Directory-példány történő bejelentkezések nevezzük *egybérlős alkalmazás*. Külső felhasználókat (beleértve a más szervezetek a munkahelyi vagy iskolai fiókokhoz vagy személyes fiókkal) bejelentkezhet egy egybérlős alkalmazást minden felhasználó hozzáadása után *vendégfiók* az Azure Active Directory példányt, amely a alkalmazás regisztrálva lesz. Egy Azure Active Directory-n keresztül, a vendég felhasználók is hozzáadhat a [ *Azure AD B2B együttműködés* ](../b2b/what-is-b2b.md) -teheti meg, és [programozottan](../b2b/code-samples.md). Hozzáadásakor a felhasználó Vendég fiókkal az Azure Active Directory, a felhasználó, aki rendelkezik a meghívás elfogadásához kattintson a hivatkozásra a meghívó e-mailben a meghívó e-mail érkezik. A meghívó szervezetet, amely egyben a fiókpartner-szervezet tagja a további felhasználó számára küldött nem szükségesek egy jelentkezzen be a meghívás elfogadásához.

Egybérlős alkalmazások engedélyezheti a *kapcsolatfelvételi* tapasztal, de az egy kattintással / ingyenes próbaverziója által AppSource javasolt engedélyezni szeretné, ha több bérlős üzemmód engedélyezése az alkalmazás helyett.


## <a name="appsource-trial-experiences"></a>Próbaverziós élményt appsource-ban

### <a name="free-trial-customer-led-trial-experience"></a>Ingyenes próbaverzió (ügyfél által vezetett kipróbálására nyújt lehetőséget) 
A *ügyfél által irányított próbaverzió* a szolgáltatás, amelynek a AppSource javasol, mert az egy kattintással az alkalmazáshoz való hozzáférést biztosít. Az ábra bemutatja, hogyan alatt ez a tapasztalat hasonlóan néz ki:<br/><br/>

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

### <a name="contact-me-partner-led-trial-experience"></a>Megkeresést kérek (Partner által irányított kipróbálására nyújt lehetőséget)
A *próbaverziója partneri* is használható, ha a manuális vagy hosszú távú műveletet kell a felhasználóhoz / vállalati: például az alkalmazásnak kell kiépíteni a virtuális gépek, adatbázis-példány vagy műveletek mennyi ideig tarthat, amely. Ebben az esetben a felhasználó által után a *próbaverzió kérése* gombra, és alapján tölti ki egy űrlapot, AppSource küld Önnek a felhasználó kapcsolattartási adatait. Ez az információ fogadásakor ezután kiépítése a környezet és az utasítások küldése a felhasználónak az próbaverziója elérésével:<br/><br/>

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

- További információ az alkalmazások, amelyek támogatják az Azure Active Directory bejelentkezések létrehozására: [hitelesítési forgatókönyvek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Az SaaS-alkalmazását az appsource-ban való információkért látogasson el lásd: [partneradatok appsource-ban](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Támogatás kérése
Az Azure Active Directory-integráció, használunk [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) támogatásához a Közösséggel. 

Először a Stack overflow-ra tesz fel kérdéseket, és keresse meg a meglévő problémák megtekintéséhez, ha valaki kérte a kérdés előtt erősen ajánlott. Győződjön meg arról, hogy a kérdéseit vagy megjegyzéseit címkével ellátott [ `[azure-active-directory]` és `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: azure-ad-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: azure-ad-developers-guide.md#get-started


<!--Image references-->