---
title: "Az Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – első lépések |} Microsoft Docs"
description: "Ez a cikk ismerteti az első lépések az Azure Active Directory zökkenőmentes egyszeri bejelentkezést."
services: active-directory
keywords: "Mi az Azure AD Connect telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: billmath
ms.openlocfilehash: 58ca992f9fcf9a03d917f0dc250a292c4d5f49e5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés: első lépések

## <a name="deploy-seamless-single-sign-on"></a>Zökkenőmentes egyszeri bejelentkezést telepítése

Az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezést (zökkenőmentes SSO) automatikusan képes bejelentkeztetni a felhasználókat, ha vannak a saját vállalati asztali számítógépek, amelyek a vállalati hálózathoz csatlakoznak. Zökkenőmentes egyszeri Bejelentkezést biztosít a felhasználók számára könnyen a felhőalapú alkalmazások anélkül, hogy további helyszíni összetevők.

Zökkenőmentes SSO telepítéséhez kövesse az alábbi lépéseket.

## <a name="step-1-check-the-prerequisites"></a>1. lépés: Az Előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételeket:

* **Az Azure AD Connect kiszolgáló**: Ha [áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication.md) legyen a bejelentkezési módszer, nincs további előfeltétel-ellenőrzésre szükség. Ha [Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-synchronization.md) legyen a bejelentkezési módszer, és ha az Azure AD Connect és az Azure AD között tűzfal, ellenőrizze, hogy:
   - Német nyelvű verziót 1.1.644.0 vagy újabb, az Azure AD Connect. 
   - Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezett, az engedélyezett a kapcsolatok a  **\*. msappproxy.net** URL-címek 443-as porton keresztül. Ha nem, a hozzáférést a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653), amely hetente frissíteni. Ezt az előfeltételt csak a szolgáltatás engedélyezése esetén alkalmazható. Nincs szükség a tényleges felhasználói bejelentkezések.

    >[!NOTE]
    >Az Azure AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0 verziónál Jelszókivonat-szinkronizálást kapcsolatos hiba. Ha Ön _nem_ kíván használni a Jelszókivonat-szinkronizálást átmenő hitelesítéssel, olvassa el a [az Azure AD Connect kibocsátási megjegyzések](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) további.

* **Tartományi rendszergazdai hitelesítő adatok beállítása**: kell rendelkeznie a tartományi rendszergazda hitelesítő adatait, az egyes Active Directory-erdőben, amely:
    * Az Azure AD-keresztül az Azure AD Connect szinkronizálása.
    * Zökkenőmentes egyszeri bejelentkezéshez engedélyezni kívánt felhasználókat tartalmazza.

## <a name="step-2-enable-the-feature"></a>2. lépés: A funkció engedélyezése

A zökkenőmentes SSO engedélyezése [az Azure AD Connect](active-directory-aadconnect.md).

Ha végzett az Azure AD Connect új példánya, válassza ki azt a [egyéni telepítési útvonal](active-directory-aadconnect-get-started-custom.md). : A **felhasználói bejelentkezés** lapon jelölje be a **engedélyezése egyszeri bejelentkezéshez** lehetőséget.

![Az Azure AD Connect: Felhasználói bejelentkezés](./media/active-directory-aadconnect-sso/sso8.png)

Ha már rendelkezik egy Azure AD Connect telepítése, válassza ki a **felhasználói bejelentkezés módosítása** az Azure AD Connectben lapon, majd válassza ki **következő**.

