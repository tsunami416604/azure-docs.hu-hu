---
title: Válassza ki a megfelelő hitelesítési módszert az Azure AD hibrid identitáskezelési megoldás |} Microsoft Docs
description: Az útmutató segítségével CEOs, igazgatók, CISOs, fő identitás fejlesztők, vállalati fejlesztők és biztonsági és informatikai döntéshozók felelős az Azure AD hibrid identitáskezelési megoldás hitelesítési módszer kiválasztása a közepes és nagy méretű szervezeteknek.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 062b5e48cfba5de64aa11f79629e82645df87f96
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809260"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldás 

Ez a cikk több olyan cikket, amely segítségével a szervezetek teljes Azure Active Directory (Azure AD) hibrid identitáskezelési megoldás megvalósítása kezdődik. Ez a megoldás lett leírt a [hibrid identitás digitális átalakítása keretrendszer](https://aka.ms/aadframework). Az üzleti tevékenységét kiterjed, és a hatékony és biztonságos hibrid identitáskezelési megoldás megvalósításának célok szervezetek összpontosíthat. 

A keretrendszer első üzleti eredményeit határozza meg a szervezetek számára, hogy a hitelesítési folyamat biztonságos felhőalapú alkalmazások felhasználók általi elérésekor követelményei. Az első üzleti hitelesítési biztonságos üzleti eredményében célja felhőalapú alkalmazásokba a helyszíni felhasználónevek és jelszavak használatával bejelentkezni, hogy a felhasználók. A bejelentkezési folyamat és a felhasználók hogyan hitelesítik lehetővé teszik, minden a felhőben.

A megfelelő hitelesítési módszer kiválasztása az első feladata a szervezet számára, aki a helyezze át az alkalmazásaikat a felhőben. Nem lép a döntés enyhén, a következő okok miatt:

1. Helyezze át a felhőbe szeretné szervezet első döntési. 

2. A hitelesítési módszer a felhőben a szervezetek jelenléte kritikus összetevője. Meghatározza, hogy az összes felhőbeli adatát és erőforrásokhoz való hozzáférését.

3. Ez az alapja az összes többi speciális biztonsági és felhasználói élményt nyújtó szolgáltatásokat az Azure ad-ben.

4. A hitelesítési módszert is nehézkes megvalósítása után módosítsa.

Identitás az új vezérlő vezérlősík informatikai biztonsági. Így a hitelesítés az egy szervezet hozzáférés őr az új felhőalapú világába. Az identitás vezérlő vezérlősík, amely megerősíti a biztonsági és a felhőalapú alkalmazások biztonságban tartja a szervezeteknek kell.

### <a name="out-of-scope"></a>Hatókörén kívül
Egy meglévő helyszíni címtár erőforrásigényét nem rendelkező szervezeteknek nem ez a cikk a fókuszt. Ezen vállalatok általában csak a felhőben, amelyek nem igényelnek a hibrid identitáskezelési megoldás létrehozása az identitások. Csak felhőalapú Identitások kizárólag a felhőben vannak, és nem megfelelő a helyszíni identitások társítva.

## <a name="authentication-methods"></a>Hitelesítési módszerek
Ha az Azure AD hibrid identitáskezelési megoldás az új vezérlő vezérlősík, hitelesítési az alapja a felhő-hozzáférést. Az Azure AD hibrid identitáskezelési megoldás létrehozása a kritikus fontosságú első döntés a megfelelő hitelesítési módszer kiválasztása. A hitelesítési módszer, amelynek van konfigurálva, amely is látja el a felhőbeli felhasználók az Azure AD Connect használatával valósítja meg.

Válassza ki a hitelesítési módszert, fontolja meg az időt, meglévő infrastruktúra, összetettségét, és a választott végrehajtási költséget kell. Ezek a tényezők esetében minden egyes szervezet különböző, és idővel változhatnak. 

Az Azure AD hibrid identitáskezelési megoldások a következő hitelesítési módszereket támogatja.

### <a name="cloud-authentication"></a>Felhőalapú hitelesítés
Ez a hitelesítési módszer választása esetén az Azure AD kezeli a felhasználói bejelentkezési folyamat. Alapján kialakulhat zökkenőmentes egyszeri bejelentkezést (SSO), felhasználó bejelentkezve is anélkül, hogy a hitelesítő adatok újbóli felhőalapú alkalmazásokba. A felhőalapú hitelesítés a két lehetőség közül választhat: 

**Az Azure AD Jelszókivonat-szinkronizálást**. A legegyszerűbben az Azure AD helyszíni címtár objektumainak hitelesítés engedélyezése. Felhasználók használhatják ugyanazt a felhasználónevet és jelszót használják-e a helyszíni anélkül, hogy minden további infrastruktúra központi telepítéséhez. Néhány prémium szintű Azure ad Identity Protection, például funkciókhoz Jelszókivonat-szinkronizálást a függetlenül attól, milyen hitelesítési módszert válassza.

> [!NOTE] 
> Jelszavak soha nem titkosítatlan szövegként tárolt és az Azure AD egy visszafejthető algoritmus titkosított. Jelszókivonat-szinkronizálást a tényleges folyamatáról további információk: [Jelszókivonat-szinkronizálást és az Azure AD Connect-szinkronizálás megvalósítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Az Azure AD-áteresztő hitelesítés**. Lehetővé teszi az Azure AD hitelesítési szolgáltatások egy egyszerű jelszó érvényesítése a szoftver ügynököt futtat egy vagy több helyszíni kiszolgálók használatával. A kiszolgálók ellenőrzése a felhasználók közvetlenül a helyszíni Active Directoryban, amely biztosítja, hogy a nem a felhőben kerül sor a jelszó érvényesítése. 

Vállalatok egy biztonsági követelmény, hogy azonnal érvénybe lépteti a helyi felhasználói fiók állapotok, jelszóházirendeket, és a bejelentkezési óra használhatja ezt a hitelesítési módszert. A tényleges átmenő hitelesítést további információkért lásd: [felhasználói jelentkezzen be az Azure AD átmenő hitelesítés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Összevont hitelesítés
Ez a hitelesítési módszer választása esetén az Azure AD beavatkozás nélküli ki egy külön megbízható hitelesítési rendszerén, például a helyszíni Active Directory összevonási szolgáltatások (AD FS), a hitelesítési folyamat a jelszó érvényesítése.

A hitelesítési rendszer képes további speciális hitelesítési követelmények. Többek között az intelligenskártya-alapú hitelesítést vagy harmadik felek többtényezős hitelesítést. További információkért lásd: [Active Directory összevonási szolgáltatások telepítése](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Az alábbi szakasz segítségével eldöntheti, milyen hitelesítési módszert az Ön számára legmegfelelőbb segítségével el a döntési fában. A program segít szükség van-e a felhő vagy összevont hitelesítés az Azure AD hibrid identitáskezelési megoldás telepítéséhez.

## <a name="decision-tree"></a>Döntési fa

![Az Azure AD hitelesítési döntési fája](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>Részletes kapcsolatos szempontok

### <a name="cloud-authentication-password-hash-synchronization"></a>Felhőalapú hitelesítés: Jelszókivonat-szinkronizálást

* **Elérhető**. Jelszókivonat-szinkronizálást igényel a telepítés, karbantartási és infrastruktúra módosításokat.  Ez a szint erőfeszítéssel általában a szervezetek csak a felhasználók jelentkezhetnek be az Office 365, SaaS-alkalmazásokhoz és más Azure AD-alapú erőforrások vonatkozik. Amikor engedélyezve van, a Jelszókivonat-szinkronizálást az Azure AD Connect szinkronizálási folyamat része, és két percenként.

* **Felhasználói élmény**. A felhasználói bejelentkezési élmény javítása érdekében telepítsen zökkenőmentes SSO Jelszókivonat-szinkronizálást. Zökkenőmentes SSO megszünteti a szükségtelen megjelenő utasításokat, ha felhasználó jelentkezik be.

* **Speciális forgatókönyvek**. Szervezetek mellett dönt, akkor információkat kaphat a identitások az Azure AD Identity Protection jelentések használatát. Példa: a kiszivárgott hitelesítő adatok jelentés. Vállalati Windows Hello egy másik lehetőség, amely rendelkezik [konkrét követelmények Jelszókivonat-szinkronizálást használatakor](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    A szervezeteknek, amelyek a Jelszókivonat-szinkronizálást a többtényezős hitelesítés megkövetelése az Azure AD többtényezős hitelesítést kell használnia. Azon szervezetek nem használható külső vagy a helyszíni többtényezős hitelesítési módszert.

* **Az üzletmenet folytonossága**. Jelszókivonat-szinkronizálást a felhőalapú hitelesítés magas rendelkezésre állást biztosít, és az összes Microsoft adatközpontjaiban felhőszolgáltatásként érték használata. Győződjön meg arról, hogy a Jelszókivonat-szinkronizálást nem hosszabb ideig leáll, a második az Azure AD Connect kiszolgáló átmeneti módban a készenléti konfigurációban telepíti.

* **Szempontok**. Jelenleg Jelszókivonat-szinkronizálást nem azonnal érvénybe lépteti a helyi fiók állapotok változásairól. Ilyen esetben a felhasználó hozzáférhet felhőalapú alkalmazásokba, csak a felhasználói fiók állapota szinkronizálva az Azure ad Szolgáltatásba. A szervezetek szeretné egy új szinkronizálási ciklust követően a rendszergazdák tömeges frissítéseket a helyi felhasználói fiók állapotok futtatásával Ez a korlátozás áthidalható. Példa letiltja a fiókot.

> [!NOTE]
> A jelszó lejárt, és a fiók zárolt állapotok jelenleg az Azure AD az Azure AD Connect nem szinkronizálja. 

Tekintse meg [Jelszókivonat-szinkronizálást végrehajtási](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) a központi telepítés lépéseit.

### <a name="cloud-authentication-pass-through-authentication"></a>Felhőalapú hitelesítés: áteresztő hitelesítés  

* **Elérhető**. Átmenő hitelesítés van szüksége egy vagy több (ajánlott három) egyszerűsített meglévő kiszolgálókon telepített ügynököket. Ügynökök kell férniük a helyszíni Active Directory tartományi szolgáltatások, beleértve a helyszíni AD-tartományvezérlő. Kimenő Internet-hozzáférés és a tartományvezérlőhöz történő hozzáférés szükséges. Emiatt nem támogatott a szegélyhálózaton levő ügynökök telepítésére. 

    Áteresztő hitelesítés tartományvezérlők korlátozás nélküli hálózati kapcsolatra van szüksége. Az összes hálózati forgalom titkosítva, és a hitelesítési kérelmek korlátozódik. Ez a folyamat további információkért tekintse meg a [biztonsági mélyreható](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) az átmenő hitelesítés.

* **Felhasználói élmény**. A felhasználói bejelentkezési élmény javítása érdekében áteresztő hitelesítés zökkenőmentes SSO telepítéséhez. Zökkenőmentes SSO megszünteti a szükségtelen felszólítás után a felhasználók bejelentkeznek.

* **Speciális forgatókönyvek**. Áteresztő hitelesítés érvényesíti a helyi házirend bejelentkezés alkalmával. Hozzáférés megtagadva például amikor az állapot le van tiltva, egy helyi felhasználói fiók zárolva, vagy [lejárt](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) vagy amikor a felhasználó bejelentkezhet engedélyezett-e a munkaidőn kívül esik. 

    Azok a szervezetek, többtényezős hitelesítés az átmenő hitelesítést igénylő Azure multi-factor Authentication (MFA) kell használnia. Azon szervezetek egy külső vagy a helyszíni többtényezős hitelesítési módszer nem használható. Speciális funkciók szükségesek, hogy a Jelszókivonat-szinkronizálást történik-e, attól függetlenül, hogy átmenő hitelesítést választja. Példa: Identity Protection kiszivárgott hitelesítő adatok jelentése.

* **Az üzletmenet folytonossága**. Azt javasoljuk, hogy két extra átmenő hitelesítés ügynök központi telepítése. Ezek a kiegészítő funkciók mellett az Azure AD Connect-kiszolgáló az első ügynöki vannak. A további telepítési hitelesítési kérelmek magas rendelkezésre állását biztosítja. Ha rendelkezik telepített ügynökök három, egy ügynök továbbra is sikertelen lehet, ha egy másik ügynök karbantartási nem működik. 

    Nincs központi telepítése a Jelszókivonat-szinkronizálást átmenő hitelesítés mellett egy másik előnye. Úgy működik, mint a biztonsági mentési hitelesítési módszert, ha már nem érhető el az elsődleges hitelesítési módszert.

* **Szempontok**. Az átmenő hitelesítés tartalék hitelesítési módszerként használhatja a Jelszókivonat-szinkronizálást, és az ügynökök nem lehet érvényesíteni a felhasználó hitelesítő adatait. Ezután a feladatátvételt a Jelszókivonat-szinkronizálást nem kerül sor automatikusan. A bejelentkezési módszer váltás manuálisan az Azure AD Connect használatával. 

    Áteresztő hitelesítés csak a felhőalapú alkalmazások, amelyek a modern hitelesítést és az Exchange Online protokollok támogatja. Néhány protokollok a következők: az ActiveSync, POP3 és IMAP4. Például a Microsoft Office 2013 és a későbbi támogatnia kell a modern hitelesítést, de a korábbi verziók nem. Office alkalmazások által nyújtott további információkért lásd: [frissített Office 365 modern hitelesítést](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/). Egyéb szempontok a áteresztő hitelesítés, beleértve a másik azonosító támogatja, a következő témakörben: [gyakran ismételt kérdések](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Tekintse meg [átmenő hitelesítés végrehajtási](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) a központi telepítés lépéseit.

### <a name="federated-authentication"></a>Összevont hitelesítés

* **Elérhető**. Egy összevont hitelesítés rendszer egy külső megbízható rendszer felhasználók hitelesítésére támaszkodik. Egyes vállalatok szeretné, hogy az Azure AD hibrid identitáskezelési megoldás a meglévő összevont rendszer befektetés újból. A karbantartás és az összevont rendszer kezelése az Azure AD felügyelete kívül esik. Ez legfeljebb lesz a szervezet az összevont rendszer használatával ellenőrizze, hogy a rendszer biztonságosan, és a hitelesítési terhelés kezelésére is. 

* **Felhasználói élmény**. A felhasználói élmény összevont hitelesítési szolgáltatások, topológia, és az összevonási kiszolgálófarm konfigurációja függ. Egyes szervezetek kell erre a rugalmasságra igazítja, és konfigurálja az összevonási kiszolgálófarm a biztonsági követelményeinek megfelelően elérését. Például úgy is konfigurálhatja a felhasználók automatikus bejelentkezés, a névtelen hitelesítő adatok nélkül belső csatlakozó felhasználók és eszközök. Ez a konfiguráció működik, mivel azok már bejelentkezett az eszközeiket. Ha szükséges, néhány speciális funkciókat megnehezítik a felhasználói bejelentkezési folyamat.

* **Speciális forgatókönyvek**. Egy összevont hitelesítési megoldás egy hitelesítési követelményt, amely natív módon nem támogatja az Azure AD a felhasználóknak kell esetén általában szükséges. Részletes információk segítséget nyújtanak [bejelentkezés jobb lehetőséget](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Vegye figyelembe a következő követelményeknek:

    * Intelligens kártyák vagy tanúsítványokat igénylő hitelesítési.
    * A helyi multi-factor Authentication kiszolgálók vagy harmadik felek többtényezős szolgáltatók.
    * Hitelesítés harmadik fél hitelesítési megoldás használata. Tekintse meg a [az Azure AD összevonás kompatibilitási listája](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Jelentkezzen be, amely szükséges egy sAMAccountName, például TARTOMÁNY\felhasználónév, helyett egy egyszerű felhasználónév (UPN), például user@domain.com.

* **Az üzletmenet folytonossága**. Összevont rendszerek általában szükség van egy elosztott terhelésű kiszolgálók tömbje, a farm néven ismert. A farm konfigurálva van egy belső hálózati és a peremhálózati topológia hitelesítési kérelmek magas rendelkezésre állásának biztosításához.

    Ha elsődleges hitelesítési módszert már nem érhető el, telepítse a Jelszókivonat-szinkronizálást összevont hitelesítés mellett egy tartalék hitelesítési módszerként. Például akkor, ha a helyszíni kiszolgálók nem érhetők el. Néhány nagyvállalat több Internet belépési pontok konfigurálva a földrajzi-DNS-ben a kis késleltetésű hitelesítési kérelmek támogatásához összevonási megoldást követel meg.

* **Szempontok**. Összevont rendszerek általában szükség van egy jelentősebb helyszíni infrastrukturális beruházások. A legtöbb szervezet válassza ezt a beállítást, ha azok már rendelkezik egy helyszíni összevonási. És, ha egyetlen identitásszolgáltató használandó erős üzleti igény. Összevonási összetettebb működik, illetve háríthatóak el annak felhőalapú hitelesítési megoldás képest.

Egy útválasztást olyan tartományhoz, amely nem lehet ellenőrizni, az Azure ad-ben meg felhasználói Azonosítóját a bejelentkezés végrehajtásához további konfigurálást igényelnek. Ez a követelmény nevezik másodlagos bejelentkezési azonosító támogatása. Lásd: [másodlagos bejelentkezési azonosító beállítása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) korlátozásokat és követelményeket. Ha egy külső többtényezős hitelesítési szolgáltató az összevonási használatát választja, győződjön meg arról, a szolgáltató támogatja a WS-Trust, az Azure AD join eszközök.

Tekintse meg [összevonási kiszolgálók üzembe helyezése](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) a központi telepítés lépéseit.

> [!NOTE] 
> Az Azure AD hibrid identitáskezelési megoldás üzembe helyezésekor meg kell valósítani az Azure AD Connect támogatott topológiák egyikét. Ismerje meg, több támogatott és nem támogatott a következő konfigurációk [az Azure AD Connect topológiák](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Architektúra

Az alábbi ábrák szerkezeti szükséges minden egyes hitelesítési módszer csak akkor használhatja az Azure AD hibrid identitáskezelési megoldás magas szintű architektúra összetevők. Hasonlítsa össze a megoldások segítségével áttekintést biztosítanak.

* A jelszó kivonatát adatszinkronizálási megoldás jellemzője az egyszerűség:

    ![Jelszókivonat-szinkronizálást az Azure AD hibrid identitás](media/azure-ad/azure-ad-authn-image2.png)

* Átmenő hitelesítés ügynök követelményeinek:

    ![Az áteresztő hitelesítés az Azure AD hibrid identitás](media/azure-ad/azure-ad-authn-image3.png)

* A szegélyhálózat és a szervezet belső hálózati összevonási szükséges összetevők:

    ![Az összevont hitelesítés az Azure AD hibrid identitás](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Módszerek összehasonlítása

|Szempont|Jelszókivonat-szinkronizálást + zökkenőmentes SSO|Áteresztő hitelesítés + zökkenőmentes SSO|Összevonás az AD FS rendszerrel|
|:-----|:-----|:-----|:-----|
|Amikor megtörténik a hitelesítési?|A felhőben|A biztonságos jelszó ellenőrzési adatcsere a helyszíni hitelesítési ügynök után a felhőben|Helyszíni követelmények|
|Mik a helyi kiszolgáló követelményei a kiépítési rendszer túl: az Azure AD Connect?|None|Egy kiszolgáló minden további hitelesítési ügynök|Két vagy több AD FS-kiszolgáló<br><br>A szegélyhálózati/DMZ hálózatban két vagy több WAP-kiszolgálókkal|
|Mik azok a helyszíni Internet követelményei és hálózati túl a kiépítési rendszer?|None|[Kimenő Internet-hozzáférés](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) a kiszolgálók futtató hitelesítési ügynökök|[A bejövő Internet-hozzáférés](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) a WAP-kiszolgálókkal a szegélyhálózatban található<br><br>A bejövő hálózati hozzáférést AD FS-kiszolgáló a WAP-kiszolgálókkal a szegélyhálózatban található<br><br>A hálózati terheléselosztás|
|Nem szükséges egy SSL-tanúsítvány?|Nem|Nem|Igen|
|Van egy állapotfigyelési megoldás?|Nem szükséges|Ügynök állapota által biztosított [Azure Active Directory felügyeleti központban](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Hajtsa végre a felhasználónál egyszeri bejelentkezést a felhőalapú erőforrásokhoz a tartományhoz csatlakoztatott eszközök a vállalati hálózaton belül?|Igen, a [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Igen, a [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Igen|
|Milyen bejelentkezési típusok támogatottak?|UserPrincipalName + jelszó<br><br>Segítségével integrált Windows-hitelesítés [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Másodlagos bejelentkezési Azonosítóval](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + jelszó<br><br>Segítségével integrált Windows-hitelesítés [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Másodlagos bejelentkezési Azonosítóval](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + jelszó<br><br>sAMAccountName + jelszó<br><br>Integrált Windows-hitelesítés<br><br>[A tanúsítvány és az intelligens kártyás hitelesítés](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Másodlagos bejelentkezési Azonosítóval](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Az a Windows Hello támogatott vállalati?|[Kulcs megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Az Intune tanúsítvány-megbízhatósági modell](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Kulcs megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Az Intune tanúsítvány-megbízhatósági modell](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Kulcs megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Tanúsítvány-megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Mik a többtényezős hitelesítési beállítások?|[Azure MFA használatával](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA használatával](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA használatával](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br><br>[Azure MFA-kiszolgáló](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[Harmadik felek többtényezős hitelesítés](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Milyen felhasználói fiók állapotok támogatottak?|Letiltott fiókok<br>(legfeljebb 30 perces késleltetés)|Letiltott fiókok<br><br>A fiók zárolása<br><br>A jelszó lejárt<br><br>Bejelentkezési üzemideje (óra)|Letiltott fiókok<br><br>A fiók zárolása<br><br>A jelszó lejárt<br><br>Bejelentkezési üzemideje (óra)|
|Mik a feltételes hozzáférési beállítások?|[Azure AD feltételes hozzáférés](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD feltételes hozzáférés](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD feltételes hozzáférés](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[AD FS jogcímszabályok](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Támogatott örökölt protokollok blokkolja?|[Igen](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Igen](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Igen](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Testreszabhatja a embléma, a lemezkép és a leírás a bejelentkezési oldalon?|[Igen, az Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Igen, az Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Igen](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Milyen speciális forgatókönyvek támogatottak?|[Jelszó az intelligens zárolás](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br><br>[Hitelesítő adatok jelentések szivárognak](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Jelszó az intelligens zárolás](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Többhelyes kis késleltetésű hitelesítési rendszer<br><br>[AD FS extranet kizárás](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[Integráció a külső identitáskezelő rendszerekkel](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>Javaslatok
Az azonosítási rendszer biztosítja a hozzáférést a felhőalapú alkalmazások és a sor üzleti alkalmazások áttelepítése, és hogy elérhető legyen a felhőben. Hitelesített felhasználók hatékony és ezeken kívül a vállalat bizalmas adatok megőrzéséhez hitelesítési szabályozza az alkalmazásokhoz való hozzáférés.

Használja, vagy engedélyezze a Jelszókivonat-szinkronizálást a bármelyik hitelesítési módszert választja, a következő okok miatt:

1. **Magas rendelkezésre állási és vészhelyreállítási helyreállítási**. Áteresztő hitelesítés és az összevonási támaszkodjon a helyszíni infrastruktúrát. Az átmenő hitelesítés a helyszíni kezdjen a Kiszolgálóhardver tartalmazza, és az áteresztő hitelesítés hálózati ügynökök igényel. Az összevonáshoz a helyszíni kezdjen ennél nagyobb. A proxy hitelesítési kérelemre peremhálózat és a belső összevonási kiszolgálók igényel. 

    Egyetlen ponton felmerülő hibák elkerülése érdekében redundáns kiszolgálót telepít. Hitelesítési kérelmek majd mindig szolgálja ki, ha valamelyik összetevő meghibásodik. Átmenő hitelesítés, mind az összevonási is támaszkodjon tartományvezérlők válaszolni a hitelesítési kérelmeket, amelyek is sikertelen lehet. Ezek az összetevők számos kell karbantartási kifogástalan állapotú marad. Valamilyen okból kimaradás esetén nagyobb a valószínűsége karbantartási nem tervezett és nem megfelelő. Kimaradások elkerülése Jelszókivonat-szinkronizálást használatával, mert a Microsoft Azure AD felhőalapú hitelesítési szolgáltatás globálisan méretezi, és mindig elérhető.

2. **A helyszíni kimaradás túlélési**.  Lehet, hogy az egy számítógépes-támadás vagy katasztrófa miatt a helyszíni kimaradás következményeiről jelentős, nem foglalkozik a támadás mozgáskorlátozottak szervezet reputational márka kárt kezdve. Számos szervezet nemrég, a kártékony programok támadásait, beleértve a célzott nevű, a helyszíni kiszolgálók leáll okozó áldozatai volt. A Microsoft segítségével az ügyfelek az ilyen támadások kezelésére, amikor két kategóriába szervezetek számára megjelenített:

   * A szervezeteknek, amelyek korábban engedélyezve van a Jelszókivonat-szinkronizálást a Jelszókivonat-szinkronizálást használandó hitelesítési módszer megváltozott. Ismét online óra be voltak. Keresztül Office 365 e-mail hozzáférés segítségével eredménnyel problémák megoldásához, és más felhőalapú munkaterhelések eléréséhez.

   * A szervezeteknek, amelyek korábban nem engedélyezi a Jelszókivonat-szinkronizálást kellett a feloldó problémák és a nem megbízható külső fogyasztói e-mail rendszerek igénybe. Ezekben az esetekben végrehajtásának őket hét vagy több is működik, és újra kell.

3. **Azonosító adatok védelmét**. A legjobb módszer a felhőbeli felhasználók védelmére egyike az Azure AD Identity Protection. Microsoft folyamatosan ellenőrzi az interneten a felhasználói és jelszót, hogy ezeken eladásra, és a sötét webes elérhetővé tenni. Az Azure AD ezen információk használatával győződjön meg arról, ha a felhasználóneveket és jelszavakat a szervezetben kerülnek veszélybe. Ezért kiemelten fontos Jelszókivonat-szinkronizálást függetlenül attól, milyen hitelesítési módszert használja, hogy, hogy össze van vonva vagy átmenő hitelesítés engedélyezéséhez. Kiszivárgott hitelesítő adatok jelennek meg a jelentés. Ezen információk használatával letiltása, és kényszeríteni a felhasználót, hogy módosítsák a jelszavukat, amikor próbálnak bejelentkezni a kiszivárgott jelszavát.

Végül megfelelően a [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft be van állítva a legtöbb teljes körű identitások és hozzáférések felügyeleti funkciókat. A Microsoft kezeli [450 milliárd hitelesítési kérelmek](https://www.microsoft.com/en-us/security/intelligence-report) minden hónapban, hogy hozzáférést biztosítson az Office 365-höz hasonló SaaS-alkalmazásokhoz több ezer szinte bármilyen eszközről. 

## <a name="conclusion"></a>Összegzés

Ez a cikk ismerteti a különböző hitelesítési beállításait, hogy a szervezetek konfigurálásával és telepítésével felhőalapú alkalmazásokba való hozzáférés támogatásához. Szervezet különböző üzleti, biztonsági és műszaki követelmények teljesítéséhez választhat Jelszókivonat-szinkronizálást, az áteresztő hitelesítés és az összevonási. 

Vegye figyelembe az egyes hitelesítési módszert. A annak érdekében, hogy a megoldás, és a felhasználói élmény a bejelentkezési folyamat, a rendszer üzleti követelményeit használ? Mérlegelje, hogy szervezetének szüksége van a speciális forgatókönyveket és üzleti folytonosságot biztosító szolgáltatásokat minden egyes hitelesítési módszer. Végezetül értékelése az egyes hitelesítési módszerek ismertetett szempontok alapján. Tegye valamelyiket megakadályozhatják a kiválasztott végrehajtási?

## <a name="next-steps"></a>További lépések

Napjaink világ fenyegetések jelen a nap 24 órájában, és everywhere származik. A megfelelő hitelesítési módszert, és a biztonsági kockázatok csökkentése, és az identitások védelme.

[Első lépések](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) az Azure AD és a szervezete számára megfelelő hitelesítési megoldás üzembe helyezéséhez.

Ha most gondolat áttelepítés összevont hitelesítési felhőalapú, további információ [módosítása a bejelentkezési módszer](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Ön tervezését és megvalósítását az áttelepítés érdekében használjon [ezeket a központi telepítési csomagok projekt](http://aka.ms/deploymentplans).
