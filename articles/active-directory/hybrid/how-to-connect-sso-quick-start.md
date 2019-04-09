---
title: 'Azure AD Connect: Közvetlen egyszeri bejelentkezés – gyors üzembe helyezési |} A Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Active Directory zökkenőmentes egyszeri bejelentkezés
services: active-directory
keywords: Mi az Azure AD Connect, Active Directory telepítése szükséges összetevők SSO, Azure AD egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f8483eb0ce8f5ea890e453828d36afda61ef86f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256889"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Első lépések

## <a name="deploy-seamless-single-sign-on"></a>Közvetlen egyszeri bejelentkezés üzembe helyezése

Az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezés (közvetlen egyszeri bejelentkezés) automatikusan bejelentkezteti a felhasználókat, amelyek a saját vállalati asztali számítógépek, a vállalati hálózathoz csatlakozó. Közvetlen egyszeri bejelentkezés a felhasználókat az felhőalapú alkalmazásait is egyszerű hozzáférést biztosít anélkül, hogy bármilyen további helyszíni összetevők.

Közvetlen egyszeri bejelentkezés üzembe helyezéséhez kövesse az alábbi lépéseket.

## <a name="step-1-check-the-prerequisites"></a>1. lépés: Az előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételeket:

* **Az Azure AD Connect-kiszolgáló beállítása**: Ha [átmenő hitelesítés](how-to-connect-pta.md) a bejelentkezési módszerrel, nincs további előfeltétel-ellenőrzés nem szükséges. Ha [Jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md) legyen a bejelentkezési módszer, és ha az Azure AD Connect és az Azure AD között tűzfal, ellenőrizze, hogy:
   - 1.1.644.0 verziót vagy az Azure AD Connect újabb. 
   - Ha a tűzfal vagy proxy engedélyezett DNS engedélyezéskor a kapcsolatokat engedélyez a  **\*. msappproxy.net** URL-címek 443-as porton keresztül. Való hozzáférés engedélyezése, ha nem, akkor a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653), amely hetente frissül. Ezt az előfeltételt alkalmazható, csak akkor, ha engedélyezi a funkciót. Nem kötelező a tényleges felhasználói bejelentkezéseket.

    >[!NOTE]
    >Az Azure AD Connect-verziók 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0 van a Jelszókivonat-szinkronizálás kapcsolatos probléma. Ha Ön _nem_ kívánja használni a Jelszókivonat-szinkronizálás az átmenő hitelesítéssel, olvassa el a [kibocsátási megjegyzések az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) további.

* **Használjon egy támogatott az Azure AD Connect topológia**: Győződjön meg arról, hogy használja az Azure AD Connect támogatott topológiák leírt egyik [Itt](plan-connect-topologies.md).

    >[!NOTE]
    >Közvetlen egyszeri bejelentkezés több AD-erdőkkel, támogatja a vannak-e AD Megbízhatóságok között vagy sem.

* **Tartományi rendszergazdai hitelesítő adatok beállítása**: Minden egyes Active Directory-erdő, amely tartományi rendszergazdai hitelesítő adatokkal rendelkeznie kell:
    * Az Azure AD-keresztül az Azure AD Connect szinkronizálása.
    * Közvetlen egyszeri bejelentkezés engedélyezése kívánt felhasználókat tartalmazza.
    
* **A modern hitelesítés engedélyezése**: Engedélyeznie kell a [modern hitelesítést](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) a bérlő esetében ez a funkció működéséhez.

* **Az Office 365-ügyfelek a legújabb verziókat használhatja**: A beavatkozás nélküli bejelentkezést az Office 365-ügyfelek (Outlook, Word, Excel és mások), a felhasználók kell verziók 16.0.8730.xxxx használja vagy újabb.

## <a name="step-2-enable-the-feature"></a>2. lépés: A funkció engedélyezése

