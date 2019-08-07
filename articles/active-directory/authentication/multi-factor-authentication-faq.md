---
title: Azure multi-Factor Authentication – gyakori kérdések – Azure Active Directory
description: Az Azure multi-Factor Authentication szolgáltatással kapcsolatos gyakori kérdések és válaszok.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46abe367c9047616174a1e43dffd57861e6278e8
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68811836"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Gyakori kérdések az Azure multi-Factor Authenticationről

Ez a gyakori kérdések az Azure multi-Factor Authentication és a multi-Factor Authentication szolgáltatás használatával kapcsolatos gyakori kérdésekre adnak választ. A szolgáltatással kapcsolatos kérdésekre általánosságban, számlázási modellekkel, felhasználói élményekkel és hibaelhárítással foglalkozunk.

## <a name="general"></a>Általános

**K: Hogyan kezeli az Azure multi-Factor Authentication-kiszolgáló a felhasználói adatkezelést?**

A multi-Factor Authentication kiszolgáló esetében a felhasználói adatszolgáltatások tárolása csak a helyszíni kiszolgálókon történik. A felhőben nincsenek állandó felhasználói adatok. Ha a felhasználó kétlépéses ellenőrzést hajt végre, a multi-Factor Authentication kiszolgáló adatokat küld az Azure multi-Factor Authentication Cloud Service-be a hitelesítéshez. A multi-Factor Authentication kiszolgáló és a multi-Factor Authentication felhőalapú szolgáltatás közötti kommunikáció a 443-as porton keresztül SSL (SSL) vagy Transport Layer Security (TLS) protokollt használ.

Ha hitelesítési kérelmeket küld a Cloud Service-nek, a rendszer adatokat gyűjt a hitelesítési és használati jelentésekhez. A kétlépéses ellenőrzési naplókban szereplő adatmezők a következők:

* **Egyedi azonosító** (vagy Felhasználónév vagy helyszíni multi-Factor Authentication-kiszolgáló azonosítója)
* Keresztnév **és vezetéknév** választható
* **E-mail-cím** választható
* **Telefonszám** (hanghívás vagy SMS-hitelesítés használatakor)
* **Eszköz** jogkivonata (a Mobile App Authentication használatakor)
* **Hitelesítési mód**
* **Hitelesítési eredmény**
* **Multi-Factor Authentication kiszolgáló neve**
* **Multi-Factor Authentication kiszolgáló IP-címe**
* **Ügyfél IP-címe** (ha elérhető)

A nem kötelező mezők konfigurálhatók a multi-Factor Authentication kiszolgálón.

Az ellenőrzés eredményét (sikeres vagy elutasítás) és a Megtagadás okát a hitelesítő adatként tárolja a rendszer. Ezek az adatok a hitelesítési és használati jelentésekben érhetők el.

**K: Milyen SMS rövid kódokat használ SMS-üzenetek küldésére a felhasználóknak?**

A Egyesült Államok Microsoft a következő SMS-kódokat használja:

   * 97671
   * 69829
   * 51789
   * 99399

A kanadai Microsoft a következő SMS-kódokat használja:

   * 759731 
   * 673801

A Microsoft nem garantálja a konzisztens SMS-vagy hangalapú multi-Factor Authentication-kérések kézbesítését ugyanazzal a számmal. A felhasználók érdekében a Microsoft bármikor hozzáadhat vagy eltávolíthat rövid kódokat, ahogyan az útvonal-kiigazításokat az SMS-kézbesítés javítása érdekében. A Microsoft nem támogatja a rövid kódokat a Egyesült Államok és Kanadában kívüli országok/régiók számára.

## <a name="billing"></a>Számlázás

A legtöbb számlázási kérdés megválaszolható a [multi-Factor Authentication díjszabási lapjára](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) vagy az [Azure multi-Factor Authentication](concept-mfa-licensing.md)beszerzésére vonatkozó dokumentációra hivatkozva.

**K: Felszámítanak a szervezetem a hitelesítéshez használt telefonhívások és SMS-üzenetek küldésére?**

