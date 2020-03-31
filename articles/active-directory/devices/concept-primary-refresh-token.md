---
title: Elsődleges frissítési jogkivonat (PRT) és Az Azure AD – Azure Active Directory
description: Mi a szerepe, és hogyan kezeljük az elsődleges frissítési jogkivonatot (PRT) az Azure Active Directoryban?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672634"
---
# <a name="what-is-a-primary-refresh-token"></a>Mi az elsődleges frissítési jogkivonat?

Az elsődleges frissítési jogkivonat (PRT) az Azure AD-hitelesítés egyik fő összetevője Windows 10, iOS és Android-eszközökön. Ez egy JSON Web Token (JWT) kifejezetten a Microsoft első féltoken-brókerek számára, amelyek lehetővé teszik az egyszeri bejelentkezést (SSO) az ezeken az eszközökön használt alkalmazások között. Ebben a cikkben részletesen bemutatjuk, hogyan adják ki, használják és védik a PRT-t Windows 10-es eszközökön.

Ez a cikk feltételezi, hogy már ismeri az Azure AD-ben elérhető különböző eszközállapotokat, és hogy hogyan működik az egyszeri bejelentkezés a Windows 10-ben. Az Azure AD-ben lévő eszközökről a [Mi az eszközkezelés az Azure Active Directoryban című cikkben olvashat bővebben?](overview.md)

## <a name="key-terminology-and-components"></a>A legfontosabb terminológiák és összetevők

A következő Windows-összetevők kulcsszerepet játszanak a PRT igénylésében és használatában:

* **Felhőalapú hitelesítésszolgáltató** (CloudAP): A CloudAP a Windows bejelentkezés korszerű hitelesítési szolgáltatója, amely ellenőrzi a Windows 10-es eszközre bejelentkező felhasználókat. A CloudAP egy beépülő keretrendszert biztosít, amelyre az identitásszolgáltatók építhetnek, hogy az adott identitásszolgáltató hitelesítő adataival lehetővé tegyék a Windows hitelesítését.
* **Webaccount Manager** (WAM): WaM az alapértelmezett token bróker a Windows 10-eszközökön. A WAM egy olyan beépülő keretrendszert is biztosít, amelyre az identitásszolgáltatók építhetnek, és engedélyezhetik az adott identitásszolgáltatóra támaszkodó alkalmazásaiks használatát.
* **Azure AD CloudAP plugin:** A CloudAP-keretrendszerre épülő Azure AD-specifikus bővítmény, amely a Windows bejelentkezése során az Azure AD-vel ellenőrzi a felhasználói hitelesítő adatokat.
* **Azure AD WAM plugin:** A WAM-keretrendszerre épülő Azure AD-specifikus bővítmény, amely lehetővé teszi az SSO-t az Azure AD-hitelesítésre támaszkodó alkalmazások számára.
* **Dsreg**: Egy Azure AD-specifikus összetevő a Windows 10-en, amely kezeli az eszköz regisztrációs folyamatát az összes eszközállapotok.
* **Platformmegbízhatósági modul** (TPM): A TPM egy eszközbe épített hardverösszetevő, amely hardveralapú biztonsági funkciókat biztosít a felhasználói és eszköztitkokszámára. További részletek a [Platformmegbízhatósági modul technológia áttekintése](/windows/security/information-protection/tpm/trusted-platform-module-overview)című cikkben találhatók.

## <a name="what-does-the-prt-contain"></a>Mit tartalmaz a PRT?

A PRT tartalmazza a jogcímek általában tartalmaz bármely Azure AD frissítési jogkivonatot. Ezenkívül a PRT tartalmaz néhány eszközspecifikus követelést. Ezek a következők:

* **Eszközazonosító**: A PRT egy adott eszközön lévő felhasználó számára kerül kiadásra. Az eszközazonosító-jogcím `deviceID` azt az eszközt határozza meg, amelyen a PRT-t kiállították a felhasználó számára. Ezt az igényt később a PRT-n keresztül kapott tokenek számára adják ki. Az eszközazonosító-jogcím a feltételes hozzáférés engedélyezése nek az eszköz állapota vagy megfelelősége alapján történő meghatározására szolgál.
* **Munkamenetkulcs:** A munkamenetkulcs egy titkosított szimmetrikus kulcs, amelyet az Azure AD hitelesítési szolgáltatás generál, és amelyet a PRT részeként adnak ki. A munkamenetkulcs a birtoklás bizonyítékaként működik, ha a PRT-t más alkalmazások tokenek beszerzésére használják.

### <a name="can-i-see-whats-in-a-prt"></a>Megnézhetem, mi van a PRT-ben?

