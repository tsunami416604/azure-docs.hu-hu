---
title: Válassza ki a megfelelő hitelesítési módszert az Azure AD hibrid identitáskezelési megoldás |} Microsoft Docs
description: Ez az útmutató célja, hogy segíti a CEOs, igazgatók, CISOs, fő identitás fejlesztők, vállalati fejlesztők és biztonsági és informatikai döntéshozók felelős a közepes vagy nagyméretű az Azure AD hibrid identitáskezelési megoldás hitelesítési módszer kiválasztása a szervezetek.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 373efcd821946ab5814ab58284ef7778b779260a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Az Azure Active Directory hibrid identitáskezelési megoldás a megfelelő hitelesítési módszer kiválasztása 

Ez a cikk az első, a cikkek valósítja meg a teljes szervezetek számára az Azure AD hibrid identitáskezelési megoldás. Teljes Azure AD hibrid identitáskezelési megoldás lett ezt a hibrid identitáskezelési digitális átalakítása keretrendszer és magában foglalja az üzleti tevékenységét, és célok szervezetek irányul, hogy győződjön meg arról, hogy azok már megvalósított egy hatékony és biztonságos hibrid identitás megoldás. A keretrendszer első üzleti eredményeit határozza meg a szervezetek számára, hogy a hitelesítési folyamat biztonságos felhőalapú alkalmazások felhasználók általi elérésekor követelményei. Az első üzleti biztonságos üzleti hitelesítés eredménye a célja felhasználók felhasználásával a helyszíni felhasználónevek és jelszavak felhőbe bejelentkezni. A bejelentkezési folyamat és a felhasználók hogyan hitelesítik lehetővé teszi, minden a felhőben.

A megfelelő hitelesítési módszer kiválasztása az első feladata a szervezet számára, aki a helyezze át az alkalmazásaikat a felhőben. Ez nem ajánlott döntést enyhén a következők lehetnek az okai:

1. Olyan vállalatnál, helyezze át a felhőbe történő első döntés. 

2. A hitelesítési módszer egy szervezet jelenlétét a felhőben található összes felhő adatokhoz és erőforrásokhoz való hozzáférés szabályozása kritikus összetevője.

3. Ez az alapja az összes többi speciális biztonsági és felhasználói élményt nyújtó szolgáltatásokat az Azure ad-ben.

4. Nehezen módosítása a hitelesítési módszert, egyszer megvalósítva.

Az azonosító adatai különböznek a új vezérlő vezérlősík informatikai biztonsági a hitelesítés az egy szervezet hozzáférés őr az új felhőalapú világába. A szervezetek kell győződjön meg arról, hogy az identitás-ellenőrzés vezérlősík lehetővé teszi az erősebb biztonsági és biztonságban tartsa a felhőalapú alkalmazásokat.

### <a name="out-of-scope"></a>Hatókörén kívül

Egy meglévő helyszíni címtár erőforrásigényét nem rendelkező szervezeteknek nem ez a cikk a fókuszt. Ezen vállalatok általában csak a felhőben, amelyek nem igényelnek a hibrid identitáskezelési megoldás létrehozása az identitások. Csak felhőalapú Identitások kizárólag a felhőben vannak, és nem megfelelő a helyszíni identitások társított.  

## <a name="choosing-the-right-authentication-method"></a>A megfelelő hitelesítési módszer kiválasztása

Az Azure AD hibrid identitáskezelési megoldás, az új vezérlő vezérlősík, a hitelesítés az alapja a felhő-hozzáférést. Az Azure AD hibrid identitáskezelési megoldás létrehozása a kritikus fontosságú első döntés a megfelelő hitelesítési módszer kiválasztása. A hitelesítési módszer végrehajtási segítségével konfigurálható: az Azure AD Connect, amely is konfigurálta a felhasználókat a felhőben. 

Válassza ki a hitelesítési módszert, fontolja meg az időt, meglévő infrastruktúra, összetettségét, és a választott végrehajtási költséget kell. Ezek a tényezők esetében minden egyes szervezet különböző, és idővel változhatnak. 

Az Azure AD hibrid identitáskezelési megoldások a következő hitelesítési módszereket támogatja:

