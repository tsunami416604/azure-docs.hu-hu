---
title: A biztonsági adatait – Azure Active Directory kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kell biztonsági adatait, beleértve a kétlépéses ellenőrzési beállítások használata kezelheti.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 4cd88490118343a9a00dbd919d7820f0334df92e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016146"
---
# <a name="manage-your-security-info-preview"></a>A biztonsági adatok (előzetes verzió) kezelése

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Használhatja a biztonsági adatait, jelentkezzen be munkahelyi vagy iskolai fiókjával, vagy a jelszó.

Amikor bejelentkezik, attól függően, a szervezet beállítások, megjelenhet egy jelölőnégyzet, amely szerint, **ne jelenjen meg többé az X nap**. Ez a jelölőnégyzet lehetővé teszi marad a számú napig engedélyezi a rendszergazda anélkül, hogy reverification jelentkezett be az eszközt.

## <a name="change-your-info"></a>Az adatok módosítása
Frissítés vagy biztonsági adatok megadása, vagy módosíthatja az alapértelmezett, a rendszergazda és a szervezet által engedélyezett alapján.

### <a name="to-change-your-info"></a>Az adatok módosítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2. Nyissa meg myapps.microsoft.com, jelölje ki a nevet az oldal jobb felső sarokban, majd válassza ki **profil**.

3. Az a **fiók kezelése** területen válassza **biztonsági adatok szerkesztése**.

    ![Profil képernyő és Szerkesztés biztonsági információ hivatkozás kiemelésével](media/security-info/security-info-profile.png)

4. Az alapértelmezett módszer a hozzáférés jóváhagyása és a tekintse meg az aktuális biztonsági adatai adatait, ha a rendszergazda úgy állította be a felhasználói élményt a szervezet számára.

5. Az a **a fiókja biztonságának megőrzéséhez** lapon is:

    - Válassza ki **adja hozzá a biztonsági adatok** további módszerek hozzáadása.

    - Válassza ki **alapértelmezés módosítása** az alapértelmezett módszer módosításához.

    - Válassza ki a **ceruza** ikon mellett egy meglévő módszer az adatok frissítéséhez.

    ![Biztonsági adatai képernyőjén, meglévő, szerkeszthető információval](media/security-info/security-info-edit.png)

6. Miután elvégezte a módosításokat, is elhagyja a lapot, és menti a módosításokat.

Ha nem látja ezeket a beállításokat, vagy Ön nem fér hozzá a myapps.microsoft.com lap, akkor lehet, hogy a szervezete egyéni beállításokat, vagy egy egyéni oldal. További segítségért forduljon a rendszergazdához kell.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>Az elveszett vagy vélhetően feltört eszközök biztonsági adatok kezelése

Ha az eszköz elvesztése vagy az eszköz biztonságának sérülése esetén kell újra elvégzi az ellenőrzési folyamat összes a korábban megbízhatónak minősített eszközökön.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>Elveszett vagy vélhetően feltört eszközök esetén a biztonsági adatok kezelése

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2. Nyissa meg myapps.microsoft.com, jelölje ki a nevet az oldal jobb felső sarokban, majd válassza ki **profil**.

