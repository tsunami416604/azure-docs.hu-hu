---
title: Az Azure Multi-Factor Authentication konfigurálása – Azure Active Directory
description: Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Multi-Factor Authentication beállításait a Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf732ec97a57a5bc1d2bcaa39e5fd14a305504d0
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075502"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Az Azure Multi-Factor Authentication beállításainak konfigurálása

Ez a cikk segítséget nyújt a Azure Portal Multi-Factor Authentication beállításainak kezelésében. Különböző témaköröket tartalmaz, amelyek segítségével a legtöbbet hozhatja ki az Azure Multi-Factor Authentication. Nem minden szolgáltatás érhető el az Azure Multi-Factor Authentication minden verziójában.

Az Azure multi-Factor Authenticationhoz kapcsolódó beállításokat a Azure Portal **Azure Active Directory** > **MFA**-val való tallózással érheti el.

![Azure Portal – Azure AD-Multi-Factor Authentication beállításai](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Beállítások

Ezen beállítások némelyike az MFA-kiszolgálóra, az Azure MFA-ra vagy mindkettőre vonatkozik.

| Funkció | Leírás |
| ------- | ----------- |
| Fiókzárolás | A fiókok ideiglenes zárolása a multi-Factor Authentication szolgáltatásban, ha túl sok megtagadott hitelesítési kísérlet van egy sorban. Ez a funkció csak azokra a felhasználókra vonatkozik, akik hitelesítő PIN-kódot ad meg. (MFA-kiszolgáló) |
| [Felhasználók blokkolása/feloldása](#block-and-unblock-users) | Arra szolgál, hogy bizonyos felhasználók képesek legyenek Multi-Factor Authentication kérelmek fogadására. A letiltott felhasználók hitelesítési kísérleteit a rendszer automatikusan letiltja. A felhasználók a blokkolásuk időpontjában 90 napig maradnak letiltva. |
| [Csalási riasztás](#fraud-alert) | A felhasználókhoz kapcsolódó beállítások konfigurálása a csalárd ellenőrzési kérelmek jelentéséhez |
| [Értesítések](#notifications) | Az MFA-kiszolgálóról érkező események értesítéseinek engedélyezése. |
| [ESKÜ tokenek](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Felhőalapú Azure MFA-környezetekben használatos a felhasználók eskü-jogkivonatának kezeléséhez. |
| [Telefonhívás beállításai](#phone-call-settings) | A Felhőbeli és a helyszíni környezetekhez kapcsolódó telefonhívások és üdvözlések beállításainak konfigurálása. |
| Szolgáltatók | Ez megjeleníti a fiókjához esetlegesen hozzárendelt meglévő hitelesítési szolgáltatókat is. Az új hitelesítési szolgáltatók nem hozhatók létre szeptember 1-től 2018-ig |

## <a name="manage-mfa-server"></a>MFA-kiszolgáló kezelése

Az ebben a szakaszban szereplő beállítások csak az MFA-kiszolgálók esetében használhatók.

| Funkció | Leírás |
| ------- | ----------- |
| Kiszolgáló beállításai | Az MFA-kiszolgáló letöltése és az aktiválási hitelesítő adatok előállítása a környezet inicializálásához |
| [Egyszeri Mellőzés](#one-time-bypass) | A felhasználó hitelesítésének engedélyezése a kétlépéses ellenőrzés végrehajtása nélkül korlátozott ideig. |
| [Gyorsítótárazási szabályok](#caching-rules) |  A gyorsítótárazás elsődlegesen akkor használatos, ha a helyszíni rendszerek, például a VPN, több ellenőrzési kérelmet küldenek, miközben az első kérelem még folyamatban van. Ez a funkció lehetővé teszi, hogy a következő kérelmek automatikusan sikeresek legyenek, miután a felhasználó sikeresen megtörtént az első ellenőrzés. |
| Kiszolgáló állapota | Tekintse meg a helyszíni MFA-kiszolgálók állapotát, beleértve a verziószámot, az állapotot, az IP-címet és az utolsó kommunikációs időt és dátumot. |

## <a name="activity-report"></a>Tevékenységjelentés

Az itt elérhető jelentéskészítés az MFA-kiszolgálóra (helyszíni) vonatkozik. Az Azure MFA-(Cloud-) jelentések az Azure AD-ben a bejelentkezések jelentésében találhatók.

## <a name="block-and-unblock-users"></a>Felhasználók blokkolása és feloldása

A felhasználók _blokkolása és feloldása_ funkció használatával megakadályozhatja, hogy a felhasználók hitelesítési kéréseket kapjanak. A letiltott felhasználók hitelesítési kísérleteit a rendszer automatikusan letiltja. A felhasználók a blokkolásuk időpontjában 90 napig maradnak letiltva.

### <a name="block-a-user"></a>Felhasználó blokkolása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg **Azure Active Directory** > **MFA** >  **-blokkoló/-** feloldási felhasználókat.
3. A felhasználó blokkolásához válassza a **Hozzáadás** lehetőséget.
4. Válassza ki a **replikációs csoportot**. Adja meg a letiltott felhasználó felhasználónevét **a\@Felhasználónév domain.com**. Írjon be egy megjegyzést az **OK** mezőben.
5. A felhasználó blokkolásának befejezéséhez válassza a **Hozzáadás** lehetőséget.

### <a name="unblock-a-user"></a>Felhasználó blokkolásának feloldása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg **Azure Active Directory** > **MFA** >  **-blokkoló/-** feloldási felhasználókat.
3. Válassza a **Letiltás feloldása** elemet a **művelet** oszlopban a Letiltás feloldásához.
4. Adjon meg egy megjegyzést a **blokkolás feloldásának okát** illetően.
5. Válassza a **tiltás** feloldása lehetőséget a felhasználó tiltásának feloldásához.

## <a name="fraud-alert"></a>Visszaélési riasztás

Konfigurálja a _csalási riasztás_ funkciót, hogy a felhasználók jelentsenek csalárd kísérleteket az erőforrásaik eléréséhez. A felhasználók a Mobile App használatával vagy a telefonján keresztül jelenthetnek csalási kísérleteket.

### <a name="turn-on-fraud-alerts"></a>Csalási riasztások bekapcsolása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg **Azure Active Directory** > **MFA** > -**csalások riasztását**.
3. Állítsa be a **csalások beküldésének engedélyezése a felhasználók** számára **beállítást.**
4. Kattintson a **Mentés** gombra.

### <a name="configuration-options"></a>Beállítási lehetőségek

* **Felhasználó tiltása a csalások jelentésekor**: Ha a felhasználó csalást jelez, a fiókja 90 napig blokkolva van, vagy amíg a rendszergazda fel nem oldja a fiókját. A rendszergazdák a bejelentkezési jelentés segítségével ellenőrizhetik a bejelentkezéseket, és megtehetik a megfelelő lépéseket a jövőbeli csalások megelőzése érdekében. A rendszergazdák Ezután [letilthatják](#unblock-a-user) a felhasználó fiókját.
* **Kód a csalás jelentéséhez a kezdeti üdvözlés során**: Ha a felhasználók telefonhívást kapnak a kétlépéses ellenőrzés végrehajtásához, a rendszer **#** általában megnyomja a bejelentkezés megerősítését. A csalások jelentéséhez a felhasználónak be kell írnia egy **#** kódot a gomb megnyomása előtt. Alapértelmezés szerint ez a kód **0** , de testre is szabhatja.

   >[!NOTE]
   >A Microsoft alapértelmezett hang-üdvözlése arra utasítja a felhasználókat, hogy a **0 #** lenyomásával küldjenek csalási riasztást. Ha **nullától**eltérő kódot szeretne használni, jegyezze fel és töltse fel a saját egyéni hangfelvételeit a felhasználókra vonatkozó megfelelő utasításokkal.
   >

### <a name="view-fraud-reports"></a>Csalási jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > a**bejelentkezéseket**. A csalási jelentés mostantól a szabványos Azure AD-beli bejelentkezési jelentés része.

## <a name="notifications"></a>Értesítések

Adja meg az e-mail-címeket azon felhasználók számára, akik csalási értesítő e-mailt kapnak.

![Értesítési csalások riasztási e-mail mintája](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Telefonhívás beállításai

### <a name="caller-id"></a>Hívó azonosítója

**MFA-HÍVÓ azonosítószáma** – ez az a szám, amelyet a felhasználók a telefonján látni fognak. Csak az USA-alapú számok engedélyezettek.

>[!NOTE]
>Ha Multi-Factor Authentication hívásokat a nyilvános telefonos hálózaton keresztül helyezi át, időnként olyan szolgáltatón keresztül irányítja őket, amely nem támogatja a hívó AZONOSÍTÓját. Emiatt a hívó azonosítója nem garantált, noha a Multi-Factor Authentication rendszer mindig elküldi.

### <a name="custom-voice-messages"></a>Egyéni hangüzenetek

A kétlépéses ellenőrzéshez használhatja saját felvételeit és üdvözléseit az _Egyéni hangüzenetek_ szolgáltatással. Ezek az üzenetek a Microsoft-felvételek mellett vagy a helyett is használhatók.

Mielőtt elkezdené, vegye figyelembe a következő korlátozásokat:

* A támogatott fájlformátumok:. wav és. mp3.
* A fájlméret korlátja 5 MB.
* A hitelesítési üzeneteknek 20 másodpercnél rövidebbnek kell lenniük. A 20 másodpercnél hosszabb üzenetek esetében az ellenőrzés sikertelen lehet. Előfordulhat, hogy a felhasználó nem válaszol az üzenet befejeződése előtt, és a hitelesítés időtúllépést okoz.

### <a name="custom-message-language-behavior"></a>Egyéni üzenet nyelvi viselkedése

Ha egy egyéni hangüzenet jelenik meg a felhasználó számára, az üzenet nyelve az alábbi tényezőktől függ:

* Az aktuális felhasználó nyelve.
  * A felhasználó böngészőjében észlelt nyelv.
  * Más hitelesítési forgatókönyvek különbözőképpen működhetnek.
* Az elérhető egyéni üzenetek nyelve.
  * Ezt a nyelvet a rendszergazda választja ki egyéni üzenet hozzáadásakor.

Ha például csak egy egyéni üzenet van, a német nyelvvel:

* A német nyelven hitelesítő felhasználó az egyéni német üzenetet fogja hallani.
* Az angol nyelvű hitelesítéssel rendelkező felhasználó a standard angol nyelvű üzenetet fogja hallani.

### <a name="set-up-a-custom-message"></a>Egyéni üzenet beállítása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **MFA** > **telefonhívási beállításait**.
1. Válassza az **üdvözlés hozzáadása**lehetőséget.
1. Válassza ki az üdvözlés típusát.
1. Válassza ki a nyelvet.
1. Válassza ki a feltölteni kívánt. MP3 vagy. wav hangfájl-fájlt.
1. Válassza a **Hozzáadás** lehetőséget.

### <a name="custom-voice-message-defaults"></a>Egyéni hangüzenetek alapértelmezett értékei

Parancsfájlok egyéni üzenetek létrehozásához.

| Üzenet neve | Parancsfájl |
| --- | --- |
| A hitelesítés sikerült | A bejelentkezés ellenőrzése sikerült. viszlát. |
| Mellék kérése | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. A folytatáshoz nyomja le a font billentyűt. |
| Visszaélés megerősítése | A rendszer csalási riasztást küldött. A fiók zárolásának feloldásához forduljon a vállalat informatikai ügyfélszolgálatához. |
| Csalási üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. Ha nem kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbál hozzáférni a fiókjához. A csalási riasztás beküldéséhez nyomja le a nulla kilót. Ez értesíti a vállalat informatikai csapatát, és blokkolja a további ellenőrzési kísérleteket. |
| Csalást jelentett, csalási riasztást küldtek. | A fiók zárolásának feloldásához forduljon a vállalat informatikai ügyfélszolgálatához. |
| Aktiválás | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| A hitelesítés elutasítva – újrapróbálkozás | Ellenőrzés megtagadva. |
| Újrapróbálkozás (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. |
| Visszaélési üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja.  Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. Ha nem kezdeményezte ezt az ellenőrzést, előfordulhat, hogy valaki megpróbál hozzáférni a fiókjához. A csalási riasztás beküldéséhez nyomja le a nulla kilót. Ez értesíti a vállalat informatikai csapatát, és blokkolja a további ellenőrzési kísérleteket. |
| Újrapróbálkozás (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. |
| Mellék kérése számok után | Ha már ezen a bővítményen van, a folytatáshoz nyomja le a font billentyűt. |
| A hitelesítés elutasítva | Sajnos nem lehet bejelentkezni. Később próbálja meg újra. |
| Aktiválási üdvözlés (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Aktiválás újrapróbálkozás (standard) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Az ellenőrzés befejezéséhez nyomja meg a font billentyűt. |
| Aktiválási üdvözlés (PIN-kód) | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Adja meg a PIN-kódját, majd az ellenőrzés befejezéséhez írja be a font billentyűt. |
| Mellék kérése számok előtt | Köszönjük, hogy a Microsoft bejelentkezési ellenőrzési rendszerét használja. Vigye át ezt a hívást a bővítménybe... |

## <a name="one-time-bypass"></a>Egyszeri Mellőzés

Az _egyszeri Mellőzés_ funkció lehetővé teszi, hogy a felhasználó egyetlen alkalommal hitelesítse magát a kétlépéses ellenőrzés végrehajtása nélkül. A Mellőzés ideiglenes, és a megadott számú másodperc elteltével lejár. Olyan helyzetekben, amikor a Mobile alkalmazás vagy telefon nem kap értesítést vagy telefonhívást, engedélyezheti az egyszeri mellőzést, hogy a felhasználó hozzáférhessen a kívánt erőforráshoz.

### <a name="create-a-one-time-bypass"></a>Egyszeri Mellőzés létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg **Azure Active Directory** > **MFA** > **egyszeri mellőzését**.
3. Válassza a **Hozzáadás** lehetőséget.
4. Ha szükséges, válassza ki a replikálási csoportot a mellőzéshez.
5. Adja meg a felhasználónevet **Felhasználónév\@domain.com**. Adja meg azt a másodpercet, ameddig a megkerülésnek utolsónak kell lennie. Adja meg a Mellőzés okát.
6. Válassza a **Hozzáadás** lehetőséget. Az időkorlát azonnal érvénybe lép. A felhasználónak be kell jelentkeznie, mielőtt az egyszeri Mellőzés lejár.

### <a name="view-the-one-time-bypass-report"></a>Az egyszeri mellőzési jelentés megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg **Azure Active Directory** > **MFA** > **egyszeri mellőzését**.

## <a name="caching-rules"></a>Gyorsítótárszabályok

Beállíthat egy időszakot, amely lehetővé teszi a hitelesítési kísérleteket a felhasználó hitelesítése után a _gyorsítótárazási_ funkció használatával. A felhasználó további hitelesítési kísérletei a megadott időszakon belül automatikusan sikeresek lesznek. A gyorsítótárazás elsődlegesen akkor használatos, ha a helyszíni rendszerek, például a VPN, több ellenőrzési kérelmet küldenek, miközben az első kérelem még folyamatban van. Ez a funkció lehetővé teszi, hogy a következő kérelmek automatikusan sikeresek legyenek, miután a felhasználó sikeresen megtörtént az első ellenőrzés.

>[!NOTE]
>A gyorsítótárazási funkció nem használható a bejelentkezések Azure Active Directory (Azure AD) szolgáltatásba való használatra.

### <a name="set-up-caching"></a>Gyorsítótárazás beállítása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Keresse meg **Azure Active Directory** > **MFA** > **gyorsítótárazási szabályait**.
3. Válassza a **Hozzáadás** lehetőséget.
4. Válassza ki a **gyorsítótár típusát** a legördülő listából. Adja meg a **gyorsítótárazási másodpercek**maximális számát.
5. Ha szükséges, válasszon ki egy hitelesítési típust, és adjon meg egy alkalmazást.
6. Válassza a **Hozzáadás** lehetőséget.

## <a name="mfa-service-settings"></a>MFA-szolgáltatás beállításai

Az alkalmazások jelszavainak, megbízható IP-címeinek, ellenőrzési lehetőségeinek és az Azure Multi-Factor Authentication többtényezős hitelesítésének megjegyzései a szolgáltatás beállításai között találhatók. A szolgáltatás beállításai a Azure Portal érhetők el, ha megkeresi **a Azure Active Directory** > **MFA** > **első lépések** > **konfigurálását** > **további felhőalapú MFA-beállítások**.

![Azure Multi-Factor Authentication szolgáltatás beállításai](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Alkalmazásjelszavak

Néhány alkalmazás, például az Office 2010 vagy a korábbi és az Apple Mail az iOS 11 előtt, nem támogatja a kétlépéses ellenőrzést. Az alkalmazások nem a második ellenőrzés elfogadására vannak konfigurálva. Az alkalmazások használatához használja ki az _alkalmazás jelszavai_ funkciót. A hagyományos jelszó helyett használhat egy alkalmazást, amely lehetővé teszi, hogy az alkalmazások megkerüljék a kétlépéses ellenőrzést, és folytatják a munkát.

A modern hitelesítés a Microsoft Office 2013-ügyfelek és újabb rendszerek esetében támogatott. Office 2013-ügyfelek, beleértve az Outlookot, támogatják a modern hitelesítési protokollokat, és lehetővé teheti a kétlépéses ellenőrzéssel való munkát. Az ügyfél engedélyezése után az alkalmazás jelszavai nem szükségesek az ügyfélhez.

>[!NOTE]
>Az alkalmazás jelszavai nem működnek a feltételes hozzáférésen alapuló multi-Factor Authentication házirendek és a modern hitelesítés használatával.

### <a name="considerations-about-app-passwords"></a>Az alkalmazás jelszavával kapcsolatos megfontolások

Az alkalmazások jelszavainak használatakor vegye figyelembe a következő fontos szempontokat:

* Az alkalmazások jelszavait csak egyszer kell megadnia egy alkalmazásban. A felhasználóknak nem kell nyomon követniük a jelszavakat, vagy minden alkalommal meg kell adniuk őket.
* A rendszer automatikusan létrehozza a tényleges jelszót, és a felhasználó nem biztosítja azt. Az automatikusan generált jelszó nehezebbé teszi a támadók számára, hogy kitalálják és biztonságosabbak legyenek.
* Felhasználónként legfeljebb 40 jelszó adható meg.
* A jelszavakat gyorsítótárazó és a helyszíni helyzetekben használni kívánt alkalmazások nem tudnak elindulni, mert az alkalmazás jelszava nem ismert a munkahelyi vagy iskolai fiókon kívül. Példa erre a forgatókönyvre a helyszíni Exchange-e-mailek, de az archivált levelek a felhőben vannak. Ebben az esetben ugyanez a jelszó nem működik.
* Miután Multi-Factor Authentication engedélyezve van egy felhasználói fiókon, az alkalmazás jelszavai a legtöbb nem böngészővel rendelkező ügyféllel használhatók, például az Outlook és a Microsoft Skype vállalati verzióhoz. Nem hajthatók végre rendszergazdai műveletek az alkalmazások jelszavainak használatával a böngészőn kívüli alkalmazásokon, például a Windows PowerShellben. A műveletek akkor sem hajthatók végre, ha a felhasználó rendelkezik rendszergazdai fiókkal. A PowerShell-parancsfájlok futtatásához hozzon létre egy erős jelszót tartalmazó szolgáltatásfiókot, és ne engedélyezze a fiókot kétlépéses ellenőrzéshez.

>[!WARNING]
>Az alkalmazások jelszavai nem működnek olyan hibrid környezetekben, ahol az ügyfelek a helyszíni és a Felhőbeli automatikus észlelési végpontokkal kommunikálnak. A helyi hitelesítéshez tartományi jelszavak szükségesek. A felhővel való hitelesítéshez az alkalmazás jelszavai szükségesek.

### <a name="guidance-for-app-password-names"></a>Útmutatás az alkalmazás jelszavának neveihez

Az alkalmazás jelszavának nevének tükröznie kell azt az eszközt, amelyen a használatban van. Ha olyan laptopja van, amely nem böngésző alkalmazásokkal, például az Outlook, a Word és az Excel alkalmazással rendelkezik, hozzon létre egy **laptop** nevű alkalmazást az alkalmazások számára. Hozzon létre egy **Desktop** nevű másik alkalmazás-jelszót ugyanahhoz az asztali számítógépen futó alkalmazásokhoz.

>[!NOTE]
>Azt javasoljuk, hogy eszközönként hozzon létre egy alkalmazási jelszót, és ne egy alkalmazás jelszavaként.

### <a name="federated-or-single-sign-on-app-passwords"></a>Összevont vagy egyszeri bejelentkezési alkalmazás jelszavai

Az Azure AD támogatja az összevonást vagy az egyszeri bejelentkezést (SSO) helyszíni Windows Server Active Directory tartományi szolgáltatások (AD DS). Ha a szervezete az Azure AD-vel összevont, és Azure Multi-Factor Authenticationt használ, vegye figyelembe a következő szempontokat az alkalmazás jelszavával kapcsolatban.

>[!NOTE]
>A következő pontok csak az összevont (SSO) ügyfelekre érvényesek.

* Az alkalmazások jelszavait az Azure AD ellenőrzi, ezért az összevonás mellőzése. Az összevonás csak az alkalmazás jelszavának beállításakor használatos.
* Az identitás-szolgáltató (identitásszolgáltató) nem kapcsolódik az összevont (SSO) felhasználókhoz, a passzív folyamattól eltérően. Az alkalmazás jelszavait a munkahelyi vagy iskolai fiók tárolja. Ha a felhasználó elhagyja a vállalatot, a felhasználó adatai valós **időben, a** felhasználók adatait a munkahelyi vagy iskolai fiókba áramlanak. A fiók letiltása/törlése akár három órát is igénybe vehet, ami késleltetheti az alkalmazás jelszavának letiltását/törlését az Azure AD-ben.
* A helyszíni ügyfél Access Control beállításait az alkalmazás jelszavai szolgáltatása nem veszi figyelembe.
* Nem érhető el helyszíni hitelesítési naplózási/naplózási képesség az alkalmazás jelszavai szolgáltatással való használatra.
* Néhány speciális architektúrához a hitelesítő adatok kombinációja szükséges a kétlépéses ellenőrzéshez az ügyfelekkel. Ezek a hitelesítő adatok a munkahelyi vagy iskolai fiókhoz tartozó felhasználónevet és jelszót, valamint az alkalmazás jelszavait is tartalmazhatják. A követelmények attól függnek, hogyan történik a hitelesítés. A helyszíni infrastruktúrát, a munkahelyi vagy iskolai fiókhoz tartozó felhasználónevet és jelszót hitelesítő ügyfelek esetében kötelező megadni. Az Azure AD-t hitelesítő ügyfelek esetében szükség van egy alkalmazás jelszavára.

  Tegyük fel például, hogy a következő architektúrával rendelkezik:

  * Active Directory helyszíni példányát az Azure AD-vel összevontuk.
  * Az Exchange Online-t használja.
  * A Skype vállalati verzió a helyszínen üzemel.
  * Az Azure Multi-Factor Authentication-t használja.

  Ebben az esetben a következő hitelesítő adatokat használja:

  * A Skype vállalati verzióba való bejelentkezéshez használja a munkahelyi vagy iskolai fiókja felhasználónevét és jelszavát.
  * Ha a címjegyzéket az Exchange Online-hoz kapcsolódó Outlook-ügyfélről szeretné elérni, használja az alkalmazás jelszavát.

### <a name="allow-users-to-create-app-passwords"></a>Alkalmazások jelszavainak létrehozásának engedélyezése a felhasználók számára

Alapértelmezés szerint a felhasználók nem hozhatnak létre alkalmazás-jelszavakat. Az alkalmazás jelszava funkciót engedélyezni kell. A következő eljárással biztosíthatja a felhasználóknak az alkalmazás jelszavának létrehozását:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza **Azure Active Directory** > **felhasználók**lehetőséget.
3. Válassza a többtényezős **hitelesítés**lehetőséget.
4. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai**elemet.
5. A **szolgáltatás beállításai** lapon jelölje be a **felhasználók számára az alkalmazás jelszavának engedélyezése lehetőséget a nem böngésző alkalmazásokba való bejelentkezéshez** .

### <a name="create-app-passwords"></a>Alkalmazás jelszavának létrehozása

A felhasználók a kezdeti regisztráció során hozhatnak létre alkalmazás-jelszavakat. A felhasználónak lehetősége van az alkalmazás jelszavainak létrehozására a regisztrációs folyamat végén.

A felhasználók a regisztráció után is létrehozhatnak alkalmazás-jelszavakat. További információk és részletes lépések a felhasználók számára: Mik az [alkalmazások jelszavai az Azure-ban multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Megbízható IP-címek

Az Azure Multi-Factor Authentication _megbízható IP_ -címei funkciót a felügyelt vagy összevont bérlők rendszergazdái használják. A szolgáltatás megkerüli a kétlépéses ellenőrzést a vállalati intranetről bejelentkező felhasználók számára. A szolgáltatás az Azure Multi-Factor Authentication teljes verziójával érhető el, nem pedig a rendszergazdák ingyenes verziójával. Az Azure Multi-Factor Authentication teljes verziójának beszerzésével kapcsolatos további információkért lásd: [azure multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> Az MFA megbízható IP-címei és a nevesített helyek feltételes hozzáférése csak IPV4-címekkel működik.

Ha a szervezete telepíti a hálózati házirend-kiszolgáló bővítményét, hogy az MFA-t a helyszíni alkalmazások számára biztosítsa, jegyezze fel, hogy a forrás IP-cím mindig az NPS-kiszolgáló lesz, amely a hitelesítési kísérleten átfolyik.

| Azure AD-bérlő típusa | Megbízható IP-címek funkciójának beállításai |
|:--- |:--- |
| Kezelt |**IP-címek meghatározott tartománya**: A rendszergazdák olyan IP-címtartományt határoznak meg, amely elkerülheti a kétlépéses ellenőrzést a vállalati intranetről bejelentkező felhasználók számára.|
| Összevont |**Minden összevont felhasználó**: A szervezeten belülről bejelentkező összes összevont felhasználó figyelmen kívül hagyhatja a kétlépéses ellenőrzést. A felhasználók megkerülik az ellenőrzést Active Directory összevonási szolgáltatások (AD FS) (AD FS) által kiállított jogcímek használatával.<br/>**IP-címek meghatározott tartománya**: A rendszergazdák olyan IP-címtartományt határoznak meg, amely elkerülheti a kétlépéses ellenőrzést a vállalati intranetről bejelentkező felhasználók számára. |

A megbízható IP-címek megkerülése csak a vállalati intraneten belülről működik. Ha a **minden összevont felhasználó** lehetőséget választja, és a felhasználó a vállalati intraneten kívülről jelentkezik be, a felhasználónak kétlépéses ellenőrzéssel kell hitelesítenie magát. A folyamat akkor is ugyanaz, ha a felhasználó AD FS jogcímet jelent. 

### <a name="end-user-experience-inside-of-corpnet"></a>Végfelhasználói élmény a Corpnet-on belül

Ha a megbízható IP-címek funkció le van tiltva, a böngésző folyamataihoz kétlépéses ellenőrzés szükséges. Az alkalmazások jelszavai a régebbi, gazdag ügyfélalkalmazások esetében szükségesek.

Ha a megbízható IP-címek szolgáltatás engedélyezve van, a böngésző folyamataihoz *nem* szükséges kétlépéses ellenőrzés. Az alkalmazás jelszavai nem szükségesek a régebbi, Rich ügyfélalkalmazások számára, ha a felhasználó *nem* hozott létre alkalmazás-jelszót. Ha egy alkalmazás jelszava használatban van, a jelszót is meg kell adni. 

### <a name="end-user-experience-outside-corpnet"></a>Végfelhasználói élmény a Corpnet-en kívül

Függetlenül attól, hogy engedélyezve van-e a megbízható IP-címek szolgáltatás, kétlépéses ellenőrzés szükséges a böngésző folyamataihoz. Az alkalmazások jelszavai a régebbi, gazdag ügyfélalkalmazások esetében szükségesek.

### <a name="enable-named-locations-by-using-conditional-access"></a>Elnevezett helyszínek engedélyezése feltételes hozzáférés használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza a **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés** > **elnevezett tárolóhelyek**elemet.
3. Válassza az **új hely**lehetőséget.
4. Adja meg a hely nevét.
5. Válassza **a megjelölés megbízható helyként**lehetőséget.
6. Adja meg az IP-címtartományt a CIDR-jelölésben, például **192.168.1.1/24**.
7. Kattintson a **Létrehozás** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>A megbízható IP-címek funkciójának engedélyezése feltételes hozzáférés használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza a **Azure Active Directory** > **biztonsági** >  **feltételes hozzáférés** > **elnevezett tárolóhelyek**elemet.
3. Válassza az **MFA megbízható IP**-címek konfigurálása lehetőséget.
4. A **Szolgáltatásbeállítások** oldalon, a **megbízható IP**-címek területen válasszon a következő két lehetőség közül:

   * Az **intranetről származó összevont felhasználóktól érkező kérelmek esetén**: A beállítás kiválasztásához jelölje be a jelölőnégyzetet. Az összes olyan összevont felhasználó, aki bejelentkezik a vállalati hálózatról a kétlépéses ellenőrzés megkerülésére egy AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy AD FS rendelkezik olyan szabállyal, amely az intranetes jogcímet hozzáadja a megfelelő adatforgalomhoz. Ha a szabály nem létezik, hozza létre a következő szabályt a AD FSban:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * A **nyilvános IP-címek meghatározott tartományának kéréseire**: Ha ezt a lehetőséget választja, adja meg az IP-címeket a szövegmezőben a CIDR-jelölés használatával.
      * Az xxx. xxx. xxx. 1 és XXX. xxx. xxx. 254 tartományba tartozó IP-címek esetében használja az alábbi jelölést: **xxx. xxx. xxx. 0/24**.
      * Egyetlen IP-cím esetén használjon olyan jelölést, mint a **xxx.xxx.xxx.xxx/32**.
      * Akár 50 IP-címtartományt is megadhat. Azok a felhasználók, akik ezen IP-címekről jelentkeznek be, megkerülik a kétlépéses ellenőrzést.

5. Kattintson a **Mentés** gombra.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>A megbízható IP-címek funkció engedélyezése a szolgáltatás beállításai alapján

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza **Azure Active Directory** > **felhasználók**lehetőséget.
3. Válassza a többtényezős **hitelesítés**lehetőséget.
4. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai**elemet.
5. A **Szolgáltatásbeállítások** oldalon, a **megbízható IP**-címek területen válasszon egyet (vagy mindkettőt) a következő két lehetőség közül:

   * Az **intraneten lévő összevont felhasználóktól érkező kérések esetén**: A beállítás kiválasztásához jelölje be a jelölőnégyzetet. Az összes olyan összevont felhasználó, aki bejelentkezik a vállalati hálózatról a kétlépéses ellenőrzés megkerülésére egy AD FS által kiadott jogcím használatával. Győződjön meg arról, hogy AD FS rendelkezik olyan szabállyal, amely az intranetes jogcímet hozzáadja a megfelelő adatforgalomhoz. Ha a szabály nem létezik, hozza létre a következő szabályt a AD FSban:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Az IP-alhálózatok megadott tartományában található kérelmek esetén**: Ha ezt a lehetőséget választja, adja meg az IP-címeket a szövegmezőben a CIDR-jelölés használatával.
      * Az xxx. xxx. xxx. 1 és XXX. xxx. xxx. 254 tartományba tartozó IP-címek esetében használja az alábbi jelölést: **xxx. xxx. xxx. 0/24**.
      * Egyetlen IP-cím esetén használjon olyan jelölést, mint a **xxx.xxx.xxx.xxx/32**.
      * Akár 50 IP-címtartományt is megadhat. Azok a felhasználók, akik ezen IP-címekről jelentkeznek be, megkerülik a kétlépéses ellenőrzést.

6. Kattintson a **Mentés** gombra.

## <a name="verification-methods"></a>Ellenőrzési módszerek

A felhasználók számára elérhető ellenőrzési módszereket is kiválaszthatja. Az alábbi táblázat a módszerek rövid áttekintését tartalmazza.

Amikor a felhasználók regisztrálják a fiókjaikat az Azure Multi-Factor Authenticationhoz, az Ön által engedélyezett beállítások alapján választják ki az előnyben részesített ellenőrzési módszert. A felhasználói beléptetési folyamatra vonatkozó útmutatást a [saját fiók beállítása a kétlépéses ellenőrzéshez című témakörben talál](../user-help/multi-factor-authentication-end-user-first-time.md).

| Módszer | Leírás |
|:--- |:--- |
| Megadott telefonszám hívása |Automatikus hanghívást helyez el. A felhasználó megválaszolja a hívást, majd a hitelesítéshez lenyomja a telefon billentyűzetén a # gombot. A telefonszám nincs szinkronizálva a helyszíni Active Directory. |
| SMS küldése megadott telefonszámra |Egy ellenőrző kódot tartalmazó szöveges üzenetet küld. A rendszer felszólítja a felhasználót, hogy adja meg az ellenőrző kódot a bejelentkezési felületen. Ezt a folyamatot egy egyirányú SMS-nek nevezzük. A kétirányú SMS azt jelenti, hogy a felhasználónak egy adott kódot kell visszaadnia. A kétirányú SMS elavult, és 2018 november 14. után nem támogatott. A kétirányú SMS-re konfigurált felhasználók automatikusan átváltanak a telefonos ellenőrzés _meghívására_ .|
| Értesítés mobilalkalmazáson keresztül |Leküldéses értesítést küld a telefonra vagy a regisztrált eszközre. A felhasználó megtekinti az értesítést, **és kiválasztja az ellenőrzés** befejezését. A Microsoft Authenticator alkalmazás [Windows Phone-telefon](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)rendszerhez érhető el. |
| Ellenőrző kód a Mobile App vagy a Hardware tokenből |A Microsoft Authenticator alkalmazás 30 másodpercenként létrehoz egy új eskü-ellenőrző kódot. A felhasználó beírja az ellenőrző kódot a bejelentkezési felületre. A Microsoft Authenticator alkalmazás [Windows Phone-telefon](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)rendszerhez érhető el. |

### <a name="enable-and-disable-verification-methods"></a>Ellenőrzési módszerek engedélyezése és letiltása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza **Azure Active Directory** > **felhasználók**lehetőséget.
3. Válassza a többtényezős **hitelesítés**lehetőséget.
4. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai**elemet.
5. A **Szolgáltatásbeállítások** lapon az **ellenőrzési beállítások**területen válassza ki/törölje a felhasználók számára megadható metódusokat.
6. Kattintson a **Save** (Mentés) gombra.

A hitelesítési módszerek használatával kapcsolatos további információk a következő cikkben találhatók: [Mi a hitelesítési módszer](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Ne feledje Multi-Factor Authentication

A felhasználó által megbízhatónak tartott eszközök és böngészők _megjegyzés multi-Factor Authentication_ funkciója az összes multi-Factor Authentication felhasználó számára ingyenes szolgáltatás. A felhasználók a megadott számú napra kihagyhatják a későbbi ellenőrzéseket, miután Multi-Factor Authentication használatával sikeresen bejelentkezett az eszközre. A szolgáltatás javítja a használhatóságot azáltal, hogy minimalizálja azt a számot, ahányszor a felhasználónak két lépésből álló ellenőrzést kell végrehajtania ugyanazon az eszközön.

>[!IMPORTANT]
>Ha egy fiók vagy eszköz biztonsága sérül, a megbízható eszközök Multi-Factor Authenticationának megjegyzése hatással lehet a biztonságra. Ha egy vállalati fiók biztonsága sérül, vagy egy megbízható eszköz elveszett vagy ellopták, akkor [minden eszközön vissza kell állítania multi-Factor Authentication](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>A visszaállítási művelet visszavonja a megbízható állapotot az összes eszközről, és a felhasználónak újra kell végrehajtania a kétlépéses ellenőrzést. Azt is megteheti, hogy a felhasználók a saját eszközökön is visszaállítják Multi-Factor Authentication a [beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)című részben leírtak szerint.

### <a name="how-the-feature-works"></a>A szolgáltatás működése

A ne feledje Multi-Factor Authentication szolgáltatás állandó cookie-t állít be a böngészőben, ha a felhasználó a bejelentkezéskor a **ne Kérdezzen újra X napra** lehetőséget választja. A felhasználónak nem kell ismét megadnia Multi-Factor Authentication ugyanezen böngészőből, amíg a cookie le nem jár. Ha a felhasználó egy másik böngészőt nyit meg ugyanazon az eszközön, vagy törli a cookie-kat, a rendszer ismét kérni fogja az ellenőrzésre.

A **ne jelenjen meg többé az X nap** beállítás nem jelenik meg a böngészőn kívüli alkalmazásokon, függetlenül attól, hogy az alkalmazás támogatja-e a modern hitelesítést. Ezek az alkalmazások a _frissítési jogkivonatokat_ használják, amelyek óránként új hozzáférési jogkivonatokat biztosítanak. A frissítési jogkivonat ellenőrzésekor az Azure AD ellenőrzi, hogy az utolsó kétlépéses ellenőrzés a megadott számú napon belül történt-e.

A szolgáltatás csökkenti a webes alkalmazások hitelesítésének számát, ami általában minden alkalommal felgyorsul. A szolgáltatás növeli a modern hitelesítési ügyfelek hitelesítésének számát, amelyek általában 90 naponta megkérik. A feltételes hozzáférési szabályzatokkal kombinálva növelheti a hitelesítések számát is.

>[!IMPORTANT]
>A **Ne feledje multi-Factor Authentication** funkció nem kompatibilis a AD FS **bejelentkezett** szolgáltatásával, amikor a felhasználók kétlépéses AD FS ellenőrzést végeznek az Azure multi-Factor Authentication-kiszolgáló vagy egy harmadik féltől származó multi-Factor hitelesítési megoldás.
>
>Ha a felhasználók a AD FS **bejelentkezve szeretnék megtartani** az eszközt, és a multi-Factor Authentication számára is megbízhatóként jelölik meg az eszközét, a felhasználó nem lesz automatikusan ellenőrizve a **multi-Factor Authentication számának megjegyzése** lejár. Az Azure AD egy friss kétlépéses ellenőrzést kér, de AD FS visszaadja az eredeti Multi-Factor Authentication jogcím és dátum értékű jogkivonatot ahelyett, hogy a kétlépéses ellenőrzést újra elvégezze. **Ez a reakció ellenőrző hurkot állít be az Azure AD és a AD FS között.**
>
>A **megjegyzés multi-Factor Authentication** funkció nem kompatibilis a B2B-felhasználókkal, és nem lesz látható a B2B-felhasználók számára a meghívott bérlők bejelentkezésekor.
>

### <a name="enable-remember-multi-factor-authentication"></a>Megjegyzés engedélyezése Multi-Factor Authentication

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza **Azure Active Directory** > **felhasználók**lehetőséget.
3. Válassza a többtényezős **hitelesítés**lehetőséget.
4. A Multi-Factor Authentication területen válassza a **szolgáltatás beállításai**elemet.
5. A **Szolgáltatásbeállítások** lapon **kezelheti a többtényezős hitelesítés megjegyzéseit**, és jelölje be a **felhasználók számára a többtényezős hitelesítés megemlékezésének engedélyezése a megbízható eszközökön** beállítást.
6. Állítsa be a napok számát, hogy a megbízható eszközök el lehessen kerülni a kétlépéses ellenőrzést. Az alapértelmezett érték 14 nap.
7. Kattintson a **Mentés** gombra.

### <a name="mark-a-device-as-trusted"></a>Eszköz megjelölése megbízhatóként

Miután engedélyezte a Megjegyzés Multi-Factor Authentication funkciót, a felhasználók a bejelentkezéskor megbízhatóként jelölhetik meg az eszközöket, ha a **ne Kérdezzen rá ismét**lehetőségre.

## <a name="next-steps"></a>További lépések

[Az Azure AD bejelentkezési oldalának arculatának módosítása](../fundamentals/customize-branding.md)
