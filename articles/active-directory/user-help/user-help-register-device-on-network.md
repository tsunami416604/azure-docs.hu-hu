---
title: Személyes eszközök regisztrálása a szervezet hálózatán – Azure AD
description: Ismerje meg, hogyan regisztrálhatja személyes eszközét a szervezet hálózatán, hogy hozzáférhessen a szervezet védett erőforrásaihoz.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e2e6585bac100a09f3f98037e90b24738e22816f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063866"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Személyes eszköz regisztrálása a szervezet hálózatán
Regisztrálja személyes eszközét (általában telefont vagy táblagépet) a szervezet hálózatán. Az eszköz regisztrálása után hozzáférhet a szervezet korlátozott erőforrásaihoz.

>[!Note]
>Ez a cikk egy Windows-eszközt használ demonstrációs célokra, de regisztrálhatja az iOS, Android vagy macOS rendszert futtató eszközöket is.

## <a name="what-happens-when-you-register-your-device"></a>Mi történik a készülék regisztrálásakor?
Miközben regisztrálja az eszközt a szervezet hálózatán, a következő műveletek fognak történni:

- A Windows regisztrálja az eszközt a szervezet hálózatán.

- Ha a szervezet választásai alapján a rendszer a kétlépéses ellenőrzést [a többtényezős hitelesítés](multi-factor-authentication-end-user-first-time.md) vagy a [biztonsági adatok](user-help-security-info-overview.md)alapján is megkéri.

- Szükség esetén a szervezet döntései alapján előfordulhat, hogy automatikusan regisztrált a mobileszköz-felügyeletbe, például a Microsoft Intune-ba. A Microsoft Intune-ban való regisztrálásról az [Eszköz regisztrálása az Intune-ban (Regisztrálás) témakörben](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all)talál további információt.

- A bejelentkezési folyamaton keresztül a munkahelyi vagy iskolai fiókjához használt felhasználónév és jelszó használatával.

## <a name="to-register-your-windows-device"></a>A Windows-eszköz regisztrálása

Az alábbi lépésekkel regisztrálhatja személyes eszközét a hálózaton.

1. Nyissa **meg a Beállítások**lehetőséget, és válassza a **Fiókok**lehetőséget.

    ![Fiókok a Beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza **az Access munka vagy iskola**lehetőséget, majd az Access munkahelyi vagy **iskolai** képernyőjén válassza a **Csatlakozás** lehetőséget.

    ![Munkahelyi vagy iskolai képernyő elérése kiemelt Csatlakozás lehetőséggel](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. A **Munkahelyi vagy iskolai fiók hozzáadása** képernyőn írja be a munkahelyi vagy iskolai fiókhoz tartozó e-mail címét, és válassza a Tovább **gombot.** Például: alain@contoso.com.

4. Jelentkezzen be munkahelyi vagy iskolai fiókjába, és válassza **a Bejelentkezés**lehetőséget.

5. Végezze el a regisztrációs folyamat hátralévő részét, beleértve a személyazonosság-ellenőrzési kérelem jóváhagyását (ha kétlépéses ellenőrzést használ), és szükség esetén a Windows Hello beállítását).

## <a name="to-verify-that-youre-registered"></a>Annak ellenőrzése, hogy regisztrált-e
A beállításokból meggyőződhet arról, hogy regisztrált.

1. Nyissa **meg a Beállítások**lehetőséget, és válassza a **Fiókok**lehetőséget.

    ![Fiókok a Beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza **az Access Work or school (Hozzáférés a munka vagy az iskola)** lehetőséget, és győződjön meg arról, hogy a munkahelyi vagy iskolai fiókja látható.

    ![Munkahelyi vagy iskolai képernyő elérése csatlakoztatott contoso-fiókkal](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>További lépések
Miután regisztrálta személyes eszközét a szervezet hálózatára, hozzá kell tudnia férni az erőforrások nagy részéhez.

- Ha a szervezet azt szeretné, hogy csatlakozzon a munkahelyi eszközéhez, olvassa el [A munkahelyi eszköz csatlakoztatása a szervezet hálózatához](user-help-join-device-on-network.md).



