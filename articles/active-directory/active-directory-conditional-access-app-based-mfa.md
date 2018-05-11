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
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: b451ede984d3baa8331ec87575557f845686c01f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Gyors üzembe helyezés: Megkövetelő adott alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel 

A bejelentkezés során tapasztal élmény a felhasználók leegyszerűsítése érdemes lehetővé teszik a felhőalapú alkalmazásokhoz, a felhasználónév és jelszó használatával való bejelentkezéshez. Azonban a legtöbb környezetben legalább néhány olyan alkalmazások, amelyekre tanácsos erősebb formája Fiókellenőrzés, például a többtényezős hitelesítés megkövetelése rendelkeznek. Ez lehet például IGAZ, a szervezet e-mail rendszer vagy a HR-alkalmazásokhoz való hozzáféréshez. Az Azure Active Directoryban a feltételes hozzáférési házirenddel ezen cél megvalósításához.    

A gyors üzembe helyezés ismerteti, hogyan konfigurálható egy [az Azure AD feltételes hozzáférési házirend](active-directory-conditional-access-azure-portal.md) állítja be a kijelölt felhőalapú alkalmazások vannak a környezetében többtényezős hitelesítést igényel.


## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben a cikkben a forgatókönyvet használja az Azure-portálon helyőrző egy felhő-alkalmazást, amely egy megadott felhasználó többtényezős hitelesítést igényel. Isabella Simonsen az a felhasználó a szervezetében. Amikor bejelentkezik az Azure-portálon, rá, hogy további ellenőrizzük a saját fiókját a többtényezős hitelesítéssel szeretne.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)



## <a name="prerequisites"></a>Előfeltételek 

A forgatókönyv a gyors üzembe helyezés befejeződik, az alábbiak szükségesek:

- **Hozzáférés az Azure AD prémium kiadás** – az Azure AD feltételes hozzáférés egy Azure AD Premium-funkció. Ha nincs az Azure AD Premium edition a hozzáférést, akkor [előfizetési próbaverziójára](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Egy olyan fiókot nevű Isabella Simonsen** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, olvassa el [ezeket az utasításokat](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).



## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása 

Ez a szakasz ismerteti a feltételes hozzáférési házirend létrehozása.  
A házirend beállítása:

|Beállítás |Érték|
|---     | --- |
|Felhasználók és csoportok | Isabella Simonsen |
|Felhőalkalmazások | A Microsoft Azure felügyeleti |
|Hozzáférés | Többtényezős hitelesítés megkövetelése |
 

![Szabályzat létrehozása](./media/active-directory-conditional-access-app-based-mfa/21.png)




**A feltételes hozzáférési házirend konfigurálása:**

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.

2. Kattintson a bal oldali navigációs sávja, az Azure-portálon **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. Az a **feltételes hozzáférés** lapon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Hozzáadás](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. Az a **új** lap a **neve** szövegmezőhöz típus **többtényezős hitelesítés megkövetelése az Azure portál elérésére szolgáló**.

    ![Name (Név)](./media/active-directory-conditional-access-app-based-mfa/05.png)

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


## <a name="evaluate-your-conditional-access-policy"></a>A feltételes hozzáférési házirend kiértékelése

Most, hogy a feltételes hozzáférési házirend van beállítva, érdemes tudni, hogy az megfelelően működik-e. Első lépésként, használhatja a [feltételes hozzáférés, milyen if házirend eszközzel](active-directory-conditional-access-whatif.md) szimulálása a bejelentkezés felhasználói. Az eszköz konfigurálása során **Isabella Simonsen** felhasználóként és **Microsoft Azure Management** felhőalkalmazás, mint az eszköz látható **többtényezős hitelesítés megkövetelése az Azure portál elérésére szolgáló** alatt **Házirendek, amelyek mindegyikre érvényesek** és **többtényezős hitelesítést** , **Grant vezérlők**.

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

Az előző szakaszban az előző szakaszban, akkor 

Tesztelje a házirendet, próbálja meg jelentkezzen be a [Azure-portálon](https://portal.azure.com) használatával a **Isabella Simonsen** fiók tesztelése. Meg kell jelennie egy párbeszédpanelt, amely további biztonsági ellenőrzés feliratkozott a fiók beállítását igényli.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="next-steps"></a>További lépések

Ha azt szeretné, további információ a feltételes hozzáférési, lásd: [Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-azure-portal.md).