Engedélyezze a közvetlen egyszeri bejelentkezés révén [az Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Emellett [PowerShell-lel közvetlen egyszeri bejelentkezés engedélyezése](tshoot-connect-sso.md#manual-reset-of-the-feature) , ha az Azure AD Connect nem felel meg a követelményeknek. Használja ezt a beállítást, ha az Active Directory-erdőnként egynél több tartományban van, és szeretné engedélyezni a közvetlen egyszeri bejelentkezés, a tartománnyal kapcsolatban további megcélozni kívánt.

Ha az Azure AD Connect új példánya, válassza ki a [egyéni telepítési útvonal](how-to-connect-install-custom.md). Jelenleg a **felhasználói bejelentkezés** lapon válassza ki a **egyszeri bejelentkezés engedélyezése** lehetőséget.

>[!NOTE]
> A lehetőség lesz elérhető a kiválasztásra, csak akkor, ha a bejelentkezési módszer **Jelszókivonat-szinkronizálás** vagy **átmenő hitelesítés**.

![Azure AD Connect: Felhasználói bejelentkezés](./media/how-to-connect-sso-quick-start/sso8.png)

Ha már rendelkezik egy Azure AD Connect telepítését, jelölje be a **felhasználói bejelentkezés módosítása** az Azure AD Connect lapján, és válassza ki **tovább**. Az Azure AD Connect verziója 1.1.880.0 használatakor, vagy újabb, a **egyszeri bejelentkezés engedélyezése** alapértelmezés szerint a program lehetőséget választja. Ha az Azure AD Connect régebbi verzióit használ, válassza ki a **egyszeri bejelentkezés engedélyezése** lehetőséget.

![Azure AD Connect: A felhasználói bejelentkezés módosítása](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Járja végig a varázsló, amíg nem kap a **egyszeri bejelentkezés engedélyezése** lapot. Adja meg a tartományi rendszergazda hitelesítő adatai az egyes Active Directory erdő, amely:

* Az Azure AD-keresztül az Azure AD Connect szinkronizálása.
* Közvetlen egyszeri bejelentkezés engedélyezése kívánt felhasználókat tartalmazza.

A varázsló befejezését követően a közvetlen egyszeri bejelentkezés engedélyezve van a bérlő.

>[!NOTE]
> A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Csak az a funkció engedélyezéséhez használhatók.

Kövesse az alábbi utasításokat, győződjön meg arról, hogy engedélyezte a közvetlen egyszeri bejelentkezés megfelelően:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlőhöz tartozó globális rendszergazdai hitelesítő adatokkal.
2. Válassza ki **Azure Active Directory** a bal oldali panelen.
3. Válassza ki **az Azure AD Connect**.
4. Ellenőrizze, hogy a **zökkenőmentes egyszeri bejelentkezést** jelenik meg a szolgáltatás **engedélyezve**.

![Az Azure Portalon: Az Azure AD Connect panel](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Közvetlen egyszeri bejelentkezés nevű számítógép-fiókot hoz létre `AZUREADSSOACC` a helyszíni Active Directory (AD) minden olyan AD-erdőben. A `AZUREADSSOACC` számítógépfiók biztonsági okokból erősen védeni kell. Csak a tartományi rendszergazdák kezelése a számítógépfiók képesnek kell lennie. Győződjön meg arról, hogy a Kerberos-delegálás, a számítógép fiók le van tiltva. Store a számítógépfiókot a egy szervezeti egység (OU), ha azok véletlen törlések biztonságáról, és csak tartományi rendszergazdák hozzáférhetnek.

>[!NOTE]
> Annak érdekében, hogy ha a Pass-the-Hash és a hitelesítő adatok ellopásának kockázatcsökkentési architektúrák használ a helyszíni környezetben, megfelelő módosításokat a `AZUREADSSOACC` számítógépfiók végül nem a a karanténba helyezett tárolóra. 

## <a name="step-3-roll-out-the-feature"></a>3. lépés: A szolgáltatás bevezetése

Is fokozatosan fokozatosan zökkenőmentes egyszeri Bejelentkezést a felhasználók az alábbi utasítások szerint. Először adja hozzá a következő, Azure AD-URL az összes, vagy a kiválasztott felhasználók intranetes zóna beállításait az Active Directory csoportházirend használatával:

- `https://autologon.microsoftazuread-sso.com`

Ezenkívül, engedélyeznie kell az Intranet zóna házirend nevű beállítása **lehetővé teszik a frissítések állapotsor keresztül parancsfájl** csoportházirenden keresztül. 

>[!NOTE]
> Az alábbi utasításokat követve dolgozhat csak az Internet Explorer és a Google Chrome Windows (ha az Internet Explorer Megbízható helyek URL-címek készletét közös). Olvassa el a következő szakaszban útmutatást állíthatja be a Mozilla Firefox és a Google Chrome-ban MacOS-gépeken.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Miért van szüksége felhasználói intranetes zóna beállításait módosítani?

Alapértelmezés szerint a böngésző automatikusan kiszámolja a megfelelő zónához, internetes vagy intranetes, egy adott URL-címről. Például `http://contoso/` az Intranet zóna képez, mivel a `http://intranet.contoso.com/` rendeli az Internet zóna (mivel az URL-cím pont szerepel). Böngészők nem küldenek Kerberos-jegyekhez felhőbeli végpont, például az Azure AD URL-cím, kivéve, ha explicit módon fel az URL-CÍMÉT a böngésző intranetzónához az.

Felhasználók Intranet zóna beállításainak módosításához két módja van:

| Beállítás | Admin consideration | Felhasználói élmény |
| --- | --- | --- |
| Csoportházirend | Felügyeleti zárolások le az Intranet zóna beállításainak szerkesztése | A felhasználó nem módosíthatja a saját beállítások |
| Csoportházirend-beállítások |  Rendszergazdai lehetővé teszi, hogy az Intranet zóna beállításainak szerkesztése | Felhasználók a saját beállítások módosíthatók. |

### <a name="group-policy-option---detailed-steps"></a>"Csoportházirend" beállítás – részletes lépései

1. Nyissa meg a Csoportházirendkezelés-szerkesztő eszközt.
2. Az egyes alkalmazott csoportházirend szerkesztése vagy az összes felhasználó. Ez a példa **alapértelmezett tartományi házirend**.
3. Keresse meg a **felhasználói konfiguráció** > **házirend** > **felügyeleti sablonok** > **Windows Összetevők** > **az Internet Explorer** > **Internet Vezérlőpult** > **Biztonság lap**. Válassza ki **zónákhoz való társításának listája a hely**.
    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso6.png)
4. Engedélyezze a házirendet, és írja be a következő értékeket a párbeszédpanelen:
   - **Érték neve**: Az Azure AD URL-CÍMÉT, a Kerberos-jegyet a rendszer továbbítja.
   - **Érték** (adatok): **1** azt jelzi, hogy az Intranet zóna.

     Az eredmény a következőhöz hasonló:

     Érték neve: `https://autologon.microsoftazuread-sso.com`
  
     Érték (adatok): 1

   >[!NOTE]
   > Ha azt szeretné, letilthatja egyes a közvetlen egyszeri bejelentkezés használatával (például, ha ezek a felhasználók jelentkezzen be a megosztott számítógépek), állítsa az előző értékeket **4**. Ez a művelet az Azure AD URL-címet ad hozzá a Tiltott helyek zónához, és közvetlen egyszeri bejelentkezés folyamatosan meghiúsul.
   >

5. Válassza ki **OK**, majd válassza ki **OK** újra.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso7.png)

6. Keresse meg a **felhasználói konfiguráció** > **felügyeleti sablonok** **házirend** > ** > **Windows-összetevők**  >  **Az Internet Explorer** > **Internet Vezérlőpult** > **Biztonság lap**  >   **Intranet zóna**. Válassza ki **lehetővé teszik a frissítések állapotsor keresztül parancsfájl**.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso11.png)

7. Engedélyezi a házirend-beállítást, és válassza ki **OK**.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>"Group csoportházirend-beállítások" lehetőséget – részletes lépései

1. Nyissa meg a Csoportházirendkezelés-szerkesztő eszközt.
2. Az egyes alkalmazott csoportházirend szerkesztése vagy az összes felhasználó. Ez a példa **alapértelmezett tartományi házirend**.
3. Keresse meg a **felhasználói konfiguráció** > **beállítások** > **Windows beállítások** > **beállításjegyzék**  >  **Új** > **beállításjegyzék-elem**.

    ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso15.png)

