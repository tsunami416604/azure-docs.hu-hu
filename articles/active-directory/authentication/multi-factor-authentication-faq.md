---
title: Az Azure multi-factor Authentication – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések és válaszok az Azure multi-factor Authentication kapcsolatos.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 20f0e53b0eb31b3c1bde8bd9992f0b9d4202f021
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54433220"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication – gyakran ismételt kérdések

Ez a GYIK az Azure multi-factor Authentication és a multi-factor Authentication szolgáltatás használatával kapcsolatos gyakori kérdésekre ad választ. Azt bontani a szolgáltatással kapcsolatos kérdéseit általában számlázási modellek, felhasználói élményt, és a hibaelhárítás.

## <a name="general"></a>Általános kérdések

**K: Hogyan kezeli az Azure multi-factor Authentication kiszolgáló felhasználói adatok?**

A multi-factor Authentication kiszolgáló felhasználói adatok csak a helyszíni kiszolgálókon tárolt. A felhőben nincsenek állandó felhasználói adatok. Amikor a felhasználó kétlépéses ellenőrzést végez, a multi-factor Authentication kiszolgáló adatokat küld az Azure multi-factor Authentication hitelesítés felhőalapú szolgáltatás. Multi-factor Authentication-kiszolgáló és a felhőbeli multi-factor Authentication szolgáltatás közötti kommunikációhoz használ Secure Sockets Layer (SSL) vagy Transport Layer Security (TLS) 443-as kimenő porton keresztül.

Ha hitelesítési kérések érkeznek a felhőszolgáltatáshoz adatgyűjtés a hitelesítéshez és a használati jelentések. A kétlépéses ellenőrzés naplókban szereplő adatmezőket a következők:

* **Egyedi azonosító** (vagy felhasználó neve vagy a helyszíni multi-factor Authentication hitelesítési kiszolgáló azonosítója)
* **Első és utolsó neve** (nem kötelező)
* **E-mail-cím** (nem kötelező)
* **Telefonszám** (használatakor hanghívások vagy SMS-hitelesítés)
* **Eszköztoken** (mobilalkalmazás-hitelesítés használatakor)
* **Hitelesítési mód**
* **Hitelesítés eredménye**
* **Multi-factor Authentication-kiszolgáló neve**
* **A multi-factor Authentication-kiszolgáló IP**
* **Ügyfél IP** (ha elérhető)

Az opcionális mezők értékét a multi-factor Authentication-kiszolgálón konfigurálható.

Az ellenőrzési eredményeket (sikeres vagy elutasítási), és az a megtagadva, ha tárolja a hitelesítési adatokkal. A hitelesítés és a használati jelentésekben érhetők el az adatok.

**K: Milyen SMS rövid kódokat használnak a SMS-üzenetek küldését a felhasználók?**

Az Egyesült Államok Microsoft használja a következő rövid SMS-kódok:

   * 97671
   * 69829
   * 51789
   * 99399

A kanadai Microsoft használja a következő rövid SMS-kódok:

   * 759731 
   * 673801

A Microsoft nem garantálja konzisztens SMS-ben vagy a multi-factor Authentication Voice-alapú Rákérdezés az azonos számát. Léptethetünk, hogy a felhasználók Microsoft adhatók hozzá, vagy rövid kód azt módosításokat útvonal SMS mailek kézbesítési eredményességét javítása érdekében bármikor eltávolíthatja. A Microsoft nem támogatja a rövid kód országok mellett az Egyesült Államok és Kanada

## <a name="billing"></a>Számlázás

A legtöbb számlázási kérdésekhez választ vagy hivatkozással a [multi-factor Authentication díjszabása oldalon](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) vagy ismertető dokumentációban [beszerzése az Azure multi-factor Authentication](concept-mfa-licensing.md).

**K: Saját szervezet kell fizetnie a telefonhívások és a hitelesítéshez használt szöveges üzenetek küldéséhez?**

Nem, akkor is nem számítunk fel díjat azokért a telefonhívásokért és SMS üzeneteket küldeni a felhasználók Azure multi-factor Authentication szolgáltatás révén. Ha a hitelesítésenkénti MFA-szolgáltatót használ, minden hitelesítést, de a használt módszer a nem számítunk fel.