### <a name="cloud-authentication"></a>Felhőalapú hitelesítés
Ez a hitelesítési módszer választása esetén az Azure AD kezeli a bejelentkezési folyamat a felhasználók számára. Alapján kialakulhat zökkenőmentes egyszeri bejelentkezést (SSO), felhasználó bejelentkezve is anélkül, hogy a hitelesítő adatok újbóli felhőalapú alkalmazásokba. A felhőalapú hitelesítés két lehetőség közül választhat: 

**PHS (jelszó kivonatát Sync-ben)** – a legegyszerűbben az Azure AD helyszíni címtár objektumainak hitelesítés engedélyezése. Jelszókivonat-szinkronizálás lehetővé teszi, hogy a felhasználók ugyanazt a felhasználónevet és jelszót használják-e a helyszíni általi anélkül, hogy minden további infrastruktúra központi telepítéséhez. Az Azure AD Identity Protection, például egyes prémium szolgáltatások elérhetők, bármelyik hitelesítési mód van kiválasztva Jelszókivonat-szinkronizálás szükséges.

> [!NOTE] 
> Jelszavak soha nem titkosítatlan szövegként tárolt és az Azure AD egy visszafejthető algoritmus titkosított. Jelszókivonat-szinkronizálás folyamatáról további információk: [Jelszókivonat-szinkronizálást és az Azure AD Connect-szinkronizálás megvalósítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Áteresztő hitelesítés (ESP)** – biztosít egy egyszerű jelszó érvényesítése egy egy vagy több helyszíni kiszolgálókon futó szoftver-ügynök használatával a felhasználók közvetlenül a helyszíni Active ellenőrzése az Azure AD hitelesítési szolgáltatások A jelszó érvényesítése biztosítása Directory nem kerül sor, a felhőben. Egy biztonsági követelmény, hogy azonnal érvénybe lépteti a helyi felhasználói fiók vállalatok szerint jelszóházirendek és a nyitvatartási idő szeretné használni ezt a hitelesítési módszert. A tényleges átmenő hitelesítést további információkért lásd: [felhasználó bejelentkezhet az Azure Active Directory átmenő hitelesítés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Összevont hitelesítés
Ha a hitelesítési módszer választása az Azure AD beavatkozás nélküli ki a hitelesítési folyamat egy külön megbízható hitelesítési rendszerre, például egy a helyszíni Active Directory összevonási szolgáltatások (AD FS) a jelszó érvényesítése. A hitelesítési rendszer által nyújtott további hitelesítési követelmények, például az intelligenskártya-alapú hitelesítést vagy egy külső multi-factor Authentication hitelesítést. További információkért lásd: [Active Directory összevonási szolgáltatások telepítése](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Az alábbi szakasz segítségével eldöntheti, milyen hitelesítési módszert megfelel az Ön a döntési fa használatával. Ez segít szükség van-e a felhő vagy összevont hitelesítés az Azure AD hibrid identitáskezelési megoldás telepítéséhez.

## <a name="azure-ad-authentication-decision-tree"></a>Az Azure AD hitelesítési döntési fája

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>A hitelesítési módszerek részletes szempontok

### <a name="cloud-authentication-password-hash-sync"></a>Felhőalapú hitelesítés: Jelszókivonat-szinkronizálás 

* **Elérhető:** Jelszókivonat-szinkronizálás telepítési, karbantartási és infrastruktúra legalább részéről az erőfeszítés szükséges a szervezet számára, akik csak a felhasználók jelentkezhetnek be az Office 365, SaaS-alkalmazásokhoz és más Azure AD-alapú erőforrások engedélyezni kell. Ha engedélyezve van, a Jelszókivonatok szinkronizálása az Azure AD Connect szinkronizálási folyamat része, és a két percenként. 

* **Felhasználói élmény:** javasoljuk, hogy a szervezetek üzembe helyezéséhez zökkenőmentes egyszeri bejelentkezést (SSO) Jelszókivonat-szinkronizálás lehetőleg ne a szükségtelen kér, miután bejelentkezett azokat a felhasználói bejelentkezési élmény javítása érdekében.

* **Speciális forgatókönyvek:** szervezetek mellett dönt, esetén lehetséges az Azure AD Identity Protection jelentéseit, például a kiszivárgott hitelesítő adatok jelentés identitások információkat kaphat a. Vállalati Windows Hello egy másik lehetőség, amely rendelkezik [konkrét követelmények Jelszókivonat-szinkronizálás használatakor](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). A szervezetek Jelszókivonat-szinkronizálás a többtényezős hitelesítés megkövetelése az Azure AD többtényezős hitelesítést kell használnia, és nem használható egy harmadik féltől származó vagy a helyi multi-factor Authentication hitelesítési módszerek.

* **Az üzletmenet folytonossága:** Jelszókivonat-szinkronizálás eredendően magas rendelkezésre állást biztosít, és az összes Microsoft adatközpontjaiban felhőszolgáltatásként. Javasoljuk, hogy a második az Azure AD Connect-kiszolgáló átmeneti módban a vész-helyreállítási célokra készenléti konfigurációban van-e telepítve.

* **Szempontok:** Jelszókivonat-szinkronizálás azonnal érvényesíti azokat a módosításokat a helyi fiók állapota jelenleg. Ebben az esetben a felhasználók felhőalapú alkalmazásokba, csak a felhasználói fiók állapota szinkronizálva az Azure ad hozzáféréssel rendelkeznek. Ha ez a korlátozás áthidalható kívánják, javasolt egy új szinkronizálási ciklust követően a rendszergazdák számára a helyi felhasználói fiók, például fiókok letiltása frissítések tömeges aktiválva lesz. A fiók zárolt egy másik felhasználói fiók állapota, amely a következő ciklusban van-e szinkronizálva. 

> [!NOTE] 
> A jelszó lejárt állapotban nem jelenleg szinkronizálva az Azure AD az az Azure AD Connect. 

Tekintse meg [Jelszókivonat-szinkronizálás megvalósítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) a központi telepítés lépéseit.

### <a name="cloud-authentication-pass-through-authentication"></a>Felhőalapú hitelesítés: áteresztő hitelesítés  

* **Elérhető:** áteresztő hitelesítés esetén szükség van egy vagy több (három javasolt) egyszerűsített ügynököt telepíteni meglévő kiszolgálókon, aki hozzáféréssel rendelkezik a helyszíni Active Directory tartományi szolgáltatások, beleértve a hozzáférést a helyszíni AD-tartomány tartományvezérlők. Ügynökök kell a kimenő Internet-hozzáféréssel, és elérheti a tartományvezérlőkhöz. Emiatt nem támogatott a szegélyhálózaton, az ügynökök telepítésére, amelyhez a korlátozás nélküli hálózati tartományvezérlőhöz történő hozzáférés szükséges. Az összes hálózati forgalom titkosítva, és a hitelesítési kérelmek korlátozódik. Ez a folyamat további információkért tekintse meg a [biztonsági mélyreható](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) az átmenő hitelesítés.

* **Felhasználói élmény:** javasoljuk, hogy a szervezetek zökkenőmentes egyszeri bejelentkezést a felhasználói bejelentkezés során tapasztal élmény javítására lehetőleg ne a szükségtelen kér, miután azok bejelentkezett átmenő hitelesítéssel telepítését.

* **Speciális forgatókönyvek:** áteresztő hitelesítés azonnal érvénybe lépteti a helyi házirend. Például elérését, amikor egy helyi felhasználói fiók állapota le van tiltva, zárolva van, a jelszó lejárt vagy a felhasználó kívül esik a nyitvatartási idő engedélyezett.  A szervezetek átmenő hitelesítést a multi-factor Authentication hitelesítést igénylő az Azure AD többtényezős hitelesítést kell használnia, és nem használható egy harmadik féltől származó vagy a helyi multi-factor authentication módszer. Speciális szolgáltatások, például a kiszivárgott hitelesítő adatok a jelentés az Identity Protection telepítésére van szükség Jelszókivonat-szinkronizálás van függetlenül Ha úgy dönt, hogy átmenő hitelesítést.

* **Az üzletmenet folytonossága:** ajánlott telepíteni, két extra átmenő hitelesítés ügynökök, a hitelesítési kérelmek magas rendelkezésre állásának biztosításához az Azure AD Connect-kiszolgáló az első ügynök mellett. Ha rendelkezik telepített ügynökök három, egy ügynök továbbra is sikertelen lehet, ha egy másik ügynök karbantartási nem működik. Egy másik előnye, hogy a Jelszókivonat-szinkronizálás telepítése az átmenő hitelesítés mellett az működhessen biztonsági hitelesítési módszert, ha elsődleges hitelesítési módszert már nem érhető el, például amikor a helyszíni kiszolgálók nem érhetők el.

* **Szempontok:** Ha Jelszókivonat-szinkronizálás az átmenő hitelesítés tartalék hitelesítési módszerként használhatják az ügynökök nem lehet érvényesíteni a felhasználói hitelesítő adatokat, majd a feladatátvételt a Jelszókivonatok szinkronizálása nem történik meg automatikusan. Váltás a bejelentkezési módszer manuálisan az Azure AD Connect használatával kell. Áteresztő hitelesítés csak a felhőalapú alkalmazások, amelyek a modern hitelesítést és az Exchange Online például ActiveSync-, POP3- és IMAP4 protokollok támogatja. Például [Microsoft Office 2013 és újabb verziók által támogatott modern hitelesítést, de a korábbi verziók nem](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) további információk az Office-alkalmazások támogatása. Lásd: [gyakran ismételt kérdések](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) és egyéb szempontok átmenő hitelesítést, beleértve a másik azonosító a támogatja.

Tekintse meg [átmenő hitelesítés végrehajtási](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) a központi telepítés lépéseit.

### <a name="federated-authentication"></a>Összevont hitelesítés

* **Elérhető:** összevont hitelesítés rendszert használ egy külső rendszer felhasználók hitelesítésére támaszkodnak. Egyes vállalatok szeretné, hogy az Azure AD hibrid identitáskezelési megoldás a meglévő összevont rendszer befektetés újból. A karbantartás és az összevont rendszer kezelése az Azure AD felügyelete kívül esik. A szervezet az összevont rendszer segítségével ellenőrizze, hogy a rendszer biztonságosan, és kezelheti a hitelesítési terhelés esetén. 

* **Felhasználói élmény:** a felhasználói élmény összevont hitelesítési a szolgáltatások, topológia és az összevonási kiszolgálófarm konfigurációja végrehajtásának függ. Egyes szervezetek erre a rugalmasságra igazítja, és konfigurálja a biztonsági követelményeinek megfelelően az összevonási kiszolgálófarmhoz hozzáférést igényelnek. Például úgy is konfigurálhatja a felhasználók automatikus bejelentkezés, anélkül, hogy a névtelen hitelesítő adatok, mert azok rendelkezik már bejelentkezett az eszközükkel belső csatlakozó felhasználók és eszközök. Másrészről Ha szükséges, néhány speciális funkciókat is megnehezítik a felhasználói bejelentkezési folyamat.

* **Speciális forgatókönyvek:** külső hitelesítési megoldás általában szükséges. Ha a felhasználóknak kell egy nem natív módon támogatja az Azure AD hitelesítési követelményt, részletes információkat [az itt felsorolt](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/), de közös követelmények a következők:

    * Intelligens kártyák vagy tanúsítványokat igénylő hitelesítési
    * Használja a helyi multi-factor Authentication kiszolgáló vagy a külső multi-factor hitelesítésszolgáltató.
    * Külső hitelesítés megoldással hitelesítés. Tekintse meg a [az Azure AD összevonás kompatibilitási listája](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Felhasználói azonosítójával kell bejelentkeznie a sAMAccountName, például TARTOMÁNY\felhasználónév helyett egy egyszerű felhasználónév (UPN), például user@domain.com

* **Az üzletmenet folytonossága:** külső rendszerek általában szükség van egy betöltési elosztott terhelésű kiszolgálók, más néven a farmhoz, konfigurált tömböt egy belső hálózati és a külső hálózati topológia hitelesítési kérelmek magas rendelkezésre állásának biztosításához. Jelszókivonat-szinkronizálás telepíthető együtt külső hitelesítés tartalék hitelesítési módszerként Ha elsődleges hitelesítési módszert már nem érhető el, például amikor a helyszíni kiszolgálók nem érhetők el. Néhány nagyvállalat több Internet belépési pontok konfigurálva a földrajzi-DNS-ben a kis késleltetésű hitelesítési kérelmek támogatásához összevonási megoldást követel meg.

* **Szempontok:** külső rendszerek használatához általában szükség van egy jelentősebb helyszíni infrastrukturális beruházások. A legtöbb szervezet válassza ezt a beállítást, ha azok már rendelkezik egy helyszíni összevonási és erős üzleti követelmény egy egyetlen identitást szolgáltató használatára. Összevonási összetettebb működik, illetve háríthatóak el annak felhőalapú hitelesítési megoldás képest. Felhasználói azonosítók használata nem átirányítható tartománnyal, amely nem lehet ellenőrizni, az Azure AD-rászoruló felesleges konfigurálás végrehajtásához jelentkezzen be. Ez a követelmény nevezik másodlagos bejelentkezési azonosító támogatása. Lásd: [másodlagos bejelentkezési azonosító beállítása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) korlátozásokat és követelményeket. Ha egy külső többtényezős hitelesítési szolgáltató az összevonási használatát választja, győződjön meg arról a szolgáltató támogatja a WS-Trust, az Azure AD join eszközök.

Tekintse meg [összevonási szolgáltatások végrehajtási](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) a központi telepítés lépéseit.

> [!NOTE] 
> Az Azure AD hibrid identitáskezelési megoldás telepítésekor biztosítania kell a támogatott topológiák az Azure AD Connect egyik megvalósítása. Ismerje meg, több támogatott és nem támogatott a következő konfigurációk [az Azure AD Connect topológiák](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Architektúra

A következő diagram vázol fel a magas szintű architektúra összetevők minden egyes hitelesítési módszer az Azure AD hibrid identitáskezelési megoldás használata szükséges. Biztosítja a hasonlítsa össze a megoldások áttekintése.

Az alábbi ábra bemutatja azokat a Jelszókivonat-szinkronizálás megoldás az egyszerűség kedvéért:

![PHS-BEN](media/azure-ad/azure-ad-authn-image2.png)

Az alábbi ábra mutatja be az átmenő hitelesítés ügynök követelményeinek:

![ESP](media/azure-ad/azure-ad-authn-image3.png)

A következő ábra bemutatja azokat a szegélyhálózat és a szervezet belső hálózati összevonási szükséges összetevők:

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>A hitelesítési módszerek összehasonlítása

|Szempont|Jelszókivonat-szinkronizálás + zökkenőmentes SSO|Áteresztő hitelesítés + zökkenőmentes SSO|Összevonás az AD FS rendszerrel|
|:-----|:-----|:-----|:-----|
|Amikor megtörténik a hitelesítési?|A felhőben|A felhőben, a biztonságos jelszó ellenőrzési adatcsere a helyszíni hitelesítési ügynök után|Helyszíni követelmények|
|Mik a helyi kiszolgáló követelményei a kiépítési rendszer túl: az Azure AD Connect?|None|minden további hitelesítési ügynök 1 kiszolgálót|2 vagy több AD FS-kiszolgáló<br>a szegélyhálózati/DMZ hálózati 2 vagy több WAP-kiszolgálókkal|
|Mik azok a helyszíni Internet és hálózati követelményei a kiépítési rendszer túl?|None|[Kimenő Internet-hozzáférés](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) a kiszolgálók futtató hitelesítési ügynökök|[A bejövő Internet-hozzáférés](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) a szegélyhálózatban található WAP-kiszolgálókkal<br>Bejövő hálózati hozzáférést AD FS-kiszolgáló a szegélyhálózatban található WAP-kiszolgálókkal<br>A hálózati terheléselosztás|
|Nem szükséges egy SSL-tanúsítvány?|Nem|Nem|Igen|
|Van egy állapotfigyelési megoldás?|Nem szükséges|Ügynök állapota által biztosított [Azure Active Directory felügyeleti központban](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Hajtsa végre a felhasználónál egyszeri bejelentkezést a felhőalapú erőforrásokhoz a tartományhoz csatlakoztatott eszközök a vállalati hálózaton belül?|Igen, a [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Igen, a [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Igen|
|Milyen bejelentkezési típusok támogatottak?|UserPrincipalName + jelszó<br>Integrált Windows-hitelesítés használatával [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Másodlagos bejelentkezési Azonosítóval](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + jelszó<br>Integrált Windows-hitelesítés használatával [zökkenőmentes SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Másodlagos bejelentkezési Azonosítóval](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + jelszó<br>sAMAccountName + jelszó<br>Integrált Windows-hitelesítés<br>[Tanúsítvány és az intelligens kártyás hitelesítés](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[Másodlagos bejelentkezési Azonosítóval](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Az a Windows Hello támogatott vállalati?|[Kulcs megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Az Intune tanúsítvány-megbízhatósági modell](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Kulcs megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Az Intune tanúsítvány-megbízhatósági modell](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Kulcs megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Tanúsítvány-megbízhatósági modell](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Mik a többtényezős hitelesítési beállítások?|[Azure MFA használatával](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA használatával](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA használatával](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Azure MFA-kiszolgáló](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[Harmadik felek többtényezős hitelesítés](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Milyen felhasználói fiók állapotok támogatottak?|Letiltott fiókok<br>A fiók zárolása<br>(legfeljebb 30 perces késleltetés)|Letiltott fiókok<br>A fiók zárolása<br>A jelszó lejárt<br>Bejelentkezési üzemideje (óra)|Letiltott fiókok<br>A fiók zárolása<br>A jelszó lejárt<br>Bejelentkezési üzemideje (óra)|
|Mik a feltételes hozzáférési beállítások?|[Az Azure AD feltételes hozzáférés ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Az Azure AD feltételes hozzáférés ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Az Azure AD feltételes hozzáférés](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[AD FS jogcímszabályok](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Támogatott örökölt protokollok blokkolja?|Nem|Nem|[Igen](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Testreszabható az emblémát, a lemezkép és a leírást a bejelentkezési oldalon?|[Az Azure AD Premium Igen](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Az Azure AD Premium Igen](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Igen](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Milyen speciális forgatókönyvek támogatottak?|[Jelszó az intelligens zárolás](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[Hitelesítő adatok jelentések szivárognak](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Jelszó az intelligens zárolás](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Többhelyes kis késleltetésű hitelesítési rendszer<br>[AD FS extranet kizárás](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[Integráció a külső gyártótól származó identitáskezelő rendszerekkel](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Javaslatok és az Azure AD szempontok

Az azonosítási rendszer biztosítja, hogy a felhasználók rendelkeznek-e a felhőalapú alkalmazások és az üzletági alkalmazások áttelepítése, és hogy elérhető legyen a felhő elérésére. Hitelesítési szabályozza a jogosult felhasználók hatékony és ezeken kívül a vállalat bizalmas adatokhoz, alkalmazásokhoz való hozzáférés. Emiatt vegye figyelembe a következőket a ki a szervezet számára megfelelő hitelesítési módszert. 

Használja, vagy engedélyezze a Jelszókivonat-szinkronizálás függetlenül attól, milyen hitelesítési módszert választja, a következő okok miatt:

1. **Magas rendelkezésre állás és a katasztrófa utáni helyreállítás:** áteresztő hitelesítés és az összevonási a helyszíni infrastruktúrára alapulnak. A helyszíni kezdjen átmenő hitelesítés esetén a kiszolgáló hardver- és hálózati az átmenő hitelesítés ügynökök igényelnek. Az összevonáshoz a helyszíni kezdjen mindig nagyobb, mert a proxy hitelesítési kérelemre peremhálózat és a belső összevonási kiszolgálók igényel. Egyetlen ponton felmerülő hibák elkerülése érdekében a szervezet redundáns kiszolgálók győződjön meg arról, ha valamelyik összetevő sikertelen hitelesítési kérelmek kiszolgált mindig telepítse. Átmenő hitelesítés, mind az összevonási nélkül is sikertelen lehet hitelesítési kérelmek megválaszolásához tartományvezérlők egyaránt. Ezek az összetevők számos kell, hogy karbantartási kifogástalan állapotú marad, és valamilyen okból kimaradás esetén nagyobb a valószínűsége karbantartási nem tervezett, és megfelelően implementálva. Valamilyen okból kimaradás elkerülhetők a Jelszókivonat-szinkronizálás használatával, mert a Microsoft Azure AD felhőalapú hitelesítési szolgáltatás globálisan méretezi, és mindig elérhető.

2. **A helyszíni kimaradás túlélési:** egy számítógépes-támadás vagy katasztrófa miatt a helyszíni kimaradás következményeinek jelentős, nem foglalkozik a támadás mozgáskorlátozottak szervezet reputational márka kárt közötti lehet. Az utolsó évben számos szervezet a kártevő-támadásokat, beleértve a célzott nevű, le kell a helyszíni kiszolgálók okozó áldozatai volt. Így az ügyfelek az ilyen támadások kezelésére, a Microsoft első fellépése szervezetek két csoportja:

   a. A szervezeteknek, amelyek korábban a Jelszókivonat-szinkronizálás engedélyezve volt ismét online óra be a Jelszókivonat-szinkronizálás használandó hitelesítési módszer módosítását. Access használatával keresztül Office 365 e-mail sikerült működnek a problémák megoldásához, és más felhőalapú munkaterhelések hozzáféréssel rendelkeznek.

   b. A szervezeteknek, amelyek korábban nem engedélyezi a Jelszókivonat-szinkronizálás kellett a feloldó problémák és a nem megbízható külső fogyasztói e-mail rendszerek igénybe. Ezekben az esetekben végrehajtásának őket hét vagy több is működik, és újra kell.

3. **Azonosító adatok védelmét:** a legjobb módszer a felhőbeli felhasználók védelmére egyike az Azure AD Identity Protection. Microsoft folyamatosan ellenőrzi az interneten a felhasználói és jelszavak listája, ezeken eladásra, és a sötét weben elérhetővé. Az Azure AD ezen információk használatával győződjön meg arról, ha a felhasználóneveket és jelszavakat a szervezetben kerülnek veszélybe. Ezért fontos a Jelszókivonat-szinkronizálás engedélyezése a függetlenül attól, milyen hitelesítési módszert használja, hogy ez az összevont vagy átmenő hitelesítés. Kiszivárgott hitelesítő adatok jelennek meg a jelentés és letiltó vagy a felhasználó megváltoztathatja a jelszavát, ha mégis megpróbálják kiszivárgott jelszóval történő bejelentkezéshez használható.

Végül megfelelően a [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft be van állítva a legtöbb teljes körű identitás és hozzáférés-kezelés a funkciókat. Microsoft fends ki a hét trillió számítógépes események napi ugyanakkor biztosítható a bejelentkezéshez Office 365-höz hasonló SaaS-alkalmazásokhoz több ezer szinte bármilyen eszközről engedéllyel rendelkező felhasználók számára. 

## <a name="conclusion"></a>Összegzés

Az ebben a cikkben vázol fel különböző hitelesítési beállításait, a szervezetek konfigurálásával és telepítésével felhőalapú alkalmazásokba való hozzáférés támogatásához. Szervezet különböző üzleti, biztonsági és műszaki követelmények teljesítéséhez választhat Jelszókivonat-szinkronizálás, az átmenő hitelesítés és az összevonási. Minden hitelesítési módszerrel a szervezet kiválaszthatja, hogy az üzleti igényeknek tárgyalt részéről az erőfeszítés, a megoldás és a felhasználói élmény, a bejelentkezési folyamat központi telepítése. Fel kell mérnie, hogy szervezetének szüksége van a speciális forgatókönyveket és üzleti folytonosságot biztosító szolgáltatásokat az egyes hitelesítési módszerek is. Végezetül kell kiértékelni az egyes hitelesítési módszerek megjelenítéséhez, ha bármelyik megakadályozhatják a kiválasztott végrehajtási ismertetett szempontok alapján.

## <a name="next-steps"></a>További lépések

Napjaink világ fenyegetések jelen a nap 24 órájában, és everywhere származik. A megfelelő hitelesítési módszert fog segíthetnek a biztonsági kockázatok csökkentése és a identitások védelme. 

[Első lépések](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) az Azure AD és a szervezete számára megfelelő hitelesítési megoldás üzembe helyezéséhez.

Ha a felhőalapú hitelesítés összevont áttelepítéséhez, további [módosításával a bejelentkezési módszer](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Segítséget tervezését és megvalósítását az áttelepítést, használhatja a [ezek projekt segít tervek](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/tree/master/Authentication).