4. Adja meg a következő értékeket a megfelelő, és kattintson a **OK**.
   - **Elérési kulcs**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Érték neve**: ***https***.
   - **Érték típusa**: ***REG_DWORD***.
   - **Érték**: ***00000001***.
 
     ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Egyszeri bejelentkezés](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Böngésző kapcsolatos szempontok

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (minden platform)

Mozilla Firefox automatikusan nem használ Kerberos-hitelesítést. Minden felhasználónak manuálisan kell hozzáadni az Azure AD URL-cím a Firefox beállításait az alábbi lépések segítségével:
1. Futtassa a Firefox és adja meg `about:config` címet a címsorba. Hagyja figyelmen kívül minden értesítést fog látni.
2. Keresse meg a **network.negotiate-auth.trusted-URI-k** előnyben. Ez a beállítás a megbízható helyek Firefox a Kerberos-hitelesítéshez sorolja fel.
3. Kattintson a jobb gombbal, és válassza ki **módosítás**.
4. Adja meg `https://autologon.microsoftazuread-sso.com` a mezőben.
5. Válassza ki **OK** majd nyissa meg a böngészőben.

#### <a name="safari-macos"></a>Safari (macOS)

Győződjön meg arról, hogy a gép a macOS rendszerű ad-hez csatlakozik. Utasítások az AD-csatlakozás macOS-eszköz nem ez a cikk foglalkozik.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform)

