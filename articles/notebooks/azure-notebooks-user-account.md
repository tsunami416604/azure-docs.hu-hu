---
title: Bejelentkezés Azure Notebooks előzetes verzióra
description: A Azure Notebooks felhasználói fiókjának konfigurálása Microsoft-fiók vagy munkahelyi/iskolai fiók használatával.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646296"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Azure Notebooks előzetes verziójának felhasználói fiókja

A Azure Notebooks a felhasználói fiókkal való bejelentkezéssel vagy anélkül is használhatja:

- Bejelentkezés nélkül létrehozhat és futtathat jegyzetfüzeteket, de nem tudja megőrizni a jegyzetfüzeteket és az adatfájlokat a projektek részeként. Azok a felhasználók, akik egy Azure-jegyzetfüzetre mutató hivatkozást kapnak, így nem kell bejelentkezniük a jegyzetfüzetbe.
- Ha bejelentkezett, Azure Notebooks megőrzi az összes projektet a fiókjával. A bejelentkezett felhasználóknak olyan felhasználói AZONOSÍTÓjuk is van, amely lehetővé teszi, hogy másokkal is megosszák a projektjeiket és a jegyzetfüzeteket.
- Ha a Azure Notebookshez használt fiók egy Azure-előfizetéshez is társítva van, akkor további előnyökkel jár, mint például a jegyzetfüzetek futtatása nagyobb teljesítményű kiszolgálókon, privát jegyzetfüzetek létrehozása és engedélyek engedélyezése a jegyzetfüzetekhez az egyes felhasználók számára.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Az Azure Notebooksba való bejelentkezéshez Microsoft-fiókra vagy munkahelyi vagy iskolai fiókra van szükség. A rendszer a jegyzetfüzetek oldal jobb felső sarkában található **Bejelentkezés** parancs kiválasztásakor kéri a fiókját:

![Bejelentkezési parancs a Azure Notebooks](media/accounts/sign-in-command.png)

A Azure Notebooksben végzett összes művelet társítva van a bejelentkezéshez használt fiókkal. Minden fióknak egyedi felhasználói AZONOSÍTÓval kell rendelkeznie a [felhasználói profilban](azure-notebooks-user-profile.md). Ennek eredményeképpen különböző fiókokkal is bejelentkezhet Azure Notebooksba, ha különálló projekteket és különálló identitásokat kell fenntartania. Előfordulhat például, hogy egy adatelemzési csapat minden tagja külön fiókkal rendelkezik, mint egy megosztott csoportfiók, amelyet a vállalaton kívüli személyek számára a munkájuk bemutatására használnak. Az oktatók hasonlóan, a külső tanácsadásban és a nyílt forráskódú munkában használt fióktól eltérő oktatási szerepkörhöz is tarthatnak egy fiókot.

## <a name="microsoft-accounts"></a>Microsoft-fiókok

A Microsoft-fiókok segítségével tetszőleges számú Microsoft-termék és-szolgáltatásba jelentkezhet be, például a Windows, az Azure, a outlook.com, a OneDrive és az XBox Live rendszerbe. Ha bármelyik szolgáltatást használja, akkor valószínű, hogy már rendelkezik egy Microsoft-fiókkal, amelyet a Azure Notebooks használhat.

Ha nem biztos abban, hogy a fiók **létrehozása** parancsra kattint, válassza a létrehozás lehetőséget. Létrehozhat egy új Microsoft-fiók bármely szolgáltatótól származó e-mail-cím használatával.

![Új Microsoft-fiók létrehozására szolgáló parancs](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Ha olyan e-mail-címmel próbál meg létrehozni egy új fiókot, amelyhez már tartozik fiók társítva, akkor a következő üzenet jelenhet meg: "nem lehet regisztrálni egy munkahelyi vagy iskolai e-mail-címmel. Használjon személyes e-maileket, például a Gmailt vagy a Yahoo!-t, vagy szerezzen be egy új Outlook e-mail címet. " Ebben az esetben próbáljon meg bejelentkezni a munkahelyi e-mail-címmel új fiók létrehozása nélkül.

A alárendelt fiókok esetében a Azure Notebooks alapértelmezés szerint le van tiltva. Az alárendelt fiókkal való bejelentkezéskor az alábbi hibaüzenet jelenik meg:

![Hiba történt egy alárendelt fiókkal való bejelentkezéskor: hiba történt, a szülő letiltotta a hozzáférést](media/accounts/child-account-error.png)

A hozzáférés engedélyezéséhez a szülőnek a következő lépéseket kell elvégeznie:

1. Látogasson el `https://account.live.com/mk` egy fölérendelt fiókkal, és jelentkezzen be.
1. A kérdéses gyermek szakaszban válassza a **gyermek hozzáférésének kezelése harmadik féltől származó alkalmazásokhoz**lehetőséget.
1. A következő lapon válassza a **hozzáférés engedélyezése**lehetőséget.
1. Ha a alárendelt fiók a Azure Notebooksba való bejelentkezéshez használatos, válassza az **Igen** lehetőséget a megjelenő engedélyek megadásával.

> [!Warning]
> A harmadik féltől származó alkalmazásokhoz való hozzáférés engedélyezése Azure Notebooks is lehetővé teszi a hozzáférést minden más külső alkalmazáshoz. A szülők számára javasolt a hozzáférés engedélyezése, és a gyermek tevékenységének alaposabb figyelése.

## <a name="work-or-school-accounts"></a>Munkahelyi vagy iskolai fiókok

A szervezet rendszergazdája létrehoz egy munkahelyi vagy iskolai fiókot, amely lehetővé teszi, hogy a szervezet egy tagja hozzáférhessen a Microsoft Cloud Services, például az Office 365, valamint egy olyan fiókhoz, amellyel a Windowsba bejelentkezhetnek a tartományhoz csatlakoztatott számítógépeken. A munkahelyi vagy iskolai fiók jellemzően szervezeti e-mail-címet használ, any-user@contoso.compéldául:.

A munkahelyi vagy iskolai fiókkal való Azure Notebooks bejelentkezéshez rendszergazdai jogosultságra lehet szükség, mert a Azure Notebooks gyűjti vagy használja (de nem fedi le) az adatokat, például a fiók e-mail-címét és a felhasználó böngésző-információit. (A böngésző adatait a funkciók népszerű használat szerint történő optimalizálására használják.)

A szervezeti fiók rendszergazdájának a felhasználók nevében kell megadnia a beleegyezést, ha a felhasználók nem rendelkeznek külön hozzájárulással. Ebben az esetben a felhasználók "nem férhetnek hozzá ehhez az alkalmazáshoz" üzenet jelenik meg:

!["Az alkalmazás nem érhető el" üzenet munkahelyi vagy iskolai fiók használata esetén](media/accounts/consent-permissions-denied.png)

Ha rendszergazdai jogosultságot szeretne biztosítani, használja a [rendszergazdai jogosultságok lapot](https://notebooks.azure.com/account/adminConsent), amely végigvezeti a folyamaton.

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [A profil és a felhasználói azonosító szerkesztése](azure-notebooks-user-profile.md)
