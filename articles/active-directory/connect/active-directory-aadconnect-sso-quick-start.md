---
title: "Az Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – első lépések |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan az Azure Active Directory zökkenőmentes egyszeri bejelentkezést lásson."
services: active-directory
keywords: "Mi az Azure AD Connect telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 8975a82c5573cc0c284e1fc76cd0ef2c19fbbd72
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés: első lépések

## <a name="how-to-deploy-seamless-sso"></a>Zökkenőmentes SSO központi telepítése

Azure Active Directory zökkenőmentes egyszeri bejelentkezést (az Azure AD zökkenőmentes SSO) amikor kapcsolódnak a vállalati asztali számítógép kapcsolódik a vállalati hálózathoz a felhasználók bejelentkezésekor automatikusan. A felhasználók egyszerű hozzáférést biztosít a felhőalapú alkalmazások anélkül, hogy semmilyen további helyszíni összetevőt.

Zökkenőmentes SSO telepítéséhez kövesse az alábbi lépéseket kell:

## <a name="step-1-check-prerequisites"></a>1. lépés: Követelmények ellenőrzésének megismétlése

Győződjön meg arról, hogy a következő előfeltételeket:

1. Az Azure AD Connect-kiszolgáló beállítása: Ha [áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication.md) legyen a bejelentkezési módszer nincs további előfeltétel-ellenőrzésre szükség. Ha [Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-synchronization.md) legyen a bejelentkezési módszer, és ha az Azure AD Connect és az Azure AD között tűzfal, ellenőrizze, hogy:
- Verziók 1.1.644.0 használ vagy újabb, az Azure AD Connect. 
- Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezett, az engedélyezett kapcsolatok  **\*. msappproxy.net** URL-címek 443-as porton keresztül. Ha nem, a hozzáférést [Azure DataCenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653), amely hetente frissíteni. Ezt az előfeltételt csak engedélyezi a szolgáltatást, nem a tényleges felhasználói bejelentkezések esetén alkalmazható.

    >[!NOTE]
    >Az Azure AD Connect-verziók 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0 Jelszókivonat-szinkronizálást kapcsolatos probléma lehet. Ha Ön _nem_ kíván használni a Jelszókivonat-szinkronizálást átmenő hitelesítéssel, olvassa el a [az Azure AD Connect kibocsátási megjegyzések](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) további.

2. Minden AD-erdőben, amely szinkronizálja az Azure AD (Azure AD Connect használatával), és amelynek a felhasználók számára zökkenőmentes SSO engedélyezni szeretné tartományi rendszergazdai hitelesítő adatokra van szükség.

## <a name="step-2-enable-the-feature"></a>2. lépés: A funkció engedélyezése

Zökkenőmentes SSO használatával engedélyezhető [az Azure AD Connect](active-directory-aadconnect.md).

Ha az Azure AD Connect új példánya, válassza ki a [egyéni telepítési útvonal](active-directory-aadconnect-get-started-custom.md). A "Felhasználói bejelentkezés" lapon jelölje be a "Engedélyezése egyszeri bejelentkezéshez" beállítást.

![Az Azure AD Connect - felhasználói bejelentkezés](./media/active-directory-aadconnect-sso/sso8.png)

Ha már rendelkezik egy Azure AD Connect telepítése, válassza a "Módosítási felhasználói bejelentkezési oldalon" az Azure AD Connect, és kattintson a "Tovább gombra". Ezután jelölje be a "Engedélyezése egyszeri bejelentkezéshez" beállítást.

