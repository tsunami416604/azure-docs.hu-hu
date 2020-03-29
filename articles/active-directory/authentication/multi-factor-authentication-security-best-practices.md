---
title: Biztonsági útmutató az Azure többtényezős hitelesítéséhez – Azure Active Directory
description: Ez a dokumentum útmutatást nyújt az Azure MFA Azure-fiókokkal való használatával kapcsolatban
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e42234e9fcdcfe3ee5ce975babbe03b64a750e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846827"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Biztonsági útmutató az Azure többtényezős hitelesítésazure-AD-fiókokkal való használatához

A kétlépéses ellenőrzés az előnyben részesített választás a legtöbb olyan szervezet számára, amely szeretné növelni a hitelesítési folyamatot. Az Azure többtényezős hitelesítés (MFA) segít a vállalatoknak megfelelni a biztonsági és megfelelőségi követelményeknek, miközben egyszerű bejelentkezési élményt nyújt a felhasználók számára. Ez a cikk néhány olyan tippet ismerteti, amelyeket érdemes figyelembe venni az Azure MFA bevezetésének tervezésekor.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Az Azure MFA üzembe helyezése a felhőben

Az [Azure MFA-t kétféleképpen engedélyezheti az összes felhasználó számára.](howto-mfa-getstarted.md)

* Licencek vásárlása az egyes felhasználók (akár Az Azure MFA, Az Azure AD Premium vagy enterprise mobility + security)
* Többtényezős hitelesítésszolgáltató létrehozása és felhasználónkénti vagy hitelesítésenkénti fizetés

### <a name="licenses"></a>Licencek

![Licencek alkalmazása a felhasználókra, engedélyezés, értesítés](./media/multi-factor-authentication-security-best-practices/ems.png)

Ha Rendelkezik Azure AD Premium vagy Enterprise Mobility + Security licenccel, már rendelkezik Azure MFA-val. A szervezetnek nincs szüksége további további adatokra ahhoz, hogy a kétlépéses ellenőrzési képességet minden felhasználóra kiterjessze. Csak licencet kell hozzárendelnie egy felhasználóhoz, majd bekapcsolhatja az MFA-t.

A többtényezős hitelesítés beállításakor vegye figyelembe az alábbi tippeket:

* Ne hozzon létre hitelesítésenkénti többtényezős hitelesítésszolgáltatót. Ha így tesz, a végén megkell fizetnie a licenccel már rendelkező felhasználók ellenőrzési kérelmeit.
* Ha nem rendelkezik elegendő licenccel az összes felhasználó számára, létrehozhat egy felhasználónkénti többtényezős hitelesítésszolgáltatót, amely lefedi a szervezet többi részét. 
* Az Azure AD Connect csak akkor szükséges, ha szinkronizálja a helyszíni Active Directory-környezetegy Azure AD-címtár. Ha olyan Azure AD-könyvtárat használ, amely nincs szinkronizálva az Active Directory helyszíni példányával, nincs szüksége az Azure AD Connectre.

### <a name="multi-factor-auth-provider"></a>Többtényezős hitelesítésszolgáltató

