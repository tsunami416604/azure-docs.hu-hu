---
title: Azure AD átmenő hitelesítés – gyorsindítás | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan lehet elkezdeni az Azure Active Directory (Azure AD) áthaladási hitelesítés.
services: active-directory
keywords: Azure AD Connect áthaladási hitelesítés, active directory telepítése, szükséges összetevők az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés hez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc45033cdf1bdaa6d4ecd6ab58cc7f90ff9c1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331421"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory átadó hitelesítése: Gyorsindítás

## <a name="deploy-azure-ad-pass-through-authentication"></a>Az Azure AD áthaladási hitelesítésének üzembe helyezése

Az Azure Active Directory (Azure AD) átadó hitelesítéslehetővé teszi a felhasználók számára, hogy jelentkezzen be mind a helyszíni és a felhőalapú alkalmazások segítségével ugyanazokat a jelszavakat. Az átadó hitelesítés úgy jelentkezik be a felhasználókba, hogy közvetlenül a helyszíni Active Directoryhoz ellenőrzik jelszavukat.

>[!IMPORTANT]
>Ha az AD FS-ről (vagy más összevonási technológiákról) átmenő hitelesítésre vándorol, javasoljuk, hogy kövesse [az itt](https://aka.ms/adfstoPTADPDownload)közzétett részletes telepítési útmutatónkat.

Kövesse az alábbi utasításokat az átmenő hitelesítés bérlőn történő üzembe helyezéséhez:

## <a name="step-1-check-the-prerequisites"></a>1. lépés: Az előfeltételek ellenőrzése

Győződjön meg arról, hogy a következő előfeltételek vannak érvényben.

### <a name="in-the-azure-active-directory-admin-center"></a>Az Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú globális rendszergazdai fiókot az Azure AD-bérlőn. Ily módon kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások sikertelenek lesznek, vagy elérhetetlenné válnak. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról.](../active-directory-users-create-azure-portal.md) Ez a lépés elengedhetetlen annak érdekében, hogy ne zárja ki a bérlő.
2. Adjon hozzá egy vagy több [egyéni tartománynevet](../active-directory-domains-add-azure-portal.md) az Azure AD-bérlőhöz. A felhasználók a tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-on-premises-environment"></a>A helyszíni környezetben

1. Az Azure AD Connect futtatásához azonosítsa a Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálót. Ha még nincs engedélyezve, [engedélyezze a TLS 1.2-t a kiszolgálón.](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect) Adja hozzá a kiszolgálót ugyanahhoz az Active Directory erdőhöz, mint azoka felhasználók, akiknek a jelszavát ellenőrizni kell.
2. Telepítse az [Azure AD Connect legújabb verzióját](https://www.microsoft.com/download/details.aspx?id=47594) az előző lépésben azonosított kiszolgálóra. Ha már fut az Azure AD Connect, győződjön meg arról, hogy a verzió 1.1.750.0 vagy újabb.

    >[!NOTE]
    >Az Azure AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 és 1.1.614.0-s verzióinak problémája van a jelszókivonat-szinkronizálással kapcsolatban. Ha _nem_ kívánja használni a jelszókivonat-szinkronizálást az átadó-hitelesítéssel együtt, olvassa el az [Azure AD Connect kiadási megjegyzéseket.](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470)

3. Azonosítson egy vagy több további kiszolgálót (Windows Server 2012 R2 vagy újabb verziót futtatva, ha a TLS 1.2 engedélyezve van), ahol önálló hitelesítési ügynököket futtathat. Ezek a további kiszolgálók szükségesek a bejelentkezési kérelmek magas rendelkezésre állásának biztosításához. Adja hozzá a kiszolgálókat ugyanahhoz az Active Directory erdőhöz, mint azoka felhasználók, akiknek a jelszavát ellenőrizni kell.

    >[!IMPORTANT]
    >Éles környezetben azt javasoljuk, hogy legalább 3 hitelesítési ügynökök futnak a bérlőn. Bérlőnként 40 hitelesítési ügynök rendszerkorlát van. A hitelesítési ügynököket futtató kiszolgálókat ajánlott esetben a 0- as szintű rendszerként kell kezelni (lásd [a hivatkozást).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

4. Ha tűzfal van a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
   - Győződjön meg arról, hogy a hitelesítési ügynökök *kimenő* kérelmeket tudnak végrehajtani az Azure AD-nek a következő portokon keresztül:

     | Portszám | Hogyan használják |
     | --- | --- |
     | **80** | Letölti a visszavont tanúsítványok listáját a TLS/SSL tanúsítvány érvényesítése közben |
     | **443** | Kezeli a szolgáltatással folytatott összes kimenő kommunikációt |
     | **8080** (nem kötelező) | A hitelesítési ügynökök tízpercenként jelentik az állapotukat a 8080-as porton keresztül, ha a 443-as port nem érhető el. Ez az állapot jelenik meg az Azure AD portálon. A 8080-as port _nem_ használható a felhasználói bejelentkezéshez. |
     
     Ha a tűzfal a rendszertől függően kényszeríti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatásokból érkező forgalom számára.
   - Ha a tűzfal vagy a proxy engedélyezi a DNS engedélyezési listáját, az ** \*engedélyezési** lista a .msappproxy.net és ** \*a .servicebus.windows.net**. Ha nem, engedélyezze a hozzáférést az [Azure-adatközpont IP-tartományaihoz,](https://www.microsoft.com/download/details.aspx?id=41653)amelyek hetente frissülnek.
   - A hitelesítési ügynököknek hozzáférésre van szükségük **login.windows.net** és **login.microsoftonline.com** a kezdeti regisztrációhoz. Nyissa meg a tűzfalat az URL-ek számára is.
   - A tanúsítványok érvényesítéséhez a következő URL-címek blokkolásának feloldásához: **mscrl.microsoft.com:80,** **crl.microsoft.com:80,** **ocsp.msocsp.com:80**és **\.www microsoft.com:80**. Mivel ezeket az URL-címeket más Microsoft-termékek tanúsítvány-ellenőrzésre használják, előfordulhat, hogy ezeket az URL-címeket már feloldotta.

## <a name="step-2-enable-the-feature"></a>2. lépés: A funkció engedélyezése

Az átmenő hitelesítés engedélyezése az [Azure AD Connect en](whatis-hybrid-identity.md)keresztül.

>[!IMPORTANT]
>Az Azure AD Connect elsődleges vagy átmeneti kiszolgálón engedélyezheti az átamenő hitelesítést. Erősen ajánlott, hogy engedélyezze az elsődleges kiszolgálóról. Ha a jövőben egy Azure AD Connect átmeneti kiszolgálót állít be, továbbra is az átmenő hitelesítést **kell** választania bejelentkezési beállításként; egy másik lehetőség kiválasztásával **letiltja** az átmenő hitelesítést a bérlőn, és felülbírálja a beállítást az elsődleges kiszolgálón.

Ha először telepíti az Azure AD Connectet, válassza az [egyéni telepítési útvonalat.](how-to-connect-install-custom.md) A **Felhasználó bejelentkezési** lapján válassza az **Átamenő hitelesítés lehetőséget** **bejelentkezési módként.** A sikeres befejezéskor egy átmenő hitelesítési ügynök van telepítve ugyanazon a kiszolgálón, mint az Azure AD Connect. Emellett az átmenő hitelesítési funkció engedélyezve van a bérlőn.

![Azure AD Connect: Felhasználói bejelentkezés](./media/how-to-connect-pta-quick-start/sso3.png)

Ha már telepítette az Azure AD Connectet az [expressz telepítés](how-to-connect-install-express.md) vagy az [egyéni telepítési](how-to-connect-install-custom.md) útvonal használatával, válassza a **Felhasználói bejelentkezési** feladat módosítása lehetőséget az Azure AD Connecten, majd kattintson a **Tovább**gombra. Ezután válassza **az áthaladási hitelesítés t** a bejelentkezési módszer. A sikeres befejezéskor egy átamenő hitelesítési ügynök van telepítve ugyanazon a kiszolgálón, mint az Azure AD Connect, és a szolgáltatás engedélyezve van a bérlőn.

![Azure AD Connect: Felhasználói bejelentkezés módosítása](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Az átmenő hitelesítés bérlői szintű szolgáltatás. Bekapcsolása hatással van a bejelentkezés a felhasználók számára a bérlő _összes_ felügyelt tartományában. Ha az Active Directory összevonási szolgáltatásokról (AD FS) átmenő hitelesítésre vált, várjon legalább 12 órát az AD FS-infrastruktúra leállítása előtt. Ez a várakozási idő biztosítja, hogy a felhasználók az átmenet során továbbra is bejelentkezhessenek az Exchange ActiveSync szolgáltatásba. Az AD FS-ről az átmenő hitelesítésre való áttéréssel kapcsolatos további segítségért tekintse meg [az itt](https://aka.ms/adfstoptadpdownload)közzétett részletes telepítési tervünket.

## <a name="step-3-test-the-feature"></a>3. lépés: A funkció tesztelése

Kövesse az alábbi utasításokat annak ellenőrzéséhez, hogy megfelelően engedélyezte-e az átmenő hitelesítést:

1. Jelentkezzen be az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. Válassza az **Azure Active Directory** a bal oldali ablaktáblában.
3. Válassza **az Azure AD Connect lehetőséget.**
4. Ellenőrizze, hogy az **áthaladási hitelesítési** szolgáltatás **engedélyezve van-e.**
5. Válassza **az Áthaladási hitelesítés lehetőséget.** Az **áthaladási hitelesítés** ablaktábla felsorolja azokat a kiszolgálókat, amelyeken a hitelesítési ügynökök telepítve vannak.

![Azure Active Directory felügyeleti központ: Az Azure AD Connect ablaktábla](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory felügyeleti központ: Átadó hitelesítés ablaktábla](./media/how-to-connect-pta-quick-start/pta8.png)

Ebben a szakaszban a bérlő összes felügyelt tartományának felhasználói bejelentkezhetnek átmenő hitelesítéssel. Az összevont tartományok felhasználói azonban továbbra is bejelentkeznek az AD FS vagy egy korábban konfigurált más összevonási szolgáltató használatával. Ha egy tartományt összevontról felügyeltre konvertál, az adott tartomány összes felhasználója automatikusan megkezdi a bejelentkezést az átadó hitelesítés használatával. Az átadó hitelesítés funkció nincs hatással a csak felhőalapú felhasználókra.

## <a name="step-4-ensure-high-availability"></a>4. lépés: Magas rendelkezésre állás biztosítása

Ha éles környezetben kívánja üzembe helyezni az átmenő hitelesítést, további önálló hitelesítési ügynököket kell telepítenie. Telepítse ezeket a hitelesítési ügynök(ek) a kiszolgáló (k) nem az Azure AD Connect.Install these Authentication Agent(s) on server(s) _other_ the one running Azure AD Connect. Ez a beállítás magas rendelkezésre állást biztosít a felhasználói bejelentkezési kérelmek számára.

>[!IMPORTANT]
>Éles környezetben azt javasoljuk, hogy legalább 3 hitelesítési ügynökök futnak a bérlőn. Bérlőnként 40 hitelesítési ügynök rendszerkorlát van. A hitelesítési ügynököket futtató kiszolgálókat ajánlott esetben a 0- as szintű rendszerként kell kezelni (lásd [a hivatkozást).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

Több átmenő hitelesítési ügynök telepítése biztosítja a magas rendelkezésre állást, de nem determinisztikus terheléselosztást a hitelesítési ügynökök között. Annak meghatározásához, hogy hány hitelesítési ügynökök van szüksége a bérlő, fontolja meg a maximális és átlagos terhelésbejelentkezési kérelmek, amelyek várhatóan látni a bérlőn. Viszonyítási alapként egyetlen hitelesítési ügynök képes kezelni 300–400 hitelesítésmásodpercenként egy szabványos 4-magos CPU, 16 GB RAM-kiszolgáló.

A hálózati forgalom becsléséhez használja a következő méretezési útmutatót:
- Minden kérelem hasznos mérete (0,5 K + 1K * num_of_agents) bájt; azaz az Azure AD-ből a hitelesítési ügynökre vonatkozó adatok. Itt a "num_of_agents" a bérlőn regisztrált hitelesítési ügynökök számát jelzi.
- Minden válasz hasznos mérete 1K bájt; azaz a hitelesítési ügynök től az Azure AD-ig.

A legtöbb ügyfél számára összesen három hitelesítési ügynök elegendő a magas rendelkezésre álláshoz és kapacitáshoz. A bejelentkezési késés javítása érdekében telepítse a tartományvezérlők közelében lévő hitelesítési ügynököket.

Először is kövesse az alábbi utasításokat a hitelesítési ügynök szoftverének letöltéséhez:

1. A hitelesítési ügynök (1.5.193.0-s vagy újabb verzió) legújabb verziójának letöltéséhez jelentkezzen be az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. Válassza az **Azure Active Directory** a bal oldali ablaktáblában.
3. Válassza **az Azure AD Connect**( Azure AD Connect , majd az **Átmenő hitelesítés**) lehetőséget, majd a **Letöltési ügynök**lehetőséget.
4. Válassza a **Feltételek elfogadása & letöltésgombot.**

![Azure Active Directory felügyeleti központ: Hitelesítési ügynök letöltése gomb](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory felügyeleti központ: Agent letöltése ablaktábla](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Közvetlenül is [letöltheti a Hitelesítési ügynök szoftvert.](https://aka.ms/getauthagent) A hitelesítési ügynök [szolgáltatási feltételeit](https://aka.ms/authagenteula) a telepítés _előtt_ tekintse át és fogadja el.

Az önálló hitelesítési ügynök kétféleképpen helyezhető üzembe:

Először is megteheti interaktívan csak fut a letöltött hitelesítési ügynök végrehajtható, és a bérlő globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri.

Másodszor, létrehozhat és futtathat egy felügyelet nélküli központi telepítési parancsfájlt. Ez akkor hasznos, ha egyszerre több hitelesítési ügynököt szeretne telepíteni, vagy hitelesítési ügynököket szeretne telepíteni olyan Windows-kiszolgálókra, amelyeken nincs engedélyezve a felhasználói felület, vagy amelyeket a Távoli asztal lal nem tud elérni. Az alábbi utasításokat olvashatja a módszer használatára:

1. Hitelesítési ügynök telepítéséhez futtassa a következő parancsot: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. A hitelesítési ügynököt regisztrálhatja a szolgáltatásunkban a Windows PowerShell használatával. Hozzon létre egy `$cred` PowerShell-hitelesítő adatok objektumot, amely globális rendszergazdai felhasználónevet és jelszót tartalmaz a bérlő számára. Futtassa a következő parancsot a * \<felhasználónév\> * és * \<a jelszó lecserélésével:\>*

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Nyissa meg a **C:\Program Files\Microsoft Azure AD Connect hitelesítési ügynököt,** és futtassa a következő parancsfájlt a `$cred` létrehozott objektum mal:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Ha egy hitelesítési ügynök telepítve van egy virtuális gépen, nem klónozhatja a virtuális gépet egy másik hitelesítési ügynök beállításához. Ez a módszer **nem támogatott.**

## <a name="step-5-configure-smart-lockout-capability"></a>5. lépés: Intelligens zárolási képesség konfigurálása

A Smart Lockout segít a rossz szereplők kizárásában, akik megpróbálják kitalálni a felhasználók jelszavait, vagy brute-force módszereket használnak a bejutáshoz. A Smart Lockout-beállítások konfigurálásával az Azure AD és / vagy a megfelelő zárolási beállítások at helyszíni Active Directory, támadások kiszűrhető, mielőtt azok elérnék az Active Directoryt. Ebből [a cikkből](../authentication/howto-password-smart-lockout.md) többet is megtudhat arról, hogyan konfigurálhatja a Smart Lockout beállításait a bérlőn a felhasználói fiókok védelme érdekében.

## <a name="next-steps"></a>További lépések
- [Az AD FS-ről átmenő hitelesítésre való áttelepítés](https://aka.ms/adfstoptadp) részletes útmutató az AD FS-ről (vagy más összevonási technológiákról) az átmenő hitelesítésre való áttelepítéshez.
- [Intelligens zárolás:](../authentication/howto-password-smart-lockout.md)Ismerje meg, hogyan konfigurálhatja a bérlő intelligens zárolási képességét a felhasználói fiókok védelme érdekében.
- [Jelenlegi korlátozások:](how-to-connect-pta-current-limitations.md)Ismerje meg, hogy mely forgatókönyvek támogatottak az átmenő hitelesítéssel, és melyek nem.
- [Technikai mélymerülés:](how-to-connect-pta-how-it-works.md)Ismerje meg, hogyan működik az átmenő hitelesítés funkció.
- [Gyakori kérdések](how-to-connect-pta-faq.md): Válaszok a gyakori kérdésekre.
- [Hibaelhárítás:](tshoot-connect-pass-through-authentication.md)Ismerje meg, hogyan oldhatja meg az átmenő hitelesítés szolgáltatással kapcsolatos gyakori problémákat.
- [Biztonsági mélymerülés:](how-to-connect-pta-security-deep-dive.md)Technikai információk beszerezni az átmenő hitelesítés i.
- [Azure AD seamless egyszeri bejelentkezés:](how-to-connect-sso.md)További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Az Azure Active Directory fórum használatával új szolgáltatáskérelmek et nyújthat be.
