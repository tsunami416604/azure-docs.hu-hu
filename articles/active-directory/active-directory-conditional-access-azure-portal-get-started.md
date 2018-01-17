---
title: "Az Azure Active Directoryban feltételes hozzáférés – első lépések |} Microsoft Docs"
description: "A helyre vonatkozó feltétellel feltételes hozzáférés tesztelése."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c780efe92ec69ff692b42a586f55a1036d2ec952
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Ismerkedés a feltételes hozzáférés az Azure Active Directoryban

Feltételes hozzáférés az Azure Active Directoryban, amely lehetővé teszi a feltételeket, amely alatt engedéllyel rendelkező felhasználók férhetnek hozzá az alkalmazások képesek. 

Ez a témakör nyújt útmutatást a hely feltétel a környezetben a feltételes hozzáférés tesztelése.  


## <a name="scenario-description"></a>Forgatókönyv leírása

Számos szervezetben egy közös követelmény-hoz csak többtényezős hitelesítés megkövetelése az alkalmazásokhoz való hozzáférés, amely nem történik a vállalati intranethez. Az Azure Active Directoryval könnyen megvalósításához a cél helyalapú feltételes hozzáférési házirend konfigurálása. Ez a témakör részletes útmutatást kapcsolódó házirend beállítása. A házirend használja [megbízható IP-címek](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) kérés érkezett a vállalati hozzáférés kísérletek közötti különbséget tenni a következőre intranetes és minden egyéb helyek.


## <a name="prerequisites"></a>Előfeltételek

A témakörben ismertetett forgatókönyv feltételezi, hogy Ön ismeri a ismertetett fogalmakat [Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-azure-portal.md).

Ez a forgatókönyv teszteléséhez kell:

- Tesztfelhasználó létrehozása 

- Az Azure AD Premium licenc hozzárendelése a tesztfelhasználó számára

- Konfigurálhatja egy felügyelt alkalmazást, és rendelje hozzá a tesztfelhasználó számára

- Megbízható IP-címek konfigurálása

Ha további információt a megbízható IP-cím van szüksége, tekintse meg [megbízható IP-címek](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Házirend konfigurációs lépések

**A feltételes hozzáférési házirend konfigurálásához hajtsa végre:**

1. Kattintson a bal oldali navigációs sávja, az Azure-portálon **Azure Active Directory**. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. A a **Azure Active Directory** panelen, a a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. Az a **feltételes hozzáférési** panelt, nyissa meg a **új** panelen, a felső eszköztáron kattintson **Hozzáadás**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. Az a **új** panelen, a a **neve** szövegmező, írja be a házirend nevét.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. Az a **hozzárendelés** kattintson **felhasználók és csoportok**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. Az a **felhasználók és csoportok** panelen végezze el a következő lépéseket:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Kattintson a **felhasználók és csoportok kiválasztása**.

    b. Kattintson a **Kiválasztás** gombra.

    c. Az a **válasszon** panelen, jelölje be a tesztfelhasználó számára, és kattintson **válasszon**.

    d. Az a **felhasználók és csoportok** panelen kattintson a **végzett**.

7. Az a **új** panelt, nyissa meg a **felhőalapú alkalmazásokba** panelen, a a **hozzárendelés** területen kattintson **felhőalapú alkalmazásokba**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Az a **felhőalapú alkalmazásokba** panelen végezze el a következő lépéseket:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Kattintson a **alkalmazásokról**.

    b. Kattintson a **Kiválasztás** gombra.

    c. A a **válasszon** panelen válassza ki a cloud app, és kattintson a **válasszon**.

    d. Az a **felhőalapú alkalmazásokba** panelen kattintson a **végzett**.

9. Az a **új** panelt, nyissa meg a **feltételek** panelen, a a **hozzárendelés** területen kattintson **feltételek**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. A a **feltételek** panelt, nyissa meg a **helyek** panelen kattintson a **helyek**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. Az a **helyek** panelen végezze el a következő lépéseket:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. A **konfigurálása**, kattintson a **Igen**.

    b. A **Include**, kattintson a **minden hely**.

    c. Kattintson a **kizárása**, és kattintson a **a megbízható IP-címek**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Kattintson a **Done** (Kész) gombra.

12. Az a **feltételek** panelen kattintson a **végzett**.

13. Az a **új** panelt, nyissa meg a **Grant** panelen, a a **vezérlők** területen kattintson **Grant**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Az a **Grant** panelen végezze el a következő lépéseket:

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Válassza ki **többtényezős hitelesítést**.

    b. Kattintson a **Kiválasztás** gombra.

15. Az a **új** panel alatt **házirend engedélyezése**, kattintson a **a**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Az a **új** panelen kattintson a **létrehozása**.


## <a name="testing-the-policy"></a>A házirendek tesztelésével

A házirend teszteléséhez az eszközről kell hozzáférést az alkalmazás, amely: 

1. IP-címet tartalmazza, amely megfelel a konfigurált megbízható IP-címek 

1. IP-címet tartalmazza, amely kívül esik a konfigurált megbízható IP-címek

A multi-factor authentication csak kell, amely egy eszközről, amely kívül esik a konfigurált megbízható IP-címek történt kapcsolódási kísérlet során. 


## <a name="next-steps"></a>További lépések

Ha azt szeretné, további információ a feltételes hozzáférési, lásd: [Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-azure-portal.md).

