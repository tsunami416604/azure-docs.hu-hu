---
title: Gyors üzembe helyezés – szükséges a többtényezős hitelesítés (MFA) Azure Active Directory feltételes hozzáféréssel rendelkező adott alkalmazásokhoz |} Microsoft Docs
description: A gyors üzembe helyezés elsajátíthatja, hogyan lehet összekötését a hitelesítési követelményeknek, Azure Active Directory (Azure AD) a feltételes hozzáférés használatának használt felhőalkalmazás-típusra.
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 475b0229b9e627a56b02d2299ee2e5400aa0ede1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Gyors üzembe helyezés: Megkövetelő adott alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel 

A bejelentkezés során tapasztal élmény a felhasználók leegyszerűsítése érdemes lehetővé teszik a felhőalapú alkalmazásokhoz, a felhasználónév és jelszó használatával való bejelentkezéshez. Azonban a legtöbb környezetben, amelynek tanácsos igényelnek, például a többtényezős hitelesítés (MFA) a Fiókellenőrzés erősebb formája legalább néhány alkalmazásokkal rendelkeznek. Ez lehet például IGAZ, a szervezet e-mail rendszer vagy a HR-alkalmazásokhoz való hozzáféréshez. Az Azure Active Directory (Azure AD) a feltételes hozzáférési házirenddel ezen cél megvalósításához.    

A gyors üzembe helyezés ismerteti, hogyan konfigurálható egy [az Azure AD feltételes hozzáférési házirend](active-directory-conditional-access-azure-portal.md) többtényezős hitelesítést, amelyhez a kijelölt felhőalapú alkalmazások a környezetben.

![Szabályzat létrehozása](./media/active-directory-conditional-access-app-based-mfa/32.png)


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.



## <a name="prerequisites"></a>Előfeltételek 

A forgatókönyv a gyors üzembe helyezés befejeződik, az alábbiak szükségesek:

- **Hozzáférés az Azure AD prémium kiadás** – az Azure AD feltételes hozzáférés egy Azure AD Premium-funkció. 

