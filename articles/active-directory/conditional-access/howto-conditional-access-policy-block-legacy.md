---
title: Feltételes hozzáférés – Örökölt hitelesítés blokkolása – Azure Active Directory
description: Egyéni feltételes hozzáférési házirend létrehozása az örökölt hitelesítési protokollok blokkolására
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295219"
---
# <a name="conditional-access-block-legacy-authentication"></a>Feltételes hozzáférés: Az örökölt hitelesítés blokkolása

Az örökölt hitelesítési protokollokkal kapcsolatos megnövekedett kockázat miatt a Microsoft azt javasolja, hogy a szervezetek tiltsák le a hitelesítési kérelmeket ezekkel a protokollokkal, és modern hitelesítést igényeljenek.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

A következő lépések segítségével hozzon létre egy feltételes hozzáférési szabályzatot az örökölt hitelesítési kérelmek blokkolása érdekében. Ez a házirend [a csak jelentés módban](howto-conditional-access-report-only.md) indul, így a rendszergazdák meg tudják határozni, hogy milyen hatással lesznek a meglévő felhasználókra. Ha a rendszergazdák számára kényelmes, hogy a házirend a kívánt módon vonatkozik, akkor átválthatnak **a Be** vagy a szakasza a központi telepítés hozzáadásával adott csoportok és mások kizárásával.

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás csoportban**válassza a **Felhasználók és csoportok** lehetőséget, és válassza ki azokat a fiókokat, amelyeknek fenn kell tartaniuk az örökölt hitelesítés használatát. Legalább egy fiókot kizárhat, hogy ne zárjon ki. Ha nem zár ki egyetlen fiókot sem, akkor nem fogja tudni létrehozni ezt a házirendet.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalkalmazások és -műveletek csoportban**válassza a **Minden felhőalapú alkalmazás lehetőséget.**
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Feltételek** > **az ügyfélalkalmazások (előzetes verzió)** csoportban állítsa **a Konfigurálás** beállítást **Igen**értékre.
   1. Jelölje be csak a **mobilalkalmazások és asztali ügyfelek** > **jelölőnégyzetet Egyéb ügyfelek**.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférés blokkolása**lehetőséget.
   1. Válassza a **Kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be a **Házirend engedélyezése** **csak jelentést.**
1. A létrehozás gombra a házirend engedélyezéséhez válassza a **Létrehozás** gombot.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)
