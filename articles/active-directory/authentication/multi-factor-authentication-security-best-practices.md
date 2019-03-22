---
title: Az Azure multi-factor Authentication – Azure Active Directory biztonsági útmutató
description: Ez a dokumentum nyújt útmutatást az Azure-fiókok az Azure MFA használatával
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
ms.openlocfilehash: 4441bf945b14a275f5b19ffe39f5ffd419e74e58
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310659"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Biztonsági útmutató az Azure multi-factor Authentication használata az Azure AD-fiókok

A kétlépéses ellenőrzés az előnyben részesített választás a legtöbb szervezet számára, amelyek a hitelesítési folyamat bővítheti. Az Azure multi-factor Authentication (MFA) révén a vállalatok a biztonsági és megfelelőségi követelményeinek művelet során gondoskodik a egy egyszerű bejelentkezési élmény a felhasználók számára. Ez a cikk néhány tippet az Azure MFA bevezetését tervezése során megfontolandó.

## <a name="deploy-azure-mfa-in-the-cloud"></a>A felhőbeli Azure MFA-kiszolgáló üzembe helyezése

Két módja van [az Azure MFA engedélyezése minden felhasználó számára](howto-mfa-getstarted.md).

* Az egyes felhasználók (vagy az Azure MFA, Azure AD prémium vagy Enterprise Mobility + Security) licencek vásárlása
* Hozzon létre egy multi-factor Auth szolgáltatót, és használatalapú felhasználónkénti vagy hitelesítésenkénti

### <a name="licenses"></a>Licencek
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Prémium szintű Azure AD vagy Enterprise Mobility + Security-licenccel rendelkezik, ha már rendelkezik Azure MFA. A szervezet nem kell semmi mást a kétlépéses ellenőrzés képesség kiterjesztését minden felhasználó számára. Csak kell egy licencet hozzárendelni egy felhasználóhoz, és ezután többtényezős hitelesítés bekapcsolása.

Többtényezős hitelesítés beállításakor vegye figyelembe a következőket:

