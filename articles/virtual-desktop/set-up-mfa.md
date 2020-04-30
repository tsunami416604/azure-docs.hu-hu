---
title: Azure-Multi-Factor Authentication beállítása a Windows rendszerű virtuális asztali környezethez – Azure
description: Az Azure Multi-Factor Authentication beállítása a fokozott biztonság érdekében a Windows Virtual Desktopban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 069d2a153e307ed94032ce1d980f26521969fc56
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508343"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Azure-Multi-Factor Authentication engedélyezése a Windows rendszerű virtuális asztalok számára

A Windows rendszerű virtuális asztal Windows-ügyfele kiváló megoldás a Windows rendszerű virtuális asztalok helyi géppel való integrálására. Ha azonban a Windows rendszerű virtuális asztali fiókját a Windows-ügyfélre konfigurálja, bizonyos intézkedésekre van szükség, hogy a felhasználók biztonságban maradjanak.

Amikor először jelentkezik be, az ügyfél a felhasználónevet, a jelszót és az Azure MFA-t kéri. Ezután, amikor legközelebb bejelentkezik, az ügyfél a Azure Active Directory (AD) vállalati alkalmazásból emlékszik a tokenre. Ha a **Megjegyzés**bejelölése lehetőséget választja, a felhasználók az ügyfél újraindítása után is bejelentkezhetnek, anélkül, hogy újra meg kellene adniuk a hitelesítő adataikat.

Habár a hitelesítő adatok megjegyzése kényelmes, az üzembe helyezést a vállalati forgatókönyvek vagy a személyes eszközök kevésbé biztonságosak is tehetik. A felhasználók biztosításához meg kell győződnie arról, hogy az ügyfél továbbra is kéri az Azure Multi-Factor Authentication (MFA) hitelesítő adatok megadását. Ez a cikk bemutatja, hogyan konfigurálhatja a Windows rendszerű virtuális asztal feltételes hozzáférési szabályzatát a beállítás engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A következő lépésekkel kell kezdenie:

- Rendeljen hozzá prémium szintű Azure Active Directory P1 vagy P2-t tartalmazó licencet a felhasználókhoz.
- Egy Azure Active Directory csoport, amely a felhasználók csoport tagjaként van hozzárendelve.
- Engedélyezze az Azure MFA-t az összes felhasználó számára. Ennek módjáról további információt a [felhasználó kétlépéses ellenőrzésének megkövetelése](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)című témakörben talál.

> [!NOTE]
> A következő beállítás a [Windows rendszerű virtuális asztali webes ügyfélprogramra](https://rdweb.wvd.microsoft.com/webclient/index.html)is érvényes.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre olyan feltételes hozzáférési szabályzatot, amely a Windows rendszerű virtuális asztalhoz való csatlakozáskor többtényezős hitelesítést igényel.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését**.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **felhasználók és csoportok** > kiválasztása**felhasználók és csoportok** lehetőséget > válassza ki az előfeltételek szakaszban létrehozott csoportot.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Cloud apps vagy** > **a**műveletek területen válassza az **alkalmazások kiválasztása**lehetőséget.
   1. Válassza a **Windows virtuális asztal** és a **Windows rendszerű virtuális asztali ügyfél**lehetőséget, majd válassza a **kiválasztás** , majd a **kész**lehetőséget.
   ![Képernyőkép a Cloud apps vagy a Actions lapról. A Windows rendszerű virtuális asztali és a Windows rendszerű virtuális asztali ügyfélprogramok piros színnel vannak kiemelve.](media/cloud-apps-enterprise-selected.png)
1. A **hozzáférés-vezérlés** > **megadása**területen válassza a **hozzáférés biztosítása**, **többtényezős hitelesítés megkövetelése**, majd a **lehetőséget**.
1. A **hozzáférés-vezérlési** > **munkamenet**területen válassza a **bejelentkezési gyakoriság**elemet, állítsa az értéket **1** értékre, a mértékegységet pedig **órákra**, majd **válassza a elemet**.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>További lépések

- [További információ a feltételes hozzáférési házirendekről](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [További információ a felhasználói bejelentkezés gyakoriságáról](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
