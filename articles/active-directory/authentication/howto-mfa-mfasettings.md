---
title: Az Azure többtényezős hitelesítésének konfigurálása – Azure Active Directory
description: Ez a cikk bemutatja, hogyan konfigurálható az Azure többtényezős hitelesítési beállításai az Azure Portalon
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
ms.openlocfilehash: da995afba1dc8eff295c9b724a78da95b9caac85
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653968"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Az Azure Multi-Factor Authentication beállításainak konfigurálása

Ez a cikk segít a többtényezős hitelesítési beállítások kezelésében az Azure Portalon. Számos témakört foglal magában, amelyek segítségével a legtöbbet hozhatja ki az Azure többtényezős hitelesítéséből. Nem minden funkció érhető el az Azure többtényezős hitelesítésének minden verziójában.

Az Azure Többtényezős hitelesítéssel kapcsolatos beállításokat az Azure Active **Directory** > **Security** > **MFA**szolgáltatással való böngészéssel érheti el az Azure Többtényezős hitelesítéssel kapcsolatban.

![Azure Portal – Az Azure AD többtényezős hitelesítési beállításai](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Beállítások

Ezek közül a beállítások közül néhány az MFA-kiszolgálóra, az Azure MFA-ra vagy mindkettőre vonatkozik.

| Szolgáltatás | Leírás |
| ------- | ----------- |
| Fiók zárolása | A többtényezős hitelesítési szolgáltatás fiókjainak ideiglenes zárolása, ha túl sok a megtagadási hitelesítési kísérlet egy sorban. Ez a funkció csak azokra a felhasználókra vonatkozik, akik PIN-kódot adnak meg a hitelesítéshez. (MFA-kiszolgáló) |
| [Felhasználók blokkolása/feloldása](#block-and-unblock-users) | Letiltására bizonyos felhasználók nem fogadhatnak többtényezős hitelesítési kérelmeket. A letiltott felhasználók összes hitelesítési kísérlete automatikusan el lesz utasítva. A felhasználók a letiltás időpontjától kezdve 90 napig letiltva maradnak. |
| [Csalási riasztás](#fraud-alert) | A felhasználók csalárd ellenőrzési kérelmek bejelentésére való képességével kapcsolatos beállítások konfigurálása |
| [Értesítések](#notifications) | Események értesítéseinek engedélyezése az MFA-kiszolgálóról. |
| [OATH tokenek](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Felhőalapú Azure MFA-környezetekben használható oath-jogkivonatok kezelésére a felhasználók számára. |
| [Telefonhívás beállításai](#phone-call-settings) | Konfigurálja a telefonhívásokhoz és üdvözlésekhez kapcsolódó beállításokat felhőbeli és helyszíni környezetekben. |
| Szolgáltatók | Ez megmutatja a fiókjához társított összes meglévő hitelesítésszolgáltatót. 2018. szeptember 1-től nem hozhatók létre új hitelesítési szolgáltatók |

## <a name="manage-mfa-server"></a>MFA-kiszolgáló kezelése

Az ebben a szakaszban megadott beállítások csak az MFA-kiszolgálóra szolgálnak.

| Szolgáltatás | Leírás |
| ------- | ----------- |
| Kiszolgáló beállításai | Az MFA-kiszolgáló letöltése és aktiválási hitelesítő adatok létrehozása a környezet inicializálásához |
| [Egyszeri bypass](#one-time-bypass) | A felhasználó korlátozott ideig történő hitelesítése engedélyezése a kétlépéses ellenőrzés végrehajtása nélkül. |
| [Gyorsítótárazási szabályok](#caching-rules) |  A gyorsítótárazás elsősorban akkor használatos, ha a helyszíni rendszerek, például a VPN, több ellenőrzési kérelmet küldenek, amíg az első kérés még folyamatban van. Ez a funkció lehetővé teszi, hogy a későbbi kérelmek automatikusan sikeresek legyenek, miután a felhasználó sikeresen sikeresen végzett az első folyamatban lévő ellenőrzéssel. |
| Kiszolgáló állapota | Tekintse meg a helyszíni MFA-kiszolgálók állapotát, beleértve a verziót, az állapotot, az IP-címet és az utolsó kommunikáció időpontját és dátumát. |

## <a name="activity-report"></a>Tevékenységjelentés

Az itt elérhető jelentések az MFA-kiszolgálóra (helyszíni) vonatkoznak. Az Azure MFA (felhőalapú) jelentések tekintse meg a bejelentkezések jelentést az Azure AD-ben.

## <a name="block-and-unblock-users"></a>Felhasználók letiltása és tiltásának feloldása

A blokk és a _felhasználók tiltásának feloldása_ funkcióval megakadályozhatja, hogy a felhasználók hitelesítési kérelmeket fogadjanak. A letiltott felhasználók összes hitelesítési kísérlete automatikusan el lesz utasítva. A felhasználók a letiltás időpontjától kezdve 90 napig letiltva maradnak.

### <a name="block-a-user"></a>Felhasználó letiltása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **mfa-blokkolása/tiltásának** > **feloldása felhasználókat.**
3. A Felhasználó letiltásához válassza a **Hozzáadás** lehetőséget.
4. Válassza ki a **replikációs csoportot**. Adja meg a letiltott felhasználó felhasználónevét **domain.com.\@** Írjon be egy megjegyzést az **Ok** mezőbe.
5. A felhasználó letiltásának befejezéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="unblock-a-user"></a>Felhasználó letiltásának feloldása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **mfa-blokkolása/tiltásának** > **feloldása felhasználókat.**
3. A tiltás feloldásához válassza a Felhasználó melletti **Művelet oszlopTiltás** **feloldása** elemét.
4. Írjon be egy megjegyzést a **feloldómezőben.**
5. A felhasználó letiltásának feloldásához válassza a **Tiltás feloldása** lehetőséget.

## <a name="fraud-alert"></a>Csalási riasztás

Konfigurálja a _csalásriasztási_ funkciót, hogy a felhasználók bejelenthessék az erőforrásaikhoz való hozzáférésre irányuló csalárd kísérleteket. A felhasználók a mobilalkalmazás sal vagy a telefonjukon keresztül jelenthetik a csalási kísérleteket.

### <a name="turn-on-fraud-alerts"></a>Csalási riasztások bekapcsolása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **mfa-csalásokra** > **vonatkozó riasztását.**
3. Állítsa a **Felhasználók számára a csalási riasztások elküldésének engedélyezése** beállítást **Be**beállításra.
4. Kattintson a **Mentés** gombra.

### <a name="configuration-options"></a>Beállítási lehetőségek

* **Csalás bejelentése esetén tiltsa le a felhasználót:** Ha egy felhasználó csalást jelent, a fiókját 90 napig letiltjuk, vagy amíg a rendszergazda fel nem oldja a fiókját. A rendszergazda áttekintheti a bejelentkezési jelentéseket a bejelentkezési jelentés használatával, és megteheti a megfelelő lépéseket a jövőbeli csalások megelőzése érdekében. A rendszergazda ezután [feloldhatja](#unblock-a-user) a felhasználói fiók letiltását.
* **A csalás bejelentésére használt kód az első üdvözlés során:** Amikor a **#** felhasználók telefonhívást kapnak a kétlépéses ellenőrzés elvégzéséhez, általában megnyomják a bejelentkezés megerősítését. A csalás bejelentéséhez a felhasználó a **#** gomb megnyomása előtt beír egy kódot. Ez a kód alapértelmezés szerint **0,** de testre szabható.

   >[!NOTE]
   >A Microsoft alapértelmezett hangüdvözlései arra utasítják a felhasználókat, hogy a **0#** billentyűt nyomják meg a csalási riasztás elküldéséhez. Ha **a 0-tól**eltérő kódot szeretne használni, rögzítse és töltse fel saját egyéni hangüdvözléseit a felhasználók számára megfelelő utasításokkal.
   >

### <a name="view-fraud-reports"></a>Csalási jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Azure Active Directory** > **bejelentkezések**lehetőséget. A csalási jelentés most már része a szabványos Azure AD bejelentkezések jelentés.

## <a name="notifications"></a>Értesítések

Itt állíthatja be az e-mail címeket azoknak a felhasználóknak, akik csalásra figyelmeztető e-maileket kapnak.

![Értesítési csalás riasztáse e-mail minta](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Telefonhívás beállításai

### <a name="caller-id"></a>Hívó azonosítója

**MFA hívóazonosító száma** – Ez az a szám, amelyet a felhasználók a telefonjukon fognak látni. Csak amerikai alapú számok engedélyezettek.

>[!NOTE]
>Ha a többtényezős hitelesítési hívásokat a nyilvános telefonhálózaton keresztül bonyolítják le, néha olyan szolgáltatón keresztül irányítják őket, amely nem támogatja a hívóazonosítót. Emiatt a hívóazonosító nem garantált, még akkor sem, ha a többtényezős hitelesítési rendszer mindig elküldi azt.

Az Egyesült Államokban, ha még nem konfigurálta az MFA hívóazonosítóját, a Microsoft tól érkező hanghívások a következő számokból származnak: +1 (866) 539 4191, +1 (855) 330 8653 és +1 (877) 668 6536. Ha levélszemétszűrőket használ, győződjön meg arról, hogy kizárja ezeket a számokat.

### <a name="custom-voice-messages"></a>Egyéni hangüzenetek

Az _egyéni hangüzenetek_ funkcióval használhatja saját felvételeit vagy üdvözléseit a kétlépéses ellenőrzéshez. Ezek az üzenetek a Microsoft-felvételek mellett vagy azok cseréjére is használhatók.

Mielőtt elkezdené, vegye figyelembe az alábbi korlátozásokat:

* A támogatott fájlformátumok a .wav és az .mp3.
* A fájlméret-korlát 1 MB.
* A hitelesítési üzeneteknek 20 másodpercnél rövidebbnek kell lenniük. A 20 másodpercnél hosszabb üzenetek az ellenőrzés sikertelensét okozhatják. Előfordulhat, hogy a felhasználó nem válaszol, mielőtt az üzenet befejeződik, és az ellenőrzés időtúljár.

### <a name="custom-message-language-behavior"></a>Egyéni üzenetnyelv viselkedése

Amikor egyéni hangüzenetet játszik le a felhasználónak, az üzenet nyelve az alábbi tényezőktől függ:

* Az aktuális felhasználó nyelve.
  * A felhasználó böngészője által észlelt nyelv.
  * Más hitelesítési forgatókönyvek eltérően viselkedhetnek.
* A rendelkezésre álló egyéni üzenetek nyelve.
  * Ezt a nyelvet a rendszergazda választja ki, amikor egyéni üzenetet ad hozzá.

Ha például csak egy egyéni üzenet van, német nyelvvel:

* A német nyelven hitelesíti magát felhasználó, és meghallja az egyéni német üzenetet.
* Az angol nyelven hitelesíti magát felhasználó, és a szokásos angol nyelvű üzenetet fogja hallani.

### <a name="set-up-a-custom-message"></a>Egyéni üzenet beállítása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > MFA-hívásbeállításait.**MFA** > **Phone call settings**
1. Válassza **az Üdvözlés hozzáadása**lehetőséget.
1. Válassza ki az üdvözlés típusát.
1. Válassza ki a nyelvet.
1. Jelöljön ki egy .mp3 vagy .wav hangfájlt a feltöltendő fájlba.
1. Válassza a **Hozzáadás** lehetőséget.

### <a name="custom-voice-message-defaults"></a>Az egyéni hangüzenetek alapértelmezései

Példaparancsfájlok egyéni üzenetek létrehozásához.

| Üzenet neve | Parancsfájl |
| --- | --- |
| A hitelesítés sikeres | A bejelentkezés sikeresen megtörtént. viszlát. |
| Bővítmény kérdés | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. A folytatáshoz nyomja meg a font billentyűt. |
| Csalás megerősítése | Csalási riasztást nyújtottak be. A fiók blokkolásának feloldásához forduljon a vállalat informatikai ügyfélszolgálatához. |
| Csalás üdvözlés (Standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font gombot. Ha nem ön kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbál hozzáférni a fiókjához. Kérjük, nyomja meg a nulla font, hogy nyújtson be egy csalás riasztást. Ez értesíti a vállalat informatikai csapatát, és blokkolja a további ellenőrzési kísérleteket. |
| Csalás bejelentésére csalási riasztást nyújtottak be. | A fiók blokkolásának feloldásához forduljon a vállalat informatikai ügyfélszolgálatához. |
| Aktiválás | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font gombot. |
| A hitelesítés megtagadva az újrapróbálkozást | Ellenőrzés megtagadva. |
| Újrapróbálkozás (normál) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font gombot. |
| Üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font gombot. |
| Üdvözlés (PIN) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Kérjük, adja meg a PIN-kódot, majd a font gombot, hogy befejezze az ellenőrzést. |
| Csalás üdvözlése (PIN) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja.  Kérjük, adja meg a PIN-kódot, majd a font gombot, hogy befejezze az ellenőrzést. Ha nem ön kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbál hozzáférni a fiókjához. Kérjük, nyomja meg a nulla font, hogy nyújtson be egy csalás riasztást. Ez értesíti a vállalat informatikai csapatát, és blokkolja a további ellenőrzési kísérleteket. |
| Újrapróbálkozás(PIN) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Kérjük, adja meg a PIN-kódot, majd a font gombot, hogy befejezze az ellenőrzést. |
| Kiterjesztés iprompt a számjegyek után | Ha már a bővítménynél van, a folytatáshoz nyomja le a font gombot. |
| Hitelesítés megtagadva | Sajnálom, de most nem tudjuk beírni. Próbálkozzon újra később. |
| Aktiválási üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font gombot. |
| Aktiválás immára (normál) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font gombot. |
| Aktiválási üdvözlés (PIN) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Kérjük, adja meg a PIN-kódot, majd a font gombot, hogy befejezze az ellenőrzést. |
| Kiterjesztési kérdés a számjegyek előtt | Köszönjük, hogy a Microsoft bejelentkezési ellenőrző rendszerét használja. Kérjük, utalja át ezt a hívást a kiterjesztés ... |

## <a name="one-time-bypass"></a>Egyszeri bypass

Az _egyszeri megkerülési_ funkció lehetővé teszi a felhasználó számára, hogy egyetlen alkalommal hitelesítse magát kétlépéses ellenőrzés nélkül. A megkerülés ideiglenes, és meghatározott számú másodperc után lejár. Olyan esetekben, amikor a mobilalkalmazás vagy a telefon nem kap értesítést vagy telefonhívást, engedélyezheti az egyszeri megkerülést, hogy a felhasználó hozzáférhessen a kívánt erőforráshoz.

### <a name="create-a-one-time-bypass"></a>Egyszeri megkerülés létrehozása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Tallózással keresse meg az **Azure Active Directory** > **security** > **mfa** > **egyszeri megkerülését.**
3. Válassza a **Hozzáadás** lehetőséget.
4. Szükség esetén válassza ki a megkerülés replikációs csoportját.
5. Írja be a felhasználónevet **domain.com\@felhasználónévként.** Adja meg, hogy hány másodpercig kell tartania a bypassnak. Adja meg a bypass okát.
6. Válassza a **Hozzáadás** lehetőséget. A határidő azonnal hatályba lép. Az egyszeri megkerülés lejárta előtt be kell jelentkeznie.

### <a name="view-the-one-time-bypass-report"></a>Az egyszeri bypass jelentés megtekintése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Tallózással keresse meg az **Azure Active Directory** > **security** > **mfa** > **egyszeri megkerülését.**

## <a name="caching-rules"></a>Gyorsítótárazási szabályok

Beállíthat egy időszakot, amely lehetővé teszi a hitelesítési kísérleteket, miután a felhasználó hitelesítése a _gyorsítótárazási_ szolgáltatás használatával történik. A felhasználó által a megadott időszakon belül tett későbbi hitelesítési kísérletek automatikusan sikeresek lesznek. A gyorsítótárazás elsősorban akkor használatos, ha a helyszíni rendszerek, például a VPN, több ellenőrzési kérelmet küldenek, amíg az első kérés még folyamatban van. Ez a funkció lehetővé teszi, hogy a későbbi kérelmek automatikusan sikeresek legyenek, miután a felhasználó sikeresen sikeresen végzett az első folyamatban lévő ellenőrzéssel.

>[!NOTE]
>A gyorsítótárazási szolgáltatás nem az Azure Active Directoryba (Azure AD) való bejelentkezéshez használható.

### <a name="set-up-caching"></a>Gyorsítótárazás beállítása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **mfa-gyorsítótárazási****MFA** > szabályait.
3. Válassza a **Hozzáadás** lehetőséget.
4. Válassza ki a **gyorsítótár típusát** a legördülő listából. Adja meg a **gyorsítótár másodperceinek**maximális számát.
5. Szükség esetén válasszon ki egy hitelesítési típust, és adjon meg egy alkalmazást.
6. Válassza a **Hozzáadás** lehetőséget.

## <a name="mfa-service-settings"></a>MFA-szolgáltatás beállításai

Az alkalmazásjelszavak, a megbízható IP-k, az ellenőrzési beállítások és a többtényezős hitelesítés megjegyzése az Azure többtényezős hitelesítéséhez a szolgáltatásbeállításokban találhatók. A szolgáltatásbeállítások az Azure-portálról érhetők el az **Azure Active Directory** > **Security** > **MFA** > **első** > lépések című részében, további**felhőalapú MFA-beállítások****konfigurálása** > .

![Az Azure többtényezős hitelesítési szolgáltatásbeállításai](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

A megbízható IP-címtartományok lehetnek privátok vagy nyilvánosak.

## <a name="app-passwords"></a>Alkalmazásjelszavak.

Egyes alkalmazások, például az Office 2010-es vagy korábbi, valamint az iOS 11 előtti Apple Mail nem támogatják a kétlépéses ellenőrzést. Az alkalmazások nincsenek beállítva, hogy elfogadja a második ellenőrzés. Ezen alkalmazások használatához használja ki az _alkalmazásjelszavak_ funkciót. A hagyományos jelszó helyett alkalmazásjelszót is használhat, hogy az alkalmazások megkerülhesd a kétlépéses ellenőrzést, és folytathassák a munkát.

A Modern hitelesítés a Microsoft Office 2013-ügyfelek és újabb verziók esetében támogatott. Az Office 2013-ügyfelek, beleértve az Outlookot is, támogatják a modern hitelesítési protokollokat, és lehetővé tehetők a kétlépéses ellenőrzéssel való együttműködésre. Az ügyfél engedélyezése után az alkalmazásjelszavak nem szükségesek az ügyfélhez.

>[!NOTE]
>Az alkalmazásjelszavak nem működnek feltételes hozzáférésalapú többtényezős hitelesítési házirendekkel és modern hitelesítéssel.

### <a name="considerations-about-app-passwords"></a>Az alkalmazásjelszavakkal kapcsolatos szempontok

Az alkalmazásjelszavak használatakor vegye figyelembe a következő fontos pontokat:

* Az alkalmazásjelszavak at csak egyszer kell megadni alkalmazásonként. A felhasználóknak nem kell minden alkalommal nyomon követniük vagy beírniuk a jelszavakat.
* A tényleges jelszó automatikusan generálódik, és a felhasználó nem biztosítja. Az automatikusan létrehozott jelszót a támadó nehezebben tudja kitalálni, és biztonságosabb.
* Felhasználónként legfeljebb 40 jelszó érhető el.
* Alkalmazások, amelyek gyorsítótárazási jelszavakat, és használja őket a helyszíni forgatókönyvek indítása korig sikertelen, mert az alkalmazás jelszavát nem ismert kívül a munkahelyi vagy iskolai fiók. Egy példa erre a forgatókönyv exchange-e-maileket, amelyek a helyszínen, de az archivált e-mail a felhőben. Ebben az esetben ugyanaz a jelszó nem működik.
* Miután a többtényezős hitelesítés engedélyezve van a felhasználó fiókjában, az alkalmazásjelszavak a legtöbb nem böngészőben használt ügyfélnél, például az Outlookban és a Microsoft Skype Vállalati verzióban használhatók. Felügyeleti műveletek nem hajthatók végre alkalmazásjelszavak nem böngészőalkalmazásokon, például a Windows PowerShellen keresztül. A műveletek nem hajthatók végre akkor sem, ha a felhasználó rendszergazdai fiókkal rendelkezik. PowerShell-parancsfájlok futtatásához hozzon létre egy szolgáltatásfiókot egy erős jelszóval, és nem engedélyezi a fiókot a kétlépéses ellenőrzéshez.

>[!WARNING]
>Az alkalmazásjelszavak nem működnek hibrid környezetekben, ahol az ügyfelek kommunikálnak mind a helyszíni, mind a felhőalapú automatikus felderítési végpontokkal. A helyszíni hitelesítéshez tartományi jelszavak szükségesek. Az alkalmazásjelszavak szükségesek a felhővel való hitelesítéshez.

### <a name="guidance-for-app-password-names"></a>Útmutató az alkalmazásjelszó-nevekhez

Az alkalmazásjelszó-neveknek tükrözniük kell azt az eszközt, amelyen használják őket. Ha olyan laptopja van, amely nem böngészőalkalmazásokkal rendelkezik, például az Outlook, a Word és az Excel, hozzon létre egy **Laptop** nevű alkalmazásjelszót ezekhez az alkalmazásokhoz. Hozzon létre egy másik **alkalmazásjelszót, amelyet Asztal** néven neveznek az asztali számítógépen futó alkalmazásokhoz.

>[!NOTE]
>Azt javasoljuk, hogy eszközönként hozzon létre egy alkalmazásjelszót, ne pedig alkalmazásonként egy alkalmazásjelszót.

### <a name="federated-or-single-sign-on-app-passwords"></a>Összevont vagy egyszeri bejelentkezési alkalmazásjelszavak

Az Azure AD támogatja az összevonást vagy egyszeri bejelentkezést (SSO), a helyszíni Windows Server Active Directory tartományi szolgáltatásokkal (AD DS). Ha a szervezet össze van egyítve az Azure AD-vel, és az Azure többtényezős hitelesítést használja, vegye figyelembe az alkalmazásjelszavakkal kapcsolatos alábbi pontokat.

>[!NOTE]
>A következő pontok csak az összevont (SSO) ügyfelekre vonatkoznak.

* Az alkalmazásjelszavakat az Azure AD ellenőrzi, és ezért megkerüli az összevonást. Az összevonás csak az alkalmazásjelszavak beállításakor használatos.
* Az identitásszolgáltató (IdP) nem érhető el az összevont (SSO) felhasználók, ellentétben a passzív folyamat. Az alkalmazásjelszavak a munkahelyi vagy iskolai fiókban tárolódnak. Ha egy felhasználó elhagyja a vállalatot, a felhasználó információi valós időben a **munkahelyi** vagy iskolai fiókba áramlanak. A fiók letiltása/törlése akár három órát is igénybe vehet a szinkronizálás, ami késleltetheti az alkalmazás jelszavának letiltását/törlését az Azure AD-ben.
* A helyszíni ügyfélhozzáférés-vezérlési beállításokat az alkalmazásjelszavak szolgáltatás nem tartja be.
* Nincs helyszíni hitelesítésnaplózási/naplózási képesség az alkalmazásjelszavak szolgáltatáshoz való használatra.
* Egyes speciális architektúrák az ügyfelekkel való kétlépéses ellenőrzéshez hitelesítő adatok kombinációját igénylik. Ezek a hitelesítő adatok tartalmazhatnak munkahelyi vagy iskolai fiók felhasználónevét és jelszavait, valamint alkalmazásjelszavakat. A követelmények a hitelesítés módjától függenek. A helyszíni infrastruktúrával hitelesítendő ügyfelek számára a munkahelyi vagy iskolai fiók felhasználóneve és jelszava szükséges. Az Azure AD-vel hitelesítendő ügyfelek számára alkalmazásjelszó szükséges.

  Tegyük fel például, hogy a következő architektúrával rendelkezik:

  * Az Active Directory helyszíni példányát össze van kapcsolva az Azure AD.Your on-premises instance of Active Directory is eteded with Azure AD.
  * Az Exchange online használatát használja.
  * A Helyszíni Skype Vállalati verziót használja.
  * Az Azure többtényezős hitelesítést használja.

  Ebben az esetben a következő hitelesítő adatokat használja:

  * A Skype Vállalati verzióba való bejelentkezéshez használja munkahelyi vagy iskolai fiókjának felhasználónevét és jelszavát.
  * Ha az Exchange online verzióhoz kapcsolódó Outlook-ügyfélprogramból szeretné elérni a címjegyzéket, használjon alkalmazásjelszót.

### <a name="allow-users-to-create-app-passwords"></a>Alkalmazásjelszavak létrehozásának engedélyezése a felhasználóknak

Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazásjelszavakat. Az alkalmazásjelszavak funkciót engedélyezni kell. Ahhoz, hogy a felhasználók létrehozzsalják az alkalmazásjelszavakat, kövesse az alábbi eljárást:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az Azure Active > **Directory-felhasználók lehetőséget.** **Azure Active Directory**
3. Válassza a **Többtényezős hitelesítés lehetőséget.**
4. A Többtényezős hitelesítés csoportban válassza a **szolgáltatásbeállításokat.**
5. A **Szolgáltatás beállításai** lapon jelölje be a **Felhasználók számára alkalmazásjelszavak létrehozásához nem böngészőalkalmazásokba való bejelentkezés** engedélyezése lehetőséget.

### <a name="create-app-passwords"></a>Alkalmazásjelszavak létrehozása

A felhasználók alkalmazásjelszavakat hozhatnak létre a kezdeti regisztráció során. A felhasználónak lehetősége van alkalmazásjelszavakat létrehozni a regisztrációs folyamat végén.

A felhasználók a regisztráció után is létrehozhatnak alkalmazásjelszavakat. További információkért és a felhasználók részletes lépéseiért olvassa el [a Mik azok az alkalmazásjelszavak az Azure többtényezős hitelesítésében?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Megbízható IP-címek

Az Azure többtényezős hitelesítés _megbízható IP-címeit_ egy felügyelt vagy összevont bérlő rendszergazdái használják. A szolgáltatás megkerüli a kétlépéses ellenőrzést azon felhasználók számára, akik a vállalati intranetről jelentkeznek be. A funkció az Azure többtényezős hitelesítés teljes verziójával érhető el, nem pedig a rendszergazdák számára ingyenes verzióval. Az Azure többtényezős hitelesítés teljes verziójának beszerezhetéséről az [Azure többtényezős hitelesítéscímű](multi-factor-authentication.md)témakörben talál részleteket.

> [!NOTE]
> Az MFA megbízható IP-címei és a feltételes hozzáféréssel ellátott helyek csak IPV4-címekkel működnek.

Ha a szervezet telepíti a nps kiterjesztést, hogy az MFA a helyszíni alkalmazások figyelmét a forrás IP-cím mindig úgy tűnik, hogy a hitelesítési kísérlet a hitelesítési kísérlet folyik keresztül.

| Azure AD-bérlőtípusa | Megbízható IP-szolgáltatások szolgáltatásbeállításai |
|:--- |:--- |
| Kezelt |**Ip-címek adott tartománya:** A rendszergazdák olyan IP-címtartományt határoznak meg, amely megkerülheti a kétlépéses ellenőrzést a vállalati intranetről bejelentkező felhasználók számára. Legfeljebb 50 megbízható IP-tartomány konfigurálható.|
| Összevont |**Minden összevont felhasználó:** A szervezeten belülről bejelentkező összes összevont felhasználó megkerülheti a kétlépéses ellenőrzést. A felhasználók az Active Directory összevonási szolgáltatások (AD FS) által kiadott jogcím használatával kerülik meg az ellenőrzést.<br/>**Ip-címek adott tartománya:** A rendszergazdák olyan IP-címtartományt határoznak meg, amely megkerülheti a kétlépéses ellenőrzést a vállalati intranetről bejelentkező felhasználók számára. |

A megbízható IP-k megkerülése csak a vállalati intraneten belülműködik. Ha a **Minden összevont felhasználó lehetőséget választja,** és egy felhasználó bejelentkezik a vállalati intraneten kívülről, a felhasználónak kétlépéses ellenőrzéssel kell hitelesítenie magát. A folyamat akkor is ugyanaz, ha a felhasználó AD FS-jogcímet jelenít meg. 

### <a name="end-user-experience-inside-of-corpnet"></a>Végfelhasználói élmény a testhálózaton belül

Ha a megbízható IP-cím szolgáltatás le van tiltva, a böngészőfolyamatokhoz kétlépéses ellenőrzésszükséges. Alkalmazásjelszavak szükségesek a régebbi gazdag ügyfélalkalmazások.

Ha a megbízható IP-cím funkció engedélyezve van, a böngészőfolyamatok *hoz nem* szükséges kétlépéses ellenőrzés. Alkalmazásjelszavak *nem* szükségesek a régebbi gazdag ügyfélalkalmazások, feltéve, hogy a felhasználó nem hozott létre alkalmazásjelszót. Az alkalmazásjelszó használata után a jelszó ra marad szükség. 

### <a name="end-user-experience-outside-corpnet"></a>Végfelhasználói élmény a testhálózaton kívül

Függetlenül attól, hogy a megbízható IP-k funkció engedélyezve van-e, a böngészőfolyamatokhoz kétlépéses ellenőrzésszükséges. Alkalmazásjelszavak szükségesek a régebbi gazdag ügyfélalkalmazások.

### <a name="enable-named-locations-by-using-conditional-access"></a>Elnevezett helyek engedélyezése feltételes hozzáféréssel

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az **Azure Active Directory** > **biztonsági** > **feltételes hozzáféréssel** > **ellátott helyek lehetőséget.**
3. Válassza az **Új hely lehetőséget.**
4. Adja meg a hely nevét.
5. Válassza **a Megjelölés megbízható helyként**lehetőséget.
6. Írja be az IP-tartományt CIDR jelölésben, mint **a 192.168.1.1/24**.
7. Kattintson a **Létrehozás** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>A Megbízható IP-szolgáltatások engedélyezése feltételes hozzáférés sel

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az **Azure Active Directory** > **biztonsági** >  **feltételes hozzáféréssel** > **ellátott helyek lehetőséget.**
3. Válassza **az MFA megbízható IP-k konfigurálása**lehetőséget.
4. A **Szolgáltatás beállítások** lapján, a **Megbízható IP-k**csoportban válasszon az alábbi két lehetőség közül:

   * **Az intranetről származó összevont felhasználók tól érkező kérések esetén:** A beállítás kiválasztásához jelölje be a jelölőnégyzetet. A vállalati hálózatról bejelentkező összes összevont felhasználó megkerüli a kétlépéses ellenőrzést az AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy az AD FS rendelkezik egy olyan szabállyal, amely hozzáadja az intranetes jogcímet a megfelelő forgalomhoz. Ha a szabály nem létezik, hozza létre a következő szabályt az AD FS-ben:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **A nyilvános IP-címek adott tartományából érkező kérelmek esetén:** A beállítás kiválasztásához írja be az IP-címeket a szövegmezőbe CIDR jelöléssel.
      * Az xxx.xxx.xxx.1 és xxx.xxx.xxx.254 közötti IP-címek esetén használja az **xxx.xxx.xxx.0/24**jelölési számot.
      * Egyetlen IP-cím esetén a **jelöléssel**például xxx.xxx.xxx.xxx/32 .
      * Legfeljebb 50 IP-címtartomány t. Az ezekből az IP-címekről bejelentkező felhasználók megkerülik a kétlépéses ellenőrzést.

5. Kattintson a **Mentés** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>A Megbízható IP-szolgáltatások szolgáltatás használatának engedélyezése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az Azure Active > **Directory-felhasználók lehetőséget.** **Azure Active Directory**
3. Válassza a **Többtényezős hitelesítés lehetőséget.**
4. A Többtényezős hitelesítés csoportban válassza a **szolgáltatásbeállításokat.**
5. A **Szolgáltatás beállítások** lapján, a **Megbízható IP-k**csoportban válasszon az alábbi két lehetőség közül:

   * **Az intraneten lévő összevont felhasználók tól érkező kérések esetén:** A beállítás kiválasztásához jelölje be a jelölőnégyzetet. A vállalati hálózatról bejelentkező összes összevont felhasználó megkerüli a kétlépéses ellenőrzést az AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy az AD FS rendelkezik egy olyan szabállyal, amely hozzáadja az intranetes jogcímet a megfelelő forgalomhoz. Ha a szabály nem létezik, hozza létre a következő szabályt az AD FS-ben:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Az IP-címalhálózatok megadott tartományából érkező kérelmek esetén:** A beállítás kiválasztásához írja be az IP-címeket a szövegmezőbe CIDR jelöléssel.
      * Az xxx.xxx.xxx.1 és xxx.xxx.xxx.254 közötti IP-címek esetén használja az **xxx.xxx.xxx.0/24**jelölési számot.
      * Egyetlen IP-cím esetén a **jelöléssel**például xxx.xxx.xxx.xxx/32 .
      * Legfeljebb 50 IP-címtartomány t. Az ezekből az IP-címekről bejelentkező felhasználók megkerülik a kétlépéses ellenőrzést.

6. Kattintson a **Mentés** gombra.

## <a name="verification-methods"></a>Ellenőrzési módszerek

Kiválaszthatja a felhasználók számára elérhető ellenőrzési módszereket. Az alábbi táblázat rövid áttekintést nyújt a módszerekről.

Amikor a felhasználók regisztrálják a fiókjukat az Azure többtényezős hitelesítéshez, az általad engedélyezett beállítások közül választják ki az előnyben részesített ellenőrzési módszert. A felhasználói regisztrációs folyamathoz a Fiók [beállítása kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-first-time.md)című részben talál útmutatást.

| Módszer | Leírás |
|:--- |:--- |
| Telefonhívás |Automatikus hanghívást kezdeményez. A felhasználó fogadja a hívást, majd a hitelesítéshez lenyomja a telefon billentyűzetén a # gombot.  A telefonszám nincs szinkronizálva a helyszíni Active Directoryval. |
| Sms-ben telefonon |Ellenőrző kódot tartalmazó szöveges üzenetet küld. A felhasználónak meg kell adnia az ellenőrző kódot a bejelentkezési felületen. Ezt a folyamatot egyirányú SMS-nek nevezzük. A kétirányú SMS azt jelenti, hogy a felhasználónak egy adott kódot kell visszaküldenie. 2018. november 14-e után a kétirányú SMS elavult, és nem támogatott. A rendszergazdáknak más módszert kell engedélyezniük a korábban kétirányú SMS-t használó felhasználók számára.|
| Értesítés mobilalkalmazáson keresztül |Küld egy push értesítést a telefon vagy a regisztrált eszköz. A felhasználó megtekinti az értesítést, és az **Ellenőrzés** lehetőséget választja az ellenőrzés befejezéséhez. A Microsoft Authenticator alkalmazás [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)rendszerhez érhető el. |
| Ellenőrző kód mobilalkalmazásból vagy hardvertokenből |A Microsoft Authenticator alkalmazás 30 másodpercenként új OATH-ellenőrző kódot hoz létre. A felhasználó beírja az ellenőrző kódot a bejelentkezési felületre. A Microsoft Authenticator alkalmazás [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)rendszerhez érhető el. |

### <a name="enable-and-disable-verification-methods"></a>Ellenőrzési módszerek engedélyezése és letiltása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az Azure Active > **Directory-felhasználók lehetőséget.** **Azure Active Directory**
3. Válassza a **Többtényezős hitelesítés lehetőséget.**
4. A Többtényezős hitelesítés csoportban válassza a **szolgáltatásbeállításokat.**
5. A **Szolgáltatás beállításai** lapon az **Ellenőrzési beállítások**csoportban válassza ki vagy törölje a felhasználók számára biztosítandó módszereket.
6. Kattintson a **Mentés** gombra.

További részletek a hitelesítési módszerek használatával kapcsolatban a [Mik a hitelesítési módszerek.](concept-authentication-methods.md)

## <a name="remember-multi-factor-authentication"></a>Többtényezős hitelesítés megjegyzése

A felhasználó által megbízhatónak nevezett eszközök és böngészők _megjegyzése a többtényezős hitelesítésre_ vonatkozó ingyenes funkció minden többtényezős hitelesítést igénybe vevő számára. A felhasználók több napon át kihagyhatják az egymást követő ellenőrzéseket, miután a Multi-Factor Authentication használatával sikeresen bejelentkeztek egy eszközre. A funkció növeli a használhatóságot azáltal, hogy minimálisra csökkentette, hogy a felhasználónak hányszor kell kétlépéses ellenőrzést végeznie ugyanazon az eszközön.

>[!IMPORTANT]
>Ha egy fiók vagy eszköz biztonsága sérül, a megbízható eszközök többtényezős hitelesítésének megjegyzése hatással lehet a biztonságra. Ha egy vállalati fiók feltörése vagy egy megbízható eszköz elvész vagy ellopják, [vonja vissza az MFA-munkameneteket.](howto-mfa-userdevicesettings.md)
>
>A visszaállítási művelet visszavonja a megbízható állapotot az összes eszközről, és a felhasználónak újra el kell végeznie a kétlépéses ellenőrzést. Azt is utasíthatja a felhasználókat, hogy a többtényezős hitelesítést saját eszközeiken állítsák vissza a [Kétlépéses ellenőrzés beállításainak kezelése](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)című útmutató utasításaival.

### <a name="how-the-feature-works"></a>A funkció működése

A remember Multi-Factor Authentication szolgáltatás állandó cookie-t állít be a böngészőben, amikor a felhasználó a **Bejelentkezéskor** a Ne kérjen újra X nap lehetőséget. A rendszer nem kéri újra a felhasználót a többtényezős hitelesítésre ugyanattól a böngészőtől, amíg a cookie le nem jár. Ha a felhasználó egy másik böngészőt nyit meg ugyanazon az eszközön, vagy törli a cookie-kat, a rendszer ismét kéri az ellenőrzést.

A **Ne kérjen újra x nap** lehetőséget nem jelenik meg a nem böngészőalkalmazásokban, függetlenül attól, hogy az alkalmazás támogatja-e a modern hitelesítést. Ezek az alkalmazások _frissítési jogkivonatokat_ használnak, amelyek óránként új hozzáférési jogkivonatokat biztosítanak. Frissítési jogkivonat érvényesítésekor az Azure AD ellenőrzi, hogy az utolsó kétlépéses ellenőrzés történt a megadott számú napon belül.

A szolgáltatás csökkenti a webalkalmazások hitelesítéseinek számát, amelyek általában minden alkalommal kérik. A szolgáltatás növeli a hitelesítések számát a modern hitelesítési ügyfelek, amelyek általában 90 naponta kéri. A feltételes hozzáférési házirendekkel kombinálva is növelheti a hitelesítések számát.

>[!IMPORTANT]
>A **többtényezős hitelesítés megjegyzése** funkció nem kompatibilis az AD FS **bejelentkezett állapotának** funkciójával, amikor a felhasználók kétlépéses ellenőrzést végeznek az AD FS-hez az Azure többtényezős hitelesítési kiszolgálón vagy egy harmadik féltől származó többtényezős hitelesítési megoldáson keresztül.
>
>Ha a felhasználók azt választják, **hogy bejelentkezve maradjak az** AD FS-en, és az eszközüket megbízhatóként jelöljék meg a többtényezős hitelesítéshez, a rendszer nem ellenőrzi automatikusan a felhasználót, miután a **többtényezős hitelesítési** napok száma lejár. Az Azure AD újabb kétlépéses ellenőrzést kér, de az AD FS egy jogkivonatot ad vissza az eredeti többtényezős hitelesítési jogcímekkel és dátummal, ahelyett, hogy újra kétlépéses ellenőrzést végezne. **Ez a reakció beállítja az Azure AD és az AD FS közötti ellenőrzési hurkot.**
>
>A **ne feledje, többtényezős hitelesítés** funkció nem kompatibilis a B2B felhasználók, és nem lesz látható a B2B felhasználók számára, amikor bejelentkezik a meghívott bérlők.
>

### <a name="enable-remember-multi-factor-authentication"></a>Többtényezős emlékezés engedélyezése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az Azure Active > **Directory-felhasználók lehetőséget.** **Azure Active Directory**
3. Válassza a **Többtényezős hitelesítés lehetőséget.**
4. A Többtényezős hitelesítés csoportban válassza a **szolgáltatásbeállításokat.**
5. A **Szolgáltatás beállítások** lapján kezelje a **többtényezős hitelesítés megjegyzését,** és válassza a Felhasználók számára a **többtényezős hitelesítés megjegyzésének engedélyezése** a megbízható eszközökön lehetőséget.
6. Állítsa be, hogy a megbízható eszközök hány napig kerülhetik meg a kétlépéses ellenőrzést. Az alapértelmezett érték 14 nap.
7. Kattintson a **Mentés** gombra.

### <a name="mark-a-device-as-trusted"></a>Eszköz megjelölése megbízhatóként

A többtényezős hitelesítés megjegyzése funkció engedélyezése után a felhasználók megbízhatóként jelölhetnek meg egy eszközt, amikor bejelentkeznek, ha a **Ne kérdezzen rá újra**lehetőséget.

## <a name="next-steps"></a>További lépések

[Az Azure AD bejelentkezési oldalának márkajelzésének módosítása](../fundamentals/customize-branding.md)
