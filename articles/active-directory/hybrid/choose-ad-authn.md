---
title: Az Azure AD hibrid identitáskezelési megoldásainak hitelesítése
titleSuffix: Active Directory
description: Ez az útmutató a vezérigazgatók, a ciosok, a ciso-k, a fő identitástervezők, a vállalati tervezők, valamint a biztonsági és informatikai döntéshozók számára nyújt segítséget, akik az Azure AD hibrid identitáskezelési megoldásuk hitelesítési módszerének kiválasztásáért felelősek a közép- és nagyvállalatoknál.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e263ecde532a8aaed420932bf355910da201723e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365846"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Válassza ki az Azure Active Directory hibrid identitáskezelési megoldásának megfelelő hitelesítési módszert

A megfelelő hitelesítési módszer kiválasztása az első szempont a szervezetek számára, amelyek az alkalmazásaikat a felhőbe szeretnék áthelyezni. Ne vegye félvállról ezt a döntést a következő okok miatt:

1. Ez az első döntés egy olyan szervezet számára, amely a felhőbe szeretne költözni.

2. A hitelesítési módszer a szervezet felhőbeli jelenlétének kritikus összetevője. Szabályozza az összes felhőbeli adathoz és erőforráshoz való hozzáférést.

3. Ez az alapja az Azure AD összes többi fejlett biztonsági és felhasználói élményfunkciójának.

Az identitás az informatikai biztonság új vezérlősíkja, így a hitelesítés a szervezet hozzáférés-védője az új felhővilághoz. A szervezeteknek olyan identitásvezérlési síkra van szükségük, amely megerősíti a biztonságukat, és biztonságban tartja felhőalkalmazásaikat a behatolóktól.

> [!NOTE]
> A hitelesítési módszer módosításához tervezésre, tesztelésre és potenciálisan állásidőre van szükség. [A szakaszos bevezetés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) nagyszerű módja annak, hogy tesztelje és fokozatosan áttérjen az összevonásról a felhőalapú hitelesítésre.

### <a name="out-of-scope"></a>Hatókörön kívül
Szervezetek, amelyek nem rendelkeznek a meglévő helyszíni címtár-alapterület nem a hangsúly a cikk. Ezek a vállalkozások általában csak a felhőben hoznak létre identitásokat, amely nem igényel hibrid identitáskezelési megoldást. Csak felhőalapú identitások kizárólag a felhőben találhatók, és nincsenek társítva a megfelelő helyszíni identitásokkal.

## <a name="authentication-methods"></a>Hitelesítési módszerek
Ha az Azure AD hibrid identitáskezelési megoldás az új vezérlősík, a hitelesítés a felhőbeli hozzáférés alapja. A megfelelő hitelesítési módszer kiválasztása egy fontos első döntés az Azure AD hibrid identitáskezelési megoldás beállítása. Valósítsa meg a hitelesítési módszert, amely az Azure AD Connect használatával konfigurálva van, amely a felhőben lévő felhasználókat is biztosít.

A hitelesítési módszer kiválasztásához figyelembe kell vennie a választás idejét, meglévő infrastruktúráját, összetettségét és költségét. Ezek a tényezők minden szervezetesetében eltérőek, és idővel változhatnak.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Az Azure AD a következő hitelesítési módszereket támogatja a hibrid identitáskezelési megoldásokhoz.

### <a name="cloud-authentication"></a>Felhőalapú hitelesítés
Ha ezt a hitelesítési módszert választja, az Azure AD kezeli a felhasználók bejelentkezési folyamatát. A zökkenőmentes egyszeri bejelentkezéssel (SSO) együtt a felhasználók anélkül jelentkezhetnek be a felhőalapú alkalmazásokba, hogy újra meg kellene adniuk a hitelesítő adataikat. A felhőalapú hitelesítéssel két lehetőség közül választhat:

**Az Azure AD jelszókivonat-szinkronizálás.** A legegyszerűbb módja annak, hogy engedélyezze a hitelesítést a helyszíni címtárobjektumok az Azure AD-ben. A felhasználók használhatják ugyanazt a felhasználónevet és jelszót, amelyet a helyszínen használnak anélkül, hogy további infrastruktúrát kellene üzembe helyezniük. Az Azure AD egyes prémium szintű funkciói, például az Identity Protection és az [Azure AD tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)jelszókivonat-szinkronizálást igényelnek, függetlenül attól, hogy melyik hitelesítési módszert választja.

