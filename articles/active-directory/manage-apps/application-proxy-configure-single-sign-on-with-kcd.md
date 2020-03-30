---
title: Egyszeri bejelentkezés alkalmazásproxyval | Microsoft dokumentumok
description: Bemutatja, hogyan biztosítható egyszeri bejelentkezés az Azure AD alkalmazásproxy használatával.
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
ms.date: 08/13/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5948fba67d3f071d77192f9ad89bc696fdc0c3cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253454"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos korlátozott delegálása az alkalmazásokba való egyszeri bejelentkezéshez alkalmazásproxyval

Az integrált Windows-hitelesítéssel védett alkalmazásproxyn keresztül közzétett helyszíni alkalmazások hoz is biztosítható. Ezekhez az alkalmazásokhoz Kerberos-jegy szükséges a hozzáféréshez. Az alkalmazásproxy a Kerberos korlátozott delegálás (KCD) segítségével támogatja ezeket az alkalmazásokat. 

Az integrált Windows-hitelesítés (IWA) használatával engedélyezheti az alkalmazásokegyszeri bejelentkezést, ha az Active Directoryban engedélyt ad az alkalmazásproxy-összekötőknek a felhasználók megszemélyesítésére. Az összekötők ezt az engedélyt használják jogkivonatok küldésére és fogadására a nevükben.

## <a name="how-single-sign-on-with-kcd-works"></a>Hogyan működik az egyszeri bejelentkezés a KCD-vel?
Ez az ábra ismerteti a folyamatot, amikor a felhasználó megpróbál hozzáférni egy helyszíni alkalmazás, amely IWA-t használ.

