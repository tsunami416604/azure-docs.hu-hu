---
title: Feltételes hozzáférés – örökölt hitelesítés tiltása – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az örökölt hitelesítési protokollok blokkolásához
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc7710619b8975064ec4ec60ed44ccede970ac34
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200946"
---
# <a name="conditional-access-block-legacy-authentication"></a>Feltételes hozzáférés: Régi hitelesítési folyamat letiltása

Az örökölt hitelesítési protokollok fokozott kockázata miatt a Microsoft azt javasolja, hogy a szervezetek ezen protokollok használatával blokkolják a hitelesítési kérelmeket, és modern hitelesítést igényeljenek.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítséget nyújtanak egy feltételes hozzáférési szabályzat létrehozásához az örökölt hitelesítési kérelmek blokkolásához.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > a**feltételes hozzáférést**.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **felhasználók és csoportok** lehetőséget, és válassza ki azokat a fiókokat, amelyeknek fenn kell tartaniuk a régi hitelesítés használatát. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Cloud apps vagy a műveletek** > területen**válassza a** **minden felhőalapú alkalmazás**lehetőséget.
   1. Ha ki kell zárnia bizonyos alkalmazásokat a szabályzatból, kiválaszthatja őket a **kizárás lapon** a **kizárt felhőalapú alkalmazások kiválasztása** területen, majd válassza a **kiválasztás**lehetőséget.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **feltételek** > **ügyfélalkalmazások (előzetes verzió)** területen állítsa az **Igen**értékre a **konfigurálást** .
   1. Csak a **Mobile apps és az asztali ügyfelek** > **egyéb ügyfeleinek**bejelölése.
   2. Válassza a **Done** (Kész) lehetőséget.
1. A **hozzáférés-vezérlés** > **megadása**területen válassza a **hozzáférés letiltása**lehetőséget.
   1. Válassza a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