3. Az a **fiók kezelése** területen válassza **MFA elfelejtette a korábban megjegyzett eszközökön**.
    
    Ez a beállítás azt jelenti, hogy nyissa meg a multi-factor Authentication (MFA) folyamatot újra bejelentkezés után kell kiválasztása.

    ![Profil képernyő és a törlés hivatkozás kiemelésével](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>Gyakori problémák és megoldások a biztonsági adatait

Ez a cikk segít, hogy a biztonsági adatait, beleértve a kétlépéses ellenőrzés kapcsolatos problémák elhárítása.

|Probléma|Megoldás|
|-------|--------|
|Nem rendelkezem telefonom velem megosztva|Annak lehetséges, hogy nem rendelkezik a telefonját mindig, de hogy fog továbbra is szeretné jelentkezzen be a munkahelyi vagy iskolai fiókjával. A probléma megoldásához, bejelentkezhet egy másik hitelesítési módszerrel, amely nem igényel a telefonra, például az irodai telefonszámát. Adja hozzá a további metódusokat hozzá biztonsági adataihoz, kövesse a lépéseket a [módosítása az adatok](#change-your-info) szakaszban.|
|A telefon elvesztése vagy azt ellopták|Sajnos a telefon, vagy hogy mérsékelje elvesztése fordulhat elő. Ebben az esetben azt javasoljuk, hogy, tájékoztassa a szervezetet, így az informatikai részleg alaphelyzetbe állíthatja az alkalmazásjelszókat, és törölje a megbízható eszközök listájáról eszközök bármely megjegyzett. A saját megbízható eszközök felejtse el a lépéseket követve a [az elveszett vagy vélhetően feltört eszközök biztonsági adatok kezelése](#manage-your-security-info-for-a-lost-or-potentially-compromised-device) szakaszban.|
|Új telefonszám kaptam|A probléma megoldásának két módja van. Bejelentkezhet, és nem igényel a telefonszámát, e-mail, például másik hitelesítési módszert használ, vagy ha ez nem egy beállítást, forduljon a szervezet informatikai személyzetet tart fenn, és azok a beállítások törlése. Adja hozzá a további metódusokat hozzá biztonsági adataihoz, kövesse a lépéseket a [módosítása az adatok](#change-your-info) szakaszban.|
|Az alapértelmezett módszer nem megfelelő|Az alapértelmezett módszer a biztonsági beállítások között frissítheti. Konkrét részletekért nyissa meg a [módosítása az adatok](#change-your-info) szakaszban.|
|E nem érkeznek meg egy szöveget, vagy hívja a mobil eszközömre|Ha már sikeresen kapott szövegek vagy telefonhívások a mobileszközére a múltban, probléma legvalószínűbb a telefonos szolgáltatón, a fiókja nem. Győződjön meg arról, hogy a helyes cella jelet kapunk, és tudja fogadni, SMS-EK és a telefonhívások. Megkérheti, hogy egy ismerőse hívás vagy szöveges üzenet, teszteléshez használhat.<br><br>Ha is sikeresen üzeneteket fog kapni, szöveg és a telefon, de még nem található az értesítés, próbálja meg más módszerrel. A lépéseket követve a biztonsági adataimhoz további módszereket is hozzáadhat a [módosítása az adatok](#change-your-info) szakaszban. Nem kell hozzáadni egy másik módszert, ha a céges ügyfélszolgálattól, és kérje meg őket, törölje a beállításokat, így beállíthatja a módszer a következő bejelentkezéskor.<br><br>Ha gyakran rossz cella fogadás miatt, akkor javasoljuk, használja a Microsoft Authenticator alkalmazást a mobileszközén. Az alkalmazás való bejelentkezéshez használt véletlenszerű biztonsági kódokat generálhat, és ezek a kódok nem igénylik bármilyen cella jel vagy internetes kapcsolatot. A Microsoft Authenticator alkalmazással kapcsolatos további információkért tekintse meg a [Ismerkedés a Microsoft Authenticator alkalmazás](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) cikk.|
|A jelen táblázatban lévő lehetőségek egyike már megoldott probléma|Ha már próbálkozott ezeket a hibaelhárítási lépéseket, de futó ütközne; a céges ügyfélszolgálattól, képes segítséget kell lennie.|

## <a name="next-steps"></a>További lépések

- További információ a biztonsági adatok [Security info (előzetes verzió) – áttekintés](user-help-security-info-overview.md).

- További tudnivalók a kétlépéses ellenőrzést a [kétlépéses ellenőrzési áttekintése](user-help-two-step-verification-overview.md) cikk. 

- Kövesse az alábbi útmutató cikkek, hogyan állíthatja be az eszközök a biztonsági adatok területen:

    - [A biztonsági adatok hitelesítő alkalmazással történő hitelesítésének beállítása](security-info-setup-auth-app.md)

    - [A biztonsági adatok telefonhívással történő hitelesítésének beállítása](security-info-setup-phone-number.md)

    - [Biztonsági adatok beállítása a szöveges üzenetek használata](security-info-setup-text-msg.md)

    - [A biztonsági adatok e-mailben történő hitelesítésének beállítása](security-info-setup-email.md)

    - [Állítsa be a biztonsági adatok biztonsági kérdések használata](security-info-setup-questions.md)

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.