---
title: Hozzáférési csomagra vonatkozó kérelmek megtekintése és kezelése az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan tekintheti meg, újradolgozhatja és szakíthatja meg a hozzáférési csomagra vonatkozó kérelmeket Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
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
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430269"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Hozzáférési csomagra vonatkozó kérelmek megtekintése és kezelése az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure AD-jogosultságok kezelése szolgáltatásban megtekintheti, hogy ki kért hozzáférési csomagokat, a szabályzatot és az állapotot. Ez a cikk a hozzáférési csomagokra vonatkozó kérelmek megtekintését, újrafeldolgozását és megszakítását ismerteti.

## <a name="view-requests"></a>Kérelmek megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. A további részletek megtekintéséhez kattintson egy adott kérésre.

    ![Hozzáférési csomagra vonatkozó kérelmek listája](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>Kérelem kézbesítési hibáinak megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Válassza ki a megtekinteni kívánt kérelmet.

    Ha a kérelem kézbesítési hibával rendelkezik, a kérelem állapota nem **kézbesítve** vagy **részben kézbesítve**lesz.

    Ha kézbesítési hibák merülnek fel, a kérelem részleteit tartalmazó ablaktáblán a kézbesítési hibák száma jelenik meg.

1. Kattintson a számra az összes kérelem kézbesítési hibájának megjelenítéséhez.

## <a name="reprocess-a-request"></a>Kérelem újrafeldolgozása

Ha egy kérelem hibát észlel, újra feldolgozhatja a kérelmet, hogy újra megpróbálja. Csak olyan kérelmeket lehet újra feldolgozni, amelyeknek a **kézbesítési állapota nem sikerült** , vagy **részlegesen kézbesítve** lett, és egy héttel rövidebb befejezési dátummal rendelkezik.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Kattintson az újradolgozni kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **kérelem újrafeldolgozása**elemre.

    ![Sikertelen kérelem újrafeldolgozása](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Csak olyan függőben lévő kérést lehet megszakítani, amely még nem lett kézbesítve, vagy a kézbesítés sikertelen volt.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Kattintson a megszakítani kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **kérelem megszakítása**elemre.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomag kérelem-és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md)
- [Hozzáférési csomag hozzárendeléseinek megtekintése, hozzáadása és eltávolítása](entitlement-management-access-package-assignments.md)