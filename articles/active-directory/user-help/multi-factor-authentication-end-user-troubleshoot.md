---
title: Gyakori problémák a kétfaktoros fiókhitelesítéssel kapcsolatban - Azure AD
description: Megoldások a leggyakoribb kétfaktoros ellenőrzési problémákra és a munkahelyi vagy iskolai fiókra.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: c28b63749cfdbcd16b94cbd3ca7dd4023f46a351
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897727"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Gyakori problémák a kétfaktoros ellenőrzéssel és a munkahelyi vagy iskolai fiókkal

Amikor az Azure Active Directory (Azure AD) szervezete bekapcsolja a kétfaktoros ellenőrzést, a munkahelyi vagy iskolai fiókba való bejelentkezéshez a felhasználónév, a jelszó, valamint egy mobileszköz vagy telefon kombinációjára van szükség. Ez biztonságosabb, mint egy jelszó, támaszkodva két formája hitelesítés: valami, amit tud, és valami van veled. A kétfaktoros ellenőrzés segíthet megakadályozni, hogy a rosszindulatú hackerek úgy tegyenek, mintha ön lennének, mert még ha van is jelszavajuk, akkor is előfordulhat, hogy nem rendelkeznek az Ön eszközével.

<center>

![Koncepcionális hitelesítési módszerek képe](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Van néhány gyakori kéttényezős ellenőrzési probléma, amely úgy tűnik, hogy gyakrabban fordul elő, mint bármelyikünk szeretné. Ezt a cikket a leggyakoribb problémák és néhány lehetséges javítás kezelésére állítottuk össze.

>[!Important]
>Ha Ön rendszergazda, az Azure AD-környezet beállításával és kezelésével kapcsolatos további információkért az [Azure AD dokumentációjában](https://docs.microsoft.com/azure/active-directory)olvashat.
>
>Ez a tartalom is csak a munkahelyi vagy iskolai fiókjához használható, amely a szervezet alain@contoso.comáltal biztosított fiók (például ). Ha problémái vannak a kétfaktoros ellenőrzéssel és a személyes Microsoft-fiókjával, amely egy danielle@outlook.comsaját maga által beállított fiók (például), olvassa el [a Kétfaktoros ellenőrzés be- és kikapcsolása a Microsoft-fiókjához című témakört.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nincs nálam a mobileszközöm

Megesik az ilyen. Otthon hagyta a mobileszközt, és most nem tudja a telefonjával ellenőrizni, hogy ki ön. Ha korábban más módszert adott meg a fiókba való bejelentkezéshez, például az irodai telefonjára, akkor most már használhatja ezt a módszert. Ha soha nem adott meg további ellenőrzési módszert, kapcsolatba kell lépnie a szervezet ügyfélszolgálatával, és meg kell kapnia őket, hogy segítsenek visszatérni a fiókjába.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Bejelentkezés munkahelyi vagy iskolai fiókjába más ellenőrzési módszerrel

1. Jelentkezzen be a fiókjába, de a **Kétfaktoros ellenőrzés** oldalon válassza a **Bejelentkezés más módon** hivatkozást.

    ![Bejelentkezési ellenőrzési módszer módosítása](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Ha nem látja a **Sign más módon** linket, az azt jelenti, hogy nem állított be más ellenőrzési módszereket. A fiókba való bejelentkezéshez forduljon a rendszergazdához.

2. Válassza ki az alternatív ellenőrzési módszert, és folytassa a kétfaktoros ellenőrzési folyamatot.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Elvesztettem a mobileszközömet, vagy ellopták

Ha elvesztette vagy ellopták a mobileszközét, más módszerrel is bejelentkezhet, vagy kérheti a szervezet ügyfélszolgálatát a beállítások törlésére. Javasoljuk, hogy tudassa a szervezet ügyfélszolgálatával, hogy elveszett vagy ellopták-e a telefonját, hogy a megfelelő frissítéseket el lehessen tenni a fiókjában. A beállítások törlése után a rendszer a következő bejelentkezéskor kéri a [kétfaktoros ellenőrzésre való regisztrációt.](multi-factor-authentication-end-user-first-time.md)

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Nem kapom meg az ellenőrző kódot a mobileszközömre

Az ellenőrző kód be szerzése gyakori probléma, és általában a mobileszközhöz és annak beállításaihoz kapcsolódik. Néhány lehetséges dolog, amit ki próbálhat:

Próbálja ki ezt | Útmutató információk
--------- | ------------
A mobileszköz újraindítása | Néha a készülékcsak frissíteni kell. Az eszköz újraindítása véget vet minden olyan háttérfolyamatnak vagy szolgáltatásnak, amely jelenleg fut, és problémákat okozhat, valamint frissíti az eszköz alapvető összetevőit, és újraindítja őket arra az esetre, ha egy bizonyos ponton összeomlanának.
A biztonsági adatok helyességének ellenőrzése | Győződjön meg arról, hogy a biztonsági ellenőrzési módszer adatai pontosak, különösen a telefonszámok. Ha rossz telefonszámot ad meg, az összes értesítés a helytelen számra fog menni. Szerencsére a felhasználó nem tud semmit sem tenni a figyelmeztetésekkel, de nem is segít a fiókba való bejelentkezésben. Az adatok helyessére vonatkozó utasításokat a [Kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben találja.
Az értesítések bekapcsolásának ellenőrzése | Győződjön meg arról, hogy a mobileszközön be vannak kapcsolva az értesítések, és hogy kiválasztott egy értesítési módszert, amely lehetővé teszi a telefonhívások, a hitelesítési alkalmazás és az üzenetküldő alkalmazás (szöveges üzenetek esetén) számára, hogy látható riasztási értesítéseket küldjön a mobileszközére.
Ellenőrizze, hogy van-e eszközjel és internetkapcsolat | Győződjön meg arról, hogy a telefonhívások és a szöveges üzenetek átjutnak a mobileszközre. Egy ismerőse hívja fel, és küldjön egy SZÖVEGES üzenetet, hogy biztosan megkapja mindkettőt. Ha nem, először ellenőrizze, hogy a mobileszköz be van-e kapcsolva. Ha az eszköz be van kapcsolva, de még mindig nem kapja meg a hívást vagy a szöveges üzenetet, akkor valószínűleg probléma van a hálózattal, és beszélnie kell a szolgáltatóval. Ha gyakran vannak jellel kapcsolatos problémái, javasoljuk, hogy telepítse és használja a [Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md) a mobileszközén. A hitelesítő alkalmazás véletlenszerű biztonsági kódokat hozhat létre a bejelentkezéshez anélkül, hogy bármilyen mobiljelet vagy internetkapcsolatot igényelne.
Kapcsolja ki a Ne zavarjanak funkciót | Győződjön meg arról, hogy nem kapcsolta be a **Ne zavarjanak** funkciót a mobileszközén. Ha ez a funkció be van kapcsolva, az értesítések nem figyelmeztethetnek a mobileszközén. A funkció kikapcsolására vonatkozó tudnivalókat a mobileszköz kézikönyvében találja.
Telefonszámok blokkolásának feloldása | Az Egyesült Államokban a Microsoft hanghívásai a következő számokból származnak: +1 (866) 539 4191, +1 (855) 330 8653 és +1 (877) 668 6536.
Az akkumulátorral kapcsolatos beállítások ellenőrzése | Ez egy kicsit furcsának tűnik a felszínen, de ha beállította az akkumulátor optimalizálását, hogy megakadályozza a kevésbé használt alkalmazások aktív vájkát a háttérben, az értesítési rendszert valószínűleg érintette. A probléma megoldásához kapcsolja ki a hitelesítési alkalmazás és az üzenetküldő alkalmazás akkumulátorának optimalizálását, majd próbáljon meg újra bejelentkezni a fiókjába.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nem kéria rendszer a második ellenőrzési adatokat

Ha a felhasználónevével és jelszavával jelentkezett be munkahelyi vagy iskolai fiókjába, de a rendszer nem kéri a további biztonsági ellenőrzési adatokat, akkor lehet, hogy még nem állította be az eszközt. A mobileszközt úgy kell beállítani, hogy az adott további biztonsági ellenőrzési módszerrel működjön. Ha meg szeretné győződni arról, hogy bekapcsolta a mobileszközt, és hogy az elérhető-e az ellenőrzési módszerrel, olvassa el [a Kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikket. Ha tudja, hogy nem állította be az eszközét vagy a fiókját, ezt most megteheti a [Fiók beállítása kétlépéses ellenőrzési](multi-factor-authentication-end-user-first-time.md) cikkben leírt lépéseket követve.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Új telefonszámot kaptam, és hozzá szeretném adni

Ha új telefonszámot kapott, frissítenie kell a biztonsági ellenőrzési módszer adatait, hogy az ellenőrző kérések a megfelelő helyre lépjenek. Az ellenőrzési módszer frissítéséhez kövesse a **Telefonszám hozzáadása vagy módosítása** című cikk Telefonszám hozzáadása vagy módosítása című cikkének [lépéseit.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Új mobileszközt kaptam, és hozzá szeretném adni

Ha új mobileszközt kapott, be kell állítania, hogy működjön a kétfaktoros ellenőrzéssel. Ez egy többlépéses megoldás:

1. Állítsa be az eszközt úgy, hogy működjön a munkahelyi vagy iskolai fiókjával a [Kétlépéses ellenőrzési](multi-factor-authentication-end-user-first-time.md) cikk Fiók beállítása című cikkben leírt lépéseket követve.

1. Frissítse a fiók- és eszközadatokat a **További biztonsági ellenőrzés** lapon, a régi eszköz törlésével és az új hozzáadásával. További információt a [Kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md) című cikkben talál.

Nem kötelező lépések:

- Töltse le, telepítse és állítsa be a Microsoft Authenticator alkalmazást mobileszközén a [Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben leírt lépésekben.

- Kapcsolja be a kétfaktoros ellenőrzést a megbízható eszközökön a [Manage your two-factor verification method settings](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) **Kétfaktoros ellenőrzés bekapcsolása című cikk megbízható eszközén című szakaszának lépéseit** követve.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Problémáim vannak a mobileszközömön való bejelentkezéskor utazás közben

Nemzetközi helyen nehezebben használhatja a mobileszközökkel kapcsolatos ellenőrzési módszereket, például a szöveges üzeneteket. Az is lehetséges, hogy a mobileszköz roamingdíjakat okozhat. Ebben az esetben azt javasoljuk, hogy használja a Microsoft Authenticator alkalmazást, és csatlakozzon egy Wi-Fi hotspothoz. A Microsoft Authenticator alkalmazás mobileszközön történő letöltéséről, telepítéséről és beállításáról [a Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben olvashat bővebben.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nem tudom beszerezni az alkalmazásjeleimet

Az alkalmazásjelszavak lecserélik a normál jelszót a régebbi asztali alkalmazásokesetében, amelyek nem támogatják a kétfaktoros ellenőrzést. Először ellenőrizze, hogy helyesen írta-e be a jelszót. Ha ez nem oldja meg, próbáljon meg új alkalmazásjelszót létrehozni az alkalmazáshoz az Alkalmazásjelszavak létrehozása és törlése című cikk Alkalmazásjelszavak [kezelése](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) című szakaszának **Lépései alapján.**

## <a name="i-cant-turn-two-factor-verification-off"></a>Nem tudom kikapcsolni a kétfaktoros ellenőrzést

Ha kétfaktoros ellenőrzést használ a munkahelyi vagy iskolai fiókjához (például), az valószínűleg azt jelenti, hogy a szervezet úgy döntött, alain@contoso.comhogy ezt a hozzáadott biztonsági funkciót kell használnia. Mivel a szervezet úgy döntött, hogy használnia kell ezt a funkciót, nincs mód arra, hogy egyenként kikapcsolja. Ha azonban kétfaktoros ellenőrzést használ egy személyes fiókkal, például alain@outlook.coma lehetőséggel, be- és kikapcsolhatja a funkciót. A személyes fiókok kétfaktoros ellenőrzésének szabályozásáról a [Kétfaktoros ellenőrzés be- és kikapcsolása a Microsoft-fiókjához.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nem találtam választ a problémámra

Ha már próbálkozott ezekkel a lépésekkel, de továbbra is problémákba ütközik, kérjen segítséget a szervezet ügyfélszolgálatátél.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [A fiókom beállítása a kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md)

- [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)
