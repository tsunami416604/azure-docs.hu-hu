---
title: Az Azure többtényezős hitelesítés beállítása |} Microsoft Docs
description: Ez a cikk ismerteti az Azure multi-factor Authentication beállításainak jelentések, visszaélési riasztás, egyszeri mellőzés, egyéni hangüzenetek, gyorsítótárazás, a megbízható IP-címek és az alkalmazásjelszókat.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4259715fbb062bfb170509c928b5a1ad898ea394
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure multi-factor Authentication beállításainak konfigurálása

Ez a cikk segít Azure multi-factor Authentication kezeléséhez most, hogy működik, és Ön. Bemutatja, különböző témakörök, amelyek segítenek az Azure multi-factor Authentication minél hatékonyabb működtetését. Nem minden szolgáltatását érhetők el minden [Azure multi-factor Authentication verziójának](multi-factor-authentication-get-started.md#what-features-do-i-need).

| Szolgáltatás | Leírás | 
|:--- |:--- |
| [Blokkolnia és feloldania felhasználók](#block-and-unblock-users) |A felhasználók blokkolása/feloldása szolgáltatás segítségével megakadályozhatja, hogy a felhasználók hitelesítési kérelmek megkapja. |
| [Csalási riasztás](#fraud-alert) |Konfigurálja a csalási riasztás szolgáltatás úgy, hogy a felhasználók jelenthetik-e rosszindulatú megpróbál hozzáférni az erőforrásokhoz. |
| [Az egyszeri Mellőzés](#one-time-bypass) |Az egyszeri Mellőzés szolgáltatással a felhasználók által egy alkalommal a hitelesítést _kihagyásával_ többtényezős hitelesítést. |
| [Egyedi Hangüzenetek](#custom-voice-messages) |A szolgáltatással egyedi Hangüzenetek üzeneteket a saját felvételek vagy a hónap és a többtényezős hitelesítés. |
| [Caching](#caching-in-azure-multi-factor-authentication) |A gyorsítótárazási szolgáltatás használatával beállíthat egy adott időszakra vonatkozóan, így a későbbi hitelesítési próbálkozások automatikusan sikeres. |
| [Megbízható IP-címek](#trusted-ips) |A felügyelt vagy összevont bérlők rendszergazdái a megbízható IP-címek szolgáltatás segítségével a felhasználók számára a vállalati intraneten jelentkezzen be a kétlépéses ellenőrzés megkerülését. |
| [Alkalmazásjelszók](#app-passwords) |Az alkalmazás jelszó szolgáltatás használatával lehetővé teszik az alkalmazások az választhatják a multi-factor Authentication, és folytathatja a munkát. |
| [Ne feledje többtényezős hitelesítés a megbízható eszközök és böngészők](#remember-multi-factor-authentication-for-trusted-devices) |E szolgáltatás használatával megbízható eszközökkel és a böngészők tárolja egy meghatározott számú nap elteltével a felhasználó rendelkezik-e sikeresen bejelentkezett a multi-factor Authentication használatával. |
| [Választható hitelesítési módszerek](#selectable-verification-methods) |Ez a szolgáltatás segítségével válassza ki a hitelesítési módszereket, amelyek lehetővé tevő használja listáját. |

## <a name="block-and-unblock-users"></a>Blokkolnia és feloldania felhasználók

Használja a _blokkolnia és feloldania felhasználók_ szolgáltatás megakadályozhatja, hogy a felhasználók a hitelesítési kérelmek fogadását. Letiltott felhasználók számára bármely hitelesítési kísérleteit a rendszer automatikusan blokkolja. Így a letiltott 90 napig, hogy azok le vannak tiltva.

### <a name="block-a-user"></a>Felhasználó blokkolása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA kiszolgáló** > **felhasználó blokkolása/feloldása**.
3. Válassza ki **Hozzáadás** a felhasználó blokkolását.
4. Válassza ki a **replikációs csoport**. Adja meg a felhasználónevet a letiltott felhasználónak **felhasználónév<span></span>@domain.com**. Adja meg a megjegyzéseit a **OK** mező.
5. Válassza ki **Hozzáadás** a felhasználó letiltásának befejezéséhez.

### <a name="unblock-a-user"></a>Felhasználó blokkolásának feloldása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA kiszolgáló** > **felhasználó blokkolása/feloldása**.
3. Válassza ki **Unblock** a a **művelet** a felhasználó tiltásának feloldása melletti oszlopra.
4. Adja meg a megjegyzéseit a **vonatkozó tiltás feloldásának okból** mező.
5. Válassza ki **Unblock** a felhasználó blokkolásának feloldása befejezéséhez.

## <a name="fraud-alert"></a>Visszaélési riasztás

Konfigurálja a _csalási riasztás_ a beállítást, így a felhasználók jelenthetik-e rosszindulatú megpróbál hozzáférni az erőforrásokhoz. A mobilalkalmazás vagy a telefont keresztül a felhasználók jelenthetik csalás kísérletek.

### <a name="turn-on-fraud-alerts"></a>Csalási riasztás bekapcsolása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA kiszolgáló** > **csalási riasztás**.

   ![Csalási riasztás bekapcsolása](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Állítsa be a **engedélyezése a felhasználók számára visszaélési riasztás** beállítást **a**.
4. Kattintson a **Mentés** gombra.

### <a name="configuration-options"></a>Konfigurációs beállítások

- **Felhasználó blokkolása visszaélés jelentésekor**: Ha egy felhasználó csalás, a fiókja mindaddig zárolva van 90 napig, vagy amíg a rendszergazda feloldja a fiókba. A rendszergazda bejelentkezések áttekintheti a bejelentkezési jelentés használatával, és hajtsa végre a megfelelő műveletet jövőbeli csalás megelőzése érdekében. A rendszergazda tudja majd [feloldása](#unblock-a-user) a felhasználói fiók.
- **Kód a visszaélés jelentéséhez a kezdeti üdvözlés során**: amikor a felhasználók felhívja Önt telefonján kétlépéses ellenőrzés, akkor általában nyomja le az ENTER **#** a bejelentkezés megerősítéséhez. A visszaélés jelentéséhez a felhasználó megadja a kód előtt **#**. Ez a kód **0** alapértelmezés szerint azonban testre szabható.

  >[!NOTE]
  >Az alapértelmezett hangüdvözléseit Microsoft utasítsa a felhasználókat, hogy nyomja meg az **0#** a csalási értesítést küldeni. Ha egy kódot eltérő használni kívánt **0**, jegyezze fel, és töltse fel a megfelelő utasításokat tartalmaz a felhasználók a saját egyéni hangüdvözléseit.
  >

### <a name="view-fraud-reports"></a>Csalás jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **az Azure Active Directory** > **bejelentkezések**. A csalások jelentés mostantól az Azure AD-bejelentkezések normál jelentés részét képezi.

## <a name="one-time-bypass"></a>Egyszeri mellőzés

A _az egyszeri Mellőzés_ funkció lehetővé teszi, hogy a felhasználó egy alkalommal kétlépéses ellenőrzés végrehajtása nélkül. A Mellőzés átmeneti, és a megadott számú másodperc után lejár. Olyan esetekben, ahol a mobilalkalmazás vagy a telefon nem kap egy értesítést vagy a telefonhívás engedélyezheti az egyszeri mellőzés, így a felhasználó hozzáférhet a kívánt erőforrás.

### <a name="create-a-one-time-bypass"></a>Az egyszeri Mellőzés létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA kiszolgáló** > **az egyszeri Mellőzés**.

   ![Az egyszeri Mellőzés létrehozása](./media/multi-factor-authentication-whats-next/onetimebypass.png)

3. Válassza a **Hozzáadás** lehetőséget.
4. Ha szükséges, válassza ki a replikációs csoport a Mellőzés.
5. Megadja a felhasználónevét, **felhasználónév<span></span>@domain.com**. Adja meg másodpercben, hogy a Mellőzés kell utoljára. Adja meg a mellőzés okát. 
6. Válassza a **Hozzáadás** lehetőséget. Az időkorlát azonnal működésbe lép. A felhasználónak be kell jelentkeznie az egyszeri Mellőzés lejárta előtt. 

### <a name="view-the-one-time-bypass-report"></a>Az egyszeri Mellőzés jelentés megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a **Active Directory** > **MFA kiszolgáló** > **az egyszeri Mellőzés**.

## <a name="custom-voice-messages"></a>Egyedi Hangüzenetek
A kétlépéses ellenőrzéshez a használhatja a saját felvételek vagy hónap a _egyedi Hangüzenetek_ szolgáltatás. Ezek az üzenetek ezen kívül, vagy cserélje le a Microsoft felvétel használható.

Mielőtt elkezdené, vegye figyelembe a következő korlátozások vonatkoznak:

* A támogatott formátumok a következők: .wav és .mp3.
* A fájlok méretkorlátját érték 5 MB.
* Hitelesítési üzenetek 20 másodperc karakternél rövidebbnek kell lennie. 20 másodpercnél tovább üzenetek okozhat az ellenőrzés sikertelen lesz. A felhasználó befejezi az üzenet és az ellenőrzés időtúllépése előtt nem válaszol.

### <a name="set-up-a-custom-message"></a>Állítson be egy egyéni üzenet

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA kiszolgáló** > **telefonhívás beállítások**.

   ![Rekord egyéni telefonos üzenetek](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Válassza ki **Hozzáadás üdvözlés**.
4. Válassza ki az üdvözlőlap. Válassza ki azt a nyelvet.
5. Válasszon egy .mp3, .wav vagy hangfájl feltöltése.
6. Válassza a **Hozzáadás** lehetőséget.

## <a name="caching-in-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication hitelesítési gyorsítótárazás

Egy adott időszakban engedélyezi a hitelesítési kísérlet után a felhasználók hitelesítése használatával beállíthatja a _gyorsítótárazás_ szolgáltatás. További hitelesítési kísérleteket a felhasználó a megadott belül automatikusan sikeres időszakra vonatkozóan. Gyorsítótárazás elsősorban a helyszíni rendszerekre, például VPN, több ellenőrzési kérés küldött, miközben még folyamatban van az első kérésre. Ez a funkció lehetővé teszi, hogy automatikusan, sikeres, a felhasználó sikeres folyamatban az első ellenőrzést követően a későbbi kérelmeket. 

>[!NOTE]
>A gyorsítótárazási szolgáltatás célja nem használható az Azure Active Directory (Azure AD) bejelentkezéseket.

### <a name="set-up-caching"></a>Gyorsítótárazás beállítása 

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg a **Azure Active Directory** > **MFA kiszolgáló** > **szabályok gyorsítótárazás**.

   ![Gyorsítótárazás szabályok beállítása](./media/multi-factor-authentication-whats-next/cachingrules.png)

3. Válassza a **Hozzáadás** lehetőséget.
4. Válassza ki a **gyorsítótár típusa** a legördülő listából. Adja meg a maximális számát **másodperc gyorsítótár**. 
5. Ha szükséges, válassza ki a hitelesítés típusát, és adjon meg egy alkalmazást. 
6. Válassza a **Hozzáadás** lehetőséget.


## <a name="trusted-ips"></a>Megbízható IP-címek

A _megbízható IP-címek_ a felügyelt vagy összevont bérlők rendszergazdái által használt Azure multi-factor Authentication szolgáltatást. A funkció nincs hatással a kétlépéses ellenőrzést, a felhasználók számára, akik jelentkezzen be a vállalati intraneten. A szolgáltatás esetén érhető el az Azure multi-factor Authentication teljes verzióját, és nem rendszergazdák számára ingyenes verziót. Hogyan kérhet az Azure multi-factor Authentication teljes verzióját a részletekért lásd: [Azure multi-factor Authentication](multi-factor-authentication.md).

| Az Azure AD bérlő típusa | Megbízható IP-címek szolgáltatás beállításai |
|:--- |:--- |
| Felügyelt |**Egyedi IP-címek**: a rendszergazdák, adjon meg egy adott IP-címek, amelyek jogosultak a felhasználók számára a vállalati intraneten jelentkezzen be a kétlépéses ellenőrzést.|
| Összevont |**Minden összevont felhasználók**: Jelentkezzen be a a szervezeten belüli összes összevont felhasználók jogosultak a kétlépéses ellenőrzést. A felhasználók Active Directory összevonási szolgáltatások (AD FS) által kiadott jogcím használatával ellenőrzés kihagyása.<br/>**Egyedi IP-címek**: a rendszergazdák, adjon meg egy adott IP-címek, amelyek jogosultak a felhasználók számára a vállalati intraneten jelentkezzen be a kétlépéses ellenőrzést. |

A megbízható IP-címek megkerülése a vállalati intraneten belül csak akkor működik. Ha bejelöli a **minden összevont felhasználók** beállítás és a felhasználó bejelentkezik a vállalati intraneten kívül a felhasználó rendelkezik-e a hitelesítéshez használja a kétlépéses ellenőrzést. A folyamat megegyezik akkor is, ha a felhasználó megadja egy AD FS jogcímet. 

**Végfelhasználói élmény a vállalati hálózat belül**

A megbízható IP-címek funkció le van tiltva, ha a kétlépéses ellenőrzést böngésző adatfolyamok szükség. Alkalmazásjelszók régebbi gazdagügyfél-alkalmazások szükségesek. 

Ha a megbízható IP-címek szolgáltatás engedélyezve van, van-e a kétlépéses ellenőrzést *nem* böngésző adatfolyamok szükséges. Alkalmazásjelszókat *nem* régebbi gazdagügyfél-alkalmazások esetében szükséges, feltéve, hogy a felhasználó még nem létrehozott jelszót. Miután jelszót használja, a jelszó szükséges marad. 

**Végfelhasználói élmény corpnet kívül**

Függetlenül attól, hogy a megbízható IP-címek szolgáltatás engedélyezve van kétlépéses ellenőrzés böngésző adatfolyamok szükség. Alkalmazásjelszók régebbi gazdagügyfél-alkalmazások szükségesek. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Elnevezett helyek engedélyezze a feltételes hozzáférés

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **feltételes hozzáférés** > **helyek nevű**.
3. Válassza ki **új helyre**.
4. Adja meg a hely nevét.
5. Válassza ki **megbízható helyen megjelölés**.
6. Adja meg az IP-címtartományt a CIDR-formátumban, például a **192.168.1.1/24**.
7. Kattintson a **Létrehozás** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>A megbízható IP-címek szolgáltatás engedélyezze a feltételes hozzáférés

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **feltételes hozzáférés** > **helyek nevű**.
3. Válassza ki **konfigurálja MFA megbízható IP-címek**.
4. Az a **Szolgáltatásbeállítások** lap **megbízható IP-címek**, a következő két lehetőség közül választhat:
   
   * **Az összevont felhasználók intranetről származó kérelmeknél**: válassza ezt a lehetőséget, jelölje be a jelölőnégyzetet. Minden összevont felhasználók, akik jelentkezzen be a vállalati hálózatról megkerülése kétlépéses ellenőrzés az AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy az AD FS rendelkezik-e a megfelelő forgalmat az intranetes jogcím hozzáadása egy szabályt. Ha a szabály nem létezik, hozza létre a következő szabályt az AD FS-ben:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **A nyilvános IP-címek meghatározott számos érkező kéréseket**: válassza ezt a beállítást, a mezőben adja meg az IP-címek a CIDR jelölésrendszer szerint.
   
     * A tartomány xxx.xxx.xxx.1 keresztül xxx.xxx.xxx.254 lévő IP-címekhez, használja a jelölés például **xxx.xxx.xxx.0/24**.
     * Egyetlen IP-cím, használja a jelölés például **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Adjon meg legfeljebb 50 IP-címtartományok. Jelentkezzen be az alábbi IP-címekről érkező felhasználók kétlépéses ellenőrzés megkerülését.

5. Kattintson a **Mentés** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>A megbízható IP-címek funkció engedélyezése a szolgáltatás beállításainak használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**.
4. A multi-factor Authentication területen válassza a **szolgáltatás beállításai**.
5. Az a **Szolgáltatásbeállítások** lap **megbízható IP-címek**, a következő két lehetőség közül választhat:
   
   * **Összevont felhasználók intranetről indított kérelmeknél**: válassza ezt a lehetőséget, jelölje be a jelölőnégyzetet. Minden összevont felhasználók, akik jelentkezzen be a vállalati hálózatról megkerülése kétlépéses ellenőrzés az AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy az AD FS rendelkezik-e a megfelelő forgalmat az intranetes jogcím hozzáadása egy szabályt. Ha a szabály nem létezik, hozza létre a következő szabályt az AD FS-ben:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **A megadott IP-címek alhálózatai érkező kérésekhez**: válassza ezt a beállítást, a mezőben adja meg az IP-címek a CIDR jelölésrendszer szerint. 
     
     * A tartomány xxx.xxx.xxx.1 keresztül xxx.xxx.xxx.254 lévő IP-címekhez, használja a jelölés például **xxx.xxx.xxx.0/24**.
     * Egyetlen IP-cím, használja a jelölés például **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Adjon meg legfeljebb 50 IP-címtartományok. Jelentkezzen be az alábbi IP-címekről érkező felhasználók kétlépéses ellenőrzés megkerülését.

6. Kattintson a **Mentés** gombra.

![A Szolgáltatásbeállítások megbízható IP-címek engedélyezése](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Alkalmazásjelszavak

Egyes alkalmazások, például az Office 2010 vagy korábbi és az Apple Mail nem támogatják a kétlépéses ellenőrzést. Az alkalmazások nincs konfigurálva. a második ellenőrzési fogadásához. Ezek az alkalmazások használatához előnyeit a _alkalmazásjelszók_ szolgáltatás. A hagyományos jelszó helyett egy alkalmazásjelszót segítségével az alkalmazás figyelmen kívül hagyása a kétlépéses ellenőrzést, és folytathatja a munkát.

A modern hitelesítést is támogatja, a Microsoft Office 2013-ügyfelek és újabb verziók. Office 2013 Outlook, beleértve az ügyfelek támogatják a modern hitelesítési protokollok megvalósítását végzi, és a kétlépéses ellenőrzéshez használttal működéséhez engedélyezni kell. Miután az ügyfél engedélyezve van, alkalmazásjelszók nem lesz szükség az ügyfél számára.

### <a name="considerations-about-app-passwords"></a>Tudnivalók az alkalmazásjelszókról kapcsolatos szempontok

Alkalmazásjelszavak használata esetén vegye figyelembe a következő fontos szempontokat:

* Az alkalmazásjelszók csak egyszer megadott alkalmazásonként. Felhasználók nyomon követjük, hogy a jelszavak, vagy minden alkalommal megadniuk azokat nem kell.
* A tényleges jelszót automatikusan jön létre, és a felhasználó által nem biztosított. Az automatikusan generált jelszót nehezebben kitalálható támadók, és biztonságosabb.
* Egy legfeljebb 40 jelszó felhasználónként van. 
* Sikertelen, mert az alkalmazásjelszó nem ismeri a munkahelyi vagy iskolai fiókkal kívül szükséges alkalmazásokat, amelyek a jelszavuk gyorsítótárazása és helyszíni forgatókönyvekben használhatóak. Ebben a forgatókönyvben például a helyszíni Exchange e-maileket, de az archivált levelek a felhőben. Ebben a forgatókönyvben ugyanazt a jelszót nem működik.
* Többtényezős hitelesítés a felhasználói fiók engedélyezése után alkalmazásjelszók használható a böngészőn kívüli ügyfelekkel, például az Outlook és a Microsoft Skype vállalati. Felügyeleti műveletek alkalmazásjelszókat a böngészőn kívüli alkalmazások, például a Windows PowerShell használatával nem hajtható végre. A művelet nem hajtható végre, akkor is, ha a felhasználó rendelkezik-e rendszergazdai fiókokkal. PowerShell-parancsfájlok futtatásához egy erős jelszót hozzon létre egy szolgáltatásfiókot, és ne engedélyezze a fiókot, a kétlépéses ellenőrzéshez.

>[!WARNING]
>Alkalmazásjelszók hibrid környezetekben, ahol az ügyfelek kommunikálni mind a helyszíni és a felhőalapú automatikus észlelési végpontok nem működnek. Tartományi jelszó végeznek helyszíni hitelesítést igényel. Alkalmazásjelszók szükségesek a felhőben való hitelesítéshez szükséges.
>

### <a name="guidance-for-app-password-names"></a>Útmutató a jelszó alkalmazásnevek

Jelszó alkalmazásnevek egyeznie kell az eszközt, amelyen ezek mire szolgál. Ha a hordozható számítógép, amelyen a böngészőn kívüli alkalmazások, például az Outlook, a Word és Excel, hozzon létre egy alkalmazásjelszót nevű **hordozható** ezekhez az alkalmazásokhoz. Adjon meg egy másik alkalmazás jelszót nevű **asztali** az asztali számítógépen futó alkalmazásokhoz. 

>[!NOTE]
>Azt javasoljuk, hogy hozzon létre egy alkalmazásjelszót alkalmazásonként helyett eszközönként egy alkalmazásjelszót.

### <a name="federated-or-single-sign-on-app-passwords"></a>Egyetlen vagy összevont bejelentkezés alkalmazásjelszók

Az Azure AD összevonási vagy egyszeri bejelentkezés (SSO) támogatja a helyszíni Windows Server Active Directory tartományi szolgáltatások (AD DS). Ha a szervezet az Azure AD össze van vonva, és az Azure multi-factor Authentication használata, vegye figyelembe a következő szempontokat alkalmazásjelszókról.

>[!NOTE]
>A következők vonatkoznak csak összevont (SSO) vonatkozik.

* Az Azure AD által ellenőrzött alkalmazásjelszókat, és ezért összevonási. Összevonási használható aktívan csak alkalmazásjelszók beállítása során.
* Az identitásszolgáltató (IdP) nem érhető el, az összevont felhasználók (SSO), szemben a passzív folyammal. Az alkalmazásjelszók tárolódnak a munkahelyi vagy iskolai fiókkal. Ha egy felhasználó elhagyja a vállalatot, a felhasználói adatok zajló kommunikációról a munkahelyi vagy iskolai fiók segítségével **DirSync** valós időben. A fiók letiltása/törlése órát is igénybe vehet legfeljebb három szinkronizálni, amelyek késleltetheti-e az alkalmazásjelszó letiltását/törlését az Azure ad-ben.
* A helyszíni ügyfél hozzáférés-vezérlési beállításokat nem ezt a funkciót az alkalmazások jelszavak funkciói.
* Naplózási/naplózási képesség a helyszíni hitelesítés nem használható az alkalmazás jelszavak szolgáltatással.
* Néhány speciális architektúrák a hitelesítő adatok kombinációja szükséges a kétlépéses ellenőrzéshez az ügyfelekkel. Ezek a hitelesítő adatok lehetnek munkahelyi vagy iskolai fiók felhasználónevét és a jelszavak és alkalmazásjelszókat. A követelmények attól függenek, hogyan történik a hitelesítés. A hitelesítést, a helyszíni infrastruktúra-ügyfelek esetében a munkahelyi vagy iskolai fiók felhasználónevét és jelszavát egy szükséges. A hitelesítést, az Azure AD-ügyfelek esetében az alkalmazásjelszó megadása kötelező.

  Tegyük fel például, hogy rendelkezik-e a következő architektúra:

  * Az Active Directory helyszíni példányát az Azure AD össze van vonva.
  * Exchange online használ.
  * Skype vállalati helyszíni használja.
  * Azure multi-factor Authentication használata.

  ![Egy összevont szervezetben alkalmazásjelszók használatával](./media/multi-factor-authentication-whats-next/federated.png)

  Ebben a forgatókönyvben a következő hitelesítő adatokat használ:

  * A Skype vállalati verzióhoz való bejelentkezéshez használja a munkahelyi vagy iskolai fiók felhasználónevét és jelszavát.
  * Az Outlook ügyfélprogram, amely összeköti az Exchange online a címjegyzék megnyitásához használja az alkalmazásjelszó.

### <a name="allow-users-to-create-app-passwords"></a>Felhasználók alkalmazásjelszavak létrehozásának engedélyezése

Alapértelmezés szerint a felhasználók nem hozhatják létre alkalmazásjelszókat. Az alkalmazás jelszavak szolgáltatást engedélyezni kell. Lehetőséget nyújt a felhasználóknak az alkalmazásjelszavak létrehozásának képessége, a következő eljárással:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**.
4. A multi-factor Authentication területen válassza a **szolgáltatás beállításai**.
5. Az a **Szolgáltatásbeállítások** lapon jelölje be a **engedélyezése a felhasználóknak alkalmazásjelszavakat bejelentkezni a böngészőn kívüli alkalmazásokat** lehetőséget.

   ![Felhasználók alkalmazásjelszavak létrehozásának engedélyezése](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Alkalmazásjelszók létrehozása

Felhasználók hozhatnak létre alkalmazásjelszókat a kezdeti regisztráció során. A felhasználó a regisztrációs folyamat végén alkalmazásjelszavak létrehozásának lehetősége van.

Felhasználók emellett létrehozhatják alkalmazásjelszók regisztrálás után. Az alkalmazásjelszók módosíthatja a beállításokat az Azure-portálon vagy az Office 365 portálon keresztül. További információk és részletes lépéseket a felhasználók számára: [Mik az Azure multi-factor Authentication alkalmazásjelszókat?](./end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Ne feledje a multi-factor Authentication megbízható eszközök
A _ne feledje a multi-factor Authentication_ az eszközök és a felhasználó által megbízhatónak böngészők funkció lehetővé teszi az ingyenes minden multi-factor Authentication-felhasználók számára. A megadott számú napig, miután azok már sikeresen bejelentkezett egy eszközön a multi-factor Authentication használatával utólagos felhasználók jogosultak. A szolgáltatás továbbfejleszti a használhatóság csökkentésével a szám, ahányszor a felhasználó által végrehajtandó kétlépéses ellenőrzés az adott eszközön.

>[!IMPORTANT]
>Ha egy fiók vagy eszköz biztonsága sérül, a multi-factor Authentication jelszóelőzmények megbízható eszközök hatással lehet a biztonsági. Ha egy vállalati fiók biztonságának sérülése esetén vagy egy megbízható eszköz elvesztésekor vagy ellopásakor, akkor [többtényezős hitelesítés visszaállítása az összes eszközön](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>A restore művelettel visszavonja az összes eszköz megbízható állapotát, és a felhasználó kétlépéses ellenőrzés visszakapcsolásához elvégzéséhez szükséges. Azt is beállíthatja, hogy a felhasználók a multi-factor Authentication visszaállítása a saját eszközök utasításait [kezelheti a kétlépéses ellenőrzés beállításait](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>A szolgáltatás működése

A felhasználó megjegyzését multi-factor Authentication szolgáltatás állandó cookie böngésző állítja be, ha a felhasználó megadja a **ne jelenjen meg többé az X nap** bejelentkezéskor a beállítást. A nem kéri újra a multi-factor Authentication ugyanazt a böngészőt a mindaddig, amíg a cookie lejár. Ha a felhasználó megnyit egy másik böngészőben ugyanarra az eszközre, vagy törli a cookie-k, azok felkéri újra ellenőrzése. 

A **ne jelenjen meg többé az X nap** lehetőség nem jelenik meg a böngészőn kívüli alkalmazások, függetlenül attól, hogy az alkalmazás támogatja-e a modern hitelesítést. Ezek az alkalmazások használata _frissítési jogkivonatok_ , adjon meg új hozzáférési jogkivonatok óránként. Egy frissítési jogkivonat ellenőrzését az Azure AD ellenőrzi, hogy történt-e az utolsó kétlépéses ellenőrzés a megadott számú napon belül. 

A funkció csökkenti a webalkalmazásokban, amelyek általában a minden alkalommal kéri a hitelesítések számát. A funkció növeli a modern hitelesítési ügyfelek, amelyek általában a 90 naponta kérni hitelesítések számát.

>[!IMPORTANT]
>A **ne feledje a multi-factor Authentication** funkció nincs kompatibilis a **bejelentkezve szeretnék maradni** az AD FS-ben, ha felhasználók végre az AD FS Azure multi-factor Authentication segítségével kétlépéses ellenőrzést szolgáltatása Hitelesítési kiszolgáló vagy egy külső a multi-factor authentication megoldást.
>
>Ha a felhasználók **bejelentkezve szeretnék maradni** az AD FS és az is, az eszköz megbízható a többtényezős hitelesítés be van jelölve, a felhasználó nem automatikusan ellenőrzése után a **ne feledje, a multi-factor authentication**napok száma, amely lejár. Az Azure AD egy friss kétlépéses ellenőrzést igényel, de az AD FS eredeti multi-factor Authentication jogcím és a dátum, helyett a teljesítő kétlépéses ellenőrzés visszakapcsolásához jogkivonatot ad vissza. Ki az Azure közötti ellenőrzési hurkot beállítja a reakciót AD és az AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Engedélyezze a multi-factor Authentication megjegyzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**.
4. A multi-factor Authentication területen válassza a **szolgáltatás beállításai**.
5. A a **Szolgáltatásbeállítások** lap **kezelése ne feledje, a multi-factor authentication**, jelölje be a **a felhasználók menthetik a többtényezős hitelesítés eszközökönmegbízhatóságát**lehetőséget.

   ![Ne feledje a multi-factor Authentication megbízható eszközök](./media/multi-factor-authentication-whats-next/remember.png)

6. Állítsa be a kétlépéses ellenőrzés elkerülésére megbízható eszközök napok számát. Az alapértelmezett érték 14 nap.
7. Kattintson a **Mentés** gombra.

### <a name="mark-a-device-as-trusted"></a>Jelölje meg megbízhatóként eszköz

A felhasználó megjegyzését multi-factor Authentication szolgáltatás engedélyezése után felhasználók jelölheti meg, ha megbízható eszköz kiválasztásával bejelentkeznek **rákérdezés nélkül**.

![Válassza a "Ne jelenjen meg többé" megbízható eszközök](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Választható hitelesítési módszerek

Választhat, hogy az ellenőrzési módszereket, amelyek segítségével a felhasználók számára elérhetők a _választható hitelesítési módszerek_ szolgáltatás. A következő táblázat a módszerek rövid áttekintést nyújt.

Amikor a felhasználók beléptetik a fiókok az Azure multi-factor Authentication, azok az, hogy engedélyezte a lehetőségek közül választhat a kedvenc hitelesítési módszer. A felhasználói regisztrációs folyamat útmutatást [a kétlépéses ellenőrzéshez a fiók beállítása](multi-factor-authentication-end-user-first-time.md).

| Módszer | Leírás |
|:--- |:--- |
| Megadott telefonszám hívása |Egy automatizált hang hívás helyezi. A felhasználó fogadja a hívást, és a telefon billentyűzetén hitelesíteni kell nyomnia a #. A telefonszám nem szinkronizálja a helyszíni Active Directory. |
| SMS küldése megadott telefonszámra |Egy megerősítési kódot tartalmazó szöveges üzenetet küld. Kéri a felhasználót, hogy a bejelentkezési felületen meg kell adnia az ellenőrző kódot. A folyamat elnevezése egyirányú SMS. Kétirányú SMS azt jelenti, hogy a felhasználó kell szöveg vissza egy adott kód. Kétirányú SMS elavult, és November 14 2018 után nem támogatott. Felhasználók, akik vannak konfigurálva, a rendszer automatikusan át kétirányú SMS _telefonszámot_ akkori ellenőrzése.|
| Értesítés mobilalkalmazáson keresztül |Elküld egy leküldéses értesítést küld telefonjára vagy regisztrált eszközhöz. A felhasználó az értesítési megtekintése és kijelöli **ellenőrizze** igazolásához. A Microsoft Authenticator alkalmazás érhető el [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Mobilalkalmazás ellenőrzőkódja |A Microsoft Authenticator alkalmazást hoz létre egy új OATH-ellenőrző kódot 30 másodpercenként. A felhasználó beírja a megerősítési kódot, a bejelentkezési felületén. A Microsoft Authenticator alkalmazás érhető el [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Engedélyezheti vagy letilthatja a hitelesítési módszerek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**.
4. A multi-factor Authentication területen válassza a **szolgáltatás beállításai**.
5. Az a **Szolgáltatásbeállítások** lap **ellenőrzési lehetőségek**, válassza ki vagy adja meg a felhasználóknak eljárások eltávolításához.

   ![Válassza ki a hitelesítési módszerek](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Kattintson a **Save** (Mentés) gombra.
