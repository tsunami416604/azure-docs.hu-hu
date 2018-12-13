---
title: Az Azure Multi-Factor Authentication konfigurálása
description: Ez a cikk ismerteti az Azure multi-factor Authentication beállításainak konfigurálása az Azure Portalon
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 7bd63dc991500f1d7f68169342b9612c1b303a07
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320658"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Az Azure multi-factor Authentication beállításainak konfigurálása

Ez a cikk segít az Azure Portalon a multi-factor Authentication szolgáltatás beállításainak kezelése. Ez fedezi a különböző témakörök, amelyek a legtöbbet az Azure multi-factor Authentication segítségével. Nem mind a szolgáltatások áll rendelkezésre minden [verzióját az Azure multi-factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

Navigáljon az Azure-portálról a multi-factor Authentication kapcsolatos beállítások elérheti **Azure Active Directory** > **MFA**.

![Az Azure portal – Azure AD multi-factor Authentication szolgáltatás beállításainak](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Beállítások

Ezek a beállítások némelyike a alkalmazni az MFA-kiszolgáló, az Azure MFA vagy mindkettőhöz.

| Szolgáltatás | Leírás |
| ------- | ----------- |
| Fiókzárolás | Ideiglenesen zárolni fiókok a multi-factor authentication szolgáltatás Ha túl sok hitelesítési kísérleteket a sor megtagadva. Ez a funkció csak a felhasználók, akik adjon meg egy PIN-kódot vonatkozik. (MFA-kiszolgáló) |
| [Felhasználó blokkolása/feloldása](#block-and-unblock-users) | Bizonyos felhasználók (helyszíni) MFA-kiszolgálón tiltsa le a multi-factor Authentication szolgáltatás kérelmek fogadására képes használja. A letiltott felhasználók hitelesítési kísérletek a rendszer automatikusan megtagadja. Felhasználók, amelyek le vannak tiltva az idő 90 nap letiltva marad. |
| [Visszaélési riasztás](#fraud-alert) | Felhasználók képességét jelentse a csalárd ellenőrzési kérelmeket az MFA-kiszolgáló kapcsolatos beállítások konfigurálását. |
| Értesítések | MFA-kiszolgáló események értesítések engedélyezéséhez. |
| [Az OATH-tokenek](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | A felhasználók számára az OATH-tokenek kezelésére szolgál az Azure MFA felhőalapú környezetekben. |
| [Telefonhívás beállításai](#phone-call-settings) | Telefonhívási és Üdvözlések a felhőbeli és helyi környezetekben kapcsolatos beállítások konfigurálását. |
| Szolgáltatók | Így megjelennek az olyan meglévő hitelesítési szolgáltatót, hogy is társítva van a fiók. Új hitelesítési szolgáltatók nem hozható létre 2018. szeptember 1. |

## <a name="manage-mfa-server"></a>MFA-kiszolgáló kezelése

Ebben a szakaszban a beállítások csak az MFA-kiszolgálóhoz vannak.

| Szolgáltatás | Leírás |
| ------- | ----------- |
| Kiszolgáló beállításai | Töltse le az MFA-kiszolgáló és a környezet inicializálása aktiváló hitelesítő adatok előállítása |
| [Az egyszeri Mellőzés](#one-time-bypass) | A felhasználót, hogy csak korlátozott ideig a kétlépéses ellenőrzés nélküli hitelesítés engedélyezése. |
| [Gyorsítótár-szabályokkal](#caching-rules) |  Gyorsítótárazás elsősorban a Ha a helyszíni rendszerek, például VPN, több ellenőrzési kérést küld, miközben még folyamatban van az első kérésre. Ez a funkció lehetővé teszi, hogy automatikusan, sikeres, a felhasználó sikeres folyamatban az első ellenőrzést követően a későbbi kérelmeket. |
| Kiszolgáló állapota | A helyszíni MFA-kiszolgálóval, beleértve a verzió állapota, IP-Címek használatához és a legutóbbi kommunikációs időpontját és dátumát állapotának megtekintéséhez. |

## <a name="activity-report"></a>Tevékenységjelentés

A jelentéskészítési elérhető itt csak a (helyszíni) MFA-kiszolgálóra. Az Azure MFA Szolgáltatáshoz (felhő) a jelentések a bejelentkezések jelentés megtekintéséhez az Azure ad-ben.

## <a name="block-and-unblock-users"></a>Felhasználók tiltásának feloldása és tiltása

Használja a _letilthatók és feloldhatók felhasználók_ funkció meg, hogy a felhasználók hitelesítési kérelmek fogadását. A letiltott felhasználók hitelesítési kísérletek a rendszer automatikusan megtagadja. Felhasználók, amelyek le vannak tiltva az idő 90 nap letiltva marad. Ez a funkció csak a (helyszíni) MFA-kiszolgálóra.

### <a name="block-a-user"></a>Felhasználó blokkolása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA** > **felhasználó blokkolása/feloldása**.
3. Válassza ki **Hozzáadás** a felhasználó blokkolását.
4. Válassza ki a **replikációs csoport**. Adja meg a felhasználónevet a letiltott felhasználó **username@domain.com**. Adja meg a megjegyzést a **OK** mező.
5. Válassza ki **Hozzáadás** a felhasználó letiltásának befejezéséhez.

### <a name="unblock-a-user"></a>Felhasználó blokkolásának feloldása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA** > **felhasználó blokkolása/feloldása**.
3. Válassza ki **feloldása** a a **művelet** oszlop mellett a felhasználó tiltásának feloldásához.
4. Adja meg a megjegyzést a **okból a blokkolás feloldásának** mező.
5. Válassza ki **feloldása** befejeződik, a felhasználó blokkolásának feloldása.

## <a name="fraud-alert"></a>Csalási riasztás

Konfigurálja a _visszaélési riasztás_ funkciót, hogy a felhasználók jelenthetik-e rosszindulatú megpróbál hozzáférni az erőforrásokhoz. A mobilalkalmazás vagy a telefonját a felhasználók jelenthetik csalás kísérletek. Ez a funkció csak a (helyszíni) MFA-kiszolgálóra.

### <a name="turn-on-fraud-alerts"></a>Visszaélési riasztás bekapcsolása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA** > **visszaélési riasztás**.
3. Állítsa be a **engedélyezése a felhasználóknak visszaélési riasztás küldésének** beállítást **a**.
4. Kattintson a **Mentés** gombra.

### <a name="configuration-options"></a>Beállítási lehetőségek

* **Felhasználó blokkolása visszaélés jelentésekor**: Ha egy felhasználó a csalás jelentéseket, a fiók le van tiltva, 90 napig, vagy amíg a rendszergazda feloldja a fiók. A rendszergazdák, tekintse át a bejelentkezések bejelentkezési jelentés használatával, és megfelelő lépéseket a jövőbeli csalások megakadályozásához. A rendszergazda tudja majd [feloldása](#unblock-a-user) a felhasználói fiók.
* **Csalást jelent a kezdeti üdvözlés során kódot**: Amikor a felhasználók megkapják a telefonhívás, a kétlépéses hitelesítés végrehajtására, azokat általában nyomja le az **#** a bejelentkezés megerősítéséhez. Csalást jelent be, hogy a felhasználó nem ír kódot előtt **#**. Ez a kód **0** alapértelmezés szerint azonban testre szabható.

   >[!NOTE]
   >Az alapértelmezett hangalapú üdvözletek, a Microsoft nyomja meg a felhasználók felkérése **0#** a csalási riasztás küldéséhez. Ha szeretné más, a kód használata **0**, jegyezze fel, és töltse fel a saját egyéni hangalapú üdvözletek, a felhasználók megfelelő utasításokat.
   >

### <a name="view-fraud-reports"></a>Csalás jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **az Azure Active Directory** > **bejelentkezések**. A csalás jelentés most már a standard szintű Azure AD-bejelentkezések jelentés részét képezi.

## <a name="phone-call-settings"></a>Telefonhívás beállításai

### <a name="caller-id"></a>Hivó azonosítója

**MFA-hívó azonosítószáma** – Ez a szám a felhasználók látni fogják a telefonján. Csak az Egyesült államokbeli számok használata engedélyezett.

>[!NOTE]
>Ha a multi-factor Authentication hívásokat a telefon nyilvános hálózaton keresztül, néha keresztül halad a szolgáltató, amely nem támogatja a hívó azonosítóját. Emiatt hivó azonosítója nem garantált, annak ellenére, hogy a multi-factor Authentication rendszer mindig küld.

### <a name="custom-voice-messages"></a>Egyedi Hangüzenetek

A kétlépéses ellenőrzéshez a használhatja a saját felvételt vagy Üdvözlések a _egyedi Hangüzenetek_ funkció. Ezek az üzenetek használható önmagában, vagy cserélje le a Microsoft felvételt.

Mielőtt hozzálátna, vegye figyelembe a következő korlátozásokkal:

* A támogatott formátumok a következők: .wav, .mp3 és.
* A fájl mérete legfeljebb 5 MB-ot.
* Lehet, hogy a hitelesítési üzenetek 20 másodpercnél rövidebb. Üzenetek 20 másodpercnél hosszabb okozhat az ellenőrzés sikertelen lesz. A felhasználó előfordulhat, hogy nem válaszol, mielőtt befejezi az üzenetet, és az ellenőrzés túllépi az időkorlátot.

### <a name="custom-message-language-behavior"></a>Egyéni üzenet nyelvi viselkedése

Egyéni hangüzenet játsszák a felhasználónak, amikor a nyelv, az üzenet attól függ, ezek a tényezők:

* Az aktuális felhasználó nyelve.
  * A nyelv, a felhasználó böngészője által észlelt.
  * Más hitelesítési forgatókönyvek viselkedése rendellenes lehet.
* Minden rendelkezésre álló egyéni üzenetek nyelve.
  * Ezt a nyelvet a rendszergazda által van kiválasztva, egy egyéni üzenetet hozzáadásakor.

Ha például van egy német rendszernyelvű csak egy egyéni üzenetet:

* Egy felhasználó hitelesíti magát a német nyelven fog hallgassa meg az egyéni német üzenet.
* Egy felhasználó hitelesíti magát, angol nyelven fog hallgassa meg a szabványos angol üzenetet.

### <a name="set-up-a-custom-message"></a>Egy egyéni üzenetet beállítása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
1. Keresse meg a **Azure Active Directory** > **MFA** > **telefonhívási beállítások**.
1. Válassza ki **Hozzáadás üdvözlés**.
1. Válassza ki az üdvözlőlap.
1. Válassza ki a nyelvet.
1. Válassza ki egy .mp3, .wav vagy hangfájl feltöltése.
1. Válassza a **Hozzáadás** lehetőséget.

## <a name="one-time-bypass"></a>Egyszeri mellőzés

A _egyszeri Mellőzés_ funkció lehetővé teszi, hogy a felhasználó egy alkalommal a kétlépéses ellenőrzés nélkül. A Mellőzés átmeneti, és a megadott számú másodperc után lejár. Olyan esetekben, ahol a mobilalkalmazásban vagy a telefon nem kap egy értesítést vagy egy telefonhívás engedélyezheti az egyszeri mellőzés, hogy a felhasználó hozzáférhessen a kívánt erőforrást.

### <a name="create-a-one-time-bypass"></a>Hozzon létre egy egyszeri Mellőzés

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA** > **egyszeri Mellőzés**.
3. Válassza a **Hozzáadás** lehetőséget.
4. Ha szükséges, válassza ki a replikációs csoport a Mellőzés.
5. Adja meg a felhasználónevét, **username@domain.com**. Adja meg a Mellőzés kell utolsó másodpercek számát. Adja meg a mellőzés okát.
6. Válassza a **Hozzáadás** lehetőséget. Az időkorlát azonnal érvénybe lép. A felhasználónak kell bejelentkezni, mielőtt az egyszeri Mellőzés lejár.

### <a name="view-the-one-time-bypass-report"></a>Az egyszeri Mellőzés jelentés megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a **Azure Active Directory** > **MFA** > **egyszeri Mellőzés**.

## <a name="caching-rules"></a>Gyorsítótárszabályok

Beállíthatja, hogy a hitelesítési kísérlet után a felhasználók hitelesítése az egy adott időszakban az _gyorsítótárazás_ funkció. Későbbi hitelesítési próbálkozások során a felhasználó a megadott belül automatikusan sikeresek időszakban. Gyorsítótárazás elsősorban a Ha a helyszíni rendszerek, például VPN, több ellenőrzési kérést küld, miközben még folyamatban van az első kérésre. Ez a funkció lehetővé teszi, hogy automatikusan, sikeres, a felhasználó sikeres folyamatban az első ellenőrzést követően a későbbi kérelmeket.

>[!NOTE]
>A gyorsítótárazási funkció nem célja a bejelentkezéseket az Azure Active Directory (Azure AD) használható.

### <a name="set-up-caching"></a>Állítsa be a gyorsítótárazás

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA** > **gyorsítótár-szabályokkal**.
3. Válassza a **Hozzáadás** lehetőséget.
4. Válassza ki a **gyorsítótár típusa** a legördülő listából. Adja meg a maximális számát **másodperc gyorsítótár**.
5. Ha szükséges, válassza ki a hitelesítés típusát, és adjon meg egy alkalmazást.
6. Válassza a **Hozzáadás** lehetőséget.

## <a name="mfa-service-settings"></a>Az MFA Szolgáltatásbeállítások

Beállítások az alkalmazásjelszókat, a megbízható IP-cím, az ellenőrzési lehetőség, és ne feledje, többtényezős hitelesítés az Azure multi-factor Authentication szolgáltatás beállításainak tekintheti meg. Szolgáltatás beállításai érhetők el az Azure Portalon navigáljon az **Azure Active Directory** > **MFA** > **bevezetés**  >  **Konfigurálása** > **további felhőalapú MFA beállításainak**.

![Az Azure multi-factor Authentication szolgáltatás beállításai](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Alkalmazásjelszók

Bizonyos alkalmazások, például az Office 2010 vagy korábbi és az iOS 11-et, mielőtt az Apple Mail nem támogatják a kétlépéses ellenőrzést. Az alkalmazások fogadja el a második ellenőrzési levelezésre. Ezek az alkalmazások használatához kihasználhatja a _alkalmazásjelszók_ funkció. A hagyományos jelszó helyett egy alkalmazásjelszót használatával lehetővé teszi az alkalmazások a kétlépéses ellenőrzés megkerülését, és folytassa a munkát.

A modern hitelesítést is támogatja, az ügyfél a Microsoft Office 2013 és újabb verziók. Mellett az Outlook, Office 2013-ügyfelek támogatásához modern hitelesítési protokollok, és engedélyezheti a kétlépéses ellenőrzéssel összehangolhatja. Miután az ügyfél engedélyezve van, az alkalmazásjelszavak nem szükséges az ügyfél.

>[!NOTE]
>Az alkalmazásjelszavak nem működnek-alapú feltételes hozzáférés többtényezős hitelesítési házirendeket és a modern hitelesítést.

### <a name="considerations-about-app-passwords"></a>Tudnivalók az alkalmazásjelszókról szempontok

Az alkalmazásjelszavak használata esetén vegye figyelembe az alábbi fontos szempontokat:

* Alkalmazásjelszók csak egyszer megadott alkalmazásonként. A jelszavak nyomon követheti, vagy adja meg őket minden egyes felhasználóknak nem kell.
* A tényleges jelszót automatikusan jön létre, és nem a felhasználó által megadott. Az automatikusan generált jelszót kitalálni a támadónak nehezebb, és a fokozott biztonság érdekében.
* Nincs egy legfeljebb 40 jelszó felhasználónként.
* Az alkalmazásokat, amelyek gyorsítótárazzák a jelszavakat, és használja őket olyan telephelyi szituációk esetén is sikertelen, mert az alkalmazásjelszó nem ismeri a munkahelyi vagy iskolai fiók kívül megkezdődött. Ebben a forgatókönyvben például a helyszíni Exchange e-maileket, de az archivált levelek a felhőben. Ebben a forgatókönyvben ugyanazt a jelszót nem működik.
* A multi-factor Authentication szolgáltatás egy felhasználói fiók engedélyezése után az alkalmazásjelszavak használható a legtöbb nem böngésző típusú ügyfeleken például az Outlook és a Microsoft Skype vállalati. Felügyeleti művelet nem hajtható végre az alkalmazásjelszókat a böngészőn kívüli alkalmazások, például a Windows PowerShell segítségével. A művelet nem hajtható végre, akkor is, ha a felhasználó rendelkezik egy rendszergazdai fiókkal. PowerShell-parancsfájlok futtatásához, hozzon létre egy szolgáltatás-fiókot egy erős jelszót, és ne engedélyezze a fiókot, a kétlépéses ellenőrzéshez.

>[!WARNING]
>Az alkalmazásjelszavak hibrid környezetekben, ahol ügyfelek kommunikálhatnak a helyszíni és felhőalapú automatikus felderítése végpontok nem működnek. Tartomány jelszavakat a helyszíni hitelesítéshez szükségesek. Az alkalmazásjelszavak felhőbeli hitelesítéshez szükségesek.

### <a name="guidance-for-app-password-names"></a>Útmutató a jelszó alkalmazásnevek

Jelszó alkalmazásnevek tükröznie kell azt az eszközt, amelyen használhatók. Ha egy laptop, amelyen a böngészőn kívüli alkalmazások, mint az Outlook, Word és az Excel, hozzon létre egy alkalmazásjelszót nevű **hordozható** ezen alkalmazások. Hozzon létre egy másik alkalmazás jelszavának nevű **asztali** számára ugyanazokat az alkalmazásokat, amelyek az asztali számítógépen futnak.

>[!NOTE]
>Javasoljuk, hogy hozzon létre eszközönként egy alkalmazásjelszót, nem pedig egy alkalmazásjelszót alkalmazásonként.

### <a name="federated-or-single-sign-on-app-passwords"></a>Összevont vagy egyszeri bejelentkezést az alkalmazásjelszavak

Az Azure AD egyszeri bejelentkezés (SSO), vagy összevonási támogatja a helyszíni Windows Server Active Directory tartományi szolgáltatások (AD DS). Ha a szervezet össze van vonva az Azure ad-vel, és az Azure multi-factor Authentication használata, fontolja meg az alkalmazások jelszavaival kapcsolatban a következő szempontokat.

>[!NOTE]
>A következő szempontokat csak összevont (SSO) ügyfelek érvényesek.

* Az Azure AD által ellenőrzött alkalmazásjelszókat, és emiatt az összevonási megkerülése. Összevonási aktívan szolgál, csak a alkalmazásjelszavak beállítása során.
* Az identitásszolgáltató (IdP) nem felvenni a kapcsolatot az összevont felhasználók (SSO), szemben a passzív folyammal. Az alkalmazásjelszavak vannak tárolva a munkahelyi vagy iskolai fiókjával. Ha egy felhasználó elhagyja a vállalatot, a felhasználói adatok elkezdenek beérkezni a munkahelyi vagy iskolai fiók segítségével **DirSync** valós időben. A fiók a letiltását/törlését órát is igénybe vehet legfeljebb három szinkronizálni, amely késleltetheti az alkalmazásjelszó letiltását/törlését az Azure ad-ben.
* A helyszíni ügyfél hozzáférés-vezérlés beállításai nem fogadja el a az alkalmazás jelszavak funkciót.
* Naplózás és naplózási képesség a helyszíni hitelesítés nem használható az alkalmazás jelszavak funkciót.
* Néhány speciális architektúrák hitelesítő kombinációját szükséges a kétlépéses ellenőrzéshez az ügyfelekkel. Ezeket a hitelesítő adatokat is tartalmazhat, egy munkahelyi vagy iskolai fiókhoz tartozó felhasználónév és a jelszavakat és alkalmazásjelszókat. A követelmények attól függ, hogyan megy végbe a hitelesítést. Az ügyfelek számára, amely egy helyszíni infrastruktúrát, a munkahelyi vagy iskolai fiókhoz tartozó felhasználónév és jelszó szükséges a hitelesítésre. Az ügyfelek hitelesítése az Azure ad-ben egy jelszót az alkalmazáshoz szükség.

  Tegyük fel például, a következő architektúra:

  * Az Active Directory helyszíni példányát az Azure AD össze van vonva.
  * Az Exchange online használ.
  * Skype vállalati helyszíni használ.
  * Az Azure multi-factor Authentication hitelesítést használ.

  Ebben a forgatókönyvben a következő hitelesítő adatok használata:

  * A Skype vállalati verzió bejelentkezni, használja a munkahelyi vagy iskolai fiókhoz tartozó felhasználónév és jelszó.
  * Az Outlook ügyfélprogram, amely az Exchange online csatlakozik a címjegyzék megnyitásához egy alkalmazásjelszót használni.

### <a name="allow-users-to-create-app-passwords"></a>Engedélyezése a felhasználók számára az alkalmazásjelszók létrehozását

Alapértelmezés szerint a felhasználók az alkalmazásjelszavak nem hozható létre. Az alkalmazás jelszavak funkciót engedélyezni kell. Lehetővé teheti a felhasználóknak alkalmazásjelszavakat, használja az alábbi eljárást:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **multi-factor Authentication**.
4. Válassza ki a multi-factor Authentication hitelesítés **Szolgáltatásbeállítások**.
5. A a **Szolgáltatásbeállítások** lapon válassza ki a **lehetővé teszik a felhasználók könnyedén hozhatnak létre alkalmazásjelszókat a böngészőn kívüli alkalmazások bejelentkezni** lehetőséget.

### <a name="create-app-passwords"></a>Alkalmazásjelszók létrehozása

Felhasználók hozhatnak létre alkalmazásjelszókat a kezdeti regisztráció során. A felhasználók számára a regisztrációs folyamat végén az alkalmazásjelszavak létrehozásának lehetősége.

Felhasználók is hozhatnak létre alkalmazásjelszókat regisztráció után. Az alkalmazásjelszavak módosíthatja a beállításokat az Azure portal vagy az Office 365 portálon keresztül. További információk és részletes lépéseket a felhasználók számára: [Mik az Azure multi-factor Authentication alkalmazásjelszókat?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Megbízható IP-címek

A _megbízható IP-címek_ felügyelt vagy összevont bérlők rendszergazdái által használt Azure multi-factor Authentication szolgáltatás. A funkció figyelmen kívül hagyja a kétlépéses ellenőrzés felhasználók számára, akik jelentkezzen be a vállalati intraneten. A szolgáltatás az Azure multi-factor Authentication teljes verzióját, és a nem rendszergazdák számára ingyenes verzió érhető el. További információ az Azure multi-factor Authentication teljes verziójának beszerzése: [Azure multi-factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> MFA megbízható IP-címek és a feltételes hozzáférés – nevesített helyek csak az IPV4-címekkel működik.

Ha a szervezete biztosít a helyszíni alkalmazások Megjegyzés MFA NPS-bővítményének üzembe helyezi a forrás IP-cím mindig megjelenik a hálózati házirend-kiszolgáló, a hitelesítési kísérlet végig kell.

| Az Azure AD-bérlő típusa | Megbízható IP-címek funkció beállításai |
|:--- |:--- |
| Managed |**Megadott IP-címek**: A rendszergazdák adjon meg egy IP-címtartományt, amely megkerülhetik a kétlépéses ellenőrzés felhasználók számára, akik jelentkezzen be a vállalati intraneten.|
| Összevont |**Az összes összevont felhasználók**: Jelentkezhessen be a szervezeten belüli összes összevont felhasználók megkerülhetik a kétlépéses ellenőrzést. A felhasználók az Active Directory összevonási szolgáltatások (AD FS) által kiadott jogcím ellenőrzés megkerülését.<br/>**Megadott IP-címek**: A rendszergazdák adjon meg egy IP-címtartományt, amely megkerülhetik a kétlépéses ellenőrzés felhasználók számára, akik jelentkezzen be a vállalati intraneten. |

A megbízható IP-címek megkerülése a vállalati intraneten belül csak a működését. Ha a **minden összevont felhasználó** lehetőséget és a egy felhasználó bejelentkezik a vállalati intraneten kívülről a felhasználó rendelkezik a kétlépéses ellenőrzés használatával történő hitelesítéshez. A folyamat megegyezik akkor is, ha a felhasználó megadja az AD FS-jogcímek. 

### <a name="end-user-experience-inside-of-corpnet"></a>Végfelhasználói élmény a vállalati hálózaton kívüli belül

Ha a megbízható IP-címek funkció le van tiltva, a kétlépéses ellenőrzés böngésző folyamatok szükség. Az alkalmazásjelszavak is szükséges a régebbi gazdagügyfél-alkalmazásokkal.

Ha a megbízható IP-címek szolgáltatás engedélyezve van, van-e a kétlépéses ellenőrzés *nem* böngésző folyamatok számára szükséges. Alkalmazásjelszókat *nem* régebbi gazdagügyfél-alkalmazásokkal, feltéve, hogy a felhasználó nem hozott létre egy jelszót az alkalmazáshoz szükséges. Miután egy alkalmazásjelszót használatban van, a jelszó szükséges marad. 

### <a name="end-user-experience-outside-corpnet"></a>Végfelhasználói élmény corpnet kívül

Függetlenül attól, hogy a megbízható IP-címek szolgáltatás engedélyezve van a kétlépéses ellenőrzés böngésző folyamatok szükség. Az alkalmazásjelszavak is szükséges a régebbi gazdagügyfél-alkalmazásokkal.

### <a name="enable-named-locations-by-using-conditional-access"></a>Nevesített helyek engedélyezi a feltételes hozzáférés használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **feltételes hozzáférési** > **nevesített helyek**.
3. Válassza ki **új helyre**.
4. Adja meg a hely nevét.
5. Válassza ki **megjelölés megbízható helyként**.
6. Adja meg az IP-címtartományt a CIDR-jelölésrendszerben például **192.168.1.1/24**.
7. Kattintson a **Létrehozás** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>A megbízható IP-címek funkció engedélyezése a feltételes hozzáférés használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **feltételes hozzáférési** > **nevesített helyek**.
3. Válassza ki **konfigurálása MFA megbízható IP-címek**.
4. Az a **Szolgáltatásbeállítások** lap **megbízható IP-címek**, bármelyik a következő két lehetőség közül választhat:

   * **Az összevont felhasználók intranetről származó**: Válassza ezt a lehetőséget, jelölje be a jelölőnégyzetet. Az összes összevont felhasználók, akik jelentkezzen be a vállalati hálózatról a kétlépéses ellenőrzés megkerülését az AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy az AD FS rendelkezik-e a megfelelő forgalmat az intranetes jogcím hozzáadása egy szabályt. Ha a szabály nem létezik, hozza létre a következő szabályt az AD FS-ben:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Egy nyilvános IP-címek tartományát kéréseit**: Ezt a lehetőséget a mezőben adja meg az IP-címeket a CIDR formátum használatával.
      * Az IP-címeket, amely a címtartomány xxx.xxx.xxx.1 xxx.xxx.xxx.254 keresztül vannak, használja a hasonló jelöléssel **xxx.xxx.xxx.0/24**.
      * Egyetlen IP-címet használja a hasonló jelöléssel **xxx.xxx.xxx.xxx/32**.
      * Adja meg legfeljebb 50 IP-címtartományok. Ezen IP-címek a bejelentkező felhasználóknak a kétlépéses ellenőrzés megkerülését.

5. Kattintson a **Mentés** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>A megbízható IP-címek funkció engedélyezéséhez a szolgáltatási beállítások használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **felhasználók**.
3. Válassza ki **multi-factor Authentication**.
4. Válassza ki a multi-factor Authentication hitelesítés **Szolgáltatásbeállítások**.
5. Az a **Szolgáltatásbeállítások** lap **megbízható IP-címek**, a következő két lehetőség egyikét (vagy mindkettőt):

   * **Az összevont felhasználók intranetről**: Válassza ezt a lehetőséget, jelölje be a jelölőnégyzetet. Az összes összevont felhasználók, akik jelentkezzen be a vállalati hálózatról a kétlépéses ellenőrzés megkerülését az AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy az AD FS rendelkezik-e a megfelelő forgalmat az intranetes jogcím hozzáadása egy szabályt. Ha a szabály nem létezik, hozza létre a következő szabályt az AD FS-ben:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **A megadott IP-címek alhálózatait kéréseit**: Ezt a lehetőséget a mezőben adja meg az IP-címeket a CIDR formátum használatával.
      * Az IP-címeket, amely a címtartomány xxx.xxx.xxx.1 xxx.xxx.xxx.254 keresztül vannak, használja a hasonló jelöléssel **xxx.xxx.xxx.0/24**.
      * Egyetlen IP-címet használja a hasonló jelöléssel **xxx.xxx.xxx.xxx/32**.
      * Adja meg legfeljebb 50 IP-címtartományok. Ezen IP-címek a bejelentkező felhasználóknak a kétlépéses ellenőrzés megkerülését.

6. Kattintson a **Mentés** gombra.

## <a name="verification-methods"></a>Ellenőrzési módszerek

Kiválaszthatja, hogy az ellenőrzési módszereket, amelyek a felhasználók számára érhető el. Az alábbi táblázat röviden bemutatja a módszereket biztosít.

Amikor a felhasználók regisztrálják fiókjukat az Azure multi-factor Authentication, azokat a beállításokat, amelyeket engedélyezett közül választhat az előnyben részesített ellenőrzési mód. A felhasználói regisztrációs folyamat útmutatást [a kétlépéses ellenőrzéshez a fiók beállítása](../user-help/multi-factor-authentication-end-user-first-time.md).

| Módszer | Leírás |
|:--- |:--- |
| Megadott telefonszám hívása |Automatikus hanghívást indít helyezi. A felhasználó fogadja a hívást, és a hitelesítéshez lenyomja a telefon billentyűzetén a # gombot. A telefonszám nem lesznek szinkronizálva a helyszíni Active Directoryban. |
| SMS küldése megadott telefonszámra |Egy ellenőrző kódot tartalmazó szöveges üzenetet küld. Kéri a felhasználót, hogy a bejelentkezési felületen meg kell adnia az ellenőrzőkódot. Ez a folyamat egyirányú SMS nevezzük. Kétirányú SMS azt jelenti, hogy a felhasználónak meg kell szöveg vissza egy adott kódblokkra. Kétirányú SMS elavult, és 2018. November 14. után nem támogatott. Felhasználók, akik kétirányú SMS automatikusan át konfigurált _telefonszámot_ ellenőrzés időpontjában.|
| Értesítés mobilalkalmazáson keresztül |Leküldéses értesítést küld a telefonjára vagy regisztrált készülékére. A felhasználó megtekinti az értesítést, és kiválasztja **ellenőrizze** ellenőrzés befejezéséhez. A Microsoft Authenticator alkalmazás érhető el az [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Mobilalkalmazás vagy hardvertoken ellenőrző kódja |A Microsoft Authenticator alkalmazás egy új OATH-ellenőrző kód 30 másodpercenként hoz létre. A felhasználó a bejelentkezési felületen kerül, az ellenőrző kódot. A Microsoft Authenticator alkalmazás érhető el az [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Engedélyezheti vagy letilthatja a hitelesítési módszerek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **multi-factor Authentication**.
4. Válassza ki a multi-factor Authentication hitelesítés **Szolgáltatásbeállítások**.
5. Az a **Szolgáltatásbeállítások** lap **ellenőrzési lehetőségek**, válassza ki vagy törölje a módszerek segítségével biztosíthat a felhasználóknak.
6. Kattintson a **Save** (Mentés) gombra.

Hitelesítési módszerek használatával kapcsolatos további részleteket a cikkben található [hitelesítési módszerei](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication megjegyzése

A _multi-factor Authentication megjegyzése_ eszközök és böngészők, amely a felhasználó által megbízhatónak tartott a funkció egy olyan ingyenes szolgáltatás minden multi-factor Authentication-felhasználók számára. Felhasználók utólagos jogosultak a megadott számú napokon történt, miután azok már sikeresen bejelentkezett egy eszközön a multi-factor Authentication szolgáltatás használatával. A funkció javítja a használhatósági csökkenthetik a szám, ahányszor a felhasználó rendelkezik ugyanazon az eszközön a kétlépéses ellenőrzés végrehajtására.

>[!IMPORTANT]
>Ha egy fiókot, vagy az eszköz biztonsága sérül, a multi-factor Authentication megjegyzése megbízható eszközökön befolyásolhatják biztonsági. Ha egy vállalati fiók biztonságának sérülése esetén, vagy egy megbízható eszköz elvész vagy ellopják, akkor [többtényezős hitelesítés visszaállítása az összes eszközön](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>A visszaállítási művelet visszavonja a megbízható állapota az összes eszközről, és a felhasználó kétlépéses ellenőrzés visszakapcsolásához végrehajtásához szükséges. Is utasíthatja a felhasználókat, hogy a többtényezős hitelesítés visszaállítása az utasítások a saját eszközökön [felügyelheti a kétlépéses ellenőrzés](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>A funkció működése

A tárolása a multi-factor Authentication szolgáltatás egy állandó cookie-t a böngészőben állítja be, amikor egy felhasználó kijelöli a **ne jelenjen meg többé az X nap** bejelentkezéskor lehetőséget. A nem kéri újra a multi-factor Authentication ugyanazzal a böngészővel a mindaddig, amíg a cookie lejár. Ha a felhasználó megnyit egy másik böngészőben ugyanazon az eszközön, vagy törli a cookie-kat, újra kell adnia ellenőrzése.

A **ne jelenjen meg többé az X nap** beállítás nem jelenik meg a böngészőn kívüli alkalmazások, függetlenül attól, hogy az alkalmazás támogatja-e a modern hitelesítést. Ezek az alkalmazások használata _frissítési jogkivonatok_ , amely új hozzáférési jogkivonatokat biztosítson óránként. A frissítési jogkivonatok érvényesítve van, az Azure AD ellenőrzi a, hogy történt-e az utolsó kétlépéses ellenőrzést a megadott számú napon belül.

A funkció csökkenti a web apps szolgáltatásban, amely minden alkalommal általában kérni a hitelesítések számát. A funkció növeli a modern hitelesítési ügyfelek, amelyek általában kéri a 90 naponta hitelesítések számát. Feltételes hozzáférési szabályzatok kombinálva hitelesítések számát is növelhető.

>[!IMPORTANT]
>A **multi-factor Authentication megjegyzése** funkció, nem kompatibilis a **bejelentkezve szeretnék maradni** az AD FS, ha felhasználók végrehajtani a kétlépéses ellenőrzés keresztül az Azure multi-factor Authentication AD FS szolgáltatása Hitelesítési kiszolgáló vagy egy harmadik felek többtényezős hitelesítési megoldás.
>
>Ha a felhasználók a **bejelentkezve szeretnék maradni** az AD FS és is be van jelölve, a multi-factor Authentication megbízható, az eszközt, a felhasználó nem automatikusan ellenőrzése után a **multi-factorauthenticationmegjegyzése**lejár a napok számát. Az Azure AD-kérelmek friss kétlépéses ellenőrzést, de az AD FS és az eredeti multi-factor Authentication jogcím és a dátum, nem pedig teljesítő kétlépéses ellenőrzés visszakapcsolásához jogkivonatot ad vissza. **A visszajelzés állítja ki egy ellenőrzési hurok között az Azure AD és az AD FS.**
>

### <a name="enable-remember-multi-factor-authentication"></a>Engedélyezze a multi-factor Authentication megjegyzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **multi-factor Authentication**.
4. Válassza ki a multi-factor Authentication hitelesítés **Szolgáltatásbeállítások**.
5. Az a **Szolgáltatásbeállítások** lapon **kezelése multi-factor authentication megjegyzése**, jelölje be a **engedélyezése a felhasználók számára a multi-factor authentication megjegyzése eszközökönmegbíznak**lehetőséget.
6. A kétlépéses ellenőrzés megkerülését megbízható eszközök napok számának megadása. Az alapértelmezett érték 14 nap.
7. Kattintson a **Mentés** gombra.

### <a name="mark-a-device-as-trusted"></a>Egy eszköz akkor megbízható megjelölése

Miután engedélyezte a tárolása a multi-factor Authentication szolgáltatás, felhasználók jelölheti meg, ha megbízható eszköz kiválasztásával bejelentkezés **ne kérdezzen rá ismét**.

## <a name="next-steps"></a>További lépések

[Az Azure AD bejelentkezési oldal márkajelzési módosítása](../fundamentals/customize-branding.md)