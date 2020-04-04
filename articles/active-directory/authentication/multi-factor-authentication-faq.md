---
title: Azure többtényezős hitelesítés – gyakori kérdések – Azure Active Directory
description: Az Azure többtényezős hitelesítésével kapcsolatos gyakori kérdések és válaszok.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e5a533bac5d7adac8b0423eff6c05f797c56f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652129"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication – gyakran ismételt kérdések

Ez a gyIK választ ad az Azure többtényezős hitelesítésével és a többtényezős hitelesítési szolgáltatás használatával kapcsolatos gyakori kérdésekre. A szolgáltatással, a számlázási modellekkel, a felhasználói élményekkel és a hibaelhárítással kapcsolatos kérdésekre bontva.

## <a name="general"></a>Általános kérdések

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.
> 
> A fogyasztásalapú licencelés már nem érhető el az új ügyfelek számára 2018.
> 2018. szeptember 1-i hatállyal előfordulhat, hogy új hitelesítési szolgáltatók jönnek létre. A meglévő hitelesítésszolgáltatók továbbra is használhatók és frissíthetők. A többtényezős hitelesítés továbbra is elérhető funkció marad az Azure AD Premium-licencekben.

> [!NOTE]
> Az alábbiakban megosztott információk az Azure többtényezős hitelesítési kiszolgálócsak azon felhasználók számára, akik már fut nak az MFA-kiszolgáló.

**K: Hogyan kezeli az Azure többtényezős hitelesítési kiszolgáló a felhasználói adatokat?**

A többtényezős hitelesítési kiszolgáló val a felhasználói adatok at csak a helyszíni kiszolgálókon tárolja a rendszer. A felhőben nincsenek állandó felhasználói adatok. Amikor a felhasználó kétlépéses ellenőrzést hajt végre, a többtényezős hitelesítési kiszolgáló adatokat küld az Azure többtényezős hitelesítésfelhő-szolgáltatásának hitelesítésre. A többtényezős hitelesítési kiszolgáló és a többtényezős hitelesítésfelhő-szolgáltatás közötti kommunikáció secure sockets layer (SSL) vagy Transport Layer Security (TLS) protokollt használ a 443-as kimenő porton.

Amikor hitelesítési kérelmeket küld a felhőszolgáltatásba, a hitelesítési és használati jelentések hez adatokat gyűjtenek. A kétlépéses ellenőrzési naplókban szereplő adatmezők a következők:

* **Egyedi azonosító** (felhasználónév vagy helyszíni többtényezős hitelesítési kiszolgáló azonosítója)
* **Vezeték- és utónév** (nem kötelező)
* **E-mail cím** (nem kötelező)
* **Telefonszám** (hanghívás vagy SMS-hitelesítés használata esetén)
* **Eszköztoken** (mobilalkalmazás-hitelesítés használata esetén)
* **Hitelesítési mód**
* **Hitelesítés eredménye**
* **Többtényezős hitelesítési kiszolgáló neve**
* **Többtényezős hitelesítési kiszolgáló IP-címe**
* **Ügyfél IP-címe** (ha elérhető)

A választható mezők konfigurálhatók a többtényezős hitelesítési kiszolgálón.

Az ellenőrzés eredménye (sikeres vagy megtagadás), és az oka, ha megtagadták, a hitelesítési adatokkal együtt tárolódnak. Ezek az adatok a hitelesítési és használati jelentésekben érhetők el.

**K: Milyen rövid SMS-kódokat használnak sms-üzenetek küldésére a felhasználók nak?**

Az Egyesült Államokban a Microsoft a következő SMS-kódokat használja:

   * 97671
   * 69829
   * 51789
   * 99399

Kanadában a Microsoft a következő SMS rövid kódokat használja:

   * 759731 
   * 673801

A Microsoft nem garantálja az egységes SMS- vagy hangalapú többtényezős hitelesítési gyorskézbesítést ugyanazzal a számmal. Felhasználóink érdekében a Microsoft bármikor hozzáadhat vagy eltávolíthat rövid kódokat, miközben az SMS-ek kézbesíthetőségének javítása érdekében útvonal-módosításokat végzünk. A Microsoft az Egyesült Államokon és Kanadán kívül nem támogatja a rövid kódokat az Országok/Régiók számára.

