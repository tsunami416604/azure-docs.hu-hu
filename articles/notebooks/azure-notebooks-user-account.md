---
title: Jelentkezzen be az Azure notebookok
description: Konfigurálja a felhasználói fiók Azure notebookokhoz Microsoft-fiókkal vagy egy munkahelyi vagy iskolai fiókot.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: a2d8c8180dfb5dc31e273c7953a57f40cf31238d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277627"
---
# <a name="your-user-account-for-azure-notebooks"></a>A felhasználói fiók Azure-notebookokhoz

Az Azure-jegyzetfüzeteket is használhatja, vagy egy felhasználói fiókkal történő bejelentkezés nélkül:

- Bejelentkezés nélkül is létrehozhat és futtathat jegyzetfüzeteket, de nem ürítheti a jegyzetfüzet vagy adatfájlok projektek részeként. Felhasználók, akik az Azure-jegyzetfüzetnek hivatkozást kap például élvezheti a notebook anélkül, hogy jelentkezzen be.
- Miután bejelentkezett, Azure notebookok megőrzi a fiókjához a projektek. Bejelentkezett felhasználók is egy felhasználói Azonosítót, amely lehetővé teszi, hogy a projektek és jegyzetfüzetek oszthatja meg másokkal.
  - Azure notebookokhoz használt fiókot is az Azure-előfizetéssel társítva, révén a további előnyökkel, például a hatékonyabb kiszolgálókon, jegyzetfüzetek futtatása személyes jegyzetfüzet létrehozása és ad engedélyt a notebookok egyéni felhasználók számára.

Notebookok Azure-ba történő bejelentkezéskor kell a Microsoft Account vagy egy "Munkahelyi vagy iskolai" fiók. A rendszer a jegyzetfüzetek oldal jobb felső sarkában található **Bejelentkezés** parancs kiválasztásakor kéri a fiókját:

![Jelentkezzen be a parancs Azure notebookokhoz](media/accounts/sign-in-command.png)

Az Azure-jegyzetfüzetek teheti meg az összes munkahelyi társítva a bejelentkezéshez használt fióknak. Minden fióknak egyedi felhasználói AZONOSÍTÓval kell rendelkeznie a [felhasználói profilban](azure-notebooks-user-profile.md). Ennek eredményeképpen jelentkezhet be Azure-jegyzetfüzetek különböző fiókokkal Ha külön csoportok projektek és különálló biztosítani kell a. Egy data science csapat minden tagjának például előfordulhat mellett egyéni fiókot is használnak munkahelyi jelenleg a vállalat kívüli személyeknek megosztott csoport fiókként. Oktatók, hasonlóan egy olyan fiókkal, amely eltér a külső helyét, vagy a nyílt forráskódú használt fiók oktatási szerepkörük előfordulhat, hogy fenn.

## <a name="microsoft-accounts"></a>A Microsoft-fiókok

Jelentkezzen be Microsoft-termékek és szolgáltatások, például Windows, Azure, outlook.com, onedrive vállalati verzió és az XBox Live tetszőleges számú Microsoft-fiókok segítségével. Ha használja ezeket a szolgáltatásokat, valószínű, hogy már rendelkezik egy Microsoft Account, amely az Azure-jegyzetfüzeteket is használhatja.

Ha nem biztos abban, hogy a fiók **létrehozása** parancsra kattint, válassza a létrehozás lehetőséget. Létrehozhat egy új Microsoft-fiók összes e-mail-címmel a bármely szolgáltatónál.

![A parancs egy új Microsoft-fiók létrehozása](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Ha olyan e-mail-címmel próbál meg létrehozni egy új fiókot, amelyhez már tartozik fiók társítva, akkor a következő üzenet jelenhet meg: "nem lehet regisztrálni egy munkahelyi vagy iskolai e-mail-címmel. Használjon személyes e-maileket, például a Gmailt vagy a Yahoo!-t, vagy szerezzen be egy új Outlook e-mail címet. " Ebben az esetben próbáljon meg bejelentkezni a munkahelyi e-mail-címmel új fiók létrehozása nélkül.

Gyermek-fiókok esetében a hozzáférést az Azure-jegyzetfüzetek alapértelmezés szerint le van tiltva. A gyermek fiókkal történő bejelentkezés alább látható hibaüzenetet jeleníti meg:

![Hiba történt, amikor gyermek fiókkal próbál: Hiba történt, szülője letiltotta a hozzáférést](media/accounts/child-account-error.png)

Hozzáférés engedélyezése egy szülő a következő lépéseket kell tennie:

1. Látogasson el `https://account.live.com/mk`re, és jelentkezzen be egy fölérendelt fiókkal.
1. A kérdéses gyermek szakaszban válassza a **gyermek hozzáférésének kezelése harmadik féltől származó alkalmazásokhoz**lehetőséget.
1. A következő lapon válassza a **hozzáférés engedélyezése**lehetőséget.
1. Ha a alárendelt fiók a Azure Notebooksba való bejelentkezéshez használatos, válassza az **Igen** lehetőséget a megjelenő engedélyek megadásával.

> [!Warning]
> Harmadik felek alkalmazásaihoz való hozzáférés engedélyezése az Azure-jegyzetfüzeteket is lehetővé teszi, hogy minden más külső alkalmazás hozzáférését. Szülők javasolja, hogy saját belátása szerint értékeli használja, amikor engedélyezi az eléréséhez, és előfordulhat, hogy azok gyermektevékenységet jobban figyelni szeretné.

## <a name="work-or-school-accounts"></a>Munkahelyi vagy iskolai fiókok

Munkahelyi vagy iskolai fiók engedélyezése a Microsoft felhőszolgáltatásai, például az Office 365, valamint olyan fiókkal jelentkezzen be Windows-tartományhoz csatlakoztatott számítógépen való eléréséhez a szervezet tagja a szervezet rendszergazdája által jön létre. A munkahelyi vagy iskolai fiók jellemzően szervezeti e-mail-címet használ, például any-user@contoso.com.

Azure notebookok bejelentkezik egy munkahelyi vagy iskolai fiókkal lehet szükség rendszergazdai jóváhagyást, mert Azure notebookok gyűjti, vagy használja (de nem hozza nyilvánosságra) például a fiókhoz tartozó e-mail cím és a felhasználó böngészőben információkat. (Böngészési adatok segítségével optimalizálhatják a funkciókat népszerű használat alapján.)

Szervezeti fiók rendszergazdája kell megadnia a jóváhagyás a felhasználó nevében, ha a felhasználók korlátozva vannak a küldőnek külön-külön. Ebben az esetben felhasználók az "Ez az alkalmazás nem tud hozzáférni" üzenet jelenik meg:

!["Az alkalmazás nem tud hozzáférni" jelenik meg, amikor egy munkahelyi vagy iskolai fiókkal](media/accounts/consent-permissions-denied.png)

Ha rendszergazdai jogosultságot szeretne biztosítani, használja a [rendszergazdai jogosultságok lapot](https://notebooks.azure.com/account/adminConsent), amely végigvezeti a folyamaton.

## <a name="next-steps"></a>Következő lépések  

> [!div class="nextstepaction"]
> [A profil és a felhasználói azonosító szerkesztése](azure-notebooks-user-profile.md)
