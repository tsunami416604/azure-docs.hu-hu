---
title: Azure AD Connect Cloud Sync – véletlen törlések
description: Ez a témakör azt ismerteti, hogyan használható a véletlen törlés funkció a törlés megakadályozására.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613801"
---
# <a name="accidental-delete-prevention"></a>Véletlen törlés megakadályozása

A következő dokumentum a Azure AD Connect Cloud Sync véletlen törlési funkcióját ismerteti.  A véletlen törlés funkció úgy lett kialakítva, hogy megvédje a véletlen konfigurációs változásokat és a helyszíni címtár azon módosításait, amelyek számos felhasználót és csoportot érintenek.  Ez a funkció lehetővé teszi a következőket:

- konfigurálhatja a véletlen törlések automatikus elkerülésének lehetőségét. 
- Állítsa be, hogy a rendszer milyen típusú objektumokat (küszöbértéket) fog érvénybe lépni 
- értesítési e-mail cím beállítása, hogy e-mailben értesítést kapjon, ha a kérdéses szinkronizálási feladatot a rendszer karanténba helyezi az adott forgatókönyv esetében 

Ennek a funkciónak a használatához meg kell határoznia a küszöbértéket azon objektumok számára, amelyek törlését követően a szinkronizálás leáll.  Így ha eléri ezt a számot, a szinkronizálás leáll, és a rendszer értesítést küld a megadott e-mail-címre.  Ez lehetővé teszi, hogy vizsgálja meg, mi történik.


## <a name="configure-accidental-delete-prevention"></a>Véletlen törlés megelőzésének konfigurálása
Az új funkció használatához kövesse az alábbi lépéseket.


1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
2.  Válassza a **Azure ad Connect** lehetőséget.
3.  Válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.
4. A **konfiguráció** területen válassza ki a konfigurációt.
5. A **Beállítások** területen adja meg a következőket:
    - **Értesítő e-mail** – értesítésekhez használt e-mail
    - **Véletlen törlések megakadályozása** – jelölje be ezt a jelölőnégyzetet a funkció engedélyezéséhez.
    - **Véletlen törlési küszöb** – adjon meg több objektumot a szinkronizálási leállítás és az értesítés elindításához

![Véletlen törlések](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Következő lépések 

- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
- [A Azure AD Connect Cloud Sync telepítése](how-to-install.md)
 