![Microsoft AAD hitelesítési folyamatdiagram](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó adja meg az URL-címet a helyszíni alkalmazás eléréséhez alkalmazásproxyn keresztül.
2. Az alkalmazásproxy átirányítja a kérelmet az Azure AD hitelesítési szolgáltatásaihoz az előzetes hitelesítéshez. Ezen a ponton az Azure AD alkalmazza a vonatkozó hitelesítési és engedélyezési szabályzatok, például a többtényezős hitelesítés. Ha a felhasználó érvényesítve van, az Azure AD létrehoz egy jogkivonatot, és elküldi azt a felhasználónak.
3. A felhasználó átadja a jogkivonatot az alkalmazásproxynak.
4. Az alkalmazásproxy ellenőrzi a jogkivonatot, és lekéri belőle a felhasználó egyszerű nevét (UPN), majd az összekötő lekéri az egyszerű felhasználónevet, és az egyszerű szolgáltatásnevet (SPN) egy kettős hitelesítésű biztonságos csatornán keresztül.
5. Az összekötő kerberos-korlátozott delegálási (KCD) egyeztetést hajt végre a helyszíni AD-vel, megszemélyesítve a felhasználót, hogy Kerberos-jogkivonatot kapjon az alkalmazáshoz.
6. Az Active Directory elküldi az alkalmazás Kerberos-jogkivonatát az összekötőnek.
7. Az összekötő elküldi az eredeti kérelmet az alkalmazáskiszolgálónak az AD-től kapott Kerberos-token használatával.
8. Az alkalmazás elküldi a választ az összekötőnek, amelyezután visszakerül az alkalmazásproxy szolgáltatásnak, és végül a felhasználónak.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdene egyszeri bejelentkezést az IWA-alkalmazásokhoz, győződjön meg arról, hogy a környezet készen áll az alábbi beállításokkal és konfigurációkkal:

* Az alkalmazások, például a SharePoint-webalkalmazások, integrált Windows-hitelesítést használnak. További információt a [Kerberos-hitelesítés támogatásának engedélyezése](https://technet.microsoft.com/library/dd759186.aspx)és a SharePoint része a [Kerberos-hitelesítés megtervezése a SharePoint 2013-ban című témakörben talál.](https://technet.microsoft.com/library/ee806870.aspx)
* Minden alkalmazás egyszerű [szolgáltatásnévvel rendelkezik.](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)
* Az összekötőt futtató kiszolgáló és az alkalmazást futtató kiszolgáló tartományhoz csatlakozik, és ugyanannak a tartománynak vagy megbízható tartománynak a része. A tartományhoz való csatlakozásról a Csatlakozás tartományhoz című [témakörben talál](https://technet.microsoft.com/library/dd807102.aspx)további információt.
* Az összekötőt futtató kiszolgáló rendelkezik hozzáféréssel a TokenGroupsGlobalAndUniversal attribútum olvasásához a felhasználók számára. Ez az alapértelmezett beállítás hatással lehet a környezet biztonsági megerősítésére.

### <a name="configure-active-directory"></a>Az Active Directory konfigurálása
Az Active Directory konfigurációja attól függően változik, hogy az alkalmazásproxy-összekötő és az alkalmazáskiszolgáló ugyanabban a tartományban van-e vagy sem.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Összekötő és alkalmazáskiszolgáló ugyanabban a tartományban
1. Az Active Directory ban nyissa meg **az Eszközök** > **felhasználók és számítógépek**lehetőséget.
2. Jelölje ki az összekötőt futtató kiszolgálót.
3. Kattintson a jobb gombbal, és válassza a Tulajdonságok > **delegálása parancsot.** **Properties**
4. Válassza **A számítógépen csak a megadott szolgáltatások delegálhatók** lehetőséget. 
5. Válassza a **Bármely hitelesítési protokoll** lehetőséget.
6. A **Szolgáltatások csoportban, amelynek ez a fiók delegált hitelesítő adatokat tud bemutatni,** adja hozzá az alkalmazáskiszolgáló SPN-identitásának értékét. Ez lehetővé teszi, hogy az alkalmazásproxy-összekötő megszemélyesítse az AD-ben lévő felhasználókat a listában definiált alkalmazásokkal szemben.

   ![Összekötő-SVR tulajdonságai ablak képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Összekötő és alkalmazáskiszolgáló különböző tartományokban
1. A KCD tartományok közötti együttműködésének előfeltételeit a [Tartományok közötti Kerberos korlátozott delegálás című témakörben található.](https://technet.microsoft.com/library/hh831477.aspx)
2. A `principalsallowedtodelegateto` webalkalmazás szolgáltatásfiókjának (számítógép ének vagy dedikált tartományi felhasználói fiókjának) tulajdonságával engedélyezheti a Kerberos hitelesítésdelegálást az alkalmazásproxyról (összekötő). Az alkalmazáskiszolgáló a környezetében `webserviceaccount` fut, és a `connectorcomputeraccount`delegáló kiszolgáló . Futtassa az alábbi parancsokat egy tartományvezérlőn (Windows Server 2012 `webserviceaccount`R2 vagy újabb rendszert futtatva) a tartományban. Mindkét fiókhoz használjon síkneveket (nem UPN).

   Ha `webserviceaccount` számítógépfiókról van-e itt, használja az alábbi parancsokat:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Ha `webserviceaccount` felhasználói fiók, használja az alábbi parancsokat:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása 
1. Tegye közzé az alkalmazást az [Alkalmazások közzététele alkalmazásproxyval](application-proxy-add-on-premises-application.md)című könyvben leírtaknak megfelelően. Győződjön meg arról, hogy az **Azure Active Directoryt** **választja előhitelesítési módszerként.**
2. Miután az alkalmazás megjelenik a vállalati alkalmazások listájában, jelölje ki, és kattintson **az Egyszeri bejelentkezés gombra.**
3. Állítsa az egyszeri bejelentkezési módot **integrált Windows-hitelesítésre.**  
4. Adja meg az alkalmazáskiszolgáló **belső alkalmazás-spelét.** Ebben a példában a közzétett alkalmazás spn http/www.contoso.com. Ennek az egész szolgáltatásnévnek szerepelnie kell azon szolgáltatások listájában, amelyekhez az összekötő delegált hitelesítő adatokat tud bemutatni. 
5. Válassza ki a **delegált bejelentkezési identitást** az összekötő számára a felhasználók nevében használandó. További információ: [A különböző helyszíni és felhőalapú identitások megtekintése](#working-with-different-on-premises-and-cloud-identities)

   ![Speciális alkalmazáskonfiguráció](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Egyszeri szolgáltatás nem Windows-alkalmazásokhoz

A Kerberos-delegálási folyamat az Azure AD alkalmazásproxy ban akkor kezdődik, amikor az Azure AD hitelesíti a felhasználót a felhőben. Miután a kérelem megérkezik a helyszínen, az Azure AD alkalmazásproxy-összekötő kerberos-jegyet ad ki a felhasználó nevében a helyi Active Directoryval való együttműködéssel. Ezt a folyamatot Kerberos korlátozott delegálásnak (KCD) nevezik. A következő fázisban a rendszer egy kérelmet küld a háttéralkalmazásnak ezzel a Kerberos-jeggyel. 

Számos protokoll határozza meg az ilyen kérések küldésének módját. A legtöbb nem Windows rendszerű kiszolgáló az SPNEGO-val szeretne tárgyalni. Ez a protokoll támogatja az Azure AD alkalmazásproxy, de alapértelmezés szerint le van tiltva. A kiszolgáló spnego-hoz vagy szabványos KCD-hez konfigurálható, de mindkettőre nem.

Ha egy csatlakozógépet konfigurál az SPNEGO-hoz, győződjön meg arról, hogy az adott összekötő csoport összes többi csatlakozója is konfigurálva van az SPNEGO-val. A szabványos KCD-t váró alkalmazásokat más, SPNEGO-hoz nem konfigurált csatlakozókon keresztül kell irányítani.
 

A SPNEGO engedélyezése:

1. Nyisson meg egy rendszergazdaként futó parancssort.
2. A parancssorból futtassa a következő parancsokat az spNEGO-t igénylő összekötő kiszolgálókon.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

A Kerberosról további információt a [Kerberos korlátozott delegálásról (KCD) szeretne tudni.](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)

A nem Windows-alkalmazások általában felhasználóneveket vagy SAM-fiókneveket neveznek meg a tartományi e-mail címek helyett. Ha ez a helyzet vonatkozik az alkalmazásokra, konfigurálnia kell a delegált bejelentkezési identitásmezőt, hogy a felhőidentitások az alkalmazás identitásokhoz kapcsolódjanak. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Különböző helyszíni és felhőalapú identitások közös munkálatai
Az alkalmazásproxy feltételezi, hogy a felhasználók pontosan ugyanazzal az identitással rendelkeznek a felhőben és a helyszíni környezetben. Bizonyos környezetekben azonban a vállalati házirendek vagy az alkalmazásfüggőségek miatt előfordulhat, hogy a szervezeteknek alternatív azonosítókat kell használniuk a bejelentkezéshez. Ilyen esetekben továbbra is használhatja a KCD egyszeri bejelentkezéshez. Konfiguráljon **delegált bejelentkezési identitást** minden alkalmazáshoz, és adja meg, hogy melyik identitást kell használni az egyszeri bejelentkezéshez.  

Ez a funkció lehetővé teszi, hogy számos szervezet, amely különböző helyszíni és felhőalapú identitások, hogy egyszeri bejelentkezés a felhőből a helyszíni alkalmazások anélkül, hogy a felhasználók különböző felhasználóneveket és jelszavakat. Ide tartoznak azok a szervezetek is, amelyek:

* Több tartomány belső (joe@us.contoso.com joe@eu.contoso.com, ) és egy tartományjoe@contoso.coma felhőben ( ).
* A nem irányítható tartománynévjoe@contoso.usabelső ( ) és egy jogi a felhőben.
* Ne használjon tartományneveket belsőleg (joe)
* Különböző aliasokat használhat a helyszínen és a felhőben. Például, joe-johns@contoso.com vs.joej@contoso.com  

Az alkalmazásproxyval kiválaszthatja, hogy melyik identitást használja a Kerberos-jegy megszerzéséhez. Ez a beállítás alkalmazásonként. Néhány ilyen lehetőség alkalmas rendszerek, amelyek nem fogadják el az e-mail cím formátum, mások célja az alternatív bejelentkezés.

![Delegált bejelentkezési azonosító paraméter képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Delegált bejelentkezési identitás esetén előfordulhat, hogy az érték nem egyedi a szervezet összes tartományában vagy erdőjében. Ezt a problémát úgy kerülheti el, hogy két különböző összekötőcsoport használatával kétszer közzéteszi ezeket az alkalmazásokat. Mivel minden alkalmazás más felhasználói közönséggel rendelkezik, az összekötők egy másik tartományhoz csatlakozhat.

### <a name="configure-sso-for-different-identities"></a>Az SSO konfigurálása különböző identitásokhoz
1. Konfigurálja az Azure AD Connect beállításait, hogy a fő identitás az e-mail cím (e-mail). Ez a testreszabási folyamat részeként történik, a szinkronizálási beállítások **egyszerű felhasználónév** mezőjének módosításával. Ezek a beállítások azt is meghatározzák, hogy a felhasználók hogyan jelentkezzenek be az Office365-be, a Windows10-eszközökbe és más alkalmazásokba, amelyek az Azure AD-t használják identitástárolóként.  
   ![Felhasználók azonosítása képernyőkép – Egyszerű felhasználónév legördülő menü](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. A módosítani kívánt alkalmazás konfigurációs beállításai között válassza ki a használni kívánt **delegált bejelentkezési identitást:**

   * Egyszerű felhasználónév (például) joe@contoso.com
   * Alternatív egyszerű felhasználónév (például joed@contoso.local)
   * Az egyszerű felhasználónév része (például joe)
   * Az Alternatív egyszerű felhasználónév felhasználónév része (például joed)
   * Helyszíni SAM-fiók neve (a tartományvezérlő konfigurációjától függ)

### <a name="troubleshooting-sso-for-different-identities"></a>Az SSO hibáinak elhárítása a különböző identitások esetén
Ha hiba történik az SSO-folyamatban, az megjelenik az összekötő gép eseménynaplójában a [hibaelhárítás magyarázata szerint.](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
De bizonyos esetekben a kérelem sikeresen elküldve a háttéralkalmazásnak, miközben ez az alkalmazás válaszol a különböző más HTTP-válaszok. Az esetek hibaelhárítása az alkalmazásproxy-munkamenet eseménynaplójában az összekötő gépen lévő 24029-es eseményszám vizsgálatával kezdődhet. A delegáláshoz használt felhasználói identitás az esemény részleteinek "felhasználó" mezőjében jelenik meg. A munkamenetnapló bekapcsolásához válassza az Eseménynapló nézet menüjében az **Analitikus és hibakeresési naplók megjelenítése** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Alkalmazásproxy-alkalmazás konfigurálása a Kerberos korlátozott delegáláshasználatára](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)


A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](https://blogs.technet.com/b/applicationproxyblog/).
