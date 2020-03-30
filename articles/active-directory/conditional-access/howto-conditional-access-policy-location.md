---
title: Feltételes hozzáférés – Hozzáférés letiltása hely szerint – Azure Active Directory
description: Egyéni feltételes hozzáférési házirend létrehozása az erőforrásokhoz való hozzáférés IP-hely szerint történő letiltásához
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
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295191"
---
# <a name="conditional-access-block-access-by-location"></a>Feltételes hozzáférés: Hozzáférés letiltása hely szerint

A feltételes hozzáférés helyfeltételével szabályozhatja a felhőalapú alkalmazásokhoz való hozzáférést a felhasználó hálózati helye alapján. A helyfeltétel tágan használható olyan országokból származó hozzáférés letiltására, ahol a szervezet tudja, hogy a forgalom nem származhat.

## <a name="define-locations"></a>Helyek meghatározása

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **–feltételes** > **hozzáféréssel** > **ellátott helyek et.**
1. Válassza az **Új hely lehetőséget.**
1. Adja meg a tartózkodási helyét a nevét.
1. Válassza **az IP-tartományok at,** ha ismeri az adott helyet vagy **országokat/régiókat.**
   1. Adja meg az **IP-tartományokat,** vagy válassza ki a megadott **helyhez az Országok/régiók** lehetőséget.
      * Ha az Országok/régiók lehetőséget választja, választható, hogy ismeretlen területeket is felszeretne tüntetni.
1. **Mentés** kiválasztása

További információ a helyfeltétel feltételes hozzáférés megtalálható a cikkben, [Mi a hely feltétele az Azure Active Directory feltételes hozzáférés](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások vagy műveletek** > közé tartozik a Include (**Felhőalapú alkalmazások)** csoportban válassza az **Összes felhőalapú alkalmazás**lehetőséget, és válassza a **Kész**lehetőséget.
1. **Feltételek szerint** > **hely**.
   1. A **Konfigurálás** beállítása **Igen-re**
   1. **Kijelölt** **helyek** felvétele
   1. Válassza ki a szervezethez létrehozott letiltott helyet.
   1. Kattintson a**Kész** >  **kijelölése** > **gombra.**
1. A **Feltételek** > **az ügyfélalkalmazások (előzetes verzió)** csoportban állítsa a **Konfigurálás** beállítást **Igen**értékre, és válassza a **Kész gombot.**
1. Az **Access vezérlői blokk** > **csoportban**válassza **a Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A létrehozás gombra a házirend engedélyezéséhez válassza a **Létrehozás** gombot.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)
