---
title: "Az Azure többtényezős hitelesítés – gyakori kérdések |} Microsoft Docs"
description: "Gyakran ismételt kérdések és válaszok Azure multi-factor Authentication kapcsolódik. A multi-factor Authentication a módszer, amely több, mint egy felhasználónevet és jelszót igényel a felhasználók személyazonossága ellenőrzésére. A biztonság a felhasználói bejelentkezés és a tranzakciók további réteget biztosít."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 042035c89d466083659176ac49fc1b470244ef61
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure multi-factor Authentication kapcsolatos gyakori kérdések
Ez a GYIK Azure multi-factor Authentication és a multi-factor Authentication szolgáltatással kapcsolatos gyakori kérdésekre ad választ. Azt bontani a szolgáltatással kapcsolatos kérdésekre általában modellek, felhasználói élményt, számlázási és hibaelhárítást.

## <a name="general"></a>Általános kérdések
**K: hogyan Azure multi-factor Authentication kiszolgáló felhasználói adatok kezeléséhez?**

Multi-factor Authentication kiszolgáló felhasználói adatok csak a helyszíni kiszolgálókon tárolja. A felhőben nincsenek állandó felhasználói adatok. Amikor a felhasználó a kétlépéses ellenőrzést hajt végre, a multi-factor Authentication kiszolgáló adatokat küld az Azure multi-factor Authentication hitelesítés felhőalapú szolgáltatás. A multi-factor Authentication kiszolgáló és a multi-factor Authentication felhőszolgáltatás közötti kommunikáció használja a Secure Sockets Layer (SSL) vagy Transport Layer Security (TLS) 443-as kimenő porton keresztül.

Amikor hitelesítési kérelmek küldenek a felhőszolgáltatásba, hitelesítés és használati adatok gyűjtése a jelentés. Kétlépéses ellenőrzés naplók szereplő adatok mezők a következők:

* **Egyedi azonosító** (vagy felhasználó neve vagy a helyi multi-factor Authentication hitelesítési kiszolgáló azonosítója)
* **Első és utolsó neve** (nem kötelező)
* **E-mail cím** (nem kötelező)
* **Telefonszám** (használatakor hang hívás vagy SMS-hitelesítés)
* **Eszköz jogkivonatát** (mobilalkalmazás-hitelesítés használatakor)
* **Hitelesítési módszer**
* **Hitelesítés eredménye**
* **A multi-factor Authentication kiszolgáló neve**
* **A multi-factor Authentication kiszolgáló IP**
* **Ügyfél IP** (ha elérhető)

A választható mezőket a multi-factor Authentication kiszolgáló beállítható.

Az ellenőrzés eredménye (sikeres vagy megtagadását), és a ok miatt megtagadva, ha a hitelesítési adatokat tárolja. A hitelesítés és használati jelentések érhetők el az adatok.

## <a name="billing"></a>Számlázás
A legtöbb számlázási kérdésekhez vagy hivatkozással is válaszolni a [multi-factor Authentication árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) vagy ismertető dokumentációban [beolvasásával Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md).

**K: van szó, a telefonhívások és a hitelesítéshez használt szöveges üzeneteket küld a szerveztem?**

Nem, akkor nem felszámított egyes telefonhívásokat elhelyezett vagy szöveges keresztül Azure multi-factor Authentication felhasználóknak küldött üzeneteket. A hitelesítés MFA-szolgáltatóra használatakor minden hitelesítést, de a használt módszer nem kell fizetni.

Előfordulhat, hogy a felhasználók a telefonhívásokat vagy szöveges üzeneteinek kapnak, a saját telefonja szolgáltatásában alapján számítjuk fel.

**K: az egyes felhasználók számlázási modellt kérek a díjat minden engedélyezett felhasználó, és csak az adott végzett a kétlépéses ellenőrzést?**

Számlázási függetlenül attól, hogy azok végre kétlépéses ellenőrzés az adott hónapban a multi-factor Authentication használatára konfigurált felhasználók számát alapul.

