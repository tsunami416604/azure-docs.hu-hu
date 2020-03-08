---
title: Egyszeri bejelentkezés alkalmazásproxyval |} A Microsoft Docs
description: Bemutatja, hogyan biztosít egyszeri bejelentkezés az Azure AD-alkalmazásproxy használatával.
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
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668855"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos által korlátozott delegálás az egyszeri bejelentkezést az alkalmazásokba az alkalmazásproxy használatával

Egyszeri bejelentkezés biztosíthatja a helyszíni Application Proxy védett integrált Windows-hitelesítés és a közzétett alkalmazásokhoz. Ezeket az alkalmazásokat egy Kerberos-jegyet megkövetelése a hozzáféréshez. Alkalmazásproxy ezeket az alkalmazásokat támogatja a Kerberos által korlátozott delegálás (KCD) használja. 

Az alkalmazásproxy-összekötők jogosultság, így az Active Directory integrált Windows-hitelesítés (IWA) használatával a felhasználók megszemélyesítésére alkalmazások engedélyezheti az egyszeri bejelentkezés. Az összekötők küldjön és fogadjon a tokeneket a felhasználók nevében használja ezt az engedélyt.

## <a name="how-single-sign-on-with-kcd-works"></a>Az egyszeri bejelentkezés KCD Works
Ez a diagram ismerteti a folyamatot, amikor egy felhasználó egy IWA-t használó helyszíni alkalmazáshoz próbál hozzáférni.