Ha rendelkezik felül a [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) vagy a [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) házirend-beállítások a környezetben, győződjön meg arról, hogy a az Azure AD URL-cím hozzáadása (`https://autologon.microsoftazuread-sso.com`) nekik is.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS és más platformokon, nem Windows)

Google Chrome, a Mac OS és más nem Windows platformokon, tekintse meg a [a króm projekt Policy List](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) információk, amelyeket engedélyezni kell hogy Azure ad-ben URL-Címének hogyan integrált hitelesítés.

Külső Active Directory csoportházirend-bővítmények számára, hogy az Azure AD URL-cím Google Chrome, Firefox a Mac-felhasználók használata ebben a cikkben hatókörén kívül.

#### <a name="known-browser-limitations"></a>A böngésző ismert korlátozások

Közvetlen egyszeri bejelentkezés nem működik a privát böngészési módban, a Firefox és a Microsoft Edge böngészőben. Azt is nem működik az Internet Explorer böngészőben fokozott védett módban fut, ha.

## <a name="step-4-test-the-feature"></a>4. lépés: A szolgáltatás tesztelése

A funkció egy adott felhasználó teszteléséhez ellenőrizze, hogy az alábbi feltételek teljesülnek:
  - A felhasználó bejelentkezik vállalati eszközök.
  - Az eszköz az Active Directory-tartományhoz csatlakozik. Az eszköz _nem_ kell lennie [Azure AD-csatlakoztatás](../active-directory-azureadjoin-overview.md).
  - Az eszköz a tartományvezérlő (DC), hogy közvetlen kapcsolat van, a vállalati vezetékes vagy vezeték nélküli hálózaton vagy a távelérési kapcsolatot, például a VPN-kapcsolat használatával.
  - Rendelkezik [egyik tagján jelennek meg a szolgáltatás](##step-3-roll-out-the-feature) ehhez a felhasználóhoz a csoportházirenden keresztül.

A forgatókönyv, ahol a felhasználó sikeresen Megadja, csak a felhasználónév, de a jelszavát nem teszteléséhez:
   - Jelentkezzen be a `https://myapps.microsoft.com/` egy új privát böngésző-munkamenetben.

Tesztelje a forgatókönyvet, ahol a felhasználó nem rendelkezik a felhasználónév vagy jelszó megadását, használja az alábbi lépéseket: 
   - Jelentkezzen be a `https://myapps.microsoft.com/contoso.onmicrosoft.com` egy új privát böngésző-munkamenetben. Cserélje le *contoso* a bérlő nevét.
   - Jelentkezzen be a `https://myapps.microsoft.com/contoso.com` egy új privát böngésző-munkamenetben. Cserélje le *contoso.com* az a bérlő ellenőrzött tartományt (nem összevont tartományban).

## <a name="step-5-roll-over-keys"></a>5. lépés: Kulcsok vihető

2. lépésben az Azure AD Connect létrehoz számítógépfiókok (amely az Azure AD), amelyen engedélyezte a közvetlen egyszeri bejelentkezés az Active Directory erdőkben. További tudnivalókért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Részletes technikai](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>A számítógép-fiók, a Kerberos-visszafejtési kulcs kiszivárgott, ha használható lehet létrehozni a Kerberos-jegyekhez bármely felhasználó számára az AD-erdőben. Rosszindulatú majd tudja megszemélyesíteni az Azure AD bejelentkezések a feltört felhasználók számára. Kifejezetten ajánljuk, hogy rendszeres időközönként rotálja ezek Kerberos visszafejtési kulcsok – 30 nap során legalább egyszer.

Hogyan állítható kulcsok útmutatásért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Gyakori kérdések](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account). Dolgozunk a kulcsok vezessen be automatikus visszaállítási keresztül teszi lehetővé.

>[!IMPORTANT]
>Ez a lépés nem kell _azonnal_ a funkció engedélyezése után. Vihetők át a Kerberos-visszafejtési kulcsok a 30 nap során legalább egyszer.

## <a name="next-steps"></a>További lépések

- [Részletes technikai](how-to-connect-sso-how-it-works.md): A közvetlen egyszeri bejelentkezés funkció működésének megismerése.
- [Gyakori kérdések](how-to-connect-sso-faq.md): Válaszok a gyakori kérdésekre vonatkozó zökkenőmentes egyszeri bejelentkezést.
- [Hibaelhárítás](tshoot-connect-sso.md): Ismerje meg, a zökkenőmentes egyszeri bejelentkezés funkció szolgáltatással kapcsolatos gyakori problémák megoldásához.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Az Azure Active Directory-fórumon használatával új funkcióra vonatkozó javaslata fájlt.
