---
title: Regisztrálja a személyes eszköz a vállalati hálózaton – Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, hogyan kell regisztrálni a személyes eszköz a vállalati hálózaton, a szervezet védett erőforrások eléréséhez.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 1f321ea3a7fffd4d54887fb41aee25b17d391e67
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825403"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Regisztrálja a személyes eszköz a vállalati hálózaton
Regisztrálja a személyes eszköz (általában egy telefonon vagy táblagépen) a vállalati hálózaton. Az eszköz regisztrálása után, akkor fog hozzáférni a szervezet korlátozott erőforrások eléréséhez.

>[!Note]
>Ez a cikk egy Windows eszköz használja bemutatási célokra, de az iOS, Android vagy macOS rendszerű eszközöket is regisztrálhatja.

## <a name="what-happens-when-you-register-your-device"></a>Mi történik, ha az eszköz regisztrálása
Az eszköz a vállalati hálózathoz a regisztrálás közben történik a következő műveleteket:

- Windows regisztrálja az eszközt a vállalati hálózaton.

- Igény szerint a szervezet lehetőségek alapján, akkor a rendszer keresztül vagy a kétlépéses ellenőrzés beállítása [multi-factor Authentication](multi-factor-authentication-end-user-first-time.md) vagy [biztonsági adatok](user-help-security-info-overview.md).

- Igény szerint lehetőségeket a szervezet alapján, akkor előfordulhat, hogy automatikusan regisztrálhatók mobileszköz-kezelés, például a Microsoft Intune. A Microsoft Intune-ban regisztrációval kapcsolatos további információkért lásd: [-eszköz regisztrálása az Intune-ban](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- A bejelentkezési folyamat lépünk, a felhasználónév és jelszó használata a munkahelyi vagy iskolai fiókjával.

## <a name="to-register-your-windows-device"></a>A Windows-eszköz regisztrálása

Kövesse az alábbi lépéseket a hálózaton a személyes eszköz regisztrálásához.

1. Nyissa meg **beállítások**, majd válassza ki **fiókok**.

    ![Fiókok, a beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza ki **hozzáférés munkahelyi vagy iskolai**, majd válassza ki **Connect** származó a **hozzáférés munkahelyi vagy iskolai** képernyő.

    ![Munkahelyi vagy iskolai képernyő eléréséhez Connect opció kiemelésével](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Az a **munkahelyi vagy iskolai fiók beállítása** képernyőn, majd válassza ki, írja be a munkahelyi vagy iskolai fiókjához tartozó e-mail cím **tovább**. Például: alain@contoso.com.

4. Jelentkezzen be munkahelyi vagy iskolai fiókjával, és válassza **jelentkezzen be a**.

5. Fejezze be a regisztrációs folyamat, beleértve a hagyja jóvá a személyazonosság-ellenőrző kérést, (Ha a kétlépéses ellenőrzés használja) beállítása és telepítése Windows Hello (ha szükséges).

## <a name="to-verify-that-youre-registered"></a>Ellenőrizze, hogy Ön regisztrálva van
Biztosíthatja, hogy Ön regisztrálva megnézzük a beállításokat.

1. Nyissa meg **beállítások**, majd válassza ki **fiókok**.

    ![Fiókok, a beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza ki **hozzáférés munkahelyi vagy iskolai**, és ellenőrizze, hogy a munkahelyi vagy iskolai fiókjával.

    ![Hozzáférés munkahelyi vagy iskolai képernyő és csatlakoztatott contoso-fiók](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>További lépések
Miután regisztrálta a személyes eszköz a vállalati hálózaton, hozzáférhet az erőforrások kell lennie.

- Ha a szervezet azt szeretné, hogy az eszköz munkahelyi csatlakoztatáson, [a munkahelyi eszköz csatlakoztatása a szervezet hálózati](user-help-join-device-on-network.md).



