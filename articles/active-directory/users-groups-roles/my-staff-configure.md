---
title: A saját munkatársak használata a felhasználói felügyelet delegálására (előzetes verzió) – Azure AD | Microsoft Docs
description: Felhasználói felügyelet delegálása a saját munkatársak és a felügyeleti egységek használatával
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 282946a023e4e79ee79b05cc2a317efc5a4056e4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165865"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Felhasználók kezelése a munkatársaival (előzetes verzió)

A saját munkatársak lehetővé teszik, hogy a delegálást egy olyan hatóságra ruházza át, mint például egy áruház kezelője vagy egy csapat vezető, az engedélyek, amelyek biztosítják, hogy a személyzet tagjai hozzáférjenek az Azure AD-fiókokhoz. A központi ügyfélszolgálatra való támaszkodás helyett a szervezetek olyan gyakori feladatokat delegálnak, mint például a jelszavak alaphelyzetbe állítása vagy a telefonszámok módosítása a Team Managerben. A munkatársakkal egy olyan felhasználó, aki nem tud hozzáférni a fiókjához, mindössze néhány kattintással visszanyerheti a hozzáférést, és nem igényel ügyfélszolgálatot vagy informatikai személyzetet.

Mielőtt a saját munkatársaival konfigurálja a munkahelyét, javasoljuk, hogy tekintse át ezt a dokumentációt, valamint a [felhasználói dokumentációt](../user-help/my-staff-team-manager.md) , hogy megismerje a funkció funkcióit és hatásait a felhasználók számára. A felhasználó dokumentációját kihasználva betaníthatja és felkészítheti a felhasználókat az új felületre, és segítséget nyújthat a sikeres bevezetéshez.

## <a name="how-my-staff-works"></a>A munkatársak működése

A munkatársak a felügyeleti egységeken (AUs) alapulnak, amelyek olyan erőforrás-tárolók, amelyekkel korlátozható a szerepkör-hozzárendelés felügyeleti vezérlő hatóköre. A saját munkatársaiban az AUs a szervezet felhasználóinak, például az áruháznak vagy a részlegnek egy részhalmazát határozza meg. Ezután például a Team Manager olyan szerepkörhöz rendelhető hozzá, amelynek hatóköre egy vagy több AUs. Az alábbi példában a felhasználó megkapta a hitelesítési rendszergazdai szerepkört, és a három au a szerepkör hatókörét képezi. További információ a felügyeleti egységekről: [felügyeleti egységek kezelése Azure Active Directoryban](directory-administrative-units.md).

## <a name="how-to-enable-my-staff"></a>Munkatársak engedélyezése

Miután konfigurálta az AUs-t, alkalmazza ezt a hatókört a munkatársaihoz hozzáférő felhasználókra. Csak a rendszergazdai szerepkörhöz rendelt felhasználók férhetnek hozzá a munkatársaihoz. A munkatársak engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portal felhasználói rendszergazdaként.
2. Tallózással keresse meg **Azure Active Directory** > **Beállítások** > **felhasználói szolgáltatás** > **előzetesek kezelése felhasználói szolgáltatás előnézetének beállításait**.
3. A **rendszergazdák hozzáférhetnek a munkatársaihoz**lehetőséggel engedélyezheti az összes felhasználó, a kiválasztott felhasználó vagy a felhasználói hozzáférés beállítást.

> [!Note]
> Csak a rendszergazdai szerepkörhöz rendelt felhasználók férhetnek hozzá a munkatársaihoz. Ha olyan felhasználó számára engedélyezi a munkatársakat, aki nem rendelkezik rendszergazdai szerepkörrel, akkor nem fog tudni hozzáférni a munkatársaihoz.

## <a name="conditional-access"></a>Feltételes hozzáférés

Az Azure AD feltételes hozzáférési szabályzatával védetté teheti a saját munkatársak portálját. Használhatja olyan feladatokhoz, mint például a többtényezős hitelesítés megkövetelése a munkatársak elérése előtt.

Határozottan javasoljuk, hogy az [Azure ad feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/conditional-access/)használatával megvédje a munkatársakat. Ha feltételes hozzáférési szabályzatot szeretne alkalmazni a munkatársaim számára, manuálisan kell létrehoznia a saját munkatársak szolgáltatását a PowerShell használatával.

### <a name="apply-a-----conditional-access-policy-to-my-staff"></a>Feltételes hozzáférési szabályzat alkalmazása a saját munkatársakra

1. Telepítse a [Microsoft Graph Beta PowerShell-parancsmagokat](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1).
1. Futtassa az alábbi parancsot:

        Connect-Graph -Scopes "Directory.AccessAsUser.All"
        New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"

