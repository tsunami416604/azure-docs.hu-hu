---
title: Válassza ki a megfelelő hitelesítési módszert az Azure AD hibrid identitáskezelési megoldás |} A Microsoft Docs
description: Az útmutató segítségével CEOs, informatikai vezetők, CISOs, vezető identitás Architects, nagyvállalati Rendszertervezőknek és biztonsági és az informatikai döntéshozókat felelős azok az Azure AD hibrid identitáskezelési megoldás egy olyan hitelesítési módszer kiválasztása a közepes és nagy szervezetek is használnak.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: f1fd60774f5790a514e540984812fc1aaf6e38e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238913"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldás 

Ez a cikk egy sorozat, amely megkönnyíti a szervezetek számára a teljes Azure Active Directory (Azure AD) hibrid identitáskezelési megoldás megvalósítása kezdődik. Ez a megoldás volt című rész, mint a [hibrid identitás digitális átalakítási keretrendszer](https://aka.ms/aadframework). Az üzleti eredmények kiterjed, és a hatékony és biztonságos hibrid identitáskezelési megoldás megvalósításához célok szervezetek összpontosíthat. 

Az első üzleti eredmény-keretrendszer határozza meg a szervezetek számára, hogy a hitelesítési folyamat biztonságos, felhőalapú alkalmazások felhasználók általi elérésekor követelményei. Az első üzleti a hitelesítéshez biztonságos üzleti eredmény a célja a felhőalapú alkalmazásokba a helyszíni felhasználónevek és jelszavak használatával jelentkezzen be, hogy a felhasználók. A bejelentkezési folyamat, és hogy miképpen hitelesítik a felhasználókat lehetővé teszik, minden a felhőben.

Az első szempont a szervezet számára, aki a saját alkalmazások a felhőbe helyezheti át a megfelelő hitelesítési módszer választása. Nem kell ezt a döntést a kisebb terhelésű, a következő okok miatt:

1. Az első döntés, amely a felhőbe szeretne szervezet számára fontos. 

2. A hitelesítési módszer a felhőben a szervezet jelenlét kritikus összetevője. Az összes felhőbeli adatok és erőforrások elérését szabályozza azt.

3. Ez az alapja az összes egyéb speciális biztonsági és felhasználói élményt javító szolgáltatások az Azure ad-ben.

4. A hitelesítési módszert is nehézkes megvalósítása után módosítsa.

Identitás számítástechnikai biztonsági új vezérlősíkját. Így a hitelesítés a szervezet hozzáférés guard az új felhő világába az. Cégeknek szükségük van egy identitás vezérlősík erősíti a biztonsági állapotuk és biztonságban tartja a felhőalapú alkalmazások.

### <a name="out-of-scope"></a>Hatókörén kívül
Egy meglévő helyszíni címtár erőforrás-igényű nem rendelkező szervezeteknek a lépéseknek az ismertetése, ez a cikk nem. Ezek a vállalatok általában csak a felhőben, amelyek nem igényelnek a hibrid identitáskezelési megoldás létrehozása az identitások. Kizárólag felhőalapú Identitások csak a felhőben található, és nem megfelelő a helyszíni identitások kapcsolódnak.

## <a name="authentication-methods"></a>Hitelesítési módszerek
Ha az Azure AD hibrid identitáskezelési megoldás az új vezérlősík, hitelesítési az alapja a cloud-access. A megfelelő hitelesítési módszer választása az beállítása az Azure AD hibrid identitáskezelési megoldás kulcsfontosságú első döntése. A hitelesítési módszer, amely konfigurálva van, amely emellett kiosztja a felhőbeli felhasználók az Azure AD Connect használatával hajtja végre.

Válasszon hitelesítési módszert, fontolja meg a idő, a meglévő infrastruktúra, az összetettséget és a költség végrehajtási választott kell. Ezek a tényezők különböző minden szervezet számára, és idővel változhatnak. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Az Azure AD hibrid identitáskezelési megoldások az alábbi hitelesítési módszereket támogatja.

### <a name="cloud-authentication"></a>Felhőalapú hitelesítés
Ha ezt a hitelesítési módszert választja, az Azure AD felhasználói bejelentkezési folyamat kezeli. Közvetlen egyszeri bejelentkezés (SSO) szolgáltatással párosítva, felhasználók jelentkezhetnek be a felhőalkalmazások írja be újra a hitelesítő adatok nélkül. A felhőalapú hitelesítés a két lehetőség közül választhat: 

**Az Azure AD a Jelszókivonat-szinkronizálás**. A legegyszerűbben a helyszíni címtárobjektumok az Azure AD-hitelesítés engedélyezése. A felhasználók használhatják ugyanazt a felhasználónevet és jelszót használják-e a helyszíni további infrastruktúra üzembe helyezése nélkül. Egyes prémium szintű funkciók az Azure AD Identity Protection, például a Jelszókivonat-szinkronizálás szükséges, függetlenül attól, hogy mely hitelesítési módszert válasszon.

> [!NOTE] 
> Jelszavak soha nem szövegként tárolt és az Azure ad-ben visszafejthető algoritmus titkosított. A Jelszókivonat-szinkronizálás folyamatáról további információk: [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization). 

**Az Azure AD átmenő hitelesítés**. Egy egyszerű jelszó érvényesítése az Azure AD hitelesítési szolgáltatásokat biztosít a szoftver ügynököt futtat egy vagy több helyszíni kiszolgálók használatával. A kiszolgálók ellenőrzése a felhasználók közvetlenül a helyszíni Active Directory, amely biztosítja, hogy a jelszó érvényesítése nem történik a felhőben. 

Vállalatok egy biztonsági követelmény, hogy azonnal érvénybe lépteti a helyi felhasználói fiók állapotok, jelszóházirendek, és a bejelentkezési órában használhatja ezt a hitelesítési módszert. A tényleges átmenő hitelesítési folyamat további információkért lásd: [felhasználó jelentkezzen be az Azure AD átmenő hitelesítésének](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta).

### <a name="federated-authentication"></a>Az összevont hitelesítés
Ha ezt a hitelesítési módszert választja, az Azure AD teljes ki egy külön megbízható hitelesítési rendszerre, például a helyszíni Active Directory összevonási szolgáltatások (AD FS), a hitelesítési folyamat a jelszó érvényesítése.

A hitelesítési rendszere biztosíthat további speciális hitelesítés követelményeinek. Példák a következők: intelligenskártya-alapú hitelesítést vagy harmadik felek többtényezős hitelesítést. További információkért lásd: [Active Directory összevonási szolgáltatások telepítése](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Az alábbi szakasz segít eldönteni, melyik hitelesítési módszert az Önnek megfelelő döntési fa használatával. Ez segít határozza meg, hogy a felhő vagy összevont hitelesítés az Azure AD hibrid identitáskezelési megoldás üzembe helyezéséhez.

## <a name="decision-tree"></a>Döntési fa

![Az Azure AD hitelesítési döntési fa](media/azure-ad/azure-ad-authn-image1.png)

Döntési kérdések adatai:

1. Az Azure AD ellenőrizze a jelszavakat a helyszíni összetevők igénylése nélkül képes kezelni jelentkezzen be a felhasználók számára.
2. Azure ad-ben is kiosztják felhasználói bejelentkezés, egy megbízható hitelesítésszolgáltató, például a Microsoft Active Directory összevonási szolgáltatások.
3. Felhasználói szintű Active Directory biztonsági házirendeket alkalmazhatnak, mint például a fiók lejárt van szüksége, ha le van tiltva a fiók jelszava lejárt, a fiók zárolt, és a felhasználói bejelentkezési óra jelentkezzen be, az Azure AD igényel néhány helyi összetevőt.
4. Bejelentkezési módok nem támogatja natív módon az Azure ad-ben:
   * Jelentkezzen be az intelligens kártyákkal, illetve tanúsítványokkal.
   * Jelentkezzen be a helyszíni MFA-kiszolgáló használatával.
   * Jelentkezzen be 3. fél hitelesítési megoldás használatával.
   * Többhelyes kapcsolat a helyszíni hitelesítési megoldás.
5. Az Azure AD Identity Protection függetlenül attól, milyen bejelentkezési módszert választja, adja meg a "Kiszivárogtatott hitelesítő adatokkal rendelkező felhasználók" jelentést a Jelszókivonat-szinkronizálás szükséges. Szervezetek számára történő Jelszókivonat-szinkronizálás feladatátvételt is, ha az elsődleges bejelentkezési módszer nem jár sikerrel, és a hiba esemény előtt konfigurálva volt.

## <a name="detailed-considerations"></a>Részletes szempontok

### <a name="cloud-authentication-password-hash-synchronization"></a>Felhőalapú hitelesítés: a Jelszókivonat-szinkronizálás

* **Tevékenységi**. A Jelszókivonat-szinkronizálás központi telepítési, karbantartási és infrastruktúra legalább részéről az erőfeszítés szükséges.  Ez a tevékenységi szint általában a szervezeteknek, amelyek csak szüksége van a bejelentkezni az Office 365-höz, az SaaS-alkalmazások és más Azure AD-alapú erőforrások vonatkozik. Bekapcsolása esetén a Jelszókivonat-szinkronizálás az Azure AD Connect szinkronizálási folyamat része, és két percenként.

* **Felhasználói élmény**. Felhasználói bejelentkezési élmény javítása érdekében a Jelszókivonat-szinkronizálás közvetlen egyszeri bejelentkezés üzembe. Közvetlen egyszeri bejelentkezés használata esetén nem szükségtelen utasításokat, ha a felhasználó be van jelentkezve.

* **Speciális forgatókönyvek**. Ha szervezetek választja, akkor lehet is használható elemzéseket készítsenek identitások használata az Azure AD Identity Protection jelentéseket. Ilyen például, mint a kiszivárgott hitelesítő adatok a jelentés. Windows Hello for Business egy másik lehetőség, amely rendelkezik [konkrét követelmények, a Jelszókivonat-szinkronizálás használata esetén](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    A Jelszókivonat-szinkronizálás a többtényezős hitelesítést igénylő szervezeteknek az Azure AD többtényezős hitelesítést kell használnia. Azon szervezetek külső vagy a helyszíni többtényezős hitelesítési módszer nem használható.

* **Üzletmenet-folytonossági**. A Jelszókivonat-szinkronizálás használata a felhőalapú hitelesítés egy felhőalapú szolgáltatás, amely az összes Microsoft-adatközpontok méretezhető, magas rendelkezésre állású. Ahhoz, hogy a Jelszókivonat-szinkronizálás nem hosszabb ideig leáll, üzembe helyezése átmeneti módban a készenléti konfigurációban egy második Azure AD Connect-kiszolgáló.

* **Megfontolandó szempontok**. Jelenleg a Jelszókivonat-szinkronizálás nem azonnal érvénybe lépteti az helyi fiók Államokban módosításokat. Ebben a helyzetben a felhasználó hozzáfér a felhőalkalmazásokhoz, amíg az Azure AD szinkronizálása a felhasználói fiók állapota. Ez a korlátozás áthidalható futtatásával egy új szinkronizálási ciklust követően a rendszergazdák a frissítéseket a helyi felhasználói fiók állapotok tömeges szervezeteknek érdemes. Egy példa a fiókok letiltása.

> [!NOTE]
> A jelszó lejárt, és a fiók zárolt állapotok jelenleg az Azure AD az Azure AD Connect nem szinkronizálja. 

Tekintse meg [Jelszókivonat-szinkronizálás megvalósítása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) a telepítési lépéseket.

### <a name="cloud-authentication-pass-through-authentication"></a>Felhőalapú hitelesítés: az átmenő hitelesítés  

* **Tevékenységi**. Átmenő hitelesítés, szüksége lesz egy vagy több (javasoljuk, hogy három) könnyen használható, a meglévő kiszolgálókon telepített ügynökök. Ezeket az ügynököket kell férniük a helyszíni Active Directory tartományi szolgáltatások, többek között a helyszíni AD-tartományvezérlőket. Kimenő Internet-hozzáférés és a hozzáférést a tartományvezérlőkhöz van szükségük. Ezen okból kifolyólag nem támogatott a szegélyhálózaton az ügynökök telepítéséhez. 

    Az átmenő hitelesítés tartományvezérlők korlátozás hálózati hozzáférést igényel. Az összes hálózati forgalom titkosítva, és a hitelesítési kérések korlátozódik. Ezen folyamatról további információkért lásd: a [biztonság részletes bemutatása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-security-deep-dive) az átmenő hitelesítést.

* **Felhasználói élmény**. Felhasználói bejelentkezési élmény javítása érdekében telepítse a közvetlen egyszeri bejelentkezés az átmenő hitelesítés. Közvetlen egyszeri bejelentkezés használata esetén nem szükségtelen utasításokat után a felhasználók bejelentkeznek.

* **Speciális forgatókönyvek**. Az átmenő hitelesítés az helyi fiók házirend megköveteli a bejelentkezés alkalmával. Ha például a hozzáférés megtagadva állapot le van tiltva, egy helyi felhasználói fiók zárolva, amikor vagy [jelszava lejárt](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) vagy az órákat, amikor a felhasználó számára engedélyezett bejelentkezni kívül esik. 

    Többtényezős hitelesítés az átmenő hitelesítést igénylő szervezeteknek az Azure multi-factor Authentication (MFA) kell használnia. Ezek a szervezetek egy külső vagy a helyszíni többtényezős hitelesítési módszer nem használható. Speciális funkciók szükséges, hogy a Jelszókivonat-szinkronizálás üzemel-e úgy dönt, hogy átmenő hitelesítést. Ilyen például, a kiszivárgott hitelesítő adatok a jelentés az Identity Protection.

* **Üzletmenet-folytonossági**. Azt javasoljuk, hogy a két további átmenő hitelesítési ügynökök telepítése. Ezek a kiegészítő funkciók az Azure AD Connect-kiszolgáló az első ügynök mellett vannak. Ez további üzembe helyezési biztosítja a magas rendelkezésre állás, a hitelesítési kérelmek. Három ügynök telepítve van, ha egy ügynök továbbra is sikertelen lehet, ha egy másik ügynök karbantartás miatt nem üzemel. 

    Átmenő hitelesítés mellett a Jelszókivonat-szinkronizálás telepítése egy másik előnye van. Ha már nem érhető el az elsődleges hitelesítési módszert funkcionál a biztonsági mentési hitelesítési módszert.

* **Megfontolandó szempontok**. Használhatja a Jelszókivonat-szinkronizálás átmenő hitelesítéshez, a biztonsági mentési hitelesítési módszerként, ha az ügynökök a felhasználó hitelesítő adatait egy helyszíni jelentős hiba miatt nem lehet érvényesíteni. Feladatátvétel a Jelszókivonat-szinkronizálás automatikusan nem történik, és az Azure AD Connect a bejelentkezési módszer váltás manuálisan kell használnia. 

    Egyéb szempontok az átmenő hitelesítés, beleértve a másik azonosító támogatja, lásd: [– gyakori kérdések](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq).

Tekintse meg [átmenő hitelesítés végrehajtására](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) a telepítési lépéseket.

### <a name="federated-authentication"></a>Az összevont hitelesítés

* **Tevékenységi**. Egy összevont hitelesítési rendszere támaszkodik egy külső megbízható rendszer hitelesítheti a felhasználókat. Egyes vállalatok szeretné újból felhasználhatja a meglévő összevont rendszer befektetés az Azure AD hibrid identitáskezelési megoldás. A karbantartás és a összevont rendszer kezelését az Azure AD vezérlése kívül esik. Van arra a szervezeti: Ellenőrizze, hogy biztonságosan van telepítve, és a hitelesítési terhelést képes kezelni az összevont rendszer használatával. 

* **Felhasználói élmény**. A felhasználói élmény az összevont hitelesítés attól függ, hogy az a Funkciók, topológia és az összevonási kiszolgálófarm konfigurációja megvalósítását. Egyes szervezetek kell ezt a rugalmasságot, adaptáláshoz és az összevonási kiszolgálófarmhoz a biztonsági követelményektől a hozzáférést konfigurálja. Például akkor lehet belső csatlakozó felhasználók és eszközök jelentkezzen be a felhasználók automatikusan, anélkül, hogy a névtelen hitelesítő adatok konfigurálása. Ez a konfiguráció működik, mivel azok már bejelentkezett a eszközeiket. Ha szükséges, bizonyos speciális biztonsági funkciók megnehezítheti felhasználói bejelentkezési folyamat.

* **Speciális forgatókönyvek**. Egy összevont hitelesítési megoldás általában kötelező, ha az ügyfél rendelkezik egy hitelesítési követelmények, amely az Azure AD nem támogatja natív módon. Részletes információkat lásd: [válassza ki a bejelentkezési jobb lehetőséget](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Vegye figyelembe a következő általános követelményeknek:

    * A hitelesítés, amely szükséges az intelligens kártyákkal vagy tanúsítványokkal.
    * A helyszíni MFA-kiszolgálóval, vagy többtényezős külső szolgáltatók.
    * Hitelesítés a harmadik fél hitelesítési megoldások használatával. Tekintse meg a [az Azure AD összevonás kompatibilitási listája](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-compatibility).
    * Jelentkezzen be, amely szükséges az sAMAccountName, például TARTOMÁNY\felhasználónév, helyett egy egyszerű felhasználónév (UPN), például user@domain.com.

* **Üzletmenet-folytonossági**. Összevont rendszerek általában szükség van a kiszolgálók, a farm néven egy elosztott terhelésű tömbje. Ezen a farmon konfigurálva van egy belső hálózatot és a peremhálózati topológia hitelesítési kéréseket a magas rendelkezésre állásának biztosításához.

    Telepítse a Jelszókivonat-szinkronizálás az összevont hitelesítés mellett egy biztonsági mentési hitelesítési módszerként, amikor már nem érhető el az elsődleges hitelesítési módszert. Ilyen például, ha a helyszíni kiszolgálók nem érhetők el. Néhány nagyvállalat geo-DNS közel valós idejű hitelesítési kéréseket konfigurálva több Internet bejövő pontok támogatásához összevonási megoldásra van szüksége.

* **Megfontolandó szempontok**. Összevont rendszerek általában a helyszíni infrastruktúra több jelentős befektetéseket igényel. A legtöbb szervezet válassza ezt a lehetőséget, ha azok már rendelkezik egy helyszíni összevonási. És ha az erős üzleti igény egy egyszeres identitást szolgáltatóval. Összevonási a felhőalapú hitelesítés megoldásokhoz képest összetettebb működtetéséhez és hibaelhárításához.

Útválasztást tartományhoz, amely az Azure ad-ben nem lehetett ellenőrizni felhasználói azonosító bejelentkezési megvalósításához további konfigurációt kell. Ez a követelmény az úgynevezett másodlagos bejelentkezési azonosító használatának támogatását. Lásd: [másik bejelentkezési azonosító konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) vonatkozó korlátokat és követelményeket. Ha egy harmadik felek többtényezős hitelesítési szolgáltató használata összevonási választja, győződjön meg arról, a szolgáltató támogatja a WS-Trust, az eszközök Azure AD join.

Tekintse meg [összevonási kiszolgálók üzembe helyezése](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) a telepítési lépéseket.

> [!NOTE] 
> Az Azure AD hibrid identitáskezelési megoldás telepítésekor meg kell valósítani a támogatott topológiák az Azure AD Connect egyik. Ismerje meg a további támogatott és nem támogatott konfigurációi, [az Azure AD Connect-topológiák](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies).

## <a name="architecture-diagrams"></a>Architektúra-diagramok

Az alábbi ábrák a magas szintű architektúra minden hitelesítési módszert használhatja az Azure AD hibrid identitáskezelési megoldás szükséges összetevők tagolni. Hasonlítsa össze a megoldások segítségével áttekintést nyújtanak.

* Az egyszerűség jelszó Jelszókivonat-szinkronizálási megoldások:

    ![A Jelszókivonat-szinkronizálás az Azure AD hibrid identitás](media/azure-ad/azure-ad-authn-image2.png)

* Átmenő hitelesítés követelményeinek ügynök:

    ![Az átmenő hitelesítés az Azure AD hibrid identitás](media/azure-ad/azure-ad-authn-image3.png)

* A szegélyhálózat és a szervezet belső hálózati az összevonáshoz szükséges összetevők:

    ![Az összevont hitelesítés az Azure AD hibrid identitás](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Módszerek összehasonlítása

|Szempontok|A Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés|Az átmenő hitelesítés + közvetlen egyszeri bejelentkezés|Összevonás az AD FS rendszerrel|
|:-----|:-----|:-----|:-----|
|Amikor megtörténik a hitelesítés?|A felhőben|Egy biztonságos jelszó ellenőrzési exchange helyszíni hitelesítési ügynök után a felhőben|Helyszíni követelmények|
|Mik azok a helyszíni kiszolgáló követelmények meghaladják a kiépítési rendszer: az Azure AD Connect?|None|Egy kiszolgáló minden további hitelesítési ügynök|Két vagy több AD FS-kiszolgálók<br><br>A szegélyhálózat-alapú vagy szegélyhálózat (DMZ) hálózatban két vagy több WAP-kiszolgálók|
|Mik a helyszíni Internet követelményei és hálózatkezelési túl a kiépítési rendszer?|None|[Kimenő Internet-hozzáférés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) a kiszolgálókról a futtató hitelesítési ügynökök|[Bejövő Internet-hozzáférés](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) a WAP-kiszolgálókat a szegélyhálózaton<br><br>Bejövő hálózati hozzáférést AD FS-kiszolgálók a WAP-kiszolgálókat a szegélyhálózaton<br><br>Hálózati terheléselosztás|
|Van egy SSL-tanúsítványra vonatkozó követelménnyel?|Nem|Nem|Igen|
|Van egy állapotfigyelési megoldás?|Nem szükséges|Ügynök állapota által biztosított [Azure Active Directory felügyeleti központ](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|Felhasználók beszerzésének egyszeri bejelentkezés a felhőbeli erőforrásokhoz a vállalati hálózaton belüli tartományhoz csatlakoztatott eszközökről?|Igen, a [közvetlen egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Igen, a [közvetlen egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Igen|
|Milyen bejelentkezési típusok támogatottak?|UserPrincipalName + jelszó<br><br>Az integrált Windows-hitelesítés [közvetlen egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternatív bejelentkezési Azonosítóval](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)|UserPrincipalName + jelszó<br><br>Az integrált Windows-hitelesítés [közvetlen egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternatív bejelentkezési Azonosítóval](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq)|UserPrincipalName + jelszó<br><br>sAMAccountName és jelszó<br><br>Integrált Windows-hitelesítés<br><br>[Tanúsítvány és az intelligens kártyás hitelesítés](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatív bejelentkezési Azonosítóval](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|A Windows Hello for Business támogatott?|[Kulcs megbízhatósági modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Az Intune-nal a tanúsítvány megbízhatósági modell](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Kulcs megbízhatósági modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Az Intune-nal a tanúsítvány megbízhatósági modell](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Kulcs megbízhatósági modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Tanúsítvány megbízhatósági modell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Mik azok a többtényezős hitelesítési beállítások?|[Az Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[A feltételes hozzáférés * az egyéni vezérlők](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-1)|[Az Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[A feltételes hozzáférés * az egyéni vezérlők](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-1)|[Az Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Az Azure MFA-kiszolgáló](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[Külső MFA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[A feltételes hozzáférés * az egyéni vezérlők](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-1)|
|Milyen felhasználói fiók állapotok támogatottak?|Letiltott fiókokat<br>(legfeljebb 30 perces késleltetés)|Letiltott fiókokat<br><br>A fiók zárolva<br><br>A fiók lejárt<br><br>A jelszó lejárt<br><br>Jelentkezzen be óra|Letiltott fiókokat<br><br>A fiók zárolva<br><br>A fiók lejárt<br><br>A jelszó lejárt<br><br>Jelentkezzen be óra|
|Mik a feltételes hozzáférési beállításokat?|[Az Azure AD feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Az Azure AD feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Az Azure AD feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Az AD FS-jogcímszabályok](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Támogatott régebbi protokollokra blokkolja?|[Igen](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Igen](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Igen](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Testreszabhatja a embléma, a lemezkép és a leírás a bejelentkezési oldalakon?|[Igen, az Azure AD Premium szolgáltatással](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Igen, az Azure AD Premium szolgáltatással](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Igen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Milyen speciális forgatókönyvek támogatottak?|[Az intelligens jelszózárolás](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[Kiszivárgott hitelesítő adatok jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Az intelligens jelszózárolás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Többhelyes közel valós idejű hitelesítő rendszerrel<br><br>[Az AD FS extranetes fiókzárolás](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integráció a külső identitáskezelési rendszerek](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

> [!NOTE] 
> Egyéni vezérlők az Azure AD feltételes hozzáférés jelenleg nem támogatja az eszköz regisztrálása.

## <a name="recommendations"></a>Javaslatok
Az identitás rendszer gondoskodik arról, hogy a felhasználók hozzáférését a felhőalapú alkalmazások és az üzleti alkalmazásokat, hogy át, és elérhetővé teheti a felhőben. Így a jogosult felhasználók hatékonyan és a kártékony elemek kívül a szervezet bizalmas adatokat, a hitelesítési szabályozza az alkalmazásokhoz való hozzáférés.

Használhatja, vagy bármelyik hitelesítési módszert választja, a következő okok miatt a Jelszókivonat-szinkronizálás engedélyezése:

1. **Magas rendelkezésre állás és vészhelyreállítás helyreállítási**. Az átmenő hitelesítés és az összevonás a helyszíni infrastruktúrára támaszkodik. Átmenő hitelesítéshez a helyszíni üzembe helyezésének előkészítése magában foglalja a Kiszolgálóhardver, és a hálózatkezelés az átmenő hitelesítés ügynökök megkövetelése. Az összevonáshoz a helyszíni erőforrásigénye még nagyobb. A proxy hitelesítési kéréseket a szegélyhálózaton és a belső összevonási kiszolgálók van szükség. 

    Hibaérzékeny pontokat hibák elkerülése érdekében helyezzen üzembe redundáns kiszolgálókon. Hitelesítési kérelmek majd mindig szolgálja ki, ha bármelyik összetevő meghibásodik. Átmenő hitelesítés és az összevonási is támaszkodik tartományvezérlők is meghiúsulhat hitelesítési kérelmek megválaszolásához. Ezek az összetevők számos kell karbantartási kifogástalan állapotban marad. Kimaradások esetén nagy valószínűséggel karbantartási nem tervezett és nem megfelelő. Kimaradások elkerülése a Jelszókivonat-szinkronizálás használatával, mert a Microsoft Azure AD hitelesítési felhőszolgáltatás globálisan méretezhető, és mindig elérhető.

2. **A helyszíni szolgáltatáskimaradás túlélési**.  A kibertámadás vagy katasztrófa miatt a helyszíni kimaradás következményeinek reputational márka kárt kezdve nem lehet kezelni a támadás mozgáskorlátozottak szervezet jelentős lehet. Nemrég számos szervezet áldozatul a kártevőtámadások, beleértve a megcélzott zsarolóprogramok elleni, a helyszíni kiszolgálók lejjebb okozó. A Microsoft segítségével az ügyfelek az ilyen típusú támadások foglalkozik, amikor állapottáblázatban két kategóriájuk szervezetek:

   * Szervezetek számára, amelyek korábban bekapcsolta a Jelszókivonat-szinkronizálás módosítani a Jelszókivonat-szinkronizálás használandó hitelesítési módszert. Csupán néhány óra ismét online voltak. Hozzáférés az e-mailek az Office 365-n keresztül, eredménnyel kapcsolatos hibák elhárítása és egyéb felhőalapú számítási feladatok eléréséhez.

   * Nem megbízható külső fogyasztói e-mail rendszerek kommunikációs és feloldó problémák közelítse kellett szervezeteknek, amelyek korábban nem engedélyezte a Jelszókivonat-szinkronizálás. Ezekben az esetekben végrehajtásának őket hét vagy hosszabb lehet újra működik.

3. **Identity protection**. Az Azure AD Identity Protection védelme érdekében a felhasználók a felhőben a legjobb módszerek valamelyikével. A Microsoft folyamatosan vizsgálatokat végez a felhasználó számára az interneten, és a jelszó sorolja fel, hogy kártékony elemek értékesítésére, és elérhetővé teszi a webről. Azure ad-ben ezen információk használatával győződjön meg arról, ha bármelyik a felhasználónevek és jelszavak a szervezet kerülnek veszélybe. Ezért fontos, függetlenül attól, milyen hitelesítési módszer használata esetén, amelyek összevont-e, vagy az átmenő hitelesítést a Jelszókivonat-szinkronizálás engedélyezése. Egy jelentés kiszivárgott hitelesítő adatok jelennek meg. Ezen információk használatával letiltása, vagy kényszerítse a felhasználókat a jelszavuk módosításához, ha megpróbálnak bejelentkezni a kiszivárgott jelszavát.

Végül, a következők szerint a [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), a Microsoft rendelkezik a legtöbb teljes körű identitás- és hozzáférés-kezelési funkciók. A Microsoft kezeli [450 milliárd hitelesítési kérelmek](https://www.microsoft.com/en-us/security/intelligence-report) biztosíthat hozzáférést az Office 365-höz hasonló SaaS-alkalmazások ezreinek szinte bármilyen eszközről minden hónapban. 

## <a name="conclusion"></a>Összegzés

Ez a cikk ismerteti a különböző hitelesítési beállítások, amelyek a szervezetek konfigurálásával és telepítésével a felhőalkalmazások hozzáférés támogatásához. Szervezetek különböző üzleti, biztonsági és technikai követelmények teljesítésére, választhat a Jelszókivonat-szinkronizálás, átmenő hitelesítést és összevonást. 

Fontolja meg az egyes hitelesítési módszert. Nem a szalagcsere a megoldást, és a felhasználói élmény a bejelentkezési folyamat üzembe helyezése, oldja meg az üzleti követelményeinek? Mérlegelje, hogy szervezetének szüksége van, a speciális forgatókönyvek és üzletmenet-folytonossági funkciókat az egyes hitelesítési módszert. Végül értékeli az egyes hitelesítési módszerek szempontokat kell figyelembe vennie. Hajtsa végre ezek közül bármelyik megakadályozzák a végrehajtási választott?

## <a name="next-steps"></a>További lépések

A mai világ a fenyegetések jelen a nap 24 órájában, és mindenhol származnak. A megfelelő hitelesítési módszert, és a biztonsági kockázatok mérséklésében, és megvédheti identitásait.

[Első lépések](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) az Azure ad-vel és a szervezet számára a megfelelő hitelesítési megoldás üzembe helyezéséhez.

Ha szeretne áttérni a rendszerről történő áttelepítésről összevont hitelesítési cloud, tudjon meg többet [a bejelentkezési mód módosítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Akkor megtervezheti és megvalósíthatja az áttelepítés érdekében használjon [ezeket a központi telepítési csomagok projekt](https://aka.ms/deploymentplans).
