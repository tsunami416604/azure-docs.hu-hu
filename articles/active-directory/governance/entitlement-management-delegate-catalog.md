---
title: Hozzáférés-vezérlés delegálása az Azure AD-jogosultságok kezelése szolgáltatásban az alkotók katalógusához – Azure Active Directory
description: Megtudhatja, hogyan delegálhat hozzáférési szabályozást a rendszergazdáktól az alkotók és a projektmenedzserek számára, hogy azok képesek legyenek a hozzáférés kezelésére.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f71007b886d3cc25a7cf9dc23d784144ed4e1fbd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174376"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Hozzáférés-vezérlés delegálása az Azure AD-jogosultságok kezelésében az alkotók katalógusában

Ha olyan felhasználók számára kíván delegálni, akik nem rendszergazdák, így saját katalógusokat hozhatnak létre, ezeket a felhasználókat hozzáadhatja az Azure AD jogosultságok kezelése által definiált katalógus-létrehozó szerepkörhöz. Hozzáadhat egyéni felhasználókat, vagy hozzáadhat egy csoportot is, amelynek tagjai ezután létrehozhatnak katalógusokat.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Rendszergazdaként delegált katalógus létrehozója

Kövesse az alábbi lépéseket egy felhasználó hozzárendeléséhez a katalógus-létrehozó szerepkörhöz.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben, a **jogosultságok kezelése** szakaszban kattintson a **Beállítások**elemre.

1. Kattintson a **Szerkesztés** gombra.

    ![Katalógus-készítők hozzáadására szolgáló beállítások](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. A jogosultság-kezelés **delegálása** szakaszban kattintson a **katalógus-létrehozók hozzáadása** lehetőségre azon felhasználók vagy csoportok kiválasztásához, akikre a jogosultsági felügyeleti szerepkört delegálni kívánja.

1. Kattintson a **Kiválasztás** gombra.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>A Azure Portal elérésének engedélyezése a delegált szerepköröknek

Ha engedélyezni szeretné a delegált szerepköröket, például a katalógus-létrehozókat és a hozzáférési csomag kezelőit, a hozzáférési csomagok kezeléséhez tekintse meg a Azure Portal a felügyeleti portál beállításban.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. A Azure Portal kattintson a **Azure Active Directory** , majd a **felhasználók**elemre.

1. A bal oldali menüben kattintson a **felhasználói beállítások**elemre.

1. Győződjön meg arról, hogy az **Azure ad felügyeleti portálhoz való hozzáférés korlátozása** **nem**értékre van állítva.

    ![Azure AD felhasználói beállítások – felügyeleti portál](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Következő lépések

- [Erőforrások katalógusának létrehozása és kezelése](entitlement-management-catalog-create.md)
- [Hozzáférési szabályozás delegálása a csomagkezelő-kezelők eléréséhez](entitlement-management-delegate-managers.md)

