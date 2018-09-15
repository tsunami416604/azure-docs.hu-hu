---
title: Rövid útmutató – megkövetelése a többtényezős hitelesítés (MFA) az Azure Active Directory feltételes hozzáférés konkrét alkalmazások esetén |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan köthet a hitelesítési követelményeknek, az Azure Active Directory (Azure AD) feltételes hozzáférés használatával elért felhőalapú alkalmazás típusát.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: f27ced5c67c5124d0792c5ce3884c88473fba056
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604195"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Gyors útmutató: Többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés konkrét alkalmazások esetén 

A felhasználók bejelentkezési élmény egyszerűsítése, érdemes, hogy jelentkezzen be a felhőalapú alkalmazások, a felhasználónév és jelszó használatával. Azonban sok környezet rendelkezik, amelynek tanácsos igényelnek, például a többtényezős hitelesítés (MFA) a Fiókellenőrzés erősebb egyfajta legalább néhány alkalmazással. Ez lehet például IGAZ, a szervezet e-mail rendszer vagy a HR-alkalmazások eléréséhez. Az Azure Active Directoryban (Azure AD) a cél a feltételes hozzáférési szabályzattal együtt is elérheti.    

Ez a rövid útmutató ismerteti, hogyan konfigurálható egy [Azure AD feltételes hozzáférési szabályzat](../active-directory-conditional-access-azure-portal.md) a multi-factor authentication szolgáltatás egy kijelölt felhőalkalmazás a környezetben, amely igényel.

![Szabályzat létrehozása](./media/app-based-mfa/32.png)


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.



## <a name="prerequisites"></a>Előfeltételek 

Ebben a rövid útmutatóban a forgatókönyv végrehajtásához szükséges:

- **Hozzáférés az Azure AD Premium Edition** – Azure AD feltételes hozzáférés egy Azure AD Premium-funkció. 

- **Tesztfiók nevű Isabella Simonsen** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, lásd: [adja hozzá a felhőalapú felhasználók](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

Ebben a lépésben az a célja, hogy egy benyomást kell szereznie a bejelentkezési élmény a feltételes hozzáférési szabályzat nélkül.

**A környezet inicializálása:**

1. Jelentkezzen be az Azure portal Isabella Simonsen.

2. Jelentkezzen ki.


## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása 

Ez a szakasz bemutatja, hogyan hozhat létre a feltételes hozzáférési szabályzat. Ebben a rövid útmutatóban a forgatókönyvet használja:

- Az Azure Portalon, helyőrző egy felhőalapú alkalmazás, amely a többtényezős Hitelesítést követel meg. 
- A Mintafelhasználó a feltételes hozzáférési házirend tesztelése.  

Állítsa be a szabályzat:

|Beállítás |Érték|
|---     | --- |
|Felhasználók és csoportok | Isabella Simonsen |
|Felhőalkalmazások | A Microsoft Azure Management |
|Hozzáférés biztosítása | Többtényezős hitelesítés megkövetelése |
 

![Szabályzat létrehozása](./media/app-based-mfa/31.png)

 


**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférési rendszergazdájaként.

2. Az Azure Portalon, a bal oldali navigációs sávon kattintson **Azure Active Directory**. 

    ![Azure Active Directory](./media/app-based-mfa/02.png)

3. Az a **Azure Active Directory** lap a **kezelés** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/app-based-mfa/03.png)
 
4. Az a **feltételes hozzáférési** oldalon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Hozzáadás](./media/app-based-mfa/04.png)

5. Az a **új** lap a **neve** szövegmezőbe írja be **többtényezős hitelesítés megkövetelése az Azure portál elérésére szolgáló**.

    ![Name (Név)](./media/app-based-mfa/05.png)

6. Az a **hozzárendelés** területén kattintson **felhasználók és csoportok**.

    ![Felhasználók és csoportok](./media/app-based-mfa/06.png)

7. Az a **felhasználók és csoportok** lapon, a következő lépésekkel:

    ![Felhasználók és csoportok](./media/app-based-mfa/24.png)

    a. Kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki **felhasználók és csoportok**.

    b. Kattintson a **Kiválasztás** gombra.

    c. Az a **válassza** lapra, jelölje be **Isabella Simonsen**, és kattintson a **kiválasztása**.

    d. Az a **felhasználók és csoportok** kattintson **kész**.

