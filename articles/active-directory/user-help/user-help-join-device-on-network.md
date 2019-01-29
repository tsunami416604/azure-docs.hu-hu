---
title: A munkahelyi eszköz csatlakoztatása a szervezet hálózati – Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a munkahelyi eszközök a vállalati hálózathoz.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: ff36c4c9becd797d98de401606a45c05525d0faf
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190513"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>A munkahelyi eszköz csatlakoztatása a vállalati hálózaton
A munkahelyi tulajdonú Windows 10 rendszerű eszköz csatlakoztatása a vállalati hálózaton potenciálisan korlátozott erőforrások eléréséhez.

## <a name="what-happens-when-you-join-your-device"></a>Mi történik, ha az eszköz csatlakozik
Windows 10 rendszerű eszközét a vállalati hálózathoz csatlakozik, miközben történik, a következő műveleteket:

- Az eszköz a vállalati hálózaton, ami lehetővé teszi az erőforrások a személyes fiókjával hozzáférni Windows regisztrál. Az eszköz regisztrálása után Windows majd csatlakoztatja az eszközt a hálózathoz, így jelentkezik be, és a korlátozott erőforrások eléréséhez használhatja a szervezet felhasználónevet és jelszót.

- Igény szerint a szervezet lehetőségek alapján, akkor a rendszer keresztül vagy a kétlépéses ellenőrzés beállítása [multi-factor Authentication](multi-factor-authentication-end-user-first-time.md) vagy [biztonsági adatok](user-help-security-info-overview.md).

- Igény szerint lehetőségeket a szervezet alapján, akkor előfordulhat, hogy automatikusan regisztrálhatók mobileszköz-kezelés, például a Microsoft Intune. A Microsoft Intune-ban regisztrációval kapcsolatos további információkért lásd: [-eszköz regisztrálása az Intune-ban](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- A bejelentkezési folyamat lépünk használatával automatikus bejelentkezés a munkahelyi fiókjával.

## <a name="to-join-a-brand-new-windows-10-device"></a>A leállításig; a Windows 10-es eszköz csatlakoztatása
Ha az eszköz vadonatúj, és még nincs beállítva, nyissa meg az eszköz csatlakoztatása a hálózathoz, a Windows a kezdőélmény (OOBE) folyamatát.

1. Indítsa el az új eszközt, és megkezdheti a OOBE folyamaton.

2. Az a **Bejelentkezés Microsoft-fiókkal** képernyőn írja be a munkahelyi vagy iskolai e-mail-címét.

    ![Bejelentkezési képernyő, e-mail-címmel](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Az a **adja meg a jelszót** írja be a jelszót.

    ![Adja meg a jelszót képernyő](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. A mobileszközén hagyja jóvá az eszköz, ezért a fiók hozzáférhessen. 

    ![Mobilértesítést képernyő](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Fejezze be a OOBE folyamaton, beleértve a beállítás az adatvédelmi beállításokat, és beállítása Windows Hello (ha szükséges).

    Az eszköz most már csatlakozott a munkahelyi hálózathoz.

## <a name="to-make-sure-youre-joined"></a>Győződjön meg arról, hogy csatlakoznak
Biztos lehet benne, hogy csatlakoznak-megnézzük a beállításokat.

1. Nyissa meg **beállítások**, majd válassza ki **fiókok**.

    ![Fiókok, a beállítások képernyőn](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Válassza ki **hozzáférés munkahelyi vagy iskolai**, és ellenőrizze, hogy arról, hogy a következőhöz hasonló, szöveges **csatlakozik *< Saját_szervezet elemre >* Azure ad-ben**.

    ![Hozzáférés munkahelyi vagy iskolai képernyő és csatlakoztatott contoso-fiók](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Csatlakozás egy már konfigurált Windows 10 rendszerű eszköz
Ha már rendelkezik egy ideig, és ez az eszköz már létre van hozva, hajtsa végre az alábbi lépéseket az eszköz csatlakoztatása a hálózathoz.

1. Nyissa meg **beállítások**, majd válassza ki **fiókok**.

2. Válassza ki **hozzáférés munkahelyi vagy iskolai**, majd válassza ki **Connect**.

    ![Hozzáférés munkahelyi vagy iskolai és Connect hivatkozások](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Az a **munkahelyi vagy iskolai fiók beállítása** képernyőn válassza ki **eszköz csatlakoztatása az Azure Active Directory**.

    ![Állítsa be a munkahelyi vagy iskolai fiók képernyő](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Az a **jelentkezzen** írja be az e-mail-címét (például alain@contoso.com), majd válassza ki **tovább**.

    ![Képernyőn jelentkezzen](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Az a **jelszó** képernyőn írja be a jelszavát, majd válassza ki **jelentkezzen be a**.

    ![Írja be a jelszót](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. A mobileszközén hagyja jóvá az eszköz, ezért a fiók hozzáférhessen. 

    ![Mobilértesítést képernyő](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Az a **ellenőrizze, hogy ez a szervezet** képernyőn, majd válassza ki, tekintse át az adatokat, hogy megfelelő **csatlakozzon**.

    ![Ellenőrizze, hogy ez az a szervezet ellenőrzési képernyő](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Az a **készen vagyunk** kattintson **kész**.

    ![Ön az összes beállítása képernyő](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Győződjön meg arról, hogy csatlakoznak
Biztos lehet benne, hogy csatlakoznak-megnézzük a beállításokat.

1. Nyissa meg **beállítások**, majd válassza ki **fiókok**.

    ![Fiókok, a beállítások képernyőn](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Válassza ki **hozzáférés munkahelyi vagy iskolai**, és ellenőrizze, hogy arról, hogy a következőhöz hasonló, szöveges **csatlakozik *< Saját_szervezet elemre >* Azure ad-ben**.

    ![Hozzáférés munkahelyi vagy iskolai képernyő és csatlakoztatott contoso-fiók](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>További lépések
Miután az eszköz csatlakoztatása a szervezet hálózatához, elérhető minden, az erőforrások a munkahelyi vagy iskolai fiókjának adatait, képesnek kell lennie.

- Ha a szervezet azt szeretné, hogy regisztrálja a személyes eszköz, például a telefon [regisztrálja a személyes eszköz a vállalati hálózaton](user-help-register-device-on-network.md).