**K: hogyan működik a multi-factor Authentication számlázási?**

Amikor létrehoz egy felhasználói vagy a hitelesítés MFA-szolgáltatóra, a szervezet Azure-előfizetésre lesz számlázva, havonta-használata alapján. A számlázási modellt az Azure váltók az a virtuális gépek és a webhelyek hasonlít.

Az Azure multi-factor Authentication megvásárolt egy előfizetést, a szervezet csak fizet az éves licenc díj minden felhasználó számára. MFA licencek és Office 365, Azure AD Premium, vagy vállalati mobilitási + biztonsági csomagok számlázása ily módon. 

További információ a rendelkezésére álló lehetőségekről [beolvasásával Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md).

**K: van egy Azure multi-factor Authentication ingyenes verzióját?**

Bizonyos esetekben igen.

Azure-rendszergazdák a többtényezős hitelesítést biztosít az a Microsoft online-szolgáltatásokhoz való hozzáférés, beleértve az Azure és az Office 365 felügyeleti portálon ingyenesen Azure MFA-funkciók egy részéhez. Ez az ajánlat csak a globális rendszergazdák az Azure Active Directory-példányokat, amelyek nem rendelkeznek a teljes változatát, az Azure MFA egy MFA-licencet, a csomag egyik gyermekszoftver vagy egy önálló fogyasztás alapján szolgáltató vonatkozik. Ha a rendszergazdák ingyenes verzióját használja, és ezután vásárol Azure MFA teljes verzióját, majd az összes globális rendszergazda emelt szintű a fizetős verzióra automatikusan.

Office 365-felhasználók a többtényezős hitelesítést biztosít az Office 365-szolgáltatásokhoz, beleértve az Exchange Online és SharePoint online-hoz való hozzáféréshez ingyenesen Azure MFA-funkciók egy részéhez. Ez az ajánlat vonatkozik, akik rendelkeznek egy Office 365-licenccel, amikor az Azure Active Directory megfelelő példány nincs egy MFA-licencet, a csomag egyik gyermekszoftver vagy egy önálló fogyasztás alapján szolgáltató az Azure MFA teljes verzióját.

**A szervezet felhasználói és hitelesítési fogyasztás számlázási modell bármikor közötti váltáshoz k:?**

Ha a szervezete vásárolja MFA önálló szolgáltatásként fogyasztás alapján történő számlázáshoz, a számlázási modellt választja az MFA-szolgáltatóra létrehozásakor. A számlázási modellt az MFA-szolgáltatóra létrehozása után nem módosítható. Azonban az MFA-szolgáltatóra töröl, és ezután hozzon létre egy-egy másik számlázási modellt.

Az MFA-szolgáltatóra létrehozásakor az Azure Active Directory-alapú (más néven "Azure AD-bérlő") lehet társítani. Ha az aktuális többtényezős hitelesítési szolgáltató az Azure AD-bérlő kapcsolódik, biztonságosan törölje az MFA-szolgáltató és hozzon létre egyet, amely csatolva van a azonos Azure AD-bérlő. Másik lehetőségként, ha elegendő MFA, prémium szintű Azure AD vagy Enterprise Mobility + Security (EMS) licencet vásárolt az összes olyan felhasználó lefedésére, akiknél az MFA engedélyezett, akkor teljes egészében törölheti az MFA szolgáltatót.

Ha a többtényezős hitelesítési szolgáltató *nem* csatolva az Azure AD-bérlő vagy az új MFA-szolgáltatóra csatolunk egy másik Azure ad bérlői, felhasználói beállítások és konfigurációs lehetőség nem kell másolnia. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

További tudnivalók a többtényezős hitelesítési szolgáltatók [Ismerkedés az Azure multi-factor Auth Provider a](multi-factor-authentication-get-started-auth-provider.md).

