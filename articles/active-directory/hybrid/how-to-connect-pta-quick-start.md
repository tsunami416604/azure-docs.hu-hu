---
title: Az Azure AD átmenő hitelesítés – gyors üzembe helyezési |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezdheti el az Azure Active Directory (Azure AD) átmenő hitelesítés.
services: active-directory
keywords: Az Azure AD Connect az átmenő hitelesítés, Active Directory telepítése szükséges összetevők SSO, Azure AD egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 95083ec1d909333596fd36ad998022778a4f9ec9
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582742"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Az Azure Active Directory átmenő hitelesítés: Gyors útmutató

## <a name="deploy-azure-ad-pass-through-authentication"></a>Üzembe helyezése az Azure AD átmenő hitelesítés

Az Azure Active Directory (Azure AD) átmenő hitelesítés lehetővé teszi, hogy a felhasználókat, hogy jelentkezzen be a helyszíni és felhőalapú alkalmazások is ugyanazt a jelszót. Az átmenő hitelesítés közvetlenül a helyi Active Directorybeli jelszavuk érvényesítésével felhasználó jelentkezik be.

>[!IMPORTANT]
>Ha az Active Directory összevonási szolgáltatások (vagy más összevonási technológiákkal) átmenő hitelesítésre migrál, javasoljuk, hogy kövesse a részletes üzembe helyezési útmutató, közzétett [Itt](https://aka.ms/adfstoPTADPDownload).

Kövesse ezeket az utasításokat az átmenő hitelesítés telepítése a bérlő:

## <a name="step-1-check-the-prerequisites"></a>1. lépés: Az Előfeltételek ellenőrzése

Győződjön meg arról, hogy az alábbi előfeltételek teljesülnek.

### <a name="in-the-azure-active-directory-admin-center"></a>Az Azure Active Directory felügyeleti központban

1. Csak felhőalapú globális rendszergazdai fiók létrehozása az Azure AD-bérlőre. Ily módon kezelheti a bérlője konfigurációját kell a helyszíni szolgáltatások sikertelen vagy elérhetetlenné válik. Ismerje meg [hozzáadása egy csak felhőalapú globális rendszergazdai fiókkal](../active-directory-users-create-azure-portal.md). Ez a lépés befejezése fontos győződjön meg arról, hogy ne zárja ki a bérlő.
2. Adjon hozzá egy vagy több [egyéni tartománynevek](../active-directory-domains-add-azure-portal.md) az Azure AD-bérlővel. A felhasználók ezek tartománynevek egyik bejelentkezhet.

### <a name="in-your-on-premises-environment"></a>A helyszíni környezetben

1. Azonosítsa a Windows Server 2012 R2 rendszerű kiszolgáló vagy futtatása az Azure AD Connect használatával. Ha nincs engedélyezve már, [engedélyezze a TLS 1.2 a kiszolgálón](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Adja hozzá a kiszolgálót a felhasználókat, amelyeknek a jelszava érvényesítenie kell ugyanabban az Active Directory erdőben.
2. Telepítse a [az Azure AD Connect legújabb verziójának](https://www.microsoft.com/download/details.aspx?id=47594) a kiszolgálón, az előző lépésben azonosított. Ha már rendelkezik Azure AD Connect fut, győződjön meg arról, hogy a verzió 1.1.750.0 vagy újabb.

    >[!NOTE]
    >Az Azure AD Connect-verziók 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0 van a Jelszókivonat-szinkronizálás kapcsolatos probléma. Ha Ön _nem_ kívánja használni a Jelszókivonat-szinkronizálás az átmenő hitelesítéssel, olvassa el a [kibocsátási megjegyzések az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Egy vagy több további kiszolgálók azonosításához (Windows Server 2012 R2 rendszerű vagy újabb verzió, a TLS 1.2 engedélyezve), különálló hitelesítési ügynökök futtathatja. Ezek a további kiszolgálók a kérelmet, jelentkezzen be a magas rendelkezésre állás biztosításához szükségesek. A kiszolgálókat hozzáadja a felhasználókat, amelyeknek a jelszava érvényesítenie kell ugyanabban az Active Directory erdőben.

    >[!IMPORTANT]
    >Éles környezetben azt javasoljuk, hogy rendelkezik-e legalább 3 hitelesítési ügynökök futtassa a bérlő. 12 hitelesítési ügynökök bérlőnként rendszer korlátozva van. Ajánlott eljárásként kezeljük az összes olyan kiszolgálóalkalmazást futtató hitelesítési ügynökök, a Tier 0 rendszerek és (lásd: [referencia](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Ha egy a kiszolgálók és az Azure AD között tűzfal található, adja meg a következő elemek:
   - Győződjön meg arról, hogy a hitelesítési ügynökök kezdeményezhetik *kimenő* kéréseket az Azure AD az alábbi portokon keresztül:

    | Portszám | Hogyan használja fel azokat |
    | --- | --- |
    | **80** | A visszavont tanúsítványok listájának (CRL) letölti az SSL-tanúsítvány érvényesítése közben |
    | **443** | Kezeli a szolgáltatással folytatott minden kimenő kommunikáció |
    | **8080-as** (nem kötelező) | Hitelesítési ügynökök jelentik az állapotukat 8080,-as porton tíz percenként, ha a 443-as port nem érhető el. Ez az állapot az Azure AD portálon jelenik meg. 8080-as porton van _nem_ használt felhasználói bejelentkezéseket. |

    Ha a tűzfal szabályok alapján származó kikényszeríti, nyissa meg ezeket a portokat, a forgalom hálózati szolgáltatásként futó Windows-szolgáltatások.
   - Ha a tűzfal vagy proxy lehetővé teszi a DNS engedélyezéskor engedélyezett kapcsolatokat  **\*. msappproxy.net** és  **\*. servicebus.windows.net**. Való hozzáférés engedélyezése, ha nem, akkor a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653), amely hetente frissül.
   - A hitelesítési ügynökök hozzáférésre van szükségük **login.windows.net** és **login.microsoftonline.com** kezdeti regisztráció. Nyissa meg a tűzfal, valamint az URL-címeket.
   - A tanúsítványok ellenőrzését, a következő URL-címek feloldása: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, és  **www.microsoft.com:80**. Mivel az URL-címek szolgálnak más Microsoft-termékekkel, előfordulhat, hogy már ezen URL-címek feloldva a tanúsítványok ellenőrzését.

## <a name="step-2-enable-the-feature"></a>2. lépés: A funkció engedélyezése

Átmenő hitelesítés keresztül engedélyezése [az Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Az átmenő hitelesítés az Azure AD Connect elsődleges vagy átmeneti kiszolgálón engedélyezheti. Azt javasoljuk, hogy engedélyezi azt az elsődleges kiszolgálóról. Ha hoz létre egy Azure AD Connectet átmeneti kiszolgálót a jövőben, **kell** válassza a bejelentkezés beállításaként az átmenő hitelesítés továbbra is; a program egy másik lehetőség kiválasztásával **letiltása** A bérlői és az elsődleges kiszolgálón a beállítás felülbírálása az átmenő hitelesítés.

Ha először telepíti az Azure AD Connect, válassza ki a [egyéni telepítési útvonal](how-to-connect-install-custom.md). Jelenleg a **felhasználói bejelentkezés** lapon a **átmenő hitelesítés** , a **bejelentkezési módszert**. A sikeres telepítést, a egy átmenő hitelesítési ügynök telepítve van az Azure AD Connect ugyanazon a kiszolgálón. Emellett az átmenő hitelesítés szolgáltatás engedélyezve van a bérlőn.

![Az Azure AD Connect: Felhasználói bejelentkezés](./media/how-to-connect-pta-quick-start/sso3.png)

Ha már telepítette az Azure AD Connect használatával a [Expressz telepítés](how-to-connect-install-express.md) vagy a [egyéni telepítési](how-to-connect-install-custom.md) elérési útját, válassza ki a **felhasználói bejelentkezés módosítása** feladat az Azure ad-ben Csatlakozzon, és válassza ki **tovább**. Válassza ki **átmenő hitelesítés** bejelentkezési módszert. A művelet sikeresen befejeződött egy átmenő hitelesítési ügynök telepítve van az Azure AD Connect ugyanarra a kiszolgálóra, és a szolgáltatás engedélyezve van a bérlő.

![Az Azure AD Connect: Felhasználói bejelentkezés módosítása](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Az átmenő hitelesítés egy olyan bérlői szintű szolgáltatás. Bekapcsolását, hatással van a bejelentkezés a felhasználók több _összes_ a felügyelt tartomány a bérlőben. Ha az átmenő hitelesítés való váltás az Active Directory összevonási szolgáltatások (AD FS), az ellenőrzést, várjon legalább 12 órán át az AD FS-infrastruktúra leállítása előtt. A várakozási idő, hogy győződjön meg arról, hogy felhasználók is folyamatosan jelentkezik be az Exchange ActiveSync az áttérés során. Az áttelepítés az AD FS-ről átmenő hitelesítés további segítségért tekintse meg a részletes telepítési csomag közzétett [Itt](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>3. lépés: A szolgáltatás tesztelése

Kövesse az alábbi utasításokat, győződjön meg arról, hogy engedélyezte az átmenő hitelesítés megfelelően:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlőhöz tartozó globális rendszergazdai hitelesítő adatokkal.
2. Válassza ki **Azure Active Directory** a bal oldali panelen.
3. Válassza ki **az Azure AD Connect**.
4. Ellenőrizze, hogy a **átmenő hitelesítés** jelenik meg a szolgáltatás **engedélyezve**.
5. Válassza ki **átmenő hitelesítés**. A **átmenő hitelesítés** panel felsorolja a kiszolgálókat, amelyeken a hitelesítési ügynökök telepítve vannak-e.

![Az Azure Active Directory felügyeleti központ: az Azure AD Connect panel](./media/how-to-connect-pta-quick-start/pta7.png)

![Az Azure Active Directory felügyeleti központ: az átmenő hitelesítés panel](./media/how-to-connect-pta-quick-start/pta8.png)

Ezen a ponton a bérlő összes felügyelt tartományokban lévő felhasználók az átmenő hitelesítés használatával jelentkezhetnek be. Összevont tartományokban lévő felhasználók azonban továbbra is, az AD FS vagy egy másik összevonási szolgáltató, amelyet korábban konfigurált használatával bejelentkezni. Ha alakít át egy tartományt a felügyelt összevont, az adott tartomány összes felhasználó automatikusan indítsa el az átmenő hitelesítés használatával jelentkezik be. Az átmenő hitelesítés szolgáltatás nincs hatással a kizárólag felhőalapú felhasználói.

## <a name="step-4-ensure-high-availability"></a>4. lépés: A magas rendelkezésre állásának biztosításához

Ha azt tervezi, éles környezetben üzembe helyezése az átmenő hitelesítés, telepítenie kell a további önálló hitelesítési ügynökök. Ezek a hitelesítési ügynököt telepíteni ko _más_ , mint az egy futó Azure AD Connect. A telepítő felhasználói bejelentkezési kérelmek magas rendelkezésre állást biztosít.

>[!IMPORTANT]
>Éles környezetben azt javasoljuk, hogy rendelkezik-e legalább 3 hitelesítési ügynökök futtassa a bérlő. 12 hitelesítési ügynökök bérlőnként rendszer korlátozva van. Ajánlott eljárásként kezeljük az összes olyan kiszolgálóalkalmazást futtató hitelesítési ügynökök, a Tier 0 rendszerek és (lásd: [referencia](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Kövesse az alábbi utasításokat a hitelesítési ügynök szoftver letöltéséhez:

1. A hitelesítési ügynök legújabb verziójának letöltése (1.5.193.0 verzió vagy újabb), jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali panelen.
3. Válassza ki **az Azure AD Connect**válassza **átmenő hitelesítés**, majd válassza ki **-ügynök letöltése**.
4. Válassza ki a **feltételek elfogadása és letöltés** gombra.

![Az Azure Active Directory felügyeleti központ: Töltse le a hitelesítési ügynök gomb](./media/how-to-connect-pta-quick-start/pta9.png)

![Az Azure Active Directory felügyeleti központ: ablaktábla-ügynök letöltése](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Emellett közvetlenül is [töltse le a hitelesítési ügynök szoftver](https://aka.ms/getauthagent). Tekintse át és fogadja el a hitelesítési ügynök [szolgáltatási feltételeit](https://aka.ms/authagenteula) _előtt_ telepíti azt.

Kétféleképpen helyezhet üzembe egy önálló hitelesítési ügynök:

Először ezt megteheti interaktív módon csak a letöltött végrehajtható hitelesítési-ügynök fut, és a bérlő globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri megadásával.

A második hozzon létre, és a egy felügyelet nélküli telepítési parancsfájl futtatásához. Ez akkor hasznos, ha meg szeretné üzembe helyezésekor egyszerre több hitelesítési ügynök vagy hitelesítési ügynökök telepítése Windows-kiszolgálókon, amely nem rendelkezik a felhasználói felület engedélyezve van, vagy a távoli asztal nem érhetők el. Az alábbiakban az ezzel a módszerrel útmutatást:

1. Hitelesítési ügynök telepítéséhez a következő parancsot: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. A hitelesítési ügynök regisztrálhatja az szolgáltatás Windows PowerShell használatával. Hozzon létre egy PowerShell hitelesítő objektumot `$cred` egy globális rendszergazdai felhasználónevet és jelszót, amely tartalmazza a bérlő számára. Futtassa a következő parancsot, és cserélje le *\<felhasználónév\>* és  *\<jelszó\>*:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Lépjen a **C:\Program Files\Microsoft Azure AD Connect hitelesítési ügynökének** , és futtassa az alábbi szkriptet az a `$cred` létrehozott objektum:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>További lépések
- [Az AD FS át az átmenő hitelesítés](https://aka.ms/adfstoptadp) – egy részletes útmutató, amellyel áttelepíteni az átmenő hitelesítés az Active Directory összevonási szolgáltatások (vagy más összevonási technológiákkal).
- [Az intelligens zárolási](../authentication/howto-password-smart-lockout.md): ismerje meg, hogyan konfigurálhatja az intelligens zárolás funkciót a bérlő felhasználói fiókok védelmét.
- [Aktuális korlátozások](how-to-connect-pta-current-limitations.md): ismerje meg, milyen forgatókönyvekre az átmenő hitelesítés használata támogatott, és melyek nem.
- [Részletes technikai](how-to-connect-pta-how-it-works.md): az átmenő hitelesítési szolgáltatás működésének megismerése.
- [Gyakori kérdések](how-to-connect-pta-faq.md): választ találhat a gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): ismerje meg az átmenő hitelesítés szolgáltatás szolgáltatással kapcsolatos gyakori problémák megoldásához.
- [A biztonság részletes bemutatása](how-to-connect-pta-security-deep-dive.md): ismerje meg az átmenő hitelesítés szolgáltatás technikai információit.
- [Az Azure AD közvetlen egyszeri bejelentkezés](how-to-connect-sso.md): További információ a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory-fórumon használatával új funkcióra vonatkozó javaslata fájlt.
