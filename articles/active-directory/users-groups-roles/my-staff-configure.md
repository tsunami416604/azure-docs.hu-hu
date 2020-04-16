---
title: Felhasználói kezelés delegálása (előzetes verzió) – Azure AD | Microsoft dokumentumok
description: Felhasználókezelés delegálása a Saját személyzet és a felügyeleti egységek használatával
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394214"
---
# <a name="manage-your-users-with-my-staff-preview"></a>A felhasználók kezelése a Saját személyzettel (előzetes verzió)

A saját személyzet lehetővé teszi, hogy delegálja a hatóság, például egy üzletvezető vagy egy csoport vezető, az engedélyeket annak biztosítására, hogy a személyzet tagjai hozzáférhetnek az Azure AD-fiókok. Ahelyett, hogy egy központi ügyfélszolgálatra támaszkodna, a szervezetek delegálhatják a gyakori feladatokat, például a jelszavak alaphelyzetbe állítását vagy a telefonszámok módosítása a csapatkezelőre. A Saját személyzettel azok a felhasználók, akik nem férnek hozzá a fiókjukhoz, néhány kattintással visszaszerezhetik a hozzáférést, és nincs szükség ügyfélszolgálatra vagy informatikai személyzetre.

Mielőtt konfigurálná a Saját személyzetet a szervezetéhez, javasoljuk, hogy tekintse át ezt a dokumentációt, valamint a [felhasználói dokumentációt,](../user-help/my-staff-team-manager.md) hogy biztosan tisztában legyen a szolgáltatás funkcióival és a felhasználókra gyakorolt hatásával. A felhasználói dokumentáció segítségével betaníthatja és felkészítheti a felhasználókat az új élményre, és elősegítheti a sikeres bevezetést.

## <a name="how-my-staff-works"></a>Hogyan működik a munkatársaim?

A saját személyzet felügyeleti egységeken alapul, amelyek olyan erőforrások tárolói, amelyek a szerepkör-hozzárendelés felügyeleti ellenőrzésének hatókörének korlátozására használhatók. A Saját személyzetben az Egyesült államokbeli szervezet felhasználóinak egy részhalmazát, például egy üzletet vagy részleget határoz meg. Ezután például egy csapatvezető hozzárendelhető egy olyan szerepkörhöz, amelynek hatóköre egy vagy több AUs. Az alábbi példában a felhasználó megkapta a hitelesítési felügyeleti szerepkört, és a három AUs a szerepkör hatóköre. A felügyeleti egységekről további információt az Azure Active Directory felügyeleti egységeinek kezelése című [témakörben talál.](directory-administrative-units.md)

## <a name="how-to-enable-my-staff"></a>Hogyan engedélyezze a Saját személyzet

Miután konfigurálta az Atus-t, alkalmazhatja ezt a hatókört azokra a felhasználókra, akik hozzáférnek a Saját személyzethez. Csak a felügyeleti szerepkörhöz rendelt felhasználók férhetnek hozzá a Saját személyzethez. A Saját munkatársak engedélyezéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon felhasználói rendszergazdaként.
2. Tallózással keresse meg az **Azure Active Directory** > **felhasználói beállításait:** > **A felhasználói funkciók előnézetének** > **megtekintése A felhasználói funkciók előnézeti beállításainak kezelése**.
3. A **Rendszergazdák csoportban**engedélyezheti az összes felhasználó, a kijelölt felhasználók számára, vagy a felhasználói hozzáférés nélkül.

> [!Note]
> Csak azok a felhasználók férhetnek hozzá, akik rendszergazdai szerepkört kaptak, férhetnek hozzá a Saját munkatársak hoz. Ha engedélyezi a Saját személyzet et egy olyan felhasználó számára, aki nem rendelkezik rendszergazdai szerepkörrel, akkor nem fogja tudni elérni a Saját munkatársakat.

## <a name="using-my-staff"></a>A munkatársaim használata

