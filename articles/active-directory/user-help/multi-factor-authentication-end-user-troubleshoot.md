---
title: Gyakori problémák a fiók-hitelesítéssel & megoldásokkal – Azure AD
description: Ismerje meg a leggyakoribb kétfaktoros ellenőrzési problémákkal és a munkahelyi vagy iskolai fiókkal kapcsolatos lehetséges problémákat és megoldásokat.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 3f44884f54278f7b4d07a265604f4a256d66e8a8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062540"
---
# <a name="common-problems-and-solutions-with-two-factor-verification-and-your-work-or-school-account"></a>Gyakori problémák és megoldások kétfaktoros ellenőrzéssel és munkahelyi vagy iskolai fiókkal

A szervezet bekapcsolta a kétfaktoros ellenőrzést, ami azt jelenti, hogy a munkahelyi vagy iskolai fiókja most már a Felhasználónév, a jelszó, valamint a mobileszköz vagy telefon kombinációját igényli. A szervezet bekapcsolta ezt az extra ellenőrzést, mert az sokkal biztonságosabb, mint a jelszó, és a hitelesítés két formájára támaszkodik. A kétfaktoros ellenőrzés segít megakadályozni, hogy a rosszindulatú hackerek az Ön számára legyenek, mert még ha a jelszavuk is van, az esélye, hogy nem rendelkezik az eszközzel.