![Az Azure AD Connect - módosítás felhasználói bejelentkezés](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Kövesse a varázsló utasításait, amíg a "Engedélyezése egyszeri bejelentkezéshez" a lap. Tartományi rendszergazda hitelesítő adatai minden AD-erdőben, amely szinkronizálja az Azure AD (Azure AD Connect használatával), és amelynek a felhasználók számára zökkenőmentes SSO engedélyezni szeretné. 

A varázsló befejezése után a zökkenőmentes SSO engedélyezve van a tenant.

>[!NOTE]
> A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben, de a funkció engedélyezéséhez csak használható.

Következő lépések követésével ellenőrizze, hogy engedélyezte zökkenőmentes SSO megfelelően:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő számára a globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali navigációs.
3. Válassza ki **az Azure AD Connect**.
4. Ellenőrizze, hogy a **zökkenőmentes egyszeri bejelentkezést** funkció jelenít meg, mint a **engedélyezve**.

![Azure portál – az Azure AD Connect panel](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>3. lépés: A szolgáltatás megkezdik

Számára, hogy a szolgáltatás a felhasználók számára, a következő Azure AD URL-címek hozzáadása a felhasználói intranetes zóna beállításai az Active Directory csoportházirend segítségével kell:

- https://AutoLogon.microsoftazuread-sso.com
- https://aadg.Windows.NET.nsatc.NET

Továbbá szüksége ahhoz, hogy az Intranet zóna a házirend-beállítást (a csoportházirend használatával) "Beállítható a frissítések állapotsor parancsfájl segítségével" hívása.

>[!NOTE]
> Az alábbi utasítások csak működnek az Internet Explorer és a Google Chrome Windows (ha az Internet Explorer Megbízható helyek URL-osztanak azt). Olvassa el a következő részben Mozilla Firefox és a Google Chrome a Mac beállítása

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Miért van szüksége a felhasználói intranetes beállítások módosítása?

Alapértelmezés szerint a böngésző automatikusan számítja ki a megfelelő zónához (internetes vagy intranetes) URL-címről. Például http://contoso/ van leképezve az Intranet zónához, mivel http://intranet.contoso.com/ van rendelve az Internet zóna (mert az URL-cím pontot tartalmaz). Böngészők ne küldjön Kerberos-jegyek küldenek a felhővégpontnak – például a két Azure AD URL-címek – kivéve, ha az URL-cím kifejezetten a böngésző Intranet zónához.

### <a name="detailed-steps"></a>Részletes lépések

1. Nyissa meg a Csoportházirend kezelése eszközt.
2. Az egyes alkalmazott csoportházirend szerkesztése vagy a felhasználók. A jelen példában használjuk a **alapértelmezett tartományházirend**.
3. Navigáljon a **Felhasználó konfigurációja\Felügyeleti sablonok\Windows összetevők\Internet Explorer\Internet vezérlő Panel\Security lap** válassza **zónákhoz való társításának listája a hely**.
![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso6.png)
4. Engedélyezi a házirendet, és adja meg a következő értékeket (az Azure AD URL-címek amelyben továbbított Kerberos-jegyek) és adatok (*1* Intranet zóna jelzi) párbeszédpanel.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> Ha azt szeretné, ha ezek a felhasználók a megosztott számítógépeken - bejelentkezés nem engedélyezi a zökkenőmentes SSO - példányhoz, egyes felhasználók állítsa be a fenti értékeket *4*. Ez a művelet az Azure AD URL-címeket ad hozzá a Tiltott helyek zóna, és zökkenőmentes SSO folyamatosan meghiúsul.

5. Kattintson a **OK** és **OK** újra.
![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso7.png)
6. Navigáljon a **Felhasználó konfigurációja\Felügyeleti sablonok\Windows összetevők\Internet Explorer\Internet vezérlő Panel\Security Page\Intranet zóna** válassza **beállítható a frissítések állapotsor parancsfájlkeresztül**.
![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso11.png)
7. Engedélyezi a házirend-beállítást, és kattintson a **OK**.
![Egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Böngésző kapcsolatos szempontok

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox automatikusan nem használ Kerberos-hitelesítést. Minden felhasználó rendelkezik-e manuálisan az Azure AD URL-címek hozzáadása a Firefox beállításait, az alábbi lépéseket követve:
1. Futtassa a Firefox, és adja meg `about:config` a böngésző címsorába. Hagyja figyelmen kívül belőle értesítéseket, amelyek akkor jelennek meg.
2. Keresse meg a **network.negotiate-auth.trusted-URI-azonosítók** beállítás. Ez a beállítás a Firefox a megbízható helyek a Kerberos-hitelesítést sorolja fel.
3. Kattintson a jobb gombbal, és válassza a "Módosítás".
4. Adja meg "https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net" mezőjében.
5. Az "OK" gombra, majd nyissa meg a böngészőben.

#### <a name="safari-on-mac-os"></a>Mac OS Safari

Győződjön meg arról, hogy a Mac OS futtató gép AD egy tartományhoz. Kövesse az utasításokat, amelyek erre [Itt](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-on-mac-os"></a>Google Chrome Mac OS

Google Chrome a Mac OS és más nem Windows platformokon, tekintse meg a [Ez a cikk](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) integrált hitelesítés engedélyezett az Azure AD URL-címek olvashat.

Segítségével a külső Active Directory csoportházirend-bővítmények számára, hogy az Azure AD URL-címek Firefox és Google Chrome Mac-felhasználók nem érhető el ez a cikk.

#### <a name="known-browser-limitations"></a>A böngésző ismert korlátozásai

Privát böngészés módban a Firefox és Edge böngésző zökkenőmentes SSO nem működik. Is nem működik az Internet Explorer böngészőben fokozott védelmet módban fut. Ha.

>[!IMPORTANT]
>A Microsoft nemrég visszaállítása vizsgálja meg az ügyfél által jelentett problémák peremhálózati támogatása.

## <a name="step-4-test-the-feature"></a>4. lépés: A szolgáltatás tesztelése

A szolgáltatás adott felhasználó teszteléséhez győződjön meg arról, hogy _összes_ helyen vannak a következő feltételeknek:
  - A felhasználó egy vállalati eszközre jelentkezik be.
  - Az eszköz korábban csatlakoztatva lett az Active Directory (AD) tartományhoz.
  - Az eszköz számára a tartományvezérlőn (DC), vagy a vállalati vezetékes vagy vezeték nélküli hálózat vagy távelérési kapcsolatot, például a VPN-kapcsolaton keresztül közvetlen kapcsolattal rendelkezik.
  - Rendelkezik [megkezdődött a szolgáltatás](##step-3-roll-out-the-feature) csoportházirend használatával a felhasználóhoz.

A forgatókönyv, ahol a felhasználó beírja, csak a felhasználónév, de nem a jelszó ellenőrzése
- Jelentkezzen be a *https://myapps.microsoft.com/* egy új titkos böngésző-munkamenetben.

A tesztelje a forgatókönyvet, ahol a felhasználó nem rendelkezik a felhasználónév vagy jelszó megadását: 
- Jelentkezzen be a *https://myapps.microsoft.com/contoso.onmicrosoft.com* egy új titkos böngésző-munkamenetben. Cserélje le "*contoso*" a bérlő névvel.
- Jelentkezzen be vagy *https://myapps.microsoft.com/contoso.com* egy új titkos böngésző-munkamenetben. Cserélje le "*contoso.com*" a bérlő ellenőrzött tartomány (nem összevont tartományhoz).

## <a name="step-5-roll-over-keys"></a>5. lépés: A kulcs váltása

2. lépésben az Azure AD Connect számítógépfiókot hoz létre (az Azure AD közti) amelyiken engedélyezte a zökkenőmentes SSO minden AD-erdőben. Ismerje meg, a részletesebb [Itt](active-directory-aadconnect-sso-how-it-works.md). A nagyobb biztonság érdekében javasoljuk, hogy Ön rendszeresen váltása a Kerberos visszafejtési kulcs esetében a számítógépfiókok. Az utasítások alapján való váltása [Itt](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Végezze el ezt a lépést nem kell _azonnal_ a szolgáltatás engedélyezése után. Váltása a Kerberos-visszafejtési kulcsok legalább 30 nap.

## <a name="next-steps"></a>Következő lépések

- [Műszaki mélyreható](active-directory-aadconnect-sso-how-it-works.md) – Ez a funkció működésének megismerése.
- [Gyakran ismételt kérdések](active-directory-aadconnect-sso-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-sso.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
