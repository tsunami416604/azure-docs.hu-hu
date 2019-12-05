---
title: Feltételes hozzáférés – örökölt hitelesítés tiltása – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az örökölt hitelesítési protokollok blokkolásához
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b4627080879c9e7d2635b950bb7f31b7d23581
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803631"
---
# <a name="conditional-access-block-legacy-authentication"></a>Feltételes hozzáférés: örökölt hitelesítés tiltása

Az örökölt hitelesítési protokollok fokozott kockázata miatt a Microsoft azt javasolja, hogy a szervezetek ezen protokollok használatával blokkolják a hitelesítési kérelmeket, és modern hitelesítést igényeljenek.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítséget nyújtanak egy feltételes hozzáférési szabályzat létrehozásához az örökölt hitelesítési kérelmek blokkolásához.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **felhasználók és csoportok** lehetőséget, és válassza ki azokat a fiókokat, amelyeknek fenn kell tartaniuk a régi hitelesítés használatát. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **feltételek** > **ügyfélalkalmazások (előzetes verzió)** területen állítsa **az** **Igen**értékre.
   1. Győződjön meg arról, hogy csak a **Mobile apps és az asztali ügyfelek** > **más ügyfeleket**.
   2. Válassza a **Done** (Kész) lehetőséget.
1. A **hozzáférés-vezérlés** > a **támogatás**területen válassza a **hozzáférés letiltása**lehetőséget.
   1. Válassza a **Kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
