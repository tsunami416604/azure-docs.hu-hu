---
title: Az AppSource azure Active Directoryhoz való minősítése| Microsoft dokumentumok
description: Az alkalmazás AppSource-minősítésazure-Directoryminősítéssel való beszerzése részletei.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ad4efa3b8126a9b9c6557822f61e3bfff3fe120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154882"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Az AppSource Certified for Azure Active Directory beszerezni

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[A Microsoft AppSource](https://appsource.microsoft.com/) az üzleti felhasználók számára lehetővé teszi az üzletági SaaS-alkalmazások (önálló SaaS és meglévő Microsoft SaaS-termékek bővítmény) felderítésére, kipróbálására és kezelésére szolgáló célhely.

Egy önálló SaaS-alkalmazás listázásához az AppSource-on, az alkalmazás nak el kell fogadnia egyszeri bejelentkezés a munkahelyi fiókok bármely vállalat vagy szervezet, amely rendelkezik az Azure Active Directory (Azure AD). A bejelentkezési folyamatnak az [OpenID Connect](v1-protocols-openid-connect-code.md) vagy az [OAuth 2.0](v1-protocols-oauth-code.md) protokollt kell használnia. Saml-integráció nem fogadható el az AppSource-minősítéshez.

## <a name="guides-and-code-samples"></a>Útmutatók és kódminták

Ha meg szeretné tudni, hogyan integrálhatja alkalmazását az Azure AD-vel az Open ID connect használatával, kövesse útmutatóinkat és kódmintáinkat az [Azure Active Directory fejlesztői útmutatójában.](v1-overview.md#get-started "Első lépések az Azure AD-vel fejlesztőknek")

## <a name="multi-tenant-applications"></a>Több-bérlős alkalmazások

A *több-bérlős alkalmazás* olyan alkalmazás, amely elfogadja a bejelentkezések a felhasználók bármely vállalat vagy szervezet, amely az Azure AD anélkül, hogy egy külön példány, konfiguráció vagy üzembe helyezés. Az AppSource azt javasolja, hogy az alkalmazások valósítsák meg a több-bérlős, hogy az *egykattintásos* ingyenes próbaverzió.

A több-bérlős alkalmazás engedélyezéséhez kövesse az alábbi lépéseket:
1. Állítsa `Multi-Tenanted` be `Yes` a tulajdonságot az alkalmazásregisztráció adataira az [Azure Portalon.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) Alapértelmezés szerint az Azure Portalon létrehozott alkalmazások *[egybérlősként](#single-tenant-applications)* vannak konfigurálva.
1. Frissítse a kódot, hogy `common` kéréseket küldjön a végpontra. Ehhez frissítse a végpontot `https://login.microsoftonline.com/{yourtenant}` a `https://login.microsoftonline.com/common*`rendszerről a rendszerre.
1. Egyes platformokon, például az ASP .NET-en, a kódot is frissítenie kell, hogy több kibocsátót fogadjon el.

A többbérlős szolgáltatásról további információt a [Bejelentkezés bármely Azure Active Directory (Azure AD) felhasználóba a több-bérlős alkalmazásmintával című témakörben](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)talál.

### <a name="single-tenant-applications"></a>Egybérlős alkalmazások

Egy *egybérlős alkalmazás* olyan alkalmazás, amely csak egy meghatározott Azure AD-példány felhasználóitól fogad el bejelentkezéseket. Külső felhasználók (beleértve a munkahelyi vagy iskolai fiókok más szervezetek, vagy a személyes fiókok) bejelentkezhet egy egybérlős alkalmazás hozzáadása után minden felhasználó vendégfiókként az Azure AD-példány, hogy az alkalmazás regisztrálva van. 

Az [Azure AD B2B együttműködésen](../b2b/what-is-b2b.md) keresztül vendégfiókként adhat hozzá felhasználókat az Azure AD-hez, és ezt programozott módon is [megteheti.](../../active-directory-b2c/code-samples.md) A B2B használata esetén a felhasználók létrehozhatnak egy önkiszolgáló portált, amely nem igényel meghívást a bejelentkezéshez. További információ: [Self-service portal for Azure AD B2B collaboration sign-up.](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)

Az egybérlős alkalmazások engedélyezhetik a *Kapcsolat i* agatát, de ha engedélyezni szeretné az AppSource által ajánlott egykattintásos/ingyenes próbaverziót, engedélyezze a többbérlős alkalmazást az alkalmazáson.

## <a name="appsource-trial-experiences"></a>AppSource próbaverziós élményei

### <a name="free-trial-customer-led-trial-experience"></a>Ingyenes próbaverzió (ügyfél által vezetett próbaverzió)

Az ügyfél által vezetett próbaverzió az appsource által ajánlott élmény, mivel egykattintásos hozzáférést biztosít az alkalmazáshoz. A következő példa bemutatja, hogyan néz ki ez az élmény:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>A felhasználó megkeresi az alkalmazást az AppSource webhelyen</li><li>Az "Ingyenes próbaverzió" lehetőség kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>Az AppSource átirányítja a felhasználót egy URL-re a webhelyén</li><li>A webhely automatikusan elindítja az <i>egyszeri bejelentkezési</i> folyamatot (oldalbetöltéskor)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>A felhasználó átlesz irányítva a Microsoft bejelentkezési lapjára</li><li>A felhasználó hitelesítő adatokat ad a bejelentkezéshez</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>A felhasználó hozzájárul az ön alkalmazásához</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>A bejelentkezés befejeződik, és a felhasználó torkig lesz a webhelyével</li><li>A felhasználó elindítja az ingyenes próbaverziót</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kapcsolatfelvétel (partner által vezetett próbaverzió)

Használhatja a partner próba-tapasztalat, ha egy manuális vagy hosszú távú művelet et kell történnie, hogy a felhasználó/vállalat kiépítése – például az alkalmazás kell kiépíteni a virtuális gépek, adatbázis-példányok, vagy műveletek, amelyek sok időt vesz igénybe. Ebben az esetben, miután a felhasználó kiválasztja a **Próbakérés** gombot, és kitölt egy űrlapot, az AppSource elküldi önnek a felhasználó kapcsolattartási adatait. Amikor megkapja ezt az információt, akkor kiépíti a környezetet, és elküldi az utasításokat a felhasználónak, hogyan férhet hozzá a próbaverzióélményhez:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>A felhasználó megkeresi az alkalmazást az AppSource webhelyén</li><li>A "Kapcsolat" lehetőség kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Kitölt egy űrlapot kapcsolattartási adatokkal</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Felhasználói adatokat kap</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>A környezet beállítása</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Kapcsolatfelvétel a felhasználóval a próbaverzió adataival</td>
        </tr>
        </table><br/><br/>
        <ul><li>Megkapja a felhasználó adatait és a beállítási próbapéldányt</li><li>A hivatkozást az alkalmazás eléréséhez küldi a felhasználónak</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>A felhasználó hozzáfér az alkalmazáshoz, és befejezi az egyszeri bejelentkezési folyamatot</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>A felhasználó hozzájárul az ön alkalmazásához</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>A bejelentkezés befejeződik, és a felhasználó torkig lesz a webhelyével</li><li>A felhasználó elindítja az ingyenes próbaverziót</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>További információ

Az AppSource próbaverziós élményéről további információt ebben a [videóban](https://aka.ms/trialexperienceforwebapps)talál. 

## <a name="next-steps"></a>Következő lépések

- Az Azure AD-bejelentkezéseket támogató alkalmazások létrehozásáról az [Azure AD hitelesítési forgatókönyvei](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios)című témakörben talál további információt.
- A SaaS-alkalmazás AppSource-ban való listázásáról az [AppSource partnerinformáció című](https://appsource.microsoft.com/partners) témakörben talál további információt.

## <a name="get-support"></a>Támogatás kérése

Az Azure AD-integrációhoz [a Veremtúlcsordulást](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) használjuk a közösséggel, hogy támogatást nyújtsunk.

Javasoljuk, hogy először tegye fel kérdéseit a Stack Overflow-val kapcsolatban, és böngésszen a meglévő problémák között, és nézze meg, hogy valaki korábban feltette-e a kérdését. Győződjön meg arról, hogy kérdéseit vagy megjegyzéseit a [ `[azure-active-directory]` és `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)a címkével látták el.

A következő megjegyzések részben visszajelzést adhat, és segíthet a tartalom finomításában és alakításában.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
