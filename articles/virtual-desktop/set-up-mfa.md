---
title: Azure-Multi-Factor Authentication beállítása a Windows rendszerű virtuális asztali környezethez – Azure
description: Az Azure Multi-Factor Authentication beállítása a fokozott biztonság érdekében a Windows Virtual Desktopban.
author: Heidilohr
ms.topic: how-to
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e8e723aa26ab08c8a09e75f506802101dc07f7e8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017777"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Az Azure Multi-Factor Authentication engedélyezése Windows Virtual Desktophoz

>[!IMPORTANT]
> Ha ezt az oldalt a Windowsos virtuális asztal (klasszikus) dokumentációjában látogatja meg, akkor a befejezést követően [térjen vissza a Windows virtuális asztali (klasszikus) dokumentációba](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) .

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

Ebből a témakörből megtudhatja, hogyan hozhat létre olyan feltételes hozzáférési szabályzatot, amely többtényezős hitelesítést igényel a Windows virtuális asztalhoz való csatlakozáskor:

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
2. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
3. Válassza az **új szabályzat**lehetőséget.
4. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
5. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
6. A **Belefoglalás**területen válassza a **felhasználók és csoportok kiválasztása**  >  **felhasználók és csoportok** lehetőséget > válassza ki az [Előfeltételek](#prerequisites) szakaszban létrehozott csoportot.
7. Válassza a **Kész** lehetőséget.
8. A **Cloud apps vagy a műveletek**területen  >  **Include**válassza az **alkalmazások kiválasztása**lehetőséget.
9. Válassza ki a következő alkalmazások egyikét, amely alapján a Windows rendszerű virtuális asztali verziót használja.
   - Ha a Windowsos virtuális asztalt (klasszikus) használja, válassza ki ezt az alkalmazást:
       - **Windows rendszerű virtuális asztal** (alkalmazás-azonosító 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
   - Ha a Windows rendszerű virtuális asztalt használja, válassza a következő alkalmazást:
       -  **Windows rendszerű virtuális asztal** (alkalmazás-azonosító 9cdead84-A844-4324-93f2-b2e6bb768d07)

   >[!IMPORTANT]
   > Ne válassza ki a Windows rendszerű virtuális asztali Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63) nevű alkalmazást. Ez az alkalmazás csak a felhasználói hírcsatorna beolvasására szolgál, és nem rendelkezhet MFA-val.

10. Lépjen a **feltételek**  >  **ügyfélalkalmazások**elemre, majd válassza ki, hogy hová szeretné alkalmazni a szabályzatot:
    
    - Válassza a **böngésző** lehetőséget, ha azt szeretné, hogy a házirend a webes ügyfélre vonatkozzon.
    - Válassza a **Mobile apps és asztali ügyfelek** lehetőséget, ha a szabályzatot más ügyfelekre szeretné alkalmazni.
    - Jelölje be mindkét jelölőnégyzetet, ha az összes ügyfélre alkalmazni kívánja a házirendet.
   
    > [!div class="mx-imgBorder"]
    > ![Az ügyfélalkalmazások oldal képernyőképe. A felhasználó bejelölte a Mobile apps és az asztali ügyfelek jelölőnégyzetet.](media/select-apply.png)

11. Miután kiválasztotta az alkalmazást, válassza a **kiválasztás**lehetőséget, majd kattintson a **kész**gombra.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Cloud apps vagy a Actions lapról. A Windows rendszerű virtuális asztali és a Windows rendszerű virtuális asztali ügyfélprogramok piros színnel vannak kiemelve.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >A kijelölni kívánt alkalmazás AZONOSÍTÓjának megkereséséhez nyissa meg a **vállalati alkalmazások** elemet, és válassza a **Microsoft-alkalmazások** lehetőséget az alkalmazás típusa legördülő menüből.

12. A **hozzáférés-vezérlés**  >  **megadása**területen válassza a **hozzáférés biztosítása**, **többtényezős hitelesítés megkövetelése**, majd a **lehetőséget**.
13. A **hozzáférés-vezérlési**  >  **munkamenet**területen válassza a **bejelentkezési gyakoriság**elemet, állítsa az értéket **1** értékre, a mértékegységet pedig **órákra**, majd válassza a **kiválasztás**lehetőséget.
14. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
15. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

- [További információ a feltételes hozzáférési házirendekről](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [További információ a felhasználói bejelentkezés gyakoriságáról](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
