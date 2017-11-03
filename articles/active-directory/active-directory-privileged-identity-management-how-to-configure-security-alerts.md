---
title: "Biztonsági riasztások konfigurálása |} Microsoft Docs"
description: "Útmutató: Azure Privileged Identity Management bővítmény biztonsági riasztások konfigurálása."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4e0c911a-36c6-42a0-8f79-a01c03d2d04f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: e057120e31eeebc3da274536c09d6d9972854338
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management biztonsági riasztások konfigurálása
## <a name="security-alerts"></a>Biztonsági riasztások
Azure Privileged Identity Management (PIM) riasztásokat állít elő, ha nem biztonságos vagy gyanús tevékenységek a környezetben. Riasztást vált ki, ha azt mutatja a PIM irányítópult. Jelölje ki a riasztást, a felhasználók vagy szerepkörök a riasztást kiváltó felsoroló jelentés megtekintéséhez.

![A PIM irányítópult biztonsági riasztások – képernyőkép][1]

| Riasztás | Eseményindító | Ajánlás |
| --- | --- | --- |
| **Szerepkörök hozzárendeli PIM-en kívül** |A rendszergazda véglegesen rendelt szerepkör, a PIM felületen kívülre. |Tekintse át az új szerepkör-hozzárendelés. Egyéb szolgáltatások csak rendelhet hozzá az állandó rendszergazdák, mert azt egy jogosult hozzárendeléshez szükség esetén módosítsa. |
| **Szerepkörök aktiválása túl gyakran van folyamatban** |A beállításokban megadott időn belül az azonos szerepkör túl sok újraaktiválás volt. |Lépjen kapcsolatba a felhasználóval, hogy miért ezek aktiválta a szerepkör annyi alkalommal. Lehet, hogy az időkorlát értéke túl rövid a feladataikat befejeződnek, vagy lehet, hogy ezek segítségével parancsfájlok automatikusan aktiválja a szerepkört. |
| **Szerepkörök az aktiváláshoz nincs szükség a multi-factor authentication** |A beállítások engedélyezve van az MFA nélkül szerepkör is létezik. |Azt a legtöbb kiemelt jogosultságokkal rendelkező szerepkörök megkövetelő, de erősen ösztönözzék a többtényezős hitelesítés engedélyezése a szerepkörök aktiválásához. |
| **A rendszergazdák a kiemelt szerepköröket a nem használt.** |Nincsenek jogosult rendszergazdák, amely még nem aktiválta a közelmúltban szerepük. |A felhasználók többé nem kell hozzáférést, hogy egy hozzáférés-ellenőrzés indítása. |
| **Nincsenek a túl sok globális rendszergazda** |Nincsenek további globális rendszergazdák mint ajánlott. |Ha nagyszámú globális rendszergazdák, valószínű, hogy a felhasználók kihozhatják több engedélyt van szükségük. Felhasználók áthelyezése kevesebb kiemelt szerepköröket, vagy ellenőrizze némelyikük abban az esetben jogosult a szerepkör ahelyett, hogy tartósan hozzárendelt. |

## <a name="configure-security-alert-settings"></a>Biztonsági riasztási beállításainak konfigurálása
Testre szabhatja a biztonsági riasztásokat a környezet és a biztonsági célok PIM némelyike. Kövesse az alábbi lépéseket a beállítások panelről eléréséhez:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) válassza ki a **Azure AD Privileged Identity Management** csempére az irányítópulton.
2. Válassza ki **kiemelt szerepköröket felügyelt** > **beállítások** > **beállításokat riasztások**.
   
    ![Navigáljon a biztonsági riasztások beállításainak][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>"Szerepkörök vannak aktiválásának túl gyakran" riasztás
Ez a riasztás akkor váltja ki, ha egy felhasználó aktiválja a ugyanarra a kiemelt szerepkörre meghatározott időn belül többször. Beállíthatja, hogy az adott időszakban, mind az aktiválás számát.

* **Az aktiválás megújítása időkeretre**: Adja meg a nap, óra, perc, és az adott időszakban segítségével nyomon követheti a gyanús megújításokat kívánt második.
* **Aktiválási megújításának száma**: Adja meg az aktiválás 2 100, a választott időkereten belül, riasztás worthy figyelembe venni a számát. A beállítás módosítható a csúszka, vagy írjon be egy értéket a szövegmezőben.

### <a name="there-are-too-many-global-administrators-alert"></a>"There are túl sok globális rendszergazdák" riasztás
PIM váltja ki ezt a figyelmeztetést, ha két különböző feltételek teljesülnek, és mindkettő konfigurálhatja. Először egy bizonyos küszöböt, a globális rendszergazdai eléréséhez. Az összes szerepkör-hozzárendelések bizonyos százalékát, globális rendszergazdák kell lennie. Ha csak megfelel egy, a mérési adatok, a figyelmeztetés nem jelenik meg.  

* **Globális rendszergazdák minimális száma**: Adja meg a globális rendszergazdákat, 2 és 100, akkor fontolja meg, hogy egy nem biztonságos meghatározott számát.
* **Globális rendszergazdák aránya**: Adja meg a rendszergazdák és globális rendszergazdák aránya, 0-100 %-os, ez az a környezetben nem biztonságos.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>"A rendszergazdák nem használják a kiemelt szerepköröket" riasztás
Ez a riasztás akkor váltja ki, ha egy felhasználó egy bizonyos időn kerül a szerepkör aktiválása nélkül.

* **A napok számát**: Adja meg a 0 és 100, amely a felhasználói szerepkör aktiválása nélkül folytathatja a napok számát.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