Vannak olyan gyakori kétfaktoros ellenőrzési problémák, amelyek gyakrabban fordulnak elő, mint bármelyikünk. Ebben a cikkben a leggyakoribb problémákkal és néhány lehetséges javítással foglalkozunk.

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.
>
>Ez a tartalom csak a munkahelyi vagy iskolai fiókjával, a szervezet által megadott fiókkal (például alain@contoso.com) való használatra szolgál. Ha problémái vannak a kétfaktoros ellenőrzéssel és a személyes Microsoft-fiókával, akkor a saját maga által beállított fiókkal (például danielle@outlook.com) kapcsolatban lásd: a [kétfaktoros ellenőrzés bekapcsolása a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-forgot-my-mobile-device-at-home"></a>Elfelejtettem a mobileszközom otthon

Ez történik. Meghagyta a mobileszköz otthonát, és most már nem használhatja a telefont annak ellenőrzéséhez, hogy ki mondta. Ha korábban már hozzáadott egy másik módszert a fiókjába való bejelentkezéshez, például az irodai telefonhoz, akkor ezt a metódust kell használnia. Ha soha nem adott meg további ellenőrzési módszert, akkor kapcsolatba kell lépnie az ügyfélszolgálattal, és segítségre van szüksége a fiókhoz való visszatéréshez.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Bejelentkezés munkahelyi vagy iskolai fiókba más ellenőrzési módszer használatával

1. Normál bejelentkezéssel jelentkezzen be a fiókjába, és a **kétfaktoros ellenőrző** lapon válassza a **bejelentkezés másik módon** hivatkozását.

    ![Bejelentkezés ellenőrzési módszerének módosítása](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Ha nem látja a **Bejelentkezés más módon** hivatkozást, az azt jelenti, hogy nem állított be semmilyen más ellenőrzési módszert. Kérje meg a rendszergazdát, hogy jelentkezzen be a fiókjába.

2. Válassza ki az alternatív ellenőrzési módszert, és folytassa a kétfaktoros ellenőrzési folyamattal.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Elveszett a mobileszköz, vagy ellopták

Ha elvesztette vagy megkapta a mobileszköz ellopását, más módszerrel is bejelentkezhet, vagy megkérheti az ügyfélszolgálatot, hogy törölje a beállításait. Határozottan javasoljuk, hogy tájékoztassa az ügyfélszolgálatot arról, hogy a telefonja elveszett vagy ellopták, így a megfelelő frissítések elhelyezhetők a fiókjával. A beállítások törlése után a rendszer felszólítja, hogy regisztráljon a [kétfaktoros ellenőrzésre](multi-factor-authentication-end-user-first-time.md) a következő bejelentkezés alkalmával.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Nem kapok a mobileszközön eljuttatott ellenőrző kódot

Az ellenőrző kód beszerzése nem gyakori probléma, és általában a mobileszköz és a hozzá tartozó beállításokhoz kapcsolódik. Néhány lehetséges dolog a kipróbáláshoz:

- **Indítsa újra a mobileszköz-eszközt.** Előfordulhat, hogy az eszköz csak a frissítésre van szüksége. Az eszköz újraindítása véget ér minden olyan háttérben futó folyamatnak vagy szolgáltatásnak, amely éppen fut, és problémákat okozhat, valamint az eszköz alapvető összetevőinek frissítését, újraindítását, ha egy bizonyos ponton összeomlott.

- **Ellenőrizze, hogy helyesek-e a biztonsági adatok.** Győződjön meg arról, hogy a biztonsági ellenőrzési módszer adatai pontosak, különösen a telefonszámok. Ha helytelen telefonszámot helyez üzembe, az összes riasztás nem megfelelő számú. Szerencsére a felhasználó nem fog tudni bármit megtenni a riasztásokkal, de nem segít bejelentkezni a fiókjába. Az adatok helyességének biztosításához tekintse meg a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikket.

- **Ellenőrizze, hogy az értesítések be vannak-e kapcsolva.** Győződjön meg arról, hogy a mobileszköz értesítések be vannak kapcsolva, és hogy olyan értesítési módszert jelölt ki, amely lehetővé teszi a telefonhívásokat, a hitelesítési alkalmazást és az üzenetkezelési alkalmazást (szöveges üzenetek esetén), hogy látható riasztási értesítéseket küldjön a mobileszközön.

- **Győződjön meg arról, hogy rendelkezik egy eszköz-és internetkapcsolattal.** Győződjön meg arról, hogy a telefonhívások és a szöveges üzenetek bekerülnek a mobileszközön. Ismerőse van, és SMS-üzenetet küld Önnek, hogy mindkét fél megkapja. Ha nem, először ellenőrizze, hogy a mobileszköz be van-e kapcsolva. Ha az eszköz be van kapcsolva, de még nem kérdezi le a hívást vagy a szöveget, valószínűleg probléma van a hálózattal, és a szolgáltatóval kell kommunikálnia. Ha gyakran vannak jelekkel kapcsolatos problémák, javasoljuk, hogy telepítse és használja a [Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md) a mobileszközön. A hitelesítő alkalmazás véletlenszerű biztonsági kódokat tud létrehozni a bejelentkezéshez, és nem igényel semmilyen cellahivatkozást vagy internetkapcsolatot.

- **A kikapcsolás nem zavarja.** Győződjön meg arról, hogy nincs bekapcsolva a mobileszköz **ne zavarja** a funkcióját. Ha ez a funkció be van kapcsolva, az értesítések nem kaphatnak riasztást a mobileszközön. A szolgáltatás kikapcsolásával kapcsolatos útmutatásért tekintse meg a mobileszköz kézikönyvét.

- **Telefonszámok feloldásának feloldása** A Egyesült Államok a Microsoft hanghívásai a következő számokból származnak: + 1 (866) 539 4191, + 1 (855) 330 8653 és + 1 (877) 668 6536.

- **Az akkumulátorral kapcsolatos beállítások megkeresése.** Ez úgy tűnik, hogy egy kicsit furcsa a felületen, de ha úgy állította be az akkumulátor-optimalizálást, hogy leállítsa a kevésbé használt alkalmazásokat a háttérben, akkor az értesítési rendszer valószínűleg érintett. A probléma megoldásához kapcsolja ki az akkumulátor-optimalizálást a hitelesítési alkalmazáshoz és az üzenetkezelési alkalmazáshoz, majd próbálkozzon újra a fiókba való bejelentkezéssel.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nem kapok Rákérdezés a második ellenőrzési információra

Ha a felhasználónevével és jelszavával jelentkezett be a munkahelyi vagy az iskolai fiókjába, de nem kérték a további biztonsági ellenőrzési információk megadását, előfordulhat, hogy még nem állította be az eszközt. A mobileszköznek kifejezetten be kell állítania, hogy működjön a további biztonsági ellenőrzési módszerekkel. Győződjön meg arról, hogy bekapcsolta a mobileszköz használatát, és hogy az elérhető az ellenőrzési módszerhez. a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben talál további információt. Ha tudja, hogy nem állította be az eszközt vagy a fiókját, a [saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben ismertetett lépéseket követve teheti meg.

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>Új telefonszámom van, hogyan változtathatom meg a kétfaktoros ellenőrzéshez?

Ha új telefonszámot kaptunk, frissítenie kell a biztonsági ellenőrzési módszer részleteit, hogy az ellenőrző kérések a megfelelő helyre lépjenek. Az ellenőrzési módszer frissítéséhez kövesse a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) című cikk a **telefonszám hozzáadása vagy módosítása** című szakaszának lépéseit.

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>Új mobileszköz van, Hogyan adhatom hozzá?

Ha új mobileszköz van, akkor be kell állítania, hogy működjön a kétfaktoros ellenőrzéssel. Ez egy több lépésből álló megoldás:

1. Állítsa be az eszközt a munkahelyi vagy iskolai fiókjával való együttműködéshez a [saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben ismertetett lépéseket követve.

2. Frissítse a fiókját és az eszköz adatait a **további biztonsági ellenőrzés** oldalon, törölje a régi eszközt, és adja hozzá az újat. További információt a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben talál.

3. Választható. Töltse le, telepítse és állítsa be a Microsoft Authenticator alkalmazást a mobileszközön a [Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben ismertetett lépéseket követve.

4. Választható. Kapcsolja be a kétfaktoros ellenőrzést a megbízható eszközökhöz a kétfaktoros [ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) című cikk a kéttényezős ellenőrzési **kérések megbízható eszközön** című részében ismertetett lépéseket követve.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Problémák léptek fel a mobil eszközön való bejelentkezés közben

Előfordulhat, hogy a mobileszköz-alapú ellenőrzési módszer (például szöveges üzenetküldés) használata még nehezebbé válik, miközben nemzetközi helyen dolgozik. Azt is megteheti, hogy a mobileszköz barangoló díjat von maga után. Ebben az esetben javasoljuk, hogy használja a Microsoft Authenticator alkalmazást a Wi-Fi elérési ponthoz való csatlakozás lehetőségével. A Microsoft Authenticator alkalmazás mobileszközön való letöltésével, telepítésével és beállításával kapcsolatos további információkért tekintse meg a [Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikket.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nem tudom beolvasni az alkalmazás jelszavait

Az alkalmazás jelszavai lecserélik a normál jelszót a régebbi asztali alkalmazások esetében, amelyek nem támogatják a kétfaktoros ellenőrzést. Először ellenőrizze, hogy helyesen írta-e be a jelszót. Ha ez nem oldja meg, akkor az alkalmazás jelszavainak [kezelése a kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) című cikkben ismertetett lépéseket követve hozzon létre egy új alkalmazást az alkalmazás jelszavának létrehozásához **és törléséhez** .

## <a name="why-cant-i-turn-two-factor-verification-off"></a>Miért nem kapcsolhatom ki a kétfaktoros ellenőrzést?

Ha kétfaktoros ellenőrzést használ munkahelyi vagy iskolai fiókjával (például alain@contoso.com), az valószínűleg azt jelenti, hogy a szervezet úgy döntött, hogy ezt a hozzáadott biztonsági szolgáltatást kell használnia. Mivel a szervezet úgy döntött, hogy ezt a funkciót kell használnia, nincs mód arra, hogy önállóan kikapcsolja. Ha azonban a kéttényezős ellenőrzést személyes fiókkal (például alain@outlook.com) használja, lehetősége van a funkció be-és kikapcsolására. A személyes Microsoft-fiókok kétfaktoros ellenőrzésének vezérlésével kapcsolatos útmutatásért lásd: [kétfaktoros ellenőrzés bekapcsolása vagy kikapcsolása a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nem találok választ a problémára

Ha kipróbálta ezeket a lépéseket, de továbbra is problémákat tapasztal, segítségért forduljon az ügyfélszolgálathoz.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md)

- [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)
