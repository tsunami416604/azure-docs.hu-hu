---
title: Az Active Directory csatlakoztatása az Azure Active Directoryhoz | Microsoft Docs
description: Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz.
keywords: az Azure AD Connect bemutatása, az Azure AD Connect áttekintése, mi az Azure AD Connect, az Active Directory telepítése
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/25/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9c946c9b7d041b1d08dadc9f7bd830d4a1d658ad
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250869"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Hibrid identitáskezelési és Microsoft identitáskezelési megoldások
A [Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) hibrid identitásmegoldásaival szinkronizálhatja helyszíni címtárobjektumait az Azure AD-vel, miközben továbbra is a helyszínen kezelheti a felhasználókat. Amikor megtervezi a helyszíni Windows Server Active Directory és az Azure AD szinkronizálását, elsőként azt kell eldöntenie, hogy szinkronizált vagy összevont identitásokat szeretne-e használni. 

- A **szinkronizált identitások** – és az opcionális jelszókivonatok – segítségével a felhasználók ugyanazt a jelszót használhatják mind a helyszíni, mind a felhőalapú vállalati erőforrások eléréséhez. 
- Az **összevont identitások** nagyobb fokú kontrollt biztosítanak a felhasználók felett, mivel a felhasználók hitelesítése le van választva az Azure-ról, és egy megbízható helyszíni identitásszolgáltató foglalkozik vele. 

A hibrid hitelesítés többféleképpen is konfigurálható. A cég igényeinek leginkább megfelelő identitáskezelési modell kiválasztásakor figyelembe kell vennie az időigényeket, a meglévő infrastruktúrát, a megoldás összetettségét és a felmerülő költségeket. Ezek a tényezők cégenként különbözőek, és idővel változhatnak is, de lehetősége van rugalmasan áttérni egy másik identitáskezelési modellre, ha a követelmények megváltozása ezt szükségszerűvé teszi.

## <a name="synchronized-identity"></a>Szinkronizált identitások 

A szinkronizált identitáskezelés a legegyszerűbb módja a helyszíni címtárobjektumok (felhasználók és csoportok) Azure AD-vel való szinkronizálásának. 

![Szinkronizált hibrid identitások](./media/whatis-hybrid-identity/synchronized-identity.png)

Bár a szinkronizált identitáskezelés a legkönnyebb és leggyorsabb módszer, a felhasználóknak továbbra is külön jelszóra van szükségük a felhőbeli erőforrásokhoz. Ennek elkerülése érdekében dönthet úgy, hogy [szinkronizálja a felhasználói jelszavak kivonatát](how-to-connect-password-hash-synchronization.md) az Azure AD-címtárral. A jelszókivonatok szinkronizálásával a felhasználók ugyanazzal a felhasználónév-jelszó párossal jelentkezhetnek be a felhőalapú vállalati erőforrásokba, mint amelyet a helyszíni rendszereken használnak. Az Azure AD Connect rendszeresen ellenőrzi a változásokat a helyszíni címtárban, és szinkronban tartja az Azure AD-t. Ha egy felhasználói attribútum vagy jelszó megváltozik a helyszíni Active Directoryban, automatikusan frissül az Azure AD-ben. 

A legtöbb cég számára, amelyek csak az Office 365-be, SaaS-alkalmazásokba és más Azure AD-alapú erőforrásokba szeretnék lehetővé tenni a felhasználók bejelentkezését, az alapértelmezett jelszó-szinkronizálási beállítás használata javasolt. Ha ez nem megfelelő megoldás, el kell eldöntenie, hogy átmenő hitelesítést vagy AD FS-t szeretne-e használni.

> [!TIP]
> A felhasználói jelszavakat a helyszíni Windows Server Active Directory tárolja egy kivonatérték formájában, amely a tényleges felhasználói jelszót jelöli. A kivonatérték egy egyirányú matematikai függvény (a kivonatoló algoritmus) eredménye. Az egyirányú függvény eredménye semmilyen módszerrel nem fejthető vissza a jelszó egyszerű szöveges verziójára. A jelszókivonattal nem lehet a helyszíni hálózatra bejelentkezni. Ha úgy dönt, hogy szinkronizálja a jelszavakat, az Azure AD Connect kinyeri a jelszókivonatokat a helyszíni Active Directoryból, és további biztonsági intézkedéseket alkalmaz rajtuk, mielőtt szinkronizálná őket az Azure AD-vel. A jelszó-szinkronizálás és a jelszóvisszaírás együttes használata lehetővé teszi az új jelszó önkiszolgáló kérését az Azure AD-ben. Egyszeri bejelentkezést (single sign-on, SSO) is engedélyezhet a vállalati hálózathoz kapcsolódó, tartományhoz csatlakozó számítógépeken dolgozó felhasználók számára. Az egyszeri bejelentkezéssel a felhasználóknak csak egy felhasználónevet kell megadniuk a felhőalapú erőforrások biztonságos eléréséhez. 
>

