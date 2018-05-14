---
title: Egyszeri bejelentkezés az alkalmazásproxy |} Microsoft Docs
description: Bemutatja, hogyan adhat meg az egyszeri bejelentkezés az Azure AD-alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 506ff0bce0b68b1477f27f913bd3fe119e36cca1
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos által korlátozott delegálás az egyszeri bejelentkezést az alkalmazásokba a Proxy

Egyszeri bejelentkezés biztosítható a helyszíni alkalmazások proxyn keresztül történő titkosított integrált Windows-hitelesítésre van közzétéve. Ezek az alkalmazások a Kerberos jegy megkövetelése a hozzáféréshez. Alkalmazásproxy Kerberos által korlátozott delegálás (KCD) használ, ezek az alkalmazások támogatásához. 

Egyszeri bejelentkezés az integrált Windows-hitelesítéssel (IWA) használata az Active Directory alkalmazásproxyt összekötők engedélyt adjon számára felhasználók megszemélyesítését az alkalmazások engedélyezése. Az összekötők ezt az engedélyt küldésére és fogadására a nevében jogkivonatok használja.

## <a name="how-single-sign-on-with-kcd-works"></a>Hogyan egyszeri bejelentkezéshez a Kerberos által korlátozott Delegálás works
Ezen a diagramon ismerteti a folyamatot, amikor egy felhasználó megpróbál elérni az integrált Windows-Hitelesítést használó helyszíni alkalmazások.

