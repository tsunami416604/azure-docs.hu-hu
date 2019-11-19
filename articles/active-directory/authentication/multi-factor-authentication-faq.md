---
title: Azure Multi-Factor Authentication GYIK – Azure Active Directory
description: Az Azure Multi-Factor Authentication kapcsolatos gyakori kérdések és válaszok.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37f375b6d4284b4728b2337dc5ab5186ce22772c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167771"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Gyakran ismételt kérdések az Azure Multi-Factor Authentication

Ez a gyakori kérdések az Azure Multi-Factor Authentication és az Multi-Factor Authentication szolgáltatás használatával kapcsolatos gyakori kérdésekre adnak választ. A szolgáltatással kapcsolatos kérdésekre általánosságban, számlázási modellekkel, felhasználói élményekkel és hibaelhárítással foglalkozunk.

## <a name="general"></a>Általános kérdések

**K: hogyan kezeli az Azure Multi-Factor Authentication-kiszolgáló a felhasználói adatmennyiséget?**

A Multi-Factor Authentication-kiszolgáló a felhasználói adatszolgáltatások tárolása csak a helyszíni kiszolgálókon történik. A felhőben nincsenek állandó felhasználói adatok. Ha a felhasználó kétlépéses ellenőrzést hajt végre, Multi-Factor Authentication-kiszolgáló adatokat küld az Azure Multi-Factor Authentication Cloud Service-be a hitelesítéshez. A Multi-Factor Authentication-kiszolgáló és a Multi-Factor Authentication Cloud Service közötti kommunikáció SSL (SSL) vagy Transport Layer Security (TLS) protokollt használ a kimenő 443-as porton keresztül.

Ha hitelesítési kérelmeket küld a Cloud Service-nek, a rendszer adatokat gyűjt a hitelesítési és használati jelentésekhez. A kétlépéses ellenőrzési naplókban szereplő adatmezők a következők:

* **Egyedi azonosító** (vagy Felhasználónév vagy helyszíni multi-Factor Authentication-kiszolgáló azonosító)
* Keresztnév **és vezetéknév** (nem kötelező)
* **E-mail-cím** (nem kötelező)
* **Telefonszám** (hanghívás vagy SMS-hitelesítés használatakor)
* **Eszköz jogkivonata** (a Mobile App Authentication használatakor)
* **Hitelesítési mód**
* **Hitelesítési eredmény**
* **Multi-Factor Authentication-kiszolgáló neve**
* **Multi-Factor Authentication-kiszolgáló IP-cím**
* **Ügyfél IP-címe** (ha elérhető)

A nem kötelező mezők konfigurálhatók Multi-Factor Authentication-kiszolgálóban.

Az ellenőrzés eredményét (sikeres vagy elutasítás) és a Megtagadás okát a hitelesítő adatként tárolja a rendszer. Ezek az adatok a hitelesítési és használati jelentésekben érhetők el.

**K: milyen SMS rövid kódokat használ SMS-üzenetek küldésére a felhasználóknak?**

A Egyesült Államok Microsoft a következő SMS-kódokat használja:

   * 97671
   * 69829
   * 51789
   * 99399

A kanadai Microsoft a következő SMS-kódokat használja:

   * 759731 
   * 673801

A Microsoft nem garantálja a konzisztens SMS-és hangalapú Multi-Factor Authentication kérések kézbesítését ugyanazzal a számmal. A felhasználók érdekében a Microsoft bármikor hozzáadhat vagy eltávolíthat rövid kódokat, ahogyan az útvonal-kiigazításokat az SMS-kézbesítés javítása érdekében. A Microsoft nem támogatja a rövid kódokat a Egyesült Államok és Kanadában kívüli országok/régiók számára.

## <a name="billing"></a>Számlázás

A legtöbb számlázási kérdés megválaszolható a [multi-Factor Authentication díjszabási lapjára](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) vagy az [Azure-multi-Factor Authentication beszerzésére](concept-mfa-licensing.md)vonatkozó dokumentációra hivatkozva.

**K: a szervezetem a hitelesítéshez használt telefonhívások és SMS-üzenetek küldésére van felszámítva?**

Nem, nem számítunk fel díjat a felhasználóknak az Azure Multi-Factor Authentication-on keresztül küldött vagy szöveges üzenetekért. Ha hitelesítési MFA-szolgáltatót használ, akkor minden egyes hitelesítés után a használt módszerhez nem kell fizetnie.

A felhasználók a személyes telefonos szolgáltatásuk alapján felmerülhetnek a telefonhívások vagy a kapott szöveges üzenetekért.

