---
title: Hozzáférési csomag életciklus-beállításainak módosítása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja a hozzáférési csomag életciklus-beállításait a Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
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
ms.openlocfilehash: 46d63a12443edf04db3570fa43fbd8f20619122e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392344"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Hozzáférési csomag életciklus-beállításainak módosítása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hozzáférés-csomagkezelőként bármikor módosíthatja egy hozzáférési csomag életciklus-beállításait egy meglévő szabályzat szerkesztésével. Ha módosítja egy házirend lejárati dátumát, a függőben lévő jóváhagyás vagy jóváhagyott állapotú kérelmek lejárati dátuma nem változik.

Ez a cikk egy meglévő hozzáférési csomag életciklus-beállításainak módosítását ismerteti.

## <a name="open-lifecycle-settings"></a>Életciklus-beállítások megnyitása

Egy hozzáférési csomag életciklus-beállításainak módosításához meg kell nyitnia a megfelelő szabályzatot. Az alábbi lépéseket követve megnyithatja egy hozzáférési csomag életciklusának beállításait.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** elemre, majd kattintson a szerkeszteni kívánt életciklus-beállításokat tartalmazó házirendre.

    Megnyílik a szabályzat részletei ablaktábla az oldal alján.

    ![Hozzáférési csomag – szabályzat részletei ablaktábla](./media/entitlement-management-shared/policy-details.png)

1. A szabályzat szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Hozzáférési csomag – szabályzat szerkesztése](./media/entitlement-management-shared/policy-edit.png)

1. Az életciklus-beállítások megnyitásához kattintson az **életciklus** fülre.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomag kérelem-és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md)