**K: a saját szervezetem válthat fogyasztás alapján számlázási és előfizetés (licenc-alapú modell) bármikor?**

Bizonyos esetekben igen.

Ha a címtár egy *felhasználói* Azure többtényezős hitelesítési szolgáltató, MFA-licencet is hozzáadhat. -Licenccel rendelkező felhasználók a felhasználói fogyasztás alapján történő számlázáshoz a nem számít. Licenc nélküli felhasználók továbbra is engedélyezhető az MFA szolgáltatásra az MFA-szolgáltató használatával. Ha a beszerzési és licencek hozzárendelése a többtényezős hitelesítés használatára konfigurált összes felhasználója számára, törölheti az Azure többtényezős hitelesítési szolgáltató. Bármikor létrehozhat egy másik felhasználói MFA-szolgáltatóra, ha a felhasználók száma meghaladja a licenceket a jövőben van.

Ha a címtár egy *hitelesítési* Azure multi-factor Authentication szolgáltatót, hogy mindig számlázása minden hitelesítést mindaddig, amíg az MFA-szolgáltató az előfizetéshez kapcsolódó. Többtényezős hitelesítés licenceket rendelhetnek a felhasználókhoz, de meg fogjuk továbbra is számlázni kétlépéses ellenőrzés kérelmek, hogy származik, valaki-e egy multi-factor Authentication licenccel rendelkező.

**K: a szervezet rendelkezik használja, és szinkronizálja az Azure többtényezős hitelesítés használata az identitások?**

Ha a szervezet fogyasztás alapján számlázási modellt használ, Azure Active Directory, nem kötelező, de nem szükséges. Ha a többtényezős hitelesítési szolgáltató nem kapcsolódik az Azure AD-bérlő, Azure multi-factor Authentication kiszolgáló vagy az Azure multi-factor Authentication SDK-t a helyszíni csak telepítheti.

Az Azure Active Directory kell adni a licenc-modell, mert a licencek kerülnek az Azure AD-bérlő beszerzési, és rendeljen hozzájuk a felhasználók számára a címtárban.

## <a name="manage-and-support-user-accounts"></a>Kezelésére, és támogatja a felhasználói fiókok

**K: Mit mondjak a teendő, ha nem kap választ a telefont, vagy nem rendelkezik a telefont, a felhasználók?**

A felhasználók remélhetőleg egynél több ellenőrzési módszer konfigurálva. Tanácsolja nekik, hogy próbáljanak újra bejelentkeznie, ezúttal azonban válasszanak egy másik ellenőrzési módszert a bejelentkezési oldalon.

A felhasználók mutathat a [végfelhasználói hibaelhárítási útmutató](./end-user/multi-factor-authentication-end-user-troubleshoot.md).


**K: Mit tegyek, ha a felhasználók közül nem olvasható be fiókjukban?**

Visszaállíthatja a felhasználói fiók azáltal, hogy azokat a regisztrációs folyamatot, nyissa meg újra. További információ [Azure multi-factor Authentication a felhőben a felhasználó-eszköz beállításainak kezelése](multi-factor-authentication-manage-users-and-devices.md).

**K: Mit tegyek, ha a felhasználók egyik elveszti a telefon által használt alkalmazásjelszók?**

Jogosulatlan hozzáférés elkerülése érdekében törölje a felhasználó alkalmazásjelszókat. Miután a felhasználó rendelkezik egy helyettesítő eszközt, ezek a jelszavak hozhatja létre. További információ [Azure multi-factor Authentication a felhőben a felhasználó-eszköz beállításainak kezelése](multi-factor-authentication-manage-users-and-devices.md).

**K: Mit tegyek, ha a felhasználó nem jelentkezhet be böngészőn kívüli alkalmazásokat?**

