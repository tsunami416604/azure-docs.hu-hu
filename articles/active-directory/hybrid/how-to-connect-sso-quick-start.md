---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – gyorsindítás | Microsoft dokumentumok'
description: Ez a cikk az Azure Active Directory zökkenőmentes egyszeri bejelentkezést ismerteti
services: active-directory
keywords: mi az Azure AD Connect, telepítse az Active Directoryt, szükséges összetevőket az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec56d37ca2c0a199968707b3d93f4797be2beca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261202"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezése: Gyors indítás

## <a name="deploy-seamless-single-sign-on"></a>Zökkenőmentes egyszeri bejelentkezés üzembe helyezése

Az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezés (seamless sso) automatikusan bejelentkezik a felhasználók, amikor azok a vállalati asztal, amely csatlakozik a vállalati hálózathoz. A zökkenőmentes egyszeri bejelentkezés egyszerű hozzáférést biztosít a felhasználók számára a felhőalapú alkalmazásokhoz anélkül, hogy további helyszíni összetevőkre lenne szüksége.

A zökkenőmentes egyszeri bejelentkezés üzembe helyezéséhez kövesse az alábbi lépéseket.

## <a name="step-1-check-the-prerequisites"></a>1. lépés: Az előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételek vannak érvényben:

* **Állítsa be az Azure AD Connect-kiszolgálót:** Ha [átmenő hitelesítést](how-to-connect-pta.md) használ bejelentkezési módszerként, nincs szükség további előfeltétel-ellenőrzésre. Ha bejelentkezési módszerként [jelszókivonat-szinkronizálást](how-to-connect-password-hash-synchronization.md) használ, és tűzfal van az Azure AD Connect és az Azure AD között, győződjön meg arról, hogy:
   - Az Azure AD Connect 1.1.644.0-s vagy újabb verzióját használja. 
   - Ha a tűzfal vagy a proxy engedélyezi a DNS engedélyezési listáját, a 443-as porton keresztül engedélyezési listára kell tenni az ** \*.msappproxy.net** URL-címekkel létesített kapcsolatokat. Ha nem, engedélyezze a hozzáférést az [Azure-adatközpont IP-tartományaihoz,](https://www.microsoft.com/download/details.aspx?id=41653)amelyek hetente frissülnek. Ez az előfeltétel csak akkor alkalmazható, ha engedélyezi a funkciót. A tényleges felhasználói bejelentkezések nem szükségesek.

    >[!NOTE]
    >Az Azure AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0-s verzióinak problémája van a jelszókivonat-szinkronizálással kapcsolatban. Ha _nem_ kívánja használni a jelszókivonat-szinkronizálást az átadó-hitelesítéssel együtt, olvassa el az [Azure AD Connect kiadási megjegyzéseket,](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) hogy további információval.

* **Támogatott Azure AD Connect-topológia**használata: Győződjön meg arról, hogy az Azure AD Connect által [itt](plan-connect-topologies.md)ismertetett támogatott topológiák egyikét használja.

    >[!NOTE]
    >A zökkenőmentes egyszeri bejelentkezés több AD-erdőt is támogat, függetlenül attól, hogy vannak-e közöttük AD-megbízhatósági kapcsolatok.

* **Tartományi rendszergazdai hitelesítő adatok beállítása:** Minden olyan Active Directory-erdőhöz rendelkeznie kell tartományi rendszergazdai hitelesítő adatokkal, amelyek:
    * Az Azure AD-vel az Azure AD Connect en keresztül szinkronizál.
    * Azokat a felhasználókat tartalmazza, akiket engedélyezni szeretne a zökkenőmentes egyszeri bejelentkezéshez.
    
* **Modern hitelesítés engedélyezése:** Engedélyeznie kell a [modern hitelesítést](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) a bérlőn ahhoz, hogy ez a szolgáltatás működjön.

* **Az Office 365-ügyfelek legújabb verzióinak használata**: Az Office 365-ügyfelekkel (Outlook, Word, Excel és mások) való csendes bejelentkezéshez a felhasználóknak a 16.0.8730.xxxx vagy újabb verziót kell használniuk.

## <a name="step-2-enable-the-feature"></a>2. lépés: A funkció engedélyezése

Zökkenőmentes egyszeri bejelentkezés engedélyezése az [Azure AD Connect en](whatis-hybrid-identity.md)keresztül.

>[!NOTE]
> [A Zökkenőmentes egyszeri bejelentkezést a PowerShell használatával](tshoot-connect-sso.md#manual-reset-of-the-feature) is engedélyezheti, ha az Azure AD Connect nem felel meg az Ön igényeinek. Akkor használja ezt a beállítást, ha Active Directory-erdőnként egynél több tartománnyal rendelkezik, és azt szeretné, hogy célzottabb legyen a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez használni kívánt tartományt.

Ha az Azure AD Connect friss telepítését végzi, válassza ki az [egyéni telepítési útvonalat.](how-to-connect-install-custom.md) A **Felhasználó bejelentkezési** lapján jelölje be az **Egyszeri bejelentkezés engedélyezése jelölőnégyzetet.**

>[!NOTE]
> A beállítás csak akkor választható ki, ha a Bejelentkezési módszer a **jelszókivonat-szinkronizálás** vagy az **átmenő hitelesítés**.

![Azure AD Connect: Felhasználói bejelentkezés](./media/how-to-connect-sso-quick-start/sso8.png)

Ha már rendelkezik az Azure AD Connect telepítésével, válassza a **Felhasználói bejelentkezés módosítása** lapot az Azure AD Connectben, majd kattintson a **Tovább**gombra. Ha az Azure AD Connect 1.1.880.0-s vagy újabb verzióit használja, alapértelmezés szerint az **Egyszeri bejelentkezés engedélyezése** beállítás lesz kiválasztva. Ha az Azure AD Connect régebbi verzióit használja, válassza az **Egyszeri bejelentkezés engedélyezése** lehetőséget.

![Azure AD Connect: A felhasználói bejelentkezés módosítása](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Folytassa a varázslóval, amíg el nem jut az **Egyjel engedélyezése lapra.** Adja meg a tartományi rendszergazdai hitelesítő adatokat minden olyan Active Directory-erdőhöz, amely:

* Az Azure AD-vel az Azure AD Connect en keresztül szinkronizál.
* Azokat a felhasználókat tartalmazza, akiket engedélyezni szeretne a zökkenőmentes egyszeri bejelentkezéshez.

A varázsló befejezése után a zökkenőmentes egyszeri bejelentkezés engedélyezve van a bérlőn.

>[!NOTE]
> A tartományi rendszergazdai hitelesítő adatok nem tárolódnak az Azure AD Connectben vagy az Azure AD-ben. Csak a funkció engedélyezéséhez használatosak.

Kövesse az alábbi utasításokat annak ellenőrzéséhez, hogy megfelelően engedélyezte-e a zökkenőmentes egyszeri bejelentkezést:

1. Jelentkezzen be az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. Válassza az **Azure Active Directory** a bal oldali ablaktáblában.
3. Válassza **az Azure AD Connect lehetőséget.**
4. Ellenőrizze, hogy a Zökkenőmentes egyszeri bejelentkezés szolgáltatás **engedélyezve van-e.** **Seamless single sign-on**

![Azure portal: Az Azure AD Connect ablaktábla](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> A zökkenőmentes egyszeri bejelentkezés `AZUREADSSOACC` minden AD-erdőben létrehoz egy, a helyszíni Active Directoryban (AD) elnevezett számítógépfiókot. A `AZUREADSSOACC` számítógépfiókot biztonsági okokból erősen védeni kell. Csak a tartományi rendszergazdák kezelhetik a számítógépfiókot. Győződjön meg arról, hogy a Kerberos-delegálás le van tiltva a számítógépfiókon, és hogy az Active Directory ban egyetlen más fiók sem rendelkezik delegálási engedéllyel a `AZUREADSSOACC` számítógépfiókhoz. A számítógépfiókot egy szervezeti egységben (OU) tárolja, ahol biztonságban vannak a véletlen törléstől, és ahol csak a tartománygazdák rendelkeznek hozzáféréssel.

>[!NOTE]
> Ha pass-the-hash és hitelesítő adatok lopáskockázat-csökkentő architektúrákat használ a helyszíni `AZUREADSSOACC` környezetben, végezze el a megfelelő módosításokat annak érdekében, hogy a számítógépfiók ne kerüljön a karanténtárolóba. 

## <a name="step-3-roll-out-the-feature"></a>3. lépés: A funkció bevezetése

Az alábbi utasítások nak megfelelően fokozatosan elvezetheti a zökkenőmentes egyszeri bejelentkezést a felhasználók számára. Először adja hozzá a következő Azure AD URL-címet az összes vagy a kiválasztott felhasználók intranetzóna-beállításaihoz az Active Directory csoportházirendjével:

- `https://autologon.microsoftazuread-sso.com`

Ezenkívül engedélyeznie kell egy intranetes zónaházirend-beállítást, amelynek neve **Frissítések engedélyezése az állapotsorhoz parancsfájlon keresztül a** csoportházirenden keresztül. 

>[!NOTE]
> Az alábbi utasítások csak a Windows rendszeren futó Internet Explorer és Google Chrome böngészőben működnek (ha a megbízható webhely URL-címeit megosztja az Internet Explorer programmal). Olvassa el a következő szakaszt a Mozilla Firefox és a Google Chrome macOS rendszeren való beállításával kapcsolatos útmutatásért.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Miért kell módosítani a felhasználók intranetzóna-beállításait?

Alapértelmezés szerint a böngésző automatikusan kiszámítja a megfelelő zónát , legyen az internet vagy intranet, egy adott URL-címből. Például `http://contoso/` az Intranet zónához, `http://intranet.contoso.com/` míg az internetzónához (mivel az URL-cím egy időszakot tartalmaz) rendel hozzá. A böngészők nem küld Kerberos-jegyeket egy felhővégpontra, például az Azure AD URL-címére, kivéve, ha explicit módon hozzáadja az URL-címet a böngésző intranetzónájának.

A felhasználók intranetzóna-beállításaikétfélekban módosíthatók:

| Beállítás | Rendszergazdai szempontok | Felhasználó felület |
| --- | --- | --- |
| Csoportházirend | Rendszergazda zárolja az Intranet zóna beállításainak szerkesztését | A felhasználók nem módosíthatják saját beállításaikat |
| Csoportházirend-beállítás |  A rendszergazda engedélyezi a szerkesztést az Intranet zóna beállításaiban | A felhasználók módosíthatják saját beállításaikat |

### <a name="group-policy-option---detailed-steps"></a>"Csoportházirend" lehetőség – Részletes lépések

1. Nyissa meg a Csoportházirend-kezelés szerkesztőeszközét.
2. Szerkessze a néhány vagy az összes felhasználóra alkalmazott csoportházirendet. Ez a példa **az Alapértelmezett tartományi házirendet**használja.
3. Tallózással keresse meg **a Felhasználó konfigurációs** > **házirendfelügyeleti** > **sablonjait:** > **Windows-összetevők** > **Internet Explorer** > **Internet Control Panel** > **Biztonsági lap**. Ezután válassza **a Hely a zóna közötti hozzárendelési listát**.
    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso6.png)
4. Engedélyezze a házirendet, majd írja be a következő értékeket a párbeszédpanelen:
   - **Érték neve:** Az Azure AD URL-cím, ahol a Kerberos-jegyek továbbítása.
   - **Érték** (adat): **1** az intranet zónát jelöli.

     Az eredmény így néz ki:

     Érték neve:`https://autologon.microsoftazuread-sso.com`
  
     Érték (adat): 1

   >[!NOTE]
   > Ha azt szeretné, hogy egyes felhasználók ne használjanak zökkenőmentes egyszeri bejelentkezést (például ha ezek a felhasználók megosztott kioszkokon jelentkeznek be), állítsa az előző értékeket **4**értékre. Ez a művelet hozzáadja az Azure AD URL-címét a tiltott zónához, és mindig sikertelen a zökkenőmentes egyszeri bejelentkezés.
   >

5. Válassza az **OK**, majd újra az **OK** elemet.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso7.png)

6. Tallózással keresse meg **a Felhasználó konfigurációs** > **házirendfelügyeleti** > **sablonjait** > **Windows-összetevők** > Internet**Explorer** > Internet Control**Panel** > biztonsági lap > **intranetzónája**.**Security Page** Ezután válassza **a Frissítések engedélyezése az állapotsorhoz parancsfájlon keresztül lehetőséget.**

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso11.png)

7. Engedélyezze a házirend-beállítást, majd kattintson **az OK gombra.**

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>"Csoportházirend-beállítás" beállítás – Részletes lépések

1. Nyissa meg a Csoportházirend-kezelés szerkesztőeszközét.
2. Szerkessze a néhány vagy az összes felhasználóra alkalmazott csoportházirendet. Ez a példa **az Alapértelmezett tartományi házirendet**használja.
3. Tallózással keresse meg **a Felhasználó konfigurációs** > **beállításai windows** > **beállítások** > **beállításjegyzék** > **új** > **beállításjegyzék-elem .**

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso15.png)