## <a name="billing"></a>Számlázás

A legtöbb számlázási kérdésre úgy lehet választ adni, hogy vagy a [többtényezős hitelesítésdíjszabási oldalra,](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) vagy az [Azure többtényezős hitelesítésbe töltésére](concept-mfa-licensing.md)vonatkozó dokumentációra hivatkozik.

**K: A szervezetemnek fizetnie kell a hitelesítéshez használt telefonhívások és szöveges üzenetek küldéséért?**

Nem, nem számítunk fel díjat az Azure többtényezős hitelesítésén keresztül a felhasználóknak küldött egyéni telefonhívásokért vagy szöveges üzenetekért. Ha hitelesítésenkénti MFA-szolgáltatót használ, minden egyes hitelesítésért díjat kell fizetnie, de a használt módszerért nem.

Előfordulhat, hogy a felhasználóknak a személyes telefonszolgáltatásuknak megfelelően díjat kell fizetniük a kapott telefonhívásokért vagy szöveges üzenetekért.

**K: A felhasználónkénti számlázási modell díjat számít fel nekem az összes engedélyezett felhasználóért, vagy csak azokért, amelyek kétlépéses ellenőrzést végeztek?**

A számlázás a többtényezős hitelesítés használatára konfigurált felhasználók számától függ, függetlenül attól, hogy az adott hónapban kétlépcsős ellenőrzést hajtottak-e végre.

**K: Hogyan működik a többtényezős hitelesítés számlázása?**

Amikor létrehoz egy felhasználónkénti vagy hitelesítésenkénti MFA-szolgáltatót, a szervezet Azure-előfizetése a használat alapján havonta kerül számlázásra. Ez a számlázási modell hasonló ahhoz, ahogyan az Azure számlázza a virtuális gépek és webhelyek használatát.

Ha előfizetést vásárol az Azure többtényezős hitelesítéshez, a szervezet csak az egyes felhasználók éves licencdíját fizeti. Az MFA-licencek és az Office 365, az Azure AD Premium vagy az Enterprise Mobility + Security csomagok számlázása így van számlázva. 

További információ az [Azure többtényezős hitelesítésbe beszerezni](concept-mfa-licensing.md)című részben.

**K: Létezik az Azure többtényezős hitelesítés ingyenes verziója?**

Bizonyos esetekben, igen.

