---
title: Munkahelyi eszköz csatlakoztatása a szervezet hálózatához – AD
description: Ismerje meg, hogyan csatlakozhat munkahelyi eszközéhez a szervezet hálózatához.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 6c93f34068cd296cb2d3a59f208fa644cdbf2260
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83745724"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Munkahelyi eszköz csatlakoztatása a szervezet hálózatához
Csatlakoztassa a munkahelyi Windows 10-es eszközét a szervezet hálózatához, hogy hozzáférhessen a potenciálisan korlátozott erőforrásokhoz.

## <a name="what-happens-when-you-join-your-device"></a>Mi történik az eszköz csatlakoztatásakor?
A Windows 10-es eszköznek a szervezet hálózatához való csatlakoztatása közben a következő műveletek történnek:

- A Windows regisztrálja az eszközt a szervezet hálózatán, és lehetővé teszi az erőforrások elérését a személyes fiókjával. Az eszköz regisztrálása után a Windows csatlakoztatja az eszközt a hálózathoz, így a bejelentkezéshez és a korlátozott erőforrásokhoz való hozzáféréshez a szervezet felhasználónevét és jelszavát is használhatja.

- A szervezete által választott lehetőségek alapján előfordulhat, hogy az [multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) vagy a [biztonsági adatok](user-help-security-info-overview.md)között két lépésből álló ellenőrzést kell beállítania.

- A szervezete által választott lehetőségek alapján előfordulhat, hogy automatikusan regisztrálva van a mobileszköz-kezelésben, például Microsoft Intune. További információ a Microsoft Intune való regisztrálásáról: [eszköz regisztrálása az Intune-ban](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- A bejelentkezési folyamattal a szervezeti fiókjával való automatikus bejelentkezést használva végezheti el a bejelentkezést.

## <a name="to-join-a-brand-new-windows-10-device"></a>Új Windows 10-es eszköz csatlakoztatása
Ha az eszköz teljesen új, és még nincs beállítva, átléphet a Windows beépített felhasználói felületének (OOBE) folyamatával, amellyel csatlakoztathatja az eszközt a hálózathoz.

1. Indítsa el az új eszközt, és kezdje meg az OOBE folyamatát.

2. A **Bejelentkezés Microsoft-fiókkal** képernyőn írja be a munkahelyi vagy iskolai e-mail-címét.

    ![Bejelentkezési képernyő e-mail-címmel](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. A **jelszó megadása** képernyőn írja be a jelszót.

    ![Adja meg a jelszó képernyőjét](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. A mobileszközön hagyja jóvá az eszközt, hogy hozzáférhessen a fiókjához. 

    ![Mobil értesítési képernyő](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Fejezze be az OOBE folyamatát, beleértve az adatvédelmi beállítások megadását és a Windows Hello beállítását (ha szükséges).

    Az eszköz most már csatlakoztatva van a szervezet hálózatához.

## <a name="to-make-sure-youre-joined"></a>Annak ellenőrzése, hogy csatlakozik-e
Győződjön meg arról, hogy a beállításait megtekinti.

1. Nyissa meg a **Beállítások**, majd a **fiókok**elemet.

    ![Fiókok a beállítások képernyőn](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Válassza a **hozzáférés munkahelyi vagy iskolai**rendszerhez lehetőséget, és győződjön meg arról, hogy az ** *\<your_organization>* Azure ad-hez kapcsolódóan**hasonló szöveg jelenik meg.

    ![Munkahelyi vagy iskolai képernyő elérése csatlakoztatott contoso-fiókkal](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Csatlakozás egy már konfigurált Windows 10-es eszközhöz
Ha már beállította az eszközét, és már be van állítva, akkor az alábbi lépéseket követve csatlakoztathatja az eszközt a hálózathoz.

1. Nyissa meg a **Beállítások**, majd a **fiókok**elemet.

2. Válassza a **hozzáférés munkahelyi vagy iskolai**rendszerhez, majd a **Kapcsolódás**lehetőséget.

    ![Munkahelyi vagy iskolai hozzáférés és hivatkozások összekapcsolása](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. A **munkahelyi vagy iskolai fiók beállítása** képernyőn válassza az **eszköz csatlakoztatása a Azure Active Directory**lehetőséget.

    ![Munkahelyi vagy iskolai fiók képernyőjének beállítása](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. A **bejelentkezett** képernyőn írja be az e-mail-címét (például: alain@contoso.com ), majd kattintson a **tovább**gombra.

    ![A bejelentkezett képernyő beolvasása](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. A **jelszó megadása** képernyőn írja be a jelszót, majd válassza a **Bejelentkezés**lehetőséget.

    ![Jelszó megadása](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. A mobileszközön hagyja jóvá az eszközt, hogy hozzáférhessen a fiókjához. 

    ![Mobil értesítési képernyő](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. A győződjön meg róla, hogy **Ez a szervezet** képernyő, tekintse át az adatokat, és győződjön meg arról, hogy a megfelelő, majd válassza a **Csatlakozás**lehetőséget.

    ![Győződjön meg arról, hogy a szervezet ellenőrző képernyője](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Az **összes beállított** képernyőn kattintson a **kész**gombra.

    ![Az összes beállított képernyő](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Annak ellenőrzése, hogy csatlakozik-e
Győződjön meg arról, hogy a beállításait megtekinti.

1. Nyissa meg a **Beállítások**, majd a **fiókok**elemet.

    ![Fiókok a beállítások képernyőn](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Válassza a **hozzáférés munkahelyi vagy iskolai**rendszerhez lehetőséget, és győződjön meg arról, hogy az ** *\<your_organization>* Azure ad-hez kapcsolódóan**hasonló szöveg jelenik meg.

    ![Munkahelyi vagy iskolai képernyő elérése csatlakoztatott contoso-fiókkal](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>További lépések
Miután csatlakoztatta az eszközt a szervezete hálózatához, a munkahelyi vagy iskolai fiókja adataival férhet hozzá az összes erőforráshoz.

- Ha a szervezete szeretné regisztrálni a személyes eszközét, például a telefont, tekintse meg a [személyes eszköz regisztrálása a szervezet hálózatán](user-help-register-device-on-network.md)című témakört.

- Ha a szervezetét Microsoft Intune használatával felügyeli, és kérdései vannak a beléptetéssel, a bejelentkezéssel vagy bármely más, az Intune-nal kapcsolatos problémával kapcsolatban, tekintse meg az [Intune felhasználói súgójának tartalmát](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done).