- **Egy olyan fiókot nevű Isabella Simonsen** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, lásd: [adja hozzá a felhőalapú felhasználók](add-users-azure-active-directory.md#add-cloud-based-users).



## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása 

Ez a szakasz ismerteti a feltételes hozzáférési házirend létrehozása. Az a gyors üzembe helyezési forgatókönyv használja:

- Az Azure portálon, mint a többtényezős Hitelesítést igénylő felhőalapú alkalmazások helyőrző. 
- A minta felhasználó, a feltételes hozzáférési házirend tesztelése.  

A házirend beállítása:

|Beállítás |Érték|
|---     | --- |
|Felhasználók és csoportok | Isabella Simonsen |
|Felhőalkalmazások | A Microsoft Azure felügyeleti |
|Hozzáférés biztosítása | Többtényezős hitelesítés megkövetelése |
 

![Szabályzat létrehozása](./media/active-directory-conditional-access-app-based-mfa/31.png)

 


**A feltételes hozzáférési házirend konfigurálása:**

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.

2. Kattintson a bal oldali navigációs sávja, az Azure-portálon **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. Az a **feltételes hozzáférés** lapon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Hozzáadás](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. Az a **új** lap a **neve** szövegmezőhöz típus **többtényezős hitelesítés megkövetelése az Azure portál elérésére szolgáló**.

    ![Név](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. Az a **hozzárendelés** kattintson **felhasználók és csoportok**.

    ![Felhasználók és csoportok](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. Az a **felhasználók és csoportok** lapon, a következő lépésekkel:

    ![Felhasználók és csoportok](./media/active-directory-conditional-access-app-based-mfa/24.png)

    a. Kattintson a **felhasználók és csoportok kiválasztása**, majd válassza ki **felhasználók és csoportok**.

    b. Kattintson a **Kiválasztás** gombra.

    c. A a **válasszon** lapon, válassza ki **Isabella Simonsen**, és kattintson a **válasszon**.

    d. Az a **felhasználók és csoportok** kattintson **végzett**.

8. Kattintson a **felhőalapú alkalmazásokba**.

    ![Felhőalkalmazások](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. Az a **felhőalapú alkalmazásokba** lapon, a következő lépésekkel:

    ![Válassza ki a felhőalapú alkalmazások](./media/active-directory-conditional-access-app-based-mfa/26.png)

    a. Kattintson a **alkalmazásokról**.

    b. Kattintson a **Kiválasztás** gombra.

    c. A a **kiválasztása** lapon, válassza ki **Microsoft Azure Management**, és kattintson a **kiválasztása**.

    d. Az a **felhőalapú alkalmazásokba** kattintson **végzett**.


10. Az a **hozzáférés-szabályozási** kattintson **Grant**.

    ![Hozzáférés-szabályozás](./media/active-directory-conditional-access-app-based-mfa/10.png)

11. Az a **Grant** lapon, a következő lépésekkel:

    ![Hozzáférés](./media/active-directory-conditional-access-app-based-mfa/11.png)

    a. Válassza ki **hozzáférést**.

    a. Válassza ki **többtényezős hitelesítést**.

    b. Kattintson a **Kiválasztás** gombra.

12. Az a **házirend engedélyezése** kattintson **a**.

    ![Házirend engedélyezése](./media/active-directory-conditional-access-app-based-mfa/18.png)

13. Kattintson a **Create** (Létrehozás) gombra.


## <a name="evaluate-a-simulated-sign-in"></a>A szimulált bejelentkezés kiértékelése

Most, hogy a feltételes hozzáférési házirend van beállítva, érdemes tudni, hogy az megfelelően működik-e. Első lépésként, a feltételes hozzáférés használata a Mi történik, ha a házirend szimulálása a bejelentkezés annak a tesztfelhasználónak eszközzel. A szimuláció becslése bejelentkezés Ez hatással legyen a házirendek és a szimuláció jelentést hoz létre.  

Inicializálni a Mi a házirend kiértékelési eszközével, ha:

- **Isabella Simonsen** felhasználóként 
- **A Microsoft Azure Management** felhő alkalmazásként

 Kattintson a **mi történik, ha** hoz létre a szimuláció jelentés tájékoztat:

- **Többtényezős hitelesítés megkövetelése az Azure portál elérésére szolgáló** alatt **házirendek, amelyek mindegyikre érvényesek** 
- **Többtényezős hitelesítés megkövetelése** , **Grant vezérlők**.

![Mi történik, ha a házirend-eszköz](./media/active-directory-conditional-access-app-based-mfa/23.png)



**A feltételes hozzáférési szabályzat kiértékelése:**

1. Az a [feltételes hozzáférés - házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson **mi történik, ha**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Kattintson a **felhasználók**, jelölje be **Isabella Simonsen**, és kattintson a **válasszon**.

    ![Felhasználó](./media/active-directory-conditional-access-app-based-mfa/15.png)

2. Válassza ki a cloud app, hajtsa végre az alábbi lépéseket:

    ![Felhőalkalmazások](./media/active-directory-conditional-access-app-based-mfa/16.png)

    a. Kattintson a **felhőalapú alkalmazásokba**.

    b. Az a **felhő lap az alkalmazások**, kattintson a **alkalmazásokról**.

    c. Kattintson a **Kiválasztás** gombra.

    d. A a **válasszon** lapon válassza ki a Microsoft Azure felügyeleti **, és kattintson a **válasszon**.

    e. A felhőalapú alkalmazások lapján kattintson a **végzett**.

3. Kattintson a **mi történik, ha**.


## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési házirend tesztelése

Az előző szakaszban rendelkezik megtudta, hogyan szimulált bejelentkezés kiértékeléséhez. A szimuláció mellett is győződjön meg arról, hogy az elvárt módon működik a feltételes hozzáférési szabályzatot kell tesztelni. 

Tesztelje a házirendet, próbálja meg jelentkezzen be a [Azure-portálon](https://portal.azure.com) használatával a **Isabella Simonsen** fiók tesztelése. Meg kell jelennie egy párbeszédpanelt, amely további biztonsági ellenőrzés feliratkozott a fiók beállítását igényli.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, a tesztfelhasználó számára és a feltételes hozzáférési házirend törlése:

- Ha nem ismeri az Azure AD-felhasználó törlése, lásd: [felhasználók törlése az Azure AD](add-users-azure-active-directory.md#delete-users-from-azure-ad).

- A házirend törléséhez válassza ki a házirendet, és kattintson **törlése** a gyorselérési eszköztár.

    ![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/33.png)


## <a name="next-steps"></a>További lépések

Ha azt szeretné, további információ a feltételes hozzáférési, lásd: [Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-azure-portal.md).