![A Microsoft AAD-hitelesítés folyamatábrája](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. A felhasználó beírja az URL-címet a helyszíni alkalmazás alkalmazás-proxyn keresztüli eléréséhez.
2. Az alkalmazásproxy átirányítja a kérést az Azure AD authentication szolgáltatások tudnak preauthenticate. Ezen a ponton az Azure AD bármilyen megfelelő hitelesítési és engedélyezési házirendeket, mint a többtényezős hitelesítés vonatkozik. Ha a felhasználó érvényesítését, Azure ad-ben létrehoz egy jogkivonatot, és elküldi azokat a felhasználó.
3. A felhasználó az alkalmazásproxy továbbítja a jogkivonatot.
4. Az alkalmazásproxy érvényesíti a jogkivonatot, és lekérdezi az egyszerű felhasználónevet (UPN), majd az összekötő lekéri az egyszerű felhasználónevet és az egyszerű szolgáltatásnevet (SPN) egy kettős hitelesítésű biztonságos csatornán keresztül.
5. Az összekötő Kerberos által korlátozott delegálás (KCD) egyeztetést végez a helyszíni AD-vel, megszemélyesítve a felhasználót, hogy Kerberos-jogkivonatot kapjon az alkalmazáshoz.
6. Az Active Directory elküldi az alkalmazás az összekötő Kerberos-jogkivonat.
7. Az összekötő az eredeti kérés küldése az application server, az AD-ből kapott Kerberos-jogkivonat használatával.
8. Az alkalmazás visszaküldi a választ, az összekötőre, amelyet ezután visszaküld az alkalmazásproxy-szolgáltatás, és végül a felhasználó számára.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, az egyszeri bejelentkezés IWA alkalmazások számára, győződjön meg arról, hogy a környezet készen áll a következő beállításokat és konfigurációkat:

* Az alkalmazások, mint például a SharePoint-webhely, az integrált Windows-hitelesítés használatára vannak beállítva. További információkért lásd: a [Kerberos-hitelesítés támogatásának engedélyezése](https://technet.microsoft.com/library/dd759186.aspx)vagy a sharepointhoz – lásd: a [Kerberos-hitelesítés tervezése a SharePoint 2013-ben](https://technet.microsoft.com/library/ee806870.aspx).
* Minden alkalmazás rendelkezik [egyszerű szolgáltatásnév nevével](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Az összekötő szolgáltatást futtató kiszolgáló és az alkalmazást futtató kiszolgáló olyan tartományhoz csatlakoztatott és a ugyanabban a tartományban vagy megbízható tartomány része. További információ a tartományhoz való csatlakozásról: [számítógép csatlakoztatása tartományhoz](https://technet.microsoft.com/library/dd807102.aspx).
* Az összekötő szolgáltatást futtató kiszolgáló hozzáfér a értékűnek attribútum olvassa el a felhasználók számára. Ez az alapértelmezett beállítás előfordulhat, hogy rendelkezik lett által érintett biztonsági korlátozások a környezetben.

### <a name="configure-active-directory"></a>Az Active Directory konfigurálása
Az Active Directory konfigurálása az Application Proxy connector és az alkalmazáskiszolgáló vannak-e ugyanabban a tartományban, vagy nem függően változik.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Összekötő és alkalmazás-kiszolgáló ugyanabban a tartományban
1. A Active Directory területen válassza az **eszközök** > **felhasználók és számítógépek**lehetőséget.
2. Válassza ki azt az összekötőt futtató kiszolgálón.
3. Kattintson a jobb gombbal, és válassza a **tulajdonságok** > **delegálás**lehetőséget.
4. Válassza a **számítógép megbízhatónak nyilvánítása csak a megadott szolgáltatások delegálásához**lehetőséget. 
5. Válassza **a bármely hitelesítési protokoll használata**lehetőséget.
6. Azon **szolgáltatások alatt, amelyekben ez a fiók delegált hitelesítő adatokat tud bemutatni** , adja hozzá az alkalmazáskiszolgáló SPN-identitásának értékét. Ez lehetővé teszi a felhasználók megszemélyesítésére az ad-ben az olyan alkalmazások, a listában megadott az alkalmazásproxy-összekötő.

   ![Összekötő – fenntartott példányok tulajdonságok ablak képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Összekötő és az alkalmazáskiszolgáló eltérő tartományokban
1. A tartományok közötti KCD-használat előfeltételeinek listáját a [Kerberos által korlátozott delegálás tartományok között](https://technet.microsoft.com/library/hh831477.aspx)című témakörben tekintheti meg.
2. A webalkalmazáshoz tartozó szolgáltatásfiók (számítógép vagy dedikált tartományi felhasználói fiók) `principalsallowedtodelegateto` tulajdonságával engedélyezze a Kerberos-hitelesítés delegálását az alkalmazásproxy (összekötő) használatával. Az alkalmazáskiszolgáló a `webserviceaccount` kontextusában fut, és a delegált kiszolgáló `connectorcomputeraccount`. Futtassa az alábbi parancsokat egy tartományvezérlőn (Windows Server 2012 R2 vagy újabb rendszert futtatva) `webserviceaccount`tartományában. Mindkét fiókhoz használjon egyszerű neveket (nem UPN).

   Ha a `webserviceaccount` egy számítógépfiók, használja a következő parancsokat:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Ha a `webserviceaccount` felhasználói fiók, használja a következő parancsokat:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása 
1. Tegye közzé az alkalmazást az Application [proxyval történő alkalmazások közzététele](application-proxy-add-on-premises-application.md)című részben leírt utasítások szerint. Győződjön meg arról, hogy a **Azure Active Directory** az **előhitelesítési módszerként**van kiválasztva.
2. Miután az alkalmazás megjelenik a vállalati alkalmazások listájában, jelölje ki, majd kattintson az **egyszeri bejelentkezés**elemre.
3. Az egyszeri bejelentkezési mód beállítása az **integrált Windows-hitelesítésre**.  
4. Adja meg az alkalmazáskiszolgáló **belső alkalmazásspecifikus egyszerű szolgáltatásnevet** . Ebben a példában a közzétett alkalmazás egyszerű Szolgáltatásnevét http/www.contoso.com. Ezt az SPN kell lennie, amelyhez az összekötőt használhat delegált hitelesítő adatokat a szolgáltatások listájában. 
5. Válassza ki az összekötő **meghatalmazott bejelentkezési azonosítóját** , amelyet a felhasználók nevében kíván használni. További információ: [a különböző helyszíni és Felhőbeli identitások használata](#working-with-different-on-premises-and-cloud-identities)

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

További információ a Kerberosról: [minden, amit tudni szeretne a Kerberos által korlátozott delegálásról (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Nem-Windows-alkalmazások általában felhasználói felhasználónevek vagy a SAM-fiókok nevét tartomány helyett e-mail-címek. Ilyen esetekben az alkalmazások vonatkozik, ha szüksége konfigurálja a felhőbeli identitások csatlakozni a alkalmazás identitások delegált bejelentkezési azonosító mezőjét. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>A különböző helyszíni és felhőbeli identitások
Az alkalmazásproxy azt feltételezi, hogy a felhasználók pontosan ugyanaz az identitás a felhőben és a helyszínen rendelkeznek. Bizonyos környezetekben azonban a vállalati házirendek vagy az alkalmazás függőségei miatt előfordulhat, hogy a szervezeteknek alternatív azonosítókat kell használniuk a bejelentkezéshez. Ilyen esetekben továbbra is használhatja a KCD az egyszeri bejelentkezéshez. Konfiguráljon egy **meghatalmazott bejelentkezési azonosítót** az egyes alkalmazásokhoz, hogy megadják, melyik identitást kell használni az egyszeri bejelentkezés végrehajtásakor.  

Ez a funkció lehetővé teszi, hogy számos különböző helyszíni és felhőalapú Identitások kell SSO a felhőből helyszíni alkalmazásokat anélkül, hogy a felhasználók különböző felhasználónevek és jelszavak rendelkező szervezeteknek. Ez magában foglalja a szervezetek, amelyek:

* Több tartományon belül (joe@us.contoso.com, joe@eu.contoso.com) és a felhőben egyetlen tartományba (joe@contoso.com) kell lennie.
* A nem átirányítható tartománynevet belsőleg (joe@contoso.usa) és egy, a felhőben található jogi területen kell megmutatni.
* Belsőleg nem használja a tartománynevek (joe)
* Használjon különböző aliasokat a helyszínen és a felhőben. Például joe-johns@contoso.com vs. joej@contoso.com  

A proxyval kiválaszthatja, melyik identitás a Kerberos-jegy beszerzéséhez használhatnak. Ez a beállítás akkor alkalmazásonként. Ezek a beállítások némelyike rendszerek, amelyek nem fogadja el az e-mail cím formátuma megfelelő, mások alternatív bejelentkezési lettek kialakítva.

![Delegált bejelentkezési azonosító paraméter képernyőképe](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Delegált bejelentkezési azonosító használata esetén az érték nem egyedi a tartományok és erdők a szervezet között. A probléma elkerüléséhez tegye közzé kétszer használatával két különböző összekötőcsoportok ezeket az alkalmazásokat. Minden alkalmazás rendelkezik egy másik felhasználói közönség számára, mivel az összekötők csatlakozhat egy másik tartományba.

### <a name="configure-sso-for-different-identities"></a>Egyszeri bejelentkezés konfigurálása a különböző identitások
1. Az Azure AD Connect-beállítások konfigurálása, így a fő identitását az e-mail-cím (levelezés). Ez a testreszabási folyamat részeként a szinkronizálási beállítások **egyszerű felhasználónév** mezőjének módosításával történik. Ezek a beállítások is határozzák hogyan való felhasználói bejelentkezés Office 365, Windows 10-es eszközök, és egyéb alkalmazásokhoz, amelyek az Azure AD használatára az ügyfélidentitás-tárolóval.  
   a felhasználók azonosításának ![képernyőképe – egyszerű felhasználónév legördülő](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. A módosítandó alkalmazás konfigurációs beállításainál válassza ki a használni kívánt **delegált bejelentkezési azonosítót** :

   * Egyszerű felhasználónév (például joe@contoso.com)
   * Másodlagos egyszerű felhasználónév (például joed@contoso.local)
   * (Például joe) egyszerű felhasználónév felhasználónév része
   * Alternatív egyszerű felhasználónév (például joed) felhasználónév része
   * A helyi SAM-fiók neve (a tartományvezérlő konfigurációja függ)

### <a name="troubleshooting-sso-for-different-identities"></a>Egyszeri bejelentkezés hibaelhárítása a különböző identitások
Ha az egyszeri bejelentkezés folyamata során hiba lép fel, az az összekötő számítógép eseménynaplójában jelenik meg, a [Hibaelhárítás](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)során.
De egyes esetekben a sikeresen irányuló kérést küld a háttéralkalmazás az alkalmazás válaszol a különböző HTTP-válaszok során. Ezekben az esetekben hibaelhárítási el kell indítaniuk az alkalmazásproxy-munkamenet eseménynaplóban összekötő gépen 24029 eseményazonosítóval megvizsgálásával. A felhasználói identitás delegálásához használt belül az esemény részleteinek a "user" mezőjében jelenik meg. A munkamenet-napló bekapcsolásához válassza az **elemzési és hibakeresési naplók megjelenítése** lehetőséget az Eseménynapló Nézet menüjében.

## <a name="next-steps"></a>További lépések

* [Alkalmazásproxy-alkalmazás konfigurálása a Kerberos által korlátozott delegálás használatára](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Az Application proxyval kapcsolatos problémák elhárítása](application-proxy-troubleshoot.md)


A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](https://blogs.technet.com/b/applicationproxyblog/).