A PRT egy átlátszatlan blob az Azure AD-ből küldött, amelynek tartalma nem ismert, hogy az ügyfél-összetevők. Nem látod, mi van a PRT-ben.

## <a name="how-is-a-prt-issued"></a>Hogyan történik a PRT kiadása?

Az eszközregisztráció az Azure AD-ben az eszközalapú hitelesítés előfeltétele. A PRT-t csak regisztrált eszközökön bocsátják ki a felhasználók számára. Az eszközregisztrációval kapcsolatos részletesebb részleteket a Windows Hello vállalati verzió és az eszközregisztráció című [cikkben találja.](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration) Az eszköz regisztrációja során a dsreg komponens két titkosítási kulcspárt hoz létre:

* Eszközkulcs (dkpub/dkpriv)
* Szállítási kulcs (tkpub/tkpriv)

A személyes kulcsok az eszköz TPM-jéhez vannak kötve, ha az eszköz érvényes és működő TPM-mel rendelkezik, míg a nyilvános kulcsokat az eszköz regisztrációs folyamata során elküldi az Azure AD-nek. Ezek a kulcsok az eszköz állapotának érvényesítésére szolgálnak a PRT-kérelmek során.

A PRT-t a Windows 10-es eszközön a felhasználói hitelesítés során adják ki két esetben:

* **Az Azure AD-hez csatlakozott** vagy **a hibrid Azure AD csatlakozott:** A Windows bejelentkezés során egy PRT-t ad ki, amikor egy felhasználó bejelentkezik a szervezeti hitelesítő adataival. A PRT minden Támogatott Windows 10-es hitelesítő adattal rendelkezik, például jelszóval és Windows Hello for Business-val. Ebben a forgatókönyvben az Azure AD CloudAP beépülő modul a PRT elsődleges hatósága.
* **Azure AD regisztrált eszköz:** A PRT akkor kerül kiadásra, amikor egy felhasználó másodlagos munkahelyi fiókot ad hozzá a Windows 10-es eszközéhez. A felhasználók kétféleképpen adhatnak hozzá fiókot a Windows 10-hez -  
   * Fiók hozzáadása a **Fiók használata mindenhol ezen az eszközön,** miután bejelentkezett egy alkalmazásba (például outlook)
   * Fiók hozzáadása a > Beállítások **fiókokból** > **Accounts** > **Hozzáférés a munkához vagy az Iskolai****csatlakozáshoz**

Az Azure AD regisztrált eszköz forgatókönyvek, az Azure AD WAM plugin az elsődleges hatóság a PRT, mivel a Windows bejelentkezés nem történik meg ezzel az Azure AD-fiókkal.

> [!NOTE]
> A harmadik félidentitás-szolgáltatóknak támogatniuk kell a WS-Trust protokollt, hogy engedélyezze a PRT-kibocsátást a Windows 10-es eszközökön. Ws-Trust nélkül a PRT nem adható ki a felhasználóknak a hibrid Azure AD-hez vagy az Azure AD-hez csatlakozott eszközökön

## <a name="what-is-the-lifetime-of-a-prt"></a>Mi az élettartama a PRT?

A kiadást követően a PRT 14 napig érvényes, és folyamatosan megújul mindaddig, amíg a felhasználó aktívan használja az eszközt.  

## <a name="how-is-a-prt-used"></a>Hogyan kell alkalmazni a PRT-t?

A PRT-t a Windows két fő összetevője használja:

* **Azure AD CloudAP beépülő modul:** A Windows bejelentkezése során az Azure AD CloudAP beépülő modul prt-t kér az Azure AD-től a felhasználó által megadott hitelesítő adatok használatával. Gyorsítótárazakat is a PRT-hez, hogy gyorsítótárazhassanak, ha a felhasználó nem fér hozzá az internetkapcsolathoz.
* **Azure AD WAM plugin:** Amikor a felhasználók megpróbálnak hozzáférni az alkalmazásokhoz, az Azure AD WAM beépülő modul a PRT-t használja az SSO engedélyezéséhez a Windows 10 rendszeren. Az Azure AD WAM beépülő modul a PRT-t használja a frissítési és hozzáférési jogkivonatok kéréséhez a jogkivonat-kérelmekhez wam-függő alkalmazásokhoz. Azt is lehetővé teszi, SSO a böngészők ben intravénás a PRT a böngésző kéri. A Windows 10 böngészős sso-ja támogatott a Microsoft Edge (natív) és a Chrome (a Windows 10-fiókok vagy az Office Online bővítmény en keresztül).

## <a name="how-is-a-prt-renewed"></a>Hogyan újítják meg a PRT-t?

A PRT-t két különböző módon újítják meg:

