---
title: Feltételes hozzáférés – örökölt hitelesítés tiltása – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az örökölt hitelesítési protokollok blokkolásához
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295219"
---
# <a name="conditional-access-block-legacy-authentication"></a>Feltételes hozzáférés: örökölt hitelesítés tiltása

Az örökölt hitelesítési protokollok fokozott kockázata miatt a Microsoft azt javasolja, hogy a szervezetek ezen protokollok használatával blokkolják a hitelesítési kérelmeket, és modern hitelesítést igényeljenek.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítséget nyújtanak egy feltételes hozzáférési szabályzat létrehozásához az örökölt hitelesítési kérelmek blokkolásához. Ezt a házirendet csak a [jelentések üzemmódba](howto-conditional-access-report-only.md) helyezheti el, így a rendszergazdák meghatározhatják, hogy milyen hatással lesznek a meglévő felhasználókra. Ha a rendszergazdák kényelmesek, hogy a házirend a kívánt módon érvényes, **akkor a telepítésre vagy az** üzembe helyezésre adott csoportok hozzáadásával, illetve mások kizárásával válthatnak.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését**.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **felhasználók és csoportok** lehetőséget, és válassza ki azokat a fiókokat, amelyeknek fenn kell tartaniuk a régi hitelesítés használatát. Zárja ki legalább egy fiókot, hogy elkerülje a zárolás kizárását. Ha nem zárja ki a fiókot, nem fogja tudni létrehozni ezt a házirendet.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Cloud apps vagy műveletek**területen válassza a **minden felhőalapú alkalmazás**lehetőséget.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **feltételek** > **ügyfélalkalmazások (előzetes verzió)** területen állítsa az **Igen**értékre a **konfigurálást** .
   1. Csak a **Mobile apps és az asztali ügyfelek** > **egyéb ügyfeleinek**bejelölése.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **hozzáférés-vezérlés** > **megadása**területen válassza a **hozzáférés letiltása**lehetőséget.
   1. Válassza a **Kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **házirendet** **csak jelentésre**.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
