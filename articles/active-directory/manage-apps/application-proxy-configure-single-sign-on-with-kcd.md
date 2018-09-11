---
title: Egyszeri bejelentkezés alkalmazásproxyval |} A Microsoft Docs
description: Bemutatja, hogyan biztosít egyszeri bejelentkezés az Azure AD-alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 1178d85f295c6ac01f367db8adc2c9b855cdc829
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347831"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos által korlátozott delegálás az egyszeri bejelentkezést az alkalmazásokba az alkalmazásproxy használatával

Egyszeri bejelentkezés biztosíthatja a helyszíni Application Proxy védett integrált Windows-hitelesítés és a közzétett alkalmazásokhoz. Ezeket az alkalmazásokat egy Kerberos-jegyet megkövetelése a hozzáféréshez. Alkalmazásproxy ezeket az alkalmazásokat támogatja a Kerberos által korlátozott delegálás (KCD) használja. 

Az alkalmazásproxy-összekötők jogosultság, így az Active Directory integrált Windows-hitelesítés (IWA) használatával a felhasználók megszemélyesítésére alkalmazások engedélyezheti az egyszeri bejelentkezés. Az összekötők küldjön és fogadjon a tokeneket a felhasználók nevében használja ezt az engedélyt.

## <a name="how-single-sign-on-with-kcd-works"></a>Az egyszeri bejelentkezés KCD Works
Ez az ábra ismerteti a folyamatot, amikor egy felhasználó megpróbál hozzáférni IWA használó helyszíni alkalmazások.

