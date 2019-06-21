---
title: Elsődleges frissítési jogkivonat (PRT) és az Azure AD - az Azure Active Directory
description: Mi az a szerepe, és hogyan kezelni az elsődleges frissítési jogkivonat (PRT) az Azure Active Directoryban?
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
ms.openlocfilehash: 6515f60fb67e52a7924afec2effd0c2001935e5d
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190255"
---
# <a name="what-is-a-primary-refresh-token"></a>Mi az elsődleges frissítési Token?

Egy elsődleges frissítési jogkivonat (PRT) a Windows 10, iOS és Android-eszközök Azure AD-hitelesítés fő összetevője. Egy JSON webes jogkivonat (JWT) kifejezetten kiadott Microsoft első féltől származó jogkivonat közvetítők egyszeri bejelentkezés (SSO) engedélyezése az eszközökön használható az alkalmazások között. Ebben a cikkben egy PRT kiadott, használt, és a Windows 10-es eszközök védett részletek biztosítunk.

Ez a cikk feltételezi, hogy már ismeri a különböző eszközök állapotai elérhető az Azure ad-ben és az egyszeri bejelentkezéshez használható a Windows 10. Az Azure AD-eszközökkel kapcsolatos további információkért tekintse meg a cikket [Eszközfelügyelet az Azure Active Directoryban?](overview.md)

## <a name="key-terminology-and-components"></a>Legfontosabb terminológia és összetevők

A következő Windows-összetevők kér, és a egy PRT használatával kulcsfontosságú szerepet játszanak:

* **Felhőalapú hitelesítésszolgáltató** (CloudAP): Windows bejelentkezéshez, amely ellenőrzi a Windows 10 rendszerű eszközre bejelentkező felhasználók CloudAP a modern hitelesítési szolgáltatót. CloudAP beépülő modul keretet biztosít az identitásukat szolgáltatók létrehozhatja, a Windows-hitelesítés engedélyezéséhez, hogy identitásszolgáltató hitelesítő adatok használatával.
* **Webes Account Manager** (WAM): WAM az alapértelmezett-jogkivonat közvetítői Windows 10 rendszerű eszközökön. WAM az identitásukat,-szolgáltatók is épülnek, és engedélyezze az egyszeri Bejelentkezést, hogy az identitásszolgáltató a függő alkalmazásokhoz is biztosít egy beépülő modul keretrendszer.
* **Az Azure AD CloudAP beépülő modulja**: Az Azure AD adott beépülő modul a CloudAP keretrendszeren alapuló, amely ellenőrzi a felhasználói hitelesítő adatokat az Azure AD-Windows-bejelentkezés során.
* **Az Azure AD WAM beépülő modulja**: Az Azure AD adott beépülő modul, amely lehetővé teszi az egyszeri bejelentkezés az Azure AD-hitelesítést használó alkalmazások WAM keretrendszeren alapuló.
* **Dsreg**: Egy Azure AD adott összetevő a Windows 10-ben, amely az eszköz regisztrációs folyamat összes eszköz állapotának kezeli.
* **Platformmegbízhatósági modul** (TPM): A TPM egy hardverösszetevő, egy eszköz, hardveralapú biztonsági funkciókat biztosít a felhasználó- és titkos kulcsokat a beépített. További részleteket a cikkben található [platformmegbízhatósági modul megbízható technológiai áttekintés](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Mit tartalmaz a PRT?

Egy PRT általában minden olyan Azure AD-frissítési jogkivonat található jogcímek tartalmazza. Emellett néhány eszközre vonatkozó jogcím szerepel a PRT vannak. Ezek a következők:

* **Eszközazonosító**: A felhasználót egy adott eszközhöz egy PRT jelenik meg. Az eszköz azonosítója jogcím `deviceID` meghatározza, hogy az eszköz a PRT lett kiadva a felhasználó számára. Ez a jogcím később jelenik meg a PRT keresztül kapott jogkivonatok. Az eszköz azonosítója jogcím Eszközállapot és megfelelőségi alapuló feltételes hozzáférés engedélyezését meghatározására szolgál.
* **Munkamenetkulcs**: A munkamenet-kulcs egy titkosított szimmetrikus kulcsot, a PRT részeként az Azure AD hitelesítési szolgáltatás által létrehozott. A munkamenetkulcs a koncepció igazolása birtokában funkcionál, ha egy PRT használják más alkalmazások tokenek beszerzése érdekében.

### <a name="can-i-see-whats-in-a-prt"></a>Felszámított díj a egy PRT?

Egy PRT egy Azure ad-ben, amelyek tartalma nem ismertek bármely ügyfél összetevők által küldött átlátszatlan blob. Mi található a PRT nem látható.

## <a name="how-is-a-prt-issued"></a>Hogyan jelenik meg egy PRT?

Az eszköz regisztrálása az Azure AD-alapú eszköz hitelesítési előfeltétele. Egy PRT a felhasználók számára csak a regisztrált eszközökön jelenik meg. Az eszköz regisztrálása a részletesebb bemutatásért olvassa el a [Windows Hello for Business és az Eszközregisztráció](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Eszközregisztráció, során a dsreg összetevő kriptográfiai kulcspárokhoz két készletnyi hoz létre:

* Eszközkulcs (dkpub/dkpriv)
* Átviteli kulcs (tkpub/tkpriv)

A titkos kulcsok vannak kötve az eszköz TPM-be, ha az eszköz egy érvényes és működő TPM, míg a nyilvános kulcsok az Azure AD az eszköz regisztrációs folyamat során küldött. Ezekkel a kulcsokkal az állapot ellenőrzése során PRT kérelmeket.

A Windows 10 rendszerű eszköz két esetben a felhasználói hitelesítés során a PRT jelenik meg:

* **Az Azure AD-hez csatlakoztatott** vagy **hibrid Azure AD-csatlakoztatott**: Egy PRT ki Windows a bejelentkezés során, ha egy felhasználó bejelentkezik a szervezeti hitelesítő adataikkal. Egy PRT kiadott összes Windows 10-es támogatott hitelesítő adatokkal, például jelszó és a Windows Hello for Business. Ebben a forgatókönyvben az Azure AD CloudAP beépülő modul az elsődleges szolgáltató az PRT számára.
* **Az Azure ad-ben regisztrált eszköz**: Egy PRT ki, ha egy felhasználó egy másodlagos munkahelyi fiókot hozzáadja a Windows 10-es eszközén. Felhasználókat adhat hozzá egy fiókot a Windows 10-es két különböző módon –  
   * Egy fiók keresztül hozzáadása a **a fiók használata mindenhol ezen az eszközön** kérése egy app (például Outlook) való bejelentkezés után
   * A fiók hozzáadása **beállítások** > **fiókok** > **hozzáférés munkahelyi vagy iskolai** > **Connect**

Ezekben az esetekben az Azure AD WAM beépülő modul az elsődleges hatóság a PRT azért, mert a Windows bejelentkezési nem történik az Azure AD-fiókot.

> [!NOTE]
> 3\. fél Identitásszolgáltatók a WS-Trust protokollal a Windows 10 rendszerű eszközökön PRT kiállítási támogatnia kell. Nélkül WS-Trust, PRT nem adható ki számára a felhasználók a hibrid Azure AD-hez vagy az Azure AD-hez csatlakoztatott eszközök

## <a name="what-is-the-lifetime-of-a-prt"></a>Mi az a PRT élettartama?

Ki, ha egy PRT érvényes 14 napig, és folyamatosan megújulásakor mindaddig, amíg a felhasználó aktívan használja az eszközt.  

## <a name="how-is-a-prt-used"></a>Egy PRT használatáról?

Egy PRT két fő összetevői a Windows által használt:

* **Az Azure AD CloudAP beépülő modulja**: Windows bejelentkezéshez az Azure AD CloudAP beépülő modul az Azure ad-ből a felhasználó által megadott hitelesítő adatok használatával egy PRT kérelmek. Azt is gyorsítótáraz a PRT, amikor a felhasználó nem rendelkezik internetkapcsolattal a hozzáférést a gyorsítótárazott bejelentkezés engedélyezéséhez.
* **Az Azure AD WAM beépülő modulja**: Felhasználók megpróbálják elérni az alkalmazást, az Azure AD WAM beépülő modul a Windows 10-es egyszeri bejelentkezés engedélyezése a PRT használ. Az Azure AD WAM beépülő modulja a PRT használatával kérése a jogkivonat-kérelmeket a WAM használó alkalmazások esetében a frissítés és a hozzáférési jogkivonatokban. Lehetővé teszi SSO a böngészők által a PRT injektálásra böngésző kérelmeket. A böngésző egyszeri bejelentkezés a Windows 10 támogatott a Microsoft Edge (natív) és Chrome (keresztül a Windows 10-fiókok vagy az Office Online kiterjesztésű).

## <a name="how-is-a-prt-renewed"></a>Hogyan újítható meg egy PRT?

Egy PRT meg nem újítják két különböző módszerek:

* **Az Azure AD CloudAP beépülő modulja 4 óránként**: A beépülő modul CloudAP megújítja a PRT 4 óránként Windows bejelentkezés során. Ha a felhasználó nem rendelkezik internet-kapcsolat ebben az időszakban, CloudAP beépülő modul megújítja a PRT után az eszköz csatlakozik az internethez.
* **Az Azure AD WAM beépülő modulja során alkalmazás jogkivonat-kérelmeket**: A WAM beépülő modul lehetővé teszi a beavatkozás nélküli jogkivonat-kérelmeket alkalmazások engedélyezésével egyszeri bejelentkezés a Windows 10-es eszközökön. A beépülő modul WAM megújíthatják a PRT során a jogkivonat-kérelmeket két különböző módon:
   * Alkalmazás WAM vonatkozó kérések egy hozzáférési jogkivonatot csendes módban, de nem áll rendelkezésre, amelyet az alkalmazás az nincs a frissítési jogkivonatot. Ebben az esetben WAM a PRT kérjen az alkalmazás tokent használ, és vissza egy új PRT lekéri a válaszban.
   * Alkalmazás WAM kér a hozzáférési jogkivonat, de a PRT érvénytelen, vagy az Azure AD szükséges további engedélyezési (például az Azure multi-factor Authentication). Ebben a forgatókönyvben WAM kezdeményez a felhasználó számára, hogy hitelesítse magát újra, vagy adja meg a további ellenőrzést igénylő interaktív bejelentkezés, és a egy új PRT sikeres hitelesítésre van kiadva.

### <a name="key-considerations"></a>Fő szempontok

* Egy PRT csak kiadott és megújítása natív alkalmazás hitelesítés során. Egy PRT nem újítható meg, vagy böngésző-munkamenet során ki.
* Az Azure AD tartományhoz csatlakozik, és a hibrid Azure AD-csatlakoztatott eszközök, a CloudAP beépülő modul az elsődleges hatóság egy PRT számára. Ha egy PRT megújult WAM-alapú jogkivonatkérések közben, a PRT küld vissza CloudAP beépülő modul, amely az Azure ad-vel elfogadása előtt a PRT érvényességét ellenőrzi.

## <a name="how-is-the-prt-protected"></a>Hogyan van védve a PRT?

Egy PRT lehet védelemmel ellátni, hogy az eszköz a felhasználó bejelentkezett-kötést. Az Azure AD és a Windows 10-es PRT védelem engedélyezése a következő módszerek használatával:

* **Első bejelentkezés során**: Első bejelentkezés során egy PRT adja ki a titkosítási szempontból eszközregisztráció során előállított eszközkulcs-kérések aláírás. Az eszközön érvényes és működő TPM-mel az eszköz kulcsát a TPM által védett minden olyan rosszindulatú hozzáférés megakadályozása. Egy PRT nem jelenik meg, ha a megfelelő eszköz kulcs aláírása nem ellenőrizhető.
* **Jogkivonat-kérelmeket és -megújítás során**: Egy PRT kiadásakor az Azure AD is kibocsát egy titkosított munkamenetkulcs az eszközön. Az átviteli nyilvános kulcsával (tkpub) és elküldhet az Azure AD eszközregisztrációt részeként van titkosítva. Ez a munkamenet-kulcs csak a TPM által védett átviteli titkos kulcs (tkpriv) által lehet visszafejteni. A munkamenet kulcsának minden olyan Azure ad-ben küldött kérelmeket a koncepció birtokában (POP) kulcsot.  A munkamenet-kulcsot a TPM-eszköz is védi, és nincs más operációsrendszer-összetevő elérhető-e. Jogkivonat-kérelmeket, vagy PRT megújítási kérések biztonságosan révén a TPM-eszköz a munkamenetkulcs alá van írva, és ezért ne lehessen illetéktelenül. Az Azure AD érvénytelenné válik, nem a megfelelő munkamenetkulcs által aláírt kérések az eszközről.

A biztonságos ezeket a kulcsokat, a TPM-mel, a rosszindulatú lophassa a kulcsokat, sem a PRT visszajátszani máshol, mivel a TPM nem érhető el, akkor is, ha egy támadó, az eszköz a birtokában van.  Így a TPM használatával jelentősen növeli az Azure AD-csatlakoztatás, a hibrid Azure AD-hez, biztonságát és az Azure ad-ben regisztrált eszközök hitelesítőadat-lopás elleni. A teljesítmény és megbízhatóság érdekében a TPM 2.0-val a Windows 10 az összes Azure ad-ben eszköz regisztrációs forgatókönyv esetében az ajánlott verzió.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Hogyan történik az alkalmazás-jogkivonatokról és védett böngésző cookie-kat?

**Alkalmazás-jogkivonatokról**: Amikor egy alkalmazás jogkivonat WAM keresztül, az Azure AD kibocsát egy frissítési jogkivonatot, és a egy hozzáférési jogkivonatot. WAM azonban csak az alkalmazásba a hozzáférési jogkivonatot ad vissza, és biztonságossá teszi a frissítési jogkivonat a gyorsítótárban a felhasználó adatok protection alkalmazás alkalmazásprogramozási felületet (DPAPI) kulccsal titkosításával. WAM biztonságosan használja a frissítési jogkivonat a munkamenetkulcs kérelmek aláírásával további hozzáférési jogkivonatok kiállításához. A DPAPI-kulccsal védi az Azure AD-alapú szimmetrikus kulcs magát Azure AD-ben. Ha az eszköz a felhasználói profil visszafejteni a DPAPI-kulccsal, az Azure AD a munkamenetkulcs, mely CloudAP beépülő modul kérelmek visszafejteni a TPM által titkosított DPAPI kulcsot biztosít. Ez a funkció biztosítja, hogy konzisztencia frissítési biztonsági jogkivonat biztonságossá tétele az, és elkerülhető az alkalmazásokat a saját védelmi mechanizmus megvalósításához.  

**Böngésző cookie-k**: A Windows 10 Azure ad-ben támogatja az egyszeri Bejelentkezést az Internet Explorer és Microsoft Edge böngésző natív módon, vagy a Google Chrome-ban a Windows 10-fiókok bővítményével. A biztonsági épül, nem csak a cookie-kat, de a végpontok, amelyek a cookie-kat küldik védelme érdekében. Böngésző cookie-k védett ugyanúgy, mint egy PRT van, és a cookie-k védelme a munkamenetkulcs felhasználásával.

Amikor egy felhasználó alkalmazástelepítést kezdeményez a böngésző kapcsolati, a böngésző (vagy a bővítmény) hívja meg az egy COM natív ügyfél gazdagépre. A natív ügyfél gazdagép biztosítja, hogy az oldal a megengedett tartomány egyik. A böngésző küldhet más paramétereket az natív ügyfél üzemeltet, beleértve egy egyszeri, azonban a natív ügyfél gazdagép biztosítja, az állomásnév érvényesítése. A natív ügyfél gazdagép PRT-cookie-t kér CloudAP beépülő modul, amely létrehozza és aláírja azt a munkamenetet a TPM-védelemmel ellátott kulcs. Ahogy a PRT-cookie-t a munkamenet kulcsának aláírásával rendelkezik, akkor ne lehessen illetéktelenül. A kérelem fejlécét, az eszköz van származó ellenőrzése az Azure ad a PRT cookie tartalmazza. Ha a Chrome böngészőt használ, csak a natív ügyfél gazdagép jegyzékfájlban explicit módon definiálva kiterjesztés hívhat, tetszőleges bővítmények meggátolja, hogy így ezeket a kérelmeket. Az Azure AD ellenőrzi a PRT cookie-t, miután a böngésző kiad egy munkamenetcookie-t. Ez a munkamenet-cookie egy PRT kibocsátása ugyanazon munkamenet kulcsot is tartalmazza. További kérések során a munkamenetkulcs érvényesítve lesz hatékony kötése a cookie-t az eszközön, és megakadályozza az replays területéről.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Ha egy PRT MFA-jogcím be?

Egy PRT bizonyos forgatókönyvek esetén a multi-factor authentication (MFA) jogcím kérheti le. Az MFA-alapú PRT alkalmazások jogkivonatokat kérhet használata esetén az MFA-jogcím átkerül alkalmazás jogkivonatok. Ez a funkció zökkenőmentes élményt nyújt a felhasználók megakadályozzák, hogy a minden alkalmazás, amely ezt megköveteli az MFA-hitelesítést. Egy PRT kaphat az MFA-jogcímet a következő módon:

* **Jelentkezzen be a vállalati Windows Hello**: Windows Hello for Business váltja fel a jelszavak és a kriptográfiai kulcsokat használ a erős, kétfaktoros hitelesítés. Windows Hello for Business az eszközön a felhasználó, és magát a többtényezős Hitelesítést igényel üzembe helyezni. Amikor egy felhasználó bejelentkezik az Windows Hello for Business, a felhasználó PRT lekérdezi az MFA-jogcímet. Ebben a forgatókönyvben a felhasználók bejelentkezés intelligens kártyák, ha intelligens kártyás hitelesítés hoz létre az AD FS MFA jogcím is vonatkozik.
   * Windows Hello for Business számít a multi-factor authentication, az MFA-jogcím frissül magát a PRT frissítésekor, amikor a felhasználók bejelentkeznek a vállalati WIndows Hello for Business folyamatosan kiterjeszti az MFA időtartama
* **WAM interaktív bejelentkezés során MFA**: Során egy jogkivonat kérése WAM keresztül Ha a felhasználó köteles így tenni a többtényezős hitelesítés az alkalmazás eléréséhez a PRT, amely a meg nem újítják kommunikáció során imprinted az MFA-jogcímet.
   * Ebben az esetben az MFA-jogcím nem frissül folyamatosan, így az MFA időtartama alapján a élettartamának beállítása a könyvtárhoz.
* **Eszközök regisztrációja során MFA**: Ha a rendszergazda úgy konfigurálta az Eszközbeállítások az Azure AD- [többtényezős hitelesítés regisztrálható az eszköz](device-management-azure-portal.md#configure-device-settings), a felhasználó MFA a regisztráció befejezéséhez szüksége van. Ez a folyamat során a felhasználó számára kiállított PRT van az MFA-jogcímet a regisztráció során kapott. Ez a funkció csak a nem a többi eszközre bejelentkező felhasználóknak a csatlakoztatási műveletet végrehajtó felhasználó vonatkozik.
   * Hasonlóan a WAM interaktív bejelentkezést, az MFA-jogcím nem frissül folyamatosan, így az MFA időtartama alapján a élettartamának beállítása a könyvtárhoz.

Windows 10-es PRTs particionált listája az egyes hitelesítő adatokat tárolja. Tehát van egy PRT minden, a Windows Hello for Business, jelszóval vagy intelligens kártya. Ezt a particionálása biztosítja, hogy az MFA-jogcím elkülönített alapján a hitelesítő adatokat használ, és nem vegyes során a jogkivonat-kérelmeket.

## <a name="how-is-a-prt-invalidated"></a>Hogyan egy PRT érvénytelenné válik?

Egy PRT érvénytelenné válik, a következő esetekben:

* **Érvénytelen felhasználó**: Ha a felhasználó nem található vagy le van tiltva, az Azure ad-ben, a saját PRT érvénytelenné válik, és nem használható alkalmazások tokenek beszerzése érdekében. Ha törölték, vagy letiltott felhasználó már bejelentkezett egy eszközt, mielőtt, gyorsítótárazott bejelentkezés lenne jelentkezzen őket, amíg CloudAP érvénytelen állapotuk tisztában. Miután CloudAP határozza meg, hogy a felhasználó érvénytelen, letiltja a további bejelentkezéseket. Egy érvénytelen felhasználói automatikusan blokkolva van jelentkezzen be az új eszközökre, amelyek nem rendelkeznek a gyorsítótárazott hitelesítő adataikat.
* **Érvénytelen eszköz**: Ha egy eszköz nem található vagy le van tiltva, az Azure ad-ben, az, hogy az eszközről beszerzett PRT érvénytelenné válik, és nem használható más alkalmazások tokenek beszerzése érdekében. Ha a felhasználó már bejelentkezett egy érvénytelen eszközhöz, azok továbbra is ehhez. De az eszközön lévő összes jogkivonatok érvénytelenné válnak, és a felhasználó nem rendelkezik egyszeri bejelentkezés bármely erőforrás erről az eszközről.
* **Jelszó módosítása**: Miután a felhasználó megváltoztatja a jelszavát, a korábbi jelszót kapott PRT Azure AD által érvénytelenné válik. Jelszó módosítása a felhasználó egy új PRT első eredményez. Ez érvénytelenítésére két különböző módon fordulhat elő:
   * Ha a felhasználó az új jelszóval jelentkezik be Windows, CloudAP elveti a régi PRT, és az Azure AD egy új PRT kiadását az új jelszót kér. Ha a felhasználó nem rendelkezik internetkapcsolattal, az új jelszó érvényesítése sikertelen, a Windows szükség lehet a felhasználónak meg kell adnia a régi jelszavát.
   * Ha egy felhasználó jelentkezik be a régi jelszavát vagy módosítani a jelszavát Miután bejelentkezett a Windows, a régi PRT szolgál bármely WAM-alapú jogkivonat-kérelmeket. Ebben a forgatókönyvben kéri a felhasználót, hogy hitelesítse magát újra, a WAM jogkivonatkérések közben, és a egy új PRT jelenik meg.
* **A TPM problémák**: Egyes esetekben a TPM-eszköz falter vagy meghibásodik, a TPM által védett kulcsok inaccessibility vezető. Ebben az esetben az eszköz nem képes egy PRT beolvasása, vagy nem tudja a birtokában a titkosítási kulcsok használatának egy meglévő PRT jogkivonatok kérő. Ennek eredményeképpen minden olyan meglévő PRT érvénytelenné válik az Azure AD által. Windows 10-es hibát észlel, amikor azt újra regisztrálni az eszközt az új titkosítási kulcsok egy helyreállítási folyamatot kezdeményez. A hibrid Azure Ad join, csakúgy, mint a kezdeti regisztráció a helyreállítás történik csendes felhasználói beavatkozás nélkül. Az Azure AD-hez vagy az Azure ad-ben regisztrált eszközök, a helyreállítás kell egy felhasználó, aki rendszergazdai jogosultságokkal rendelkezik az eszközön végezheti el. Ebben a forgatókönyvben egy Windows-parancssort, amely végigvezeti a felhasználót, hogy az eszköz sikeres helyreállításához a helyreállítási folyamatot kezdeményez.

## <a name="detailed-flows"></a>Részletes folyamatok

Az alábbi ábrák bemutatják a kiállító, megújítása és a egy PRT használata egy alkalmazás hozzáférési jogkivonat kérése a mögöttes részletekkel. Ezenkívül ezeket a lépéseket is ismertetik ezeket interakció során alkalmazási módja a fent említett biztonsági mechanizmusok.

### <a name="prt-issuance-during-first-sign-in"></a>PRT kiállítási első bejelentkezés során

![PRT kiállítási részletes folyamat első bejelentkezés során](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Az Azure AD-hez csatlakoztatott eszközök, szinkron módon történik, az exchange-PRT kiadására, mielőtt a felhasználó bejelentkezhet, a Windows. Hibrid Azure AD-hez csatlakoztatott eszközök, a helyszíni Active Directory az elsődleges szolgáltató. Tehát a felhasználó csak arra vár, mindaddig, amíg azok vásárolhatja meg a bejelentkezéshez TGT közben a PRT kiállítási aszinkron módon történik. Ebben a forgatókönyvben nem vonatkozik Azure ad-ben regisztrált eszközökre, a bejelentkezés nem használja az Azure AD hitelesítő adatait.

| Lépés | Leírás |
| :---: | --- |
| A | Felhasználó a bejelentkezési felhasználói felület a beírja a jelszavát. LogonUI egy hitelesítési kiegyenlítő a hitelesítő adatokat, ami viszont belső CloudAP átadja azokat az LSA továbbítja. CloudAP továbbítja a kérést a CloudAP beépülő modult. |
| B | CloudAP beépülő modul az identitásszolgáltató a felhasználó azonosítására a kezdőtartomány felderítése irányuló kérelmet kezdeményez. Ha a felhasználó bérlői egy összevonási szolgáltató telepítése, Azure ad-ben az összevonási szolgáltató metaadatok Exchange végpont (MEX) végpontja adja vissza. Ha nem, akkor az Azure AD adja vissza, hogy felügyelt felhasználóról van jelzi, hogy a felhasználó az Azure AD-hitelesítést. |
| C | Ha a felhasználó felügyelt, CloudAP az egyszeri fog kapni az Azure ad-ből. Ha a felhasználó össze van vonva, CloudAP beépülő modul kér egy SAML-jogkivonat az összevonási szolgáltató a felhasználó hitelesítő adataival. Miután kap, a SAML-jogkivonatban egy egyszeri kér Azure ad-ben. |
| D | CloudAP beépülő modul hoz létre a hitelesítési kérelmet a felhasználó hitelesítő adatait, egyszeri és a egy közvetítő hatókör, aláírja a kérelem eszközkulcs (dkpriv), és elküldi azt az Azure ad-hez. Összevont környezetben CloudAP beépülő modul használja az összevonási szolgáltató helyett a felhasználó által visszaadott SAML-jogkivonatban "hitelesítő adatok. |
| E | Az Azure AD ellenőrzi a felhasználó hitelesítő adatait, az egyszeri és eszköz aláírását, ellenőrzi, hogy az eszköz a bérlőben érvényes-e, és a titkosított PRT problémák. Az Azure AD a PRT együtt is kibocsát egy szimmetrikus kulcsot, az Azure ad-ben, az átviteli kulcs (tkpub) használatával titkosított munkamenetkulcs nevű. Emellett a munkamenetkulcs is beágyazott a PRT. A munkamenetkulcs funkcionál, a megvalósíthatósági birtokában (PoP) kulcsot az PRT a későbbi kérelmeket. |
| F | CloudAP beépülő modul a titkosított PRT és a munkamenet-kulcs CloudAP továbbítja. CloudAP a TPM-eszköz a munkamenetkulcs átviteli kulccsal (tkpriv) visszafejtése kér, és újra titkosítja a TPM saját kulcs használatával. CloudAP tárolja a titkosított munkamenetkulcs a gyorsítótárat a PRT együtt. |

### <a name="prt-renewal-in-subsequent-logons"></a>A további bejelentkezéseket PRT megújítás

![A további bejelentkezéseket PRT megújítás](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Lépés | Leírás |
| :---: | --- |
| A | Felhasználó a bejelentkezési felhasználói felület a beírja a jelszavát. LogonUI egy hitelesítési kiegyenlítő a hitelesítő adatokat, ami viszont belső CloudAP átadja azokat az LSA továbbítja. CloudAP továbbítja a kérést a CloudAP beépülő modult. |
| B | Ha a felhasználó korábban jelentkezett be a felhasználó, a Windows kezdeményezi a gyorsítótárba jelentkezzen be, és ellenőrzi a hitelesítő adatokat a felhasználó jelentkezzen be. 4 óránként, a CloudAP beépülő modul kezdeményezi PRT megújítási aszinkron módon történik. |
| C | CloudAP beépülő modul az identitásszolgáltató a felhasználó azonosítására a kezdőtartomány felderítése irányuló kérelmet kezdeményez. Ha a felhasználó bérlői egy összevonási szolgáltató telepítése, Azure ad-ben az összevonási szolgáltató metaadatok Exchange végpont (MEX) végpontja adja vissza. Ha nem, akkor az Azure AD adja vissza, hogy felügyelt felhasználóról van jelzi, hogy a felhasználó az Azure AD-hitelesítést. |
| D | Ha a felhasználó össze van vonva, CloudAP beépülő modul kér egy SAML-jogkivonat az összevonási szolgáltató a felhasználó hitelesítő adataival. Miután kap, a SAML-jogkivonatban egy egyszeri kér Azure ad-ben. Ha a felhasználó felügyelt, CloudAP közvetlenül le az egyszeri Azure ad-ben. |
| E | CloudAP beépülő modul hoz létre a hitelesítési kérelmet a felhasználó hitelesítő adatait, egyszeri és a meglévő PRT, a munkamenetkulcs a kérelemben jelentkezik, és elküldi azt az Azure ad-hez. Összevont környezetben CloudAP beépülő modul használja az összevonási szolgáltató helyett a felhasználó által visszaadott SAML-jogkivonatban "hitelesítő adatok. |
| F | Azure ad-ben a munkamenet-kulcs aláírását érvényesíti a munkamenetkulcs ágyazva az PRT összehasonlítással, az egyszeri érvényesíti, és ellenőrzi, hogy az eszköz a bérlőben érvényes, és a egy új PRT problémák. Szűkítsen az előbb, mivel a PRT újra az átviteli kulcs (tkpub) által titkosított munkamenetkulcs együtt. |
| G | CloudAP beépülő modul a titkosított PRT és a munkamenet-kulcs CloudAP továbbítja. CloudAP a TPM-eszköz a munkamenetkulcs átviteli kulccsal (tkpriv) visszafejtése kéréseket, és újra titkosítja a TPM saját kulccsal. CloudAP tárolja a titkosított munkamenetkulcs a gyorsítótárat a PRT együtt. |

### <a name="prt-usage-during-app-token-requests"></a>Alkalmazás jogkivonat-kérelmeket során PRT használat

![Alkalmazás jogkivonat-kérelmeket során PRT használat](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Lépés | Leírás |
| :---: | --- |
| A | Egy alkalmazás (például az Outlook, a OneNote stb.) a WAM token irányuló kérelmet kezdeményez. WAM, viszont kéri az Azure AD WAM beépülő modul a token kérelem kiszolgálására. |
| B | Ha az alkalmazás egy frissítési jogkivonat már elérhető, az Azure AD WAM beépülő modul használja a hozzáférési jogkivonat kérése. Eszköz kötés igazolását, WAM beépülő modul a kérelem aláírja a munkamenetkulcs. Az Azure AD ellenőrzi a munkamenetkulcs, és a egy hozzáférési jogkivonatot, és a egy új frissítési jogkivonat az alkalmazás által a munkamenet-kulcs titkosított. WAM beépülő modul a felhő-hozzáférési pont beépülő modul a jogkivonatokat, amely, viszont a TPM-eszköz a munkamenetkulcs, így mindkét a jogkivonatok lekérésének lépéseiről WAM beépülő modul használatával visszafejtéséhez kérelmeket. Ezután WAM beépülő modult biztosít csak az alkalmazáshoz, a hozzáférési jogkivonat, újratitkosítja a frissítési jogkivonat a DPAPI-t és a saját gyorsítótárban tárolja azokat  |
| C |  A frissítési jogkivonatok az alkalmazás nem érhető el, ha az Azure AD WAM beépülő modul használatával a PRT hozzáférési jogkivonat kérése. Birtokában igazolását, WAM beépülő modul aláírja a kérelmet, amely tartalmazza a PRT munkamenet a kulccsal. Azure ad-ben a munkamenet-kulcs aláírását érvényesíti a munkamenetkulcs ágyazva az PRT összehasonlítással, ellenőrzi, hogy az eszközön érvényes, és a egy hozzáférési jogkivonatot és az alkalmazás egy frissítési jogkivonatot. Emellett az Azure ad-ben kiadhatnak egy új PRT (frissítési ciklus alapján), a munkamenet kulcsának titkosítja azokat. |
| D | WAM beépülő modul a felhő-hozzáférési pont beépülő modul a jogkivonatokat, amely, viszont a TPM-eszköz a munkamenetkulcs, így mindkét a jogkivonatok lekérésének lépéseiről WAM beépülő modul használatával visszafejtéséhez kérelmeket. Ezután az WAM beépülő modul csak az alkalmazáshoz, a hozzáférési jogkivonatot biztosít, míg a újratitkosítja a frissítési jogkivonat a DPAPI-t, és a saját gyorsítótárban tárolja azokat. WAM beépülő modul a frissítési jogkivonat továbbítja az alkalmazás fogja használni. WAM beépülő modul is ad vissza az új PRT felhőalapú AP beépülő modul ellenőrzi a PRT, mielőtt frissítené azt a saját gyorsítótár az Azure AD-val. Felhő-hozzáférési pont beépülő modul a későbbiekben új PRT fogja használni. |
| E | WAM WAM, amely ezután átadja a hívó alkalmazás biztosít az újonnan kiállított hozzáférési jogkivonat|

### <a name="browser-sso-using-prt"></a>Egyszeri bejelentkezés böngésző PRT használatával

![Egyszeri bejelentkezés böngésző PRT használatával](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Lépés | Leírás |
| :---: | --- |
| A | A felhasználó bejelentkezik a Windows hitelesítő adataikkal egy PRT beolvasásához. Miután a felhasználó megnyitja a böngésző, böngésző (vagy a bővítmény) betölti az URL-címeket a beállításjegyzékből. |
| B | Amikor a felhasználó megnyit egy Azure AD bejelentkezési URL-címe, a böngészőben vagy a bővítmény érvényesíti az URL-cím olyanokra cserélni a beállításjegyzékből kapott. Ha megegyeznek, a böngészőben a natív ügyfél gazdagépet a jogkivonatok hív meg. |
| C | A natív ügyfél gazdagépet ellenőrzi, hogy az URL-címeket tartozik (a Microsoft-fiókkal vagy az Azure AD) a Microsoft identitás-szolgáltatóktól, kinyeri az URL-címről küldött egy egyszeri és a hívást indít CloudAP beépülő modul a PRT cookie-k eléréséhez. |
| D | A CloudAP beépülő modul a PRT cookie-k létrehozása, jelentkezzen be a TPM kötött munkamenetkulcs és küldi vissza a natív ügyfél gazdagép. Ahogy a cookie-t a munkamenet kulcsának aláírásával rendelkezik, akkor ne lehessen illetéktelenül. |
| E | A natív ügyfél gazdagép a böngészőben, amely magában foglalja az Azure AD-ből és a kérelem x-ms-RefreshTokenCredential jogkivonatok nevű kérelem fejléce részeként visszaküldi a PRT cookie. |
| F | Az Azure AD érvényesíti a munkamenet-kulcs aláírást a PRT cookie-k, az egyszeri érvényesíti, ellenőrzi, hogy az eszköz a bérlőben érvényes, és a egy azonosító jogkivonatot a weblap és a egy titkosított munkamenetcookie-t, a böngésző. |

## <a name="next-steps"></a>További lépések

PRT kapcsolatos problémák hibaelhárításával kapcsolatos további információkért tekintse meg a cikket [hibaelhárítás hibrid Azure Active Directoryhoz csatlakoztatott Windows 10 és Windows Server 2016 eszközök](troubleshoot-hybrid-join-windows-current.md).