Nem, nem számítunk fel díjat a felhasználóknak az Azure multi-Factor Authentication szolgáltatáson keresztül elküldött telefonhívásokra vagy szöveges üzenetekre. Ha hitelesítési MFA-szolgáltatót használ, akkor minden egyes hitelesítés után a használt módszerhez nem kell fizetnie.

A felhasználók a személyes telefonos szolgáltatásuk alapján felmerülhetnek a telefonhívások vagy a kapott szöveges üzenetekért.

**K: Díjköteles a felhasználónkénti számlázási modell az összes engedélyezett felhasználónál, vagy csak a kétlépéses ellenőrzést végzők?**

A számlázás a többtényezős hitelesítés használatára konfigurált felhasználók számától függ, függetlenül attól, hogy az adott hónapban kétlépéses ellenőrzést hajtottak végre.

**K: Hogyan működik a multi-Factor Authentication számlázása?**

Felhasználónkénti vagy hitelesítési MFA-szolgáltató létrehozásakor a szervezet Azure-előfizetését havi használat alapján számítjuk fel. Ez a számlázási modell hasonló ahhoz, ahogyan az Azure számláz a virtuális gépek és webhelyek használatára vonatkozóan.

Ha előfizetést vásárol az Azure multi-Factor Authentication szolgáltatáshoz, a szervezet csak az egyes felhasználókra vonatkozó éves licenc díját fizeti. Az MFA-licencek és az Office 365-, prémium szintű Azure AD-vagy Enterprise Mobility + Security-csomagok számlázása így történik. 

További információ az [Azure multi-Factor Authentication](concept-mfa-licensing.md)beszerzésének lehetőségeiről.

**K: Létezik az Azure multi-Factor Authentication ingyenes verziója?**

Bizonyos esetekben igen.