![Az Azure AD Connect: Módosítsa a felhasználói bejelentkezés](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Kövesse a varázsló utasításait, amíg elér a **engedélyezése egyszeri bejelentkezéshez** lap. Adja meg a tartományi rendszergazda hitelesítő adatait, az egyes Active Directory-erdőben:
    * Az Azure AD-keresztül az Azure AD Connect szinkronizálása.
    * Zökkenőmentes egyszeri bejelentkezéshez engedélyezni kívánt felhasználókat tartalmazza.

A varázsló befejezése után a zökkenőmentes SSO engedélyezve van a tenant.

>[!NOTE]
> A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Ezek mire szolgál a csak a funkció engedélyezése érdekében.

Következő lépések követésével ellenőrizze, hogy engedélyezte zökkenőmentes SSO megfelelően:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő számára a globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali ablaktáblán.
3. Válassza ki **az Azure AD Connect**.
4. Ellenőrizze, hogy a **zökkenőmentes egyszeri bejelentkezést** jelenik meg a szolgáltatás **engedélyezve**.

![Azure-portálon: az Azure AD Connect ablaktábla](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>3. lépés: A szolgáltatás megkezdik

Számára, hogy a szolgáltatás a felhasználók számára, a következő Azure AD URL-cím hozzáadása a felhasználók Intranet zóna beállításainak az Active Directory csoportházirend segítségével kell:

- https://autologon.microsoftazuread-sso.com


Emellett az Intranet zóna a házirend-beállítással a hívott engedélyeznie kell **beállítható a frissítések állapotsor keresztül parancsfájl** csoportházirenden keresztül. 

>[!NOTE]
> Az alábbi utasítások működnek csak az Internet Explorer és a Google Chrome Windows (Ha a megbízható helyek URL-címek készlete, osztja meg az Internet Explorer). Mozilla Firefox és Google Chrome Mac beállításával kapcsolatos utasításokat a következő szakaszban olvasható

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Miért van szüksége a felhasználói intranetes beállítások módosítása?

Alapértelmezés szerint a böngésző automatikusan kiszámítja a megfelelő zónához, internetes vagy intranetes, egy adott URL-címről. Például "http://contoso/" leképezve az Intranet zónához, mivel a "http://intranet.contoso.com/" (mert az URL-cím pontot tartalmaz) van leképezve az Internet zónában. Böngészők nem küld a Kerberos jegyek küldenek a felhővégpontnak, például az Azure AD URL-CÍMÉT, kivéve, ha explicit módon hozzáadása az URL-CÍMÉT a böngésző Intranet zónához.

### <a name="detailed-steps"></a>Részletes lépések

1. Nyissa meg a Csoportházirendkezelés-szerkesztő eszközt.
2. Az egyes alkalmazott csoportházirend szerkesztése vagy a felhasználók. Ez a példa **alapértelmezett tartományházirend**.
3. Keresse meg a **felhasználói konfiguráció** > **felügyeleti sablonok** > **Windows-összetevők**  >   **Az Internet Explorer** > **Internet Vezérlőpult** > **biztonsági beállításait tartalmazó lapot**. Válassza ki **zónákhoz való társításának listája a hely**.
    ![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso6.png)
4. Engedélyezi a házirendet, és írja be a következő értékeket a párbeszédpanelen:
   - **Érték neve**: az Azure AD URL-CÍMÉT a Kerberos-jegyek továbbított ahol.
   - **Érték** (adatok): **1** az Intranet zóna jelzi.

    Az eredmény így néz ki:

    Érték: https://autologon.microsoftazuread-sso.com
  
    Adatok: 1

   >[!NOTE]
   > Ha azt szeretné, hogy egyes felhasználók zökkenőmentes SSO használatával (például, ha ezek a felhasználók jelentkezzen be a megosztott számítógépeken) nem engedélyezi, a fenti értékek beállítása **4**. Ez a művelet az Azure AD URL-címet ad hozzá a Tiltott helyek zónához, és zökkenőmentes SSO folyamatosan sikertelen.
   >

5. Válassza ki **OK**, majd válassza ki **OK** újra.

    ![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso7.png)

6. Keresse meg a **felhasználói konfiguráció** > **felügyeleti sablonok** > **Windows-összetevők**  >   **Az Internet Explorer** > **Internet Vezérlőpult** > **biztonsági beállításait tartalmazó lapot** > **Intranet zóna**. Válassza ki **beállítható a frissítések állapotsor keresztül parancsfájl**.

    ![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso11.png)

7. Engedélyezi a házirend-beállítást, majd válassza ki **OK**.

    ![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Böngésző kapcsolatos szempontok

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (minden platform)

Mozilla Firefox automatikusan nem használja a Kerberos-hitelesítést. Minden felhasználó manuálisan kell hozzáadnia az Azure AD URL-CÍMÉT a Firefox beállításait, az alábbi lépéseket követve:
1. Futtassa a Firefox, és adja meg `about:config` a böngésző címsorába. Hagyja figyelmen kívül belőle értesítéseket, amelyek akkor jelennek meg.
2. Keresse meg a **network.negotiate-auth.trusted-URI-azonosítók** beállítás. Ez a beállítás a Firefox a megbízható helyek a Kerberos-hitelesítést sorolja fel.
3. Kattintson a jobb gombbal, és válassza ki **módosítás**.
4. Adja meg https://autologon.microsoftazuread-sso.com a mezőben.
5. Válassza ki **OK** és a böngészőben nyissa meg újra.

#### <a name="safari-mac-os"></a>Safari (Mac OS)

Győződjön meg arról, hogy a Mac OS futtató számítógép egy tartományhoz az Azure AD-e. Csatlakozás az Azure AD, lásd: [ajánlott eljárások az Active Directory integrálása OS X](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform)

Ha felülbírálva a [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) vagy a [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) házirend-beállítások a környezetben, győződjön meg arról, az Azure AD URL-cím hozzáadása (https://autologon.microsoftazuread-sso.com) is számukra.

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (Mac OS csak)

Google Chrome a Mac OS és más nem Windows platformokon, tekintse meg a [a króm házirend Projektlista](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) az engedélyezési lista hogy az Azure AD URL-címe hogyan integrált hitelesítés olvashat.

Külső Active Directory csoportházirend-bővítmények számára, hogy az Azure AD URL-cím Firefox és Google Chrome Macintosh-felhasználók a használata, ez a cikk hatókörén kívül.

#### <a name="known-browser-limitations"></a>A böngésző ismert korlátozásai

Privát böngészés módban a Firefox és Edge böngésző zökkenőmentes SSO nem működik. Is nem működik az Internet Explorer böngészőben fokozott védett módban fut. Ha.

## <a name="step-4-test-the-feature"></a>4. lépés: A szolgáltatás tesztelése

A szolgáltatás adott felhasználó tesztelése, gondoskodjon arról, hogy a következő feltételek teljesülnek:
  - A felhasználó bejelentkezik vállalati eszköz.
  - Az eszköz az Active Directory-tartomány tagja.
  - Az eszköz a tartományvezérlő (DC) közvetlen kapcsolattal rendelkezik, a vállalati vezetékes vagy vezeték nélküli hálózat vagy távelérési kapcsolatot, például a VPN-kapcsolaton keresztül.
  - Rendelkezik [megkezdődött a szolgáltatás](##step-3-roll-out-the-feature) csoportházirenden keresztül a felhasználóhoz.

A forgatókönyv, ahol a felhasználó beírja, csak a felhasználónév, de nem a jelszó ellenőrzése
   - Jelentkezzen be https://myapps.microsoft.com/ egy új titkos böngésző-munkamenetben.

Tesztelje a forgatókönyvet, ahol a felhasználó nem rendelkezik a felhasználónév vagy jelszó megadását, használja az alábbi lépések egyikét: 
   - Jelentkezzen be https://myapps.microsoft.com/contoso.onmicrosoft.com egy új titkos böngésző-munkamenetben. Cserélje le *contoso* a bérlő névvel.
   - Jelentkezzen be https://myapps.microsoft.com/contoso.com egy új titkos böngésző-munkamenetben. Cserélje le *contoso.com* a tenant egy ellenőrzött tartomány (nem összevont tartományhoz).

## <a name="step-5-roll-over-keys"></a>5. lépés: A kulcs váltása

2. lépésben az Azure AD Connect számítógépfiókot hoz létre (az Azure AD közti) amelyiken engedélyezte a zökkenőmentes SSO minden Active Directory-erdőben. További tudnivalókért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés: technikai mélyreható](active-directory-aadconnect-sso-how-it-works.md). A nagyobb biztonság érdekében azt javasoljuk, hogy Ön rendszeresen váltása a Kerberos visszafejtési kulcs esetében a számítógépfiókok. Kulcsok váltása kapcsolatos útmutatásért lásd: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakran ismételt kérdések](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Végezze el ezt a lépést nem kell _azonnal_ a szolgáltatás engedélyezése után. Váltása a Kerberos-visszafejtési kulcsok 30 naponta legalább egyszer.

## <a name="next-steps"></a>További lépések

- [Műszaki mélyreható](active-directory-aadconnect-sso-how-it-works.md): a zökkenőmentes egyszeri bejelentkezés funkció működésének megismerése.
- [Gyakori kérdések](active-directory-aadconnect-sso-faq.md): kapcsolatos zökkenőmentes egyszeri bejelentkezést gyakran feltett kérdésekre adott válaszok.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-sso.md): Útmutató: a zökkenőmentes egyszeri bejelentkezés funkció kapcsolatos gyakori problémák megoldásához.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory fórumán használja a következő fájl új frissítéseiről.
