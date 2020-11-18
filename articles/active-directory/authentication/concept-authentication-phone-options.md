---
title: Telefonos hitelesítési módszerek – Azure Active Directory
description: További információ a Azure Active Directory telefonos hitelesítési módszereinek használatáról a bejelentkezési események javításához és biztonságossá tételéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39555c5b396e8a8fd3449331cd2fd68b96ad2087
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839998"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Hitelesítési módszerek Azure Active Directory-telefon beállításaiban

A szöveges üzenetekkel történő közvetlen hitelesítéshez beállíthatja [és engedélyezheti a felhasználók számára az SMS-alapú hitelesítést (előzetes verzió)](howto-authentication-sms-signin.md). Az SMS-alapú bejelentkezés kiválóan használható az előtérben dolgozóknak. Az SMS-alapú bejelentkezéshez a felhasználóknak nem kell tudniuk az alkalmazások és a szolgáltatások eléréséhez szükséges felhasználónevet és jelszót. A felhasználó Ehelyett beírja a regisztrált mobiltelefonszámát, egy ellenőrző kódot tartalmazó szöveges üzenetet kap, és a bejelentkezési felületen megadja azt.

A felhasználók az Azure AD Multi-Factor Authentication vagy az önkiszolgáló jelszó-visszaállítás (SSPR) során is ellenőrizhetik magukat mobil telefon vagy irodai telefon formájában.

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám* formátumban kell lenniük, például: *+ 1 4251234567*.

> [!NOTE]
> Az ország/régió kódja és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a *+ 1 4251234567X12345* formátumban.

## <a name="mobile-phone-verification"></a>Mobiltelefon ellenőrzése

Az Azure AD Multi-Factor Authentication vagy SSPR esetében a felhasználók dönthetnek úgy, hogy szöveges üzenetet kapnak a bejelentkezési felületen beírni kívánt ellenőrző kóddal, vagy telefonhívást fogadnak.

Ha a felhasználó nem szeretné, hogy a mobil telefonszáma megjelenjen a címtárban, de azt szeretné használni a jelszó alaphelyzetbe állításához, akkor a rendszergazdák nem tölthetik fel a telefonszámot a címtárban. Ehelyett a felhasználóknak fel kell tölteniük a **hitelesítési telefonos** attribútumot a következő helyen: [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . A rendszergazdák láthatják ezeket az információkat a felhasználó profiljában, de máshol nem jelennek meg.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Képernyőkép a Azure Portalről, amely a hitelesítési módszereket jeleníti meg egy telefonszámmal feltöltve":::

A Microsoft nem garantálja a konzisztens SMS-és hangalapú Azure AD-Multi-Factor Authentication küldését ugyanazzal a számmal. A felhasználók érdekében bármikor hozzáadhatjuk vagy eltávolíthatjuk a rövid kódokat, ahogy az útvonal-kiigazításokat az SMS-kézbesítés javítására is felhasználjuk. A Microsoft nem támogatja a rövid kódokat az országok/régiók számára a Egyesült Államok és Kanada mellett.

### <a name="text-message-verification"></a>SMS-üzenet ellenőrzése

A SSPR vagy az Azure AD Multi-Factor Authentication során a szöveges üzenetek ellenőrzésekor a rendszer SMS-t küld az ellenőrző kódot tartalmazó mobil telefonszámra. A bejelentkezési folyamat befejezéséhez a megadott ellenőrző kód bekerül a bejelentkezési felületre.

### <a name="phone-call-verification"></a>Telefonhívás ellenőrzése

Ha a SSPR vagy az Azure AD Multi-Factor Authentication során telefonhívás-ellenőrzést végez, a felhasználó által regisztrált telefonszámra automatikusan hanghívás történik. A bejelentkezési folyamat befejezéséhez a rendszer felszólítja a felhasználót, hogy nyomja meg a # gombot a billentyűzetén.

## <a name="office-phone-verification"></a>Irodai telefon ellenőrzése

Ha a SSPR vagy az Azure AD Multi-Factor Authentication során telefonhívás-ellenőrzést végez, a felhasználó által regisztrált telefonszámra automatikusan hanghívás történik. A bejelentkezési folyamat befejezéséhez a rendszer felszólítja a felhasználót, hogy nyomja meg a # gombot a billentyűzetén.

## <a name="troubleshooting-phone-options"></a>Telefonos beállítások hibaelhárítása

Ha problémák merülnek fel az Azure AD-beli telefonos hitelesítéssel kapcsolatban, tekintse át a következő hibaelhárítási lépéseket:

* "Az ellenőrző hívások korlátja" vagy "a szöveges ellenőrző kódok korlátozása" hibaüzenet jelenik meg a bejelentkezéskor
   * A hitelesítés befejezéséhez használja a Micrsoft-hitelesítő alkalmazást vagy az ellenőrző kódot, vagy próbálkozzon újra később.
* Blokkolt hívóazonosító egyetlen eszközön.
   * Tekintse át az eszközön konfigurált blokkolt számokat.
* Helytelen a telefonszám vagy az ország/régió kódja, vagy a személyes telefonszám és a munkahelyi telefonszám közötti zűrzavar.
   * A felhasználói objektum és a konfigurált hitelesítési módszerek hibáinak megoldása. Győződjön meg arról, hogy a megfelelő telefonszámok regisztrálva vannak.
* Helytelen PIN-kód van megadva.
   * Erősítse meg, hogy a felhasználó a megfelelő PIN-kódot használta a fiókjához (csak MFA-kiszolgáló felhasználóinak).
* A hívás a hangpostára lett továbbítva.
   * Győződjön meg arról, hogy a felhasználó telefonja be van kapcsolva, és hogy a szolgáltatás elérhető a saját területén, vagy használjon másik módszert.
* A felhasználó blokkolva van
   * Az Azure AD-rendszergazda feloldja a felhasználót a Azure Portal.
* Az SMS nincs előfizetve az eszközön.
   * A felhasználó módosíthatja a metódusokat, vagy aktiválhatja az SMS-t az eszközön.
* Hibás távközlési szolgáltatók, például nem észlelhetők a telefonos eszközök, a DTMF-hangok hiánya, a letiltott hívóazonosító több eszközön, illetve az SMS több eszközön való letiltása.
   * A Microsoft több távközlési szolgáltatót használ a telefonhívások és SMS-üzenetek továbbítására a hitelesítéshez. Ha a fenti problémák bármelyikét látja, akkor a felhasználó öt percen belül legalább ötször megpróbálta használni a metódust, és a felhasználó információi elérhetők a Microsoft ügyfélszolgálatával való kapcsolatfelvételkor.

## <a name="next-steps"></a>Következő lépések

Az első lépésekhez tekintse meg az [önkiszolgáló jelszó-visszaállítás (SSPR)][tutorial-sspr] és az [Azure ad multi-Factor Authentication][tutorial-azure-mfa]oktatóanyagát.

További információ a SSPR fogalmakról: az [Azure ad önkiszolgáló jelszó-visszaállításának működése][concept-sspr].

További információ az MFA-fogalmakról: [Hogyan működik az Azure AD multi-Factor Authentication][concept-mfa].

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)használatával.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