Amikor egy felhasználó a Saját személyzet hez kerül, azoknak a [felügyeleti egységeknek](directory-administrative-units.md) a neve jelenik meg, amelyek felett rendszergazdai engedélyekkel rendelkeznek. A [Saját személyzet felhasználói dokumentációjában](../user-help/my-staff-team-manager.md)a "hely" kifejezést használjuk az adminisztratív egységekre való hivatkozáshoz. Ha a rendszergazda engedélyei nem rendelkeznek AU hatókörrel, az engedélyek a szervezetre is vonatkoznak. A Saját személyzet engedélyezése után az engedélyezett és felügyeleti szerepkörhöz rendelt felhasználók [https://mystaff.microsoft.com](https://mystaff.microsoft.com)a programon keresztül férhetnek hozzá. Kijelölhetnek egy AU-t az adott AU felhasználóinak megtekintéséhez, és kijelölhetnek egy felhasználót a profil megnyitásához.

## <a name="licenses"></a>Licencek

Minden olyan felhasználónak, aki engedélyezve van a Saját személyzetben, licenccel kell rendelkeznie, még akkor is, ha nem használja a Saját személyzet portált. Minden engedélyezett felhasználónak rendelkeznie kell az alábbi Azure AD- vagy Microsoft 365-licencekkel:

- Azure AD Premium P1 vagy P2
- Microsoft 365 F1 vagy F3

## <a name="reset-a-users-password"></a>Új felhasználói jelszó létrehozása

A következő szerepkörök rendelkeznek engedéllyel a felhasználó jelszavának alaphelyzetbe állításához:

- [Hitelesítési rendszergazda](directory-assign-admin-roles.md#authentication-administrator)
- [Kiemelt hitelesítési rendszergazda](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globális rendszergazda](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Ügyfélszolgálati rendszergazda](directory-assign-admin-roles.md#helpdesk-administrator)
- [Rendszergazda](directory-assign-admin-roles.md#user-administrator)
- [Jelszókezelő](directory-assign-admin-roles.md#password-administrator)

A **Saját személyzet ből**nyissa meg egy felhasználói profilt. Válassza **a Jelszó alaphelyzetbe állítása lehetőséget.**

- Ha a felhasználó csak felhőalapú, akkor egy ideiglenes jelszót is megadhat a felhasználónak.
- Ha a felhasználó szinkronizálása a helyszíni Active Directoryból történik, megadhat egy jelszót, amely megfelel a helyszíni AD-házirendeknek. Ezután adja meg ezt a jelszót a felhasználónak.

    ![Jelszó-visszaállítási folyamatjelző és sikerességi értesítés](media/my-staff-configure/reset-password.png)

A felhasználónak a következő bejelentkezéskor módosítania kell a jelszavát.

## <a name="manage-a-phone-number"></a>Telefonszám kezelése

A **Saját személyzet ből**nyissa meg egy felhasználói profilt.

- A **Telefonszám hozzáadása** szakasz kiválasztása a felhasználó telefonszámának hozzáadásához
- A **telefonszám szerkesztése** a telefonszám módosításához válassza a Telefonszámot
- A felhasználó telefonszámának eltávolításához válassza a **Telefonszám eltávolítása** lehetőséget.

A beállításoktól függően a felhasználó a beállított telefonszámot használhatja az SMS-sel történő bejelentkezéshez, a többtényezős hitelesítés végrehajtásához és az önkiszolgáló jelszó-visszaállításhoz.

A felhasználó telefonszámának kezeléséhez az alábbi szerepkörök egyikét kell hozzárendelnie:

- [Hitelesítési rendszergazda](directory-assign-admin-roles.md#authentication-administrator)
- [Kiemelt hitelesítési rendszergazda](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Globális rendszergazda](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Keresés

A munkatársaim keresősávja segítségével kereshet a szervezet ben lévő atusokat és felhasználókat. Kereshet a szervezet összes AU-jában és felhasználójában, de csak azon felhasználókat módosíthatja, akik olyan AU-ban vannak, amely felett rendszergazdai engedélyeket kapott.

Az AU-n belül is kereshet felhasználót. Ehhez használja a felhasználói lista tetején lévő keresősávot.

## <a name="audit-logs"></a>Naplók

Az Azure Active Directory-portálon a Saját munkatársak szolgáltatásban végrehajtott műveletek naplóiból naplózhat. Ha a naplót a Saját személyzet ben végrehajtott művelet hozta létre, akkor ezt a naplózási esemény TOVÁBBI RÉSZLETEK című témaköre jelzi.

## <a name="next-steps"></a>További lépések

[Saját személyzeti felhasználói dokumentáció](../user-help/my-staff-team-manager.md)
[Adminisztratív egységek dokumentációja](directory-administrative-units.md)
