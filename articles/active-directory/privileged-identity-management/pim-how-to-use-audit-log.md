---
title: A PIM az Azure AD-címtárbeli szerepkörök naplózási előzmények megtekintése |} A Microsoft Docs
description: Ismerje meg az Azure AD-címtárbeli szerepkörökhöz tartozó naplózási előzmények megtekintése az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ab5a072d845bfdbaafabe1e0e7bdce2dfce6184d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188179"
---
# <a name="view-audit-history-for-azure-ad-directory-roles-in-pim"></a>A PIM az Azure AD-címtárbeli szerepkörök naplózási előzmények megtekintése
A Privileged Identity Management (PIM) naplózási előzmények segítségével tekintse meg a felhasználó-hozzárendelés és aktiválás minden a kiemelt szerepkörökhöz tartozó megadott időtartamon belül. Ha meg szeretné tekinteni a teljes naplózási előzmények tevékenység a saját bérlőjében, beleértve a rendszergazda, a végfelhasználó és a szinkronizálási tevékenység, használhatja a [Azure Active Directory hozzáférési és használati jelentéseket.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>Keresse meg a naplózási előzmények
Az a [az Azure portal](https://portal.azure.com) irányítópulton válassza a **Azure AD Privileged Identity Management** alkalmazást. Itt kattintva férhet hozzá a naplózási előzmények **kiemelt szerepkörök kezelése** > **naplózási előzmények** a PIM-irányítópulton.

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Rendezze az adatokat a művelet, és keressen az "Aktiválás Approved"


## <a name="audit-history-graph"></a>Naplózási előzmények diagram
A naplózási előzmények használatával megtekintheti az összes aktiválás maximális aktiválások száma / nap és átlagos aktiválások száma / nap vonaldiagramon.  Szűrhet emellett az adatok szerepkör Ha egynél több szerepkört a naplózási előzmények.

Használja a **idő**, **művelet**, és **szerepkör** gombok segítségével rendezze az előzményeket.

## <a name="audit-history-list"></a>Naplózási előzmények listája
A naplózási Előzmények listában az oszlopok a következők:

* **A kérelmező** – a felhasználó, aki kérte a szerepkör aktiválása vagy módosítása.  Ha az érték "Azure rendszer", ellenőrizze a további információ az Azure naplózási előzmények.
* **Felhasználói** -aktivál, vagy egy olyan szerepkörhöz hozzárendelt felhasználó.
* **Szerepkör** -a szerepkör hozzárendelése, vagy a felhasználó aktiválása.
* **A művelet** – a kérelmező által végrehajtott műveletek. Ez magában foglalhatja hozzárendelés, ügyfelek, aktiválása vagy deaktiválása.
* **Idő** – Ha a művelet történt.
* **Mintafelismerési** – Ha a szöveg az aktiválás során megadott az oka mező, azt itt fognak megjelenni.
* **Lejárati** – csak akkor érvényes, a szerepkörök aktiválásához.

## <a name="filter-audit-history"></a>Szűrő naplózási előzmények
Az adatokat, hogy megjelenik-e a naplózási előzmények kattintva szűrheti a **szűrő** gombra.  A **frissítés diagram paraméterei panelen** jelenik meg.

Miután beállította a szűrőket, kattintson a **frissítés** , szűrje az adatokat az előzmények.  Ha az adatok nem jelennek meg azonnal, frissítse az oldalt.

### <a name="change-the-date-range"></a>A dátumtartomány módosítása
Használja a **Ma**, **múlt héten**, **elmúlt hónap**, vagy **egyéni** gombokkal módosíthatja az időtartományt, a naplózási előzmények.

Ha úgy dönt a **egyéni** gomb, egyúttal egy **a** dátum mező és a egy **való** dátum mezőben adja meg a dátum az előzmények.  Adja meg a dátumot hh/nn/éééé formátumban, vagy kattintson a a **naptár** ikonra, és válassza ki a dátumot egy naptárból.

### <a name="change-the-roles-included-in-the-history"></a>Az előzményekben található szerepköreinek módosítása
Jelölje be vagy a **szerepkör** egyes szerepkörök vagy kizárja a az előzményekből melletti jelölőnégyzetet.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrások szerepköreihez tartozó naplózási előzmények megtekintése](pim-resource-roles-use-the-audit-log.md)