Az Azure-rendszergazdák többtényezős hitelesítése az Azure MFA szolgáltatásainak egy részhalmazát kínálja díjmentesen a Microsoft online szolgáltatásokhoz való hozzáféréshez, beleértve a [Azure Portal](https://portal.azure.com) és a [Microsoft 365 felügyeleti](https://admin.microsoft.com)központot. Ez az ajánlat csak olyan Azure Active Directory-példányokban lévő globális rendszergazdákra vonatkozik, amelyek nem rendelkeznek az Azure MFA teljes verziójával egy MFA-licenccel, egy kötegtel vagy egy önálló fogyasztáson alapuló szolgáltatóval. Ha a rendszergazdák az ingyenes verziót használják, és az Azure MFA teljes verzióját vásárolja meg, akkor az összes globális rendszergazda automatikusan megemelt a fizetős verzióra.

Az Office 365-felhasználók többtényezős hitelesítése az Azure MFA szolgáltatásainak egy részhalmazát kínálja díjmentesen az Office 365-szolgáltatásokhoz, például az Exchange Online-hoz és a SharePoint Online-hoz való hozzáféréshez. Ez az ajánlat azokra a felhasználókra vonatkozik, akiknek van hozzárendelve Office 365-licence, ha a Azure Active Directory megfelelő példánya nem rendelkezik az Azure MFA teljes verziójával egy MFA-licenccel, egy kötegtel vagy egy önálló fogyasztáson alapuló szolgáltatóval.

**K: A szervezetem bármikor válthat a felhasználónkénti és a felhasználónkénti számlázási modellek között?**

Ha a szervezete az MFA-t önálló szolgáltatásként vásárolja meg a fogyasztáson alapuló számlázást, akkor az MFA-szolgáltató létrehozásakor kiválaszthat egy számlázási modellt. Az MFA-szolgáltató létrehozása után nem módosítható a számlázási modell. 

Ha az MFA-szolgáltató nincs Azure ad-bérlőhöz csatolva, vagy az új MFA-szolgáltatót egy másik Azure ad-bérlőhöz kapcsolja, a felhasználói beállítások és a konfigurációs beállítások nem lesznek átadva. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

További információ az MFA-szolgáltatókról az [Azure multi-Factor Auth-szolgáltató első lépéseiben](concept-mfa-authprovider.md).

**K: Válthatok-e a szervezetem a fogyasztáson alapuló számlázási és előfizetések (a licenc alapú modell) között bármikor?**

Bizonyos esetekben igen.

Ha a címtár *felhasználónkénti* Azure multi-Factor Authentication-szolgáltatóval rendelkezik, MFA-licenceket adhat hozzá. A licenccel rendelkező felhasználókat nem számítjuk fel a felhasználónkénti fogyasztási számlázás. A licenccel nem rendelkező felhasználók továbbra is engedélyezhetők az MFA-szolgáltatón keresztül. Ha a többtényezős hitelesítés használatára konfigurált összes felhasználóhoz vásárol és rendel hozzá licenceket, törölheti az Azure multi-Factor Authentication-szolgáltatót. Ha a jövőben több felhasználóval rendelkezik, akkor mindig létrehozhat egy másik felhasználónkénti MFA-szolgáltatót.

Ha a címtárban egy *hitelesítéssel* ellátható Azure multi-Factor Authentication-szolgáltató van, akkor minden egyes hitelesítés esetében mindig számlázást kell fizetni, feltéve, hogy az MFA-szolgáltató az előfizetéshez van társítva. Az MFA-licenceket hozzárendelheti a felhasználókhoz, de minden kétlépéses ellenőrzési kérelem esetében továbbra is díjat számítunk fel, függetlenül attól, hogy a hozzárendelt MFA-licenccel rendelkező valakitől származik-e.

**K: Az Azure multi-Factor Authentication használatához a szervezetnek identitásokat kell használnia és szinkronizálnia?**

Ha a szervezet egy fogyasztáson alapuló számlázási modellt használ, Azure Active Directory megadása nem kötelező, de nem kötelező. Ha az MFA-szolgáltató nincs Azure AD-bérlőhöz társítva, akkor csak az Azure multi-Factor Authentication-kiszolgálót telepítheti a helyszínen.

Azure Active Directory szükséges a licencelési modellhez, mert a licenceket az Azure AD-bérlőhöz adja hozzá, amikor megvásárolja és hozzárendeli a címtárban lévő felhasználókhoz.

## <a name="manage-and-support-user-accounts"></a>Felhasználói fiókok kezelése és támogatása

**K: Mit kell mondanom a felhasználóknak, ha nem kapnak választ a telefonjára?**

A felhasználók 5 percen belül 5 alkalommal próbálnak meg telefonhívást vagy SMS-t kérni a hitelesítéshez. A Microsoft több szolgáltatót használ a hívások és SMS-üzenetek kézbesítéséhez. Ha ez nem működik, nyisson meg egy támogatási esetet a Microsofttal a további hibák megoldásához.

Ha a fenti lépések nem működnek, remélhetőleg az összes felhasználó több ellenőrzési módszert konfigurált. Tanácsolja nekik, hogy próbáljanak újra bejelentkeznie, ezúttal azonban válasszanak egy másik ellenőrzési módszert a bejelentkezési oldalon.

A felhasználókat a végfelhasználói hibaelhárítási [útmutatóhoz](../user-help/multi-factor-authentication-end-user-troubleshoot.md)irányíthatja.

**K: Mi a teendő, ha az egyik felhasználó nem tud bejutni a fiókjába?**

A felhasználó fiókját alaphelyzetbe állíthatja, ha újra el szeretné indítani a regisztrációs folyamatot. További információ a [felhasználók és eszközök beállításainak a Felhőbeli Azure multi-Factor Authentication szolgáltatással](howto-mfa-userdevicesettings.md)történő kezeléséről.

**K: Mi a teendő, ha az egyik felhasználó elveszti az alkalmazás jelszavait használó telefont?**

A jogosulatlan hozzáférés megakadályozása érdekében törölje az összes felhasználó alkalmazásának jelszavát. Miután a felhasználó rendelkezik egy helyettesítő eszközzel, újra létrehozhatja a jelszavakat. További információ a [felhasználók és eszközök beállításainak a Felhőbeli Azure multi-Factor Authentication szolgáltatással](howto-mfa-userdevicesettings.md)történő kezeléséről.

**K: Mi a teendő, ha a felhasználó nem tud bejelentkezni a böngészőn kívüli alkalmazásokba?**

Ha a szervezet továbbra is örökölt ügyfeleket használ, és [engedélyezte az alkalmazások jelszavainak használatát](howto-mfa-mfasettings.md#app-passwords), a felhasználók nem jelentkezhetnek be az örökölt ügyfelek felhasználónevével és jelszavával. Ehelyett az [alkalmazás jelszavait](../user-help/multi-factor-authentication-end-user-app-passwords.md)kell beállítania. A felhasználóknak törölnie kell a bejelentkezési adataikat, újra kell indítaniuk az alkalmazást, majd be kell jelentkezniük a felhasználónevével és az *alkalmazás jelszavával* a normál jelszó helyett.

Ha a szervezet nem rendelkezik örökölt ügyfelekkel, ne engedélyezze a felhasználóknak az alkalmazás jelszavának létrehozását.

> [!NOTE]
> Modern hitelesítés az Office 2013-ügyfelek számára
>
> Az alkalmazás jelszavai csak a modern hitelesítést nem támogató alkalmazásokhoz szükségesek. Az Office 2013-ügyfelek támogatják a modern hitelesítési protokollokat, de konfigurálni kell őket. A modern hitelesítés mostantól minden olyan ügyfél számára elérhető, amely az Office 2013 2015 márciusi vagy újabb frissítését futtatja. További információkért tekintse meg a [frissített Office 365 modern hitelesítés](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)című blogbejegyzést.

**K: A felhasználók azt mondják, hogy néha nem kapják meg a szöveges üzenetet, vagy választ adnak a kétirányú szöveges üzenetekre, de a hitelesítés időtúllépést mutat.**

A szöveges üzenetek kézbesítése és a válaszok fogadása kétirányú SMS-ben nem garantált, mert olyan nem ellenőrizhető tényezők vannak, amelyek hatással lehetnek a szolgáltatás megbízhatóságára. Ezek a tényezők közé tartoznak a rendeltetés országa/régiója, a mobiltelefon-szolgáltató és a jel erőssége.

Ha a felhasználók gyakran problémákba ütköznek a szöveges üzenetek megbízható fogadásával, kérje a Mobile App vagy a telefonhívás módszer használatát. A Mobile alkalmazás a mobil-és Wi-Fi-kapcsolatokon keresztül is fogadhat értesítéseket. Emellett a Mobile alkalmazás akkor is létrehozhat ellenőrző kódokat, ha az eszközön nincs jel. Az Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)és [Windows Phone-telefon](https://go.microsoft.com/fwlink/?Linkid=825071)rendszerekhez érhető el.

Ha szöveges üzeneteket kell használnia, javasoljuk, hogy használjon egyirányú SMS-t, nem pedig kétirányú SMS-t, ha lehetséges. Az egyirányú SMS megbízhatóbb, és megakadályozza, hogy a felhasználók egy másik országból vagy régióból küldött szöveges üzenetre válaszoljanak a globális SMS-ben.

**K: Megváltoztathatom azt az időtartamot, ameddig a felhasználóknak meg kell adniuk az ellenőrző kódot egy szöveges üzenetből a rendszer időtúllépése előtt?**

Bizonyos esetekben igen. 

Az Azure MFA Server 7.0-s vagy újabb verziójával rendelkező egyirányú SMS-ben a beállításkulcs beállításával állíthatja be az időtúllépési beállítást. Miután az MFA Cloud Service elküldi a szöveges üzenetet, a rendszer visszaadja az ellenőrző kódot (vagy egyszer használatos jelszót) az MFA-kiszolgálónak. Az MFA-kiszolgáló alapértelmezés szerint 300 másodpercig tárolja a kódot a memóriában. Ha a felhasználó nem adja meg a kódot a 300 másodperc eltelte előtt, a hitelesítés megtagadva. Az alapértelmezett időtúllépési beállítás módosításához kövesse az alábbi lépéseket:

1. Ugrás a HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Hozzon létre egy **pfsvc_pendingSmsTimeoutSeconds** nevű DWORD beállításkulcsot, és állítsa be azt az időpontot másodpercben, ameddig az Azure MFA-kiszolgálónak egyszer használatos PIN-kódot kell tárolnia.

>[!TIP] 
>Ha több MFA-kiszolgálóval rendelkezik, csak az eredeti hitelesítési kérelem feldolgozását végző felhasználó ismeri a felhasználónak továbbított ellenőrző kódot. Ha a felhasználó megadja a kódot, az érvényesítéséhez szükséges hitelesítési kérést ugyanarra a kiszolgálóra kell elküldeni. Ha a kód érvényesítését egy másik kiszolgálóra küldik, a rendszer megtagadja a hitelesítést. 

Az Azure MFA-kiszolgálóval való kétirányú SMS-ben beállíthatja az MFA-felügyeleti portál időtúllépési beállítását. Ha a felhasználók nem válaszolnak az SMS-re a megadott időtúllépési időtartamon belül, a hitelesítés megtagadva. 

Az Azure MFA-t a felhőben (beleértve a AD FS adaptert vagy a hálózati házirend-kiszolgáló bővítményt) egyirányú SMS-ek esetében nem konfigurálhatja az időtúllépési beállítást. Az Azure AD 180 másodpercig tárolja az ellenőrző kódot. 

**K: Használhatok hardveres tokeneket az Azure multi-Factor Authentication-kiszolgálóval?**

Ha Azure multi-Factor Authentication-kiszolgálót használ, akkor importálhat harmadik féltől származó nyílt hitelesítési (eskü) időalapú, egyszer használatos jelszó-(TOTP-) jogkivonatokat, majd a két lépéses ellenőrzéshez használhatja őket.

Ha a titkos kulcsot egy CSV-fájlban helyezi el, és importálja az Azure multi-Factor Authentication-kiszolgálóra, használhatja az TOTP-tokeneket ActiveIdentity jogkivonatokat. A Active Directory összevonási szolgáltatások (AD FS) (ADFS), az Internet Information Server (IIS) űrlapalapú hitelesítésével és a távoli hitelesítés betárcsázási szolgáltatásával (RADIUS) is használhat eskü-tokeneket, feltéve, hogy az ügyfélrendszer el tudja fogadni a felhasználói adatokat.

A harmadik féltől származó eskü TOTP jogkivonatok importálása a következő formátumokkal végezhető el:  

- Hordozható szimmetrikus kulcstároló (PSKC)  
- CSV, ha a fájl sorozatszámot, titkos kulcsot tartalmaz Base 32 formátumban, és egy időintervallumot  

**K: Használhatom az Azure multi-Factor Authentication-kiszolgálót a Terminálszolgáltatások biztonságossá tételéhez?**

Igen, de ha Windows Server 2012 R2 vagy újabb rendszert használ, csak Távoli asztali átjáró (RD-átjáró) használatával biztosíthat biztonságos terminálszolgáltatásokat.

A Windows Server 2012 R2 biztonsági módosításai megváltoztak, hogyan kapcsolódik az Azure multi-Factor Authentication-kiszolgáló a helyi biztonsági szervezet (LSA) biztonsági csomagjához a Windows Server 2012 és a korábbi verziókban. A Windows Server 2012-es vagy korábbi verziójú Terminálszolgáltatások esetében a Windows- [hitelesítéssel biztonságossá](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)teheti az alkalmazásokat. Ha a Windows Server 2012 R2 rendszert használja, RD-átjáróra van szüksége.

**K: Konfiguráltam a hívót az MFA-kiszolgálón, de a felhasználók továbbra is megkapják a többtényezős hitelesítési hívásokat egy névtelen hívótól.**

Ha a többtényezős hitelesítési hívásokat a nyilvános telefonvonalon keresztül helyezi át, időnként egy olyan szolgáltatón keresztül irányítják őket, amely nem támogatja a hívó AZONOSÍTÓját. Emiatt a hívó azonosítója nem garantált, még akkor is, ha a multi-Factor Authentication rendszer mindig elküldi azt.

**K: Miért kérik a felhasználókat, hogy regisztrálják a biztonsági adataikat?**
Számos oka lehet annak, hogy a felhasználóknak meg kell adniuk biztonsági adataik regisztrálását:

- A felhasználó engedélyezve lett az MFA számára az Azure AD-beli rendszergazdája számára, de még nem rendelkezik a fiókjához regisztrált biztonsági információkkal.
- A felhasználó az Azure AD-ben engedélyezte az önkiszolgáló jelszó-visszaállítást. A biztonsági információk segítségével a későbbiekben is visszaállíthatja a jelszavát, ha elfelejtik.
- A felhasználó olyan alkalmazást kapott, amely feltételes hozzáférési házirenddel rendelkezik az MFA megköveteléséhez, és korábban nem regisztrált az MFA-hoz.
- A felhasználó regisztrálja az eszközt az Azure AD-vel (beleértve az Azure AD Joint is), és a szervezete megköveteli az MFA használatát az eszközök regisztrálásához, de a felhasználó korábban még nem regisztrált az MFA-ra.
- A felhasználó a Windows Hello for businesst generálja a Windows 10 rendszerben (amely MFA-t igényel), és korábban nem regisztrált az MFA-ra.
- A szervezet létrehozta és engedélyezte a felhasználóra alkalmazott MFA regisztrációs szabályzatot.
- A felhasználó korábban már regisztrálta az MFA-t, de olyan ellenőrzési módszert választott, amelyet a rendszergazda letiltott. A felhasználónak ezért újra kell haladnia az MFA-regisztráción, hogy új alapértelmezett ellenőrzési módszert válasszon ki.

## <a name="errors"></a>Hibák

**K: Mit kell tenniük a felhasználóknak, ha "hitelesítési kérelem nem aktivált fiókhoz" hibaüzenet jelenik meg a Mobile apps értesítéseinek használatakor?**

Ezt az eljárást követve távolítsa el a fiókját a Mobile alkalmazásból, majd adja hozzá újra:

1. Lépjen a [Azure Portal profiljához](https://account.activedirectory.windowsazure.com/profile/) , és jelentkezzen be szervezeti fiókjával.
2. Válassza a **további biztonsági ellenőrzés**lehetőséget.
3. Távolítsa el a meglévő fiókot a Mobile alkalmazásból.
4. Kattintson a **Konfigurálás**elemre, majd kövesse az utasításokat a Mobile App újrakonfigurálásához.

**K: Mi a teendő, ha 0x800434D4L hibaüzenet jelenik meg a nem böngésző alkalmazásba való bejelentkezéskor?**

A 0x800434D4L hiba akkor fordul elő, ha egy helyi számítógépen telepített, nem böngészős alkalmazásba próbál bejelentkezni, amely nem működik olyan fiókokkal, amelyek kétlépéses ellenőrzést igényelnek.

Ennek a hibának a megkerülő megoldás, ha külön felhasználói fiókkal rendelkezik a rendszergazdai és a nem rendszergazdai műveletekhez. Később a rendszergazdai fiók és a nem rendszergazdai fiók között is csatolhat postaládákat, így a nem rendszergazdai fiókkal is bejelentkezhet az Outlookba. A megoldással kapcsolatos további információkért tekintse meg, hogy a [rendszergazda hogyan nyithatja meg és tekintheti meg a felhasználó postaládájának tartalmát](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>További lépések

Ha a kérdés itt nem válaszol, kérjük, hagyja meg az oldal alján található megjegyzéseket. Vagy Íme néhány további lehetőség a Súgó beszerzéséhez:

* A gyakori technikai problémák megoldásához keressen a [Microsoft ügyfélszolgálata Tudásbázisban](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) .
* Megkeresheti és böngészheti a Közösség technikai kérdéseit és válaszait, vagy megteheti a saját kérdéseit a [Azure Active Directory fórumokon](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Ha Ön örökölt PhoneFactor-ügyfél, és kérdései vannak, vagy segítségre van szüksége a jelszó alaphelyzetbe állításához, a [jelszó](mailto:phonefactorsupport@microsoft.com) -visszaállítási hivatkozás használatával nyisson meg egy támogatási esetet.
* Forduljon a támogatási szakemberekhez az [Azure multi-Factor Authentication-kiszolgáló (PhoneFactor) támogatásával](https://support.microsoft.com/oas/default.aspx?prid=14947). Amikor kapcsolatba lép velünk, hasznos lehet, ha a lehető legtöbb információt felveheti a probléma megoldására. A megadható információk közé tartozik az a lap, ahol a hiba, a megadott hibakód, a megadott munkamenet-azonosító és a hibát megtekintő felhasználó azonosítója szerepel.
