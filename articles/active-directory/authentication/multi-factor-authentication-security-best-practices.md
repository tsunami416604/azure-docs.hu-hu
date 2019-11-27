---
title: Az Azure Multi-Factor Authentication-Azure Active Directory biztonsági útmutatója
description: Ez a dokumentum útmutatást nyújt az Azure MFA Azure-fiókokkal való használatáról
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2756d39a93751271c8c7bf2a51108b9fe5b09b1e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208435"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Biztonsági útmutató az Azure Multi-Factor Authentication Azure AD-fiókokkal való használatához

A kétlépéses ellenőrzés az ajánlott választás a legtöbb szervezet számára, akik szeretnék javítani a hitelesítési folyamatát. Az Azure Multi-Factor Authentication (MFA) segíti a vállalatoknak a biztonsági és megfelelőségi követelményeknek való megfelelést, miközben egyszerű bejelentkezési élményt biztosítanak a felhasználók számára. Ez a cikk az Azure MFA bevezetésének megtervezése során megfontolandó tippeket ismerteti.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Az Azure MFA üzembe helyezése a felhőben

Az Azure MFA két módon [engedélyezhető az összes felhasználó](howto-mfa-getstarted.md)számára.

* Licencek vásárlása minden felhasználóhoz (Azure MFA, prémium szintű Azure AD vagy Enterprise Mobility + Security)
* Multi-Factor Auth szolgáltató létrehozása és felhasználónkénti vagy felhasználónkénti fizetés

### <a name="licenses"></a>Licencek

![Licencek alkalmazása felhasználók számára, engedélyezés, értesítés](./media/multi-factor-authentication-security-best-practices/ems.png)

Ha prémium szintű Azure AD vagy Enterprise Mobility + Security licenccel rendelkezik, akkor már rendelkezik Azure MFA-val. A szervezetnek nincs szüksége további információkra a kétlépéses ellenőrzési képesség kiterjesztéséhez minden felhasználó számára. Csak egy licencet kell hozzárendelni egy felhasználóhoz, majd be lehet kapcsolni az MFA-t.

Multi-Factor Authentication beállításakor vegye figyelembe a következő tippeket:

* Ne hozzon létre hitelesítést használó multi-Factor Auth szolgáltatót. Ha így tesz, előfordulhat, hogy a licencekkel már rendelkező felhasználóktól érkező ellenőrzési kérelmekért kell fizetnie.
* Ha nem rendelkezik elegendő licenccel az összes felhasználó számára, létrehozhat egy felhasználónkénti multi-Factor Auth-szolgáltatót, hogy az fedezze a szervezet többi részét. 
* Azure AD Connect csak akkor szükséges, ha a helyszíni Active Directory környezetet egy Azure AD-címtárral szinkronizálja. Ha olyan Azure AD-címtárat használ, amely nincs szinkronizálva a Active Directory helyszíni példányával, nincs szükség Azure AD Connectra.

### <a name="multi-factor-auth-provider"></a>Multi-Factor Auth szolgáltató