4. Írja be a következő értékeket a megfelelő mezőkbe, majd kattintson **az OK**gombra.
   - **Kulcs elérési útja**: ***Szoftver\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Érték neve**: ***https***.
   - **Értéktípusa**: ***REG_DWORD***.
   - **Értékadatok**: ***00000001***.
 
     ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>A böngészővel kapcsolatos szempontok

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (minden platformon)

A Mozilla Firefox nem használja automatikusan a Kerberos-hitelesítést. Minden felhasználónak manuálisan hozzá kell adnia az Azure AD URL-címét a Firefox beállításaihoz a következő lépések végrehajtásával:
1. Futtassa `about:config` a Firefoxot, és írja be a címsorba. Utasítsa el a megjelenő értesítéseket.
2. Keresse meg a **network.negotiate-auth.trusted-uris preferenciát.** Ez a beállítás a Firefox Kerberos-hitelesítésre vonatkozó megbízható helyeit sorolja fel.
3. Kattintson a jobb gombbal, és válassza **a Módosítás parancsot.**
4. Írja `https://autologon.microsoftazuread-sso.com` be a mezőbe.
5. Válassza **az OK gombot,** majd nyissa meg újra a böngészőt.

#### <a name="safari-macos"></a>Safari (macOS)

Győződjön meg arról, hogy a macOS operációs rendszert futtató gép csatlakozik az AD-hez. Az AD-hez való csatlakozásra vonatkozó utasítások nem tartoznak a jelen cikk hatálya alá.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge a Chromium alapján (minden platformon)