## <a name="pass-through-authentication"></a>Átmenő hitelesítés

Az [Azure AD átmenő hitelesítés](how-to-connect-pta.md) egy egyszerű jelszó-érvényesítési megoldást biztosít a helyszíni Active Directoryt használó Azure AD-alapú szolgáltatásokhoz. Ha a cég biztonsági és megfelelőségi szabályzatai még kivonatolt formában sem engedélyezik a felhasználói jelszavak küldését, és csak a tartományhoz csatlakoztatott eszközökön kell támogatnia az asztali egyszeri bejelentkezést, a kiértékelést átmenő hitelesítéssel javasolt megoldani. Az átmenő hitelesítéshez nincs szükség DMZ-beli üzembe helyezésre, ami az AD FS-hez képest egyszerűbb üzembehelyezési infrastruktúrát eredményez. Az Azure AD-vel való bejelentkezéskor ez a hitelesítési módszer közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat.

![Átmenő hitelesítés](./media/whatis-hybrid-identity/pass-through-authentication.png)

Az átmenő hitelesítéssel nincs szükség komplex hálózati infrastruktúrára, és a helyszíni jelszavakat nem kell a felhőben tárolni. Az egyszeri bejelentkezéssel kombinálva az átmenő hitelesítés egy valóban integrált folyamatot kínál az Azure AD-be vagy egyéb felhőszolgáltatásokba való bejelentkezéshez.

Az átmenő hitelesítés az Azure AD Connecttel van konfigurálva, amely egy, a jelszó-érvényesítési kéréseket figyelő egyszerű helyszíni ügynököt használ. Az ügynök több gépre is könnyen telepíthető a magas rendelkezésre állás és a kellő terheléselosztás érdekében. Mivel minden kommunikáció kimenő irányú, az összekötőt nem kell a DMZ-be telepíteni. Az összekötőt futtató kiszolgáló számítógépre vonatkozó követelmények:

- Windows Server 2012 R2 vagy újabb rendszerűnek kell lennie
- Egy olyan tartományhoz kell csatlakoznia az erdőben, amelyen keresztül a felhasználók érvényesítése zajlik

## <a name="federated-identity-ad-fs"></a>Összevont identitások (AD FS)

A felhasználók Office 365-höz és egyéb felhőszolgáltatásokhoz való hozzáférésének szigorúbb szabályozásához beállíthat címtár-szinkronizálást egyszeri bejelentkezéssel (SSO) az [Active Directory összevonási szolgáltatások (AD FS)](how-to-connect-fed-whatis.md) használatával. A felhasználói bejelentkezések az AD FS-sel való összevonása a hitelesítést egy, a felhasználói hitelesítő adatokat ellenőrző helyszíni kiszolgálóra delegálja. Ebben a modellben a helyszíni Active Directoryban megtalálható hitelesítő adatok nem kerülnek el az Azure AD-be.

![Összevont identitások](./media/whatis-hybrid-identity/federated-identity.png)

Ez az identitás-összevonásnak is nevezett bejelentkezési módszer garantálja, hogy a felhasználók hitelesítésének ellenőrzésére a helyszínen kerüljön sor, így a rendszergazdák szigorúbb hozzáférés-vezérlési intézkedéseket foganatosíthatnak. Az identitás-összevonás és az AD FS együttes használata a legbonyolultabb megoldás, amelyhez további kiszolgálókat kell telepíteni a helyszíni környezetben. Az identitás-összevonás használata azzal is jár, hogy napi 24 órás támogatást kell biztosítani az Active Directory- és AD FS-infrastruktúrához. Az ilyen magas fokú támogatásra azért van szükség, mert ha a helyszíni internetkapcsolat, tartományvezérlő vagy AD FS-kiszolgálók nem működnek, a felhasználók nem tudnak bejelentkezni a felhőalapú szolgáltatásokba.

