---
title: Az Azure AD áteresztő hitelesítés – gyors üzembe helyezési |} Microsoft Docs
description: Ez a cikk ismerteti a Ismerkedés az Azure Active Directory (Azure AD) áteresztő hitelesítés.
services: active-directory
keywords: Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést.
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 365d9cde29292b40847ec2a0ac6f7d4011222f2c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028931"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Az Azure Active Directory átmenő hitelesítést: Gyors üzembe helyezési

## <a name="deploy-azure-ad-pass-through-authentication"></a>Az Azure AD-áteresztő hitelesítés központi telepítése

Az Azure Active Directory (Azure AD) áteresztő hitelesítés lehetővé teszi, hogy a felhasználókat, hogy jelentkezzen be a helyszíni és felhőalapú alkalmazásokat is ugyanazt a jelszót. Áteresztő hitelesítés-szal felhasználók a jelszavuk közvetlenül a helyszíni Active Directory általi érvényesítésével.

>[!IMPORTANT]
>Ha ez a funkció előzetes verzióval keresztül használ, győződjön meg arról, a hitelesítési ügynökök előzetes verziói frissít a utasításokat használatával [Azure Active Directory áteresztő hitelesítés: frissítési előzetes verzió Hitelesítési ügynökök](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Kövesse az alábbi utasításokat áteresztő hitelesítés telepítéséhez:

## <a name="step-1-check-the-prerequisites"></a>1. lépés: Az Előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételeket.

### <a name="in-the-azure-active-directory-admin-center"></a>Az Azure Active Directory felügyeleti központban

1. Hozzon létre egy kizárólag felhőalapú globális rendszergazdai fiókot az Azure AD-bérlő. Ezzel a módszerrel kezelheti a bérlője konfigurációját kell a helyszíni szolgáltatások sikertelen, vagy már nem érhető el. További tudnivalók [csak felhőalapú globális rendszergazdai fiókot hozzá](../active-directory-users-create-azure-portal.md). A lépés fontos annak érdekében, hogy Ön nem ártatlan tévedéssel a bérlő.
2. Vegyen fel egy vagy több [egyéni tartománynevek](../active-directory-domains-add-azure-portal.md) az Azure AD-bérlő. A felhasználók bejelentkezhetnek a tartomány neveinek egyike.

### <a name="in-your-on-premises-environment"></a>A helyszíni környezetben

1. Azonosítsa a Windows Server 2012 R2 rendszerű kiszolgáló, vagy később az Azure AD Connect futtatásához. Adja hozzá a kiszolgálót az Active Directory-erdőben, a felhasználók érvényesítenie kell a jelszavát.
2. Telepítse a [az Azure AD Connect legújabb verziójának](https://www.microsoft.com/download/details.aspx?id=47594) az előző lépésben azonosított a kiszolgálón. Ha már rendelkezik az Azure AD Connect fut, győződjön meg arról, hogy a verzió 1.1.644.0 vagy újabb.

    >[!NOTE]
    >Az Azure AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0 verziónál Jelszókivonat-szinkronizálást kapcsolatos hiba. Ha Ön _nem_ kíván használni a Jelszókivonat-szinkronizálást átmenő hitelesítéssel, olvassa el a [az Azure AD Connect kibocsátási megjegyzések](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Egy további kiszolgálóra azonosítása (Windows Server 2012 R2 rendszerű vagy újabb) hitelesítési ügynök önálló futtató. A hitelesítési ügynök verzióját kell lennie a 1.5.193.0 vagy újabb. A további kiszolgáló iránti kérelmek jelentkezzen be a magas rendelkezésre állásának biztosításához van szükség. Adja hozzá a kiszolgálót az Active Directory-erdőben, a felhasználók érvényesítenie kell a jelszavát.
4. Ha van tűzfal a kiszolgálók és az Azure AD között, a következő elemek konfigurálása:
   - Győződjön meg arról, hogy a hitelesítési ügynökök tehet *kimenő* kérelmeket az Azure AD a következő portokon keresztül:
   
    | Portszám | Hogyan használja fel azokat |
    | --- | --- |
    | **80** | A visszavont tanúsítványok listáját (CRL) letölti az SSL-tanúsítvány ellenőrzése során. |
    | **443** | Kezeli a szolgáltatással folytatott minden kimenő kommunikáció |
   
    Ha a tűzfal szabályok alapján származó érvényesíti, nyissa meg ezeket a portokat, a forgalom hálózati szolgáltatásként futó Windows-szolgáltatások.
   - Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezett, az engedélyezett kapcsolatok  **\*. msappproxy.net** és  **\*. servicebus.windows.net**. Ha nem, a hozzáférést a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653), amely hetente frissíteni.
   - A hitelesítési ügynökök kell elérniük **login.windows.net** és **login.microsoftonline.com** kezdeti regisztrációjához. Nyissa meg a tűzfal, valamint az URL-címeket.
   - A tanúsítvány érvényesítése a következő URL-címek feloldása: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, és  **www.microsoft.com:80**. Az URL-címek leellenőrizni a tanúsítvány más Microsoft-termékekkel használ. Már rendelkezik az URL-címek feloldva.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>2. lépés: (Opcionális) az Exchange ActiveSync-támogatás engedélyezése

Kövesse ezeket az utasításokat az Exchange ActiveSync-támogatás engedélyezése:

1. Használjon [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) a következő parancsot:
```
Get-OrganizationConfig | fl per*
```

2. Ellenőrizze a `PerTenantSwitchToESTSEnabled` beállítást. Ha az érték **igaz**, a bérlő helyesen van konfigurálva. Ez általában a helyzet a legtöbb felhasználó. Ha az érték **hamis**, a következő parancsot:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Ellenőrizze, hogy az értékét a `PerTenantSwitchToESTSEnabled` most beállítása **igaz**. Várjon egy órát, mielőtt továbblép a következő lépéssel.

Ha e lépés során problémákat akkor szembesülhetnek, ellenőrizze a [hibaelhárítási útmutatója](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>3. lépés: A funkció engedélyezése

Átmenő hitelesítést keresztül [az Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Átmenő hitelesítést az Azure AD Connect elsődleges vagy egy átmeneti kiszolgálón. Az elsődleges kiszolgálóról azt kell engedélyezni.

Először az Azure AD Connect telepítése, válassza ki a [egyéni telepítési útvonal](active-directory-aadconnect-get-started-custom.md). : A **felhasználói bejelentkezés** lapon, válassza ki **áteresztő hitelesítés** , a **bejelentkezés módját**. A művelet sikeres befejezését egy csatlakoztatott hitelesítési ügynök az Azure AD Connect ugyanazon a kiszolgálón telepítve van. Emellett az áteresztő hitelesítés engedélyezve van a tenant.

![Az Azure AD Connect: Felhasználói bejelentkezés](./media/active-directory-aadconnect-sso/sso3.png)

Ha már telepítette az Azure AD Connect használatával a [Expressz telepítés](active-directory-aadconnect-get-started-express.md) vagy a [egyéni telepítési](active-directory-aadconnect-get-started-custom.md) elérési útját, válassza ki a **felhasználói bejelentkezés módosítása** feladat Azure ad-val Csatlakozás, és válassza ki **következő**. Válassza ki **áteresztő hitelesítés** bejelentkezési módszert. A művelet sikeresen befejeződött az Azure AD Connect ugyanazon a kiszolgálón telepítve van egy áteresztő hitelesítés ügynök és a szolgáltatás engedélyezve van a tenant.

![Az Azure AD Connect: Módosítsa a felhasználói bejelentkezés](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Átmenő hitelesítést egy olyan bérlői szintű szolgáltatás. Bekapcsolja hatással van a bejelentkezést a felhasználók több _összes_ a felügyelt tartományok az Ön bérelt szolgáltatásának. Áteresztő hitelesítés az Active Directory összevonási szolgáltatásokban (AD FS) vált, ha meg kell várnia az AD FS infrastruktúra leállítása előtt legalább 12 óra. A várakozási idő az annak érdekében, hogy felhasználók is tartsa próbál bejelentkezni az Exchange ActiveSync az áttérés során.

## <a name="step-4-test-the-feature"></a>4. lépés: A szolgáltatás tesztelése

Következő lépések követésével ellenőrizze, hogy engedélyezte áteresztő hitelesítés megfelelően:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő számára a globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali ablaktáblán.
3. Válassza ki **az Azure AD Connect**.
4. Ellenőrizze, hogy a **áteresztő hitelesítés** jelenik meg a szolgáltatás **engedélyezve**.
5. Válassza ki **áteresztő hitelesítés**. A **áteresztő hitelesítés** ablaktábla listázza a kiszolgálók, amelyeken a hitelesítési ügynökök telepítve vannak-e.

![Az Azure Active Directory felügyeleti központ: az Azure AD Connect ablaktábla](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Az Azure Active Directory felügyeleti központ: áteresztő hitelesítés ablaktábla](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Ebben a szakaszban az Ön bérelt szolgáltatásának minden felügyelt tartományok áteresztő hitelesítés használatával jelentkezhetnek be. Azonban az összevont tartományt felhasználók továbbra is jelentkezzen be az AD FS vagy egy másik, amelyet korábban konfigurált összevonás-szolgáltatóként. Összevont kezelését a tartományhoz át az adott tartomány összes felhasználó indul el automatikusan áteresztő hitelesítés használatával jelentkezik be. Az áteresztő hitelesítés szolgáltatás csak felhőalapú felhasználók nincs hatással.

## <a name="step-5-ensure-high-availability"></a>5. lépés: Magas rendelkezésre állásának biztosításához.

Ha azt tervezi, áteresztő hitelesítés telepítése éles környezetben, telepítenie kell legalább egy további önálló hitelesítési ügynök. A hitelesítés az ügynököket telepíti a kiszolgálókra _más_ a egy fut az Azure AD Connect-nál. A telepítő felhasználó bejelentkezési kérelmek magas rendelkezésre állást biztosít.

Kövesse az alábbi utasításokat a hitelesítési ügynök szoftver letöltése:

1. A hitelesítési ügynök a legújabb verziójának letöltéséhez (1.5.193.0 verzió vagy újabb), jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. Válassza ki **Azure Active Directory** a bal oldali ablaktáblán.
3. Válassza ki **az Azure AD Connect**, jelölje be **áteresztő hitelesítés**, majd válassza ki **ügynök letöltése**.
4. Válassza ki a **feltételek elfogadásának & Letöltés** gombra.

![Az Azure Active Directory felügyeleti központ: hitelesítési ügynök letöltése gomb](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Az Azure Active Directory felügyeleti központ: ügynök letöltése ablaktábla](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Közvetlenül is letöltheti a hitelesítési ügynökszoftver [Itt](https://aka.ms/getauthagent). Tekintse át és fogadja el a hitelesítési ügynök [szolgáltatási szerződését](https://aka.ms/authagenteula) _előtt_ telepíti azt.

Önálló hitelesítési ügynök telepítése két módja van:

Először is elvégezheti interaktív módon csak a letöltött végrehajtható hitelesítési-ügynök fut, és a bérlő globális rendszergazda hitelesítő adatait, amikor a rendszer kéri megadásával.

Ezután hozzon létre, és egy felügyelet nélküli telepítési parancsfájl futtatása. Ez akkor hasznos, ha szeretné telepíteni egyszerre több hitelesítési ügynök, vagy hitelesítési ügynökök telepítése Windows-kiszolgálók, amely nem rendelkezik a felhasználói felület engedélyezve van, vagy a távoli asztal nem érhetők el. Az alábbiakban ezt a módszert használja a útmutatást:

1. Hitelesítési ügynök telepítéséhez a következő parancsot: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. A hitelesítési ügynök regisztrálhatja az szolgáltatás Windows PowerShell használatával. Hozzon létre egy PowerShell hitelesítő objektumot `$cred` egy globális rendszergazdai jogosultságú felhasználónevet és jelszót, amely tartalmazza a bérlő számára. A következő parancsot, cseréje *\<felhasználónév\>* és  *\<jelszó\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Ugrás a **C:\Program Files\Microsoft Azure AD Connect hitelesítési ügynök** , és futtassa a következő parancsfájl a `$cred` létrehozott objektum:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>További lépések
- [Intelligens zárolás](../authentication/howto-password-smart-lockout.md): megtudhatja, hogyan konfigurálja az intelligens zárolás funkció a bérlő felhasználói fiókok védelme.
- [Aktuális korlátozások](active-directory-aadconnect-pass-through-authentication-current-limitations.md): megtudhatja, mely forgatókönyvek támogatottak az áteresztő hitelesítés, és melyek nem.
- [Műszaki mélyreható](active-directory-aadconnect-pass-through-authentication-how-it-works.md): a csatlakoztatott hitelesítési szolgáltatás működésének megismerése.
- [Gyakori kérdések](active-directory-aadconnect-pass-through-authentication-faq.md): gyakran feltett kérdésekre adott válaszok.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Útmutató: az áteresztő hitelesítés szolgáltatás kapcsolatos gyakori problémák megoldásához.
- [Biztonsági mélyreható](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): ismerje meg az áteresztő hitelesítés szolgáltatás műszaki információit.
- [Az Azure AD zökkenőmentes SSO](active-directory-aadconnect-sso.md): további információk a kiegészítő funkció.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory fórumán használja a következő fájl új frissítéseiről.

