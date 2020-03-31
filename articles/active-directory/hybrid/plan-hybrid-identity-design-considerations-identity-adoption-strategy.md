---
title: Hibrid identitástervezés – bevezetési stratégia Azure | Microsoft dokumentumok
description: Feltételes hozzáférés-vezérléssel az Azure Active Directory ellenőrzi a felhasználó hitelesítésekénél és az alkalmazáshoz való hozzáférés engedélyezésekor választott feltételeket. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítve lesz, és hozzáférést biztosít az alkalmazáshoz.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109334"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Hibrid identitásbevezetési stratégia definiálása
Ebben a feladatban a hibrid identitásbevezetési stratégia a hibrid identitáskezelési megoldás, hogy megfeleljen az üzleti követelményeknek, amelyek tárgyaltak:

* [Határozza meg az üzleti igényeket](plan-hybrid-identity-design-considerations-business-needs.md)
* [Címtár-szinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Többtényezős hitelesítési követelmények meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Az üzleti igények reklénystratégiájának meghatározása
Az első feladat a szervezetek üzleti igényeinek meghatározásával foglalkozik.  Ez lehet nagyon széles és hatálya creep is előfordulhat, ha nem vigyázunk.  Az elején, tartsa egyszerű, de mindig emlékezni, hogy tervezzen egy design, amely befogadására és megkönnyítése változás a jövőben.  Függetlenül attól, hogy egyszerű vagy rendkívül összetett kialakításról van-e szó, az Azure Active Directory a Microsoft Identity platform, amely támogatja az Office 365-öt, a Microsoft Online Services-t és a felhőalapú alkalmazásokat.

## <a name="define-an-integration-strategy"></a>Integrációs stratégia meghatározása
A Microsoft három fő integrációs forgatókönyvvel rendelkezik, amelyek a felhőidentitások, a szinkronizált identitások és az összevont identitások.  Meg kell terveznie az integrációs stratégiák egyikének elfogadását.  A választott stratégia változhat, és a választássorán hozott döntések közé tartozhat, hogy milyen típusú felhasználói élményt szeretne biztosítani, rendelkezik-e meglévő infrastruktúrával, és mi a leginkább költséghatékony.  

![integrációs forgatókönyvek](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

A fenti ábrán meghatározott forgatókönyvek a következők:

* **Felhőalapú identitások:** ezek olyan identitások, amelyek kizárólag a felhőben találhatók.  Az Azure AD esetén azok kifejezetten az Azure AD-címtárban találhatók.
* **Szinkronizált:** ezek olyan identitások, amelyek a helyszínen és a felhőben léteznek.  Az Azure AD Connect használatával ezek a felhasználók vagy létrejönnek, vagy csatlakoznak a meglévő Azure AD-fiókokhoz.  A felhasználó jelszókivonata szinkronizálva van a helyszíni környezetből a felhőbe, amelyet jelszókivonatnak nevezünk.  Szinkronizált használata esetén az egyik kikötés, hogy ha egy felhasználó le van tiltva a helyszíni környezetben, akár három órát is igénybe vehet, amíg a fiók állapota megjelenik az Azure AD-ben.  Ennek oka a szinkronizálási időintervallum.
* **Összevont:** ezek az identitások léteznek a helyszíni és a felhőben.  Az Azure AD Connect használatával ezek a felhasználók vagy létrejönnek, vagy csatlakoznak a meglévő Azure AD-fiókokhoz.  

> [!NOTE]
> A szinkronizálási beállításokkal kapcsolatos további információkért olvassa el [a Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)című részt.
> 
> 

Az alábbi táblázat segít meghatározni az alábbi stratégiák előnyeit és hátrányait:

| Stratégia | Előnyök | Hátrányok |
| --- | --- | --- |
| **Felhőidentitások** |Könnyebb kezelni a kis szervezet. <br> Nincs mit telepíteni a helyszínen. Nincs szükség további hardverre<br>Könnyen letiltható, ha a felhasználó elhagyja a vállalatot |A felhasználóknak be kell jelentkezniük, amikor hozzáférnek a számítási feladatokhoz a felhőben <br> Előfordulhat, hogy a jelszavak nem azonosak a felhőbeli és helyszíni identitások esetében |
| **Szinkronizált** |A helyszíni jelszó hitelesíti a helyszíni és a felhőbeli könyvtárakat is <br>Könnyebben kezelhető kis, közepes vagy nagy szervezetek számára <br>A felhasználók egyes erőforrásokhoz egyszeri bejelentkezés (SSO) is rendelkezhetnek <br> A Microsoft által előnyben részesített szinkronizálási módszer <br> Könnyebben kezelhető |Egyes ügyfelek vonakodhatnak a könyvtárak szinkronizálását a felhővel az adott vállalat rendőrsége miatt |
| **Összevont** |A felhasználók egyszeri bejelentkezést (SSO) <br>Ha egy felhasználó megszűnik vagy kilép, a fiók azonnal letiltható, és a hozzáférés visszavonható,<br> Támogatja azokat a speciális forgatókönyveket, amelyek nem valósíthatók meg szinkronizált |További lépések a beállításhoz és a konfiguráláshoz <br> Nagyobb karbantartás <br> További hardverre lehet szükség az STS-infrastruktúrához <br> Az összevonási kiszolgáló telepítéséhez további hardverre lehet szükség. További szoftverre van szükség, ha AD FS-t használ <br> Kiterjedt beállítás szükséges az SSO-hoz <br> Kritikus meghibásodási pont, ha az összevonási kiszolgáló nem található, a felhasználók nem tudják hitelesíteni magukat |

### <a name="client-experience"></a>Ügyfélélmény
A stratégia, amely a felhasználó bejelentkezési élményt diktálja.  Az alábbi táblázatok tájékoztatást nyújtanak arról, hogy a felhasználóknak milyen bejelentkezési élményre kell számítaniuk.  Nem minden összevont identitásszolgáltató támogatja az SSO-t minden esetben.

**Doman-csatlakozott és magánhálózati alkalmazások:**

|  | Szinkronizált identitás | Federated Identity |
| --- | --- | --- |
| Webböngészők |Űrlapalapú hitelesítés |egyszeri bejelentkezés, néha a szervezeti azonosító biztosításához szükséges |
| Outlook |Hitelesítő adatok bekérése |Hitelesítő adatok bekérése |
| Skype Vállalati verzió (Lync) |Hitelesítő adatok bekérése |egyszeri bejelentkezés a Lyncbe, hitelesítő adatokat kér az Exchange-hez |
| OneDrive Vállalati verzió |Hitelesítő adatok bekérése |egyszeri bejelentkezés |
| Office Pro Plus-előfizetés |Hitelesítő adatok bekérése |egyszeri bejelentkezés |

**Külső vagy nem megbízható források:**

|  | Szinkronizált identitás | Federated Identity |
| --- | --- | --- |
| Webböngészők |Űrlapalapú hitelesítés |Űrlapalapú hitelesítés |
| Outlook, Skype Vállalati verzió (Lync), OneDrive Vállalati verzió, Office-előfizetés |Hitelesítő adatok bekérése |Hitelesítő adatok bekérése |
| Exchange ActiveSync |Hitelesítő adatok bekérése |egyszeri bejelentkezés a Lyncbe, hitelesítő adatokat kér az Exchange-hez |
| Mobilalkalmazások |Hitelesítő adatok bekérése |Hitelesítő adatok bekérése |

Ha az első feladatból úgy döntött, hogy rendelkezik egy külső idP-vel, vagy az Azure AD összevonásának biztosításához fogja használni, tisztában kell lennie a következő támogatott képességekkel:

* Az SP-Lite-profilnak megfelelő SAML 2.0-s szolgáltatók támogathatják az Azure AD és a kapcsolódó alkalmazások hitelesítését
* Támogatja a passzív hitelesítést, amely megkönnyíti az OWA, SPO stb.
* Az Exchange Online-ügyfelek az SAML 2.0 enhanced client profile (ECP) segítségével támogatottak

Tisztában kell lennie azzal is, hogy milyen képességek nem lesznek elérhetők:

* A WS-Trust/Federation támogatás nélkül az összes többi aktív ügyfél megtör
  * Ez azt jelenti, hogy az Office 2016 előtt nincs Lync-ügyfél, OneDrive-ügyfél, Office-előfizetés, Office Mobile
* Az Office passzív hitelesítésre való áttérése lehetővé teszi számukra, hogy támogassák a tiszta SAML 2.0 azonosítókat, de a támogatás továbbra is ügyfélenként történik

> [!NOTE]
> A legfrissebb lista az [Azure AD összevonási kompatibilitási listáját](how-to-connect-fed-compatibility.md)tartalmazza.
> 
> 

## <a name="define-synchronization-strategy"></a>Szinkronizálási stratégia meghatározása
Ebben a feladatban határozza meg azokat az eszközöket, amelyek a szervezet helyszíni adatainak a felhővel való szinkronizálásához, valamint a használni kívánt topológiát használják.  Mivel a legtöbb szervezet az Active Directoryt használja, az Azure AD Connect használatával kapcsolatos információk a fenti kérdések megválaszolására részletesen rendelkezésre állnak.  Az Active Directoryval nem rendelkező környezetek esetén a FIM 2010 R2 vagy a MIM 2016 használatával kapcsolatos információk segítenek a stratégia megtervezésében.  Az Azure AD Connect jövőbeli kiadásai azonban támogatják az LDAP-könyvtárakat, így az idővonaltól függően ezek az információk segíthetnek.

### <a name="synchronization-tools"></a>Szinkronizálási eszközök
Az évek során számos szinkronizálási eszköz létezett, és különböző forgatókönyvekhez használt.  Jelenleg az Azure AD Connect az összes támogatott forgatókönyvhez választható eszköz.  AAD Szinkronizál és DirSync van is csendes körül és május egyenletes lenni bemutat -ban -a környezet most. 

> [!NOTE]
> Az egyes eszközök támogatott képességeivel kapcsolatos legfrissebb információkért olvassa el a [Címtárintegrációs eszközök összehasonlító](plan-hybrid-identity-design-considerations-tools-comparison.md) cikkét.  
> 
> 

### <a name="supported-topologies"></a>Támogatott topológiák
Szinkronizálási stratégia definiálásakor meg kell határozni a használt topológiát. A 2. Az egyetlen erdő, egyetlen Azure AD topológia a leggyakoribb, és egyetlen Active Directory-erdőből és az Azure AD egyetlen példányából áll.  Ez lesz használva a legtöbb forgatókönyv, és a várt topológia használata esetén az Azure AD Connect Express telepítés, ahogy az alábbi ábrán látható.

![Támogatott topológiák](./media/plan-hybrid-identity-design-considerations/single-forest.png) Egyerdős forgatókönyv Gyakori, hogy a nagy, sőt kis szervezetek több erdővel rendelkeznek, ahogy az 5.

> [!NOTE]
> A különböző helyszíni és Azure AD-topológiák ról az Azure AD Connect szinkronizálásával kapcsolatos további információkért olvassa el az [Azure AD Connect Topologies című cikkét.](plan-connect-topologies.md)
> 
> 

![többerdős topológia](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Többerdős forgatókönyv

Ebben az esetben a többerdős egyetlen Azure AD-topológia akkor kell figyelembe venni, ha a következő elemek teljesülnek:

* A felhasználók csak 1 identitással rendelkeznek az összes erdőben – az egyedileg azonosító felhasználók szakasz ezt részletesebben ismerteti.
* A felhasználó hitelesíti magát abban az erdőben, amelyben a személyazonossága található
* Upn és forrás horgony (nem módosítható id) fog származni az erdő
* Az Azure AD Connect minden erdő elérhető – ez azt jelenti, hogy nem kell tartományhoz csatlakozni, és elhelyezhető egy DMZ-ben, ha ez megkönnyíti ezt.
* A felhasználóknak csak egy postaládája van
* A felhasználó postaládáját tároló erdő rendelkezik a legjobb adatminőséggel az Exchange globális címlistájában (GAL) látható attribútumokhoz
* Ha nincs postaláda a felhasználón, akkor bármelyik erdő felhasználható ezen értékek
* Ha összekapcsolt postaládával rendelkezik, akkor van egy másik fiók is egy másik erdőben, amelyet a bejelentkezéshez használnak.

> [!NOTE]
> A helyszíni és a felhőben egyaránt létező objektumok "egyedi azonosítón keresztül" vannak "csatlakoztatva". A címtár-szinkronizálás környezetében ezt az egyedi azonosítót SourceAnchor-nak nevezzük. Az egyszeri bejelentkezés összefüggésében ezt immutableId-nek nevezzük. [Tervezési fogalmak az Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) további szempontok at a SourceAnchor használatával kapcsolatban.
> 
> 

Ha a fentiek nem igazak, és egynél több aktív fiókkal vagy egynél több postaládával rendelkezik, az Azure AD Connect kiválasztegyet, és figyelmen kívül hagyja a másikat.  Ha csatolt postaládák, de nincs más fiók, ezek a fiókok nem lesznek exportálva az Azure AD-be, és a felhasználó nem lesz tagja semmilyen csoportnak.  Ez eltér attól, ahogyan a dirsync-szel korábban volt, és szándékosan támogatja ezeket a többerdős forgatókönyveket. A többerdős forgatókönyv az alábbi ábrán látható.

![több Azure AD-bérlő](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Többerdős, több Azure AD-forgatókönyv**

Javasoljuk, hogy csak egy könyvtárat az Azure AD egy szervezet, de támogatott, hogy egy 1:1 kapcsolat között az Azure AD Connect szinkronizálási kiszolgáló és egy Azure AD könyvtár.  Az Azure AD minden példányához szüksége van az Azure AD Connect telepítésére.  Emellett az Azure AD, a tervezés elkülönítve van, és a felhasználók egy példányát az Azure AD nem fogja látni a felhasználók egy másik példányban.

Az Active Directory egy helyszíni példányát több Azure AD-könyvtárhoz csatlakoztathatja, az alábbi ábrán látható módon:

![egyetlen erdőszűrés](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Egyerdős szűrési forgatókönyv**

Ehhez a következőnek kell igaznak lennie:

* Az Azure AD Connect szinkronizálási kiszolgálókat be kell állítani a szűréshez, hogy mindegyik nek kölcsönösen kizáró objektumkészlete legyen.  Ez például úgy történt, hogy minden kiszolgálót egy adott tartományra vagy szervezeti egységre külön-külön felkell ügyezni.
* Egy DNS-tartomány csak egyetlen Azure AD-címtárban regisztrálható, így a helyszíni AD felhasználóinak UPN-einek külön névtereket kell használniuk.
* Az Azure AD egy példányában a felhasználók csak a saját példányból láthatják a felhasználókat.  A többi esetben nem fogják látni a felhasználókat
* Csak az egyik Az Azure AD könyvtárak engedélyezheti az Exchange hibrid a helyszíni AD
* A kölcsönös kizárólagosság a visszaírásra is vonatkozik.  Ez legyártja, hogy néhány visszaírási funkció nem támogatott ezzel a topológiával, mivel ezek egyetlen helyszíni konfigurációt feltételeznek.  Az érintett műveletek közé tartoznak az alábbiak:
  * Csoportvisszaírás alapértelmezett konfigurációval
  * Eszköz visszaírása

A következők nem támogatottak, és nem választhatók implementációként:

* Nem támogatott, ha több Azure AD Connect-szinkronizálási kiszolgáló csatlakozik ugyanahhoz az Azure AD-könyvtárhoz, még akkor sem, ha úgy vannak konfigurálva, hogy szinkronizálják a kölcsönösen kizáró objektumkészletet
* Nem támogatott, hogy szinkronizálja ugyanazt a felhasználót több Azure AD-könyvtárak. 
* Az is nem támogatott, hogy a konfigurációs változás, hogy a felhasználók egy Azure AD-ben jelennek meg a kapcsolattartók egy másik Azure AD-címtárban. 
* Emellett nem támogatott az Azure AD Connect-szinkronizálás módosítása, hogy több Azure AD-könyvtárhoz csatlakozzon.
* Az Azure AD-könyvtárak kialakítás szerint elkülönítettek. Nem támogatott az Azure AD Connect-szinkronizálás konfigurációjának módosítása egy másik Azure AD-könyvtárból származó adatok olvasásához, hogy egy közös és egységes ÍTETT gal-t hozzon létre a könyvtárak között. Az Azure AD Connect-szinkronizálás használatával a felhasználók kapcsolattartók ként történő exportálása nem támogatott.

> [!NOTE]
> Ha a szervezet korlátozza a hálózat számítógépeit az internethez való csatlakozásban, ez a cikk felsorolja azokat a végpontokat (FQDNs, IPv4 és IPv6 címtartományok), amelyeket fel kell tüntetnie a kimenő engedélyezési listákban és az Internet Explorer megbízható helyek zónájában az ügyfelek számítógépeket, hogy a számítógépek sikeresen használhassák az Office 365-öt. További információért olvassa el az [Office 365 URL-címeit és IP-címtartományait.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Többtényezős hitelesítési stratégia meghatározása
Ebben a feladatban határozza meg a többtényezős hitelesítési stratégiát.  Az Azure többtényezős hitelesítés két különböző verzióban érkezik.  Az egyik egy felhőalapú, a másik pedig az Azure MFA-kiszolgáló használatával helyszíni.  A fenti értékelés alapján meghatározhatja, hogy melyik megoldás a megfelelő a stratégiájához.  Az alábbi táblázat segítségével meghatározhatja, hogy melyik tervezési lehetőség felel meg legjobban a vállalat biztonsági követelményének:

Többtényezős tervezési lehetőségek:

| Biztonságos eszköz | MFA a felhőben | Helyszíni MFA |
| --- | --- | --- |
| Microsoft-alkalmazások |igen |igen |
| SaaS-alkalmazások az alkalmazáskatalógusban |igen |igen |
| Az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások |igen |igen |
| Az Azure AD alkalmazásproxyn keresztül közzé nem tett IIS-alkalmazások |nem |igen |
| Távelérés VPN-ként, RDG-ként |nem |igen |

Annak ellenére, hogy előfordulhat, hogy a stratégia megoldása alapján telepedett le, akkor is fentről kell használnia az értékelést a felhasználók tartózkodási helye alapján.  Ez a megoldás megváltozását okozhatja.  Az alábbi táblázat segítségével meghatározhatja ezt:

| Felhasználó helye | Előnyben részesített tervezési beállítás |
| --- | --- |
| Azure Active Directory |Többtényezős hitelesítés a felhőben |
| Azure AD és helyszíni AD összevonással az AD FS-sel |Mindkettő |
| Az Azure AD és a helyszíni AD az Azure AD Connect használatával nincs jelszószinkronizálás |Mindkettő |
| Az Azure AD és a helyszíni azure AD Connect jelszószinkronizálással |Mindkettő |
| Helyszíni AD |Multi-Factor Authentication-kiszolgáló |

> [!NOTE]
> Azt is győződjön meg arról, hogy a kiválasztott többtényezős hitelesítés tervezési beállítás támogatja a tervezéshez szükséges funkciókat.  További [információ: Válassza ki a többtényezős biztonsági megoldást.](../authentication/concept-mfa-howitworks.md)
> 

## <a name="multi-factor-auth-provider"></a>Többtényezős hitelesítésszolgáltató
A többtényezős hitelesítés alapértelmezés szerint elérhető az Azure Active Directory-bérlővel rendelkező globális rendszergazdák számára. Ha azonban a többtényezős hitelesítést ki szeretné terjeszteni az összes felhasználóra és/vagy a globális rendszergazdákra, hogy kihasználhassa az olyan funkciókat, mint a felügyeleti portál, az egyéni üdvözlések és a jelentések, akkor meg kell vásárolnia és konfigurálnia kell a többtényezős hitelesítésszolgáltatót.

> [!NOTE]
> Azt is győződjön meg arról, hogy a kiválasztott többtényezős hitelesítés tervezési beállítás támogatja a tervezéshez szükséges funkciókat. 
> 
> 

## <a name="next-steps"></a>További lépések
[Az adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)