* **Az Azure AD CloudAP beépülő modul 4 óránként:** A CloudAP-bővítmény 4 óránként megújítja a PRT-t a Windows bejelentkezése során. Ha a felhasználó nem rendelkezik internetkapcsolattal ez idő alatt, a CloudAP plugin megújítja a PRT-t, miután az eszköz csatlakozik az internethez.
* **Azure AD WAM plugin az alkalmazás tokenkérelmek során:** A WAM beépülő modul lehetővé teszi az Egyszeri szolgáltatás t A Windows 10-es eszközökön azáltal, hogy csendes tokenkérelmeket engedélyez az alkalmazásokszámára. A WAM plugin megújíthatja a PRT során ezek token kérelmek két különböző módon:
   * Egy alkalmazás csendben kéri a WAM-ot egy hozzáférési jogkivonathoz, de az adott alkalmazáshoz nem érhető el frissítési jogkivonat. Ebben az esetben a WAM a PRT-t használja az alkalmazás jogkivonat ának kéréséhez, és egy új PRT-t kap vissza a válaszban.
   * Egy alkalmazás kéri a WAM egy hozzáférési jogkivonatot, de a PRT érvénytelen, vagy az Azure AD további hitelesítést igényel (például Az Azure többtényezős hitelesítés). Ebben a forgatókönyvben a WAM interaktív bejelentkezést kezdeményez, amely megköveteli a felhasználótól, hogy újra hitelesítse magát, vagy további ellenőrzést biztosítson, és a sikeres hitelesítéskor új PRT-t ad ki.

### <a name="key-considerations"></a>Fő szempontok

* A PRT csak a natív alkalmazás hitelesítése során kerül kiadásra és megújításra. A PRT nem újul meg vagy nem ad ki böngészőmunkamenet során.
* Az Azure AD-hez csatlakozott és hibrid Azure AD-hez csatlakozott eszközök, a CloudAP plugin az elsődleges hatóság a PRT. Ha egy PRT-t megújítanak egy WAM-alapú jogkivonat-kérelem során, a PRT-t visszaküldi a CloudAP-bővítménynek, amely ellenőrzi a PRT érvényességét az Azure AD-vel, mielőtt elfogadná.

## <a name="how-is-the-prt-protected"></a>Hogyan védi a PRT-t?

A PRT-t úgy védi, hogy ahhoz az eszközhöz köti, amelybe a felhasználó bejelentkezett. Az Azure AD és a Windows 10 a következő módszerekkel engedélyezi a PRT-védelmet:

* **Az első bejelentkezéskor:** Az első bejelentkezéskor a PRT-t az eszközregisztráció során létrehozott eszközkulcs-titkosítással történő aláírási kérelmek kel bocsátják ki. Érvényes és működő TPM-mel rendelkező eszközökön az eszközkulcsot a TPM biztosítja, megakadályozva a rosszindulatú hozzáférést. A PRT nem kerül kiadásra, ha a megfelelő eszközkulcs-aláírás nem érvényesíthető.
* **A tokenkérelmek és a megújítás**során: Ha egy PRT-t ad ki, az Azure AD is kiad egy titkosított munkamenetkulcsot az eszközhöz. A rendszer az eszközregisztráció részeként az Azure AD-be létrehozott és küldött tömegközlekedési kulccsal (tkpub) van titkosítva. Ezt a munkamenetkulcsot csak a TPM által védett privát átviteli kulcs (tkpriv) fejti vissza. A munkamenetkulcs az Azure AD-nek küldött kérelmek et igazoló (POP) kulcs.  A munkamenetkulcsot a TPM is védi, és más operációsrendszer-összetevő nem férhet hozzá. A jogkivonat-kérelmeket vagy a PRT-megújítási kérelmeket ez a munkamenetkulcs biztonságosan aláírta a TPM-en keresztül, ezért nem lehet illetéktelenül illetéktelenül kezelni. Az Azure AD érvényteleníti az eszközről érkező, a megfelelő munkamenetkulcs által nem aláírt kérelmeket.

Ha ezeket a kulcsokat a TPM-mel biztosítja, a rosszindulatú szereplők nem lophatják el a kulcsokat, és nem játszhatják vissza a PRT-t máshol, mivel a TPM akkor sem érhető el, ha a támadó fizikai birtokában van az eszköz.  Így a TPM használata nagyban növeli az Azure AD-hez csatlakozott, a hibrid Azure AD csatlakozott, és az Azure AD regisztrált eszközök hitelesítő adatok ellopása ellen. A teljesítmény és a megbízhatóság érdekében a TPM 2.0 az ajánlott verzió az összes Azure AD-eszköz regisztrációs forgatókönyvéhez Windows 10 rendszeren.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Hogyan védik az alkalmazástokeneket és a böngészőcookie-kat?

