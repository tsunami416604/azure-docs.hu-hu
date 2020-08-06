---
title: Hozzáférési csomagra vonatkozó kérelem és jóváhagyási beállítások módosítása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja a hozzáférési csomagokra vonatkozó kérelmeket és jóváhagyási beállításokat Azure Active Directory jogosultságok kezelése során.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: edf38013efb14e412fbcd43e06dcf17e61c3bc4a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798766"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomagra vonatkozó kérelem és jóváhagyási beállítások módosítása az Azure AD-jogosultságok kezelésében

Hozzáférés-csomagkezelőként módosíthatja azokat a felhasználókat, akik bármikor igényelhetnek hozzáférési csomagot a szabályzat szerkesztésével vagy új szabályzat hozzáadásával. A jóváhagyási beállításokat is módosíthatja.

Ez a cikk azt ismerteti, hogyan lehet módosítani egy meglévő hozzáférési csomag kérését és jóváhagyási beállításait.

## <a name="choose-between-one-or-multiple-polices"></a>Válasszon egy vagy több rendőr közül

Azt határozza meg, hogy ki kérheti a hozzáférési csomag igénylését egy szabályzattal. Hozzáférési csomag létrehozásakor meg kell adnia a kérés és a jóváhagyás beállítást, amely létrehoz egy szabályzatot. A legtöbb hozzáférési csomag egyetlen házirenddel fog rendelkezni, de egyetlen hozzáférési csomag több házirenddel is rendelkezhet. Hozzon létre több szabályzatot egy hozzáférési csomaghoz, ha engedélyezni szeretné, hogy a különböző felhasználók különböző kérelmeket és jóváhagyási beállításokat kapjanak a hozzárendelésekhez. Például egyetlen házirend nem használható belső és külső felhasználók ugyanahhoz a hozzáférési csomaghoz való hozzárendeléséhez. Ugyanakkor két házirendet is létrehozhat ugyanabban a hozzáférési csomagban – egyet a belső felhasználókhoz, egyet pedig a külső felhasználókhoz. Ha több házirend is érvényes a felhasználóra, a rendszer a kérésük időpontjában kérni fogja, hogy válassza ki azt a szabályzatot, amelyet hozzá szeretne rendelni. Az alábbi ábrán két házirendet tartalmazó hozzáférési csomag látható.

![Több házirend egy hozzáférési csomagban](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Eset | Szabályzatok száma |
| --- | --- |
| Szeretném, hogy a címtárban lévő összes felhasználó ugyanazzal a kéréssel és jóváhagyási beállításokkal rendelkezzen a hozzáférési csomaghoz | Eggyel |
| Azt szeretném, hogy az egyes csatlakoztatott szervezetek összes felhasználója hozzáférhessen egy hozzáférési csomag igényléséhez | Eggyel |
| Szeretném engedélyezni a címtárban lévő felhasználókat és a címtáron kívüli felhasználókat a hozzáférési csomag igényléséhez | Többszörös |
| Különböző jóváhagyási beállításokat szeretnék megadni egyes felhasználók számára | Többszörös |
| Azt szeretném, hogy egyes felhasználók a csomagok hozzárendelései lejárnak, míg más felhasználók is kiterjeszthetik a hozzáférésüket | Többszörös |

További információ a több házirend alkalmazása esetén használt prioritási logikáról: [több szabályzat](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>A kérelem és a jóváhagyási beállítások meglévő házirendjének megnyitása

Egy hozzáférési csomag kérésének és jóváhagyási beállításainak módosításához meg kell nyitnia a megfelelő szabályzatot. Az alábbi lépéseket követve megnyithatja egy hozzáférési csomag kérésének és jóváhagyásának beállításait.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** elemre, majd kattintson a szerkeszteni kívánt szabályzatra.

    Megnyílik a szabályzat részletei ablaktábla az oldal alján.

    ![Hozzáférési csomag – szabályzat részletei ablaktábla](./media/entitlement-management-shared/policy-details.png)

1. A szabályzat szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Hozzáférési csomag – szabályzat szerkesztése](./media/entitlement-management-shared/policy-edit.png)

1. Kattintson a **kérelmek** lapra a kérelem és a jóváhagyás beállításainak megnyitásához.

1. Hajtsa végre a következő kérelmek egyikének lépéseit.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Új szabályzat kérésének és jóváhagyási beállításainak megadása

Ha olyan felhasználói készlettel rendelkezik, amelyeknek különböző kérés-és jóváhagyási beállításokkal kell rendelkezniük, valószínűleg létre kell hoznia egy új szabályzatot. Kövesse az alábbi lépéseket egy új szabályzat meglévő hozzáférési csomaghoz való hozzáadásának megkezdéséhez.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** , majd a **házirend hozzáadása**elemre.

1. Adja meg a szabályzat nevét és leírását.

    ![Szabályzat létrehozása névvel és leírással](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. A **tovább** gombra kattintva nyissa meg a **kérelmek** lapot.

1. Hajtsa végre a következő kérelmek egyikének lépéseit.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Ha szerkeszt egy házirendet, kattintson a **frissítés**gombra. Ha új szabályzatot ad hozzá, kattintson a **Létrehozás**gombra.

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag életciklus-beállításainak módosítása](entitlement-management-access-package-lifecycle-policy.md)
- [Hozzáférési csomagra vonatkozó kérelmek megtekintése](entitlement-management-access-package-requests.md)