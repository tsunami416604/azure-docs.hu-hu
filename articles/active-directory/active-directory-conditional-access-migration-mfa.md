---
title: Áttelepíteni a klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, az Azure AD feltételes hozzáférés, biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e3a4d751c99292c55da9af70998b6f07a0e7914f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523818"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Áttelepíteni a klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon 

Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében. Bár ez nem előfeltétel, azt javasoljuk, hogy olvasási [az Azure Portalon klasszikus szabályzatok Migrálása](active-directory-conditional-access-migration.md) a klasszikus szabályzatok áttelepítése előtt.


 
## <a name="overview"></a>Áttekintés 

Ez a cikk a forgatókönyv bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


Az áttelepítési folyamat a következő lépéseket tartalmazza:

1. [Nyissa meg a klasszikus szabályzatot](#open-a-classic-policy) beolvasni a konfigurációs beállításokat.
2. Hozzon létre egy új Azure AD feltételes hozzáférési szabályzat a klasszikus szabályzat helyett. 
3. A klasszikus szabályzat letiltása.



## <a name="open-a-classic-policy"></a>Nyissa meg a klasszikus szabályzatot

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs sávon kattintson **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration-mfa/02.png)

3. Az a **kezelés** területén kattintson **(előzetes) klasszikus szabályzatok**.

    ![Klasszikus szabályzatok](./media/active-directory-conditional-access-migration-mfa/12.png)

4. Klasszikus szabályzatok listájában kattintson a szabályzatot, amely megköveteli **a multi-factor authentication** felhőalapú alkalmazás esetében.

    ![Klasszikus szabályzatok](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési szabályzat létrehozása


1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs sávon kattintson **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/02.png)



3. Az a **feltételes hozzáférési** oldal megnyitásához a **új** oldalon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/03.png)

4. Az a **új** lap a **neve** szövegmezőbe írja be a szabályzat nevét.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/29.png)

5. Az a **hozzárendelések** területén kattintson **felhasználók és csoportok**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/05.png)

    a. Ha az összes felhasználó lehetőséget jelölte meg a klasszikus szabályzat van, kattintson a **minden felhasználó**. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/35.png)

    b. Ha a klasszikus szabályzatot a kijelölt csoportok, kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki a szükséges felhasználókat és csoportokat.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/36.png)

    c. Ha a kizárt csoportok, kattintson a **kizárása** lapra, és válassza ki a szükséges felhasználókat és csoportokat. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/37.png)

6. A a **új** oldal megnyitásához a **Felhőalkalmazások** lap a **hozzárendelés** területén kattintson **Felhőalkalmazások**.

8. Az a **Felhőalkalmazások** lapon, a következő lépésekkel:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/08.png)

    a. Kattintson a **alkalmazások kiválasztása**.

    b. Kattintson a **Kiválasztás** gombra.

    c. Az a **kiválasztása** lapon válassza ki a felhőalapú alkalmazás, és kattintson a **kiválasztása**.

    d. Az a **Felhőalkalmazások** kattintson **kész**.



9. Ha rendelkezik **többtényezős hitelesítés megkövetelése** kiválasztott:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/26.png)

    a. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/27.png)

    b. Az a **Grant** kattintson **hozzáférést**, és kattintson a **többtényezős hitelesítés megkövetelése**.

    c. Kattintson a **Kiválasztás** gombra.


10. Kattintson a **a** a szabályzat engedélyezéséhez.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>A klasszikus szabályzat letiltása

A klasszikus szabályzat letiltásához kattintson **letiltása** a a **részletei** megtekintése.

![Klasszikus szabályzatok](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>További lépések

- A klasszikus szabályzat áttelepítése kapcsolatos további információkért lásd: [az Azure Portalon klasszikus szabályzatok Migrálása](active-directory-conditional-access-migration.md).


- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](conditional-access/app-based-mfa.md).

- Ha készen áll a környezetre vonatkozó feltételes hozzáférési szabályzatok konfigurálására, tekintse meg a [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](conditional-access/best-practices.md). 
