---
title: Az Azure multi-factor Authentication biztonsági útmutatói
description: Ez a dokumentum nyújt arra az Azure MFA használata az Azure-fiókra vonatkozó útmutatás
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 1e143c8c17d5cbc3403d90f7a354d5300265d679
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100385"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Biztonsági útmutatást Azure multi-factor Authentication használata az Azure AD-fiókok

Kétlépéses ellenőrzés esetén a legtöbb szervezet számára, amelyek a hitelesítési folyamat bővítheti előnyben részesített választani. Az Azure multi-factor Authentication (MFA) megkönnyíti a vállalatok számára a biztonsági és megfelelőségi követelményeknek ugyanakkor biztosítható egy egyszerű bejelentkezési élmény a felhasználók számára. Ez a cikk az Azure MFA bevezetését tervezésekor érdemes tippek ismerteti.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Azure MFA felhőben való központi telepítése

Két módja van [Azure MFA engedélyezése minden felhasználó](howto-mfa-getstarted.md).

* Az egyes felhasználók (vagy az Azure MFA, Azure AD Premium vagy Enterprise Mobility + Security) licencek vásárlása
* A többtényezős hitelesítésszolgáltató és a fizetési felhasználói vagy a hitelesítés létrehozása

### <a name="licenses"></a>Licencek
![AZ EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Prémium szintű Azure AD vagy nagyvállalati mobilitási + biztonsági licenccel rendelkezik, ha már rendelkezik Azure MFA. A szervezet nem kell semmi mást a kétlépéses ellenőrzés képesség kiterjesztését azokra a minden felhasználó számára. Csak kell licenc hozzárendelése egy felhasználóhoz, és majd többtényezős hitelesítés bekapcsolása.

A multi-factor Authentication beállításakor vegye figyelembe a következőket:

* Ne hozzon létre egy hitelesítés többtényezős hitelesítésszolgáltató. Ha így tesz, akkor sikerült végül licenccel már rendelkező felhasználóktól a hitelesítési kérelmek fizet.
* Ha a felhasználók számára nem elegendő licenccel rendelkezik, létrehozhat egy felhasználói többtényezős hitelesítésszolgáltató fedik le a szervezet többi részétől. 
* Az Azure AD Connect csak akkor szükséges, ha a helyszíni Active Directory-környezet az Azure AD-címtár szinkronizál. Ha az Azure AD-címtárhoz, amely nincs szinkronizálva az Active Directory helyszíni példányát használja, nem kell az Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Többtényezős hitelesítésszolgáltató
![Többtényezős hitelesítésszolgáltató](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Ha nem rendelkezik, amely tartalmazza az Azure MFA licenceket, akkor is [egy multi-factor Authentication hitelesítésszolgáltató létrehozása](concept-mfa-authprovider.md).

A hitelesítésszolgáltató létrehozásakor kell jelöljön ki egy könyvtárat, és vegye figyelembe a következőket:

* Nem kell a többtényezős hitelesítésszolgáltató létrehozása az Azure AD-címtár, de egy további funkciókat elérhetővé. Az alábbi funkciók engedélyezettek, ha a hitelesítésszolgáltató társítja az Azure AD-címtár:
  * Kétlépéses ellenőrzés kiterjesztése a felhasználók számára
  * A globális rendszergazdák további szolgáltatásokat, például a felügyeleti portálon, egyéni hónap és jelentéseket kínál.
* Ha Azure AD-címtárral szinkronizálja a helyszíni Active Directory környezetben, akkor a DirSync és AAD Sync. Ha az Azure AD-címtárhoz, amely nincs szinkronizálva az Active Directory helyszíni példányát használja, nem kell a DirSync és AAD Sync.
* Válassza ki, amely a legjobban megfelel az üzleti fogyasztás modelljét. A használati modell kiválasztása után nem módosítható. A két modell a következő:
  * Hitelesítésenként: elszámolás meg minden egyes ellenőrzésre. Ezt a modellt használja, ha azt szeretné, hogy a kétlépéses ellenőrzést, bárki, aki hozzáfér egy bizonyos alkalmazás, bizonyos felhasználók részére nem.
  * Engedélyezett felhasználónként: minden olyan felhasználóhoz, az Azure MFA számára engedélyezi a díja meg. Ezt a modellt használja, ha egyes felhasználók az Azure AD Premium vagy a nagyvállalati mobilitási csomag licenceket, és néhány nélkül.

### <a name="supportability"></a>Támogatási lehetőségek
Mivel a legtöbb felhasználó csak jelszavak használata a hitelesítéshez által megszokott, fontos, hogy a vállalat felhívja a kapcsolatos a folyamat minden felhasználó számára. A tájékoztatási csökkentheti a valószínűsége, hogy a felhasználók a multi-factor Authentication kapcsolatos kisebb problémák hívja a segélyszolgálatot. Van azonban néhány olyan forgatókönyvet, ahol ideiglenesen letilthatja az MFA szükség. Annak megértése, hogyan azokra kezeléséhez kövesse az alábbi iránymutatásokat:

* A technikai támogatási személyzet helyzetek kezelésére, ahol a felhasználó nem tud bejelentkezni, mert egy értesítést vagy a telefonhívás nem kap a mobilalkalmazás vagy a telefon képzése. A technikai támogatási szolgálathoz is [engedélyezése az egyszeri Mellőzés](howto-mfa-mfasettings.md#one-time-bypass) egy felhasználó egy alkalommal hitelesítik a "Mellőzés" kétlépéses ellenőrzést. A Mellőzés átmeneti, és a megadott számú másodperc után lejár.
* Vegye figyelembe a [megbízható IP-címek funkció](howto-mfa-mfasettings.md#trusted-ips) az Azure MFA minimalizálása érdekében a kétlépéses ellenőrzést is. Ezzel a funkcióval a felügyelt vagy összevont bérlők rendszergazdái jogosultak a kétlépéses ellenőrzést, a felhasználók számára, hogy a vállalat helyi intraneten jelentkezik be. A funkciók érhetők el az Azure AD-bérlőkkel, amelyek az Azure AD Premium, a nagyvállalati mobilitási csomag vagy az Azure multi-factor Authentication licenccel rendelkezik.

## <a name="best-practices-for-an-on-premises-deployment"></a>Egy a helyszíni központi telepítésének ajánlott eljárásai
Ha a vállalat úgy döntött, kihasználhatják a saját infrastruktúrát, hogy engedélyezi az MFA Használatát, akkor kell [telepítse az Azure multi-factor Authentication kiszolgáló a helyszíni](howto-mfaserver-deploy.md). Az MFA kiszolgáló-összetevők a következő ábrán láthatók:

![Multi-factor Authentication kiszolgáló-összetevők alapértelmezett: konzol, a szinkronizálási motor, a felügyeleti portálon, a felhőalapú szolgáltatás](./media/multi-factor-authentication-security-best-practices/server.png) \*alapértelmezés szerint nincs telepítve \** telepített, de alapértelmezés szerint nincs engedélyezve

Az Azure multi-factor Authentication kiszolgáló biztonságossá teheti a felhőalapú erőforrások és a helyszíni erőforrások összevonási használatával. Kell rendelkeznie az AD FS és azt az Azure AD-bérlő összevont.
Multi-factor Authentication kiszolgáló beállításakor vegye figyelembe a következőket:

* Ha biztosítása, hogy az Azure AD-erőforrások Active Directory összevonási szolgáltatások (AD FS), akkor az első ellenőrzési lépés történik a helyszíni Active Directory összevonási szolgáltatások használatával. A második lépés a helyszínen történik a jogcím betartásával.
* Az Azure multi-factor Authentication kiszolgáló az AD FS összevonási kiszolgáló telepítése nincs. Azonban a multi-factor Authentication-Adapter az AD FS telepíteni kell a Windows Server 2012 R2 AD FS-t futtató. A kiszolgáló egy másik számítógépre telepítse, mindaddig, amíg támogatott verziója, és az AD FS-adaptert külön-külön telepíteni az AD FS összevonási kiszolgálón. 
* A multi-factor Authentication AD FS-Adapter telepítése varázsló PhoneFactor-Adminisztrátorok meghívta az Active Directory biztonsági csoportot hoz létre, és hozzáadja az AD FS szolgáltatásfiókja ehhez a csoporthoz. Ellenőrizze, hogy létrejött-e a PhoneFactor-adminisztrátorok csoport a tartományvezérlőn, illetve hogy az AD FS-szolgáltatásfiók valóban tagja-e a csoportnak. Ha szükséges, adja hozzá manuálisan az AD FS-szolgáltatásfiókot a tartományvezérlőn a PhoneFactor-adminisztrátorok csoporthoz.

### <a name="user-portal"></a>Felhasználói portál
A felhasználói portál lehetővé teszi, hogy a önkiszolgálói képességeit, és a teljes felhasználói felügyeleti képességeket biztosít. Az Internet Information Server (IIS) webhely fusson. Az alábbi útmutatást követve Ez az összetevő konfigurálása:

* Használja az IIS 6 vagy újabb
* Telepítse és regisztrálja az ASP.NET v2.0.507207
* Győződjön meg arról, hogy a kiszolgáló telepíthető a szegélyhálózatban

### <a name="app-passwords"></a>Alkalmazásjelszavak
Ha a szervezet az egyszeri bejelentkezés az Azure AD össze van vonva, és lehet az Azure MFA használatával kívánja, majd vegye figyelembe a következő információkat:

* Az alkalmazásjelszó ellenőrizte-e az Azure AD, és ezért nincs hatással az összevonási. Összevonási csak akkor használatos, alkalmazásjelszók beállítása során.
* Jelszavak tárolja (SSO) összevont felhasználók esetében a szervezeti azonosítóval. Ha a felhasználó elhagyja a vállalatot, adott információ ki a DirSync használatával, szervezeti azonosítóval áramló. Fiók letiltása/törlése szinkronizálásra, amely letiltása/törlése alkalmazásjelszók késlelteti az Azure ad-ben legfeljebb három óráig is eltarthat.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Alkalmazásjelszók nem helyszíni hitelesítési naplózási/naplózási képesség érhető el.
* Bizonyos speciális architekturális tervek előfordulhat, hogy a szervezeti felhasználónévvel és a jelszavak és a alkalmazásjelszókat, ha segítségével kétlépéses hitelesítéssel rendelkező ügyfelek, attól függően, hogy hol hitelesítéshez. A hitelesítést, a helyszíni infrastruktúra-ügyfelek esetében használja a szervezeti felhasználónévvel és jelszóval. A hitelesítést, az Azure AD-ügyfelek esetében használja az alkalmazásjelszót.
* Alapértelmezés szerint a felhasználók nem hozhatják létre alkalmazásjelszókat. Ha szeretné engedélyezése a felhasználóknak alkalmazásjelszavakat, jelölje be a **engedélyezése a felhasználóknak alkalmazásjelszavakat jelentkezzen be a böngészőn kívüli alkalmazások** lehetőséget.

## <a name="additional-considerations"></a>További szempontok
További szempontokat használja ezt a listát, és útmutatást az egyes összetevők, amely a helyszíni telepített:

- Az Azure Multi-Factor Authentication telepítése az [Active Directory összevonási szolgáltatással](multi-factor-authentication-get-started-adfs.md).
- Az Azure MFA-kiszolgáló telepítése és konfigurálása [RADIUS-hitelesítéssel](howto-mfaserver-dir-radius.md).
- Beállítása és konfigurálása az Azure MFA kiszolgáló [IIS-hitelesítés](howto-mfaserver-iis.md).
- Beállítása és konfigurálása az Azure MFA kiszolgáló [Windows-hitelesítés](howto-mfaserver-windows.md).
- Beállítása és konfigurálása az Azure MFA kiszolgáló [LDAP-hitelesítés](howto-mfaserver-dir-ldap.md).
- Beállítása és konfigurálása az Azure MFA kiszolgáló [távoli asztali átjáró és az Azure multi-factor Authentication kiszolgáló RADIUS használata](howto-mfaserver-nps-rdg.md).
- Állítsa be, és a szinkronizálás konfigurálása az Azure MFA kiszolgáló között, és [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
- [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](howto-mfaserver-deploy-mobileapp.md).
- [VPN-konfiguráció Azure multi-factor Authentication speciális](howto-mfaserver-nps-vpn.md) Cisco ASA, a Citrix Netscaler és a Juniper/Pulse Secure VPN készülékek LDAP vagy a RADIUS használatával.

## <a name="next-steps"></a>További lépések
Ez a cikk az Azure MFA emel ki, néhány ajánlott eljárás, amíg nincsenek más erőforrások, amelyek az MFA telepítésének tervezése során is használhatók. Az alábbi lista tartalmaz néhány főbb cikkeket, amelyek segítséget nyújthatnak a folyamat során:

* [Azure multi-factor Authentication jelentései](howto-mfa-reporting.md)
* [A kétlépéses ellenőrzés regisztrációs élmény](end-user/current/multi-factor-authentication-end-user-first-time.md)
* [Az Azure többtényezős hitelesítés – gyakori kérdések](multi-factor-authentication-faq.md)