> [!NOTE]
> A jelszavak at soha nem tárolja a tiszta szöveg vagy titkosított egy megfordítható algoritmus az Azure AD-ben. A jelszókivonat-szinkronizálás tényleges folyamatáról további információt a [Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect-szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)című témakörben talál.

**Azure AD áthaladási hitelesítése**. Egyszerű jelszó-érvényesítést biztosít az Azure AD hitelesítési szolgáltatások hoz egy vagy több helyszíni kiszolgálókon futó szoftverügynök használatával. A kiszolgálók közvetlenül a helyszíni Active Directoryval ellenőrzik a felhasználókat, ami biztosítja, hogy a jelszó érvényesítése ne történjen meg a felhőben.

A helyszíni felhasználói fiók állapotok, jelszóházirendek és bejelentkezési órák azonnali kényszerítéséhez biztonsági követelménnyel rendelkező vállalatok használhatják ezt a hitelesítési módszert. A tényleges áthárítási hitelesítési folyamatról további információt a [Felhasználói bejelentkezés az Azure AD áthaladási hitelesítésével című témakörben](../../active-directory/hybrid/how-to-connect-pta.md)talál.

### <a name="federated-authentication"></a>Összevont hitelesítés
Ha ezt a hitelesítési módszert választja, az Azure AD átadja a hitelesítési folyamatot egy külön megbízható hitelesítési rendszernek, például a helyszíni Active Directory-összevonási szolgáltatásoknak (AD FS), hogy érvényesítse a felhasználó jelszavát.

A hitelesítési rendszer további speciális hitelesítési követelményeket tud biztosítani. Ilyenek például az intelligenskártya-alapú hitelesítés vagy a külső többtényezős hitelesítés. További információt az [Active Directory összevonási szolgáltatások telepítése című témakörben talál.](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)

A következő szakasz segítségével eldöntheti, hogy melyik hitelesítési módszer a megfelelő az Ön számára egy döntési fa használatával. Ez segít meghatározni, hogy üzembe felhőbeli vagy összevont hitelesítés az Azure AD hibrid identitáskezelési megoldás.

## <a name="decision-tree"></a>Döntési fa

![Azure AD hitelesítési döntési fa](./media/choose-ad-authn/azure-ad-authn-image1.png)

Részletek a döntési kérdésekről:

1. Az Azure AD a bejelentkezést a felhasználók számára anélkül is kezelhető, hogy a helyszíni összetevőkre támaszkodna a jelszavak ellenőrzéséhez.
2. Az Azure AD átadhat felhasználói bejelentkezést egy megbízható hitelesítési szolgáltatónak, például a Microsoft AD FS-nek.
3. Ha felhasználói szintű Active Directory biztonsági házirendeket kell alkalmaznia, például a fiók lejárt, a letiltott fiók, a jelszó lejárt, a fiók zárolt és a bejelentkezési órákat minden egyes felhasználó bejelentkezéskor, az Azure AD-nek szüksége van néhány helyszíni összetevőre.
4. Az Azure AD által natív anonim módon nem támogatott bejelentkezési funkciók:
   * Bejelentkezés intelligens kártyákkal vagy tanúsítványokkal.
   * Bejelentkezés a helyszíni MFA-kiszolgáló használatával.
   * Bejelentkezés harmadik féltől származó hitelesítési megoldással.
   * Többtelephelyes helyszíni hitelesítési megoldás.
5. Az Azure AD Identity Protection jelszókivonat-szinkronizálást igényel, függetlenül attól, hogy melyik bejelentkezési módszert választja, hogy a felhasználók számára *kiszivárgott hitelesítő adatok* jelentést. A szervezetek átvehetik a feladatát a jelszókivonat-szinkronizálásra, ha elsődleges bejelentkezési módszerük meghibásodik, és a hibaesemény előtt konfigurálták.

