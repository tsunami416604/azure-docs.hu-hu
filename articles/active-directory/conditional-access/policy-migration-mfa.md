---
title: Feltételes hozzáférési szabályzatok áttelepíteni – Azure Active Directory
description: Ez a cikk bemutatja, hogyan telepítheti át a többtényezős hitelesítést igénylő klasszikus szabályzatot a Azure Portalban.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca00ae62ba114aecef48117fd8a54b7f2e962dfd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380324"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>A többtényezős hitelesítést igénylő klasszikus szabályzat áttelepítését a Azure Portal

Ez az oktatóanyag bemutatja, hogyan telepíthet át egy klasszikus szabályzatot, amely megköveteli a **többtényezős hitelesítést** a felhőalapú alkalmazásokhoz. Bár ez nem előfeltétel, javasoljuk, hogy olvassa el a klasszikus szabályzatok áttelepítése [a Azure Portalt](policy-migration.md) , mielőtt megkezdené a klasszikus házirendek áttelepítését.

## <a name="overview"></a>Áttekintés

A jelen cikkben ismertetett forgatókönyv bemutatja, hogyan telepíthet át egy olyan klasszikus szabályzatot, amely **többtényezős hitelesítést** igényel a felhőalapú alkalmazásokhoz.

![Azure Active Directory](./media/policy-migration/33.png)

Az áttelepítési folyamat a következő lépésekből áll:

1. A konfigurációs beállítások beszerzéséhez [nyissa meg a klasszikus szabályzatot](#open-a-classic-policy) .
1. Hozzon létre egy új Azure AD feltételes hozzáférési szabályzatot a klasszikus szabályzat cseréjéhez. 
1. Tiltsa le a klasszikus szabályzatot.

## <a name="open-a-classic-policy"></a>Klasszikus szabályzat megnyitása

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. A **Azure Active Directory** lap **kezelés** szakaszában kattintson a **feltételes hozzáférés**elemre.

   ![Feltételes hozzáférés](./media/policy-migration-mfa/02.png)

1. A **kezelés** szakaszban kattintson a **klasszikus házirendek (előzetes verzió)** elemre.

   ![Klasszikus szabályzatok](./media/policy-migration-mfa/12.png)

1. A klasszikus szabályzatok listájában kattintson arra a házirendre, amely megköveteli a **többtényezős hitelesítést** a felhőalapú alkalmazásokhoz.

   ![Klasszikus szabályzatok](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési szabályzat létrehozása

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. A **Azure Active Directory** lap **kezelés** szakaszában kattintson a **feltételes hozzáférés**elemre.

   ![Feltételes hozzáférés](./media/policy-migration/02.png)

1. A **feltételes hozzáférés** lapon az **új** lap megnyitásához kattintson a felső eszköztáron a **Hozzáadás**gombra.

   ![Feltételes hozzáférés](./media/policy-migration/03.png)

1. Az **új** lap **név** szövegmezőbe írja be a szabályzat nevét.

   ![Feltételes hozzáférés](./media/policy-migration/29.png)

1. A **hozzárendelések** szakaszban kattintson a **felhasználók és csoportok**elemre.

   ![Feltételes hozzáférés](./media/policy-migration/05.png)

   1. Ha a klasszikus szabályzatban minden felhasználó van kiválasztva, kattintson a **minden felhasználó**elemre. 

      ![Feltételes hozzáférés](./media/policy-migration/35.png)

   1. Ha a klasszikus szabályzatban csoportok vannak kiválasztva, kattintson a **felhasználók és csoportok kiválasztása**elemre, majd válassza ki a szükséges felhasználókat és csoportokat.

      ![Feltételes hozzáférés](./media/policy-migration/36.png)

   1. Ha a kizárt csoportok vannak, kattintson a **kizárás** lapra, majd válassza ki a szükséges felhasználókat és csoportokat. 

      ![Feltételes hozzáférés](./media/policy-migration/37.png)

1. Az **új** lapon a **Cloud apps** lap megnyitásához kattintson a **hozzárendelés** szakaszban a **Cloud apps**elemre.
1. A **Cloud apps** oldalon hajtsa végre a következő lépéseket:
   1. Kattintson az **alkalmazások kiválasztása**elemre.
   1. Kattintson a **Kiválasztás** gombra.
   1. A **kiválasztás** lapon válassza ki a felhőalapú alkalmazást, majd kattintson a **kiválasztás**elemre.
   1. A **Cloud apps** lapon kattintson a **kész**gombra.
1. Ha a **többtényezős hitelesítés** van kiválasztva:

   ![Feltételes hozzáférés](./media/policy-migration/26.png)

   1. A **hozzáférés-vezérlések** szakaszban kattintson a **Grant (Engedélyezés**) elemre.

      ![Feltételes hozzáférés](./media/policy-migration/27.png)

   1. A **támogatás** lapon kattintson a **hozzáférés engedélyezése**elemre, majd kattintson a **többtényezős hitelesítés megkövetelése**lehetőségre.
   1. Kattintson a **Kiválasztás** gombra.
1. A házirend engedélyezéséhez kattintson **a be** gombra.

   ![Feltételes hozzáférés](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>A klasszikus szabályzat letiltása

A klasszikus szabályzat letiltásához kattintson a **Letiltás** gombra a **részletek** nézetben.

![Klasszikus szabályzatok](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>További lépések

- A klasszikus házirendek áttelepítésével kapcsolatos további információkért lásd: [klasszikus szabályzatok áttelepítése a Azure Portal](policy-migration.md).
- Ha tudni szeretné, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört.
- Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md).
