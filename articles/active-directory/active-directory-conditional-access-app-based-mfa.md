---
title: Gyors üzembe helyezés – felhőalkalmazás többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés konfigurálása |} Microsoft Docs
description: Ismerje meg, hogyan lehet összekötését a hitelesítési követelményeknek, Azure Active Directory (Azure AD) a feltételes hozzáférés használatának használt felhőalkalmazás-típusra.
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ac43817fb3f253c35cd69a8ecd8931afca50892b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-configure-per-cloud-app-mfa-with-azure-active-directory-conditional-access"></a>Gyors üzembe helyezés: Felhőalkalmazás többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés konfigurálása 


A bejelentkezés során tapasztal élmény a felhasználók leegyszerűsítése érdemes lehetővé teszik a felhőalapú alkalmazásokhoz, a felhasználónév és jelszó használatával való bejelentkezéshez. Azonban a legtöbb környezetben legalább néhány olyan alkalmazások, amelyekre tanácsos erősebb formája Fiókellenőrzés, például a többtényezős hitelesítés megkövetelése rendelkeznek. Ez lehet például IGAZ, a szervezet e-mail rendszer vagy a HR-alkalmazásokhoz való hozzáféréshez.  

A gyors üzembe helyezés bemutatja, hogyan megkövetelheti a multi-factor authentication csak a kijelölt felhő kapnia a környezet használatával egy [az Azure AD feltételes hozzáférési házirend](active-directory-conditional-access-azure-portal.md).

A gyors üzembe helyezés, a forgatókönyv végrehajtásához meg:


> [!div class="checklist"]
> * A feltételes hozzáférési szabályzat létrehozása
> * A feltételes hozzáférési házirend kiértékelése
> * A feltételes hozzáférési házirend tesztelése  


## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben a cikkben a forgatókönyvet használja az Azure-portálon helyőrző egy felhő-alkalmazást, amely egy megadott felhasználó többtényezős hitelesítést igényel. Britta Simon az a felhasználó a szervezetében. Amikor bejelentkezik az Azure-portálon, rá, hogy további ellenőrizzük a saját fiókját a többtényezős hitelesítéssel szeretne.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/01.png)



## <a name="before-you-begin"></a>Előkészületek 

A forgatókönyv a gyors üzembe helyezés befejeződik, az alábbiak szükségesek:

- **Hozzáférés az Azure AD prémium kiadás** – az Azure AD feltételes hozzáférés egy Azure AD Premium-funkció. Ha nincs az Azure AD Premium edition a hozzáférést, akkor [előfizetési próbaverziójára](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Egy olyan fiókot nevű Britta Simon** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, olvassa el [ezeket az utasításokat](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).



## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása 

Ez a szakasz ismerteti a feltételes hozzáférési szabályzat létrehozásához.  
A házirend beállítása:

|Beállítás |Érték|
|---     | --- |
|Felhasználók és csoportok | Britta Simon |
|Felhőalkalmazások | A Microsoft Azure felügyeleti |
|Hozzáférés | Többtényezős hitelesítés megkövetelése |
 

![Szabályzat létrehozása](./media/active-directory-conditional-access-app-based-mfa/12.png)




**A feltételes hozzáférési házirend konfigurálása:**

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.

2. Kattintson a bal oldali navigációs sávja, az Azure-portálon **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. Az a **feltételes hozzáférés** lapon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Hozzáadás](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. Az a **új** lap a **neve** szövegmezőhöz típus **Britta a többtényezős hitelesítés megkövetelése**.

    ![Name (Név)](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. Az a **hozzárendelés** kattintson **felhasználók és csoportok**.

    ![Felhasználók és csoportok](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. Az a **felhasználók és csoportok** lapon, a következő lépésekkel:

    ![Felhasználók és csoportok](./media/active-directory-conditional-access-app-based-mfa/07.png)

    a. Kattintson a **felhasználók és csoportok kiválasztása**.

    b. Kattintson a **Kiválasztás** gombra.

    c. A a **válasszon** lapon jelölje be a tesztfelhasználó számára, és kattintson a **válasszon**.

    d. Az a **felhasználók és csoportok** kattintson **végzett**.

8. Kattintson a **felhőalapú alkalmazásokba**.

    ![Felhőalkalmazások](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. Az a **felhőalapú alkalmazásokba** lapon, a következő lépésekkel:

    ![Válassza ki a felhőalapú alkalmazások](./media/active-directory-conditional-access-app-based-mfa/09.png)

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


## <a name="evaluate-your-conditional-access-policy"></a>A feltételes hozzáférési házirend kiértékelése

Megismerheti a hatását, a feltételes hozzáférési házirendek az adott környezet, használhatja a [feltételes hozzáférés, milyen if házirend eszközzel](active-directory-conditional-access-whatif.md). Ezzel az eszközzel kiértékelheti a szimulált bejelentkezés felhasználói.

Az eszköz konfigurálása során **Britta Simon** felhasználóként és **Microsoft Azure Management** felhőalkalmazás, mint az eszköz látható **Britta a többtényezős hitelesítés megkövetelése** alatt  **Házirendek, amelyek mindegyikre érvényesek**.

![Mi történik, ha a házirend-eszköz](./media/active-directory-conditional-access-app-based-mfa/17.png)



**A feltételes hozzáférési szabályzat kiértékelése:**

1. Az a [feltételes hozzáférés - házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson **mi történik, ha**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Kattintson a **felhasználók**, jelölje be **Britta Simon**, és kattintson a **válasszon**.

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

Tesztelje a házirendet, próbálja meg jelentkezzen be a [Azure-portálon](https://portal.azure.com) használatával a **Britta Simon** fiók tesztelése. Meg kell jelennie egy párbeszédpanelt, amely további biztonsági ellenőrzés feliratkozott a fiók beállítását igényli.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/01.png)


## <a name="next-steps"></a>További lépések

Ha azt szeretné, további információ a feltételes hozzáférési, lásd: [Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-azure-portal.md).