![Többtényezős hitelesítésszolgáltató](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Ha nem rendelkezik azure-beli mfa-t tartalmazó licencekkel, akkor [létrehozhat egy MFA hitelesítésszolgáltatót.](concept-mfa-authprovider.md)

Az Auth szolgáltató létrehozásakor ki kell választania egy könyvtárat, és figyelembe kell vennie a következő részleteket:

* Nem kell egy Azure AD könyvtárat, hogy hozzon létre egy többtényezős hitelesítésszolgáltató, de több funkciót kap egy. A következő funkciók akkor engedélyezettek, ha az Auth-szolgáltatót egy Azure AD-könyvtárhoz társítja:
  * A kétlépéses ellenőrzés kiterjesztése az összes felhasználóra
  * A globális rendszergazdáknak további funkciókat is kínálhat, például a felügyeleti portált, az egyéni üdvözléseket és a jelentéseket.
* Ha szinkronizálja a helyszíni Active Directory-környezetet egy Azure AD-könyvtárral, dirsync vagy Az Azure AD Sync szükséges. Ha olyan Azure AD-könyvtárat használ, amely nincs szinkronizálva az Active Directory helyszíni példányával, nincs szükség DirSync vagy Azure AD Sync szolgáltatásra.
* Válassza ki a vállalkozásának leginkább megfelelő fogyasztási modellt. Miután kiválasztotta a használati modellt, nem módosíthatja azt. A két modell a következő:
  * Hitelesítésenként: minden egyes ellenőrzésért díjat számít fel. Akkor használja ezt a modellt, ha kétlépéses ellenőrzést szeretne kérni bárki számára, aki egy bizonyos alkalmazáshoz fér hozzá, nem adott felhasználók számára.
  * Engedélyezett felhasználónként: díjat minden egyes felhasználó, amely engedélyezi az Azure MFA. Akkor használja ezt a modellt, ha rendelkezik néhány Azure AD Premium- vagy Enterprise Mobility Suite-licenccel rendelkező felhasználóval, és néhányat anélkül.

### <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó megszokta, hogy csak jelszavakat használ a hitelesítéshez, fontos, hogy a vállalat felhívja a figyelmet az összes felhasználóra ezzel a folyamattal kapcsolatban. Ez a tudatosság csökkentheti annak valószínűségét, hogy a felhasználók az MFA-val kapcsolatos kisebb problémák miatt hívják az ügyfélszolgálatot. Vannak azonban olyan forgatókönyvek, ahol ideiglenesen letiltja az MFA-t. Az alábbi irányelvek segítségével megtudhatja, hogyan kezelhetők ezek a forgatókönyvek:

* A technikai támogató személyzet et arra taníthatja, hogy kezelni tudják azokat a helyzeteket, amikor a felhasználó nem tud bejelentkezni, mert a mobilalkalmazás vagy a telefon nem kap értesítést vagy telefonhívást. A technikai támogatás [lehetővé teszi az egyszeri megkerülést,](howto-mfa-mfasettings.md#one-time-bypass) hogy a felhasználó egyetlen alkalommal hitelesíthesse magát a kétlépéses ellenőrzés "megkerülésével". A megkerülés ideiglenes, és meghatározott számú másodperc után lejár.
* Fontolja meg a [megbízható IP-k az](howto-mfa-mfasettings.md#trusted-ips) Azure MFA-ban, mint egy módja annak, hogy minimálisra csökkentsék a kétlépéses ellenőrzés. Ezzel a funkcióval a felügyelt vagy összevont bérlő rendszergazdái megkerülhetik a kétlépéses ellenőrzést a vállalat helyi intranetjéről bejelentkező felhasználók számára. A funkciók azure AD-bérlők számára érhetők el, amelyek Rendelkeznek az Azure AD Premium, enterprise mobility suite vagy Azure többtényezős hitelesítési licenccel.

## <a name="best-practices-for-an-on-premises-deployment"></a>Gyakorlati tanácsok a helyszíni telepítéshez

Ha a vállalat úgy döntött, hogy kihasználja a saját infrastruktúráját az MFA engedélyezéséhez, akkor telepítenie kell [egy Azure többtényezős hitelesítési kiszolgálót a helyszínen.](howto-mfaserver-deploy.md) Az MFA-kiszolgáló összetevői az alábbi ábrán láthatók:

![Az alapértelmezett MFA-kiszolgáló-összetevők](./media/multi-factor-authentication-security-best-practices/server.png) \* \*Alapértelmezés szerint nincsenek telepítve *Telepítve, de alapértelmezés szerint nincs engedélyezve

Az Azure többtényezős hitelesítési kiszolgáló az összevonás használatával biztosíthatja a felhőbeli erőforrásokat és a helyszíni erőforrásokat. Rendelkeznie kell az AD FS, és azt összekell egyeztetve az Azure AD-bérlővel.
A többtényezős hitelesítési kiszolgáló beállításakor vegye figyelembe az alábbi részleteket:

* Ha az Azure AD-erőforrásokat az Active Directory összevonási szolgáltatások (AD FS) használatával biztosítja, akkor az első ellenőrzési lépés a helyszínen történik az AD FS használatával. A második lépés a helyszínen történik a jogcím betartásával.
* Nem kell telepítenie az Azure többtényezős hitelesítési kiszolgáló az AD FS összevonási kiszolgáló. Az AD FS többtényezős hitelesítési adapterét azonban AD FS rendszert futtató Windows Server 2012 R2 rendszerre kell telepíteni. A kiszolgálót telepítheti egy másik számítógépre, feltéve, hogy az támogatott verzió, és az AD FS-adaptert külön telepítheti az AD FS összevonási kiszolgálóra. 
* A Többtényezős hitelesítés aD FS adapter telepítővarázsló létrehoz egy PhoneFactor Admins nevű biztonsági csoportot az Active Directoryban, majd hozzáadja az AD FS szolgáltatásfiókot ehhez a csoporthoz. Ellenőrizze, hogy létrejött-e a PhoneFactor-adminisztrátorok csoport a tartományvezérlőn, illetve hogy az AD FS-szolgáltatásfiók valóban tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.

### <a name="user-portal"></a>Felhasználói portál

A felhasználói portál önkiszolgáló funkciókat tesz lehetővé, és a felhasználói felügyeleti képességek teljes készletét biztosítja. Egy Internet Information Server (IIS) webhelyen fut. Az összetevő konfigurálásához használja az alábbi irányelveket:

* Az IIS 6 vagy nagyobb használata
* Telepítés és regisztráció ASP.NET 2.0.507207-es
* Annak ellenőrzése, hogy a kiszolgáló telepíthető-e peremhálózaton

### <a name="app-passwords"></a>Alkalmazásjelszavak

Ha a szervezet össze van egyítve az SSO az Azure AD-vel, és az Azure MFA-t fogja használni, akkor vegye figyelembe az alábbi részleteket:

* Az alkalmazás jelszavát az Azure AD ellenőrzi, ezért megkerüli az összevonást. Az összevonás csak az alkalmazásjelszavak beállításakor használatos.
* Összevont (SSO) felhasználók számára a jelszavak a szervezeti azonosítóban tárolódnak. Ha a felhasználó elhagyja a vállalatot, az információnak a DirSync használatával kell a szervezeti azonosítóhoz áramlania. A fiók letiltása/törlése akár három órát is igénybe vehet a szinkronizálás, ami késlelteti az alkalmazásjelszavak letiltását/törlését az Azure AD-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Az alkalmazásjelszavakhoz nem érhető el helyszíni hitelesítésnaplózási/naplózási képesség.
* Bizonyos speciális architekturális tervek szükség lehet a szervezeti felhasználónév és jelszavak és alkalmazásjelszavak kombinációjának használatára, amikor kétlépéses ellenőrzést használnak az ügyfelekkel, attól függően, hogy hol hitelesítik magukat. A helyszíni infrastruktúrával hitelesítendő ügyfelek szervezeti felhasználónevet és jelszót kell használnia. Az Azure AD-vel hitelesítendő ügyfelek esetében az alkalmazás jelszavát kell használnia.
* Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazásjelszavakat. Ha engedélyeznie kell a felhasználóknak az alkalmazásjelszavak létrehozását, jelölje be a **Felhasználók számára alkalmazásjelszavak létrehozásához nem böngészőalkalmazásokba való bejelentkezésengedélyezése** lehetőséget.

## <a name="additional-considerations"></a>További szempontok

A lista további szempontokat és útmutatást nyújt a helyszínen üzembe helyezett összetevőkről:

* Az Azure Multi-Factor Authentication telepítése az [Active Directory összevonási szolgáltatással](multi-factor-authentication-get-started-adfs.md).
* Az Azure MFA-kiszolgáló telepítése és konfigurálása [RADIUS-hitelesítéssel](howto-mfaserver-dir-radius.md).
* Az Azure MFA-kiszolgáló beállítása és konfigurálása [IIS-hitelesítéssel.](howto-mfaserver-iis.md)
* Állítsa be és konfigurálja az Azure MFA-kiszolgálót [Windows-hitelesítéssel.](howto-mfaserver-windows.md)
* Az Azure MFA-kiszolgáló beállítása és konfigurálása [LDAP-hitelesítéssel.](howto-mfaserver-dir-ldap.md)
* Állítsa be és konfigurálja az Azure MFA-kiszolgálót [a Távoli asztali átjáróval és az Azure többtényezős hitelesítési kiszolgálóval a RADIUS használatával.](howto-mfaserver-nps-rdg.md)
* Állítsa be és konfigurálja a szinkronizálást az Azure MFA Server és a [Windows Server Active Directory](howto-mfaserver-dir-ad.md)között.
* [Telepítse az Azure többtényezős hitelesítési kiszolgáló mobilalkalmazás-webszolgáltatását.](howto-mfaserver-deploy-mobileapp.md)
* [Speciális VPN-konfiguráció Az Azure multi-factor hitelesítés](howto-mfaserver-nps-vpn.md) Cisco ASA, Citrix Netscaler és Juniper/Pulse Secure VPN készülékek LDAP vagy RADIUS használatával.

## <a name="next-steps"></a>További lépések

Ez a cikk kiemeli az Azure MFA néhány ajánlott eljárását, vannak más erőforrások is, amelyek et is használhat az MFA-telepítés tervezése során. Az alábbi lista néhány kulcsfontosságú cikket tartalmaz, amelyek segíthetnek a folyamat során:

* [Jelentések az Azure többtényezős hitelesítésében](howto-mfa-reporting.md)
* [A kétlépcsős ellenőrzési regisztrációs élmény](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Az Azure többtényezős hitelesítéssel kapcsolatos gyakori kérdések](multi-factor-authentication-faq.md)