Ha felülbírálta az [AuthNegotiateDelegateAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) vagy az [AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) házirend-beállításokat a környezetben, győződjön meg arról, hogy az Azure AD URL-címét (`https://autologon.microsoftazuread-sso.com`) is hozzáadja.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge a Chromium (macOS és más nem Windows platformok) alapján

A Microsoft Edge alapuló Chromium Mac OS és más nem Windows platformokon, olvassa el [a Microsoft Edge alapján króm szabályzatlista,](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) hogyan adja hozzá az Azure AD URL-cím az engedélyezési listához.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform)

Ha felülbírálta az [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) vagy az [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) házirend-beállításokat a környezetben, győződjön meg arról, hogy az Azure AD URL-címét (`https://autologon.microsoftazuread-sso.com`) is hozzáadja.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS és más nem Windows platformok)

A Google Chrome Mac OS és más nem Windows platformokon, olvassa el [a Chromium projekt szabályzati lista,](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) hogyan whitelist az Azure AD URL-cím az integrált hitelesítés.

A külső Active Directory csoportházirend-bővítmények használata az Azure AD URL-címének firefoxés Mac-alapú Google Chrome-felhasználók számára történő bevezetéséhez nem tartozik a jelen cikk hatálya alá.

#### <a name="known-browser-limitations"></a>Ismert böngészőkorlátozások

