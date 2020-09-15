---
title: Telefonos hitelesítési módszerek – Azure Active Directory
description: További információ a Azure Active Directory telefonos hitelesítési módszereinek használatáról a bejelentkezési események javításához és biztonságossá tételéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09093a5dbd2142d3542e86a99e24f228320453d4
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532659"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Hitelesítési módszerek Azure Active Directory-telefon beállításaiban

A szöveges üzenetekkel történő közvetlen hitelesítéshez beállíthatja [és engedélyezheti a felhasználók számára az SMS-alapú hitelesítést (előzetes verzió)](howto-authentication-sms-signin.md). Az SMS-alapú bejelentkezés kiválóan használható az előtérben dolgozóknak. Az SMS-alapú bejelentkezéshez a felhasználóknak nem kell tudniuk az alkalmazások és a szolgáltatások eléréséhez szükséges felhasználónevet és jelszót. A felhasználó Ehelyett beírja a regisztrált mobiltelefonszámát, egy ellenőrző kódot tartalmazó szöveges üzenetet kap, és a bejelentkezési felületen megadja azt.

A felhasználók az Azure Multi-Factor Authentication vagy az önkiszolgáló jelszó-visszaállítás (SSPR) során is ellenőrizhetik magukat mobiltelefon vagy irodai telefon formájában.

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám*formátumban kell lenniük, például: *+ 1 4251234567*.

> [!NOTE]
> Az ország/régió kódja és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a *+ 1 4251234567X12345* formátumban.

## <a name="mobile-phone-verification"></a>Mobiltelefon ellenőrzése

Az Azure Multi-Factor Authentication vagy SSPR esetében a felhasználók dönthetnek úgy, hogy szöveges üzenetet kapnak a bejelentkezési felületen megjelenő ellenőrző kóddal, vagy telefonhívást fogadnak a megadott PIN-kód megadásához.

Ha a felhasználó nem szeretné, hogy a mobil telefonszáma megjelenjen a címtárban, de azt szeretné használni a jelszó alaphelyzetbe állításához, akkor a rendszergazdák nem tölthetik fel a telefonszámot a címtárban. Ehelyett a felhasználóknak fel kell tölteniük a **hitelesítési telefonos** attribútumot a következő helyen: [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . A rendszergazdák láthatják ezeket az információkat a felhasználó profiljában, de máshol nem jelennek meg.

![Képernyőkép a Azure Portalről, amely a hitelesítési módszereket jeleníti meg egy telefonszámmal feltöltve](media/concept-authentication-methods/user-authentication-methods.png)

A Microsoft nem garantálja a konzisztens SMS-és hangalapú Azure-Multi-Factor Authentication küldését ugyanazzal a számmal. A felhasználók érdekében bármikor hozzáadhatjuk vagy eltávolíthatjuk a rövid kódokat, ahogy az útvonal-kiigazításokat az SMS-kézbesítés javítására is felhasználjuk. A Microsoft nem támogatja a rövid kódokat az országok/régiók számára a Egyesült Államok és Kanada mellett.

### <a name="text-message-verification"></a>SMS-üzenet ellenőrzése

Ha a SSPR vagy az Azure Multi-Factor Authentication során SMS-t szeretne ellenőrizni, egy SMS-t küld a rendszer az ellenőrző kódot tartalmazó mobiltelefon-telefonszámra. A bejelentkezési folyamat befejezéséhez a megadott ellenőrző kód bekerül a bejelentkezési felületre.

### <a name="phone-call-verification"></a>Telefonhívás ellenőrzése

Ha a SSPR vagy az Azure Multi-Factor Authenticationban telefonhívás-ellenőrzést végez, a felhasználó által regisztrált telefonszámra automatikus hanghívás történik. A bejelentkezési folyamat befejezéséhez a rendszer kéri a felhasználótól, hogy adja meg a PIN-kódját, majd a billentyűzetén a # értéket.

## <a name="office-phone-verification"></a>Irodai telefon ellenőrzése

Az Office Phone-attribútumot az Azure AD rendszergazdája felügyeli, és a felhasználó nem regisztrálhat.

Ha a SSPR vagy az Azure Multi-Factor Authenticationban telefonhívás-ellenőrzést végez, a felhasználó által regisztrált telefonszámra automatikus hanghívás történik. A bejelentkezési folyamat befejezéséhez a rendszer kéri a felhasználótól, hogy adja meg a PIN-kódját, majd a billentyűzetén a # értéket.

## <a name="troubleshooting-phone-options"></a>Telefonos beállítások hibaelhárítása

Ha problémák merülnek fel az Azure AD-beli telefonos hitelesítéssel kapcsolatban, tekintse át a következő hibaelhárítási lépéseket:

* Blokkolt hívóazonosító egyetlen eszközön.
   * Tekintse át az eszközön konfigurált blokkolt számokat.
* Helytelen a telefonszám vagy az ország/régió kódja, vagy a személyes telefonszám és a munkahelyi telefonszám közötti zűrzavar.
   * A felhasználói objektum és a konfigurált hitelesítési módszerek hibáinak megoldása. Győződjön meg arról, hogy a megfelelő telefonszámok regisztrálva vannak.
* Helytelen PIN-kód van megadva.
   * Erősítse meg, hogy a felhasználó a megfelelő PIN-kódot használta a fiókjához való regisztrációhoz.
* A hívás a hangpostára lett továbbítva.
   * Győződjön meg arról, hogy a felhasználó telefonja be van kapcsolva, és hogy a szolgáltatás elérhető a saját területén, vagy használjon másik módszert.
* A felhasználó blokkolva van
   * Az Azure AD-rendszergazda feloldja a felhasználót a Azure Portal.
* Az SMS nincs előfizetve az eszközön.
   * A felhasználó módosíthatja a metódusokat, vagy aktiválhatja az SMS-t az eszközön.
* Hibás távközlési szolgáltatók, például nem észlelhetők a telefonos eszközök, a DTMF-hangok hiánya, a letiltott hívóazonosító több eszközön, illetve az SMS több eszközön való letiltása.
   * A Microsoft több távközlési szolgáltatót használ a telefonhívások és SMS-üzenetek továbbítására a hitelesítéshez. Ha a fenti problémák bármelyikét látja, akkor a felhasználó öt percen belül legalább ötször megpróbálta használni a metódust, és a felhasználó információi elérhetők a Microsoft ügyfélszolgálatával való kapcsolatfelvételkor.

## <a name="next-steps"></a>Következő lépések

Első lépésként tekintse meg az önkiszolgáló [jelszó-visszaállítás (SSPR)][tutorial-sspr] és az [Azure multi-Factor Authentication][tutorial-azure-mfa]című oktatóanyagot.

További információ a SSPR fogalmakról: az [Azure ad önkiszolgáló jelszó-visszaállításának működése][concept-sspr].

További információ az MFA-fogalmakról: [how Azure multi-Factor Authentication Works][concept-mfa].

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
