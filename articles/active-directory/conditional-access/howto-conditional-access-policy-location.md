---
title: Feltételes hozzáférés – hozzáférés tiltása hely alapján – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az erőforrásokhoz való hozzáférés blokkolására IP-hely alapján
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204026825a5132464e1adda611227db2ac7cf91f
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543987"
---
# <a name="conditional-access-block-access-by-location"></a>Feltételes hozzáférés: hozzáférés letiltása hely szerint

A feltételes hozzáférés helyének feltételével a felhasználó hálózati helye alapján vezérelheti a felhőalapú alkalmazásokhoz való hozzáférést. A hely feltételét általában arra használják, hogy blokkolja azokat az országokat, ahol a szervezet tudja, hogy a forgalom nem származik.

## <a name="define-locations"></a>Telephelyek definiálása

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > a **feltételes hozzáférés** > **elnevezett helyein**.
1. Válassza az **új hely**lehetőséget.
1. Adjon nevet a helynek.
1. Válassza az **IP-címtartományok** lehetőséget, ha ismeri az adott helyet vagy **országokat vagy régiókat**alkotó, külsőleg elérhető IPv4-címtartományt.
   1. Adja meg az **IP-tartományokat** , vagy válassza ki a megadott hely **országait/régióit** .
      * Ha az országok/régiók lehetőséget választja, dönthet úgy, hogy ismeretlen területeket is tartalmaz.
1. Válassza a **Mentés** elemet.

A feltételes hozzáférés hely feltételével kapcsolatos további információkért tekintse meg a cikk a [feltételes hozzáférés Azure Active Directory a hely feltételeit](location-condition.md) ismertető cikket.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **felhőalapú alkalmazások vagy műveletek** > a **következők**közül válassza a **minden felhőalapú alkalmazás**lehetőséget, majd válassza a **kész**lehetőséget.
1. A **feltételek** > **helyen**.
   1. **Konfigurálás** beállítása **Igen** értékre
   1. **Kiválasztott helyszínek** kijelölése
   1. Válassza ki a szervezet számára létrehozott tiltott helyet.
   1. Kattintson a **kiválasztás** > **kész** > **kész**lehetőségre.
1. A **hozzáférés-vezérlés** > **blokk**területen válassza a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