![Microsoft AAD hitelesítési folyamatábrája](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó megadja az alkalmazáshoz való hozzáférés – helyszíni proxyn keresztül történő URL-CÍMÉT.
2. Az Azure AD authentication szolgáltatások tudnak preauthenticate alkalmazásproxy átirányítja a kérést. Ezen a ponton az Azure AD alkalmazza a megfelelő hitelesítési és engedélyezési házirendeket, mint a többtényezős hitelesítés. Ha a felhasználó érvényesítését, az Azure AD létrehoz egy tokent, és elküldi a felhasználó.
3. A felhasználó alkalmazásproxy továbbítja a jogkivonatot.
4. Alkalmazásproxy érvényesíti a jogkivonatot, és az egyszerű felhasználónév (UPN) kikeresi, és ezután elküldi a kérelmet, az egyszerű Felhasználónevet és az egyszerű szolgáltatásnév (SPN) az összekötő egy dually hitelesített biztonságos csatornán keresztül.
5. Az összekötő hajtja végre a Kerberos által korlátozott delegálás (KCD)-egyeztetést a helyszíni Active Directory, a felhasználó Kerberos-jogkivonata az alkalmazás eléréséhez.
6. Az Active Directory elküldi az alkalmazás és az összekötő Kerberos-jogkivonata.
7. Az összekötő az eredeti kérés küldése a kiszolgáló, a Kerberos-tokent AD-ből kapott.
8. Az alkalmazás visszaküldi a választ, és az összekötő, amely ezután visszaérkezik az alkalmazásproxy-szolgáltatás, és végül a felhasználó számára.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzáfogna, az egyszeri bejelentkezés alkalmazásokhoz integrált Windows-Hitelesítést, győződjön meg arról, hogy a környezet készen áll a következő beállításokat, és a konfigurációkat:

* Az alkalmazások, például a SharePoint Web apps, integrált Windows-hitelesítés használatára van beállítva. További információkért lásd: [Kerberos-hitelesítés támogatásának engedélyezése](https://technet.microsoft.com/library/dd759186.aspx), vagy a SharePoint lásd [a SharePoint 2013 Kerberos-hitelesítés tervezése](https://technet.microsoft.com/library/ee806870.aspx).
* Minden alkalmazás rendelkezik [egyszerű szolgáltatásnevek](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Az összekötőt futtató kiszolgáló és az alkalmazást futtató kiszolgáló tartományhoz csatlakoztatott és részei a ugyanabban a tartományban vagy egymásban megbízó tartományokban. A tartományhoz való csatlakozást további információkért lásd: [egy számítógép csatlakozik egy tartományhoz](https://technet.microsoft.com/library/dd807102.aspx).
* Az összekötő szolgáltatást futtató kiszolgáló rendelkezik az értékűnek attribútum a felhasználók számára olvasási hozzáférést. Ez az alapértelmezett beállítás előfordulhat, hogy rendelkezik lett által érintett biztonsági korlátozására a környezetben.

### <a name="configure-active-directory"></a>Az Active Directory konfigurálása
Az Active Directory-konfiguráció a tárolóhardverektől függően eltérő az alkalmazásproxy-összekötő és az alkalmazáskiszolgáló csatlakoznak-e ugyanabban a tartományban vagy sem.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Összekötő és az alkalmazáskiszolgáló ugyanabban a tartományban
1. Az Active Directoryban, Ugrás **eszközök** > **felhasználók és számítógépek**.
2. Válassza ki az összekötőt futtató kiszolgálót.
3. Kattintson a jobb gombbal, és válassza ki **tulajdonságok** > **delegálás**.
4. Válassza ki **a számítógépen csak a megadott szolgáltatások delegálhatók**. 
5. A **szolgáltatásokat, amelyhez ezt a fiókot használhat delegált hitelesítő adatokat** vegye fel az alkalmazáskiszolgáló SPN-identitásnak értékét. Ez lehetővé teszi az alkalmazásproxy-összekötő számára felhasználók megszemélyesítését az ad-ben a a listában megadott alkalmazások ellen.

   ![Összekötő-SVR tulajdonságai ablakban képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Összekötő és eltérő tartományokban alkalmazáskiszolgáló
1. A tartományok közötti Kerberos által korlátozott Delegálás végzett munka Előfeltételek listájáért lásd: [tartományok közötti Kerberos általi korlátozott delegálás](https://technet.microsoft.com/library/hh831477.aspx).
2. Használja a `principalsallowedtodelegateto` az ahhoz, hogy az összekötő-kiszolgáló delegálása az alkalmazásproxy-összekötő kiszolgálója tulajdonságát. Az alkalmazáskiszolgáló `sharepointserviceaccount` és a felhatalmazó kiszolgáló `connectormachineaccount`. Windows 2012 R2-ben például ezt a kódot használja:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

A szervizcsomag-verzió vagy egy szolgáltatás fiókjának alatt a szervizcsomag-verzió alkalmazáskészlet fut. Sharepointserviceaccount lehet.

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása 
1. Az ismertetett utasításoknak megfelelően az alkalmazás közzétételére [alkalmazások közzétételére az alkalmazásproxy](application-proxy-publish-azure-portal.md). Ügyeljen arra, hogy válasszon **Azure Active Directory** , a **előhitelesítési módszer**.
2. Miután az alkalmazás megjelenik a vállalati alkalmazások listájában, válassza ki azt, majd kattintson a **egyszeri bejelentkezés**.
3. Az egyszeri bejelentkezés mód beállítása legyen **integrált Windows-hitelesítés**.  
4. Adja meg a **belső alkalmazás SPN** az alkalmazáskiszolgáló. Ebben a példában a közzétett alkalmazáshoz az egyszerű Szolgáltatásnevet a http/www.contoso.com. Ezt az SPN kell lennie a szolgáltatások, amelyhez az összekötőt használhat delegált hitelesítő adatokat. 
5. Válassza ki a **meghatalmazott bejelentkezési identitás** az összekötő használatára, a felhasználók nevében. További információkért lásd: [különböző helyszíni és a felhőbeli identitások használata](#Working-with-different-on-premises-and-cloud-identities)

   ![Speciális alkalmazás konfigurációja](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Egyszeri bejelentkezés a Windows alkalmazások
A Kerberos-delegálás folyamata a Azure AD alkalmazásproxy kezdődik, amikor az Azure AD akkor hitelesíti a felhasználót, a felhőben. A kérelem érkezik a helyszínen, ha az Azure AD alkalmazásproxy-összekötő által a helyi Active Directoryban való interakció állít ki a Kerberos jegyet a felhasználó nevében. A folyamatnak a neve, a Kerberos által korlátozott delegálás (KCD). A következő szakaszban kérelmet küldeni a Kerberos jegyet a háttér alkalmazást. Nincsenek több protokollok, amelyek meghatározzák, hogyan küldhet az ilyen kérelmeket. A legtöbb-Windows kiszolgálók várt Negotiate/SPNego mostantól támogatott az Azure AD-alkalmazásproxyval oldható meg.

Kerberos kapcsolatos további információkért lásd: [összes meg szeretné ismerni a kapcsolatos a Kerberos által korlátozott delegálás (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Nem Windows-alkalmazások általában felhasználói felhasználónevek vagy SAM fióknevek tartomány helyett e-mail címet. Ha ilyen esetben a alkalmazásra vonatkozik, akkor a delegált bejelentkezési azonosító mezőjét a felhőbeli identitások csatlakozni a alkalmazás identitások konfigurálása. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>A különböző helyszíni és felhőbeli identitások
Alkalmazásproxy feltételezi, hogy a felhasználók pontosan a ugyanazzal az identitással a felhőben és helyszíni rendelkeznek. Ha nem, a helyzet, az egyszeri bejelentkezés továbbra is használhatja a Kerberos által korlátozott Delegálás. Konfigurálja a **bejelentkezési identitás meghatalmazott** minden alkalmazáshoz külön adja meg, melyik identitás használható egyszeri bejelentkezéshez végrehajtása során.  

Ez a funkció lehetővé teszi, hogy számos olyan szervezeteknek, amelyek különböző helyszíni és felhőbeli identitások tartoznia egyszeri Bejelentkezést a felhőből a helyszíni alkalmazások anélkül, hogy a felhasználók különböző felhasználóneveket és jelszavakat adjon meg. Ez magában foglalja a szervezetek, amelyek:

* Belső több tartományban vannak (joe@us.contoso.com, joe@eu.contoso.com) és a tartományra, a felhőben (joe@contoso.com).
* Belső nem átirányítható tartomány neve lehet (joe@contoso.usa) és jogi egy, a felhőben.
* Nem használható tartománynevek belső (joe)
* Használjon különböző aliasnevet a helyszínen és a felhőben. Például joe-johns@contoso.com vs. joej@contoso.com  

A Proxy válassza ki a Kerberos-jegyet használandó mely identitás. Ez a beállítás akkor alkalmazásonként. A beállítások egy része, amely nem fogadja el az e-mail cím formátumú rendszerek alkalmas, mások számára készültek, alternatív bejelentkezési.

![Delegált bejelentkezési identitás paraméter képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Delegált bejelentkezési azonosítót használja, ha az érték nem feltétlenül egyedi a tartományok és erdők a szervezet között. Ezt a problémát elkerülheti, ha a ezek kétszer csoportokkal két különböző összekötő alkalmazás-közzététel. Minden alkalmazás rendelkezik egy másik felhasználó célközönség, mert egy másik tartományba csatlakozhat az összekötők.

### <a name="configure-sso-for-different-identities"></a>Egyszeri bejelentkezés konfigurálása különböző identitások
1. Adja meg az Azure AD Connect beállításait, a fő identitás az az e-mail cím (mail). Ehhez a Testreszabás folyamat részeként módosítása a **egyszerű felhasználónév** mező mellett a szinkronizálási beállításokat. Ezek a beállítások is meghatározzák milyen felhasználói bejelentkezés az Office365, Windows10 eszközök, és más alkalmazások, amelyek az Azure AD az identitás-tárolóként.  
   ![Felhasználók képernyőfelvétel - egyszerű felhasználónév legördülő azonosítása](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Az alkalmazás konfigurációs beállításait módosítani szeretné az alkalmazást, válassza ki a **meghatalmazott bejelentkezési identitás** használható:

   * Egyszerű felhasználónév (például joe@contoso.com)
   * Alternatív egyszerű felhasználónév (például joed@contoso.local)
   * Egyszerű felhasználónév (például joe) felhasználónév részét
   * Felhasználónév részét alternatív egyszerű felhasználónév (például joed)
   * A helyi SAM-neve (a tartományvezérlő konfigurációja függ)

### <a name="troubleshooting-sso-for-different-identities"></a>Hibaelhárítási SSO különböző identitások
Ha nem sikerül az egyszeri bejelentkezés folyamatban, megjelenik az összekötő gép eseménynaplójában keresse meg a [hibaelhárítás](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Azonban bizonyos esetekben a kérelem sikeresen továbbítódik a háttéralkalmazás amíg az alkalmazás ügyfélkéréseire válaszol, a különböző HTTP-válaszok. Ezekben az esetekben hibaelhárítási eseményszám 24029 az alkalmazásproxy munkamenet eseménynaplóban összekötő gépen megvizsgálásával kell kezdődnie. A felhasználói identitás delegálásához használt megjelenik a "user" mezőben belül az esemény részleteit. Munkamenet napló bekapcsolásához válassza **megjelenítése elemzési és hibakeresési naplókat** a esemény megjelenítő Nézet menüben.

## <a name="next-steps"></a>További lépések

* [Az alkalmazásproxy alkalmazás Kerberos által korlátozott delegálás konfigurálása](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Az alkalmazásproxy problémák elhárítása](../active-directory-application-proxy-troubleshoot.md)


A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).

