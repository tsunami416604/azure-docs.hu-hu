---
title: A fiók Kéttényezős hitelesítésével kapcsolatos gyakori problémák – Azure AD
description: Megoldások a leggyakoribb kétfaktoros ellenőrzési problémákhoz, valamint a munkahelyi vagy iskolai fiókhoz.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 8d2e34bcfd180dfeb814dace2a496f3ac593c5bf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738608"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>A kétfaktoros ellenőrzéssel és a munkahelyi vagy iskolai fiókkal kapcsolatos gyakori problémák

Ha a Azure Active Directory-(Azure AD-) szervezet bekapcsolja a kétfaktoros ellenőrzést, a munkahelyi vagy iskolai fiókhoz való bejelentkezéshez a Felhasználónév, a jelszó, valamint a mobileszköz vagy telefon kombinációja szükséges. Sokkal biztonságosabb, mint a jelszó, és a hitelesítés két formájára támaszkodik: valami, amit ismer, és Önnek is van. A kétfaktoros ellenőrzés segít megakadályozni, hogy a rosszindulatú hackerek az Ön számára legyenek, mert még ha a jelszavuk is van, az esélyek arra, hogy nem rendelkeznek az eszközzel.

<center>

![Fogalmi hitelesítési módszerek képe](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Vannak olyan gyakori kétfaktoros ellenőrzési problémák, amelyek gyakrabban fordulnak elő, mint bármelyikünk. Ezt a cikket összeállítottuk a leggyakoribb problémák és néhány lehetséges javítás kezelésére.

>[!Important]
>Ha Ön rendszergazda, további információt talál arról, hogyan állíthatja be és kezelheti Azure AD-környezetét az [Azure ad dokumentációjában](https://docs.microsoft.com/azure/active-directory).
>
>Ezt a tartalmat kizárólag munkahelyi vagy iskolai fiókjával való használatra szánjuk, amelyet a Szervezete (például: alain@contoso.com ) biztosít. Ha problémák merülnek fel a kétfaktoros ellenőrzéssel és a személyes Microsoft-fiókával, azaz egy olyan fiókkal, amelyet saját maga állított be (például danielle@outlook.com ), tekintse [meg a kétfaktoros ellenőrzés bekapcsolását a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nekem nincs mobileszközom

Ez történik. A mobileszköz otthon maradt, és most már nem használhatja a telefont annak ellenőrzéséhez, hogy ki vagy. Ha korábban már hozzáadott egy másik módszert a fiókjába való bejelentkezéshez, például az irodai telefonhoz, akkor ezt a metódust kell használnia. Ha soha nem adott meg további ellenőrzési módszert, lépjen kapcsolatba a szervezet ügyfélszolgálatával, és kérje meg, hogy segítsen Önnek a fiókjához való visszatérésben.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Bejelentkezés munkahelyi vagy iskolai fiókba más ellenőrzési módszer használatával

1. Jelentkezzen be a fiókjába, de a **kétfaktoros ellenőrző** lapon válassza a **bejelentkezés másik módon** hivatkozását.

    ![Bejelentkezés ellenőrzési módszerének módosítása](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Ha nem látja a **Bejelentkezés más módon** hivatkozást, az azt jelenti, hogy nem állított be semmilyen más ellenőrzési módszert. Kérje meg a rendszergazdát, hogy jelentkezzen be a fiókjába.

2. Válassza ki az alternatív ellenőrzési módszert, és folytassa a kétfaktoros ellenőrzési folyamattal.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Elveszett a mobileszköz, vagy ellopták

Ha elvesztette vagy elveszítette a mobileszköz ellopását, más módszerekkel is bejelentkezhet, vagy megkérheti a szervezet ügyfélszolgálatát, hogy törölje a beállításokat. Nyomatékosan javasoljuk, hogy a munkahelyi ügyfélszolgálat tudja, hogy a telefonja elveszett vagy ellopták-e, így a megfelelő frissítések elvégezhető a fiókjában. A beállítások törlése után a rendszer felszólítja, hogy regisztráljon a [kétfaktoros ellenőrzésre](multi-factor-authentication-end-user-first-time.md) a következő bejelentkezés alkalmával.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Nem kapok a mobileszközön eljuttatott ellenőrző kódot

Az ellenőrző kód beszerzése nem gyakori probléma, és általában a mobileszköz és a hozzá tartozó beállításokhoz kapcsolódik. Néhány lehetséges dolog a kipróbáláshoz:

Próbálja ki ezt | Útmutató információk
--------- | ------------
Mobil eszköz újraindítása | Előfordulhat, hogy az eszköz csak a frissítésre van szüksége. Az eszköz újraindítása véget ér minden olyan háttérben futó folyamatnak vagy szolgáltatásnak, amely éppen fut, és problémákat okozhat, valamint az eszköz alapvető összetevőinek frissítését, újraindítását, ha egy bizonyos ponton összeomlott.
Ellenőrizze, hogy helyesek-e a biztonsági adatok | Győződjön meg arról, hogy a biztonsági ellenőrzési módszer adatai pontosak, különösen a telefonszámok. Ha helytelen telefonszámot helyez üzembe, az összes riasztás nem megfelelő számú. Szerencsére a felhasználó nem fog tudni bármit megtenni a riasztásokkal, de nem segít bejelentkezni a fiókjába. Az adatok helyességének biztosításához tekintse meg a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikket.
Ellenőrizze, hogy be vannak-e kapcsolva az értesítések | Győződjön meg arról, hogy a mobileszköz értesítések be vannak kapcsolva, és hogy olyan értesítési módszert jelölt ki, amely lehetővé teszi a telefonhívásokat, a hitelesítési alkalmazást és az üzenetkezelési alkalmazást (szöveges üzenetek esetén), hogy látható riasztási értesítéseket küldjön a mobileszközön.
Győződjön meg arról, hogy rendelkezik egy eszköz-és internetkapcsolattal | Győződjön meg arról, hogy a telefonhívások és a szöveges üzenetek bekerülnek a mobileszközön. Ismerőse van, és SMS-üzenetet küld Önnek, hogy mindkét fél megkapja. Ha nem, először ellenőrizze, hogy a mobileszköz be van-e kapcsolva. Ha az eszköz be van kapcsolva, de még nem kérdezi le a hívást vagy a szöveget, valószínűleg probléma van a hálózattal, és a szolgáltatóval kell kommunikálnia. Ha gyakran vannak jelekkel kapcsolatos problémák, javasoljuk, hogy telepítse és használja a [Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md) a mobileszközön. A hitelesítő alkalmazás véletlenszerű biztonsági kódokat tud létrehozni a bejelentkezéshez, és nem igényel semmilyen cellahivatkozást vagy internetkapcsolatot.
Kikapcsolás ne zavarja | Győződjön meg arról, hogy nincs bekapcsolva a mobileszköz **ne zavarja** a funkcióját. Ha ez a funkció be van kapcsolva, az értesítések nem kaphatnak riasztást a mobileszközön. A szolgáltatás kikapcsolásával kapcsolatos útmutatásért tekintse meg a mobileszköz kézikönyvét.
Telefonszámok feloldásának feloldása | A Egyesült Államok a Microsoft hanghívásai a következő számokból származnak: + 1 (866) 539 4191, + 1 (855) 330 8653 és + 1 (877) 668 6536.
Akkumulátorral kapcsolatos beállítások keresése | Ez úgy tűnik, hogy egy kicsit furcsa a felületen, de ha úgy állította be az akkumulátor-optimalizálást, hogy leállítsa a kevésbé használt alkalmazásokat a háttérben, akkor az értesítési rendszer valószínűleg érintett. A probléma megoldásához kapcsolja ki az akkumulátor-optimalizálást a hitelesítési alkalmazáshoz és az üzenetkezelési alkalmazáshoz, majd próbálkozzon újra a fiókba való bejelentkezéssel.
Külső gyártótól származó biztonsági alkalmazások letiltása | Ha olyan alkalmazással rendelkezik, amely megvédi a szöveges üzeneteket vagy telefonhívásokat az ismeretlen hívók minimalizálására, akkor előfordulhat, hogy a rendszer megakadályozza az ellenőrző kód fogadását. Próbálja meg letiltani a harmadik féltől származó biztonsági alkalmazásokat a telefonján, majd kérjen egy másik ellenőrző kódot.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nem kapok Rákérdezés a második ellenőrzési információra

Ha a felhasználónevével és jelszavával jelentkezett be a munkahelyi vagy az iskolai fiókjába, de még nem kérték a további biztonsági ellenőrzési információk megadását, akkor lehet, hogy még nem állította be az eszközt. A mobileszköz beállítását úgy kell beállítani, hogy az adott további biztonsági ellenőrzési módszerrel működjön. Győződjön meg arról, hogy bekapcsolta a mobileszköz használatát, és hogy az elérhető az ellenőrzési módszerhez. a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben talál további információt. Ha tudja, hogy nem állította be az eszközt vagy a fiókját, a [saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben ismertetett lépéseket követve teheti meg.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Új telefonszámot kaptam, és szeretném felvenni

Ha új telefonszámot kaptunk, frissítenie kell a biztonsági ellenőrzési módszer részleteit, hogy az ellenőrző kérések a megfelelő helyre lépjenek. Az ellenőrzési módszer frissítéséhez kövesse a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) című cikk a **telefonszám hozzáadása vagy módosítása** című szakaszának lépéseit.

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Új mobileszköz lettem, és szeretném felvenni

Ha új mobileszköz van, akkor be kell állítania, hogy működjön a kétfaktoros ellenőrzéssel. Ez egy több lépésből álló megoldás:

1. Állítsa be az eszközt a munkahelyi vagy iskolai fiókjával való együttműködéshez a [saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben ismertetett lépéseket követve.

1. Frissítse a fiókját és az eszköz adatait a **további biztonsági ellenőrzés** oldalon, törölje a régi eszközt, és adja hozzá az újat. További információt a [kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben talál.

Nem kötelező lépések:

- Töltse le, telepítse és állítsa be a Microsoft Authenticator alkalmazást a mobileszközön a [Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben ismertetett lépéseket követve.

- Kapcsolja be a kétfaktoros ellenőrzést a megbízható eszközökhöz a kétfaktoros [ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) című cikk a kéttényezős ellenőrzési **kérések megbízható eszközön** című részében ismertetett lépéseket követve.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Problémák léptek fel a mobil eszközön való bejelentkezés közben

Előfordulhat, hogy a mobileszköz-alapú ellenőrzési módszer (például szöveges üzenetküldés) használata még nehezebbé válik, miközben nemzetközi helyen dolgozik. Azt is megteheti, hogy a mobileszköz barangoló díjat von maga után. Ebben az esetben javasoljuk, hogy használja a Microsoft Authenticator alkalmazást a Wi-Fi elérési ponthoz való csatlakozás lehetőségével. A Microsoft Authenticator alkalmazás mobileszközön való letöltésével, telepítésével és beállításával kapcsolatos további információkért tekintse meg a [Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikket.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nem tudom beolvasni az alkalmazás jelszavait

Az alkalmazás jelszavai lecserélik a normál jelszót a régebbi asztali alkalmazások esetében, amelyek nem támogatják a kétfaktoros ellenőrzést. Először ellenőrizze, hogy helyesen írta-e be a jelszót. Ha ez nem oldja meg, akkor az alkalmazás jelszavainak [kezelése a kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) című cikkben ismertetett lépéseket követve hozzon létre egy új alkalmazást az alkalmazás jelszavának létrehozásához **és törléséhez** .

## <a name="i-cant-turn-two-factor-verification-off"></a>Nem kapcsolhatom ki a kétfaktoros ellenőrzést

Ha a kétfaktoros ellenőrzést használja munkahelyi vagy iskolai fiókjával (például alain@contoso.com ), az valószínűleg azt jelenti, hogy a szervezet úgy döntött, hogy ezt a hozzáadott biztonsági funkciót kell használnia. Mivel a szervezet úgy döntött, hogy ezt a funkciót kell használnia, nincs mód arra, hogy önállóan kikapcsolja. Ha azonban a kéttényezős ellenőrzést személyes fiókkal használja, például a szolgáltatás be-és kikapcsolását alain@outlook.com is lehetővé teszi. A személyes fiókok kétfaktoros ellenőrzésének vezérlésével kapcsolatos útmutatásért lásd: [kétfaktoros ellenőrzés bekapcsolása vagy kikapcsolása a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Ha nem tudja kikapcsolni a kétfaktoros ellenőrzést, akkor a szervezeti szinten alkalmazott biztonsági beállítások miatt is előfordulhat. További információ a biztonsági alapbeállításokról: [Mi a biztonsági alapértékek?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nem találok választ a problémára

Ha kipróbálta ezeket a lépéseket, de továbbra is problémákat tapasztal, forduljon a szervezet ügyfélszolgálatához segítségért.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md)

- [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)