A felhasználók a telefonhívások és SMS-EK kapnak, a saját telefonja szolgáltatás megfelelően előfordulhat, hogy kell fizetnie.

**K: Nem a felhasználónkénti számlázási modell díja szerint számítjuk fel nekem az összes engedélyezett felhasználók, vagy csak azokat a kétlépéses ellenőrzés végrehajtott?**

A multi-factor Authentication szolgáltatás használata, függetlenül attól, hogy mindenképpen végre kell hajtani a kétlépéses ellenőrzés, hogy a hónap konfigurált felhasználók száma alapján számoljuk.

**K: Hogyan működik a multi-factor Authentication szolgáltatás számlázása?**

Amikor létrehoz egy felhasználónkénti vagy hitelesítésenkénti MFA-szolgáltató, a szervezet Azure-előfizetés elszámolási havi használat alapján. Ez a számlázási modell hasonlít az Azure virtual machines és websites használatát számlák tartoznak.

Az Azure multi-factor Authentication megvásárolt egy előfizetést, a szervezet csak azért fizet az éves licencdíját, minden felhasználóhoz. MFA-licencek és Office 365, az Azure AD Premium vagy Enterprise Mobility + Security csomagok számlázása az ezzel a módszerrel. 

További információ a lehetőségekről [beszerzése az Azure multi-factor Authentication](concept-mfa-licensing.md).

**K: Van olyan díjmentes verziója, az Azure multi-factor Authentication?**

Bizonyos esetekben igen.

A rendszergazdák az Azure multi-factor Authentication a Microsoft online szolgáltatásaihoz, például az Azure és az Office 365 felügyeleti portálokat hozzá ingyenesen az Azure MFA funkciók egy részét biztosítja. Ez az ajánlat csak a globális rendszergazdák az Azure Active Directory-példányok, amelyek nem rendelkeznek Azure MFA teljes verzióját az MFA-licencek, a csomag egyik gyermekszoftver vagy egy önálló fogyasztásalapú szolgáltató vonatkozik. Ha a rendszergazdák az ingyenes verzióját használja, és ezután vásárol egy Azure MFA teljes verzióját, majd minden globális rendszergazda emelt szintű a fizetős verzióra automatikusan.

Office 365-felhasználók a multi-factor Authentication ingyenes Office 365-szolgáltatásokhoz, ideértve az Exchange online-hoz és a SharePoint online-hoz való hozzáféréshez az Azure MFA-funkciók egy részét biztosítja. Ez az ajánlat vonatkozik, amikor az Azure Active Directory megfelelő példánya nem rendelkezik Azure MFA teljes verzióját az MFA-licencek, a csomag egyik gyermekszoftver vagy egy önálló fogyasztásalapú szolgáltató hozzárendelt Office 365-licenccel rendelkező felhasználók számára.

**K: Saját szervezet felhasználónkénti és hitelesítésenkénti használatalapú számlázási modellek között bármikor válthat?**

Ha a szervezet megvásárolja az MFA a fogyasztás alapú számlázáshoz önálló szolgáltatásként, a számlázási modellt választja MFA-szolgáltató létrehozásakor. MFA-szolgáltató létrehozása után a számlázási modell nem módosítható. Azonban törölheti az MFA szolgáltatót, és majd létrehozhat egy újat egy eltérő számlázási modellt.

MFA-szolgáltató létrehozását követően kapcsolható egy Azure Active Directory (más néven "Azure AD-bérlőhöz"). Ha az aktuális MFA-szolgáltató Azure AD-bérlő van csatolva, biztonságosan törölheti az MFA szolgáltatót és hozzon létre egyet, amely kapcsolódik az Azure AD-bérlőhöz. Másik lehetőségként, ha elegendő MFA, prémium szintű Azure AD vagy Enterprise Mobility + Security (EMS) licencet vásárolt az összes olyan felhasználó lefedésére, akiknél az MFA engedélyezett, akkor teljes egészében törölheti az MFA szolgáltatót.