Az Azure-rendszergazdák többtényezős hitelesítése az Azure MFA-funkciók egy részét kínálja a Microsoft online szolgáltatásaihoz való hozzáférés ért, beleértve az [Azure Portalt](https://portal.azure.com) és a [Microsoft 365 felügyeleti központot.](https://admin.microsoft.com) Ez az ajánlat csak az Azure Active Directory-példányok globális rendszergazdáira vonatkozik, akik nem rendelkeznek az Azure MFA teljes verziójával egy MFA-licencen, egy csomagon vagy egy önálló fogyasztásalapú szolgáltatón keresztül. Ha a rendszergazdák az ingyenes verziót használják, és az Azure MFA teljes verzióját megvásárolja, akkor az összes globális rendszergazda automatikusan a fizetős verzióra emelkedik.

Az Office 365-felhasználók többtényezős hitelesítése az Azure MFA-szolgáltatásainak egy részét kínálja ingyenesen az Office 365-szolgáltatásokhoz való hozzáféréshez, beleértve az Exchange Online-t és a SharePoint Online-t. Ez az ajánlat azokra a felhasználókra vonatkozik, akik office 365-licenccel rendelkeznek, ha az Azure Active Directory megfelelő példánya nem rendelkezik az Azure MFA teljes verziójával egy MFA-licencen, egy csomagon vagy egy önálló fogyasztásalapú szolgáltatón keresztül.

**K: Bármikor válthat a szervezeta felhasználónkénti és hitelesítésenkénti fogyasztási számlázási modellek között?**

Ha a szervezet a fogyasztásalapú számlázással önálló szolgáltatásként vásárolja meg az MFA-t, akkor az MFA-szolgáltató létrehozásakor számlázási modellt kell választania. A számlázási modell nem módosítható az MFA-szolgáltató létrehozása után. 

Ha az MFA-szolgáltató *nem* kapcsolódik egy Azure AD-bérlőhöz, vagy az új MFA-szolgáltatót egy másik Azure AD-bérlőhöz kapcsolja, a felhasználói beállítások és a konfigurációs beállítások nem kerülnek átvitelre. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

További információ az [MFA-szolgáltatókról az Azure multifactor hitelesítésszolgáltatóval való ismerkedés című részben.](concept-mfa-authprovider.md)

**K: Bármikor válthat a szervezetem a fogyasztásalapú számlázás és az előfizetések (licencalapú modell) között?**

Bizonyos esetekben, igen.

Ha a címtár rendelkezik egy *felhasználónkénti* Azure többtényezős hitelesítésszolgáltatóval, hozzáadhat MFA-licenceket. A licenccel rendelkező felhasználók nem számítanak bele a felhasználónkénti fogyasztásalapú számlázásba. A licenccel nem rendelkező felhasználók továbbra is engedélyezhetők az MFA-szolgáltatón keresztül. Ha többtényezős hitelesítéshasználatára konfigurált összes felhasználóhoz vásárol és rendel licenceket, törölheti az Azure többtényezős hitelesítésszolgáltatót. Bármikor létrehozhat egy másik felhasználónkénti MFA-szolgáltatót, ha a jövőben több felhasználója van, mint licence.

Ha a címtár rendelkezik egy hitelesítési Azure többtényezős hitelesítésszolgáltató, mindig minden egyes hitelesítés, ha az *MFA-szolgáltató* kapcsolódik az előfizetéshez. Az MFA-licenceket hozzárendelheti a felhasználókhoz, de továbbra is minden kétlépéses ellenőrzési kérelemért díjat kell fizetnie, függetlenül attól, hogy az olyan személytől származik-e, akinek többszintű licencvan hozzárendelve, vagy sem.

**K: A szervezetnek identitásokat kell használnia és szinkronizálnia az Azure többtényezős hitelesítéshasználatához?**

Ha a szervezet egy fogyasztásalapú számlázási modellt használ, az Azure Active Directory nem kötelező, de nem kötelező. Ha az MFA-szolgáltató nincs összekapcsolva egy Azure AD-bérlővel, csak a helyszínen telepítheti az Azure többtényezős hitelesítési kiszolgálót.

Az Azure Active Directory szükséges a licencmodellhez, mert a licencek az Azure AD-bérlőhöz kerülnek, amikor megvásárolja és hozzárendeli azokat a címtárban lévő felhasználókhoz.

## <a name="manage-and-support-user-accounts"></a>Felhasználói fiókok kezelése és támogatása

**K: Mit kell mondanom a felhasználóimnak, ha nem kapnak választ a telefonjukon?**

A felhasználók 5 perc alatt akár 5 alkalommal is próbálkoznak telefonhívással vagy SMS-sel a hitelesítéshez. A Microsoft több szolgáltatót használ hívások és SMS-üzenetek kézbesítésére. Ha ez nem működik, kérjük, nyisson meg egy támogatási esetet a Microsoftnál a további hibaelhárításhoz.

Ha a fenti lépések nem működnek remélhetőleg az összes felhasználó több ellenőrzési módszert konfigurált. Tanácsolja nekik, hogy próbáljanak újra bejelentkeznie, ezúttal azonban válasszanak egy másik ellenőrzési módszert a bejelentkezési oldalon.

A felhasználókat a [Végfelhasználói hibaelhárítási útmutatóra](../user-help/multi-factor-authentication-end-user-troubleshoot.md)irányíthatja.

**K: Mi a teendő, ha az egyik felhasználóm nem tud bejutni a fiókjába?**

A felhasználó fiókját úgy állíthatja vissza, hogy újra végigmegy a regisztrációs folyamaton. További információ a [felhasználói és eszközbeállítások kezeléséről az Azure többtényezős hitelesítésével a felhőben.](howto-mfa-userdevicesettings.md)

**K: Mi a teendő, ha az egyik felhasználóm elveszíti az alkalmazásjelszavakat használó telefont?**

A jogosulatlan hozzáférés megakadályozása érdekében törölje a felhasználó összes alkalmazásjelszavát. Miután a felhasználó rendelkezik egy csereeszközzel, újra létrehozhatja a jelszavakat. További információ a [felhasználói és eszközbeállítások kezeléséről az Azure többtényezős hitelesítésével a felhőben.](howto-mfa-userdevicesettings.md)

**K: Mi a teendő, ha a felhasználó nem tud bejelentkezni a nem böngészőalkalmazásokba?**

Ha a szervezet továbbra is régi ügyfeleket használ, és [engedélyezte az alkalmazásjelszavak használatát,](howto-mfa-mfasettings.md#app-passwords)akkor a felhasználók nem tudnak bejelentkezni ezekbe az örökölt ügyfelekbe a felhasználónevükkel és jelszavukkal. Ehelyett be kell [állítaniuk az alkalmazásjelszavakat.](../user-help/multi-factor-authentication-end-user-app-passwords.md) A felhasználóknak törölniük kell (törölniük) a bejelentkezési adataikat, újra kell indítaniuk az alkalmazást, majd a szokásos jelszó helyett a felhasználónevükkel és *az alkalmazásjelükkel* kell bejelentkezniük.

Ha a szervezet nem rendelkezik örökölt ügyfelekkel, ne engedje, hogy a felhasználók alkalmazásjelszavakat hozzanak létre.

> [!NOTE]
> Modern hitelesítés Office 2013-ügyfelekszámára
>
> Az alkalmazásjelszavak csak olyan alkalmazásokhoz szükségesek, amelyek nem támogatják a modern hitelesítést. Az Office 2013-ügyfelek támogatják a modern hitelesítési protokollokat, de konfigurálni kell őket. A modern hitelesítés mostantól minden olyan ügyfél számára elérhető, aki az Office 2013 2013 márciusi vagy újabb frissítését futtatja. További információt az Office [365 modern hitelesítéssel frissített blogbejegyzésében](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)talál.

**K: A felhasználók azt mondják, hogy néha nem kapják meg a szöveges üzenetet, vagy az ellenőrzési idő túl.**

Az SMS-üzenetek kézbesítése nem garantált, mert ellenőrizhetetlen tényezők befolyásolhatják a szolgáltatás megbízhatóságát. Ezek közé a tényezők közé tartozik a célország/régió, a mobiltelefon-szolgáltató és a jelerősség.

Ha a felhasználókgyakran problémáik vannak a szöveges üzenetek megbízható fogadásával, mondja meg nekik, hogy inkább a mobilalkalmazást vagy a telefonhívási módszert használják. A mobilalkalmazás mobil- és Wi-Fi-kapcsolaton keresztül is fogadhat értesítéseket. Ezenkívül a mobilalkalmazás akkor is képes ellenőrző kódokat generálni, ha az eszköz egyáltalán nem rendelkezik jellel. A Microsoft Authenticator alkalmazás [Android,](https://go.microsoft.com/fwlink/?Linkid=825072) [IOS](https://go.microsoft.com/fwlink/?Linkid=825073)és [Windows Phone rendszerhez](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)érhető el.

**K: Módosíthatom azt az időt, ameddig a felhasználóknak be kell írniuk az ellenőrző kódot egy szöveges üzenetből, mielőtt a rendszer idővel kikerül?**

Bizonyos esetekben, igen. 

Az Egyirányú SMS-ek az Azure MFA Server v7.0-s vagy újabb verzióval, konfigurálhatja az időbeli megtadalékbeállítást egy beállítási kulcs beállításával. Miután az MFA felhőszolgáltatás elküldi a szöveges üzenetet, az ellenőrző kód (vagy egyszeri jelkód) visszakerül az MFA-kiszolgálóra. Az MFA-kiszolgáló alapértelmezés szerint 300 másodpercig tárolja a kódot a memóriában. Ha a felhasználó nem adja meg a kódot, mielőtt a 300 másodperc telt el, a hitelesítés megtagadva. Az alábbi lépésekkel módosíthatja az alapértelmezett időelési beállítást:

1. Nyissa meg a HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor webhelyet.
2. Hozzon létre egy **pfsvc_pendingSmsTimeoutSeconds** nevű duplaszó beállításkulcsot, és állítsa be azt az időt másodpercben, amerre az Azure MFA-kiszolgáló egyszeri jelkódokat tárol.

>[!TIP] 
>Ha több MFA-kiszolgálóval rendelkezik, csak az, amelyik feldolgozta az eredeti hitelesítési kérelmet, ismeri a felhasználónak küldött ellenőrző kódot. Amikor a felhasználó beírja a kódot, az érvényesítésre vonatkozó hitelesítési kérelmet ugyanannak a kiszolgálónak kell elküldenie. Ha a kód érvényesítése egy másik kiszolgálóra kerül, a rendszer megtagadja a hitelesítést. 

Ha a felhasználók nem válaszolnak az SMS-re a megadott időtúladási időszakon belül, a rendszer elutasítja a hitelesítést. 

Egyirányú SMS az Azure MFA a felhőben (beleértve az AD FS-adapter vagy a hálózati házirend-kiszolgáló bővítmény), nem konfigurálhatja az időbeli elállási beállítást. Az Azure AD 180 másodpercig tárolja az ellenőrző kódot. 

**K: Használhatok hardveres jogkivonatokat az Azure többtényezős hitelesítési kiszolgálójával?**

Ha Az Azure többtényezős hitelesítési kiszolgálót használja, importálhatja a harmadik féltől származó nyílt hitelesítés (OATH) időalapú, egyszeri jelszó (TOTP) tokeneket, majd használhatja őket kétlépéses ellenőrzéshez.

Használhatja az ActiveIdentity-jogkivonatokat, amelyek OATH TOTP-tokenek, ha a titkos kulcsot csv-fájlba helyezi, és importálja az Azure többtényezős hitelesítési kiszolgálóra. Az OATH-tokenek az Active Directory összevonási szolgáltatásokkal (ADFS), az Internet Information Server (IIS) űrlapalapú hitelesítéssel és a távoli hitelesítésbetárcsázási felhasználói szolgáltatással (RADIUS) is használhatók, amennyiben az ügyfélrendszer elfogadja a felhasználói bevitelt.

Harmadik féltől származó OATH TOTP-tokeneket a következő formátumokkal importálhat:  

- Hordozható szimmetrikus kulcstartó (PSKC)  
- CSV, ha a fájl tartalmaz egy sorozatszámot, egy titkos kulcsot Base 32 formátumban, és egy időintervallumot  

**K: Használhatom az Azure többtényezős hitelesítési kiszolgálóját a terminálszolgáltatások védelmére?**

Igen, de windows Server 2012 R2 vagy újabb rendszer használata esetén csak távoli asztali átjáró (Távoli asztali átjáró) használatával tudja biztonságossá tenni a terminálszolgáltatásokat.

A Windows Server 2012 R2 biztonsági változásai megváltoztatták az Azure Multi-Factor Authentication Server csatlakozását a Helyi biztonsági szervezet (LSA) biztonsági csomaghoz a Windows Server 2012 és korábbi verzióiban. A Terminálszolgáltatások Windows Server 2012-es vagy korábbi verzióiban a [Windows-hitelesítéssel biztonságossá](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)tehet egy alkalmazást. Ha Windows Server 2012 R2 rendszert használ, távoli asztali átjáróra van szüksége.

**K: Beállítottam a hívóazonosítót az MFA-kiszolgálón, de a felhasználók továbbra is többtényezős hitelesítési hívásokat kapnak egy névtelen hívótól.**

Ha a többtényezős hitelesítési hívásokat a nyilvános telefonhálózaton keresztül bonyolítják le, néha olyan szolgáltatón keresztül irányítják őket, amely nem támogatja a hívóazonosítót. Emiatt a hívóazonosító nem garantált, még akkor sem, ha a többtényezős hitelesítési rendszer mindig elküldi azt.

**K: Miért kéri a rendszer a felhasználókat a biztonsági adataik regisztrálására?**
Több oka is lehet annak, hogy a felhasználókat rálehet kérni biztonsági adataik regisztrálására:

- A felhasználó az Azure AD-ben a rendszergazda engedélyezte az MFA-t, de még nem rendelkezik a fiókjához regisztrált biztonsági adatokkal.
- A felhasználó engedélyezve van az önkiszolgáló jelszó-visszaállítás az Azure AD-ben. A biztonsági információk segítenek nekik a jelszó visszaállításában a jövőben, ha valaha is elfelejtik.
- A felhasználó hozzáfért egy olyan alkalmazáshoz, amely feltételes hozzáférési házirenddel rendelkezik az MFA megköveteléséhez, és korábban nem regisztrált az MFA-hoz.
- A felhasználó regisztrál egy eszközt az Azure AD-vel (beleértve az Azure AD-s csatlakozást is), és a szervezetnek többfa-t kell igényelnie az eszköz regisztrációhoz, de a felhasználó korábban nem regisztrált az MFA-hoz.
- A felhasználó a Windows Hello for Business alkalmazást generálja a Windows 10-ben (amely hez MFA szükséges), és korábban nem regisztrált az MFA-hoz.
- A szervezet létrehozott és engedélyezett egy MFA regisztrációs házirendet, amely a felhasználóra lett alkalmazva.
- A felhasználó korábban regisztrált az MFA-hoz, de olyan ellenőrzési módszert választott, amelyet a rendszergazda azóta letiltott. A felhasználónak ezért újra át kell mennie az MFA-regisztráción, hogy új alapértelmezett ellenőrzési módszert válasszon.

## <a name="errors"></a>Hibák

**K: Mit tegyenek a felhasználók, ha a mobilalkalmazás-értesítések használatakor "A hitelesítési kérelem nem aktivált fiókhoz" hibaüzenet jelenik meg?**

Mondd meg nekik, hogy kövessék ezt az eljárást, hogy eltávolítsák a fiókjukat a mobilalkalmazásból, majd adja hozzá újra:

1. Nyissa meg [az Azure Portal-profilt,](https://account.activedirectory.windowsazure.com/profile/) és jelentkezzen be a szervezeti fiókkal.
2. Válassza a **További biztonsági ellenőrzés lehetőséget.**
3. Távolítsa el a meglévő fiókot a mobilalkalmazásból.
4. Kattintson **a Konfigurálás**gombra, majd kövesse az utasításokat a mobilalkalmazás újrakonfigurálásához.

**K: Mit tegyenek a felhasználók, ha 0x800434D4L hibaüzenetet látnak, amikor nem böngészőalkalmazásba jelentkeznek be?**

A 0x800434D4L hiba akkor jelentkezik be, amikor megpróbál bejelentkezni egy helyi számítógépre telepített nem böngészőalkalmazásba, amely nem működik a kétlépéses ellenőrzést igénylő fiókokkal.

A hiba kerülő megoldása, ha külön felhasználói fiókkal rendelkezik a rendszergazdai és nem rendszergazdai műveletekhez. Később a rendszergazdai és a nem rendszergazdai fiók között is összekapcsolhatja a postaládákat, így nem rendszergazdai fiókjával jelentkezhet be az Outlookba. A megoldással kapcsolatos további részletekről megtudhatja, hogy [miként adhatja meg a rendszergazdának a felhasználó postaládájának megnyitását és megtekintését.](https://help.outlook.com/141/gg709759.aspx?sl=1)

## <a name="next-steps"></a>További lépések

Ha a kérdésedre itt nem kap választ, kérjük, hagyja az oldal alján található megjegyzésekben. Vagy íme néhány további lehetőség a segítségszerzésre:

* Keressen a [Microsoft támogatási tudásbázisban](https://support.microsoft.com) a gyakori technikai problémák megoldásait.
* Keressen és böngésszen a közösség technikai kérdéseiben és válaszaiban, vagy tegye fel saját kérdését az [Azure Active Directory fórumain.](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)
* Ha Ön egy régebbi PhoneFactor-ügyfél, és kérdései vannak, vagy segítségre van szüksége a jelszó alaphelyzetbe állításához, használja a [jelszó-visszaállítási](mailto:phonefactorsupport@microsoft.com) hivatkozást egy támogatási eset megnyitásához.
* Lépjen kapcsolatba egy támogatási szakemberrel az [Azure Multi-Factor Authentication Server (PhoneFactor) támogatásán](https://support.microsoft.com/oas/default.aspx?prid=14947)keresztül. Amikor kapcsolatba lép velünk, hasznos, ha a lehető legtöbb információt meg tudja mondani a problémáról. A megadott információk közé tartozik az oldal, ahol a hibát látta, a konkrét hibakód, az adott munkamenet-azonosító és a hibát megkezdő felhasználó azonosítója.
