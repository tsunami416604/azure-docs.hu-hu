---
title: "Telepítse át a klasszikus szabályzatokkal az Azure portálon |} Microsoft Docs"
description: "Telepítse át a klasszikus szabályzatokkal az Azure portálon."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 416ae171564fa23473d09bad950d149e201bcc98
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Telepítse át a klasszikus szabályzatokkal az Azure portálon 


[Feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) egy Azure Active Directory (Azure AD), amelyek segítségével szabályozhatja, hogy hogyan funkció engedélyezett a felhasználók hozzáférést a felhőalapú alkalmazásokat. Erre a célra továbbra is megegyezik, amíg az új Azure-portálon kiadása is vezetett be jelentős fejlesztéseket való feltételes hozzáférés működését. A feltételes hozzáférési házirendek konfigurálása az Azure-portálon kívül az új házirendek létrehozásakor az Azure portálon egyszerre is használható. Mindaddig, amíg meg nem letiltása vagy távolítsa el, azok továbbra is alkalmazza a környezetben. Javasoljuk azonban, hogy telepítse át a klasszikus házirendek az új Azure AD feltételes hozzáférési házirendek, mert:

- Az új házirend lehetővé teszi, hogy Ön nem lehet kezelni a klasszikus házirendek forgatókönyvek kezeléséhez.

- Csökkentése érdekében egyesítse ezeket által felügyelni kell házirendek száma.   

Ez a témakör segítséget nyújt a meglévő klasszikus házirendek az új áttelepítése az új Azure AD feltételes hozzáférési szabályzatokat.


## <a name="classic-policies"></a>Klasszikus szabályzatok

A feltételes hozzáférési házirendek az Azure AD és az Intune nem hozott létre az Azure portálon is **klasszikus házirendek**. A klasszikus házirendek áttelepítéséhez, akkor nem hozzáféréssel kell rendelkeznie a klasszikus Azure portálra. Az Azure-portálon tartalmazza a egy [ **klasszikus házirendek (előzetes verzió)** nézet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) , amely lehetővé teszi, hogy tekintse át a klasszikus házirendeket.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Nyissa meg a klasszikus házirend

**A klasszikus házirend megnyitása:**

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a bal oldali navigációs sávja **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/02.png)
 
2. Az a **feltételes hozzáférés - házirendek** lap a **kezelése** kattintson **klasszikus házirendek (előzetes verzió)**.

3. A klasszikus házirendek listájában jelölje ki az Önt érdeklő házirend.   

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Az Azure AD feltételes hozzáférési házirendek

Ez a témakör részletes lépéseket, amelyek lehetővé teszik a klasszikus házirendek át anélkül, hogy ismeri a Azure AD feltételes hozzáférésével kapcsolatos házirendek. Azonban a ismeri a rendszerhez kapcsolódó alapfogalommal és terminológiát alatt az Azure AD feltételes hozzáférés segítségével az áttelepítés során tapasztal élmény javítására.

Lásd:

- [Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md) az alapvető fogalmakat és a terminológia

- [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md) és ismerje meg az Azure-portálon a felhasználói felületen


 





## <a name="multi-factor-authentication-policy"></a>Többtényezős hitelesítési házirend 

A példa bemutatja, hogyan telepíthet át egy klasszikus házirend igénylő **a multi-factor authentication** felhőalapú alkalmazások. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**A klasszikus házirend áttelepítése:**

1. [Nyissa meg a klasszikus házirend](#open-a-classic-policy) beolvasni a konfigurációs beállításait.
2. Hozzon létre egy új Azure AD feltételes hozzáférési házirend felülírja a klasszikus házirend. 


### <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési szabályzat létrehozása


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

11. A klasszikus házirendet. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Következő lépések

- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