Ha az MFA szolgáltató *nem* csatolva az Azure AD-bérlővel, vagy az új MFA szolgáltatót kapcsol egy másik Azure ad bérlői, felhasználói beállításokat és konfigurációs lehetőségeket vannak nem viszi át. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

További információ az MFA-szolgáltatók [Ismerkedés az Azure multi-factor Auth szolgáltatót](concept-mfa-authprovider.md).

**K: Saját szervezet válthat fogyasztásalapú számlázás és előfizetés (licenc-alapú modell) között bármikor?**

Bizonyos esetekben igen.

Ha a címtár egy *felhasználónkénti* Azure multi-factor Authentication-szolgáltatót, az MFA-licencek is hozzáadhat. Licenccel rendelkező felhasználók nem számít az a felhasználónkénti fogyasztás alapú számlázáshoz. Licenccel nem rendelkező felhasználók továbbra is engedélyezhető a multi-factor Authentication az MFA szolgáltatón keresztül. Ha vásárol, és licencek hozzárendelése a multi-factor Authentication használatára konfigurálva minden felhasználó számára, törölheti az Azure multi-factor Authentication-szolgáltatót. Ha több felhasználó licencek a későbbiekben bármikor létrehozhat egy másik felhasználónkénti MFA-szolgáltató.

Ha a címtár egy *hitelesítésenkénti* Azure multi-factor Authentication-szolgáltatót, mindig számlázzuk ki minden hitelesítés mindaddig, amíg az MFA-szolgáltató az előfizetéshez van csatolva. MFA-licencek hozzárendelése felhasználókhoz, de továbbra is kell fizetnie minden kétlépéses ellenőrzési kérelmet, a hozzárendelt-e többtényezős hitelesítés licenccel rendelkező személytől származik-e.

**K: Rendelkezik a saját szervezet használja, és az Azure multi-factor Authentication használata az identitások szinkronizálásával?**

Ha a szervezet egy fogyasztásalapú számlázási modellt használja, az Azure Active Directory, nem kötelező, de nem szükséges. Ha az MFA-szolgáltató nem kapcsolódik az Azure AD-bérlővel, helyszíni Azure multi-factor Authentication-kiszolgáló csak is telepítheti.

Az Azure Active Directory szükség a licencelési modell, mert licenceket hozzáadni az Azure AD-bérlőhöz, amikor beszerzési, és hozzárendelheti azokat a címtárban lévő felhasználó.

## <a name="manage-and-support-user-accounts"></a>Kezelheti, és támogatja a felhasználói fiókok

**K: Mit kell tudom, hogy a felhasználók a teendő, ha azok nem kap választ telefonjukra?**

A felhasználók számára, akár próbálja meg beolvasni a telefonhívást vagy SMS-hitelesítéshez 5 percen belül 5-ször rendelkezik. A Microsoft több szolgáltató hívások és az SMS-eket használ. Ha ez sem működik egy támogatási esetet kell nyitnia a Microsoft a további hibaelhárításhoz.

Ha a fenti lépések nem működnek remélhetőleg minden felhasználó konfigurálva egynél több ellenőrzési módszert. Tanácsolja nekik, hogy próbáljanak újra bejelentkeznie, ezúttal azonban válasszanak egy másik ellenőrzési módszert a bejelentkezési oldalon.

