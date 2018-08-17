---
title: Regisztrálja a személyes eszköz a vállalati hálózaton – Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, hogyan kell regisztrálni a személyes eszköz a vállalati hálózaton, a szervezet védett erőforrások eléréséhez.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180828"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Regisztrálja a személyes eszköz a vállalati hálózaton
Regisztrálja a személyes eszköz, általában egy telefonjához vagy táblagépéhez, a vállalati hálózaton. Az eszköz regisztrálása után, akkor fog hozzáférni a szervezet korlátozott erőforrások eléréséhez.

>[!Note]
>Ez a cikk egy Windows eszköz használja bemutatási célokra, de az iOS, Android vagy macOS rendszerű eszközöket is regisztrálhatja.

## <a name="what-happens-when-you-register-your-device"></a>Mi történik, ha az eszköz regisztrálása
Az eszköz a vállalati hálózathoz a regisztrálás közben történik a következő műveleteket:

- Windows regisztrálja az eszközt a vállalati hálózaton.

- Igény szerint a szervezet lehetőségek alapján, akkor a rendszer keresztül vagy a kétlépéses ellenőrzés beállítása [multi-factor Authentication](multi-factor-authentication-end-user-first-time.md) vagy [biztonsági adatok](user-help-security-info-overview.md).

- Igény szerint lehetőségeket a szervezet alapján, akkor előfordulhat, hogy automatikusan regisztrálhatók mobileszköz-kezelés, például a Microsoft Intune. A Microsoft Intune-ban regisztrációval kapcsolatos további információkért lásd: [-eszköz regisztrálása az Intune-ban](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- A bejelentkezési folyamat lépünk, a felhasználónév és jelszó használata a személyes Microsoft-fiókjával.

## <a name="to-register-your-windows-device"></a>A Windows-eszköz regisztrálása

Kövesse az alábbi lépéseket a hálózaton a személyes eszköz regisztrálásához.

1. Nyissa meg **beállítások**, majd válassza ki **fiókok**.

    ![Fiókok, a beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza ki **E-mail és fiókok**, majd válassza ki **csatlakozzon a Microsoft-fiók**.

    ![E-mailek & a fiókok és a egy Microsoft-fiók hivatkozások hozzáadása](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. Az a **Microsoft-fiók beállítása** képernyőn írja be az e-mail-címét, a személyes Microsoft-fiókjával.

    ![A Microsoft fiók e-mail-címével képernyő hozzáadása](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. Az a **jelszó** képernyőn írja be a személyes Microsoft-fiók jelszavát, majd válassza ki **jelentkezzen be a**.

    ![Adja meg a jelszót képernyő](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. Fejezze be a regisztrációs folyamat, beleértve a hagyja jóvá a személyazonosság-ellenőrző kérést, (Ha a kétlépéses ellenőrzés használja) beállítása és telepítése Windows Hello (ha szükséges).

## <a name="to-make-sure-youre-registered"></a>Győződjön meg arról, hogy már regisztrált
Biztosíthatja, hogy Ön regisztrálva megnézzük a beállításokat.

1. Nyissa meg **beállítások**, majd válassza ki **fiókok**.

    ![Fiókok, a beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza ki **E-mail és fiókok**, és ellenőrizze, hogy a személyes Microsoft-fiókjával.

    ![Hozzáférés munkahelyi vagy iskolai képernyő és csatlakoztatott contoso-fiók](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>További lépések
Miután regisztrálta a személyes eszköz a vállalati hálózaton, hozzáférhet az erőforrások kell lennie.

- Ha a szervezet azt szeretné, hogy az eszköz munkahelyi csatlakoztatáson, [a munkahelyi eszköz csatlakoztatása a szervezet hálózati](user-help-join-device-on-network.md).



