---
title: "Az Azure Active Directory hibrid identitáskezelési elrendezésével kapcsolatos szempontok - egy hibrid identitás elfogadása stratégia kidolgozása |} Microsoft Docs"
description: "Feltételes hozzáférés-vezérlést az Azure Active Directory ellenőrzi a megadott feltételek, ha a felhasználó hitelesítése és az alkalmazáshoz való hozzáférés előtt válasszon. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítése és hozzáférni az alkalmazáshoz engedélyezett."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 2c57b394beb6382807a4c8c83de975a0ae68d726
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>A hibrid identitás bevezetési stratégia meghatározása
Ebben a feladatban fogja definiálni, a hibrid identitás bevezetési stratégiát a hibrid identitáskezelési megoldás az üzleti követelményeinek megfelelően, a tárgyalt:

* [Az üzleti igények meghatározása](active-directory-hybrid-identity-design-considerations-business-needs.md)
* [Címtár-szinkronizálás követelmények meghatározása](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [A multi-factor authentication követelmények meghatározása](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Üzleti igények stratégia meghatározása
Az első feladat címeket, a szervezet üzleti meghatározása szükséges.  Ez nagyon széles körű lehet, és a hatókör kötés akkor fordulhat elő, ha nincs gondos.  A kezdete legyen egyszerű, de ne felejtsen tervezze meg a Tervező alkalmazásához és a jövőben könnyítse meg a változást.  Függetlenül attól, hogy egy egyszerű tervezett, akár a rendkívül összetett egyikét az Azure Active Directory a Microsoft Identity platform, amely támogatja az Office 365, a Microsoft Online Services és a felhőalapú alkalmazások.

## <a name="define-an-integration-strategy"></a>Az integráció stratégia meghatározása
A Microsoftnál három fő integrációs feladatokhoz, amelyek felhőbeli identitások, a szinkronizált identitások és az összevont identitások kialakítása.  Meg kell terveznie a integrációs stratégiák egyikét alkalmazásakor.  Stratégia eltérőek lehetnek, ugyanakkor a szempontjait ezúttal valamelyik is lehetnek, milyen típusú felhasználói élményt szeretne biztosítani, van néhány, a meglévő infrastruktúra már helyben, és mi az a leginkább költséghatékony.  

![](./media/hybrid-id-design-considerations/integration-scenarios.png)

A fenti ábrán meghatározott a következők:

* **Felhőbeli identitások**: ezek a identitásokat tartalmaz, amelyek kizárólag a felhőben vannak.  Esetén az Azure AD azok kellene lennie kifejezetten az Azure AD-címtár.
* **Szinkronizált**: ezek a identitásokat tartalmaz, amelyek a helyszínen és a felhőben.  Azure AD Connect használatával, ezek a felhasználók vannak létre, vagy a meglévő Azure AD-fiókokkal csatlakoztatva.  A felhasználói jelszó kivonatát a felhő Jelszókivonat úgynevezett szinkronizálja a helyszíni környezetből.  Szinkronizált használatával egy szerint esetén meg kell, hogy ha a felhasználó le van tiltva, a helyszíni környezetben, órába is telhet, legfeljebb 3 adott fiók állapot jelenik meg az Azure ad-ben.  A szinkronizálás alatt az időtartam alatt okozza.
* **Összevont**: ezeket az identitásokat létezik a helyszínen és a felhőben.  Azure AD Connect használatával, ezek a felhasználók vannak létre, vagy a meglévő Azure AD-fiókokkal csatlakoztatva.  

> [!NOTE]
> További információ a szinkronizálási beállítások [a helyszíni identitások integrálása az Azure Active Directoryval](connect/active-directory-aadconnect.md).
> 
> 

Az alábbi táblázat segít meghatározni, hogy a előnyeit és hátrányait foglalja össze a következő stratégiák:

| Stratégia | Előnyei | Hátrányok |
| --- | --- | --- |
| **Felhőbeli identitások** |Könnyebben kezelhető kis szervezet számára. <br> Semmit nem kell telepítenie a helyi nincs szükség további hardverre<br>Könnyen használható, ha a felhasználó elhagyja a vállalatot |Felhasználók kell bejelentkezni számítási feladatok felhőben való hozzáféréskor <br> Előfordulhat, hogy jelszavakat, és nem lehet azonos a felhőalapú és helyszíni identitások |
| **Szinkronizálva** |A helyszíni jelszó lesz a helyszíni hitelesítéshez és a felhőcímtárakat <br>Könnyebben kezelhető a kis, közepes vagy nagyméretű szervezet számára <br>Az egyes erőforrások felhasználók rendelkezhetnek egyszeri bejelentkezés (SSO) <br> A szinkronizálás Microsoft előnyben részesített módszer <br> Könnyebben kezelhető |Egyes felhasználók is vonakodhatnak annak a címtárak szinkronizálása a felhővel adott vállalat rendőrségi miatt |
| **Összevont** |Felhasználók rendelkezhetnek egyszeri bejelentkezés (SSO) <br>Ha a felhasználó le van állítva, vagy hagyja, a fiókot is, azonnal letiltja, és hozzáférés visszavonása esetén<br> Speciális forgatókönyvek, amelyek nem lehet, hitelesítéstípussal szinkronizálva |További lépések telepítése és konfigurálása <br> Magasabb karbantartás <br> További hardverre lehet szükség az STS-infrastruktúra <br> Az összevonási kiszolgáló telepítése további hardverre lehet szükség. További szoftverek szükség, ha az AD FS szolgál <br> Széles körű beállítása szükséges az egyszeri bejelentkezés <br> Kritikus pont hiba, ha az összevonási kiszolgáló nem működik, felhasználók nem tudják hitelesíteni |

### <a name="client-experience"></a>Ügyfélélmény
A használt stratégia szabja meg, a felhasználói bejelentkezési élmény.  A következő táblázatokban a vonatkozó, a felhasználók milyen kell látnia a bejelentkezés során tapasztal élmény kell lennie.  Vegye figyelembe, hogy nem minden összevont identitás-szolgáltatóktól mindegyik forgatókönyvben támogatja az egyszeri Bejelentkezést.

**A tartomány tagja, és saját hálózati alkalmazások**:

|  | Szinkronizált identitás | Összevont identitás |
| --- | --- | --- |
| Webböngészők |Űrlapalapú hitelesítés |Adja meg a szervezet azonosítója szükséges a, néha egyszeri bejelentkezéshez |
| Outlook |Hitelesítő adatok kérése |Hitelesítő adatok kérése |
| Skype vállalati (Lync). |Hitelesítő adatok kérése |egyszeri bejelentkezést Lync, a hitelesítő adatokat kéri az Exchange-hez |
| SkyDrive Pro |Hitelesítő adatok kérése |az egyszeri bejelentkezés |
| Office Pro Plus előfizetés |Hitelesítő adatok kérése |az egyszeri bejelentkezés |

**Külső vagy nem megbízható forrásból**:

|  | Szinkronizált identitás | Összevont identitás |
| --- | --- | --- |
| Webböngészők |Űrlapalapú hitelesítés |Űrlapalapú hitelesítés |
| Az Outlook, a Skype vállalati (Lync) Skydrive Pro, Office-előfizetés |Hitelesítő adatok kérése |Hitelesítő adatok kérése |
| Exchange ActiveSync |Hitelesítő adatok kérése |egyszeri bejelentkezést Lync, a hitelesítő adatokat kéri az Exchange-hez |
| Mobilalkalmazások |Hitelesítő adatok kérése |Hitelesítő adatok kérése |

Ha a feladat, hogy rendelkezik-e a 3. fél valamelyikével adja meg az Azure AD összevonási IdP vagy azok Ugrás 1 megállapította, kell figyelembe vennie a következő támogatott képességek:

* A SAML 2.0-s szolgáltatót, amely megfelel az SP-Lite profil támogathatja a hitelesítés az Azure AD és a társított alkalmazások
* Támogatja a passzív hitelesítést, amely megkönnyíti a hitelesítési OWA, SPO, stb.
* Exchange Online ügyfelek támogatható keresztül a SAML 2.0 fokozott ügyfél profil (ECP)

Emellett figyelembe kell venni, milyen lehetőségek nem lesznek elérhetők:

* WS-megbízhatósági/összevonási támogatás nélkül megszakítja a más aktív ügyfelek
  * Ez azt jelenti, nem Lync ügyfelet, a OneDrive-ügyfél, Office-előfizetés, Office Mobile, Office 2016 előtt
* Átmenet Office passzív hitelesítést lehetővé teszi tiszta SAML 2.0 IdPs támogatja, de támogatási marad a ügyfél-ügyfél alapon

> [!NOTE]
> Olvassa el a cikk http://aka.ms/ssoproviders a legfrissebb listáját.
> 
> 

## <a name="define-synchronization-strategy"></a>Szinkronizálás stratégia meghatározása
Ebben a feladatban határozza meg azon eszközöket, amelyek szinkronizálásához használni fog a szervezet helyszíni adatok a felhőben, és milyen topológia kell használnia.  Mivel a legtöbb szervezet számára az Active Directory használatával, információ az Azure AD Connect használatával a fenti kérdések megoldására részletesen bemutatható valósul meg.  Az Active Directory nem rendelkező környezetekben szerepel a FIM 2010 R2-ről vagy a MIM 2016 használja, hogy ennek a stratégiának megtervezése információ.  Azonban későbbi kiadásokban az Azure AD Connect támogatja az LDAP-címtárak esetén, attól függően, a ütemterv, ez az információ segít lehet.

### <a name="synchronization-tools"></a>Szinkronizálási eszközök
Az évek során több szinkronizálási eszközöket rendelkezik létezett, és különböző környezetben használható.  Az Azure AD Connect jelenleg az összes támogatott forgatókönyvek tetszés szerinti fejlesztőeszközzel Ugrás.  AAD-Szinkronizáló DirSync továbbra is körül és lehet telepítve legyen a környezetben most. 

> [!NOTE]
> A legfrissebb információk a támogatott képességek vonatkozó minden eszköz [címtár-integrációs eszközök összehasonlítása](active-directory-hybrid-identity-design-considerations-tools-comparison.md) cikk.  
> 
> 

### <a name="supported-topologies"></a>Támogatott topológiák
A szinkronizálás stratégia meghatározásakor a használt topológia kell meghatározni. Attól függően, hogy az információk lépésben meghatározott 2 megállapítható, mely topológia a megfelelő melyiket használja. Egyetlen erdő, egyetlen Azure AD-topológia a leggyakoribb, és egyetlen Active Directory-erdő és az Azure AD egyetlen példányát áll.  Ez a forgatókönyvek többsége használható lesz, és a várt topológia Azure AD Connect Expressre használatakor, az alábbi ábrán látható módon.

![](./media/hybrid-id-design-considerations/single-forest.png)Egyetlen erdő forgatókönyv esetén igen elterjedt még kis és nagy szervezeteknek, hogy több erdő, 5. ábrán látható módon.

> [!NOTE]
> További információ a különböző helyszíni és az Azure AD-topológiák az Azure AD Connect szinkronizálási olvassa [az Azure AD Connect topológiák](connect/active-directory-aadconnect-topologies.md).
> 
> 

![](./media/hybrid-id-design-considerations/multi-forest.png) 

Többerdős forgatókönyv

Ha ez a helyzet, akkor a több-forest-egyetlen Azure AD-topológia kell tekinteni a következő elemek teljesülése esetén:

* Felhasználók erdők csak 1 identitással rendelkezik – az egyedi azonosító az alábbi felhasználók a szakasz ismerteti, ez részletesebben.
* A felhasználó hitelesíti magát az identitásukat a erdőt
* Ez az erdő határozza meg UPN és Forráshorgony (megváltoztathatatlan azonosító)
* Minden erdők érhetők el az Azure AD Connect – Ez azt jelenti, hogy azt nem kell tartományhoz csatlakoztatva, és ha ez elősegíti a szegélyhálózaton elhelyezni.
* A felhasználóknál csak egy postaláda
* Az erdő, amelyen a felhasználó postaládájához van a legjobb adatok minőségének attribútumok látható a az Exchange globális cím lista (GAL)
* Ha a felhasználó nem postaláda van, majd bármely erdőben is használható közre ezeket az értékeket
* Ha rendelkezhetnek hivatkozott postafiókkal, majd nincs is egy másik fiókot a bejelentkezéshez használt egy másik erdőben.

> [!NOTE]
> Objektumok, mind a helyszíni szerepelnek, és a felhőben található "keresztül vannak csatlakoztatva" egyedi azonosító. A címtár-szinkronizálás keretében ez az egyedi azonosító nevezzük a SourceAnchor. A környezetben az egyszeri bejelentkezést ezt nevezik a ImmutableId. [Az Azure AD Connect tervezési alapelvek](connect/active-directory-aadconnect-design-concepts.md#sourceanchor) használatára vonatkozó további szempontokról a SourceAnchor.
> 
> 

Ha a fenti nem igaz értékű, és több aktív fiókkal vagy egynél több postaláda, az Azure AD Connect válasszon egyet, és figyelmen kívül hagyja a másikra.  Ha postaládák, de nincs más fiók, ezek a fiókok nem lesznek exportálva az Azure AD, és, hogy a felhasználó nem lesz csoportnak sem tagja.  Ez eltér a módját a DirSync korábban volt, és azt szándékos jobb támogatása Többerdős forgatókönyvekben. Egy Többerdős forgatókönyv az alábbi ábrán látható.

![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 

**Többerdős több Azure AD-forgatókönyv**

Javasoljuk, hogy csak egyetlen könyvtár rendelkezik a szervezet Azure AD-ben, de a támogatott 1:1 kapcsolatot folyamatosan az Azure AD Connect szinkronizálási kiszolgálót és az Azure AD-címtár között.  Minden Azure AD példányához szüksége lesz egy Azure AD Connect telepítése.  Emellett az Azure AD úgy lett kialakítva elkülönített és Azure AD egy példányát a felhasználók nem fognak tudni a felhasználók egy másik példány a.

Már lehetséges és támogatott Active Directory helyszíni példányát egy csatlakozni több Azure AD-címtártól, az alábbi ábrán látható módon:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 

**Egyerdős szűrési forgatókönyv**

Ehhez a következőket kell teljesülniük:

* Az Azure AD Connect szinkronizálási kiszolgálót úgy, hogy egyes objektumok egymást kölcsönösen kizáró csoportja szűréshez kell konfigurálni.  Ez végezhető el, például az egyes tartományokhoz vagy szervezeti egység minden kiszolgálón hatókörének.
* Egy DNS-tartomány csak egy regisztrálható az Azure AD-címtár úgy az UPN-EK a felhasználók a helyszíni AD különálló névterek kell használnia
* Azure AD egy példányát a felhasználók csak tudnak tekintse meg a felhasználók a példányból.  Nem fogják látni a más esetekben a felhasználók
* Csak az Azure AD-címtártól egyik engedélyezheti a helyszíni Exchange hibrid AD
* Kölcsönös kizárólagosság késleltetve visszaírt is vonatkozik.  Így néhány késleltetve visszaírt funkció nem támogatott a topológia, mivel ezek feltételezik, hogy egyetlen helyszíni konfigurációt.  Ehhez a következőket:
  * Késleltetve visszaírt csoport alapértelmezett konfigurációja
  * Eszköz késleltetve visszaírt

Vegye figyelembe, hogy a következő nem támogatott, és megvalósítását, nem kell kiválasztani:

* Nem támogatott több, azonos Azure AD-címtár csatlakozik, akkor is, ha egymást kölcsönösen kizáró set objektum szinkronizálásához vannak konfigurálva az Azure AD Connect szinkronizálási kiszolgálót hozzá
* A több Azure AD-címtártól ugyanaz a felhasználó szinkronizálása nem támogatott. 
* Azt is olyan konfigurációs módosítást ahhoz felhasználók számára egy Azure ad-frissítésként jelenik meg egy másik Azure AD-címtár partnerek a nem támogatott. 
* Egyúttal több Azure AD-címtártól csatlakozni az Azure AD Connect szinkronizálási módosítása nem támogatott.
* Az Azure AD könyvtárakban elkülönített azért vannak. Fontos adatokat olvasni egy másik Azure AD-címtár közös és egységes globális Címlista között a könyvtárak létrehozása az Azure AD Connect szinkronizálási konfigurációjának módosítása nem támogatott. Azt is nem támogatott mint partnereket, hogy a felhasználók exportálása egy másikra a helyszíni AD az Azure AD Connect szinkronizálási szolgáltatás használatával.

> [!NOTE]
> Ha a szervezet a hálózat számítógépeinek korlátozza az csatlakozik az internetre, ez a cikk felsorolja a végpontok (FQDN, IPv4 és IPv6-címtartományok) bele kell foglalni, listák és az Internet Explorer Megbízható helyek zónában ügyfél a kimenő engedélyezése a számítógépek számítógépekre sikeresen használhatja az Office 365. További információk [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>A multi-factor authentication-stratégia meghatározása
Ebben a feladatban határozza meg a multi-factor Authentication hitelesítés stratégiát kíván használni.  Az Azure multi-factor Authentication két különböző verzióban elérhető lesz.  Egy olyan felhőalapú, a másik helyszíni Azure MFA kiszolgáló használatával.  Kiértékelésén alapul adta meg fent meghatározhatja melyik megoldás felel meg a helyes-e egy másik pedig a stratégia.  Az alábbi táblázat segítségével határozhatja meg, mely tervezési lehetőség a vállalati biztonsági követelmény teljesítésére:

Multi-factor Authentication kialakítási lehetőségeit:

| Biztonságos eszköz | MFA a felhőben | Helyszíni MFA |
| --- | --- | --- |
| Microsoft-alkalmazások |igen |igen |
| SaaS-alkalmazások az alkalmazáskatalógusban |igen |igen |
| Az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások |igen |igen |
| Az Azure AD alkalmazás-proxyn keresztül történő nincs közzétéve az IIS-alkalmazások |nem |igen |
| VPN-és a távoli asztali Átjárókiszolgáló távoli hozzáféréshez |nem |igen |

Annak ellenére, hogy előfordulhat, hogy kiegyenlítése után a megoldást a stratégia, továbbra is szeretné a fenti értékelési használatát a felhasználók hol találhatók.  Ez a megoldás módosítása okozhatja.  Használja az alábbi táblázat segít a meghatározása:

| Felhasználó helye | Előnyben részesített tervezési beállítás |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication a felhőben |
| Azure AD és helyszíni AD összevonással az AD FS-sel |Mindkét |
| Az Azure AD és a helyszíni AD az Azure AD Connect nem jelszó-szinkronizálás |Mindkét |
| Az Azure AD és a helyszíni és a jelszó-szinkronizálás az Azure AD Connect használatával |Mindkét |
| A helyszíni AD |Multi-Factor Authentication-kiszolgáló |

> [!NOTE]
> Gondoskodnia kell arról, hogy a multi-factor Authentication hitelesítés tervezési beállítás, amely a kijelölt támogatja-e a kívánt szükséges funkciókat.  További információk [válassza ki a multi-factor Authentication biztonsági megoldást,](../multi-factor-authentication/multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Többtényezős hitelesítésszolgáltató
Többtényezős hitelesítés alapértelmezés szerint egy Azure Active Directory-bérlő rendelkező globális rendszergazdák számára érhető el. Azonban ha terjessze ki a többtényezős hitelesítés minden felhasználó és/vagy a globális rendszergazdák szeretne lehessen venni előny szolgáltatásait, például a felügyeleti portál, az egyéni hónap és a jelentések, majd vásárolnia és konfigurálnia kell Többtényezős hitelesítési szolgáltató.

> [!NOTE]
> Gondoskodnia kell arról, hogy a multi-factor Authentication hitelesítés tervezési beállítás, amely a kijelölt támogatja-e a kívánt szükséges funkciókat. 
> 
> 

## <a name="next-steps"></a>Következő lépések
[Adatvédelmi követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)