* Ne hozzon létre egy hitelesítésenkénti multi-factor Auth szolgáltatót. Ha így tesz, akkor sikerült végül ellenőrzési kérések licenccel már rendelkező felhasználóktól.
* Ha minden felhasználó számára nem elegendő licenccel rendelkezik, létrehozhat egy felhasználónkénti multi-factor Auth szolgáltatót, hogy biztosítsák a szervezet többi tagja. 
* Az Azure AD Connect csak akkor szükséges, ha a helyszíni Active Directory-környezetet Azure AD-címtár szinkronizál. Ha az Azure AD-címtárral, amely nincs szinkronizálva az Active Directory helyszíni példányát használja, nem kell az Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Multi-factor Auth szolgáltatóval
![Multi-factor Auth szolgáltatóval](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Ha nem rendelkezik Azure MFA-licencek, akkor is [MFA hitelesítési szolgáltató létrehozása](concept-mfa-authprovider.md).

Amikor a hitelesítési szolgáltatót létrehozza, kell válassza ki azt a címtárat, és vegye figyelembe a következő adatokat:

* Nem kell a multi-factor Auth szolgáltató létrehozása az Azure AD-címtárral, de kap egy további funkciókat. Az alábbi szolgáltatások engedélyezve vannak, ha a hitelesítésszolgáltatót társítja az Azure AD-címtár:
  * Kétlépéses ellenőrzés kiterjesztése a felhasználók számára
  * A globális rendszergazdák egyéb hasznos segédanyaghoz, például a felügyeleti portálon, egyéni Üdvözlések és jelentéseket kínál.
* Ha az Azure AD-címtárral szinkronizálja a helyszíni Active Directory-környezetet, kell a DirSync vagy az AAD Sync. Ha az Azure AD-címtárral, amely nincs szinkronizálva az Active Directory helyszíni példányát használja, nem kell a DirSync vagy az AAD Sync.
* Válassza ki a használatalapú modell, amely a legjobban megfelel üzleti. Miután kiválasztotta a felhasználási modellt, nem módosítható. A két modell a következők:
  * Hitelesítésenként: díjat, az egyes ellenőrzések. Ezt a modellt használja, ha azt szeretné, hogy kétlépéses ellenőrzést, kizárólag a fér hozzá egy bizonyos alkalmazás számára, nem a megadott felhasználók számára.
  * Engedélyezett felhasználónként: díjat, minden egyes felhasználó esetében engedélyezi az Azure MFA-hoz. Ezt a modellt használja, ha egyes felhasználók az Azure AD prémium vagy nagyvállalati mobilitási csomag licenceket, valamint az egyes nélkül.

### <a name="supportability"></a>Támogatási lehetőségek
Mivel a legtöbb felhasználó vannak bemutatásával csak jelszavak használatával hitelesíteni, fontos, hogy a vállalat számos lehetőséget kínál tudatosság kapcsolatban a folyamat minden felhasználó számára. A tájékoztatási valószínűsége, hogy a felhasználók hívja a segélyszolgálatot MFA kisebb kapcsolatban csökkenthető. Vannak azonban néhány olyan forgatókönyvekben, ahol ideiglenesen letilthatja a többtényezős hitelesítés szükséges. Ezek a forgatókönyvek kezelése megértéséhez használja az alábbi irányelveket:

* A technikai támogatási csoportot kezelni forgatókönyvek, ahol a felhasználó nem tud bejelentkezni, mert a mobilalkalmazásban vagy a telefonszám már nem fogad egy értesítést vagy egy telefonhívás betanításához. Technikai támogatás is [engedélyezése az egyszeri Mellőzés](howto-mfa-mfasettings.md#one-time-bypass) , hogy a felhasználó egy alkalommal a "Mellőzés" kétlépéses ellenőrzést. A Mellőzés átmeneti, és a megadott számú másodperc után lejár.
* Vegye figyelembe a [megbízható IP-címek képesség](howto-mfa-mfasettings.md#trusted-ips) az Azure MFA-ban, hogy minimalizálja a kétlépéses ellenőrzést. Ezzel a funkcióval a felügyelt vagy összevont bérlők rendszergazdái megkerülhetik a kétlépéses ellenőrzést, a felhasználók számára, hogy a vállalat helyi intranet a bejelentkezés. Az funkciók állnak rendelkezésre, amely prémium szintű Azure AD, a nagyvállalati mobilitási csomag vagy az Azure multi-factor Authentication licenccel rendelkezik az Azure AD-bérlőt.

## <a name="best-practices-for-an-on-premises-deployment"></a>A helyszíni központi telepítésével kapcsolatos ajánlott eljárások
Ha a vállalat úgy döntött, hogy a többtényezős hitelesítés engedélyezése a saját infrastruktúrájával, akkor kell [helyszíni üzembe helyezése az Azure multi-factor Authentication kiszolgáló](howto-mfaserver-deploy.md). Az MFA-kiszolgáló-összetevők a következő ábrán láthatók:

![MFA-kiszolgáló-összetevők alapértelmezett: konzol, a szinkronizálási motor, a felügyeleti portálon, a felhőszolgáltatás](./media/multi-factor-authentication-security-best-practices/server.png) \*nincs telepítve alapértelmezés szerint \** telepítve van, de alapértelmezés szerint nincs engedélyezve

Az Azure multi-factor Authentication kiszolgáló gondoskodhat felhőbeli erőforrásokat és a helyszíni erőforrások összevonási. Az AD FS és a nincs összevonva az Azure AD-bérlővel.
A multi-factor Authentication-kiszolgáló beállításakor vegye figyelembe a következő adatokat:

* Ha meg vannak az Azure AD erőforrások védelme az Active Directory összevonási szolgáltatások (AD FS), majd az első ellenőrzési lépés hajtja végre a helyszíni Active Directory összevonási szolgáltatások használatával. A második lépés a helyszínen történik a jogcím betartásával.
* Nem kell telepíteni az Azure multi-factor Authentication kiszolgáló az AD FS összevonási kiszolgálóra. Azonban a multi-factor Authentication adapterét AD FS kell telepíteni a Windows Server 2012 R2 AD FS-t futtató. A kiszolgáló telepítése egy másik számítógépen, mindaddig, amíg egy támogatott verziója, és az AD FS-adaptert külön telepítheti az AD FS összevonási kiszolgálóra. 
* A multi-factor Authentication AD FS-Adapter telepítővarázslója létrehoz egy, az Active Directoryban a PhoneFactor-Adminisztrátorok nevű biztonsági csoportot, és ezután hozzáadja az AD FS-szolgáltatásfiókot a csoporthoz. Ellenőrizze, hogy létrejött-e a PhoneFactor-adminisztrátorok csoport a tartományvezérlőn, illetve hogy az AD FS-szolgáltatásfiók valóban tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.

### <a name="user-portal"></a>Felhasználói portál
A felhasználói portál lehetővé teszi önkiszolgáló képességek és a felhasználó-felügyeleti képességek teljes készletét nyújtja. Az Internet Information Server (IIS) webhely fusson. Ez az összetevő konfigurálásához kövesse az alábbi irányelveket:

* Az IIS 6-os vagy nagyobb használata
* Telepítése és regisztrálása az ASP.NET v2.0.507207
* Győződjön meg arról, hogy ezen a kiszolgálón is üzembe helyezhetők a szegélyhálózaton

### <a name="app-passwords"></a>Alkalmazásjelszavak
Ha a szervezet össze van vonva az Azure AD egyszeri bejelentkezéshez, és szeretné használni az Azure MFA, majd vegye figyelembe a következő adatokat:

* Az alkalmazás jelszavának ellenőrizte az Azure AD-e, és így megkerüli az összevonási. Összevonási csak akkor használható, állítson be alkalmazásjelszót.
* Jelszavak (SSO) összevont felhasználók esetében a szervezeti azonosító tárolja. Ha a felhasználó elhagyja a vállalatot, a megjelenő rendelkezik áramlanak a DirSync használatával. A fiókok akár három órát vehet való szinkronizálás, ami késlelteti az alkalmazásjelszó letiltását/törlését az Azure ad-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Alkalmazásjelszavak használata esetén nem a helyszíni hitelesítési naplózás/naplózási képesség érhető el.
* Bizonyos speciális architekturális tervek a szervezeti felhasználónévvel és a jelszavak és az alkalmazásjelszók kombinációját használó ügyfelek, attól függően, hol hitelesítéshez a kétlépéses ellenőrzés használata esetén lehet szükség. Az ügyfelek számára, amely egy helyszíni infrastruktúrát a hitelesítésre akkor érdemes használnia a szervezeti felhasználónévvel és jelszóval. -Ügyfelek, amelyek az Azure AD hitelesítése akkor érdemes használnia az alkalmazásjelszót.
* Alapértelmezés szerint a felhasználók az alkalmazásjelszavak nem hozható létre. Ha szeretné lehetővé teszik a felhasználók könnyedén hozhatnak létre alkalmazásjelszókat, válassza ki a **engedélyezése a felhasználóknak alkalmazásjelszavakat jelentkezzen be a böngészőn kívüli alkalmazások** lehetőséget.

## <a name="additional-considerations"></a>További szempontok
Használja ezt a listát vonatkozó további szempontokról, és útmutatást az egyes összetevők, amely a helyszíni üzembe helyezett:

- Az Azure Multi-Factor Authentication telepítése az [Active Directory összevonási szolgáltatással](multi-factor-authentication-get-started-adfs.md).
- Az Azure MFA-kiszolgáló telepítése és konfigurálása [RADIUS-hitelesítéssel](howto-mfaserver-dir-radius.md).
- Telepítése és konfigurálása az Azure MFA-kiszolgáló [IIS-hitelesítés](howto-mfaserver-iis.md).
- Telepítése és konfigurálása az Azure MFA-kiszolgáló [Windows-hitelesítés](howto-mfaserver-windows.md).
- Telepítése és konfigurálása az Azure MFA-kiszolgáló [LDAP-hitelesítés](howto-mfaserver-dir-ldap.md).
- Telepítése és konfigurálása az Azure MFA-kiszolgáló [távoli asztali átjáró és az Azure multi-factor Authentication kiszolgáló RADIUS-t használó](howto-mfaserver-nps-rdg.md).
- És az Azure MFA-kiszolgáló közötti szinkronizálás beállítása és [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
- [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](howto-mfaserver-deploy-mobileapp.md).
- [Speciális VPN-konfiguráció az Azure multi-factor Authentication](howto-mfaserver-nps-vpn.md) Juniper/Pulse Secure VPN, Cisco ASA és a Citrix Netscaler készülékek LDAP vagy a RADIUS használatával.

## <a name="next-steps"></a>További lépések
Ez a cikk az Azure MFA emeli ki néhány ajánlott eljárást, amíg nincsenek más erőforrások, az MFA üzembe helyezésének megtervezése során is használható. Az alábbi lista néhány kulcsfontosságú cikkek, amelyek segítségére lehetnek a folyamat során rendelkezik:

* [Az Azure multi-factor Authentication jelentései](howto-mfa-reporting.md)
* [A kétlépéses ellenőrzés alkalmazásregisztrációs folyamatot](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Az Azure multi-factor Authentication – gyakori kérdések](multi-factor-authentication-faq.md)