> [!TIP]
> Ha az összevont identitáskezelést az Active Directory összevonási szolgáltatásokkal (AD FS) együtt használja, dönthet úgy, hogy biztonsági tartalékként a jelszó-szinkronizálást is konfigurálja, hogy lehessen mit használni, ha az AD FS-infrastruktúra leáll.
>

## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok

Szeretnénk bemutatni néhány gyakori hibrid identitás- és hozzáféréskezelési helyzetet és a hozzájuk ajánlott hibrid identitáskezelési lehetősége(ke)t.

|Cél:|PWS és SSO<sup>1</sup>| PTA és SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|A helyszíni Active Directoryban létrehozott új felhasználói, kapcsolattartói és csoportfiókok automatikus szinkronizálása a felhőbe|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|A bérlő beállítása hibrid Office 365-forgatókönyvekhez|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|A felhőszolgáltatásokba való bejelentkezés engedélyezése a felhasználók helyszíni jelszavaival|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|A vállalati hitelesítő adatokat használó egyszeri bejelentkezés implementálása|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|Gondoskodás arról, hogy a jelszókivonatok ne tárolódjanak a felhőben| |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|Felhőbeli többtényezős hitelesítési megoldások engedélyezése| |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|Helyszíni többtényezős hitelesítési megoldások engedélyezése| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|Intelligens kártyával végzett hitelesítés támogatása a felhasználók számára<sup>4</sup>| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|
|Jelszólejárati értesítések megjelenítése az Office portálon és a Windows 10 asztali verziójában| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Jelszó-szinkronizálás egyszeri bejelentkezéssel.
>
> <sup>2</sup> Átmenő hitelesítés és egyszeri bejelentkezés. 
>
> <sup>3</sup> Összevont egyszeri bejelentkezés AD FS-sel.
>
> <sup>4</sup> Az AD FS a vállalati nyilvános kulcsú infrastruktúrával is integrálható, hogy tanúsítványokkal is be lehessen jelentkezni. Ezek a tanúsítványok lehetnek megbízható regisztrációs csatornákon (például MDM-en vagy csoportházirend-objektumon) keresztül kiosztott szoftveres tanúsítványok, intelligenskártya-tanúsítványok (beleértve a PIV/CAC-kártyákat) vagy Vállalati Windows Hello- (tanúsítvány-megbízhatósági) megoldások. Az intelligens kártyás hitelesítés támogatásával kapcsolatos további információkat [ebben a blogban](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/) talál.
>

## <a name="what-is-azure-ad-connect"></a>Mi az Azure AD Connect?

A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat.  Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz.  Ez a tevékenység a következő jellemzőkkel bír:
    
- [Szinkronizálás](how-to-connect-sync-whatis.md) – ez az összetevő a felhasználók, csoportok és egyéb objektumok létrehozásáért felelős. Segítségével arról is meggyőződhet, hogy a helyszíni felhasználókhoz és csoportokhoz tartozó identitásadatok megegyeznek a felhőben található hasonló adatokkal.  Emellett a jelszókivonatok Azure AD-vel történő szinkronizálását is elvégzi.
- [Jelszókivonatok szinkronizálása](how-to-connect-password-hash-synchronization.md) – Egy opcionális összetevő, amely szinkronizálja a felhasználói jelszavak kivonatát az Azure AD-vel, így lehetővé teszi, hogy a felhasználók ugyanazt a jelszót használják a helyszínen és a felhőben.
-   [AD FS és összevonás-integráció](how-to-connect-fed-whatis.md) – az összevonás az Azure AD Connect nem kötelező összetevője, amelynek segítségével helyszíni AD FS-infrastruktúrát használó hibrid környezetet konfigurálhat. AD FS-kezelési képességeket is biztosít, például tanúsítványmegújítást és további AD FS-kiszolgálók üzembe helyezését.
-   [Átmenő hitelesítés](how-to-connect-pta.md) – Egy másik nem kötelező összetevő, amely lehetővé teszi a felhasználóknak, hogy ugyanazt a jelszót használják a helyszínen és a felhőben, de nem igényli az összevont környezet infrastruktúráját.
-   [A PingFederate és az összevonás-integráció](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) – Egy másik összevonási lehetőség, amelyben a PingFederate használható identitásszolgáltatóként.
-   [Állapotfigyelés](whatis-hybrid-identity-health.md) – Az Azure AD Connect Health hatékony monitorozási képességgel rendelkezik, valamint egy központi helyet biztosít az Azure Portalon az ilyen tevékenységek megtekintéséhez. 