8. Kattintson a **Felhőalkalmazások**.

    ![Felhőalkalmazások](./media/app-based-mfa/08.png)

9. Az a **Felhőalkalmazások** lapon, a következő lépésekkel:

    ![Válassza ki a felhőalapú alkalmazások](./media/app-based-mfa/26.png)

    a. Kattintson a **alkalmazások kiválasztása**.

    b. Kattintson a **Kiválasztás** gombra.

    c. A a **válassza** lapra, jelölje be **a Microsoft Azure Management**, és kattintson a **válassza**.

    d. Az a **Felhőalkalmazások** kattintson **kész**.


10. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

    ![Hozzáférés-szabályozás](./media/app-based-mfa/10.png)

11. Az a **Grant** lapon, a következő lépésekkel:

    ![Hozzáférés](./media/app-based-mfa/11.png)

    a. Válassza ki **hozzáférést**.

    a. Válassza ki **többtényezős hitelesítés megkövetelése**.

    b. Kattintson a **Kiválasztás** gombra.

12. Az a **házirend engedélyezése** területén kattintson **a**.

    ![Házirend engedélyezése](./media/app-based-mfa/18.png)

13. Kattintson a **Create** (Létrehozás) gombra.


## <a name="evaluate-a-simulated-sign-in"></a>Egy szimulált bejelentkezési kiértékelése

Most, hogy a feltételes hozzáférési szabályzat van beállítva, érdemes tudni, hogy a várt módon működik-e azt. Első lépésként, a feltételes hozzáférés használata a Mi történik, ha a házirend eszközzel egy jelentkezzen be a tesztfelhasználó szimulálásához. A szimuláció becslése a hatás bejelentkezési ebben a szabályzatok a, és a szimuláció jelentést hoz létre.  

A mi inicializálni a szabályzat kiértékelési eszközével, ha:

- **Isabella Simonsen** felhasználóként 
- **A Microsoft Azure Management** felhőalapú alkalmazásként

 Kattintson a **mi történik, ha** tartalmazó szimuláció jelentést hoz létre:

- **Többtényezős hitelesítés az Azure portál elérésére szolgáló** alatt **érvényes szabályzatok** 
- **Többtényezős hitelesítés megkövetelése** , **engedély**.

![Mi történik, ha a házirend-eszköz](./media/app-based-mfa/23.png)



**A feltételes hozzáférési szabályzat kiértékelése:**

1. Az a [feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson **mi történik, ha**.  
 
    ![What If](./media/app-based-mfa/14.png)

2. Kattintson a **felhasználók**válassza **Isabella Simonsen**, és kattintson a **kiválasztása**.

    ![Felhasználó](./media/app-based-mfa/15.png)

2. Válassza ki a cloud app, hajtsa végre az alábbi lépéseket:

    ![Felhőalkalmazások](./media/app-based-mfa/16.png)

    a. Kattintson a **Felhőalkalmazások**.

    b. Az a **felhőalapú alkalmazások lapjának**, kattintson a **alkalmazások kiválasztása**.

    c. Kattintson a **Kiválasztás** gombra.

    d. A a **válassza** lapra, jelölje be **a Microsoft Azure Management**, és kattintson a **válassza**.

    e. A felhőalapú alkalmazások lapján kattintson a **kész**.

3. Kattintson a **mi történik, ha**.


## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési házirend tesztelése

Az előző szakaszban megtanulhatta egy szimulált bejelentkezési kiértékelése. A szimuláció mellett is érdemes tesztelnie a feltételes hozzáférési szabályzat, győződjön meg arról, hogy az elvárt módon működik. 

Ha tesztelni szeretné a szabályzatot, próbálja meg, jelentkezzen be a [az Azure portal](https://portal.azure.com) használatával a **Isabella Simonsen** fiók teszteléséhez. Megjelenik egy párbeszédpanel, amely a fiók regisztrálásához további biztonsági ellenőrzéshez szükséges.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználó számára, és a feltételes hozzáférési szabályzatot:

- Ha nem ismeri az Azure AD-felhasználó törlése, lásd: [felhasználók törlése az Azure ad-ből](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Törli a szabályzatot, válassza ki a szabályzatot, és kattintson **törlése** a gyorselérési eszköztáron.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használati feltételek elfogadása szükséges](require-tou.md)
> [letiltja a hozzáférést, a munkamenet kockázata észlelésekor](app-sign-in-risk.md)
