---
title: "Az Azure Active Directory hitelesített AppSource beszerzése |} Microsoft Docs"
description: "Az alkalmazás az Azure Active Directory hitelesített AppSource tájékoztatást."
services: active-directory
documentationcenter: 
author: andretms
manager: mtillman
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5601ad80e271364fec519cf34bcdc2f650f3bb92
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Hogyan kérhet a AppSource hitelesített az Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) egy cél üzleti felhasználók számára, próbálja meg, valamint (önálló SaaS és a meglévő Microsoft SaaS-termékekben bővítmény) sor üzleti SaaS-alkalmazások kezelésében.

Önálló SaaS-alkalmazás AppSource a listában, az alkalmazás el kell fogadnia az egyszeri bejelentkezés a munkahelyi fiókok bármely vállalat vagy szervezet, amely Azure Active Directoryban. A bejelentkezési folyamat kell használnia a [OpenID Connect](./active-directory-protocols-openid-connect-code.md) vagy [OAuth 2.0](./active-directory-protocols-oauth-code.md) protokollokat. SAML-integráció AppSource hitelesítő el nem fogadott.

## <a name="guides-and-code-samples"></a>Útmutatók és mintakódok
Ha szeretne megtudni arról, hogyan integrálható az Azure Active Directoryval azonosítójával nyissa meg az alkalmazás csatlakozzon, útmutatókat követve, és a Kódminták a [Azure Active Directory fejlesztői útmutatója](./active-directory-developers-guide.md#get-started "Ismerkedés az Azure-ral A fejlesztők számára AD").

## <a name="multi-tenant-applications"></a>Több-bérlős alkalmazásokhoz

A vállalat vagy szervezet rendelkező felhasználók Azure Active Directory anélkül, hogy egy külön példányát, a konfiguráció vagy a központi telepítési bejelentkezések elfogadó alkalmazás is ismert, egy *több-bérlős alkalmazás*. AppSource javasolja, hogy az alkalmazások valósítja meg több-bérlős engedélyezése a *kattintással* ingyenes próbaverziója.

Ahhoz, hogy a több-bérlős az alkalmazásra:
- Beállítása `Multi-Tenanted` tulajdonságot `Yes` a az alkalmazás regisztrációs információit a [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (alapértelmezés szerint az Azure portálon létrehozott alkalmazások értéke, mert *single-bérlő*)
- Frissítse a kódot kérelmek küldése a "`common`" végpont (a végpont frissítése  *https://login.microsoftonline.com/{yourtenant}*  való  *https://login.microsoftonline.com/common* )
- Egyes platformokon, például az ASP.NET is frissíteni kell a kódot több kiállítók fogadásához

Több vállalat kiszolgálása kapcsolatos további információkért lásd: [bármely Azure Active Directory (AD) felhasználó a több-bérlős alkalmazás minta használatával bejelentkezés](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Single-bérlői alkalmazások
Alkalmazások bejelentkezéseket a felhasználóktól egy meghatározott Azure Active Directory-példány csak elfogadó nevezik *single-bérlő alkalmazás*. (Beleértve a munkahelyi vagy iskolai fiókok el más szervezetek vagy személyes fiók) külső felhasználók bejelentkezhetnek a minden felhasználó hozzáadása után a single-bérlői alkalmazások *Vendég fiók* az Azure Active Directory példányt, amely a alkalmazás van regisztrálva. Hozzáadhat felhasználókat Vendég fiókok Azure Active Directory segítségével a [ *Azure AD B2B együttműködés* ](../active-directory-b2b-what-is-azure-ad-b2b.md) -is elvégezhető, és [programozottan](../active-directory-b2b-code-samples.md). Azure Active Directory Vendég fiókként hozzáadni egy felhasználót, meghívó e-mailt küld a felhasználónak, aki a felkérés elfogadásával a meghívó e-mailben szereplő hivatkozásra kattintva. Egy további felhasználó hívja fel a szervezeten, amely tagja a fiókpartner-szervezet küldött meghívót esetén nincs szükség, jelentkezzen be a meghívó elfogadásához.

Single-bérlői alkalmazások engedélyezheti a *forduljon Me* tapasztalhat, de az egy kattintással / ingyenes próbaverziója, amely AppSource azt javasolja, hogy engedélyezni szeretné, ha engedélyezi az alkalmazás a több-bérlős helyette.


## <a name="appsource-trial-experiences"></a>Próba AppSource során lép fel.

### <a name="free-trial-customer-led-trial-experience"></a>Ingyenes próbaverzió (ügyfél-vezetett próbaverziója) 
A *ügyfél által vezetett próbaverzió* a található, amely AppSource azt javasolja, mint az alkalmazás egy kattintással indítható hozzáférést kínál. Az ábra bemutatja, hogyan alatt ez a felület néz ki:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Felhasználói megkeresi az alkalmazást az AppSource webhelyen</li><li>"Ingyenes" beállítás kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource felhasználó a webhely URL-címre irányítja át.</li><li>A webhely elindul a <i>single-sign-on</i> folyamat automatikusan (betöltési)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Felhasználót a rendszer átirányítja a Microsoft bejelentkezési oldalára</li><li>A felhasználó megadja bejelentkezési adatait</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Felhasználó hozzájárulását adja az alkalmazáshoz</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Bejelentkezés befejezése és a felhasználó van átirányítva a webhely</li><li>Felhasználó elindítja az ingyenes próbaverzióval</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Megkereshetnek (Partner által vezetett próbaverziója)
A *próbaverziója partneri* kézi vagy hosszú távú műveletet kell elvégezni a felhasználó kiépítéséhez használni / vállalati: például az alkalmazást kell telepíteni a virtuális gépek, az adatbázis-példány vagy a műveletek hogy mennyi ideig tarthat. Ebben az esetben után a felhasználó kiválasztja a *kérelem próbaverzió* gombra és tölti ki űrlapot, AppSource küld Önnek a felhasználó kapcsolattartási adatait. Ezek az információk fogadását, ezután a környezet kiépítése és utasításokat küldeni a felhasználónak az próbaverziója elérésével:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Felhasználó az alkalmazás AppSource webhelyen talál.</li><li>"Forduljon Me" beállítás kiválasztása</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Kitölti a kapcsolattartási adatokat az űrlap</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Megjelenik a felhasználói adatok</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Környezet beállítása</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Próba információval kapcsolattartási felhasználó</td>
        </tr>
        </table><br/><br/>
        <ul><li>Felhasználói adatokat és telepítő próba példány</li><li>A hivatkozás a felhasználónak az alkalmazás eléréséhez küldi el</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Felhasználó éri el az alkalmazást, és fejezze be a single-sign-on folyamatot</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Felhasználó hozzájárulását adja az alkalmazáshoz</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Bejelentkezés befejezése és a felhasználó van átirányítva a webhely</li><li>Felhasználó elindítja az ingyenes próbaverzióval</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>További információ
Az AppSource próbaverziója kapcsolatos további információkért lásd: [Ez a videó](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>További lépések

- Az Azure Active Directory bejelentkezések támogató alkalmazások további információkért lásd: [az Azure Active Directory hitelesítési forgatókönyvei](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- SaaS alkalmazás szerepeltetése AppSource kapcsolatos információkért látogasson lásd [AppSource partneradatok](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Támogatás kérése
Az Azure Active Directory integráció használjuk [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) támogatásához a Közösséggel. 

Először a Stack Overflow kérje meg a kérdéseket, és keresse meg a már meglévő problémák megjelenítéséhez, ha valaki feltette-e már a kérdését előtt erősen ajánlott. Győződjön meg arról, hogy a kérdéseit vagy megjegyzéseit címkével rendelkező [ `[azure-active-directory]` és `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->