A felhasználók mutathat a [végfelhasználói – hibaelhárítási útmutató](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**K: Mit tegyek, ha az egyik nem kérhető le fiókjához?**

Visszaállíthatja a felhasználói fiók azáltal, hogy azokat újra haladhat végig a regisztrációs folyamatot. Tudjon meg többet [kezelése az Azure multi-factor Authentication a felhőben a felhasználói és eszközbeállítások](howto-mfa-userdevicesettings.md).

**K: Mit tegyek, ha egy felhasználó elveszíti által használt alkalmazásjelszók telefonon?**

Jogosulatlan hozzáférés elkerülése érdekében törölje a felhasználó alkalmazásjelszókat. Miután a felhasználó rendelkezik őket, azokat újra létrehozhatja a jelszavakat. Tudjon meg többet [kezelése az Azure multi-factor Authentication a felhőben a felhasználói és eszközbeállítások](howto-mfa-userdevicesettings.md).

**K: Mi történik, ha a felhasználó nem tud bejelentkezni a böngészőn kívüli alkalmazásokat?**

Ha a szervezet továbbra is használja a hagyományos, és [az alkalmazásjelszavak használata engedélyezett](howto-mfa-mfasettings.md#app-passwords), akkor a felhasználók nem jelentkezhet be a régebbi ügyfelek a felhasználónevüket és jelszavukat. Ehelyett szükségük [alkalmazásjelszavak beállítása](../user-help/multi-factor-authentication-end-user-app-passwords.md). A felhasználók kell törlése (delete) a bejelentkezési adatait, indítsa újra az alkalmazást, és jelentkezzen be a felhasználóneve és *alkalmazásjelszót* rendszeres jelszó helyett.

Ha a szervezet nem rendelkezik a hagyományos, nem kell engedélyezné a felhasználóknak alkalmazásjelszavakat.

> [!NOTE]
> Az ügyfelek az Office 2013 modern hitelesítés
>
> Alkalmazásjelszók csak azért szükséges, modern hitelesítést nem támogató alkalmazások esetében. Office 2013-ügyfelek támogatásához modern hitelesítési protokollok, de konfigurálni kell. Újabb Office-ügyfelek automatikusan támogatják a modern hitelesítési protokollok. További információkért lásd: a [Office 2013 modern hitelesítés nyilvános előzetes verzióra vonatkozó közlemény](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**K: A felhasználók tegyük fel, hogy nem néha kapnak a szöveges üzenetet, vagy azok válasz kétirányú szöveges üzenet, de az ellenőrzés túllépi az időkorlátot.**

Szöveges üzenetek kézbesítésének és a kétirányú SMS válaszok átvételét nem garantált, mert ellenőrizhetetlen tényező, amelyek hatással lehetnek a szolgáltatás megbízhatóságát. Ezek a tényezők közé tartozik a cél ország, a mobilszolgáltatók és a jel erőssége.

Ha a felhasználók gyakran megbízhatóan a szöveges üzenetek fogadása problémáit, mondja el neki, inkább a mobil alkalmazás vagy a telefonhívásos módszer használata. A mobilalkalmazás fogadhat értesítéseket, mind a mobil- és Wi-Fi-kapcsolatok felett. Emellett a mobilalkalmazást hozhat létre ellenőrző kódok kezelésére, akkor is, ha az eszköz rendelkezik egyáltalán nincs jel. A Microsoft Authenticator alkalmazás érhető el az [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073), és [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

Ha SMS-eket kell használnia, amikor csak lehetséges kétirányú SMS helyett az egyirányú SMS használatát javasoljuk. Egyirányú SMS megbízhatóbb, és megakadályozza, hogy felhasználói költséggel globális SMS szöveges üzenetben küldött, egy másik országból megválaszolása.

**K: Mennyi ideig kell a felhasználók előtt a rendszer túllépi az időkorlátot adja meg az ellenőrző kódot szöveges üzenetet is módosíthatja?**

Néhány esetben igen. 

Az Azure MFA-kiszolgáló 7.0-s vagy újabb egyirányú SMS a timeout beállítás által beállítás konfigurálása egy beállításkulcsot. Az MFA-felhőszolgáltatásnak a szöveges üzenetet küld, ha az MFA-kiszolgáló visszaküldi az ellenőrző kódot (vagy egyszeri jelszó). Az MFA-kiszolgáló tárolja a kódot a memóriában 300 másodpercig alapértelmezés szerint. Ha a felhasználó nem adja meg a kódot, mielőtt megfeleltek a 300 másodperc, a hitelesítés megtagadva. Az alapértelmezett időtúllépési beállításának módosításához tegye a következőket:

1. Ugrás a HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Hozzon létre egy DWORD beállításkulcsot nevű **pfsvc_pendingSmsTimeoutSeconds** és állítsa be az idő másodpercben, hogy szeretné-e egyszeri PIN-kódok tárolása az Azure MFA-kiszolgáló.

>[!TIP] 
>Ha több MFA-kiszolgálóval rendelkezik, csak az eredeti hitelesítési kérelmet az azokat feldolgozó egy tudja, hogy a felhasználónak küldött ellenőrző kód. Amikor a felhasználó a kódot, a hitelesítési kérelem érvényesítéséhez, ugyanarra a kiszolgálóra kell küldeni. Ha a kódérvényesítést küld egy másik kiszolgálóra, a rendszer megtagadja a hitelesítést. 

Kétirányú SMS az Azure MFA-kiszolgálóval beállíthatja az időkorlát az MFA felügyeleti portálon. Ha a felhasználó a megadott időkorláton belül nem válaszol az SMS, a hitelesítés megtagadva. 

Az egyirányú SMS az Azure MFA a felhőben (beleértve az AD FS-adapter vagy a hálózati házirend-kiszolgáló kiterjesztéssel) az időkorlát nem konfigurálható. Azure ad-ben tárolja az ellenőrző kód 180 másodperc. 

**K: Használható az Azure multi-factor Authentication-kiszolgáló hardvertokenek?**

Ha az Azure multi-factor Authentication-kiszolgálót használ, külső nyílt hitelesítés (OATH) időalapú, egyszer használatos jelszót (TOTP) tokenek importálása, és ezután használhatja őket a kétlépéses ellenőrzéshez.

ActiveIdentity jogkivonatokat OATH TOTP jogkivonatok importálása az Azure multi-factor Authentication-kiszolgáló és helyezze a titkos kulcsot tartalmazó CSV-fájl is használhatja. Az OATH-tokenek használhatja az Active Directory összevonási szolgáltatások (ADFS), az Internet Information Server (IIS) az űrlapalapú hitelesítés és a távoli Authentication Dial-In User Service (RADIUS), mindaddig, amíg az ügyfél rendszer elfogadhatja a felhasználói bevitel.

Az alábbi formátumok külső OATH TOTP jogkivonatok importálhatja:  

- Szimmetrikus kulcs hordozható tároló (PSKC)  
- Ha a fájl tartalmaz, sorozatszám, titkos kulcs Base-32 formátumúnak és adott idő alatt CSV  

**K: Azure multi-factor Authentication-kiszolgáló segítségével biztonságos távoli asztali szolgáltatásokat?**

Igen, de ha használja a Windows Server 2012 R2 vagy újabb csak gondoskodhat a Terminálszolgáltatások használatával távoli asztali átjáró (RD átjáró).

Biztonsági módosítások a Windows Server 2012 R2 módosítani, hogy hogyan Azure multi-factor Authentication-kiszolgáló csatlakozik-e a helyi biztonsági szervezet (LSA) biztonsági csomag a Windows Server 2012 és korábbi verziói. A Terminálszolgáltatások a Windows Server 2012 vagy korábbi verziói esetén is [egy Windows-hitelesítést az alkalmazások biztonságossá tételéhez](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). A Windows Server 2012 R2 használatakor a távoli asztali átjáróra van szükség.

**K: Hívóazonosító konfiguráltam az MFA-kiszolgáló, de a felhasználók továbbra is kaphat egy névtelen hívó multi-factor Authentication hívásokat.**

Ha a multi-factor Authentication hívásokat a telefon nyilvános hálózaton keresztül, néha keresztül halad a szolgáltató, amely nem támogatja a hívó azonosítóját. Emiatt hivó azonosítója nem garantált, annak ellenére, hogy a multi-factor Authentication rendszer mindig küld.

**K: Miért vannak a felhasználók kérni a biztonsági adatok regisztrálása?**
Több oka, hogy sikerült a rendszer kérni fogja a biztonsági adatok regisztrálása:

- A felhasználó engedélyezve van a multi-factor Authentication a rendszergazda által az Azure ad-ben, de nem rendelkezik még regisztrálva fiókjuk biztonsági adatokkal.
- A felhasználó önkiszolgáló jelszó-visszaállítás, az Azure ad-ben engedélyezve van. A biztonsági információk segítenek őket a későbbiekben új jelszót kérnek, ha azok bármikor elfelejtené.
- A felhasználó többtényezős hitelesítés a feltételes hozzáférési szabályzat és korábban még nem regisztrált multi-factor Authentication alkalmazás érhető el.
- A felhasználó regisztrál egy eszközt az Azure ad-vel (beleértve az Azure AD Join), és a szervezet többtényezős Hitelesítést követel meg az eszközök regisztrációjával kapcsolatos, de a felhasználó nem korábban regisztrálva az MFA-hoz.
- A felhasználó hoz létre Windows Hello for Business (amely többtényezős Hitelesítést igényel) Windows 10 és a korábban még nem regisztrált az MFA-hoz.
- A szervezet által létrehozott és a egy MFA regisztrációs szabályzattal, amely a telepítve van a felhasználó engedélyezve van.
- A felhasználó korábban regisztrálva a többtényezős hitelesítés, de úgy döntött, hogy mivel a rendszergazda letiltotta egy ellenőrzési módszert. A felhasználó ezért haladjon át MFA-regisztráción, újra egy új alapértelmezett ellenőrzési módszert.

## <a name="errors"></a>Hibák

**K: Mit kell felhasználók? ha azok egy "hitelesítési kérelmet nem egy aktivált fiókhoz" hibaüzenet jelenik meg mobilapp-értesítések használata**

Tanácsolja nekik, hogy az alábbi eljárás segítségével eltávolíthatja a fiókját a mobilalkalmazásból, majd adja hozzá újra:

1. Lépjen a [az Azure portal profil](https://account.activedirectory.windowsazure.com/profile/) , és jelentkezzen be munkahelyi fiókjával.
2. Válassza ki **további biztonsági ellenőrzés**.
3. Távolítsa el a meglévő fiókot a mobilalkalmazásból.
4. Kattintson a **konfigurálása**, és kövesse az utasításokat követve konfigurálja újra a mobilalkalmazásban.

**K: Mit kell felhasználók? ha azok egy 0x800434D4L hibaüzenet jelenik meg a böngészőn kívüli alkalmazásokhoz bejelentkezéskor**

A 0x800434D4L hiba akkor fordul elő, amikor megpróbál bejelentkezni egy böngészőn kívüli alkalmazásba, a helyi számítógépen, amely nem működik együtt a kétlépéses ellenőrzést igénylő fiókok telepítve.

Megoldás esetében ez a hiba, hogy rendelkezzen külön felhasználói fiókok rendszergazdai kapcsolatos és a nem rendszergazdai műveletek. Később a rendszergazdai fiókot és a nem rendszergazdai fiókban között postaládák kapcsolat, így bejelentkezhet az Outlookot a nem rendszergazdai fiók használatával. Ez a megoldás kapcsolatos további információkért tekintse meg, hogyan [lehetővé teheti a rendszergazda megnyithatja és megtekintheti a felhasználók postaládáihoz tartalmát](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>További lépések

Ha a kérdését itt nem válaszolt, hagyja a megjegyzések, az oldal alján. Vagy a segítségkéréshez néhány további lehetőség:

* Keresés a [Microsoft támogatási tudásbázisát](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) megoldások gyakori technikai problémákra.
* Keresse meg és keresse meg a technikai kérdéseket és válaszokat a Közösségtől, vagy kérje meg a saját kérdését a [Azure Active Directory-fórumok](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Ha már a régebbi PhoneFactor-ügyfelek és a kérdése van vagy segítségre van szüksége új jelszó kérése, használja a [új jelszó kérésére vonatkozó](mailto:phonefactorsupport@microsoft.com) támogatási eset nyitása mutató hivatkozást.
* Forduljon a támogatási szakember keresztül [Azure multi-factor Authentication-kiszolgáló (PhoneFactor) támogatás](https://support.microsoft.com/oas/default.aspx?prid=14947). Ha felvette velünk a kapcsolatot, esetén lehet hasznos, a lehető a problémával kapcsolatban, hozzáadhatja a lehető legtöbb információt. Információt adhat meg az oldal, ahol a hiba, a konkrét hibakód, az adott munkamenet-azonosító és a felhasználó, akik láttak a hiba azonosítója látott tartalmazza.