**K: a felhasználónkénti számlázási modell díjköteles az összes engedélyezett felhasználónál, vagy csak a kétlépéses ellenőrzést végzők esetében?**

A számlázás a Multi-Factor Authentication használatára konfigurált felhasználók számától függ, függetlenül attól, hogy az adott hónapban kétlépéses ellenőrzést hajtottak végre.

**K: Hogyan működik Multi-Factor Authentication számlázás?**

Felhasználónkénti vagy hitelesítési MFA-szolgáltató létrehozásakor a szervezet Azure-előfizetését havi használat alapján számítjuk fel. Ez a számlázási modell hasonló ahhoz, ahogyan az Azure számláz a virtuális gépek és webhelyek használatára vonatkozóan.

Ha Azure Multi-Factor Authentication-előfizetést vásárol, a szervezet csak az egyes felhasználókra vonatkozó éves licenc díját fizeti. Az MFA-licencek és az Office 365-, prémium szintű Azure AD-vagy Enterprise Mobility + Security-csomagok számlázása így történik. 

További információ az [Azure-multi-Factor Authentication beszerzésének](concept-mfa-licensing.md)lehetőségeiről.

**K: az Azure Multi-Factor Authentication ingyenes verziója?**

Bizonyos esetekben igen.

Az Azure-rendszergazdák számára elérhető Multi-Factor Authentication az Azure MFA funkcióinak egy részét díjmentesen kínáljuk a Microsoft online szolgáltatásokhoz való hozzáféréshez, beleértve a [Azure Portal](https://portal.azure.com) és [Microsoft 365 felügyeleti központot](https://admin.microsoft.com). Ez az ajánlat csak olyan Azure Active Directory-példányokban lévő globális rendszergazdákra vonatkozik, amelyek nem rendelkeznek az Azure MFA teljes verziójával egy MFA-licenccel, egy kötegtel vagy egy önálló fogyasztáson alapuló szolgáltatóval. Ha a rendszergazdák az ingyenes verziót használják, és az Azure MFA teljes verzióját vásárolja meg, akkor az összes globális rendszergazda automatikusan megemelt a fizetős verzióra.

Az Office 365-felhasználók számára készült Multi-Factor Authentication az Azure MFA funkcióinak egy részhalmazát kínálja díjmentesen az Office 365-szolgáltatásokhoz való hozzáféréshez, beleértve az Exchange Online-t és a SharePoint Online-t. Ez az ajánlat azokra a felhasználókra vonatkozik, akiknek van hozzárendelve Office 365-licence, ha a Azure Active Directory megfelelő példánya nem rendelkezik az Azure MFA teljes verziójával egy MFA-licenccel, egy kötegtel vagy egy önálló fogyasztáson alapuló szolgáltatóval.

**K: a szervezetem bármikor válthat a felhasználónkénti és a felhasználónkénti számlázási modellek között?**

Ha a szervezete az MFA-t önálló szolgáltatásként vásárolja meg a fogyasztáson alapuló számlázást, akkor az MFA-szolgáltató létrehozásakor kiválaszthat egy számlázási modellt. Az MFA-szolgáltató létrehozása után nem módosítható a számlázási modell. 

Ha az MFA-szolgáltató *nincs Azure* ad-bérlőhöz csatolva, vagy az új MFA-szolgáltatót egy másik Azure ad-bérlőhöz kapcsolja, a felhasználói beállítások és a konfigurációs beállítások nem lesznek átadva. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

További információ az MFA-szolgáltatókról az [Azure multi-Factor Auth-szolgáltató első lépéseiben](concept-mfa-authprovider.md).

**K: használhatom a szervezetem a fogyasztáson alapuló számlázási és előfizetések (a licenc alapú modell) közötti váltást bármikor?**

Bizonyos esetekben igen.

Ha a címtár *felhasználónkénti* Azure multi-Factor Authentication szolgáltatóval rendelkezik, MFA-licenceket adhat hozzá. A licenccel rendelkező felhasználókat nem számítjuk fel a felhasználónkénti fogyasztási számlázás. A licenccel nem rendelkező felhasználók továbbra is engedélyezhetők az MFA-szolgáltatón keresztül. Ha a Multi-Factor Authentication használatára konfigurált összes felhasználóhoz vásárol és rendel hozzá licenceket, akkor törölheti az Azure Multi-Factor Authentication-szolgáltatót. Ha a jövőben több felhasználóval rendelkezik, akkor mindig létrehozhat egy másik felhasználónkénti MFA-szolgáltatót.

Ha a címtárban van egy hitelesítési Azure Multi-Factor Authentication *-* szolgáltató, akkor minden egyes hitelesítés esetében mindig számlázást kell fizetni, feltéve, hogy az MFA-szolgáltató az előfizetéshez van társítva. Az MFA-licenceket hozzárendelheti a felhasználókhoz, de minden kétlépéses ellenőrzési kérelem esetében továbbra is díjat számítunk fel, függetlenül attól, hogy a hozzárendelt MFA-licenccel rendelkező valakitől származik-e.

**K: a szervezetem identitások használatát és szinkronizálását kell használnia az Azure Multi-Factor Authentication használatához?**

Ha a szervezet egy fogyasztáson alapuló számlázási modellt használ, Azure Active Directory megadása nem kötelező, de nem kötelező. Ha az MFA-szolgáltató nincs Azure AD-bérlőhöz társítva, akkor csak a helyszíni Azure Multi-Factor Authentication-kiszolgáló helyezhető üzembe.

Azure Active Directory szükséges a licencelési modellhez, mert a licenceket az Azure AD-bérlőhöz adja hozzá, amikor megvásárolja és hozzárendeli a címtárban lévő felhasználókhoz.

## <a name="manage-and-support-user-accounts"></a>Felhasználói fiókok kezelése és támogatása

**K: mit kell mondanom a felhasználóknak, ha nem kapnak választ a telefonjára?**

A felhasználók 5 percen belül 5 alkalommal próbálnak meg telefonhívást vagy SMS-t kérni a hitelesítéshez. A Microsoft több szolgáltatót használ a hívások és SMS-üzenetek kézbesítéséhez. Ha ez nem működik, nyisson meg egy támogatási esetet a Microsofttal a további hibák megoldásához.

Ha a fenti lépések nem működnek, remélhetőleg az összes felhasználó több ellenőrzési módszert konfigurált. Tanácsolja nekik, hogy próbáljanak újra bejelentkeznie, ezúttal azonban válasszanak egy másik ellenőrzési módszert a bejelentkezési oldalon.

A felhasználókat a [végfelhasználói hibaelhárítási útmutatóhoz](../user-help/multi-factor-authentication-end-user-troubleshoot.md)irányíthatja.

**K: mit tegyek, ha az egyik felhasználó nem tud bejelentkezni a fiókjába?**

A felhasználó fiókját alaphelyzetbe állíthatja, ha újra el szeretné indítani a regisztrációs folyamatot. További információ a [felhasználók és eszközök beállításainak a Felhőbeli Azure-multi-Factor Authentication történő kezeléséről](howto-mfa-userdevicesettings.md).

**K: mi a teendő, ha az egyik felhasználó elveszti az alkalmazás jelszavait használó telefont?**

A jogosulatlan hozzáférés megakadályozása érdekében törölje az összes felhasználó alkalmazásának jelszavát. Miután a felhasználó rendelkezik egy helyettesítő eszközzel, újra létrehozhatja a jelszavakat. További információ a [felhasználók és eszközök beállításainak a Felhőbeli Azure-multi-Factor Authentication történő kezeléséről](howto-mfa-userdevicesettings.md).

**K: mi történik, ha a felhasználó nem tud bejelentkezni a nem böngésző alkalmazásba?**

Ha a szervezet továbbra is örökölt ügyfeleket használ, és [engedélyezte az alkalmazások jelszavainak használatát](howto-mfa-mfasettings.md#app-passwords), a felhasználók nem jelentkezhetnek be az örökölt ügyfelek felhasználónevével és jelszavával. Ehelyett az [alkalmazás jelszavait kell beállítania](../user-help/multi-factor-authentication-end-user-app-passwords.md). A felhasználóknak törölnie kell a bejelentkezési adataikat, újra kell indítaniuk az alkalmazást, majd be kell jelentkezniük a felhasználónevével és az *alkalmazás jelszavával* a normál jelszó helyett.

Ha a szervezet nem rendelkezik örökölt ügyfelekkel, ne engedélyezze a felhasználóknak az alkalmazás jelszavának létrehozását.

> [!NOTE]
> Modern hitelesítés az Office 2013-ügyfelek számára
>
> Az alkalmazás jelszavai csak a modern hitelesítést nem támogató alkalmazásokhoz szükségesek. Az Office 2013-ügyfelek támogatják a modern hitelesítési protokollokat, de konfigurálni kell őket. A modern hitelesítés mostantól minden olyan ügyfél számára elérhető, amely az Office 2013 2015 márciusi vagy újabb frissítését futtatja. További információkért tekintse meg a [frissített Office 365 modern hitelesítés](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)című blogbejegyzést.

**K: a felhasználók azt mondják, hogy néha nem kapják meg a szöveges üzenetet, vagy a hitelesítés időtúllépést mutat.**

Az SMS-üzenetek kézbesítése nem garantált, mert nem ellenőrizhető tényezők befolyásolják a szolgáltatás megbízhatóságát. Ezek a tényezők közé tartoznak a rendeltetés országa/régiója, a mobiltelefon-szolgáltató és a jel erőssége.

Ha a felhasználók gyakran problémákba ütköznek a szöveges üzenetek megbízható fogadásával, kérje a Mobile App vagy a telefonhívás módszer használatát. A Mobile alkalmazás a mobil-és Wi-Fi-kapcsolatokon keresztül is fogadhat értesítéseket. Emellett a Mobile alkalmazás akkor is létrehozhat ellenőrző kódokat, ha az eszközön nincs jel. Az Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)és [Windows Phone-telefon](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)rendszerekhez érhető el.

**K: Megváltoztathatom azt az időtartamot, ameddig a felhasználóknak meg kell adniuk az ellenőrző kódot egy szöveges üzenetből a rendszer időtúllépése előtt?**

Bizonyos esetekben igen. 

Az Azure MFA Server 7.0-s vagy újabb verziójával rendelkező egyirányú SMS-ben a beállításkulcs beállításával állíthatja be az időtúllépési beállítást. Miután az MFA Cloud Service elküldi a szöveges üzenetet, a rendszer visszaadja az ellenőrző kódot (vagy egyszer használatos jelszót) az MFA-kiszolgálónak. Az MFA-kiszolgáló alapértelmezés szerint 300 másodpercig tárolja a kódot a memóriában. Ha a felhasználó nem adja meg a kódot a 300 másodperc eltelte előtt, a hitelesítés megtagadva. Az alapértelmezett időtúllépési beállítás módosításához kövesse az alábbi lépéseket:

1. Ugrás a HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Hozzon létre egy **pfsvc_pendingSmsTimeoutSeconds** nevű DWORD beállításkulcsot, és állítsa be azt az időpontot másodpercben, ameddig az Azure MFA-kiszolgálónak egyszer használatos PIN-kódot kell tárolnia.

>[!TIP] 
>Ha több MFA-kiszolgálóval rendelkezik, csak az eredeti hitelesítési kérelem feldolgozását végző felhasználó ismeri a felhasználónak továbbított ellenőrző kódot. Ha a felhasználó megadja a kódot, az érvényesítéséhez szükséges hitelesítési kérést ugyanarra a kiszolgálóra kell elküldeni. Ha a kód érvényesítését egy másik kiszolgálóra küldik, a rendszer megtagadja a hitelesítést. 

Ha a felhasználók nem válaszolnak az SMS-re a megadott időtúllépési időtartamon belül, a hitelesítés megtagadva. 

Az Azure MFA-t a felhőben (beleértve a AD FS adaptert vagy a hálózati házirend-kiszolgáló bővítményt) egyirányú SMS-ek esetében nem konfigurálhatja az időtúllépési beállítást. Az Azure AD 180 másodpercig tárolja az ellenőrző kódot. 

**K: használhatok hardveres jogkivonatokat az Azure Multi-Factor Authentication-kiszolgáló?**

Ha az Azure Multi-Factor Authentication-kiszolgáló-t használja, akkor importálhat harmadik féltől származó nyílt hitelesítésű (ESKÜs) időalapú, egyszeri jelszavas (TOTP) jogkivonatokat, majd a két lépéses ellenőrzéshez használhatja őket.

Ha a titkos kulcsot egy CSV-fájlba helyezi, és az Azure Multi-Factor Authentication-kiszolgálóba importálja, használhatja a ActiveIdentity-tokeneket, amelyek ESKÜt TOTP jogkivonatokat használnak. A Active Directory összevonási szolgáltatások (AD FS) (ADFS), az Internet Information Server (IIS) űrlapalapú hitelesítés és a Remote Authentication Dial-In User Service (RADIUS) használatával is használhat eskü-tokeneket, feltéve, hogy az ügyfélrendszer el tudja fogadni a felhasználói adatokat.

A harmadik féltől származó eskü TOTP jogkivonatok importálása a következő formátumokkal végezhető el:  

- Hordozható szimmetrikus kulcstároló (PSKC)  
- CSV, ha a fájl sorozatszámot, titkos kulcsot tartalmaz Base 32 formátumban, és egy időintervallumot  

**K: használhatom az Azure Multi-Factor Authentication-kiszolgáló szolgáltatást a Terminálszolgáltatások biztonságossá tételéhez?**

Igen, de ha Windows Server 2012 R2 vagy újabb rendszert használ, csak Távoli asztali átjáró (RD-átjáró) használatával biztosíthat biztonságos terminálszolgáltatásokat.

A Windows Server 2012 R2 biztonsági módosításai megváltoztak, hogyan kapcsolódik az Azure Multi-Factor Authentication-kiszolgáló a helyi biztonsági szervezet (LSA) biztonsági csomagjához a Windows Server 2012 és a korábbi verziókban. A Windows Server 2012-es vagy korábbi verziójú Terminálszolgáltatások esetében a Windows- [hitelesítéssel biztonságossá](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)teheti az alkalmazásokat. Ha a Windows Server 2012 R2 rendszert használja, RD-átjáróra van szüksége.

**K: konfiguráltam a hívót az MFA-kiszolgálón, de a felhasználók továbbra is Multi-Factor Authentication hívásokat fogadnak egy névtelen hívótól.**

Ha Multi-Factor Authentication hívásokat a nyilvános telefonos hálózaton keresztül helyezi át, időnként olyan szolgáltatón keresztül irányítja őket, amely nem támogatja a hívó AZONOSÍTÓját. Emiatt a hívó azonosítója nem garantált, noha a Multi-Factor Authentication rendszer mindig elküldi.

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

**K: mit kell tenniük a felhasználóknak, ha "hitelesítési kérelem nem aktivált fiókhoz" hibaüzenet jelenik meg a Mobile apps értesítéseinek használatakor?**

Ezt az eljárást követve távolítsa el a fiókját a Mobile alkalmazásból, majd adja hozzá újra:

1. Lépjen a [Azure Portal profiljához](https://account.activedirectory.windowsazure.com/profile/) , és jelentkezzen be szervezeti fiókjával.
2. Válassza a **további biztonsági ellenőrzés**lehetőséget.
3. Távolítsa el a meglévő fiókot a Mobile alkalmazásból.
4. Kattintson a **Konfigurálás**elemre, majd kövesse az utasításokat a Mobile App újrakonfigurálásához.

**K: mi a teendő, ha 0x800434D4L hibaüzenet jelenik meg, amikor egy nem böngésző alkalmazásba jelentkezik be?**

A 0x800434D4L hiba akkor fordul elő, ha egy helyi számítógépen telepített, nem böngészős alkalmazásba próbál bejelentkezni, amely nem működik olyan fiókokkal, amelyek kétlépéses ellenőrzést igényelnek.

Ennek a hibának a megkerülő megoldás, ha külön felhasználói fiókkal rendelkezik a rendszergazdai és a nem rendszergazdai műveletekhez. Később a rendszergazdai fiók és a nem rendszergazdai fiók között is csatolhat postaládákat, így a nem rendszergazdai fiókkal is bejelentkezhet az Outlookba. A megoldással kapcsolatos további információkért tekintse meg, hogy a [rendszergazda hogyan nyithatja meg és tekintheti meg a felhasználó postaládájának tartalmát](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Következő lépések

Ha a kérdés itt nem válaszol, kérjük, hagyja meg az oldal alján található megjegyzéseket. Vagy Íme néhány további lehetőség a Súgó beszerzéséhez:

* A gyakori technikai problémák megoldásához keressen a [Microsoft ügyfélszolgálata Tudásbázisban](https://support.microsoft.com) .
* Megkeresheti és böngészheti a Közösség technikai kérdéseit és válaszait, vagy megteheti a saját kérdéseit a [Azure Active Directory fórumokon](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Ha Ön örökölt PhoneFactor-ügyfél, és kérdései vannak, vagy segítségre van szüksége a jelszó [alaphelyzetbe](mailto:phonefactorsupport@microsoft.com) állításához, a jelszó-visszaállítási hivatkozás használatával nyisson meg egy támogatási esetet.
* Forduljon a támogatási szakemberekhez az [Azure multi-Factor Authentication-kiszolgáló (PhoneFactor) támogatásával](https://support.microsoft.com/oas/default.aspx?prid=14947). Amikor kapcsolatba lép velünk, hasznos lehet, ha a lehető legtöbb információt felveheti a probléma megoldására. A megadható információk közé tartozik az a lap, ahol a hiba, a megadott hibakód, a megadott munkamenet-azonosító és a hibát megtekintő felhasználó azonosítója szerepel.
