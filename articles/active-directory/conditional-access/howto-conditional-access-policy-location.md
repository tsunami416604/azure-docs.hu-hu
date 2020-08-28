---
title: Feltételes hozzáférés – hozzáférés tiltása hely alapján – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az erőforrásokhoz való hozzáférés blokkolására IP-hely alapján
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a531692264a768e4f6cb8e6475807789df049d03
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049146"
---
# <a name="conditional-access-block-access-by-location"></a>Feltételes hozzáférés: hozzáférés letiltása hely szerint

A feltételes hozzáférés helyének feltételével a felhasználó hálózati helye alapján vezérelheti a felhőalapú alkalmazásokhoz való hozzáférést. A hely feltételét általában arra használják, hogy letiltsa a hozzáférést olyan országokból/régiókból, ahol a szervezet tudja, hogy a forgalom nem származik.

## <a name="define-locations"></a>Telephelyek definiálása

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés**  >  **elnevezett helyeinek nevét**.
1. Válassza az **új hely**lehetőséget.
1. Adjon nevet a helynek.
1. Válassza az **IP-címtartományok** lehetőséget, ha ismeri az adott helyet vagy **országokat vagy régiókat**alkotó, külsőleg elérhető IPv4-címtartományt.
   1. Adja meg az **IP-tartományokat** , vagy válassza ki a megadott hely **országait/régióit** .
      * Ha az országok/régiók lehetőséget választja, dönthet úgy, hogy ismeretlen területeket is tartalmaz.
1. **Mentés** kiválasztása

A feltételes hozzáférés hely feltételével kapcsolatos további információkért tekintse meg a cikk a [feltételes hozzáférés Azure Active Directory a hely feltételeit](location-condition.md) ismertető cikket.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
1. A **Cloud apps vagy a műveletek**területen  >  válassza a **minden felhőalapú alkalmazás**lehetőséget.**Include**
1. A **feltételek**  >  **helye**alatt.
   1. **Konfigurálás** beállítása **Igen** értékre
   1. A **Belefoglalás**területen válassza a **kiválasztott helyszínek** elemet.
   1. Válassza ki a szervezet számára létrehozott tiltott helyet.
   1. Kattintson a **Kiválasztás** elemre.
1. A **hozzáférés-vezérlés** > válassza a **hozzáférés letiltása**lehetőséget, majd válassza a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Feltételes hozzáférési szabályzat létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