Ha a szervezet továbbra is használja a hagyományos, és [alkalmazásjelszók használatával engedélyezett](multi-factor-authentication-whats-next.md#app-passwords), akkor a felhasználók nem jelentkezhetnek be a régi ügyfelek, a felhasználónév és jelszó. Ehelyett szükségük [állítson be alkalmazásjelszót](./end-user/multi-factor-authentication-end-user-app-passwords.md). A felhasználók (Törlés) törölje a bejelentkezési adatait, indítsa újra az alkalmazást, és jelentkezzen be a felhasználónevét és *alkalmazásjelszót* helyett a rendszeres jelszavát.

Ha a szervezet nem rendelkezik a hagyományos, nem kell engedélyezné a felhasználóknak alkalmazásjelszavakat.

> [!NOTE]
> Az ügyfelek Office 2013 modern hitelesítés
>
> Az alkalmazásjelszók csak szükség, modern hitelesítést nem támogató alkalmazások esetében. Ügyfelek Office 2013 modern hitelesítési protokollok támogatja, de be kell állítani. Újabb Office-ügyfelek automatikusan támogatják a modern hitelesítési protokollok megvalósítását végzi. További információkért lásd: a [Office 2013 modern hitelesítés nyilvános előzetes kiadásának bejelentésében](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**K: felhasználóim, hogy néha az a szöveg üzenet megérkezése nem vagy azok válaszolni kétirányú szöveges üzenetek, de az ellenőrzés túllépi az időkorlátot.**

A szöveges üzenetek kézbesítési és a válaszok a kétirányú SMS átvételét nem garantált mivel ellenőrizhetetlen tényező, amely hatással lehet a szolgáltatás megbízhatóságát. Ezek a tényezők közé tartozik a rendeltetési ország, a mobiltelefon szolgáltatói és a jel erőssége.

Ha a felhasználók gyakran problémákat megbízhatóan a szöveges üzenetek fogadására, kérje meg őket, helyette a mobil alkalmazás vagy a telefonhívás módszer használatához. A mobilalkalmazás is fogadhatja az értesítéseket is keresztül mobilhálózati és Wi-Fi-kapcsolatok. Emellett a mobilalkalmazás hozhat létre ellenőrző kódok kezelésére, akkor is, ha az eszköz regisztrációját egyáltalán nincs jel. A Microsoft Authenticator alkalmazás érhető el [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073), és [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Ha a szöveges üzenetek kell használnia, kétirányú SMS lehetőség helyett egyirányú SMS használatát javasoljuk. Egyirányú SMS megbízhatóbb, és megakadályozza, hogy felhasználók globális SMS díjfizetési válaszol olyan egy másik országból küldött szöveges üzenetet.

**K: mennyi ideig kell a felhasználók előtt a rendszer túllépi az időkorlátot adja meg az ellenőrző kódot szöveges üzenetet megváltozik?**

Néhány esetben igen. 

Az Azure MFA kiszolgáló v7.0 vagy annál újabb egyirányú SMS beállítás a timeout beállítás által egy beállításkulcs megadásával. Miután az MFA felhőalapú szolgáltatás a szöveges üzenetet küld, a megerősítési kódot (vagy egyszer használatos jelszót) küld vissza a multi-factor Authentication kiszolgáló. A multi-factor Authentication kiszolgáló tárolja a kód memória 300 másodpercig alapértelmezés szerint. Ha a kód megadása előtt eltelt a 300 másodperc, a rendszer megtagadja a hitelesítést. Ezeket a lépéseket használhatja az alapértelmezett időtúllépési beállítását:

1. Ugrás a HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Hozzon létre egy DWORD beállításkulcsot nevű **pfsvc_pendingSmsTimeoutSeconds** és az idő beállítása az Azure MFA kiszolgáló egyszeri PIN-kódok tárolni kívánt, másodpercben.

>[!TIP] 
>Ha több multi-factor Authentication kiszolgálón, csak azt, amelyik az eredeti hitelesítési kérelem feldolgozása tudja a felhasználó a kapott ellenőrzőkódot. Amikor a felhasználó adja meg a kódot, a hitelesítési kérelmet érvényesíti ugyanarra a kiszolgálóra kell elküldeni. A kód érvényesítésének küld egy másik kiszolgálót, ha az authentication megtagadásra kerül. 

Az Azure MFA kiszolgáló kétirányú SMS beállíthatja az időkorlát a multi-factor Authentication kezelési portál. Ha az SMS felhasználók a megadott időkorláton belül nem válaszol, a hitelesítés megtagadva. 

Az Azure MFA Használatát a felhőben (beleértve az AD FS-adapter vagy a hálózati házirend-kiszolgáló bővítmény) egyirányú SMS az időkorlát nem konfigurálható. Az Azure AD 180 másodpercig a megerősítési kódot tárolja. 

**K: használhatok hardvertokenek Azure multi-factor Authentication kiszolgáló?**

Azure multi-factor Authentication kiszolgálót használ, ha külső nyitott hitelesítési (OATH) időalapú, egyszer használatos jelszót (TOTP)-tokenek importálása, és majd felhasználja a kétlépéses ellenőrzéshez.

ActiveIdentity jogkivonatok, amelyek OATH TOTP jogkivonatok, ha a titkos kulcs be egy CSV-fájlt, és importálja az Azure multi-factor Authentication kiszolgáló is használhatja. OATH-tokenek használható Active Directory összevonási szolgáltatások (ADFS), az Internet Information Server (IIS) űrlapalapú hitelesítés és a távoli Authentication Dial-In User Service (RADIUS), mindaddig, amíg az ügyfélrendszeren elfogadhatja a felhasználó által megadott.

Az alábbi formátumok külső OATH TOTP jogkivonatok importálása  

- Hordozható szimmetrikus kulcs tárolója (PSKC)  
- Ha a fájl tartalmaz egy sorozatszám, titkos kulcsot Base-32 formátumúnak és egy adott időintervallumban CSV  

**K: használhatok Azure multi-factor Authentication kiszolgáló biztonságossá tételéhez a Terminálszolgáltatások?**

Igen, de ha használ a Windows Server 2012 R2 vagy újabb csak gondoskodhat a Terminálszolgáltatások távoli asztali átjáró (RD átjáró) használatával.

Biztonsági módosításait a Windows Server 2012 R2 megváltozott, hogyan Azure multi-factor Authentication kiszolgálóhoz kapcsolódik-e a helyi biztonsági szervezet (LSA) biztonsági csomag a Windows Server 2012 és korábbi verziói. Terminálszolgáltatások a Windows Server 2012 vagy régebbi verzióit is [alkalmazást a Windows-hitelesítés biztonságos](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Ha Windows Server 2012 R2 rendszert kell a távoli asztali átjáró.

**K: beállítása Hívóazonosító az MFA-kiszolgálón, de a felhasználók továbbra is kaphat egy névtelen hívó multi-factor Authentication hívásokat.**

Amikor a multi-factor Authentication hívásokat a telefonhálózatra a hálózaton keresztül, néha keresztül halad a vivőjel, amely nem támogatja a hívó azonosítóját. Ebből kifolyólag Hívóazonosító nem garantált, annak ellenére, hogy a multi-factor Authentication rendszer mindig elküldi.

**K: Miért vannak a felhasználók meg kell adni a biztonsági információk regisztrálni?**
Több oka, hogy sikerült megkérdezi a felhasználókat a biztonsági információk regisztrálása:

- A felhasználó engedélyezte a többtényezős Hitelesítést a rendszergazda által az Azure ad-ben, de nincs biztonsági adatokat a fiók még regisztrálva.
- A felhasználó engedélyezve van az önkiszolgáló jelszó-változtatási Azure AD-ben. A biztonsági információk segítenek visszaállítását a jövőben a jelszavát, ha azok bármikor elfelejtené őket.
- A felhasználó egy feltételes hozzáférési házirend megkövetelő és korábban még nem regisztrált multi-factor Authentication alkalmazás érhető el.
- A felhasználó regisztrál egy eszközt az Azure ad-val (beleértve az Azure AD Join), és a szervezet többtényezős Hitelesítést követel meg az eszközregisztrációhoz tartozó, de a felhasználó korábban már nincs regisztrálva az MFA szolgáltatásra.
- A felhasználó előállító vállalati Windows Hello a Windows 10-es (amely többtényezős Hitelesítést követel meg) és a korábban még nem regisztrált multi-factor Authentication.
- A szervezet létrehozott és engedélyezett egy multi-factor Authentication regisztráció szabályzattal, amely telepítve van a felhasználó.
- A felhasználó korábban az MFA szolgáltatásra regisztrált, de döntött, hogy egy hitelesítési módszert, mivel a rendszergazda letiltotta. A felhasználó ezért haladjon végig a multi-factor Authentication regisztráció újra egy új alapértelmezett hitelesítési módszer kiválasztása.


## <a name="errors"></a>Hibák
**K: milyen felhasználók tegye, ha azok egy "hitelesítési kérelme, mert nem egy aktivált fiókhoz" hibaüzenet jelenik meg mobilalkalmazáson keresztüli értesítések használata esetén?**

Kérje meg őket, az alábbi eljárás segítségével távolítsa el a fiókját a mobilalkalmazásban, majd vegye fel újra:

1. Ugrás a [az Azure portál profil](https://account.activedirectory.windowsazure.com/profile/) , és jelentkezzen be szervezeti fiókjával.
2. Válassza ki **további biztonsági ellenőrzés**.
3. A meglévő fiókot eltávolítsa a mobilalkalmazással.
4. Kattintson a **konfigurálása**, és kövesse az utasításokat, hogy engedélyezzék a mobilalkalmazásban.

**K: milyen felhasználók tegye, ha azok egy 0x800434D4L hibaüzenet jelenik meg a böngészőn kívüli alkalmazások történő bejelentkezéskor?**

A 0x800434D4L hiba akkor fordul elő, amikor megpróbál bejelentkezni a böngészőn kívüli a helyi számítógépen, amely nem működik együtt a kétlépéses ellenőrzést igénylő fiókok telepített alkalmazásokkal.

Megoldás esetében ez a hiba nem tartozhat külön felhasználói fiókjainak admin kapcsolatos és a nem rendszergazdai műveletek. Később a rendszergazdai fiókot és a nem rendszergazdai fiókot között postaládák csatolható, így a Outlook a nem rendszergazdai fiók használatával bejelentkezhet. További információt ebben a megoldásban, megtudhatja, hogyan [ad a rendszergazda megnyithatja és megtekintheti a felhasználói postaláda tartalmát képes](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Következő lépések
Ha a kérdését itt nem válaszolt, hagyja a lap alján a megjegyzések. Vagy a Súgó néhány további lehetőség:

* Keresés a [Microsoft támogatási tudásbázisát](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) gyakori technikai problémák megoldásainak.
* Keresse meg és keresse meg a technikai kérdések és válaszok a közösségi, vagy kérje meg a saját kérdést a [Azure Active Directory fórumán](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Ha egy örökölt PhoneFactor felhasználói és kérdése van vagy segítségre van szüksége átállítja jelszavát, használja a [jelszó-átállítási](mailto:phonefactorsupport@microsoft.com) nyissa meg a támogatási esetet mutató hivatkozást.
* A támogatási szakember [Azure multi-factor Authentication kiszolgáló (PhoneFactor) támogatási](https://support.microsoft.com/oas/default.aspx?prid=14947). Lépjen kapcsolatba velünk, ha esetén lehet hasznos információt tartalmazhatnak a lehető problémával kapcsolatos. Megadhat olyan információkat tartalmaz az oldal, ahol a hiba, a konkrét hibakód megtekinthető, az adott munkamenet-azonosító és a hiba látott felhasználó azonosítója látta.
