---
title: Hibrid identitás Tervező – Azure bevezetési stratégia |} A Microsoft Docs
description: A feltételes hozzáférés-vezérléssel Azure Active Directory ellenőrzi a meghatározott feltételek, válassza ki, amikor a felhasználó hitelesítése és engedélyezése az alkalmazáshoz való hozzáférés előtt. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítése és hozzáférhessen az alkalmazáshoz.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 50f2d3f3ad934668c4a243b387b56db48c3e7d35
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471803"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>A hibrid identitás bevezetési stratégia kidolgozása
Ebben a feladatban a hibrid identitáskezelési megoldás, amely aktorcsoportot tárgyalt, az üzleti igényeknek hibrid identitás bevezetési stratégiája határozza meg:

* [Üzleti igények meghatározása](plan-hybrid-identity-design-considerations-business-needs.md)
* [Határozza meg a címtár-szinkronizálás követelményei](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [A multi-factor authentication-követelmények meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Üzleti igények stratégia kidolgozása
Az első feladat címeket, amely meghatározza, hogy a szervezetek üzleti van szüksége.  Ez rendkívül széles körű lehet, és a hatókör kötés akkor fordulhat elő, ha nem gondos.  Első legyen egyszerű, de ne felejtse el fogja befogadásához, és módosítsa a jövőben megkönnyítése kialakítás tervezését.  Függetlenül attól, hogy egy egyszerű kialakítás vagy rendkívül összetett egy, az Azure Active Directory a Microsoft Identity platform, amely támogatja az Office 365-höz, a Microsoft Online Services és a felhő figyelembe vevő alkalmazásokkal.

## <a name="define-an-integration-strategy"></a>Egy integrációs stratégia kidolgozása
A Microsoft rendelkezik három fő integrációs forgatókönyvek, melyek felhőbeli identitások, a szinkronizált identitások és a összevont identitások kialakítása.  Meg kell terveznie az alábbi integrációs stratégiák egyikét bevezetése.  A választott stratégia eltérőek lehetnek, és a döntések megválasztásához egy tartalmazhatnak, milyen típusú felhasználói élményt szeretne biztosítani, rendelkezik egy meglévő infrastruktúra, és mi az a leginkább költséghatékony.  

![](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

A fenti ábrán meghatározott a következők:

* **Felhőalapú Identitások**: ezek az identitások, amelyek csak a felhőben található.  Esetén az Azure AD-ben, volna lennie kifejezetten az Azure AD-címtár.
* **Szinkronizált**: ezek a meglévő helyszíni identitások és a felhőben.  Az Azure AD Connect használatával, ezek a felhasználók vannak hoznak létre vagy meglévő Azure AD-fiókok csatlakozott.  A felhasználó Jelszókivonat szinkronizálja a helyszíni környezetből a felhőbe, az úgynevezett Jelszókivonat.  Szinkronizált használatával egy csoportosítani esetén, hogy a felhasználó le van tiltva, a helyszíni környezetben, is igénybe vehet akár három órát, hogy megjelenjen az Azure AD a fiók állapota.  A szinkronizálási időtartam alatt okozza.
* **Összevont**: ezeket az identitásokat a helyszíni létezik, és a felhőben.  Az Azure AD Connect használatával, ezek a felhasználók vannak hoznak létre vagy meglévő Azure AD-fiókok csatlakozott.  

> [!NOTE]
> A szinkronizálási beállítások kapcsolatos további információkért olvassa el a [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Az alábbi táblázat segítségével előnyeit és hátrányait az alábbi stratégiák meghatározása során:

| Stratégia | Előnyök | Hátrányai |
| --- | --- | --- |
| **Felhőbeli identitások** |Könnyebben kezelhető kis szervezete számára. <br> Semmit nem kell telepítenie a helyszínen. Nincs szükség további hardverre<br>Könnyen használható, ha a felhasználó távozik a szervezettől |Felhasználók kell bejelentkezni, a felhőalapú számítási feladatokat elérésekor <br> Előfordulhat, hogy jelszavakat, és nem lehet azonos a felhőbeli és helyszíni identitások |
| **Synchronized** |Helyszíni jelszóval hitelesíti magát, mind a helyszíni és felhőbeli címtárakban <br>Könnyebben kezelhető kicsi, közepes vagy nagy szervezetek számára <br>Az egyes erőforrások felhasználók rendelkezhetnek egyszeri bejelentkezés (SSO) <br> Szinkronizálási Microsoft előnyben részesített módszere <br> Könnyebben kezelhető |Lehet, hogy egyes ügyfeleknek védhetőek, azok a címtárak szinkronizálásának adott vállalat rendőrségi miatt a felhővel |
| **Összevont** |Egyszeri bejelentkezés (SSO) a felhasználók rendelkezhetnek <br>Ha a felhasználó le van állítva, vagy hagyja, a fiók azonnal letiltja, és a hozzáférés visszavonása esetén<br> Támogatja a speciális forgatókönyvek, amelyek nem konfigurálhatók az szinkronizálva |További lépéseket és beállítása <br> Nagyobb karbantartási <br> További hardverre lehet szükség az STS-infrastruktúra <br> Az összevonási kiszolgáló telepítése további hardverre lehet szükség. További szoftverek szükség, az AD FS használata <br> Egyszeri bejelentkezés kiterjedt beállítása szükséges <br> Kritikus pontok hiba, ha az összevonási kiszolgáló nem működik a felhasználók nem tudják hitelesíteni |

### <a name="client-experience"></a>Ügyfélélmény
A használt stratégia fogja diktálni a felhasználói bejelentkezési élmény.  A következő táblázatokban, amelyen a felhasználók milyen kell látnia kell a bejelentkezési élmény vonatkozó információk.  Nem minden összevont identitás-szolgáltatóktól minden forgatókönyvben támogatja az egyszeri Bejelentkezést.

**A tartomány tagja, és privát hálózati alkalmazások**:

|  | Szinkronizált identitás | Federated Identity |
| --- | --- | --- |
| Webböngészők |Űrlapalapú hitelesítés |egyszeri bejelentkezés, egyes esetekben szükséges adja meg a szervezet azonosítója |
| Outlook |Hitelesítő adatok kérése |Hitelesítő adatok kérése |
| Skype vállalati verzió (Lync). |Hitelesítő adatok kérése |egyszeri bejelentkezés Lync, a rendszer hitelesítő adatokat az Exchange-hez |
| OneDrive Vállalati verzió |Hitelesítő adatok kérése |Egyszeri bejelentkezés |
| Office Pro Plus előfizetés |Hitelesítő adatok kérése |Egyszeri bejelentkezés |

**Külső vagy nem megbízható forrásból**:

|  | Szinkronizált identitás | Federated Identity |
| --- | --- | --- |
| Webböngészők |Űrlapalapú hitelesítés |Űrlapalapú hitelesítés |
| Az Outlook, a Skype for Business (Lync), a OneDrive vállalati verzióba, Office-előfizetés |Hitelesítő adatok kérése |Hitelesítő adatok kérése |
| Exchange ActiveSync |Hitelesítő adatok kérése |egyszeri bejelentkezés Lync, a rendszer hitelesítő adatokat az Exchange-hez |
| Mobilalkalmazások |Hitelesítő adatok kérése |Hitelesítő adatok kérése |

Ha eldöntötte, hogy a külső identitásszolgáltató vagy van folyamatban az Azure AD összevonási használata egy 1. feladat származó, vegye figyelembe a következő támogatott funkciók szeretné:

* Bármely SAML 2.0-s szolgáltató, amely megfelel az SP-Lite profil az Azure AD-hitelesítést is támogatja, és a társított alkalmazások
* Támogatja a passzív hitelesítést, amely elősegíti a hitelesítést az OWA-, az SPO-, stb.
* Az ügyfelek Exchange online-hoz használható keresztül a SAML 2.0-s fokozott ügyfél profil (ECP)

Is kell vigyázni, milyen lehetőségek nem lesznek elérhetők:

* WS-megbízhatósági vagy összevonás-támogatás nélkül más aktív ügyfelek felosztása
  * Ez azt jelenti, nem Lync ügyfelet, a onedrive-on client, Office-előfizetés, Office Mobile, Office 2016 előtt
* Áttérés az Office passzív hitelesítést lehetővé teszi, hogy tiszta SAML 2.0 identitásszolgáltató használatát támogató, de támogatása továbbra is elszámolással, az ügyfél az ügyfél által

> [!NOTE]
> A legtöbb frissített listáját, olvassa el a cikket a [az Azure AD összevonás kompatibilitási listája](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Szinkronizálási stratégia kidolgozása
Ebben a feladatban az eszközök szinkronizálásához használt határoz a szervezet helyszíni adatokat a felhőbe, és mit kell használnia a topológia.  Mivel a legtöbb szervezet számára az Active Directory használatával, oldja meg a fenti kérdések az Azure AD Connect használatával információkat néhány részletet.  Olyan környezetekben, amelyek nem rendelkeznek az Active Directory nincs érdekében ez a stratégia megtervezése a FIM 2010 R2-ről vagy a MIM 2016 használatával kapcsolatos információkat.  Azonban későbbi kiadásokban az Azure AD Connect támogatja az LDAP-címtárak esetén a idővonalán, így függően ezek az információk, előfordulhat, hogy tudni segíteni.

### <a name="synchronization-tools"></a>Eszközök szinkronizálása
Az évek során több szinkronizálási eszközök létezett, és különféle forgatókönyvekhez használt.  Az Azure AD Connect jelenleg az Ugrás a kívánt eszközben dolgozhat az összes támogatott forgatókönyveket.  AAD-Szinkronizáló DirSync továbbra is körül és is lehet a környezetében található most. 

> [!NOTE]
> A legújabb információk a támogatott képességek kapcsolatos minden egyes eszköz [címtár-integrációs eszközök összehasonlítása](plan-hybrid-identity-design-considerations-tools-comparison.md) cikk.  
> 
> 

### <a name="supported-topologies"></a>Támogatott topológiák
A szinkronizálás stratégia meghatározásakor a használt topológia kell meghatározni. A lépésben meghatározott információknak 2 lehet meghatározni, hogy melyik topológiát megállapítsuk, melyiket érdemes használni. Egyetlen erdő, egyetlen Azure AD-topológia a leggyakoribb, és egyetlen Active Directory-erdő és az Azure ad-ben egyetlen példánya áll.  Ez a legtöbb forgatókönyv használni fog, és a várt topológia az Azure AD Connect Expressz telepítés használatakor, az alábbi ábrán látható módon.

![](./media/plan-hybrid-identity-design-considerations/single-forest.png) Egyetlen erdő forgatókönyv szokás több erdővel rendelkezik, akár kis és nagy szervezetek számára az 5. ábrán látható módon.

> [!NOTE]
> További információ a különböző helyszíni és az Azure AD Connect-topológiák az Azure AD sync olvassa [az Azure AD Connect-topológiák](plan-connect-topologies.md).
> 
> 

![](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Többerdős forgatókönyv

Ha ez a helyzet, akkor a több erdőt egyetlen Azure AD-topológia kell alkalmazni, ha teljesül a következő elemek:

* Felhasználók csak 1 identitás összes erdőben rendelkeznek. – a egyedileg azonosító felhasználók az alábbi szakasz részletesen ismerteti a.
* A felhasználó hitelesíti magát az erdőben, ahol-azonosítót fog kérni is található
* Egyszerű felhasználónév és a Forráshorgony (nem módosítható azonosító) fognak érkezni az erdő
* Minden olyan erdőben elérhetők az Azure AD Connect – Ez azt jelenti, hogy nem kell lennie a tartomány tagja, és ha ez elősegíti, hogy ez egy DMZ-ben elhelyezett.
* A felhasználóknak rendelkezniük csak egy postaláda
* Az erdő, amelyen a felhasználók postaládáihoz rendelkezik a legjobb adatminőség attribútumok látható a az Exchange globális cím lista (GAL)
* Ha a felhasználó nem postaláda van, majd bármely erdőben használható működhet közre ezeket az értékeket
* Rendelkezhetnek hivatkozott postafiókkal, majd van-e még egy másik fiókba való bejelentkezéshez használt eltérő erdőben.

> [!NOTE]
> Objektumok, a helyszínen található, és a felhőben "keresztül vannak csatlakoztatva" egyedi azonosítója. A címtár-szinkronizálás környezetében Ez az egyedi azonosító nevezzük a SourceAnchor. A környezetben az egyszeri bejelentkezést ezt nevezik immutableid azonosítója. [Tervezési alapelvek az Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) a SourceAnchor használatával kapcsolatban további szempontok.
> 
> 

Ha a fenti nem teljesülnek, és több aktív fiók vagy postaláda egynél több, az Azure AD Connect válasszon egyet, és a többi mellőzése.  Ha postaládák, de nincs más fiók, ezek a fiókok nem lesznek exportálva az Azure AD és, hogy a felhasználó nem egy csoportnak sem tagja.  Ez eltér a módját a DirSync korábban volt, és azt szándékos jobb támogatása Többerdős forgatókönyvekben. Egy Többerdős forgatókönyv az alábbi ábrán látható.

![](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Többerdős több Azure AD-forgatókönyv**

Javasoljuk, hogy a szervezet Azure AD-ben csak egyetlen címtárral rendelkezik, de támogatott egy 1:1 számosságú kapcsolatok elkülönülve lesz egy Azure AD Connect szinkronizálási kiszolgáló és az Azure AD-címtár között.  Az Azure AD minden példánya esetében az Azure AD Connect telepíteni kell.  Ezenkívül Azure AD-ben elvárt elkülönítve, és a egy példányát az Azure AD a felhasználók nem fognak látni egy másik példány a felhasználók.

Ez a módszer lehetséges és támogatott Active Directory helyszíni példányát egy több Azure AD-címtár csatlakozni, az alábbi ábrán látható módon:

![](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Egyerdős szűrési forgatókönyv**

Ehhez az alábbiaknak kell teljesülniük:

* Az Azure AD Connect szinkronizálási kiszolgálót úgy, hogy egyes objektumok egymást kölcsönösen kizáró csoportja szűréshez kell konfigurálni.  Ez történik, például hatókörének beállítása az egyes tartományokhoz vagy szervezeti egység minden kiszolgáló által.
* DNS-tartományt csak egyetlen lehet regisztrálni az Azure AD-címtár tehát az UPN-EK a felhasználók a helyszíni AD külön névtereket kell használnia
* Azure AD egy példányát a felhasználók csak lesz az áttelepített példány felhasználót láthatja.  Nem lesz a többi példány a felhasználó megjelenítése
* Csak az Azure AD-címtár egyik engedélyezheti a helyszíni Exchange hibrid AD
* Kölcsönös kizárólagosság késleltetve visszaírt is vonatkozik.  Ez lehetővé teszi az egyes késleltetve visszaírt funkciók nem támogatott az ehhez a topológiához, mert ezek feltételezik, hogy egy egyetlen helyszíni konfigurálása.  Az érintett műveletek közé tartoznak az alábbiak:
  * Alapértelmezett konfiguráció késleltetve visszaírt-csoport
  * Eszközvisszaírás

A következő nem támogatott, és nem kell megválasztani implementálhat:

* Szeretné, hogy több Azure AD Connect szinkronizálási kiszolgáló ugyanabban az Azure AD-címtár csatlakozik, akkor is, ha azok konfigurált a szinkronizáláshoz egymást kölcsönösen kizáró set-objektum nem támogatott
* Ez ugyanaz a felhasználó a több Azure AD-címtár szinkronizálása nem támogatott. 
* Azt is nem támogatja az olyan konfigurációs módosítást, hogy a felhasználók az egyik Azure AD-névjegyeit, állítsa be egy másik Azure AD-címtárban hálózatnak látszanak. 
* Emellett akkor is szeretne csatlakozni a több Azure AD-címtár Azure AD Connect szinkronizálásának módosítása nem támogatott.
* Az Azure AD-címtár elkülönített kialakítás szerint is. Egy másik Azure AD-címtár létrehozása egy közös és egységes GAL a címtárak között az adatokat olvasni az Azure AD Connect-szinkronizálás konfigurációjának módosítása nem támogatott. Azt is nem támogatja a névjegyek, a felhasználók exportálása egy másik helyszíni AD-bA az Azure AD Connect szinkronizálása.

> [!NOTE]
> Ha a szervezet a hálózaton lévő számítógépekre korlátozza a kapcsolódást az internethez, ez a cikk felsorolja a végpontok (teljesen minősített tartományneveket, IPv4 és IPv6-címtartományok), akkor tartalmaznia kell a listák és az Internet Explorer Megbízható helyek zónához ügyfél a kimenő engedélyezése Győződjön meg, hogy a számítógépek sikeresen használható Office 365-höz. További információk [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>A multi-factor authentication-stratégia kidolgozása
Ebben a feladatban a multi-factor authentication szolgáltatás stratégia használatára fogja meghatározni.  Az Azure multi-factor Authentication két változatban érhető el.  Az egyik egy felhőalapú a másik pedig a helyszíni, az Azure MFA-kiszolgáló használatával.  Az értékelés alapján volt a fent meghatározhatja melyik megoldás felel meg a megfelelőt a stratégiához.  Az alábbi táblázat segítségével határozhatja meg, melyik legjobb tervezési beállítás megfelel a vállalat biztonsági követelmény:

Multi-factor Authentication kialakítási lehetőségeit:

| Az eszköz biztonságossá tételéhez | MFA a felhőben | Helyszíni MFA |
| --- | --- | --- |
| Microsoft-alkalmazások |igen |igen |
| SaaS-alkalmazások az alkalmazáskatalógusban |igen |igen |
| Az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások |igen |igen |
| Nem az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások |nem |igen |
| Távoli elérés, VPN vagy RDG |nem |igen |

Bár előfordulhat, hogy kiegyenlítése után a megoldás a stratégia, akkor továbbra is szeretné használni a kiértékelés a fent hol találhatók a felhasználók a.  Emiatt előfordulhat, hogy a megoldás módosítása.  Használja az alábbi táblázat, amely meghatározza, hogy ez:

| Felhasználó helye | Előnyben részesített tervezési beállítás |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication a felhőben |
| Azure AD és helyszíni AD összevonással az AD FS-sel |Mindkettő |
| Az Azure AD és helyszíni AD-bA az Azure AD Connect jelszó-szinkronizálás nélkül |Mindkettő |
| Az Azure AD és helyszíni Azure AD Connect használatával, jelszó-szinkronizálással |Mindkettő |
| A helyszíni AD |Multi-Factor Authentication-kiszolgáló |

> [!NOTE]
> Gondoskodnia kell arról, hogy a multi-factor authentication szolgáltatás tervezési beállítás, amely a kijelölt támogatja-e a funkciókat, a Tervező szükséges.  További információk [válassza ki a legmegfelelőbb többtényezős biztonsági megoldást,](../authentication/concept-mfa-whichversion.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Multi-factor Auth szolgáltatóval
A multi-factor authentication szolgáltatás Azure Active Directory-bérlővel rendelkező globális adminisztrátorok számára alapértelmezés szerint érhető el. Azonban ha szeretné kiterjeszteni a multi-factor authentication szolgáltatás minden felhasználó és/vagy a globális rendszergazdákat szeretne lehessen venni használja ki a Funkciók, például a felügyeleti portálon, egyéni Üdvözlések és jelentéseket, majd vásárolnia és konfigurálnia kell Többtényezős hitelesítési szolgáltató.

> [!NOTE]
> Gondoskodnia kell arról, hogy a multi-factor authentication szolgáltatás tervezési beállítás, amely a kijelölt támogatja-e a funkciókat, a Tervező szükséges. 
> 
> 

## <a name="next-steps"></a>További lépések
[Az adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Lásd még
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

