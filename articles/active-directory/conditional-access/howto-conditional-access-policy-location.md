---
title: Feltételes hozzáférés – hozzáférés tiltása hely alapján – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az erőforrásokhoz való hozzáférés blokkolására IP-hely alapján
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64b7add9ddb1734d763c46e93d6788ce21a2c321
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150877"
---
# <a name="conditional-access-block-access-by-location"></a>Feltételes hozzáférés: hozzáférés letiltása hely szerint

A feltételes hozzáférés helyének feltételével a felhasználó hálózati helye alapján vezérelheti a felhőalapú alkalmazásokhoz való hozzáférést. A hely feltételét általában arra használják, hogy blokkolja azokat az országokat, ahol a szervezet tudja, hogy a forgalom nem származik.

## <a name="define-locations"></a>Telephelyek definiálása

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory** > **feltételes hozzáférést**.
1. Válassza az **új hely**lehetőséget.
1. Adjon nevet a helynek.
1. Válassza az **IP-címtartományok** lehetőséget, ha ismeri az adott helyet vagy **országokat vagy régiókat**alkotó, külsőleg elérhető IPv4-címtartományt.
   1. Adja meg az **IP-tartományokat** , vagy válassza ki a megadott hely **országait/régióit** .
      * Ha az országok/régiók lehetőséget választotta, dönthet úgy, hogy ismeretlen területeket is tartalmaz.
1. **Mentés** kiválasztása

A feltételes hozzáférés hely feltételével kapcsolatos további információkért tekintse meg a cikk a [feltételes hozzáférés Azure Active Directory a hely feltételeit](location-condition.md) ismertető cikket.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory** > **feltételes hozzáférést**.
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
   1. Kattintson a **kijelölés** > **kész** > **kész**lehetőségre.
1. A **hozzáférés-vezérlés** > **blokk**alatt válassza a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