A zökkenőmentes egyszeri bejelentkezés nem működik privát böngészési módban a Firefox és a Microsoft Edge böngészőkben. Az Internet Explorer böngészőben sem működik, ha a böngésző fokozottan védett módban fut. A Microsoft Edge következő, Chromium alapú verziója esetén nem fog működni InPrivate és Guest módban.

## <a name="step-4-test-the-feature"></a>4. lépés: A funkció tesztelése

Ha egy adott felhasználó számára szeretné tesztelni a funkciót, győződjön meg arról, hogy az alábbi feltételek teljesülnek:
  - A felhasználó bejelentkezik egy vállalati eszközön.
  - Az eszköz csatlakozik az Active Directory-tartományhoz. Az eszköznek _nem_ kell [Azure AD-hez csatlakoznia.](../active-directory-azureadjoin-overview.md)
  - Az eszköz közvetlen kapcsolatban áll a tartományvezérlővel, akár a vállalati vezetékes vagy vezeték nélküli hálózaton, akár távelérési kapcsolaton, például VPN-kapcsolaton keresztül.
  - A [szolgáltatást csoportházirenden](#step-3-roll-out-the-feature) keresztül vezették be a felhasználószámára.

A beállítás teszteléséhez, amelyben a felhasználó csak a felhasználónevet adja meg, a jelszót azonban nem:
   - Jelentkezzen be `https://myapps.microsoft.com/` egy új privát böngészőmunkamenetbe.

Ha tesztelni szeretné azt a forgatókönyvet, amelyben a felhasználónak nem kell megadnia a felhasználónevet vagy a jelszót, kövesse az alábbi lépéseket: 
   - Jelentkezzen be `https://myapps.microsoft.com/contoso.onmicrosoft.com` egy új privát böngészőmunkamenetbe. Cserélje le *a contoso-t* a bérlő nevére.
   - Jelentkezzen be `https://myapps.microsoft.com/contoso.com` egy új privát böngészőmunkamenetbe. Cserélje le *contoso.com* a bérlő ellenőrzött tartományára (nem összevont tartományra).

## <a name="step-5-roll-over-keys"></a>5. lépés: Gombok átgörgetése

Lépésben az Azure AD Connect létrehozza a számítógépfiókokat (az Azure AD-t képviselve) az összes Olyan Active Directory-erdőben, amelyen engedélyezte a zökkenőmentes egyszeri bejelentkezést. További információ: [Azure Active Directory Seamless Single Sign-On: Technical deep dive](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>A Kerberos visszafejtési kulcs a számítógépfiók, ha kiszivárgott, lehet használni, hogy kerberos jegyeket bármely felhasználó számára az AD erdőben. A rosszindulatú szereplők ezután megszemélyesíthetik az Azure AD-bejelentkezéseket a feltört felhasználók számára. Javasoljuk, hogy rendszeresen forgassa át ezeket a Kerberos-visszafejtési kulcsokat – legalább 30 naponta egyszer.

A kulcsok átütemezésével kapcsolatos tudnivalókat az [Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakori kérdések című témakörben találja.](how-to-connect-sso-faq.md) Dolgozunk egy olyan képességen, hogy vezessenek be automatizált átütemezése kulcsok.

>[!IMPORTANT]
>Ezt a lépést nem kell _azonnal_ megtennie, miután engedélyezte a funkciót. A Kerberos visszafejtési kulcsokat legalább 30 naponta egyszer megkell felborítani.

## <a name="next-steps"></a>További lépések

- [Műszaki mélymerülés:](how-to-connect-sso-how-it-works.md)Ismerje meg, hogyan működik a zökkenőmentes egyszeri bejelentkezés funkció.
- [Gyakori kérdések:](how-to-connect-sso-faq.md)Válaszok a zökkenőmentes egyszeri bejelentkezésről szóló gyakori kérdésekre.
- [Hibaelhárítás:](tshoot-connect-sso.md)Ismerje meg, hogyan oldhatja meg a problémamentes egyszeri bejelentkezés szolgáltatással kapcsolatos gyakori problémákat.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Az Azure Active Directory fórum használatával új szolgáltatáskérelmek et nyújthat be.