1. Hozzon létre egy feltételes hozzáférési szabályzatot, amely a saját személyzet Felhőbeli alkalmazására vonatkozik.

    ![Feltételes hozzáférési szabályzat létrehozása a saját munkatársak alkalmazáshoz](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Saját munkatársak használata

Amikor egy felhasználó a saját munkatársaihoz jut, megjelennek azoknak a [felügyeleti egységeknek](directory-administrative-units.md) a neve, amelyeken rendszergazdai jogosultságokkal rendelkeznek. A [saját munkatársak felhasználói dokumentációjában](../user-help/my-staff-team-manager.md)a "location" kifejezést használjuk a felügyeleti egységekre való hivatkozáshoz. Ha egy rendszergazda engedélyei nem rendelkeznek AU-hatókörrel, az engedélyek a szervezeten belül érvényesek. A munkatársaim engedélyezése után az engedélyezett és a hozzájuk rendelt felhasználók hozzáférhetnek a szolgáltatáshoz [https://mystaff.microsoft.com](https://mystaff.microsoft.com). Kiválaszthatnak egy AU-t az adott AU-ban lévő felhasználók megtekintéséhez, és kiválaszthatnak egy felhasználót a profil megnyitásához.

## <a name="licenses"></a>Licencek

A munkatársaink által engedélyezett összes felhasználónak licenccel kell rendelkeznie, még akkor is, ha nem használják a saját munkatársak portált. Minden engedélyezett felhasználónak rendelkeznie kell a következő Azure AD-vagy Microsoft 365-licencek valamelyikével:

- prémium szintű Azure AD P1 vagy P2
- Microsoft 365 F1 vagy F3

## <a name="reset-a-users-password"></a>Új felhasználói jelszó létrehozása

A következő szerepkörök jogosultak a felhasználó jelszavának alaphelyzetbe állítására:

- [Hitelesítés rendszergazdája](directory-assign-admin-roles.md#authentication-administrator)
- [Kiemelt jogosultságú hitelesítés rendszergazdája](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globális rendszergazda](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Segélyszolgálat rendszergazdája](directory-assign-admin-roles.md#helpdesk-administrator)
- [Felhasználói rendszergazda](directory-assign-admin-roles.md#user-administrator)
- [Jelszókezelő](directory-assign-admin-roles.md#password-administrator)

A **saját munkatársak**területen nyisson meg egy felhasználói profilt. Válassza a **jelszó alaphelyzetbe állítása**lehetőséget.

- Ha a felhasználó csak felhőalapú, megtekintheti a felhasználónak megadható ideiglenes jelszót.
- Ha a felhasználó helyi Active Directoryról van szinkronizálva, megadhat egy jelszót, amely megfelel a helyszíni AD-házirendeknek. Ezután megadhatja ezt a jelszót a felhasználónak.

    ![Jelszó-visszaállítási folyamatjelző és sikeres értesítés](media/my-staff-configure/reset-password.png)

A felhasználónak a következő bejelentkezéskor meg kell változtatnia a jelszavát.

## <a name="manage-a-phone-number"></a>Telefonszám kezelése

A **saját munkatársak**területen nyisson meg egy felhasználói profilt.

- Válassza a **telefonszám hozzáadása** szakaszt a felhasználó telefonszámának hozzáadásához
- Válassza a telefonszám **szerkesztése** lehetőséget a telefonszám módosításához.
- Válassza a **telefonszám eltávolítása** lehetőséget a felhasználó telefonszámának eltávolításához.

A beállításoktól függően a felhasználó használhatja az SMS-be való bejelentkezéshez beállított telefonszámot, a többtényezős hitelesítés végrehajtását, valamint az önkiszolgáló jelszó-visszaállítást.

A felhasználó telefonszámának kezeléséhez a következő szerepkörök egyikét kell hozzárendelni:

- [Hitelesítés rendszergazdája](directory-assign-admin-roles.md#authentication-administrator)
- [Kiemelt jogosultságú hitelesítés rendszergazdája](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globális rendszergazda](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Keresés

A saját munkatársaiban található keresősáv használatával kereshet a szervezeten belüli AUs és felhasználók számára. Kereshet a szervezet összes au-beli és felhasználóján, de csak olyan felhasználók módosíthatók, akik olyan AU-ban vannak, amelyeken rendszergazdai jogosultságokat kapott.

Egy AU-n belül is kereshet egy felhasználót. Ehhez használja a felhasználói lista tetején található keresési sávot.

## <a name="audit-logs"></a>Naplók

A naplókat megtekintheti a munkatársaim által a Azure Active Directory portálon végrehajtott műveletekhez. Ha a naplót egy, a munkatársain végrehajtott művelet hozta létre, akkor ez a naplózási esemény további részletei között jelenik meg.

## <a name="next-steps"></a>További lépések

[A munkatársak felhasználói dokumentációjának](../user-help/my-staff-team-manager.md)
[felügyeleti egységeinek dokumentációja](directory-administrative-units.md)