> [!NOTE]
> Az Azure AD Identity Protection [használatához Azure AD Premium P2-licencek](https://azure.microsoft.com/pricing/details/active-directory/) szükségesk.

## <a name="detailed-considerations"></a>Részletes szempontok

### <a name="cloud-authentication-password-hash-synchronization"></a>Felhőalapú hitelesítés: Jelszókivonat-szinkronizálás

* **erőfeszítés**. A jelszókivonat-szinkronizálás a telepítés, a karbantartás és az infrastruktúra tekintetében a legkevesebb erőfeszítést igényli.  Ez a munkaszint általában azokra a szervezetekre vonatkozik, amelyeknek csak az Office 365-be, SaaS-alkalmazásokba és más Azure AD-alapú erőforrásokba kell bejelentkezniük a felhasználóiknak. Ha be van kapcsolva, a jelszókivonat-szinkronizálás az Azure AD Connect szinkronizálási folyamatának része, és kétpercenként fut.

* **Felhasználói élmény**. A felhasználók bejelentkezési élményének javítása érdekében telepítse a zökkenőmentes egyszeri bejelentkezést jelszókivonat-szinkronizálással. A zökkenőmentes egyszeri bejelentkezés kiküszöböli a szükségtelen utasításokat, amikor a felhasználók be vannak jelentkezve.

* **Speciális forgatókönyvek**. Ha a szervezetek úgy döntenek, hogy, az Azure AD Identity Protection-jelentésekkel az Azure AD Identity Protection jelentésekkel használható identitások ból származó elemzési adatok at azure AD Premium P2. Egy példa a kiszivárgott hitelesítő adatok jelentés. A Windows Hello vállalati verzió [speciális követelményeket támaszt a jelszókivonat-szinkronizálás használatakor.](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification) [Az Azure AD tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) jelszókivonat-szinkronizálást igényel, hogy a felügyelt tartományban a felhasználók vállalati hitelesítő adatokkal rendelkezők kiépítsék.

    A jelszókivonat-szinkronizálást használó többtényezős hitelesítést igénylő szervezeteknek Azure AD többtényezős hitelesítést vagy [feltételes hozzáférésű egyéni vezérlőket](../../active-directory/conditional-access/controls.md#custom-controls-preview)kell használniuk. Ezek a szervezetek nem használhatják a harmadik féltől származó vagy helyszíni többtényezős hitelesítési módszerek, amelyek támaszkodnak összevonása.

> [!NOTE]
> Az Azure AD feltételes [hozzáféréshez Azure AD Premium P1-licencek](https://azure.microsoft.com/pricing/details/active-directory/) szükségesk.

* **Üzletmenet folytonossága**. A jelszókivonat-szinkronizálás felhőalapú hitelesítéssel való használata magas szintű rendelkezésre állású felhőszolgáltatásként, amely az összes Microsoft-adatközpontra méretezhető. Győződjön meg arról, hogy a jelszókivonat-szinkronizálás nem csökken hosszabb ideig, telepítsen egy második Azure AD Connect-kiszolgálót átmeneti módban készenléti konfigurációban.

* **Szempontok**. Jelenleg a jelszókivonat-szinkronizálás nem kényszeríti ki azonnal a helyszíni fiókállapotok módosításait. Ebben az esetben a felhasználó hozzáférhet a felhőalapú alkalmazásokhoz, amíg a felhasználói fiók állapota szinkronizálva van az Azure AD-vel. Előfordulhat, hogy a szervezetek egy új szinkronizálási ciklus futtatásával szeretnék leküzdeni ezt a korlátozást, miután a rendszergazdák tömegesen frissítik a helyszíni felhasználói fiók állapotait. Erre példa a fiókok letiltása.

> [!NOTE]
> A jelszó lejárt, és a fiók zárolt állapotok jelenleg nem szinkronizálva az Azure AD az Azure AD Connect. Amikor módosítja a felhasználó jelszavát, és beállítja a *felhasználónak meg kell változtatnia* a jelszót a következő bejelentkezési zászló, a jelszó kivonat nem lesz szinkronizálva az Azure AD az Azure AD Connect, amíg a felhasználó nem módosítja a jelszavát.

A központi telepítési lépésekért tekintse meg a [jelszókivonat-szinkronizálás implementálását.](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="cloud-authentication-pass-through-authentication"></a>Felhőalapú hitelesítés: Átmenő hitelesítés  

* **erőfeszítés**. Az átmenő hitelesítéshez egy vagy több (javasoljuk három) könnyű ügynökre van szükség a meglévő kiszolgálókon telepítve. Ezeknek az ügynököknek hozzáféréssel kell rendelkezniük a helyszíni Active Directory tartományi szolgáltatásokhoz, beleértve a helyszíni AD tartományvezérlőket is. Kimenő internet- és tartományvezérlő-hozzáférésre van szükségük. Emiatt nem támogatott az ügynökök telepítése a peremhálózaton.

    Az átmenő hitelesítés hez korlátlan hálózati hozzáférés szükséges a tartományvezérlőkhöz. Minden hálózati forgalom titkosított, és csak hitelesítési kérelmekre korlátozódik. Erről a folyamatról további információt a [biztonsági részletesen](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) átmenő hitelesítés.

* **Felhasználói élmény**. A felhasználók bejelentkezési élményének javítása érdekében telepítse a zökkenőmentes egyszeri bejelentkezést az átmenő hitelesítéssel. A zökkenőmentes egyszeri bejelentkezés kiküszöböli a szükségtelen utasításokat a felhasználók bejelentkezése után.

* **Speciális forgatókönyvek**. Az átadó hitelesítés kényszeríti a helyszíni fiók házirendet a bejelentkezés időpontjában. Például a hozzáférés megtagadva, ha egy helyszíni felhasználó fiókállapota le van tiltva, zárolva van, vagy a [jelszava lejár,](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) vagy a bejelentkezési kísérlet kívül esik az on-kóráz, amikor a felhasználó bejelentkezhet.

    Az áteres hitelesítést igénylő többtényezős hitelesítést igénylő szervezeteknek Azure multifactor authentication (MFA) vagy [feltételes hozzáférésegyéni vezérlőket kell használniuk.](../../active-directory/conditional-access/controls.md#custom-controls-preview) Ezek a szervezetek nem használhatják a külső vagy a helyszíni többtényezős hitelesítési módszer, amely támaszkodik az összevonás. A speciális szolgáltatások megkövetelik, hogy a jelszókivonat-szinkronizálás tanusítsa, függetlenül attól, hogy ön az átadó hitelesítést választja-e. Egy példa a kiszivárgott hitelesítő adatok jelentés identitásvédelem.

* **Üzletmenet folytonossága**. Azt javasoljuk, hogy két további áteres hitelesítési ügynökök üzembe helyezését. Ezek az extrák az Azure AD Connect-kiszolgáló első ügynökén kívül találhatók. Ez a további központi telepítés biztosítja a hitelesítési kérelmek magas rendelkezésre állását. Ha három ügynök van telepítve, az egyik ügynök továbbra is sikertelen lehet, ha egy másik ügynök karbantartásra nem működik.

    Az átmenő hitelesítés mellett a jelszókivonat-szinkronizálás üzembe helyezése is egy másik előnye is van. Biztonsági hitelesítési módszerként működik, ha az elsődleges hitelesítési módszer már nem érhető el.

* **Szempontok**. A jelszókivonat-szinkronizálást biztonsági mentési hitelesítési módszerként használhatja az átmenő hitelesítéshez, ha az ügynökök jelentős helyszíni hiba miatt nem tudják érvényesíteni a felhasználó hitelesítő adatait. A jelszókivonat-szinkronizálás nem történik meg automatikusan, és az Azure AD Connect használatával manuálisan kell váltania a bejelentkezési módszerre.

    Az átmenő hitelesítéssel kapcsolatos egyéb szempontokat, például az alternatív azonosítók támogatását a [gyakori kérdések című témakörben tetszetős kérdések ről](../../active-directory/hybrid/how-to-connect-pta-faq.md)itt látható.

A telepítési lépésekről tekintse meg az [áthaladási hitelesítés megvalósítását.](../../active-directory/hybrid/how-to-connect-pta.md)

### <a name="federated-authentication"></a>Összevont hitelesítés

* **erőfeszítés**. Az összevont hitelesítési rendszer egy külső megbízható rendszerre támaszkodik a felhasználók hitelesítéséhez. Egyes vállalatok szeretnék újra felhasználni a meglévő összevont rendszer befektetés az Azure AD hibrid identitáskezelési megoldás. Az összevont rendszer karbantartása és kezelése kívül esik az Azure AD. Az összevont rendszer használatával gondoskodik arról, hogy biztonságosan legyen telepítve, és kezelni tudja a hitelesítési terhelést.

* **Felhasználói élmény**. Az összevont hitelesítés felhasználói élménye az összevonási farm szolgáltatásainak, topológiájának és konfigurációjának megvalósításától függ. Egyes szervezeteknek szükségük van erre a rugalmasságra ahhoz, hogy az összevonási farmhoz való hozzáférést a biztonsági követelményeiknek megfelelően módosítsák. Például konfigurálhatja a belsőleg csatlakoztatott felhasználókat és eszközöket, hogy automatikusan jelentkezzenek be a felhasználókhoz, anélkül, hogy hitelesítő adatokat kérne tőlük. Ez a konfiguráció működik, mert már bejelentkezett az eszközeikre. Szükség esetén egyes speciális biztonsági funkciók megnehezítik a felhasználók bejelentkezési folyamatát.

* **Speciális forgatókönyvek**. Az összevont hitelesítési megoldás általában akkor szükséges, ha az ügyfelek rendelkeznek egy hitelesítési követelmény, amely az Azure AD nem támogatja natívan. Részletes információk, amelyek segítenek [a megfelelő bejelentkezési lehetőség kiválasztásában.](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/) Vegye figyelembe a következő általános követelményeket:

  * Intelligens kártyákat vagy tanúsítványokat igénylő hitelesítés.
  * Helyszíni MFA-kiszolgálók vagy külső többtényezős szolgáltatók, amelyek összevont identitásszolgáltatót igényelnek.
  * Hitelesítés külső hitelesítési megoldásokkal. Tekintse meg az [Azure AD összevonási kompatibilitási listát.](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)
  * Bejelentkezéshez sAMAccountName (például DOMAIN\username) szükséges az egyszerű felhasználónév (UPN) helyett, user@domain.compéldául .

* **Üzletmenet folytonossága**. Az összevont rendszerek általában terheléselosztásos kiszolgálótömböt, más néven farmot igényelnek. Ez a farm egy belső hálózati és peremhálózati topológiában van konfigurálva, hogy biztosítsa a hitelesítési kérelmek magas rendelkezésre állását.

    A jelszókivonat-szinkronizálást és az összevont hitelesítést biztonsági másolat hitelesítési módszerként való üzembe helyezése telepítse, ha az elsődleges hitelesítési módszer már nem érhető el. Ilyen például a helyszíni kiszolgálók nem érhetők el. Egyes nagyvállalati szervezetek összevonási megoldásra van szükségük a geo-DNS-sel konfigurált több internetes be- ésgyártási pont támogatásához az alacsony késésű hitelesítési kérelmekhez.

* **Szempontok**. Az összevont rendszerek általában jelentősebb befektetést igényelnek a helyszíni infrastruktúrába. A legtöbb szervezet ezt a lehetőséget választja, ha már rendelkezik helyszíni összevonási befektetéssel. És ha ez egy erős üzleti követelmény, hogy egy identitás szolgáltató. Az összevonás a felhőalapú hitelesítési megoldásokhoz képest összetettebb a működés és a hibaelhárítás.

Egy nem irányítható tartomány, amely nem ellenőrizhető az Azure AD-ben, a felhasználói azonosító bejelentkezés megvalósításához további konfigurációra van szükség. Ezt a követelményt alternatív bejelentkezési azonosító támogatásnak nevezzük. A korlátozásokat és követelményeket az [Alternatív bejelentkezési azonosító konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) című témakörben található. Ha úgy dönt, hogy egy külső többtényezős hitelesítésszolgáltatót használ az összevonással, győződjön meg arról, hogy a szolgáltató támogatja a WS-Trust szolgáltatást, hogy az eszközök csatlakozhassanak az Azure AD-hez.

A telepítési lépésekről az [összevonási kiszolgálók telepítéséről.](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)

> [!NOTE]
> Az Azure AD hibrid identitáskezelési megoldás üzembe helyezésekor az Azure AD Connect egyik támogatott topológiájának megvalósítását kell megvalósítania. További információ a támogatott és nem támogatott konfigurációkról [az Azure AD Connect topológiáiban.](../../active-directory/hybrid/plan-connect-topologies.md)

## <a name="architecture-diagrams"></a>Építészeti diagramok

Az alábbi ábrák ismertetik az Azure AD hibrid identitáskezelési megoldással használható minden hitelesítési módszerhez szükséges magas szintű architektúra-összetevőket. Áttekintést nyújtanak a megoldások közötti különbségek összehasonlításához.

* A jelszókivonat-szinkronizálási megoldás egyszerűsége:

    ![Azure AD hibrid identitás jelszókivonat-szinkronizálással](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Az átmenő hitelesítés ügynöki követelményei, két ügynök használata redundancia esetén:

    ![Azure AD hibrid identitás átmenő hitelesítéssel](./media/choose-ad-authn/azure-ad-authn-image3.png)

* A szervezet kerületében és belső hálózatában az összevonáshoz szükséges összetevők:

    ![Azure AD hibrid identitás összevont hitelesítéssel](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Módszerek összehasonlítása

|Megfontolandó|Jelszókivonat-szinkronizálás + Zökkenőmentes egyszeri bejelentkezés|Átmenő hitelesítés + zökkenőmentes egyszeri bejelentkezés|Összevonás az AD FS rendszerrel|
|:-----|:-----|:-----|:-----|
|Hol történik a hitelesítés?|A felhőben|A felhőben a helyszíni hitelesítési ügynökkel való biztonságos jelszó-ellenőrzési csere után|Helyszíni követelmények|
|Melyek a helyszíni kiszolgáló követelményei a létesítési rendszeren kívül: Azure AD Connect?|None|Minden további hitelesítési ügynökhöz egy kiszolgáló|Két vagy több AD FS-kiszolgáló<br><br>Két vagy több WAP-kiszolgáló a kerületben/DMZ-hálózatban|
|Milyen követelmények vonatkoznak a helyszíni internetre és a kiépítési rendszeren kívüli hálózatépítésre?|None|[Kimenő internet-hozzáférés](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) a hitelesítési ügynököket futtató kiszolgálókról|[Bejövő internet-hozzáférés](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) wap-kiszolgálókhoz a kerületben<br><br>Bejövő hálózati hozzáférés az AD FS-kiszolgálókhoz a kerületben lévő WAP-kiszolgálókról<br><br>Hálózati terheléselosztás|
|Van-e TLS/SSL tanúsítványkövetelmény?|Nem|Nem|Igen|
|Van-e egészségügyi felügyeleti megoldás?|Nem kötelező|Az Azure [Active Directory felügyeleti központja](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md) által biztosított ügynökállapot|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|A felhasználók egyszeri bejelentkezést kapnak a felhőalapú erőforrásokba a vállalati hálózaton belüli tartományhoz csatlakozó eszközökről?|Igen [zökkenőmentes egyszeri bejelentkezéssel](../../active-directory/hybrid/how-to-connect-sso.md)|Igen [zökkenőmentes egyszeri bejelentkezéssel](../../active-directory/hybrid/how-to-connect-sso.md)|Igen|
|Milyen bejelentkezési típusok támogatottak?|UserPrincipalName + jelszó<br><br>Windows-integrált hitelesítés [zökkenőmentes egyszeri bejelentkezés](../../active-directory/hybrid/how-to-connect-sso.md) használatával<br><br>[Alternatív bejelentkezési azonosító](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + jelszó<br><br>Windows-integrált hitelesítés [zökkenőmentes egyszeri bejelentkezés](../../active-directory/hybrid/how-to-connect-sso.md) használatával<br><br>[Alternatív bejelentkezési azonosító](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + jelszó<br><br>sAMAccountName + jelszó<br><br>Windows-integrált hitelesítés<br><br>[Tanúsítvány és intelligenskártya-hitelesítés](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatív bejelentkezési azonosító](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Támogatott a Windows Hello vállalati verzió?|[Kulcsmegbízhatósági modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Kulcsmegbízhatósági modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*A Windows Server 2016 tartomány működési szintje szükséges*|[Kulcsmegbízhatósági modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Megbízható tanúsítvány modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Melyek a többtényezős hitelesítési lehetőségek?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Egyéni vezérlők feltételes hozzáféréssel*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Egyéni vezérlők feltételes hozzáféréssel*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA-kiszolgáló](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[Külső mfa](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Egyéni vezérlők feltételes hozzáféréssel*](../../active-directory/conditional-access/controls.md)|
|Milyen felhasználói fiókállapotok támogatottak?|Letiltott fiókok<br>(akár 30 perces késés)|Letiltott fiókok<br><br>Fiók zárolva<br><br>A fiók lejárt<br><br>A jelszó lejárt<br><br>Bejelentkezési órák|Letiltott fiókok<br><br>Fiók zárolva<br><br>A fiók lejárt<br><br>A jelszó lejárt<br><br>Bejelentkezési órák|
|Melyek a feltételes hozzáférési lehetőségek?|[Azure AD feltételes hozzáférés, Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Azure AD feltételes hozzáférés, Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Azure AD feltételes hozzáférés, Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[AD FS jogcímszabályok](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Támogatott az örökölt protokollok blokkolása?|[Igen](../../active-directory/conditional-access/overview.md)|[Igen](../../active-directory/conditional-access/overview.md)|[Igen](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Testreszabhatja az emblémát, a képet és a leírást a bejelentkezési oldalakon?|[Igen, az Azure AD Premium szolgáltatással](../../active-directory/fundamentals/customize-branding.md)|[Igen, az Azure AD Premium szolgáltatással](../../active-directory/fundamentals/customize-branding.md)|[Igen](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Milyen speciális forgatókönyvek támogatottak?|[Intelligens jelszózárolás](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Kiszivárgott hitelesítő adatok ról szóló jelentések az Azure AD Premium P2 szolgáltatással](../../active-directory/reports-monitoring/concept-risk-events.md)|[Intelligens jelszózárolás](../../active-directory/authentication/howto-password-smart-lockout.md)|Többhelyes, alacsony késleltetésű hitelesítési rendszer<br><br>[AD FS extranet zárolás](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integráció harmadik fél identitásrendszerekkel](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Az Azure AD feltételes hozzáférés egyéni vezérlői jelenleg nem támogatják az eszközregisztrációt.

## <a name="recommendations"></a>Javaslatok
Az identitáskezelő rendszer biztosítja a felhasználók hozzáférését a felhőalapú alkalmazásokhoz és az áttelepített és a felhőben elérhetővé teendőkhez. Annak érdekében, hogy a jogosult felhasználók produktív és rossz szereplők ki a szervezet bizalmas adatok, a hitelesítés szabályozza az alkalmazásokhoz való hozzáférést.

A következő okok miatt használja vagy engedélyezze a jelszókivonat-szinkronizálást a választott hitelesítési módszerhez:

1. **Magas rendelkezésre állás és vészhelyreállítás**. Az átmenő hitelesítés és összevonás a helyszíni infrastruktúrára támaszkodik. Az átmenő hitelesítéshez a helyszíni helyigény tartalmazza a kiszolgáló hardverét és az átmenő hitelesítési ügynökök által igényelt hálózatot. Az összevonások esetében a helyszíni lábnyom még nagyobb. A proxyhitelesítési kérelmekhez és a belső összevonási kiszolgálókhoz a peremhálózat kiszolgálóira van szükség.

    Az egyes meghibásodási pontok elkerülése érdekében telepítsen redundáns kiszolgálókat. Ezután a hitelesítési kérelmek mindig ki lesznek javítva, ha valamelyik összetevő meghibásodik. Mind az átadó hitelesítés, mind az összevonás a tartományvezérlőkre is támaszkodik a hitelesítési kérelmek megválaszolásában, amelyek szintén sikertelenek lehetnek. Ezen összetevők közül sok nak karbantartásra van szüksége az egészség megőrzéséhez. Kimaradások nagyobb valószínűséggel, ha a karbantartás nem megfelelően tervezett és megfelelően megvalósított. A jelszókivonat-szinkronizálás használatával elkerülheti a kimaradásokat, mivel a Microsoft Azure Felhőalapú hitelesítési szolgáltatás globálisan méretezhető, és mindig elérhető.

2. **Helyszíni leállás túlélési túlélése**.  A kibertámadás vagy katasztrófa miatti helyszíni leállás következményei jelentősek lehetnek, a hírnévmárka károsodásától kezdve a megbénult szervezetig, amely nem képes kezelni a támadást. A közelmúltban számos szervezet rosszindulatú támadások áldozata lett, beleértve a célzott zsarolóprogramokat is, amelyek miatt a helyszíni kiszolgálók leálltak. Amikor a Microsoft segít az ügyfeleknek az ilyen típusú támadások kezelésében, a szervezetek két kategóriáját látja:

   * Azok a szervezetek, amelyek korábban bekapcsolták a jelszókivonat-szinkronizálást, megváltoztatták a hitelesítési módszerüket a jelszókivonat-szinkronizálás használatára. Néhány óra alatt újra online voltak. Az Office 365-ön keresztüli e-mailekhez való hozzáférés használatával a problémák megoldásán és más felhőalapú munkaterhelések elérésén dolgoztak.

   * Azoknak a szervezeteknek, amelyek korábban nem engedélyezték a jelszókivonat-szinkronizálást, nem megbízható külső fogyasztói e-mail rendszereket kellett igénybe tenniük a kommunikációhoz a problémák megoldásához. Ezekben az esetekben hetekbe telt a helyszíni identitás-infrastruktúrájuk visszaállítása, mielőtt a felhasználók újra be tudtak jelentkezni a felhőalapú alkalmazásokba.

3. **Identitásvédelem**. A felhőben lévő felhasználók védelmének egyik legjobb módja az Azure AD Identity Protection az Azure AD Premium P2 szolgáltatással. A Microsoft folyamatosan ellenőrzi az interneten a felhasználói és jelszó listák, hogy a rossz szereplők eladni, és elérhetővé teszik a sötét weben. Az Azure AD használhatja ezt az információt annak ellenőrzésére, ha a szervezet ben a felhasználónevek és jelszavak sérülnek. Ezért fontos, hogy a jelszókivonat-szinkronizálást engedélyezze, függetlenül attól, hogy melyik hitelesítési módszert használja, függetlenül attól, hogy összevont vagy átmenő hitelesítésről van-e szó. A kiszivárgott hitelesítő adatok jelentésként jelennek meg. Ezeket az információkat arra használhatja, hogy letiltsa vagy kényszerítse a felhasználókat jelszavuk módosítására, amikor kiszivárgott jelszavakkal próbálnak bejelentkezni.

## <a name="conclusion"></a>Összegzés

Ez a cikk ismerteti a különböző hitelesítési lehetőségeket, amelyeket a szervezetek konfigurálhatnak és telepíthetnek a felhőalapú alkalmazásokhoz való hozzáférés támogatásához. A különböző üzleti, biztonsági és műszaki követelmények teljesítése érdekében a szervezetek választhatnak a jelszókivonat-szinkronizálás, az átmenő hitelesítés és az összevonás között.

Vegye figyelembe az egyes hitelesítési módszereket. A megoldás üzembe helyezésére tett erőfeszítés és a bejelentkezési folyamat felhasználói élménye megfelel az üzleti követelményeknek? Mérje fel, hogy a szervezetnek szüksége van-e az egyes hitelesítési módszerek speciális forgatókönyveire és üzletmenet-folytonossági szolgáltatásaira. Végül értékelje ki az egyes hitelesítési módszerek szempontjait. Van-e ezek közül bármelyik megakadályozza, hogy végre a választás?

## <a name="next-steps"></a>További lépések

A mai világban a fenyegetések a nap 24 órájában jelen vannak, és mindenhonnan érkeznek. Valósítsa meg a megfelelő hitelesítési módszert, és csökkenti a biztonsági kockázatokat és védi az identitásokat.

[Ismerkedjen meg](../../active-directory/fundamentals/get-started-azure-ad.md) az Azure AD-vel, és telepítse a megfelelő hitelesítési megoldást a szervezet számára.

Ha az összevont hitelesítésről a felhőalapú hitelesítésre való áttérést fontolgatja, további információ [a bejelentkezési módszer módosításáról.](../../active-directory/hybrid/plan-connect-user-signin.md) Az áttelepítés megtervezéséhez és megvalósításához használja ezeket a [projekttelepítési terveket,](https://aka.ms/deploymentplans) vagy fontolja meg az új [szakaszos bevezetési](../../active-directory/hybrid/how-to-connect-staged-rollout.md) funkció használatát az összevont felhasználók áttelepítéséhez a felhőalapú hitelesítés szakaszos megközelítésben történő használatára.
