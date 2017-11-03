---
title: "Az Azure AD-áteresztő hitelesítés – gyors üzembe helyezési |} Microsoft Docs"
description: "Ez a cikk ismerteti a Ismerkedés az Azure Active Directory (Azure AD) áteresztő hitelesítés."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
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
ms.openlocfilehash: cbedb87722d1c230f3b8003cadd069947881f25d
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Az Azure Active Directory átmenő hitelesítést: Gyors üzembe helyezési

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Azure AD áteresztő hitelesítés központi telepítése

Az Azure Active Directory (Azure AD) áteresztő hitelesítés lehetővé teszi, hogy a felhasználók számára a helyszíni és a felhőalapú alkalmazások azonos jelszóval bejelentkezni. Felhasználók akkor jelentkezik be a jelszavuk közvetlenül a helyszíni Active Directory általi érvényesítésével.

>[!IMPORTANT]
>Ha használja a szolgáltatás előzetes keresztül, győződjön meg arról, hogy az hitelesítési ügynökök preview verzióinak frissítése az utasításokat követve [Itt](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Áteresztő hitelesítés telepítése a következő lépések követésével kell:

## <a name="step-1-check-prerequisites"></a>1. lépés: Követelmények ellenőrzésének megismétlése

Győződjön meg arról, hogy a következő előfeltételeket:

### <a name="on-the-azure-active-directory-admin-center"></a>Az az Azure Active Directory felügyeleti központban

1. Hozzon létre egy kizárólag felhőalapú globális rendszergazdai fiókot az Azure AD-bérlő. Ezzel a módszerrel kezelheti a bérlője konfigurációját kell a helyszíni szolgáltatások sikertelen, vagy már nem érhető el. További tudnivalók [csak felhőalapú globális rendszergazdai fiókot hozzá](../active-directory-users-create-azure-portal.md). Ez a lépés elvégzése fontos annak érdekében, hogy Ön nem ártatlan tévedéssel a bérlő.
2. Vegyen fel egy vagy több [egyéni tartomány neve](../active-directory-add-domain.md) az Azure AD-bérlő. A felhasználók jelentkezzen be a tartomány neveinek egyike.

### <a name="in-your-on-premises-environment"></a>A helyszíni környezetben

1. A Windows Server 2012 R2 rendszerű kiszolgáló azonosítani vagy újabb, az Azure AD Connect futtatására. Adja hozzá a kiszolgálót a felhasználókat, amelyeknek a jelszavánál kell érvényesíteni eltérő AD-erdőben.
2. Telepítse a [az Azure AD Connect legújabb verziójának](https://www.microsoft.com/download/details.aspx?id=47594) a kiszolgálón az előző lépésben azonosított. Ha már rendelkezik az Azure AD Connect fut, győződjön meg arról, hogy a verzió 1.1.644.0 vagy újabb.

    >[!NOTE]
    >Az Azure AD Connect-verziók 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0 kapcsolatos probléma lehet **Jelszókivonat-szinkronizálást**. Ha Ön _nem_ kíván használni a Jelszókivonat-szinkronizálást átmenő hitelesítéssel, olvassa el a [az Azure AD Connect kibocsátási megjegyzések](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) további.

3. További Windows Server 2012 R2 rendszerű kiszolgáló azonosítani vagy újabb verziót futtató önálló hitelesítési ügynök. A hitelesítési ügynök verzióját kell lennie a 1.5.193.0 vagy újabb. Ehhez a kiszolgálóhoz szükséges bejelentkezési kérelmek magas rendelkezésre állásának biztosításához. Adja hozzá a kiszolgálót a felhasználókat, amelyeknek a jelszavánál kell érvényesíteni eltérő AD-erdőben.
4. Ha a kiszolgálók és az Azure AD között tűzfal van, a következő elemek konfigurálása szeretné:
   - Győződjön meg arról, hogy a hitelesítési ügynökök tehet **kimenő** kérelmeket az Azure AD a következő portokon keresztül:
   
   | Portszám | Hogyan használja fel azokat |
   | --- | --- |
   | **80** | Az SSL-tanúsítvány érvényesítése közben letöltése a tanúsítvány-visszavonási listákat (CRL) |
   | **443** | A szolgáltatás minden kimenő kommunikáció |
   
   Ha a tűzfal felhasználók helye szerint szabályokat érvényesíti, nyissa meg ezeket a portokat, a forgalom hálózati szolgáltatásként futó Windows-szolgáltatások.
   - Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezett, az engedélyezett kapcsolatok  **\*. msappproxy.net** és  **\*. servicebus.windows.net**. Ha nem, a hozzáférést [Azure DataCenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653), amely hetente frissíteni.
   - A hitelesítési ügynökök kell elérniük **login.windows.net** és **login.microsoftonline.com** kezdeti regisztrációs, ezért nyissa meg a tűzfal, valamint az URL-címeket a.
   - A tanúsítvány érvényesítése a következő URL-címek feloldása: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** és  **www.microsoft.com:80**. Ezért előfordulhat, hogy már nincs zárolva URL-URL-tanúsítvány érvényesítése más Microsoft-termékekkel használ.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>2. lépés: (Opcionális) az Exchange ActiveSync-támogatás engedélyezése

Kövesse ezeket az utasításokat az Exchange ActiveSync-támogatás engedélyezése:

1. Használjon [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) a következő parancsot:
```
Get-OrganizationConfig | fl per*
```

2. Ellenőrizze a `PerTenantSwitchToESTSEnabled` beállítást. Ha az érték **igaz**, a bérlő helyesen van konfigurálva – ez általában a legtöbb ügyfél esetében fordul elő. Ha az érték **hamis**, a következő parancsot:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Ellenőrizze, hogy az értékét a `PerTenantSwitchToESTSEnabled` most beállítása **igaz**. Várjon egy órát, mielőtt továbblép a következő lépéssel.

Ha probléma merül fel ezzel a lépéssel nyomtatott oldallal, ellenőrizze a [hibaelhárítási útmutatója](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues) további információt.

## <a name="step-3-enable-the-feature"></a>3. lépés: A funkció engedélyezése

Áteresztő hitelesítés használatával engedélyezhető [az Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Áteresztő hitelesítés az Azure AD Connect elsődleges vagy egy átmeneti kiszolgálón engedélyezhető. Javasoljuk, hogy engedélyezné az elsődleges kiszolgálóról.

Ha először telepíti az Azure AD Connect, válassza ki azt a [egyéni telepítési útvonal](active-directory-aadconnect-get-started-custom.md). : A **felhasználói bejelentkezés** lapon, válassza ki **áteresztő hitelesítés** , a bejelentkezéshez. A művelet sikeresen befejeződött áteresztő hitelesítés telepítve van egy ügynök és az Azure AD Connect ugyanazon a kiszolgálón. Emellett az áteresztő hitelesítés engedélyezve van a tenant.

![Az Azure AD Connect - felhasználói bejelentkezés](./media/active-directory-aadconnect-sso/sso3.png)

Ha már telepítette az Azure AD Connect (használja a [Expressz telepítés](active-directory-aadconnect-get-started-express.md) vagy a [egyéni telepítési](active-directory-aadconnect-get-started-custom.md) elérési útja), jelölje be **felhasználói bejelentkezés módosítása** feladat Azure ad-val Csatlakozás, és kattintson a **következő**. Válassza ki **áteresztő hitelesítés** , a bejelentkezéshez. A művelet sikeresen befejeződött egy áteresztő hitelesítés ügynök telepítve van az Azure AD Connect ugyanazon a kiszolgálón, és a szolgáltatás engedélyezve van a tenant.

![Az Azure AD Connect - módosítás felhasználói bejelentkezés](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Átmenő hitelesítést egy olyan bérlői szintű szolgáltatás. Bejelentkezés a felhasználók több bekapcsolását befolyásolja _összes_ a felügyelt tartományok az Ön bérelt szolgáltatásának. Ha átmenő hitelesítés az AD FS vált, azt javasoljuk, hogy legalább 12 órában az AD FS infrastruktúra leállítása előtt várja – a várakozási idő annak érdekében, hogy felhasználók is tartsa próbál bejelentkezni az Exchange ActiveSync átmenet közben.

## <a name="step-4-test-the-feature"></a>4. lépés: A szolgáltatás tesztelése

Következő lépések követésével ellenőrizze, hogy engedélyezte áteresztő hitelesítés megfelelően:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő számára a globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali navigációs.
3. Válassza ki **az Azure AD Connect**.
4. Ellenőrizze, hogy a **áteresztő hitelesítés** funkció jelenít meg, mint a **engedélyezve**.
5. Válassza ki **áteresztő hitelesítés**. Ezen a panelen megjeleníti a kiszolgálók, ahol telepítve van-e a hitelesítés ügynök.

![Az Azure Active Directory felügyeleti központ bemutatása – az Azure AD Connect panel](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Az Azure Active Directory felügyeleti központ – áteresztő hitelesítés panel](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Ebben a szakaszban található összes felügyelt tartomány az Ön bérelt szolgáltatásának felhasználók bejelentkezhetnek áteresztő hitelesítés használatával. Összevont tartományokban lévő felhasználók azonban továbbra is bejelentkezhet az Active Directory összevonási szolgáltatások (AD FS) vagy egy másik összevonás-szolgáltatóként, amelyet korábban konfigurált. Összevont kezelését a tartományhoz át az adott tartomány összes felhasználó indul el automatikusan jelentkezik be, hogy átmenő hitelesítést használ. Csak felhőalapú felhasználók nem érintettek az áteresztő hitelesítés szolgáltatás.

## <a name="step-5-ensure-high-availability"></a>5. lépés: Magas rendelkezésre állásának biztosításához.

Ha azt tervezi, áteresztő hitelesítés telepítése éles környezetben, telepítenie kell a hitelesítési ügynök önálló. A második hitelesítési ügynök telepítése a kiszolgálón _más_ a egy fut az Azure AD Connect és az első hitelesítési ügynök. Ez a beállítás akkor magas rendelkezésre állást biztosít a bejelentkezési kérelmek. Kövesse ezeket az utasításokat egy önálló hitelesítési ügynök központi telepítése:

1. **A hitelesítési ügynök a legújabb verzió letöltéséhez (1.5.193.0 verzió vagy újabb)**: Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali navigációs.
3. Válassza ki **az Azure AD Connect** , majd **áteresztő hitelesítés**. Válassza ki **letöltési ügynök**.
4. Kattintson a **feltételek elfogadásának & Letöltés** gombra.
5. **Telepítse a legújabb verzióját a hitelesítési ügynök**: a végrehajtható fájl az előző lépésben letöltött futtatásához. Adja meg a bérlő globális rendszergazda hitelesítő adatait, amikor a rendszer kéri.

![Az Azure Active Directory felügyeleti központ – hitelesítési ügynök letöltése gomb](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Az Azure Active Directory felügyeleti központ - ügynök letöltése panel](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Emellett letöltheti a hitelesítési ügynök a [Itt](https://aka.ms/getauthagent). Győződjön meg arról, hogy tekintse át és fogadja el a hitelesítési ügynök [szolgáltatási szerződését](https://aka.ms/authagenteula) _előtt_ telepíti azt.

## <a name="next-steps"></a>Következő lépések
- [**Intelligens zárolás** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -intelligens zárolás konfigurálása képességet a bérlő a felhasználói fiókok védelme.
- [**Aktuális korlátozások** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [**Műszaki mélyreható** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-pass-through-authentication-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**Biztonsági mélyreható** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -részletes műszaki további információ a szolgáltatásról.
- [**Az Azure AD zökkenőmentes SSO** ](active-directory-aadconnect-sso.md) -további információ a kiegészítő funkció.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
