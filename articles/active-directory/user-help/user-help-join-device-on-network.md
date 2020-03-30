---
title: A munkahelyi eszköz csatlakoztatása a szervezet hálózatához – AD
description: Ismerje meg, hogyan csatlakozhat munkahelyi eszközéhez a szervezet hálózatához.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 0ff8b85a15d94ded2d702e0df247f9ebc4d3f923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266311"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>A munkahelyi eszköz csatlakoztatása a szervezet hálózatához
Csatlakozzon a munkahelyi Windows 10-es eszközéhez a szervezet hálózatához, hogy hozzáférhessen a potenciálisan korlátozott erőforrásokhoz.

## <a name="what-happens-when-you-join-your-device"></a>Mi történik, ha csatlakozik a készülékhez?
Miközben csatlakozik a Windows 10-es eszközéhez a szervezet hálózatához, a következő műveletek fognak történni:

- A Windows regisztrálja az eszközt a szervezet hálózatára, így a személyes fiókjával érheti el az erőforrásokat. Az eszköz regisztrálása után a Windows csatlakoztatja az eszközt a hálózathoz, így a szervezet felhasználónevével és jelszavával bejelentkezhet, és hozzáférhet a korlátozott erőforrásokhoz.

- Ha a szervezet választásai alapján a rendszer a kétlépéses ellenőrzést [a többtényezős hitelesítés](multi-factor-authentication-end-user-first-time.md) vagy a [biztonsági adatok](user-help-security-info-overview.md)alapján is megkéri.

- Szükség esetén a szervezet döntései alapján előfordulhat, hogy automatikusan regisztrált a mobileszköz-felügyeletbe, például a Microsoft Intune-ba. A Microsoft Intune-ban való regisztrálásról az [Eszköz regisztrálása az Intune-ban (Regisztrálás) témakörben](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all)talál további információt.

- A bejelentkezési folyamaton keresztül a szervezeti fiókkal való automatikus bejelentkezéssel kell végigmennie.

## <a name="to-join-a-brand-new-windows-10-device"></a>Csatlakozás vadonatúj Windows 10-es eszközhöz
Ha az eszköz vadonatúj, és még nincs beállítva, a Windows kezdőélmény (OOBE) folyamatán keresztül csatlakozhat az eszközhöz a hálózathoz.

1. Indítsa el az új eszközt, és indítsa el az OOBE folyamatot.

2. A Bejelentkezés a **Microsofttal** képernyőn írja be munkahelyi vagy iskolai e-mail címét.

    ![Bejelentkezési képernyő e-mail címmel](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. A **Jelszó megadása** képernyőn írja be a jelszót.

    ![Adja meg a jelszó képernyőjét](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Mobileszközén hagyja jóvá az eszközt, hogy hozzáférhessen a fiókjához. 

    ![Mobil értesítési képernyő](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. A kezdőélmény-folyamat befejezése, beleértve az adatvédelmi beállítások beállítását és a Windows Hello beállítását (ha szükséges).

    Az eszköz most már csatlakozik a szervezet hálózatához.

## <a name="to-make-sure-youre-joined"></a>Annak biztosítása, hogy csatlakozott
A beállításokkal meggyőződhet arról, hogy csatlakozik.

1. Nyissa **meg a Beállítások**lehetőséget, és válassza a **Fiókok**lehetőséget.

    ![Fiókok a Beállítások képernyőn](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Válassza **az Access munka vagy iskola**lehetőséget, és győződjön meg arról, hogy a ** A your_organization * \<>* Azure AD-hez való csatlakozás**szövege jelenik meg.

    ![Munkahelyi vagy iskolai képernyő elérése csatlakoztatott contoso-fiókkal](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Csatlakozás egy már konfigurált Windows 10-es eszközhöz
Ha már egy ideje rendelkezik az eszközzel, és az már be van állítva, az alábbi lépésekkel csatlakozhat az eszközhöz a hálózathoz.

1. Nyissa **meg a Beállítások**lehetőséget, és válassza a **Fiókok**lehetőséget.

2. Válassza **az Access Work or school (Hozzáférés munka vagy iskola)** lehetőséget, majd a **Csatlakozás**lehetőséget.

    ![Munkahelyi vagy iskolai és Connect hivatkozások elérése](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. A **Munkahelyi vagy iskolai fiók beállítása** képernyőn válassza az Eszköz **csatlakoztatása az Azure Active Directoryhoz**lehetőséget.

    ![Munkahelyi vagy iskolai fiókképernyő beállítása](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. A **Let's get you signed in (Let's get you signed in) (A lap bebejelentkezése)** képernyőn írja be az e-mail címét (például alain@contoso.com), majd válassza a **Tovább**gombot.

    ![Jelentkezz be a képernyőre!](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. A **Jelszó megadása** képernyőn írja be a jelszavát, majd válassza **a Bejelentkezés**lehetőséget.

    ![Jelszó megadása](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Mobileszközén hagyja jóvá az eszközt, hogy hozzáférhessen a fiókjához. 

    ![Mobil értesítési képernyő](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. A **Győződjön meg arról, hogy ez a szervezet** képernyője, tekintse át az adatokat, és ellenőrizze, hogy helyes-e, majd válassza a **Csatlakozás**lehetőséget.

    ![Győződjön meg arról, hogy ez a szervezet ellenőrzési képernyője](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. A **"Minden beállítás"** képernyőn kattintson a **Kész gombra.**

    ![Minden beállított képernyő](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Annak biztosítása, hogy csatlakozott
A beállításokkal meggyőződhet arról, hogy csatlakozik.

1. Nyissa **meg a Beállítások**lehetőséget, és válassza a **Fiókok**lehetőséget.

    ![Fiókok a Beállítások képernyőn](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Válassza **az Access munka vagy iskola**lehetőséget, és győződjön meg arról, hogy a ** A your_organization * \<>* Azure AD-hez való csatlakozás**szövege jelenik meg.

    ![Munkahelyi vagy iskolai képernyő elérése csatlakoztatott contoso-fiókkal](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>További lépések
Miután csatlakozott az eszközhöz a szervezet hálózatához, a munkahelyi vagy iskolai fiókadatai alapján az összes erőforrást elérheti.

- Ha a szervezet azt szeretné, hogy regisztrálja a személyes eszközét, például a telefont, olvassa el [a Személyes eszköz regisztrálása a szervezet hálózatán](user-help-register-device-on-network.md).

- Ha a szervezetet a Microsoft Intune-nal kezelik, és kérdései vannak a regisztrációval, a bejelentkezéssel vagy az Intune bármely más, az Intune-nal kapcsolatos problémával kapcsolatban, olvassa el az [Intune felhasználói súgótartalmát.](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done)