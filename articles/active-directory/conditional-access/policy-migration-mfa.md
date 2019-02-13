---
title: Áttelepíteni a klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204fec96a58bf1dca55bb0fde14ce6f85c46e05b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185757"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Áttelepíteni a klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon 

Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében. Bár ez nem előfeltétel, azt javasoljuk, hogy olvasási [az Azure Portalon klasszikus szabályzatok Migrálása](policy-migration.md) a klasszikus szabályzatok áttelepítése előtt.


 
## <a name="overview"></a>Áttekintés 

Ez a cikk a forgatókönyv bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében. 

![Azure Active Directory](./media/policy-migration/33.png)


Az áttelepítési folyamat a következő lépéseket tartalmazza:

1. [Nyissa meg a klasszikus szabályzatot](#open-a-classic-policy) beolvasni a konfigurációs beállításokat.
2. Hozzon létre egy új Azure AD feltételes hozzáférési szabályzat a klasszikus szabályzat helyett. 
3. A klasszikus szabályzat letiltása.



## <a name="open-a-classic-policy"></a>Nyissa meg a klasszikus szabályzatot

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs sávon kattintson **Azure Active Directory**.

    ![Azure Active Directory](./media/policy-migration-mfa/01.png)

2. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/policy-migration-mfa/02.png)

3. Az a **kezelés** területén kattintson **(előzetes) klasszikus szabályzatok**.

    ![Klasszikus szabályzatok](./media/policy-migration-mfa/12.png)

4. Klasszikus szabályzatok listájában kattintson a szabályzatot, amely megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében.

    ![Klasszikus szabályzatok](./media/policy-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési szabályzat létrehozása


1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs sávon kattintson **Azure Active Directory**.

    ![Azure Active Directory](./media/policy-migration/01.png)

2. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/policy-migration/02.png)



3. Az a **feltételes hozzáférési** oldal megnyitásához a **új** oldalon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Feltételes hozzáférés](./media/policy-migration/03.png)

4. Az a **új** lap a **neve** szövegmezőbe írja be a szabályzat nevét.

    ![Feltételes hozzáférés](./media/policy-migration/29.png)

5. Az a **hozzárendelések** területén kattintson **felhasználók és csoportok**.

    ![Feltételes hozzáférés](./media/policy-migration/05.png)

    a. Ha az összes felhasználó lehetőséget jelölte meg a klasszikus szabályzat van, kattintson a **minden felhasználó**. 

    ![Feltételes hozzáférés](./media/policy-migration/35.png)

    b. Ha a klasszikus szabályzatot a kijelölt csoportok, kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki a szükséges felhasználókat és csoportokat.

    ![Feltételes hozzáférés](./media/policy-migration/36.png)

    c. Ha a kizárt csoportok, kattintson a **kizárása** lapra, és válassza ki a szükséges felhasználókat és csoportokat. 

    ![Feltételes hozzáférés](./media/policy-migration/37.png)

6. A a **új** oldal megnyitásához a **Felhőalkalmazások** lap a **hozzárendelés** területén kattintson **Felhőalkalmazások**.

8. Az a **Felhőalkalmazások** lapon, a következő lépésekkel:

    ![Feltételes hozzáférés](./media/policy-migration/08.png)

    a. Kattintson a **alkalmazások kiválasztása**.

    b. Kattintson a **Kiválasztás** gombra.

    c. Az a **kiválasztása** lapon válassza ki a felhőalapú alkalmazás, és kattintson a **kiválasztása**.

    d. Az a **Felhőalkalmazások** kattintson **kész**.



9. Ha rendelkezik **többtényezős hitelesítés megkövetelése** kiválasztott:

    ![Feltételes hozzáférés](./media/policy-migration/26.png)

    a. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

    ![Feltételes hozzáférés](./media/policy-migration/27.png)

    b. Az a **Grant** kattintson **hozzáférést**, és kattintson a **többtényezős hitelesítés megkövetelése**.

    c. Kattintson a **Kiválasztás** gombra.


10. Kattintson a **a** a szabályzat engedélyezéséhez.

    ![Feltételes hozzáférés](./media/policy-migration/30.png)



## <a name="disable-the-classic-policy"></a>A klasszikus szabályzat letiltása

A klasszikus szabályzat letiltásához kattintson **letiltása** a a **részletei** megtekintése.

![Klasszikus szabályzatok](./media/policy-migration-mfa/14.png)



## <a name="next-steps"></a>További lépések

- A klasszikus szabályzat áttelepítése kapcsolatos további információkért lásd: [az Azure Portalon klasszikus szabályzatok Migrálása](policy-migration.md).


- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).

- Ha kész feltételes hozzáférési szabályzatokat konfigurálni a környezetében, tekintse át [az Azure Active Directory feltételes hozzáféréssel kapcsolatos ajánlott eljárásait](best-practices.md). 