**Alkalmazástokenek:** Amikor egy alkalmazás jogkivonatot kér a WAM-on keresztül, az Azure AD egy frissítési jogkivonatot és egy hozzáférési jogkivonatot ad ki. A WAM azonban csak a hozzáférési jogkivonatot adja vissza az alkalmazásnak, és a felhasználó adatvédelmi alkalmazásprogramozási felületének (DPAPI) kulccsal titkosítja a frissítési jogkivonatot a gyorsítótárában. A WAM biztonságosan használja a frissítési jogkivonatot a munkamenetkulettel együtt a további hozzáférési jogkivonatok kiadásához. A DPAPI-kulcsot egy Azure AD-alapú szimmetrikus kulcs biztosítja az Azure AD-ben. Ha az eszköznek vissza kell fejtenie a felhasználói profilt a DPAPI-kulccsal, az Azure AD biztosítja a munkamenetkulcs által titkosított DPAPI-kulcsot, amelyet a CloudAP beépülő modul a TPM visszafejtéséhez kér. Ez a funkció biztosítja a frissítési jogkivonatok biztonságossá tétele konzisztenciát, és elkerüli, hogy az alkalmazások saját védelmi mechanizmusaikat hajtsák végre.  

**Böngészőcookie-k**: A Windows 10-ben az Azure AD támogatja a böngésző sso-ját az Internet Explorerben és a Microsoft Edge-ben natív módon vagy a Google Chrome-ban a Windows 10-fiókok bővítményen keresztül. A biztonság nem csak a cookie-k védelmére épül, hanem azokra a végpontokra is, amelyekre a cookie-kat küldik. A böngészőcookie-k ugyanúgy védettek, mint a PRT, a munkamenetkulcs használatával a cookie-k aláírásához és védelméhez.

Amikor a felhasználó böngészőalapú interakciót kezdeményez, a böngésző (vagy bővítmény) meghívja a COM natív ügyfélállomását. A natív ügyfélállomás biztosítja, hogy a lap az engedélyezett tartományok egyikéből származik. A böngésző más paramétereket is küldhet a natív ügyfélállomásnak, beleértve a nonce-t is, azonban a natív ügyfélállomás garantálja az állomásnév érvényesítését. A natív ügyfélgazda prt-cookie-t kér a CloudAP beépülő modultól, amely létrehozza és aláírja azt a TPM-védelemmel ellátott munkamenetkulccsal. Mivel a PRT-cookie-t a munkamenetkulcs írta alá, nem lehet illetéktelenül illetéktelenül illetéktelenül kezelni. Ez a PRT-cookie szerepel az Azure AD kérelemfejlécében az eszköz érvényesítéséhez, amelyről származik. Ha a Chrome böngészőt használja, csak a natív ügyfélgazda jegyzékfájljában kifejezetten definiált bővítmény akadályozhatja meg, hogy tetszőleges bővítmények hajtsák elő ezeket a kéréseket. Miután az Azure AD érvényesíti a PRT cookie-t, kiad egy munkamenet-cookie-t a böngészőnek. Ez a munkamenet-cookie ugyanazt a munkamenetkulcsot is tartalmazza, amelyet prt-vel adtak ki. A későbbi kérések során a munkamenetkulcs hatékonyan kötelezővé teszi a cookie-t az eszközhöz, és megakadályozza a máshonnan érkező visszajátszásokat.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Mikor kap egy PRT egy MFA követelést?

A PRT kaphat többtényezős hitelesítési (MFA) jogcímet bizonyos esetekben. Ha egy MFA-alapú PRT-t használ az alkalmazások jogkivonatai kéréséhez, az MFA-jog cím átkerül ezekre az alkalmazásjogkivonatokra. Ez a funkció zökkenőmentes élményt nyújt a felhasználóknak azáltal, hogy megakadályozza az MFA-kihívást minden olyan alkalmazás esetében, amely ezt igényli. A PRT a következő módokon kaphat Többfél-fa-igénylést:

* **Bejelentkezés a Windows Hello for Business szolgáltatással:** A Windows Hello for Business lecseréli a jelszavakat, és titkosítási kulcsokat használ az erős kétfaktoros hitelesítéshez. A Windows Hello for Business az eszközön lévő felhasználókra jellemző, és saját maga is megköveteli az MFA kiépítését. Amikor egy felhasználó bejelentkezik a Windows Hello for Business szolgáltatással, a felhasználó PRT-je MFA-jogcímet kap. Ez a forgatókönyv az intelligens kártyákkal bejelentkező felhasználókra is vonatkozik, ha az intelligenskártyás hitelesítés MFA-jogcímet hoz létre az ADFS-ből.
   * Mivel a Windows Hello for Business többtényezős hitelesítésnek minősül, az MFA-jog frissül, amikor maga a PRT frissül, így az MFA időtartama folyamatosan meghosszabbodik, amikor a felhasználók bejelentkeznek a WIndows Hello for Business szolgáltatással
