---
title: Telepítse át az Azure portálon többtényezős hitelesítést igénylő klasszikus házirend |} Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus házirendet, amely az Azure portálon többtényezős hitelesítést igényel.
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
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
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 52d4297d2e86ad67672d38f1c527041b813f8d61
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723687"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Az Azure portálon többtényezős hitelesítést igénylő klasszikus házirend áttelepítése 

Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus házirend igénylő **a multi-factor authentication** felhőalapú alkalmazások. Bár ez nem előfeltétel, azt javasoljuk, hogy olvassa el [klasszikus szabályzatokkal az Azure portálon áttelepítése](active-directory-conditional-access-migration.md) a klasszikus házirendek áttelepítése előtt.


 
## <a name="overview"></a>Áttekintés 

Ebben a cikkben a forgatókönyvből megtudhatja, hogyan telepíthet át egy klasszikus házirend igénylő **a multi-factor authentication** felhőalapú alkalmazások. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


Az áttelepítési folyamat a következőket foglalják magukban:

1. [Nyissa meg a klasszikus házirend](#open-a-classic-policy) beolvasni a konfigurációs beállításait.
2. Hozzon létre egy új Azure AD feltételes hozzáférési házirend felülírja a klasszikus házirend. 
3. A klasszikus házirendet.



## <a name="open-a-classic-policy"></a>Nyissa meg a klasszikus házirend

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a bal oldali navigációs sávja **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration-mfa/02.png)

3. Az a **kezelése** kattintson **klasszikus házirendek (előzetes verzió)**.

    ![Klasszikus szabályzatok](./media/active-directory-conditional-access-migration-mfa/12.png)

4. A klasszikus házirendek listájában, kattintson a szabályzatot, amely megköveteli **a multi-factor authentication** felhőalapú alkalmazások.

    ![Klasszikus szabályzatok](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési szabályzat létrehozása


1. Az a [Azure-portálon](https://portal.azure.com), kattintson a bal oldali navigációs sávja **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/02.png)



3. A a **feltételes hozzáférés** lapon nyissa meg a **új** lapon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/03.png)

4. Az a **új** lap a **neve** szövegmező, írja be a házirend nevét.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/29.png)

5. Az a **hozzárendelések** kattintson **felhasználók és csoportok**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/05.png)

    a. Ha a klasszikus házirend kiválasztott összes felhasználó, kattintson a **minden felhasználó**. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/35.png)

    b. Ha a klasszikus házirend kijelölt csoportokat, kattintson a **felhasználók és csoportok kiválasztása**, majd válassza ki a szükséges felhasználók és csoportok.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/36.png)

    c. Ha a kizárási csoportokat, kattintson a **kizárása** lapot, és jelölje ki a szükséges felhasználók és csoportok. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/37.png)

6. Az a **új** lapon nyissa meg a **felhőalapú alkalmazásokba** lap a **hozzárendelés** területen kattintson **felhőalapú alkalmazásokba**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Az a **felhőalapú alkalmazásokba** lapon, a következő lépésekkel:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/08.png)

    a. Kattintson a **alkalmazásokról**.

    b. Kattintson a **Kiválasztás** gombra.

    c. A a **válasszon** lapon válassza ki a cloud app, és kattintson a **válasszon**.

    d. Az a **felhőalapú alkalmazásokba** kattintson **végzett**.



9. Ha rendelkezik **többtényezős hitelesítést** kijelölt:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/26.png)

    a. Az a **hozzáférés-szabályozási** kattintson **Grant**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/27.png)

    b. Az a **Grant** kattintson **hozzáférést**, és kattintson a **többtényezős hitelesítést**.

    c. Kattintson a **Kiválasztás** gombra.


10. Kattintson a **a** ahhoz, hogy a házirend.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>A klasszikus házirend letiltása

A klasszikus házirend letiltásához kattintson **letiltása** a a **részletek** megtekintése.

![Klasszikus szabályzatok](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>További lépések

- A klasszikus házirend áttelepítésével kapcsolatos további információkért lásd: [klasszikus szabályzatokkal az Azure portálon áttelepítése](active-directory-conditional-access-migration.md).


- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
