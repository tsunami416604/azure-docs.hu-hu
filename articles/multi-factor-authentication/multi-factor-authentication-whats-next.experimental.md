---
title: "Az Azure többtényezős hitelesítés beállítása |} Microsoft Docs"
description: "Ez az Azure multi-factor Authentication hitelesítés lap teendőkre az MFA Használatát ismerteti.  Ez magában foglalja a jelentések, csalási riasztás, az egyszeri mellőzés, egyéni hangüzenetek, gyorsítótárazás, megbízható IP-címek és az alkalmazás jelszavakat."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: joflore
ms.openlocfilehash: a6064e33dca09a304074efa4029c0f96fc19b2a2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure multi-factor Authentication beállításainak konfigurálása
Ez a cikk segítséget Azure multi-factor Authentication most, hogy működik és elérhető.  Bemutatja, különböző témakörök, amelyek segítenek az Azure multi-factor Authentication minél hatékonyabb működtetését.  Nem minden ezeket a szolgáltatásokat az Azure multi-factor Authentication minden verziójában érhetők el.

| Szolgáltatás | Leírás | 
|:--- |:--- |
| [Csalási riasztás](#fraud-alert) |Csalási riasztás konfigurálhatók, és állítsa be, hogy a felhasználók jelenthetik-e rosszindulatú megpróbál hozzáférni az erőforrásokhoz. |
| [Az egyszeri Mellőzés](#one-time-bypass) |Az egyszeri Mellőzés lehetővé teszi, hogy a felhasználó egy alkalommal a multi-factor authentication "kihagyásával". |
| [Egyedi Hangüzenetek](#custom-voice-messages) |Egyedi Hangüzenetek történő saját felvételek vagy hónap és a többtényezős hitelesítés használatát teszi lehetővé. |
| [Gyorsítótárazás](#caching-in-azure-multi-factor-authentication) |Gyorsítótárazás lehetővé teszi egy adott idő időszak beállítása, hogy a későbbi hitelesítési próbálkozások automatikusan sikeres. |
| [Megbízható IP-címek](#trusted-ips) |A felügyelt vagy összevont bérlők rendszergazdái a felhasználók számára, hogy jelentkezzen be a vállalat helyi intranet a kétlépéses ellenőrzés elkerülésére megbízható IP-címeket használhatnak. |
| [Alkalmazásjelszók](#app-passwords) |Az alkalmazásjelszó lehetővé teszi, hogy egy alkalmazás, amely nincs MFA-t támogató multi-factor authentication kihagyásához és a munka folytatásához. |
| [Ne feledje a multi-factor Authentication a korábban megjegyzett eszközökön és böngészők](#remember-multi-factor-authentication-for-devices-that-users-trust) |Lehetővé teszi az eszközök jegyezze meg a meghatározott számú nap elteltével a felhasználó sikeresen bejelentkezett az MFA használata. |
| [Választható hitelesítési módszerek](#selectable-verification-methods) |A felhasználók számára elérhető hitelesítési módszerek használatát teszi lehetővé. |

## <a name="access-the-azure-mfa-management-portal"></a>Hozzáférés az Azure MFA-felügyeleti portálon

A cikkben szereplő funkciók vannak konfigurálva az Azure multi-factor Authentication kezelési portál. A multi-factor Authentication kezelési portál eléréséhez a klasszikus Azure portálon keresztül két módja van. Az első való kezelésekor a többtényezős hitelesítésszolgáltató van. A második pedig a multi-factor Authentication szolgáltatás beállításainak használatával. 

### <a name="use-an-authentication-provider"></a>Egy hitelesítésszolgáltató használata

Ha a multi-factor Authentication fogyasztás alapján használja a többtényezős hitelesítésszolgáltató, ezt a módszert a felügyeleti portál eléréséhez.

A multi-factor Authentication kezelési portálon keresztül az Azure multi-factor Auth Provider eléréséhez jelentkezzen be a klasszikus Azure portálon rendszergazdaként, és válassza ki az Active Directory lehetőséget. Kattintson a **többtényezős hitelesítésszolgáltatók** fülre, majd válassza ki azt a címtárat, és kattintson a **kezelése** panel alján.

### <a name="use-the-mfa-service-settings-page"></a>A multi-factor Authentication szolgáltatás beállításai lapon 

Ha a következő licencek egyikét, használhatja a multi-factor Authentication beállításainak lapján:
- Azure MFA használatával
- Azure AD Premium 
- Enterprise Mobility + Security

Az MFA-felügyeleti portálon keresztül a multi-factor Authentication szolgáltatás beállításainak lap eléréséhez jelentkezzen be a klasszikus Azure portálon rendszergazdaként, és válassza ki az Active Directory lehetőséget. Kattintson a saját címtárára, majd a **Konfigurálás** fülre. A többtényezős hitelesítés szakaszban jelölje ki a **Szolgáltatásbeállítások kezelése** elemet. Az MFA szolgáltatásbeállítások oldal alján kattintson az **Ugrás a portálra** hivatkozásra.


## <a name="fraud-alert"></a>Visszaélési riasztás
Csalási riasztás konfigurálhatók, és állítsa be, hogy a felhasználók jelenthetik-e rosszindulatú megpróbál hozzáférni az erőforrásokhoz.  Csalás mobilalkalmazással vagy telefont keresztül a felhasználók jelenthetik.

### <a name="set-up-fraud-alert"></a>Csalási riasztás beállítása
1. Keresse meg a multi-factor Authentication kezelési portál utasítások a lap tetején.
2. Kattintson az Azure multi-factor Authentication kezelési portál **beállítások** konfigurálása szakaszban.
3. A beállítások lapon a csalási riasztás szakaszban ellenőrizze a **engedélyezése a felhasználók számára visszaélési riasztás** jelölőnégyzetet.
4. Válassza ki **mentése** a módosítások alkalmazásához. 

### <a name="configuration-options"></a>Konfigurációs beállítások

- **Felhasználó blokkolása visszaélés jelentésekor** – Ha egy felhasználó jelentések csalás, a fiók le van tiltva.
- **Kód a jelentés csalás során kezdeti köszöntés** -felhasználók általában nyomja meg a # kétlépéses ellenőrzés megerősítéséhez. Csalás jelentése szeretnék, ha azok kód megadása előtt a # gomb megnyomásával. Ez a kód **0** alapértelmezés szerint azonban testre szabható.

> [!NOTE]
> A Microsoft alapértelmezett hangüdvözléseit utasítsa a felhasználókat, hogy nyomja meg a 0# a csalási értesítést küldeni. Ha 0-tól eltérő kódot használni kívánt, jegyezze fel, és a saját egyéni hangüdvözléseit megfelelő utasításokkal feltöltés.

![Csalás vonatkozó riasztási beállítások – képernyőkép](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>Hogyan felhasználók csalás jelentése 
Csalási riasztás kétféleképpen jelenthetők.  Vagy a mobilalkalmazás vagy telefonon keresztül.  

#### <a name="report-fraud-with-the-mobile-app"></a>Visszaélés jelentéséhez a mobilalkalmazáshoz
1. Egy ellenőrzés a telefonjára küldött, válassza ki azt a Microsoft Authenticator alkalmazás megnyitásához.
2. Válassza ki **Megtagadás** az értesítésre kattinthat. 
3. Válassza ki **csalás jelentése**.
4. Zárja be az alkalmazást.

#### <a name="report-fraud-with-a-phone"></a>Visszaélés jelentéséhez a telefon
1. Ha egy ellenőrző hívást telefonján, fogadja a hívást.  
2. A visszaélés jelentéséhez adja meg a visszaéléssel összefüggő kódot (alapértelmezett érték 0), majd a # jelet. Majd értesítést kap, hogy a csalási értesítést elküldte.
3. A hívás befejezéséhez.

### <a name="view-fraud-reports"></a>Csalás jelentések megtekintése
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. A bal oldalon válassza az Active Directory elemet.
3. A lap tetején jelölje ki a **többtényezős hitelesítésszolgáltatók** a többtényezős hitelesítési szolgáltatók listájának megjelenítéséhez.
4. Válassza ki a többtényezős hitelesítésszolgáltató, és kattintson a **kezelése** az oldal alján. Az Azure multi-factor Authentication kezelési portál megnyitása.
5. Kattintson az Azure multi-factor Authentication kezelési portál, A jelentés megtekintése a **csalási riasztás**.
6. Adja meg a dátumtartományt, a jelentésben megjeleníteni kívánt. Megadhatja a felhasználónevek, a telefonszámokat, és a felhasználó állapotát.
7. Kattintson a **futtatása** visszaélési riasztások jelentés megjelenítéséhez. Kattintson a **Exportálás CSV-FÁJLBA** Ha exportálja a jelentést.

## <a name="one-time-bypass"></a>Az egyszeri Mellőzés
Az egyszeri Mellőzés lehetővé teszi, hogy a felhasználó egy alkalommal kétlépéses ellenőrzés végrehajtása nélkül. A Mellőzés átmeneti, és a megadott számú másodperc után lejár. Olyan esetekben, ahol a mobilalkalmazás vagy a telefon nem kap egy értesítés, vagy a telefonhívás-engedélyezheti az egyszeri mellőzés, így a felhasználó hozzáférhet a kívánt erőforrás.

### <a name="create-a-one-time-bypass"></a>Az egyszeri Mellőzés létrehozása
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Keresse meg a multi-factor Authentication kezelési portál utasítások a lap tetején.
3. Ha az Azure MFA-szolgáltató neve látja a bal oldalon egy  **+**  látható, kattintson a  **+** . Különböző MFA kiszolgáló replikációs csoportokat és az Azure alapértelmezett csoport is látható. Válassza ki a megfelelő.
4. Válassza ki a felhasználói felügyelet **egyszeri megkerülés**.
5. Az egyszeri megkerülés lapján kattintson a **új egyszeri megkerülés**.

  ![Felhő](./media/multi-factor-authentication-whats-next/create1.png)

6. Adja meg a felhasználónevet, a mellőzés, és a mellőzés okát időtartama. Kattintson a **áthidaló**.
7. Az időkorlát azonnal működésbe lép, így a felhasználónak be kell jelentkeznie az egyszeri Mellőzés lejárta előtt. 

### <a name="view-the-one-time-bypass-report"></a>Az egyszeri Mellőzés jelentés megtekintése
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. A bal oldalon válassza az Active Directory elemet.
3. A lap tetején jelölje ki a **többtényezős hitelesítésszolgáltatók** a többtényezős hitelesítési szolgáltatók listájának megjelenítéséhez.
4. Válassza ki a többtényezős hitelesítésszolgáltató, és kattintson a **kezelése** az oldal alján. Az Azure multi-factor Authentication kezelési portál megnyitása.
5. Kattintson az Azure multi-factor Authentication kezelési portál, a bal oldali alapján A jelentés megtekintése, **egyszeri megkerülés**.
6. Adja meg a dátumtartományt, a jelentésben megjeleníteni kívánt. Megadhatja a felhasználónevek, a telefonszámokat, és a felhasználó állapotát.
7. Kattintson a **futtatása** megjelenítése a jelentés a Mellőzés. Kattintson a **Exportálás CSV-FÁJLBA** Ha exportálja a jelentést.

## <a name="custom-voice-messages"></a>Egyedi Hangüzenetek
Egyedi Hangüzenetek lehetővé teszi a saját felvételek vagy a hónap a kétlépéses ellenőrzéshez. Egyedi Hangüzenetek kívül is használhatja, vagy lecseréli a Microsoft rögzíti.

Mielőtt elkezdené, vegye figyelembe ezeket:

* A támogatott formátumok a következők: .wav és .mp3.
* A fájlok méretkorlátját érték 5 MB.
* Hitelesítési üzenetek 20 másodperc karakternél rövidebbnek kell lennie. Üzeneteket, amelyek 20 másodpercnél tovább nem a felhasználók elegendő időt hagy válaszolni az ellenőrzési idő lejárta előtt.

### <a name="set-up-a-custom-message"></a>Állítson be egy egyéni üzenet

Az egyéni üzenet létrehozása két részből áll. Először tölt fel az üzenetet, és majd akkor kapcsolja be a felhasználók számára.

Töltse fel az egyéni üzenetet:

1. Hozzon létre egy egyéni hangüzenet a támogatott formátumok valamelyikével.
2. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
3. Keresse meg a multi-factor Authentication kezelési portál utasítások a lap tetején.
4. Kattintson az Azure multi-factor Authentication kezelési portál **Hangüzenetek** konfigurálása szakaszban.
5. A beállítás: Hangos üzenetek lap, kattintson a **új hangüzenet**.
   ![Felhő](./media/multi-factor-authentication-whats-next/custom1.png)
6. A beállítás: új hangüzenet lap, kattintson a **hangfájlok kezelése**.
   ![Felhő](./media/multi-factor-authentication-whats-next/custom2.png)
7. A beállítás: hangkártya fájlok lap, kattintson a **hang-fájl feltöltése**.
   ![Felhő](./media/multi-factor-authentication-whats-next/custom3.png)
8. A beállítás: hang-fájl feltöltése, kattintson **Tallózás** , és keresse meg a hangüzenet, kattintson a **nyitott**.
9. Adjon meg egy leírást, és kattintson a **feltöltése**.
10. A hangüzenet a feltöltést követően üzenet jelzi, hogy a fájl sikeresen feltöltötte.

A felhasználók számára az üzenet bekapcsolása:

1. Kattintson a bal oldali **Hangüzenetek**.
2. A Hangüzeneteknek szakasz alatt kattintson **új hangüzenet**.
3. A nyelvi legördülő jelölje ki a nyelvet.
4. Ha ez az üzenet egy adott alkalmazáshoz, adja meg az alkalmazás bezárásához.
5. Az üzenet típusa legördülő jelölje ki, üzenet felülírni a az új egyéni üzenetet.
6. A hangfájl legördülő menüben válassza ki a feltöltött hangfájlt első részében.
7. Kattintson a **Create** (Létrehozás) gombra. Üzenet jelzi, hogy sikeresen létrejött a hangüzenet.
    ![Felhő](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication hitelesítési gyorsítótárazás
Gyorsítótárazás lehetővé teszi, hogy a későbbi hitelesítési próbálkozások adott időn belül automatikusan sikeres időszak beállítása a megadott idő. Gyorsítótárazás használatos, ha a helyszíni rendszerekre, például VPN sok ellenőrzési kérés küldése, miközben még folyamatban van az első kérésre. Engedélyezi, hogy a felhasználó sikeres folyamatban az első ellenőrzést követően automatikusan sikeres későbbi kérelmeket. 

Gyorsítótárazás nem célja, hogy az Azure AD-bejelentkezések használható.

### <a name="set-up-caching"></a>Gyorsítótárazás beállítása 
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Keresse meg a multi-factor Authentication kezelési portál utasítások a lap tetején.
3. Kattintson az Azure multi-factor Authentication kezelési portál **gyorsítótárazását** konfigurálása szakaszban.
4. Kattintson a **új gyorsítótár** a lap gyorsítótárazásának konfigurálása.
5. Válassza ki a gyorsítótár típusa és a másodpercben mért gyorsítótárazási időt. Kattintson a **Create** (Létrehozás) gombra.

<center>![Felhő](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Megbízható IP-címek
Megbízható IP-címek lehetővé teszi az Azure MFA, amely a felügyelt vagy összevont bérlők rendszergazdái is használja a kétlépéses ellenőrzést. Olyan felhasználóknál, akik a vállalat helyi intraneten bejelentkezik, amellyel. Ez a funkció érhető el az Azure multi-factor Authentication, nem a szabad verziója a rendszergazdák teljes verzióját. Hogyan kérhet az Azure multi-factor Authentication teljes verzióját a részletekért lásd: [Azure multi-factor Authentication](multi-factor-authentication.md).

| Az Azure AD-bérlő típusa | Rendelkezésre álló megbízható IP-beállítások |
|:--- |:--- |
| Managed |<li>Adott IP-címtartományok – a rendszergazdák egy adott IP-címek, amelyek jogosultak a kétlépéses ellenőrzést, a felhasználók számára, hogy a vállalati intraneten bejelentkezés adhat meg.</li> |
| Összevont |<li>Minden külső felhasználók - bejelentkező a a szervezeten belüli összes összevont felhasználók mellőzni fog egy AD FS által kiállított jogcímet segítségével kétlépéses ellenőrzést.</li><br><li>Adott IP-címtartományok – a rendszergazdák egy adott IP-címek, amelyek jogosultak a kétlépéses ellenőrzést, a felhasználók számára, hogy a vállalati intraneten bejelentkezés adhat meg. |

Ez kihagyása csak akkor működik a belül egy vállalati intranetből fogadja. 

**Végfelhasználói élmény a vállalati hálózat belül:**

Megbízható IP-címek is le van tiltva, ha böngésző adatfolyamok szükség a kétlépéses ellenőrzést és alkalmazásjelszók kötelező megadni a régebbi funkciógazdag ügyfélalkalmazások. 

Ha engedélyezve van a megbízható IP-címek, van-e a kétlépéses ellenőrzést *nem* böngésző adatfolyamok szükséges. Alkalmazásjelszókat *nem* szükséges régebbi gazdagügyfél-alkalmazások esetén, feltéve, hogy a felhasználó már még nem létrehozott jelszót. Ha jelszót használja, továbbra is szükséges. 

**Végfelhasználói élmény külső corpnet:**

Megbízható IP-címek engedélyezve van-e, hogy böngésző adatfolyamok szükség a kétlépéses ellenőrzést, és alkalmazásjelszók szükségesek a régebbi funkciógazdag ügyfélalkalmazások. 

### <a name="to-enable-trusted-ips"></a>Megbízható IP-címek engedélyezése
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Ez a cikk elején utasítások a multi-factor Authentication szolgáltatás beállításainak lapra lép.
3. A szolgáltatás beállításai lapon a megbízható IP-címek két lehetőség közül választhat:
   
   * **Az összevont felhasználók intranetről származó kérelmeknél** – jelölje be a jelölőnégyzetet. Minden összevont felhasználók, akik a a vállalati hálózat áthidaló kétlépéses ellenőrzés egy AD FS által kiállított jogcímet használatával jelentkezik be.
   * **A nyilvános IP-címek meghatározott számos érkező kéréseket** – a megadott CIDR jelölésrendszer szövegmezőben adja meg az IP-címek. Például: a tartomány xxx.xxx.xxx.1 – az IP-címekhez xxx.xxx.xxx.0/24 xxx.xxx.xxx.254 vagy xxx.xxx.xxx.xxx/32 egyetlen IP-cím. Megadhatja, mely legfeljebb 50 IP-címtartományok. Jelentkezzen be az alábbi IP-címekről érkező felhasználók kétlépéses ellenőrzés megkerülését.
4. Kattintson a **Save** (Mentés) gombra.
5. Amikor a frissítések telepítve lettek, kattintson a **Bezárás**.

![Megbízható IP-címek](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Alkalmazásjelszavak
Apple Mail és alkalmazások, például az Office 2010 vagy korábbi nem támogatják a kétlépéses ellenőrzést. Azok a második ellenőrzési fogadásához nem konfigurálva. Ezen alkalmazások használatához szüksége "alkalmazásjelszót" helyett a hagyományos jelszavát. Az alkalmazásjelszó lehetővé teszi, hogy az alkalmazás figyelmen kívül hagyása a kétlépéses ellenőrzést, és folytathatja a munkát.

> [!NOTE]
> Modern hitelesítéssel az Office 2013-ügyfelek számára
> 
> Office 2013-ügyfelek (például az Outlook) és újabb támogatási modern hitelesítési protokollok és a kétlépéses ellenőrzéshez használttal működéséhez engedélyezni kell. Miután engedélyezte őket, alkalmazásjelszók esetén nincs szükség az ügyfelek számára.  További információkért lásd: [Office 2013 modern hitelesítés nyilvános előzetes bejelentette](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Feltétlenül tudni az alkalmazásjelszókról
Tudnivalók az alkalmazásjelszókról tudnia kell fontos dolgok listája itt található.

* Alkalmazásjelszók a beviteli mezőbe alkalmazásonként egyszer kell megadni. Felhasználók nyomon követheti, őket, és írja be őket minden egyes nem tartozik.
* A tényleges jelszót automatikusan jön létre, és a felhasználó által nem biztosított. Az automatikusan generált jelszót nehezebben kitalálható támadók, és biztonságosabb.
* Egy legfeljebb 40 jelszó felhasználónként van. 
* Jelszavuk gyorsítótárazása és a helyszíni helyzetekben használhatja az alkalmazásokat is elindíthatják működni, mert az alkalmazásjelszó nem ismeri a szervezeti azonosítóval kívül. Példa: a helyszíni Exchange e-maileket, de az archivált levelek a felhőben van. Ugyanazt a jelszót nem működik.
* A multi-factor authentication szolgáltatás indításakor, a jelszó bizonyos nem böngésző típusú ügyfeleken használható. Alkalmazásjelszók nem használhatja a felügyeleti feladatokhoz. Ellenőrizze a szolgáltatásfiók létrehozása a PowerShell-parancsfájlok futtatásához erős jelszóval, és ne engedélyezze a kétlépéses ellenőrzéshez fiók.

> [!WARNING]
> Alkalmazásjelszók hibrid környezetekben, ahol az ügyfelek kommunikálni mind a helyszíni és a felhőalapú automatikus észlelési végpontok nem működnek. Tartományi jelszó szükséges a helyszíni hitelesítéshez, és alkalmazásjelszókat a felhőalapú szolgáltatással való hitelesítésre van szükség.

### <a name="naming-guidance-for-app-passwords"></a>Alkalmazásjelszók elnevezési útmutató
Jelszó alkalmazásnevek egyeznie kell az eszközt, amelyre használhatók. Ha a hordozható számítógép, amely rendelkezik a böngészőn kívüli alkalmazások, például Laptop elnevezésű létrehozása, és azt. Ezután hozzon létre egy másik asztal ugyanazokat az alkalmazásokat, az asztali számítógép nevű jelszót. 

A Microsoft azt javasolja, hogy nem egy alkalmazásjelszót alkalmazásonként eszközönként egy alkalmazásjelszót létrehozása.

### <a name="federated-sso-app-passwords"></a>Alkalmazásjelszók összevont (SSO)
Az Azure AD összevonás (egyszeri bejelentkezés) az a helyi Windows Server Active Directory tartományi szolgáltatások (AD DS) támogatja. Ha a szervezet az Azure AD össze van vonva, és Azure multi-factor Authentication használni kívánja, majd az információkat az alkalmazásjelszók fontos meg. Ez a szakasz csak vonatkozik az összevont (SSO) ügyfél számára.

* Alkalmazásjelszók az Azure AD által ellenőrzött, és ezért az összevonási megkerülése. Összevonási csak aktívan használt alkalmazásjelszók beállítása során.
* Az összevont felhasználókra (SSO) azt nem fordul az Identity Provider (IdP) szemben a passzív folyammal. A szervezeti azonosító tárolja a jelszavakat. Ha a felhasználó elhagyja a vállalatot, hogy az adatok áramlását a DirSync használatával, a tényleges idő szervezeti azonosítóhoz ki. Fiók letiltása/törlése órát is igénybe vehet legfeljebb három szinkronizálni, ami késlelteti az alkalmazásjelszó letiltását/törlését az Azure ad-ben.
* Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Az alkalmazásjelszó nincs a helyszíni hitelesítés naplózási/naplózási képesség érhető el.
* Bizonyos speciális architekturális tervek szervezeti felhasználónév, a jelszavak és a jelszó lehet szükség, amikor segítségével kétlépéses hitelesítéssel rendelkező ügyfelek. Bár ez attól függ, hogy hol hitelesítéshez. A hitelesítéshez a helyi infrastruktúra-ügyfelek esetében használja a szervezeti felhasználónévvel és jelszóval. A hitelesítést, az Azure AD-ügyfelek esetében használja az alkalmazásjelszót.

  Tegyük fel például, ezek a példányok áll architektúrája:

  * Az Active Directory és az Azure AD a helyi példány összevonja
  * Exchange online használ
  * Lync, amely kifejezetten a helyszíni használ
  * Azure multi-factor Authentication hitelesítés

  ![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

  Ezekben az esetekben akkor kövesse az alábbi lépéseket:

  * Ha aláíró a Lync, használja a szervezet felhasználónevet és jelszót.
  * Ha a címjegyzék keresztül az Outlook ügyfélprogram, amely összeköti az Exchange online elérésére tett kísérlet, használja az alkalmazásjelszót.

### <a name="allow-app-password-creation"></a>Alkalmazás jelszó létrehozásának engedélyezése
Alapértelmezés szerint a felhasználók nem hozhatják létre alkalmazásjelszókat, de saját kezűleg a szolgáltatás használatával engedélyezheti. Alkalmazásjelszók létrehozásának engedélyezése a felhasználóknak, hogy a következő eljárással:

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Ez a cikk elején utasítások a multi-factor Authentication szolgáltatás beállításainak lapra lép.
3. A választógombot jelölje be a **engedélyezése a felhasználóknak alkalmazásjelszavakat jelentkezzen be a böngészőn kívüli alkalmazásokat**.

![Alkalmazásjelszók létrehozása](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Alkalmazásjelszók létrehozása
Felhasználók hozhatnak létre alkalmazásjelszókat a kezdeti regisztráció során. A regisztrációs folyamat, amellyel alkalmazásjelszavak létrehozásának végén egy lehetőség, számukra.

Felhasználók emellett létrehozhatják alkalmazásjelszók regisztrálás után. Az alkalmazásjelszók hozhatnak létre az Azure-portálon vagy az Office 365 portál beállításait. További információk és részletes lépéseket a felhasználók számára: [Mik az Azure multi-factor Authentication alkalmazásjelszók](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Ne feledje a multi-factor Authentication-eszközök számára megbízható felhasználók
A multi-factor Authentication megjegyzése böngészők, hogy felhasználók bizalmi kapcsolat lehetővé teszi az ingyenes minden multi-factor Authentication-felhasználók és eszközök esetében. A multi-factor authentication lehetővé teszi a felhasználóknak kihagyva MFA a beállítása a bejelentkezés után napok száma. Ez a szolgáltatás továbbfejleszti a használhatóság csökkentésével a szám, ahányszor a felhasználó elvégezheti a kapcsolódó kétlépéses ellenőrzés az adott eszközön.

Azonban ha egy fiók vagy eszköz biztonsága sérül, MFA jelszóelőzmények megbízható eszközök hatással lehet a biztonságra. A vállalati fiók biztonságának sérülése esetén vagy egy megbízható eszköz elvesztésekor vagy ellopásakor, ha szeretné [többtényezős hitelesítés visszaállítása az összes eszközön](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Ezzel a művelettel visszavonja az összes eszköz megbízható állapotát, és a felhasználó kétlépéses ellenőrzés visszakapcsolásához elvégzéséhez szükséges. Azt is beállíthatja, hogy a felhasználók a többtényezős hitelesítés visszaállítása utasításait a saját eszközök [a kétlépéses ellenőrzést beállításainak kezelése](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Működés

Jelszóelőzmények a multi-factor Authentication működik beállításával állandó cookie a böngésző, ha egy felhasználó a "Ne jelenjen meg többé a **X** nap" bejelentkezéskor a mezőbe. A felhasználó nem ismét kérni fogja a multi-factor Authentication adott böngészőből mindaddig, amíg a cookie lejár. Ha a felhasználó megnyit egy másik böngészőben ugyanarra az eszközre, vagy törli a cookie-k, meg kell ismételt visszaigazolásához. 

A "Ne jelenjen meg többé a **X** nap" jelölőnégyzet nem jelenik meg a böngészőn kívüli alkalmazásokat, a modern hitelesítést támogatják-e. Ezeket az alkalmazásokat, amelyek új hozzáférési jogkivonatok óránként biztosítanak frissítési jogkivonatokat használja. A frissítési jogkivonat ellenőrzését, az Azure AD ellenőrzi, az utolsó idő kétlépéses ellenőrzés a konfigurált számú napon belül végezte. 

Végül jelszóelőzmények többtényezős hitelesítés a megbízható eszközökön csökkenti a webalkalmazásokban (amely általában kérdés minden alkalommal) számát. De a modern hitelesítési ügyfelek (amely általában kérje meg a 90 naponta) számát is növeli.

> [!NOTE]
>Ez a szolgáltatás esetén nem támogatja a "Bejelentkezve szeretnék maradni" szolgáltatást az AD FS felhasználók hajtsa végre a kétlépéses ellenőrzést, az AD FS az Azure MFA kiszolgáló vagy egy külső MFA-megoldását. Ha az AD FS-ben válassza a "Bejelentkezve szeretnék maradni" és a is megjelölése megbízhatóként eszközüket a multi-factor Authentication a felhasználók, akkor alapesetben nem "Megjegyzése MFA" hány nap lejárata után is ellenőrizheti. Az Azure AD egy friss kétlépéses ellenőrzést igényel, de az AD FS jogkivonatot az eredeti MFA jogcím és a kétlépéses ellenőrzés visszakapcsolásához végzett helyett dátumot adja vissza. Ez a folyamat állít ki ellenőrzési hurkot közötti Azure AD és az AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Ne feledje többtényezős hitelesítés engedélyezése
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Ez a cikk elején utasítások a multi-factor Authentication szolgáltatás beállításainak lapra lép.
3. Kezelheti a felhasználói beállításokat, a szolgáltatás beállításai lapon ellenőrizze a **a felhasználók menthetik a többtényezős hitelesítés a megbízható eszközökön** mezőbe.
   ![Ne feledje eszközök](./media/multi-factor-authentication-whats-next/remember.png)
4. A megbízható eszközök kétlépéses ellenőrzés elkerülésére kívánt napok számának beállítása. Az alapértelmezett érték 14 nap.
5. Kattintson a **Save** (Mentés) gombra.
6. Kattintson a **Bezárás** gombra.

### <a name="mark-a-device-as-trusted"></a>Jelölje meg megbízhatóként eszköz

Ha engedélyezi ezt a szolgáltatást, felhasználók jelölheti meg, ha megbízható eszköz ellenőrzésével bejelentkeznek **rákérdezés nélkül**.

![Ne jelenjen meg többé – képernyőkép](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Választható hitelesítési módszerek
Kiválaszthatja, hogy mely hitelesítési módszerek állnak rendelkezésre a felhasználók számára. Az alábbi táblázat egy rövid áttekintést nyújt az egyes módszerek.

Amikor a felhasználók beléptetik a fiókjukat a multi-factor Authentication, választ ki, hogy engedélyezve van a beállítások az előnyben részesített ellenőrzési módszert. A regisztrációs folyamatot a útmutatást is ismertetjük [a kétlépéses ellenőrzéshez a fiók beállítása](multi-factor-authentication-end-user-first-time.md)

| Módszer | Leírás |
|:--- |:--- |
| Megadott telefonszám hívása |Egy automatizált hang hívás helyezi. A felhasználó fogadja a hívást, és a telefon billentyűzetén hitelesíteni kell nyomnia a #. Ez a telefonszám nem szinkronizálja a helyszíni Active Directory. |
| SMS küldése megadott telefonszámra |Egy megerősítési kódot tartalmazó szöveges üzenetet küld. A felhasználónak vagy válaszolnia kell a szöveg, a megerősítési kódot, vagy a bejelentkezési felületen meg kell adnia a megerősítési kódot üzenet kéri. |
| Értesítés mobilalkalmazáson keresztül |Elküld egy leküldéses értesítést küld telefonjára vagy regisztrált eszközhöz. A felhasználó az értesítési megtekintése és kijelöli **ellenőrizze** igazolásához. <br>A Microsoft Authenticator alkalmazás érhető el [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), és [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Mobilalkalmazás ellenőrzőkódja |A Microsoft Authenticator alkalmazást hoz létre egy új OATH-ellenőrző kódot 30 másodpercenként. A bejelentkezési felületén, a felhasználó beírja ezt a megerősítési kódot.<br>A Microsoft Authenticator alkalmazás érhető el [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), és [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Engedélyezi/letiltja a hitelesítési módszerek hogyan
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Ez a cikk elején utasítások a multi-factor Authentication szolgáltatás beállításainak lapra lép.
3. A szolgáltatás beállításai lapon a ellenőrzési beállítások kiválasztása vagy eltávolításához a használni kívánt beállításokat.
   ![Ellenőrzési lehetőségek](./media/multi-factor-authentication-whats-next/authmethods.png)
4. Kattintson a **Save** (Mentés) gombra.
5. Kattintson a **Bezárás** gombra.