* **MFA wam interaktív bejelentkezés során:** A WAM-on keresztüli jogkivonat-kérelem során, ha a felhasználónak az alkalmazás eléréséhez mfa-t kell tennie, a tevékenység során megújult PRT-t egy MFA-jogcím nyomtatja.
   * Ebben az esetben az MFA-jog nem frissül folyamatosan, így az MFA időtartama a címtárban beállított élettartamon alapul.
   * Ha egy korábbi PRT-t és RT-t használnak egy alkalmazás eléréséhez, a PRT és az RT lesz a hitelesítés első bizonyítéka. Egy új AT lesz szükség egy második bizonyíték és egy olvasható MFA követelés. Ez is kiad egy új PRT és RT.
* **MFA az eszköz regisztrációja során:** Ha egy rendszergazda úgy konfigurálta az eszköz beállításait az Azure [AD-ben, hogy az MFA-t az eszközök regisztrálásához követelje](device-management-azure-portal.md#configure-device-settings)meg, a felhasználónak a regisztráció befejezéséhez több-fa-szolgáltatást kell tennie. A folyamat során a felhasználó nak kiadott PRT rendelkezik a regisztráció során kapott Többfa-követelésre. Ez a funkció csak az illesztési műveletet végző felhasználóra vonatkozik, az eszközre bejelentkező többi felhasználóra nem.
   * A WAM interaktív bejelentkezéshez hasonlóan az MFA-jog nem frissül folyamatosan, így az MFA időtartama a címtárban beállított élettartamon alapul.

A Windows 10 minden hitelesítő adathoz a PRT-k particionált listáját tartja karban. Tehát van egy PRT minden Windows Hello For Business, jelszó vagy intelligens kártya. Ez a particionálás biztosítja, hogy az MFA-jogcímek a használt hitelesítő adatok alapján vannak elkülönítve, és nem keverednek a jogkivonat-kérelmek során.

## <a name="how-is-a-prt-invalidated"></a>Hogyan érvénytelenítik a PRT-t?

A PRT a következő esetekben érvénytelenítésre kerül:

* **Érvénytelen felhasználó:** Ha egy felhasználó törlődik vagy le van tiltva az Azure AD-ben, a PRT érvényteleníti, és nem használható az alkalmazások jogkivonatai beszerzésére. Ha egy törölt vagy letiltott felhasználó már korábban bejelentkezett egy eszközre, a gyorsítótárazott bejelentkezés mindaddig bejelentkezik, amíg a CloudAP nem tud az érvénytelen állapotukról. Miután a CloudAP megállapítja, hogy a felhasználó érvénytelen, blokkolja a további bejelentkezéseket. Egy érvénytelen felhasználó automatikusan levan tiltva a bejelentkezésaz új eszközök, amelyek nem rendelkeznek a hitelesítő adatok gyorsítótárazott.
* **Érvénytelen eszköz:** Ha egy eszközt törölnek vagy letiltanak az Azure AD-ben, az adott eszközön kapott PRT érvénytelenné válik, és nem használható más alkalmazások jogkivonatának beszerzésére. Ha egy felhasználó már be van jelentkezve egy érvénytelen eszközre, továbbra is megteheti. De az eszközön lévő összes jogkivonat érvénytelenné válik, és a felhasználó nem rendelkezik egyszeri bejelentkezéssel az adott eszközről származó erőforrásokhoz.
* **Jelszómódosítása**: Miután a felhasználó megváltoztatja a jelszavát, az Azure AD érvényteleníti az előző jelszóval kapott PRT-t. A jelszómódosítás azt eredményezi, hogy a felhasználó új PRT-t kap. Ez az érvénytelenítés kétféleképpen történhet:
   * Ha a felhasználó bejelentkezik a Windows ba az új jelszóval, a CloudAP elveti a régi PRT-t, és kéri az Azure AD-t, hogy adjon ki egy új PRT-t az új jelszavával. Ha a felhasználó nem rendelkezik internetkapcsolattal, az új jelszó nem érvényesíthető, a Windows megkövetelheti a felhasználótól a régi jelszavát.
   * Ha egy felhasználó a régi jelszavával jelentkezett be, vagy a Windows ba való bejelentkezés után módosította a jelszavát, a régi PRT-t használja a rendszer bármely WAM-alapú jogkivonat-kérelemhez. Ebben a forgatókönyvben a rendszer kéri, hogy a WAM-jogkivonat-kérelem során újra hitelesítse magát, és egy új PRT-t ad ki.
* **TPM-problémák**: Előfordulhat, hogy az eszköz TPM-je megingik vagy meghibásodik, ami a TPM által biztosított kulcsok elérhetetlenségéhez vezethet. Ebben az esetben az eszköz nem képes prt-t kapni vagy jogkivonatokat kérni egy meglévő PRT használatával, mivel nem tudja bizonyítani a titkosítási kulcsok birtoklását. Ennek eredményeképpen az Azure AD érvényteleníti a meglévő PRT-t. Amikor a Windows 10 hibát észlel, helyreállítási folyamatot indít az eszköz új titkosítási kulcsokkal történő újbóli regisztrálásához. A hibrid Azure-ad-csatlakozás, csakúgy, mint a kezdeti regisztráció, a helyreállítás történik csendben felhasználói beavatkozás nélkül. Az Azure AD-hez csatlakozott vagy az Azure AD regisztrált eszközök, a helyreállítást kell végrehajtani a felhasználó, aki rendszergazdai jogosultságokkal rendelkezik az eszközön. Ebben az esetben a helyreállítási folyamatot egy Windows-üzenet kezdeményezi, amely a felhasználót az eszköz sikeres helyreállítására irányítja.

## <a name="detailed-flows"></a>Részletes folyamatok

Az alábbi ábrák bemutatják az alapul szolgáló részleteket a kiadás, a megújítás és a PRT használatával egy alkalmazás hozzáférési jogkivonatának kérése. Ezenkívül ezek a lépések azt is leírják, hogy a fent említett biztonsági mechanizmusokat hogyan alkalmazzák ezek az interakciók során.

### <a name="prt-issuance-during-first-sign-in"></a>PRT-kibocsátás az első bejelentkezéskor

![PRT-kibocsátás a részletes folyamat első bejelentkezése során](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Az Azure AD-hez csatlakozott eszközök, ez a csere történik szinkron módon, hogy kiadja a PRT, mielőtt a felhasználó bejelentkezhet a Windows. A hibrid Azure AD-hez csatlakozó eszközök, a helyszíni Active Directory az elsődleges hatóság. Így a felhasználó csak arra vár, amíg beszerezhetegy TGT-t a bejelentkezéshez, míg a PRT-kibocsátás aszinkron módon történik. Ez a forgatókönyv nem vonatkozik az Azure AD regisztrált eszközök bejelentkezés nem használja az Azure AD hitelesítő adatait.

| Lépés | Leírás |
| :---: | --- |
| A | A felhasználó beírja a jelszavát a felhasználói felület en. A LogonUI átadja a hitelesítő adatokat egy hitelesítési pufferben az LSA-nak, amely viszont belsőleg továbbítja azt a CloudAP-nak. A CloudAP továbbítja ezt a kérést a CloudAP bővítménynek. |
| B | A CloudAP-bővítmény egy birodalomfelderítési kérelmet indít el a felhasználó identitásszolgáltatójának azonosítására. Ha a felhasználó bérlője rendelkezik egy összevonási szolgáltató beállításával, az Azure AD az összevonási szolgáltató Metaadat-exchange-végpontját (MEX) adja vissza. Ha nem, az Azure AD azt adja vissza, hogy a felhasználó felügyelt, jelezve, hogy a felhasználó hitelesíthető az Azure AD-vel. |
| C# | Ha a felhasználó felügyelt, CloudAP leveszi a nonce az Azure AD-ből. Ha a felhasználó össze van egyesítve, a CloudAP-bővítmény saml-jogkivonatot kér az összevonási szolgáltatótól a felhasználó hitelesítő adataival. Miután megkapta, az SAML-jogkivonat, kér egy nonce az Azure AD-től. |
| D | A CloudAP-bővítmény a hitelesítési kérelmet a felhasználó hitelesítő adataival és egy közvetítői hatókörrel készíti el, aláírja a kérelmet az Eszközkulccsal (dkpriv), és elküldi azt az Azure AD-nek. Összevont környezetben a CloudAP-bővítmény az összevonási szolgáltató által visszaadott SAML-jogkivonatot használja a felhasználó hitelesítő adatai helyett. |
| E | Az Azure AD ellenőrzi a felhasználói hitelesítő adatokat, a nonce és az eszköz aláírása, ellenőrzi, hogy az eszköz érvényes-e a bérlőben, és kiadja a titkosított PRT. A PRT-vel együtt az Azure AD egy szimmetrikus kulcsot is kiad, amelyet az Azure AD által a Transport kulccsal (tkpub) titkosított munkamenetkulcsnak neveznek. Ezenkívül a Munkamenet kulcs is be van ágyazva a PRT-be. Ez a munkamenetkulcs a PRT-vel kapcsolatos későbbi kérések igazolása (PoP) kulcsként működik. |
| F | CloudAP plugin átadja a titkosított PRT és a munkamenet kulcs CloudAP. A CloudAP kéri a TPM-et, hogy a Transport kulccsal (tkpriv) visszafejtse a munkamenetkulcsot, és a TPM saját kulcsával titkosítsa újra. A CloudAP a titkosított munkamenetkulcsot a PRT-vel együtt tárolja a gyorsítótárban. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT megújítása a későbbi bejelentkezések

![PRT megújítása a későbbi bejelentkezések](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Lépés | Leírás |
| :---: | --- |
| A | A felhasználó beírja a jelszavát a felhasználói felület en. A LogonUI átadja a hitelesítő adatokat egy hitelesítési pufferben az LSA-nak, amely viszont belsőleg továbbítja azt a CloudAP-nak. A CloudAP továbbítja ezt a kérést a CloudAP bővítménynek. |
| B | Ha a felhasználó korábban már bejelentkezett a felhasználóba, a Windows gyorsítótárazott bejelentkezést kezdeményez, és hitelesítő adatokat érvényesít a felhasználó bejelentkezéséhez. A CloudAP beépülő modul 4 óránként kezdeményezi a PRT megújítását aszinkron módon. |
| C# | A CloudAP-bővítmény egy birodalomfelderítési kérelmet indít el a felhasználó identitásszolgáltatójának azonosítására. Ha a felhasználó bérlője rendelkezik egy összevonási szolgáltató beállításával, az Azure AD az összevonási szolgáltató Metaadat-exchange-végpontját (MEX) adja vissza. Ha nem, az Azure AD azt adja vissza, hogy a felhasználó felügyelt, jelezve, hogy a felhasználó hitelesíthető az Azure AD-vel. |
| D | Ha a felhasználó össze van egyesítve, a CloudAP-bővítmény saml-jogkivonatot kér az összevonási szolgáltatótól a felhasználó hitelesítő adataival. Miután megkapta, az SAML-jogkivonat, kér egy nonce az Azure AD-től. Ha a felhasználó felügyelt, CloudAP közvetlenül lefogja a nonce az Azure AD-ből. |
| E | A CloudAP-bővítmény a hitelesítési kérelmet a felhasználó hitelesítő adataival és a meglévő PRT-vel készíti el, aláírja a kérelmet a munkamenetkulccsal, és elküldi azt az Azure AD-nek. Összevont környezetben a CloudAP-bővítmény az összevonási szolgáltató által visszaadott SAML-jogkivonatot használja a felhasználó hitelesítő adatai helyett. |
| F | Az Azure AD érvényesíti a munkamenetkulcs-aláírást a PRT-be ágyazott munkamenetkulcssal való összehasonlítással, ellenőrzi a nonce-t, és ellenőrzi, hogy az eszköz érvényes-e a bérlőben, és új PRT-t ad ki. Amint azt korábban láttuk, a PRT-hez ismét tartozik a Transport billentyűvel (tkpub) titkosított munkamenetkulcs. |
| G | CloudAP plugin átadja a titkosított PRT és a munkamenet kulcs CloudAP. A CloudAP arra kéri a TPM-et, hogy a Transport kulccsal (tkpriv) visszafejtse a munkamenetkulcsot, és a TPM saját kulcsával újra titkosítsa azt. A CloudAP a titkosított munkamenetkulcsot a PRT-vel együtt tárolja a gyorsítótárban. |

### <a name="prt-usage-during-app-token-requests"></a>PRT-használat az alkalmazástoken-kérelmek során

![PRT-használat az alkalmazástoken-kérelmek során](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Lépés | Leírás |
| :---: | --- |
| A | Egy alkalmazás (például az Outlook, a OneNote stb.) jogkivonat-kérelmet kezdeményez a WAM számára. A WAM viszont kéri az Azure AD WAM beépülő modult a jogkivonat-kérelem kiszolgálásához. |
| B | Ha az alkalmazás frissítési jogkivonata már elérhető, az Azure AD WAM beépülő modul egy hozzáférési jogkivonat kéréséhez használja. Az eszközkötés igazolása érdekében a WAM beépülő modul a munkamenetkulccsal aláírja a kérelmet. Az Azure AD érvényesíti a munkamenetkulcsot, és kiad egy hozzáférési jogkivonatot és egy új frissítési jogkivonatot az alkalmazáshoz, amelyet a Munkamenetkulcs titkosít. A WAM plugin a Cloud AP beépülő modult kéri a tokenek visszafejtésére, ami viszont a TPM-et kéri a Munkamenet kulcs használatával történő visszafejtésre, ami azt eredményezi, hogy a WAM plugin mindkét tokent megkapja. Ezután a WAM plugin csak az alkalmazás hozzáférési jogkivonatát biztosítja, míg újra titkosítja a frissítési tokent a DPAPI-val, és a saját gyorsítótárában tárolja  |
| C# |  Ha az alkalmazás frissítési jogkivonata nem érhető el, az Azure AD WAM beépülő modul a PRT-t használja egy hozzáférési jogkivonat kéréséhez. A birtoklás igazolása érdekében a WAM plugin aláírja a PRT-t tartalmazó kérést a Munkamenet-kulccsal. Az Azure AD érvényesíti a munkamenetkulcs-aláírást a PRT-be ágyazott munkamenetkulcssal való összehasonlításával, ellenőrzi, hogy az eszköz érvényes-e, és hozzáférési jogkivonatot és frissítési jogkivonatot ad ki az alkalmazásszámára. emellett az Azure AD kiadhat egy új PRT-t (frissítési ciklus alapján), amelyek mindegyike a Munkamenet kulcs által titkosítva van. |
| D | A WAM plugin a Cloud AP beépülő modult kéri a tokenek visszafejtésére, ami viszont a TPM-et kéri a Munkamenet kulcs használatával történő visszafejtésre, ami azt eredményezi, hogy a WAM plugin mindkét tokent megkapja. Ezután a WAM-bővítmény csak az alkalmazás hozzáférési jogkivonatát biztosítja, míg újra titkosítja a frissítési jogkivonatot a DPAPI-val, és a saját gyorsítótárában tárolja. WAM plugin fogja használni a frissítési token megy előre ezt az alkalmazást. Wam plugin is ad vissza az új PRT cloud AP plugin, amely érvényesíti a PRT az Azure AD frissítése előtt, hogy a saját cache. Cloud AP plugin fogja használni az új PRT megy előre. |
| E | A WAM biztosítja az újonnan kiadott hozzáférési jogkivonatot a WAM számára, amely viszont visszaadja azt a hívó alkalmazásnak|

### <a name="browser-sso-using-prt"></a>Böngésző sso prt használatával

![Böngésző sso prt használatával](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Lépés | Leírás |
| :---: | --- |
| A | A felhasználó bejelentkezik a Windows rendszerbe a hitelesítő adataikkal, hogy prt-t kapjon. Miután a felhasználó megnyitja a böngészőt, a böngésző (vagy bővítmény) betölti az URL-eket a rendszerleíró adatbázisból. |
| B | Amikor egy felhasználó megnyit egy Azure AD bejelentkezési URL-címet, a böngésző vagy bővítmény érvényesíti az URL-címet a beállításjegyzékből kapott URL-címet. Ha egyeznek, a böngésző meghívja a natív ügyfélgazda egy jogkivonat beszerzése. |
| C# | A natív ügyfélgazda ellenőrzi, hogy az URL-címek a Microsoft-identitásszolgáltatókhoz (Microsoft-fiók vagy Azure AD) tartoznak-e, kibontegy e-címről egy nonce küldött nonce-t, és felhívja a CloudAP-bővítményt, hogy prt cookie-t kapjon. |
| D | A CloudAP beépülő modul létrehozza a PRT cookie-t, bejelentkezik a TPM-hez kötött munkamenetkulccsal, és visszaküldi a natív ügyfélgazdaszámára. Mivel a cookie-t a munkamenetkulcs írja alá, nem lehet illetéktelenül illetéktelenül illetéktelenül kezelni. |
| E | A natív ügyfélgazda visszaadja ezt a PRT-cookie-t a böngészőnek, amely az x-ms-RefreshTokenCredential nevű kérelemfejléc részeként tartalmazza, és jogkivonatokat kér az Azure AD-től. |
| F | Az Azure AD érvényesíti a munkamenetkulcs-aláírást a PRT cookie-n, ellenőrzi a nonce, ellenőrzi, hogy az eszköz érvényes a bérlőben, és kiad egy azonosító jogkivonatot a weboldal és egy titkosított munkamenet-cookie a böngészőszámára. |

## <a name="next-steps"></a>További lépések

A PRT-vel kapcsolatos problémák elhárításáról a [Hibrid Azure Active Directory hoz csatlakozott Windows 10 és Windows Server 2016 eszközök hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)című témakörben olvashat bővebben.