![A Microsoft AAD-hitelesítés folyamatábrája](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó megadja az URL-cím alkalmazásproxyn keresztül a helyszíni alkalmazás eléréséhez.
2. Az alkalmazásproxy átirányítja a kérést az Azure AD authentication szolgáltatások tudnak preauthenticate. Ezen a ponton az Azure AD bármilyen megfelelő hitelesítési és engedélyezési házirendeket, mint a többtényezős hitelesítés vonatkozik. Ha a felhasználó érvényesítését, Azure ad-ben létrehoz egy jogkivonatot, és elküldi azokat a felhasználó.
3. A felhasználó az alkalmazásproxy továbbítja a jogkivonatot.
4. Proxy érvényesíti a jogkivonatot, és az egyszerű felhasználónév (UPN) beolvassa és és továbbítja a kérést, az egyszerű Felhasználónevet és az egyszerű szolgáltatásnév (SPN) az összekötő dually hitelesített biztonságos csatornán keresztül történik.
5. Az összekötő végez egyeztetést Kerberos által korlátozott delegálás (KCD) a helyszíni Active Directory, az alkalmazásnak a Kerberos jogkivonat beszerzéséhez a felhasználó megszemélyesítésekor.
6. Az Active Directory elküldi az alkalmazás az összekötő Kerberos-jogkivonat.
7. Az összekötő az eredeti kérés küldése az application server, az AD-ből kapott Kerberos-jogkivonat használatával.
8. Az alkalmazás visszaküldi a választ, az összekötőre, amelyet ezután visszaküld az alkalmazásproxy-szolgáltatás, és végül a felhasználó számára.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, az egyszeri bejelentkezés IWA alkalmazások számára, győződjön meg arról, hogy a környezet készen áll a következő beállításokat és konfigurációkat:

* Az alkalmazások, mint például a SharePoint-webhely, az integrált Windows-hitelesítés használatára vannak beállítva. További információkért lásd: [Kerberos-hitelesítés támogatásának engedélyezése](https://technet.microsoft.com/library/dd759186.aspx), vagy a SharePoint lásd [a SharePoint 2013-ban a Kerberos-hitelesítés tervezése](https://technet.microsoft.com/library/ee806870.aspx).
* Minden alkalmazás rendelkezik [egyszerű szolgáltatásnevek](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Az összekötő szolgáltatást futtató kiszolgáló és az alkalmazást futtató kiszolgáló olyan tartományhoz csatlakoztatott és a ugyanabban a tartományban vagy megbízható tartomány része. A tartományhoz való csatlakozás további információkért lásd: [egy számítógép csatlakozik egy tartományhoz](https://technet.microsoft.com/library/dd807102.aspx).
* Az összekötő szolgáltatást futtató kiszolgáló hozzáfér a értékűnek attribútum olvassa el a felhasználók számára. Ez az alapértelmezett beállítás előfordulhat, hogy rendelkezik lett által érintett biztonsági korlátozások a környezetben.

### <a name="configure-active-directory"></a>Az Active Directory konfigurálása
Az Active Directory konfigurálása az Application Proxy connector és az alkalmazáskiszolgáló vannak-e ugyanabban a tartományban, vagy nem függően változik.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Összekötő és alkalmazás-kiszolgáló ugyanabban a tartományban
1. Az Active Directoryban, Ugrás **eszközök** > **felhasználók és számítógépek**.
2. Válassza ki azt az összekötőt futtató kiszolgálón.
3. Kattintson a jobb gombbal, és válassza ki **tulajdonságok** > **delegálás**.
4. Válassza ki **számítógépen csak a megadott szolgáltatások delegálhatók**. 
5. A **szolgáltatásokhoz, amelyhez ezt a fiókot használhat delegált hitelesítő adatokat** adjon meg az értéket a kiszolgáló egyszerű Szolgáltatásnevének identitását. Ez lehetővé teszi a felhasználók megszemélyesítésére az ad-ben az olyan alkalmazások, a listában megadott az alkalmazásproxy-összekötő.

   ![Összekötő – fenntartott példányok tulajdonságok ablak képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Összekötő és az alkalmazáskiszolgáló eltérő tartományokban
1. Tartományok közötti Kerberos használatának előfeltételeit, lásd: [tartományok közötti Kerberos általi korlátozott delegálás](https://technet.microsoft.com/library/hh831477.aspx).
2. Használja a `principalsallowedtodelegateto` tulajdonsága az engedélyezése a Connector-kiszolgáló delegálása az alkalmazásproxy-összekötő kiszolgálója. Az alkalmazáskiszolgáló `sharepointserviceaccount` és a felhatalmazó kiszolgáló `connectormachineaccount`. Windows 2012 R2 használata esetén ezt a kódot használja példaként:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount a Szervizcsomagok számítógép fiókja vagy egy szolgáltatás a Szervizcsomagok alkalmazáskészlet van futtató fiók is lehet.

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása 
1. Közzéteheti az alkalmazását a szakaszban ismertetett utasításoknak megfelelően [alkalmazásait közzéteheti az alkalmazásproxy](application-proxy-publish-azure-portal.md). Ügyeljen arra, hogy válassza ki, hogy **Azure Active Directory** , a **előhitelesítést metódus**.
2. Miután az alkalmazás megjelenik a vállalati alkalmazások listájában, válassza ki, majd kattintson a **egyszeri bejelentkezési**.
3. Az egyszeri bejelentkezési mód beállítása legyen **integrált Windows-hitelesítés**.  
4. Adja meg a **belső alkalmazás egyszerű Szolgáltatásnevét** az alkalmazáskiszolgáló. Ebben a példában a közzétett alkalmazás egyszerű Szolgáltatásnevét http/www.contoso.com. Ezt az SPN kell lennie, amelyhez az összekötőt használhat delegált hitelesítő adatokat a szolgáltatások listájában. 
5. Válassza ki a **delegált bejelentkezési azonosító** az összekötő használatára a felhasználó nevében. További információkért lásd: [különböző helyszíni és felhőbeli identitások használata](#Working-with-different-on-premises-and-cloud-identities)

   ![Speciális alkalmazás konfigurációja](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Egyszeri bejelentkezés nem Windows-alkalmazások

A Kerberos-delegálás folyamat az Azure AD-alkalmazásproxy akkor kezdődik, amikor az Azure AD akkor hitelesíti a felhasználót a felhőben. A kérelem érkezik a helyszíni, miután az Azure AD-alkalmazásproxy-összekötő szerint a helyi Active Directoryval folytatott bocsát ki egy Kerberos-jegyet a felhasználó nevében. Ez a folyamatnak a neve, a Kerberos által korlátozott delegálás (KCD). A következő szakaszban kérelmet küldött a a Kerberos-jegyet a háttéralkalmazás. 

Nincsenek több protokollok, amelyek meghatározzák az ilyen kérések küldése. A legtöbb nem Windows-kiszolgálók várhatóan SPNEGO egyeztetni. Ez a protokoll támogatott az Azure AD-alkalmazásproxy, de alapértelmezés szerint le van tiltva. Egy kiszolgáló SPNEGO vagy standard szintű kcd Szolgáltatáshoz konfigurált, de nem mindkettőn is lehet.

Ha konfigurál egy összekötőt gép SPNEGO számára, ügyeljen arra, hogy az adott összekötő csoportban minden más összekötők is állítottak SPNEGO. Standard KCD várt alkalmazásokat, amelyek nincsenek beállítva SPNEGO más összekötőkön keresztül kell átirányítani.
 

SPNEGO engedélyezése:

1. Nyisson meg egy parancssort, hogy rendszergazdaként futtatja.
2. A parancssorból futtassa az alábbi parancsokat a SPNEGO igénylő összekötő-kiszolgálók.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

A Kerberos kapcsolatos további információkért lásd: [összes érdemes figyelembe venni a kapcsolatos a Kerberos által korlátozott delegálás (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Nem-Windows-alkalmazások általában felhasználói felhasználónevek vagy a SAM-fiókok nevét tartomány helyett e-mail-címek. Ilyen esetekben az alkalmazások vonatkozik, ha szüksége konfigurálja a felhőbeli identitások csatlakozni a alkalmazás identitások delegált bejelentkezési azonosító mezőjét. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>A különböző helyszíni és felhőbeli identitások
Az alkalmazásproxy azt feltételezi, hogy a felhasználók pontosan ugyanaz az identitás a felhőben és a helyszínen rendelkeznek. Ha nem, amely a helyzet, az egyszeri bejelentkezés továbbra is használhatja a kcd Szolgáltatáshoz. Konfigurálja a **delegált bejelentkezési azonosító** minden alkalmazáshoz, adja meg, melyik identitás használata, ha egyszeri bejelentkezést hajt végre.  

Ez a funkció lehetővé teszi, hogy számos különböző helyszíni és felhőalapú Identitások kell SSO a felhőből helyszíni alkalmazásokat anélkül, hogy a felhasználók különböző felhasználónevek és jelszavak rendelkező szervezeteknek. Ez magában foglalja a szervezetek, amelyek:

* Több tartományom van belső használatra (joe@us.contoso.com, joe@eu.contoso.com) és a egy egyetlen tartományt a felhőben (joe@contoso.com).
* Belső használatra van nem átirányítható tartománynevet (joe@contoso.usa) és a egy jogi megjegyzés a felhőben.
* Belsőleg nem használja a tartománynevek (joe)
* Használjon különböző aliasnevet a helyszíni és a felhőben. Ha például joe-johns@contoso.com vs. joej@contoso.com  

A proxyval kiválaszthatja, melyik identitás a Kerberos-jegy beszerzéséhez használhatnak. Ez a beállítás akkor alkalmazásonként. Ezek a beállítások némelyike rendszerek, amelyek nem fogadja el az e-mail cím formátuma megfelelő, mások alternatív bejelentkezési lettek kialakítva.

![Delegált bejelentkezési azonosító paraméter képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Delegált bejelentkezési azonosító használata esetén az érték nem egyedi a tartományok és erdők a szervezet között. A probléma elkerüléséhez tegye közzé kétszer használatával két különböző összekötőcsoportok ezeket az alkalmazásokat. Minden alkalmazás rendelkezik egy másik felhasználói közönség számára, mivel az összekötők csatlakozhat egy másik tartományba.

### <a name="configure-sso-for-different-identities"></a>Egyszeri bejelentkezés konfigurálása a különböző identitások
1. Az Azure AD Connect-beállítások konfigurálása, így a fő identitását az e-mail-cím (levelezés). Ez történik, a Testreszabás folyamat részeként módosításával a **egyszerű felhasználónév** mezőbe a szinkronizálási beállításokat. Ezek a beállítások is határozzák hogyan való felhasználói bejelentkezés Office 365, Windows 10-es eszközök, és egyéb alkalmazásokhoz, amelyek az Azure AD használatára az ügyfélidentitás-tárolóval.  
   ![Felhasználók képernyőkép – egyszerű felhasználónév legördülő azonosítása](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Az alkalmazás konfigurációs beállításait módosítani szeretné az alkalmazást, válassza ki a **delegált bejelentkezési azonosító** használható:

   * Egyszerű felhasználónév (például joe@contoso.com)
   * Alternatív egyszerű felhasználónév (például joed@contoso.local)
   * (Például joe) egyszerű felhasználónév felhasználónév része
   * Alternatív egyszerű felhasználónév (például joed) felhasználónév része
   * A helyi SAM-fiók neve (a tartományvezérlő konfigurációja függ)

### <a name="troubleshooting-sso-for-different-identities"></a>Egyszeri bejelentkezés hibaelhárítása a különböző identitások
Ha hiba van az egyszeri bejelentkezési folyamat, megjelenik az összekötő gép eseménynaplóban leírtak [hibaelhárítás](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
De egyes esetekben a sikeresen irányuló kérést küld a háttéralkalmazás az alkalmazás válaszol a különböző HTTP-válaszok során. Ezekben az esetekben hibaelhárítási el kell indítaniuk az alkalmazásproxy-munkamenet eseménynaplóban összekötő gépen 24029 eseményazonosítóval megvizsgálásával. A felhasználói identitás delegálásához használt belül az esemény részleteinek a "user" mezőjében jelenik meg. Munkamenet napló bekapcsolásához válassza **megjelenítése elemzési és hibakeresési naplók** a eseményt megjelenítő Nézet menü.

## <a name="next-steps"></a>További lépések

* [Az Application Proxy-alkalmazások használata a Kerberos által korlátozott delegálás konfigurálása](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Problémák merültek fel az alkalmazásproxy használatával](application-proxy-troubleshoot.md)


A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).