![Multi-Factor Authentication szolgáltató](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Ha nincs olyan licence, amely tartalmazza az Azure MFA-t, [hozzon létre egy MFA-hitelesítési szolgáltatót](concept-mfa-authprovider.md).

Az Auth-szolgáltató létrehozásakor ki kell választania egy könyvtárat, és figyelembe kell vennie a következő adatokat:

* A multi-Factor Auth szolgáltató létrehozásához nincs szükség Azure AD-címtárra, de további funkciókat is kap. A következő funkciók engedélyezettek, ha az Auth-szolgáltatót egy Azure AD-címtárral társítja:
  * Kétlépéses ellenőrzés kiterjesztése az összes felhasználóra
  * A globális rendszergazdák számára további funkciókat is kínál, például a felügyeleti portált, az egyéni üdvözléseket és a jelentéseket.
* Ha a helyszíni Active Directory-környezetet egy Azure AD-címtárral szinkronizálja, a következőkre lesz szüksége: inrsync vagy Azure AD-szinkronizáló. Ha olyan Azure AD-címtárat használ, amely nem a Active Directory helyszíni példányával van szinkronizálva, nem kell megadnia az rSync vagy a Azure AD-szinkronizáló.
* Válassza ki a vállalata számára legmegfelelőbb felhasználási modellt. A használati modell kiválasztását követően nem módosítható. A két modell a következők:
  * /Hitelesítés: az egyes ellenőrzésekhez tartozó díjakat számítjuk fel. Akkor használja ezt a modellt, ha azt szeretné, hogy a kétlépéses ellenőrzés mindenki számára, aki hozzáfér egy adott alkalmazáshoz, nem pedig adott felhasználók számára.
  * /Engedélyezett felhasználó: az Azure MFA-hoz tartozó összes felhasználóra vonatkozó díjat számít fel. Akkor használja ezt a modellt, ha prémium szintű Azure AD vagy nagyvállalati mobilitási csomaggal rendelkező felhasználókkal rendelkezik, és néhány nem.

### <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó megszokta, hogy csak a jelszavakat használja a hitelesítéshez, fontos, hogy a vállalat a folyamattal kapcsolatos összes felhasználóra felhívja a figyelmét. Ez a figyelem csökkenti annak a valószínűségét, hogy a felhasználók az MFA-hoz kapcsolódó kisebb problémák esetén meghívja az ügyfélszolgálatot. Vannak azonban olyan helyzetek, amikor átmenetileg le kell tiltani az MFA-t. Az alábbi irányelvek segítségével megismerheti, hogyan kezelheti ezeket a forgatókönyveket:

* A technikai támogatási munkatársak betanítása olyan forgatókönyvek kezelésére, amelyekben a felhasználó nem tud bejelentkezni, mert a mobil alkalmazás vagy telefon nem kap értesítést vagy telefonhívást. A technikai támogatás lehetővé teszi, hogy egy egyszeri [mellőzést](howto-mfa-mfasettings.md#one-time-bypass) engedélyezzen a felhasználóknak a kétlépéses ellenőrzés megkerülésével egy adott idő hitelesítéséhez. A Mellőzés ideiglenes, és a megadott számú másodperc elteltével lejár.
* Tekintse át az Azure MFA [megbízható IP](howto-mfa-mfasettings.md#trusted-ips) -címeinek képességét a kétlépéses ellenőrzés minimalizálására. Ezzel a funkcióval a felügyelt vagy összevont bérlő rendszergazdái megkerülhetik a kétlépéses ellenőrzést a vállalat helyi intranetéről bejelentkezett felhasználók számára. A szolgáltatások olyan Azure AD-bérlők számára érhetők el, amelyek prémium szintű Azure AD, nagyvállalati mobilitási csomaggal vagy Azure Multi-Factor Authentication licenccel rendelkeznek.

## <a name="best-practices-for-an-on-premises-deployment"></a>Ajánlott eljárások helyszíni központi telepítéshez

Ha a vállalat úgy döntött, hogy kihasználja a saját infrastruktúráját az MFA engedélyezéséhez, akkor [telepítenie kell egy Azure multi-Factor Authentication-kiszolgáló a helyszínen](howto-mfaserver-deploy.md). Az MFA-kiszolgáló összetevői a következő ábrán láthatók:

![az alapértelmezett MFA-kiszolgáló összetevőit](./media/multi-factor-authentication-security-best-practices/server.png) \*nincs telepítve alapértelmezés szerint \** telepítve, de alapértelmezés szerint nincs engedélyezve

Az Azure Multi-Factor Authentication-kiszolgáló a Felhőbeli erőforrásokat és a helyszíni erőforrásokat is biztonságossá teszi az összevonás használatával. Az Azure AD-Bérlővel AD FS kell összevontnak lennie.
Multi-Factor Authentication-kiszolgáló beállításakor vegye figyelembe az alábbi adatokat:

* Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával védi az Azure AD-erőforrásokat, akkor az első ellenőrzési lépést a helyszíni AD FS használatával hajtja végre. A második lépés a helyszínen történik a jogcím betartásával.
* Nem kell telepítenie az Azure-Multi-Factor Authentication-kiszolgáló a AD FS összevonási kiszolgálót. A AD FS Multi-Factor Authentication-adapterét azonban a AD FS rendszert futtató Windows Server 2012 R2 rendszerre kell telepíteni. A kiszolgálót másik számítógépre is telepítheti, ha az egy támogatott verzió, és a AD FS adaptert külön telepíti a AD FS összevonási kiszolgálón. 
* A Multi-Factor Authentication AD FS-adapter telepítővarázslója létrehoz egy PhoneFactor-adminisztrátorok nevű biztonsági csoportot a Active Directory, majd hozzáadja a AD FS szolgáltatásfiókot a csoporthoz. Ellenőrizze, hogy létrejött-e a PhoneFactor-adminisztrátorok csoport a tartományvezérlőn, illetve hogy az AD FS-szolgáltatásfiók valóban tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.

### <a name="user-portal"></a>Felhasználói portál

A felhasználói portál lehetővé teszi az önkiszolgáló képességeket, és teljes körű felhasználói felügyeleti képességeket biztosít. Egy Internet Information Server-(IIS-) webhelyen fut. Az összetevő konfigurálásához kövesse az alábbi irányelveket:

* AZ IIS 6-os vagy újabb használata
* A ASP.NET v 2.0.507207 telepítése és regisztrálása
* Győződjön meg arról, hogy a kiszolgáló üzembe helyezhető egy peremhálózati hálózaton

### <a name="app-passwords"></a>Alkalmazás jelszavai

Ha a szervezete az Azure AD-vel történő egyszeri bejelentkezéshez készült, és az Azure MFA-t fogja használni, vegye figyelembe a következő adatokat:

* Az alkalmazás jelszavait az Azure AD ellenőrzi, így megkerüli az összevonást. Az összevonás csak az alkalmazás jelszavának beállításakor használatos.
* Összevont (SSO) felhasználók esetén a rendszer a jelszavakat a szervezeti AZONOSÍTÓban tárolja. Ha a felhasználó elhagyja a vállalatot, az információnak a szervezet AZONOSÍTÓját kell használnia az rSync használatával. A fiókok letiltása/törlése akár három órát is igénybe vehet, ami késlelteti az alkalmazások jelszavainak letiltását/törlését az Azure AD-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Nem érhető el helyszíni hitelesítési naplózási/naplózási képesség az alkalmazások jelszavainak megadásához.
* Bizonyos speciális építészeti kialakításokhoz szükség lehet a szervezeti felhasználónevek és jelszavak és az alkalmazások jelszavának együttes használatára, ha kétlépéses ellenőrzést használ az ügyfelekkel a hitelesítés helyétől függően. A helyszíni infrastruktúrával hitelesítő ügyfelek esetében szervezeti felhasználónevet és jelszót kell használnia. Az Azure AD-vel hitelesítő ügyfelek esetében használja az alkalmazás jelszavát.
* Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazás-jelszavakat. Ha engedélyezni szeretné a felhasználóknak az alkalmazás jelszavának létrehozását, jelölje be a **felhasználók számára az alkalmazás jelszavának engedélyezése lehetőséget a nem böngésző alkalmazásokba való bejelentkezéshez** .

## <a name="additional-considerations"></a>További szempontok

A következő lista további szempontokat és útmutatást nyújt a helyszínen üzembe helyezett egyes összetevőkhöz:

* Az Azure Multi-Factor Authentication telepítése az [Active Directory összevonási szolgáltatással](multi-factor-authentication-get-started-adfs.md).
* Az Azure MFA-kiszolgáló telepítése és konfigurálása [RADIUS-hitelesítéssel](howto-mfaserver-dir-radius.md).
* Az Azure MFA-kiszolgáló beállítása és konfigurálása [IIS-hitelesítéssel](howto-mfaserver-iis.md).
* Az Azure MFA-kiszolgáló beállítása és konfigurálása [Windows-hitelesítéssel](howto-mfaserver-windows.md).
* Az Azure MFA-kiszolgáló beállítása és konfigurálása [LDAP-hitelesítéssel](howto-mfaserver-dir-ldap.md).
* Az Azure MFA-kiszolgáló beállítása és konfigurálása a [Távoli asztali átjáró és az azure multi-Factor Authentication-kiszolgáló RADIUS használatával](howto-mfaserver-nps-rdg.md).
* Az Azure MFA-kiszolgáló és a [Windows Server-Active Directory](howto-mfaserver-dir-ad.md)közötti szinkronizálás beállítása és konfigurálása.
* [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](howto-mfaserver-deploy-mobileapp.md).
* [Speciális VPN-konfiguráció az Azure multi-Factor Authentication](howto-mfaserver-nps-vpn.md) a Cisco ASA, a Citrix NetScaler és a Juniper/Pulse Secure VPN-készülékek számára LDAP vagy RADIUS használatával.

## <a name="next-steps"></a>További lépések

Ez a cikk az Azure MFA-hoz kapcsolódó ajánlott eljárásokat mutatja be, és egyéb erőforrásokat is használhat az MFA üzembe helyezésének megtervezése során. Az alábbi lista néhány kulcsfontosságú cikket tartalmaz, amelyek segítséget nyújthatnak a folyamat során:

* [Jelentések az Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [A kétlépéses ellenőrzés regisztrációs felülete](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Azure Multi-Factor Authentication GYIK](multi-factor-authentication-faq.md)
