---
title: Bejelentkezés az Azure Notebookelőzetes verzióba
description: Konfigurálja az Azure Notebookok felhasználói fiókját Egy Microsoft-fiók vagy egy munkahelyi/iskolai fiók használatával.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646296"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Az Azure Notebooks előzetes verziójának felhasználói fiókja

Az Azure Notebookokat felhasználói fiókkal vagy anélkül is használhatja:

- Bejelentkezés nélkül létrehozhat és futtathat jegyzetfüzeteket, de nem őrizheti meg a jegyzetfüzeteket és az adatfájlokat a projektek részeként. Azok a felhasználók, akik például egy Azure-jegyzetfüzetre mutató hivatkozást kapnak, anélkül élvezhetik a jegyzetfüzetet, hogy be kellene jelentkeznie.
- Amikor bejelentkezett, az Azure Notebooks megőrzi az összes projektet a fiókjával. A bejelentkezett felhasználók felhasználói azonosítóval is rendelkeznek, amely lehetővé teszi számukra, hogy megosszák projektjeiket és jegyzetfüzeteiket másokkal.
- Ha az Azure-jegyzetfüzetekhez használt fiók is azure-előfizetéshez van társítva, további előnyökre tesz szert, például jegyzetfüzeteket futtathat a hatékonyabb kiszolgálókon, privát jegyzetfüzeteket hozhat létre, és engedélyeket adhat a jegyzetfüzeteknek az egyes felhasználóknak.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Az Azure Notebooks ba való bejelentkezéshez Microsoft-fiókra vagy "Munkahelyi vagy iskola" fiókra van szükség. A rendszer a Jegyzetfüzet ek lap jobb felső részén található **Bejelentkezés** parancs kiválasztásával kéri a fiókját:

![Bejelentkezési parancs az Azure-jegyzetfüzetekhez](media/accounts/sign-in-command.png)

Az Azure Notebooks-ban végzett összes munka a bejelentkezéshez használt fiókhoz van társítva. Minden fióknak egyedi felhasználói azonosítóval kell rendelkeznie a [felhasználói profilban.](azure-notebooks-user-profile.md) Ennek eredményeképpen különböző fiókokkal jelentkezhet be az Azure Notebooks-ba, ha külön projekteket és különálló identitásokat kell fenntartania. Egy adatelemzési csoport minden tagja például rendelkezhet mindkét egyéni fiókkal egy megosztott csoportfiókként, amelyet a vállalaton kívüli személyeknek való bemutatásra használnak. Az oktatók hasonlóképpen fenntarthatnak egy olyan fiókot a tanítási szerepükhöz, amely eltér a külső tanácsadásban vagy nyílt forráskódú munkában használt fióktól.

## <a name="microsoft-accounts"></a>Microsoft-fiókok

A Microsoft-fiókok tetszőleges számú Microsoft-termékbe és -szolgáltatásba jelentkeznek be, például a Windowsba, az Azure-ba, a outlook.com, a OneDrive-ba és az XBox Live-ba. Ha ezek közül bármelyik szolgáltatást használja, valószínű, hogy már rendelkezik egy Microsoft-fiókkal, amely et az Azure Notebooks használatával is használhat.

Ha nem biztos benne, válassza az **Egy létrehozása** parancsot a fióksorban. Új Microsoft-fiókot bármely szolgáltató tól kapott e-mail címmel hozhat létre.

![Parancs új Microsoft-fiók létrehozásához](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Ha olyan e-mail címmel próbál új fiókot létrehozni, amelyhez már tartozik fiók, a következő üzenet jelenhet meg: "Itt nem regisztrálhat munkahelyi vagy iskolai e-mail címmel. Használjon személyes e-mailt, például a Gmailt vagy a Yahoo!-t, vagy szerezzen be egy új Outlook-e-mailt." Ebben az esetben próbáljon meg új fiók létrehozása nélkül bejelentkezni a munkahelyi e-mail címmel.

Gyermekfiókok esetén az Azure Notebookok eléréséhez alapértelmezés szerint le van tiltva. A gyermekfiókkal való bejelentkezés az alábbi hibaüzenetet jeleníti meg:

![Hiba hiba gyermekfiókkal történő bejelentkezéskor: valami elromlott, a szülő letiltotta a hozzáférést](media/accounts/child-account-error.png)

A hozzáférés engedélyezéséhez a szülőnek a következő lépéseket kell tennie:

1. Látogasson `https://account.live.com/mk` el és jelentkezzen be szülőfiókkal.
1. A szóban forgó gyermekre vonatkozó szakaszban válassza **a Gyermek hozzáférésének kezelése harmadik féltől származó alkalmazásokhoz lehetőséget.**
1. A következő lapon válassza a **Hozzáférés engedélyezése**lehetőséget.
1. Ha a gyermekfiók következő bejelentkezéshez az Azure Notebooks, válassza az **Igen** az engedélyek et, amely megjelenik.

> [!Warning]
> Az Azure Notebookok külső alkalmazásokhoz való hozzáférés engedélyezése az összes többi külső alkalmazás számára is lehetővé teszi a hozzáférést. A szülőknek azt tanácsoljuk, hogy a hozzáférés engedélyezésekor körültekintően járjanak el, és jobban figyelemmel kísérhetik gyermekük tevékenységét.

## <a name="work-or-school-accounts"></a>Munkahelyi vagy iskolai fiókok

A munkahelyi vagy iskolai fiókot a szervezet rendszergazdája hozta létre, hogy a szervezet egy tagja hozzáférhessen a Microsoft felhőszolgáltatásaihoz, például az Office 365-höz, valamint fiókként a Windows rendszerbe való bejelentkezéshez egy tartományhoz csatlakozó számítógépen. A munkahelyi vagy iskolai fiók általában szervezeti e-mail címet használ, például any-user@contoso.com.

Az Azure Notebookok ba munkahelyi vagy iskolai fiókkal való bejelentkezéshez rendszergazdai hozzájárulás ra lehet szükség, mert az Azure Notebookok olyan adatokat gyűjtenek (de nem adnak ki), mint például a fiók e-mail címe és a felhasználó böngészőadatai. (A böngésző adatokat a funkciók népszerű használat szerinti optimalizálására használjuk.)

A szervezeti fiók rendszergazdájának a felhasználók nevében beleegyezést kell adnia, ha a felhasználók egyénileg nem járulhatnak hozzá. Ebben az esetben a felhasználók a következő üzenetjelenik meg: "Nem férhet hozzá az alkalmazáshoz":

!["Nem fér hozzá az alkalmazáshoz" üzenet munkahelyi vagy iskolai fiók használata esetén](media/accounts/consent-permissions-denied.png)

Rendszergazdai hozzájárulás megadásához használja a [rendszergazdai hozzájárulási lapot,](https://notebooks.azure.com/account/adminConsent)amely végigvezeti a folyamaton.

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [Profil és felhasználói azonosító szerkesztése](azure-notebooks-user-profile.md)
