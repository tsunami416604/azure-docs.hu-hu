---
title: Hozzáférési csomag életciklus-beállításainak módosítása az Azure AD jogosultságkezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja egy hozzáférési csomag életciklus-beállításait az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261982"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag életciklus-beállításainak módosítása az Azure AD-jogosultságkezelésben

Access package managerként egy hozzáférési csomag életciklus-beállításait bármikor módosíthatja egy meglévő házirend szerkesztésével. Ha módosítja egy házirend lejárati dátumát, a függőben lévő jóváhagyás vagy jóváhagyott állapotban lévő kérelmek lejárati dátuma nem változik.

Ez a cikk egy meglévő hozzáférési csomag életciklus-beállításainak módosítását ismerteti.

## <a name="open-lifecycle-settings"></a>Életciklus-beállítások megnyitása

Egy hozzáférési csomag életciklus-beállításainak módosításához meg kell nyitnia a megfelelő házirendet. Az alábbi lépésekkel nyissa meg egy hozzáférési csomag életciklus-beállításait.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **Házirendek,** majd a szerkesztni kívánt életciklus-beállításokat tartalmazó házirend re.

    A Házirend részletei ablaktábla a lap alján nyílik meg.

    ![Access csomag – Házirend részletei ablaktábla](./media/entitlement-management-shared/policy-details.png)

1. A házirend szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Access csomag – Házirend szerkesztése](./media/entitlement-management-shared/policy-edit.png)

1. Kattintson az **Életciklus** fülre az életciklus-beállítások megnyitásához.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag kérelem- és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md)