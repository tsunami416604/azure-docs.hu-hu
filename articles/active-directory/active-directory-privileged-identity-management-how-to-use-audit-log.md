---
title: Az auditnapló használata az Azure AD Privileged Identity Management |} A Microsoft Docs
description: 'Útmutató: az auditnapló használata az Azure Privileged Identity Management-bővítményben.'
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7dfd14246622f297a42c6b7975539fec2882bbe0
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448169"
---
# <a name="using-the-audit-log-in-pim"></a>A PIM az auditnapló használata
A Privileged Identity Management (PIM) napló segítségével tekintse meg a felhasználó-hozzárendelés és aktiválás egy adott időtartamon belül. Ha meg szeretné tekinteni a teljes naplózási előzmények tevékenység a saját bérlőjében, beleértve a rendszergazda, a végfelhasználó és a szinkronizálási tevékenység, használhatja a [Azure Active Directory hozzáférési és használati jelentéseket.](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>Keresse meg a napló
Az a [az Azure portal](https://portal.azure.com) irányítópulton válassza a **Azure AD Privileged Identity Management** alkalmazást. Itt kattintva férhet hozzá a felügyeleti napló **kiemelt szerepkörök kezelése** > **naplózási előzmények** a PIM-irányítópulton.

## <a name="the-audit-log-graph"></a>Az auditálási napló graph
A felügyeleti napló segítségével megtekintheti az összes aktiválás maximális aktiválások száma / nap és átlagos aktiválások száma / nap vonaldiagramon.  Szűrhet emellett az adatok szerepkör Ha egynél több szerepkört a naplózási előzmények.

Használja a **idő**, **művelet**, és **szerepkör** gombok segítségével rendezze a napló.

## <a name="the-audit-log-list"></a>A vizsgálati naplók listája
A naplózási log listában az oszlopok a következők:

* **A kérelmező** – a felhasználó, aki kérte a szerepkör aktiválása vagy módosítása.  "Az Azure rendszer" érték esetén a az Azure naplózási naplóban további információt.
* **Felhasználói** -aktivál, vagy egy olyan szerepkörhöz hozzárendelt felhasználó.
* **Szerepkör** -a szerepkör hozzárendelése, vagy a felhasználó aktiválása.
* **A művelet** – a kérelmező által végrehajtott műveletek. Ez magában foglalhatja hozzárendelés, ügyfelek, aktiválása vagy deaktiválása.
* **Idő** – Ha a művelet történt.
* **Mintafelismerési** – Ha a szöveg az aktiválás során megadott az oka mező, azt itt fognak megjelenni.
* **Lejárati** – csak akkor érvényes, a szerepkörök aktiválásához.

## <a name="filter-the-audit-log"></a>Az auditnapló szűrése
Az adatokat, hogy megjelenik-e a felügyeleti napló kattintva szűrheti a **szűrő** gombra.  A **frissítés diagram paraméterei panelen** jelenik meg.

Miután beállította a szűrőket, kattintson a **frissítés** a naplóban lévő adatok szűrése.  Ha az adatok nem jelennek meg azonnal, frissítse az oldalt.

### <a name="change-the-date-range"></a>A dátumtartomány módosítása
Használja a **Ma**, **múlt héten**, **elmúlt hónap**, vagy **egyéni** gombokkal módosíthatja az időtartományt, naplók.

Ha úgy dönt a **egyéni** gomb, egyúttal egy **a** dátum mező és a egy **való** dátum mezőben adja meg a napló dátumtartományt.  Adja meg a dátumot hh/nn/éééé formátumban, vagy kattintson a a **naptár** ikonra, és válassza ki a dátumot egy naptárból.

### <a name="change-the-roles-included-in-the-log"></a>A szerepkörök a naplóban szereplő módosítása
Jelölje be vagy a **szerepkör** vagy kizárja a a naplóból minden szerepkör melletti jelölőnégyzetet.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

