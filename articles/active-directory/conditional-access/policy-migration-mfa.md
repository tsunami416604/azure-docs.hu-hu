---
title: Áttelepíteni a klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon
description: Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6661cee8ba6176bd706d31a10a8f20549e29e4d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894423"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Áttelepíteni a klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon

Ez az oktatóanyag bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében. Bár ez nem előfeltétel, azt javasoljuk, hogy olvasási [az Azure Portalon klasszikus szabályzatok Migrálása](policy-migration.md) a klasszikus szabályzatok áttelepítése előtt.

## <a name="overview"></a>Áttekintés

Ez a cikk a forgatókönyv bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében.

![Azure Active Directory](./media/policy-migration/33.png)

Az áttelepítési folyamat a következő lépéseket tartalmazza:

1. [Nyissa meg a klasszikus szabályzatot](#open-a-classic-policy) beolvasni a konfigurációs beállításokat.
1. Hozzon létre egy új Azure AD feltételes hozzáférési szabályzat a klasszikus szabályzat helyett. 
1. A klasszikus szabályzat letiltása.

## <a name="open-a-classic-policy"></a>Nyissa meg a klasszikus szabályzatot

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs sávon kattintson **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

   ![Feltételes hozzáférés](./media/policy-migration-mfa/02.png)

1. Az a **kezelés** területén kattintson **(előzetes) klasszikus szabályzatok**.

   ![Klasszikus szabályzatok](./media/policy-migration-mfa/12.png)

1. Klasszikus szabályzatok listájában kattintson a szabályzatot, amely megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében.

   ![Klasszikus szabályzatok](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési szabályzat létrehozása

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs sávon kattintson **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

   ![Feltételes hozzáférés](./media/policy-migration/02.png)

1. Az a **feltételes hozzáférési** oldal megnyitásához a **új** oldalon, a felső eszköztáron kattintson **Hozzáadás**.

   ![Feltételes hozzáférés](./media/policy-migration/03.png)

1. Az a **új** lap a **neve** szövegmezőbe írja be a szabályzat nevét.

   ![Feltételes hozzáférés](./media/policy-migration/29.png)

1. Az a **hozzárendelések** területén kattintson **felhasználók és csoportok**.

   ![Feltételes hozzáférés](./media/policy-migration/05.png)

   1. Ha az összes felhasználó lehetőséget jelölte meg a klasszikus szabályzat van, kattintson a **minden felhasználó**. 

   ![Feltételes hozzáférés](./media/policy-migration/35.png)

   1. Ha a klasszikus szabályzatot a kijelölt csoportok, kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki a szükséges felhasználókat és csoportokat.

   ![Feltételes hozzáférés](./media/policy-migration/36.png)

   1. Ha a kizárt csoportok, kattintson a **kizárása** lapra, és válassza ki a szükséges felhasználókat és csoportokat. 

   ![Feltételes hozzáférés](./media/policy-migration/37.png)

1. A a **új** oldal megnyitásához a **Felhőalkalmazások** lap a **hozzárendelés** területén kattintson **Felhőalkalmazások**.

1. Az a **Felhőalkalmazások** lapon, a következő lépésekkel:

   ![Feltételes hozzáférés](./media/policy-migration/08.png)

   1. Kattintson a **alkalmazások kiválasztása**.

   1. Kattintson a **Kiválasztás** gombra.

   1. Az a **kiválasztása** lapon válassza ki a felhőalapú alkalmazás, és kattintson a **kiválasztása**.

   1. Az a **Felhőalkalmazások** kattintson **kész**.

1. Ha rendelkezik **többtényezős hitelesítés megkövetelése** kiválasztott:

   ![Feltételes hozzáférés](./media/policy-migration/26.png)

   1. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

   ![Feltételes hozzáférés](./media/policy-migration/27.png)

   1. Az a **Grant** kattintson **hozzáférést**, és kattintson a **többtényezős hitelesítés megkövetelése**.

   1. Kattintson a **Kiválasztás** gombra.

1. Kattintson a **a** a szabályzat engedélyezéséhez.

   ![Feltételes hozzáférés](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>A klasszikus szabályzat letiltása

A klasszikus szabályzat letiltásához kattintson **letiltása** a a **részletei** megtekintése.

![Klasszikus szabályzatok](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>További lépések

- A klasszikus szabályzat áttelepítése kapcsolatos további információkért lásd: [az Azure Portalon klasszikus szabályzatok Migrálása](policy-migration.md).
- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).
- Ha kész feltételes hozzáférési szabályzatokat konfigurálni a környezetében, tekintse át [az Azure Active Directory feltételes hozzáféréssel kapcsolatos ajánlott eljárásait](best-practices.md).