![Mi az az Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Mi az Azure AD Connect Health?

Az Azure Active Directory (Azure AD) Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe. Megbízható Office 365- és Microsoft Online Services-kapcsolat fenntartását teszi lehetővé olyan fontos identitás-összetevők megfigyelésével, mint például az Active Directory összevonási szolgáltatások (AD FS) kiszolgálói, az Azure AD Connect-kiszolgálók (más néven a szinkronizálási motor) vagy az Active Directory-tartományvezérlők stb. Ezen összetevők legfontosabb adatpontjait is könnyen hozzáférhetővé teszi, így lekérdezhetővé válnak a használatra és egyéb lényeges jellemzőkre vonatkozó adatok, hogy megalapozott döntéseket hozhasson.

Ez az információ az [Azure AD Connect Health portálon](https://aka.ms/aadconnecthealth) található meg. Az Azure AD Connect Health portálon riasztásokat, teljesítményfigyelési adatokat, használatelemzési információkat és egyéb információkat tekinthet meg. Az Azure AD Connect Health egyetlen helyre gyűjti a legfontosabb identitás-összetevők állapotadatait.

![Mi az az Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Az Azure AD Connect Health funkcióinak bővülésével a portál egy identitásalapú irányítópultot kínál. Ezáltal egy nagy teljesítményű, kifogástalan állapotú és integrált környezetet biztosít, ahol a felhasználók még hatékonyabban dolgozhatnak.


## <a name="why-use-azure-ad-connect"></a>Miért érdemes az Azure AD Connect megoldást használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. A felhasználók és a szervezetek az alábbi előnyöket élvezhetik:

* A felhasználók egyetlen identitással férhetnek hozzá olyan helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365.
* Mivel egyetlen eszközről van szó, a szinkronizáláshoz és bejelentkezéshez használt rendszer üzembe helyezése egyszerűen végrehajtható.
* Többféle alkalmazási helyzethez is biztosítja az elérhető legújabb képességeket. Az Azure AD Connect olyan identitásintegrációs eszközök régebbi verzióit váltja fel, mint például a DirSync és az Azure AD Sync. További információk: [Hybrid Identity directory integration tools comparison](plan-hybrid-identity-design-considerations-tools-comparison.md) (Hibrid identitás: a címtár-integrációs eszközök összehasonlítása).

## <a name="why-use-azure-ad-connect-health"></a>Miért érdemes az Azure AD Connect Health eszközt használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások eléréséhez közös identitás áll a rendelkezésükre. Ez az integráció azonban szükségessé teszi a környezet megfelelő állapotának biztosítását, hogy a felhasználók bármilyen eszközről megbízhatóan hozzáférhessenek a helyszíni és a felhőalapú erőforrásokhoz is. Az Azure AD Connect Health segít megfigyelni az Office 365 és más Azure AD-alkalmazások eléréséhez használt helyszíni identitás-infrastruktúrát, és további betekintést nyújt annak működésébe. Használata éppolyan egyszerű, mintha egy-egy ügynököt telepítene a helyszíni identitás-kiszolgálókra.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health for AD FS](how-to-connect-health-adfs.md)
Az AD FS-hez készült Azure AD Connect Health a Windows Server 2008 R2, a Windows Server 2012, a Windows Server 2012 R2 és a Windows Server 2016 rendszeren támogatja az AD FS 2.0-s verzióját. Ezen kívül támogatja az AD FS proxy- vagy webalkalmazás-proxykiszolgálók figyelését is, amelyek az extranetes hozzáféréshez biztosítanak hitelesítési támogatást. Az állapotügynök egyszerűen és gyorsan végrehajtható telepítésével elérhetővé válnak az Azure AD Connect Health for AD FS legfontosabb képességei.

#### <a name="key-benefits-and-best-practices"></a>Főbb előnyök és ajánlott eljárások

- *Fokozott biztonság*
  - [Extranet zárolási tendenciák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Jelentések a sikertelen bejelentkezésekről](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Adatvédelmi megfelelőség](reference-connect-health-user-privacy.md)    
- *Riasztás az összes [kritikus ADFS-rendszerhibáról](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Kiszolgálók konfigurálása és rendelkezésre állása 
    - [Teljesítmény és csatlakozási lehetőségek](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Rendszeres karbantartás    
- *Könnyen üzembe helyezhető és kezelhető*
  - Gyors [ügynöktelepítés](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - Az ügynökök automatikus frissítése a legújabb verzióra 
  - Az adatok perceken belül elérhetők a portálon    
- *Részletes [használati metrikák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - A leggyakrabban használt alkalmazások
  - Hálózati helyek és TCP-kapcsolat
  - Jogkivonat-kérelmek kiszolgálónként    
- *Nagyszerű felhasználói élmény* 
  - Az Azure Portal irányítópult stílusa
  - [Riasztások e-mailben](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Kiemelt funkciók

*   Riasztásokkal kiegészített megfigyelés az AD FS és az AD FS-proxykiszolgálók nem megfelelő állapotának észleléséhez;
*   kritikus riasztásokhoz kapcsolódó e-mail értesítések;
*   Teljesítményadat-tendenciák, amelyek hasznosak az AD FS kapacitástervezéséhez;
*   Az AD FS használatának különböző területekre (például alkalmazásokra, felhasználókra vagy hálózati helyekre) kiterjedő elemzései
*   AD FS-jelentések, például a rossz felhasználónév/jelszó párossal legtöbbet próbálkozó 50 felhasználó, az utolsó IP-címükkel együtt
*   Kockázatos IP jelentés meghiúsult AD FS-bejelentkezésekhez

Itt olvashat többet [az Azure AD Connect Health használatáról az AD FS szolgáltatással](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health szinkronizálási szolgáltatás](how-to-connect-health-sync.md)
Az Azure AD Connect Health szinkronizálási szolgáltatás figyelemmel kíséri a helyszíni Active Directory és az Azure AD közötti szinkronizálásokat, és információkat biztosít róluk. Az Azure AD Connect Health for Sync legfontosabb képességei a következők:

* Riasztásokkal kiegészített monitorozás az Azure AD Connect-kiszolgáló (más néven a szinkronizálási motor) nem megfelelő állapotának észleléséhez;
* kritikus riasztásokhoz kapcsolódó e-mail értesítések;
* A szinkronizálási műveletek elemzései, beleértve a rájuk vonatkozó késési diagramokat és olyan különböző műveletek tendenciáit, mint például a hozzáadások, a frissítések vagy a törlések;
* A szinkronizálási tulajdonságok és az Azure AD-re történt legutóbbi sikeres exportálási művelet gyors áttekintése
* Az objektumszintű szinkronizációs hibákról való jelentésekhez \(nem szükséges Prémium szintű Microsoft Azure AD\)

Itt olvashat többet [az Azure AD Connect Health szinkronizálási szolgáltatás használatáról](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health for AD DS](how-to-connect-health-adds.md)
Az Active Directory Domain Serviceshez (AD DS) készült Azure AD Connect Health a Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 vagy Windows Server 2016 rendszeren telepített tartományvezérlők figyelését teszi lehetővé. Az állapotügynök telepítése lehetővé teszi, hogy a helyszíni AD DS-környezetet a felhőből figyelje meg. Az Azure AD Connect Health for AD DS legfontosabb képességei a következők:

* Riasztások figyelése a tartományvezérlő megfelelő állapotának észlelése érdekében, valamint e-mailes értesítések küldése a kritikus riasztásokról
* A tartományvezérlő irányítópultja gyors betekintést biztosít a tartományvezérlők állapotába és üzemeltetési állapotába
* A Replikáció állapota irányítópult, amely a legújabb replikációs adatokkal rendelkezik, a hibák észlelésekor hibaelhárítási útmutatókra mutató hivatkozásokkal szolgál
* Gyors és helyfüggetlen hozzáférést biztosít a népszerű teljesítményszámlálók teljesítményadat-grafikonjaihoz, melyek hibaelhárítási és figyelési célból szükségesek

Itt olvashat többet [az Azure AD Connect Health használatáról az AD DS szolgáltatással](how-to-connect-health-adds.md)

## <a name="next-steps"></a>További lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)|
- [Átmenő hitelesítés](how-to-connect-pta.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md) 
- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
- [Verzióelőzmények](reference-connect-version-history.md)
- [Címtár-integrációs eszközök összehasonlítása](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect – gyakori kérdések](reference-connect-faq.md)









