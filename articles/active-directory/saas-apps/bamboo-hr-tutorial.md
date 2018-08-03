---
title: 'Oktatóanyag: Azure Active Directory-integráció az BambooHR |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BambooHR között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: dc6664321588d383b4656199c3e8ea79159ca850
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437678"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Oktatóanyag: Azure Active Directory-integráció az BambooHR

Ebben az oktatóanyagban elsajátíthatja, hogyan BambooHR integrálása az Azure Active Directory (Azure AD).

BambooHR integrálása az Azure ad-ben a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá BambooHR Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett BambooHR és az Azure AD-fiókjukat az egyszeri bejelentkezés (SSO) használatával.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

BambooHR az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Előfizetés BambooHR egyszeri bejelentkezés engedélyezve

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy nem használja éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [ingyenes, egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. 

Ez az oktatóanyag ismerteti a forgatókönyv két fő építőelemeket áll:

1. BambooHR hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-bamboohr-from-the-gallery"></a>BambooHR hozzáadása a katalógusból
Az Azure AD integrálása a BambooHR konfigurálásához hozzá BambooHR a katalógusból a felügyelt SaaS-alkalmazások listájában az alábbiak szerint:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Egy alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az "új alkalmazás" gombra][3]

1. A Keresés mezőbe írja be a **BambooHR**. Az eredmények listájában válassza ki a **BambooHR**, majd válassza ki **Hozzáadás**.

    ![Az eredmények listájában BambooHR](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD SSO BambooHR teszt felhasználóként "Britta Simon."

Az SSO működjön az Azure AD tudnia kell, mi az megfelelőjére felhasználóhoz van BambooHR. Más szóval BambooHR az Azure AD-felhasználók és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létesítenie.

A hivatkozás kapcsolatot létesíteni a BambooHR, rendelje hozzá az Azure AD **felhasználónév** érték, mint a BambooHR **felhasználónév** értéket.

Az Azure AD SSO BambooHR tesztelése és konfigurálása, hajtsa végre a következő öt szakaszokban építőelemeket.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon, és egyszeri bejelentkezés konfigurálása az BambooHR alkalmazásban az alábbiak szerint:

1. Az Azure Portalon az a **BambooHR** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** ablakban, a a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezési ablak](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

1. A **BambooHR tartomány és URL-címek**, tegye a következőket:

    ![A BambooHR tartomány és URL-címek szakaszt](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írja be egy URL-címet a következő formátumban: `https://<company>.bamboohr.com`.

    b. Az a **azonosító** mezőbe írjon be egy értéket: `BambooHR-SAML`.

    > [!NOTE] 
    > A **bejelentkezési URL-cím** értéke nem valódi. Frissítse a tényleges bejelentkezési URL-CÍMÉT. Az érték beszerzéséhez forduljon a [BambooHR ügyfél-támogatási csapatának](https://www.bamboohr.com/contact.php). 
 
1. Alatt **SAML-aláíró tanúsítvány**válassza **tanúsítvány (Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

1. Kattintson a **Mentés** gombra.

    ![A Mentés gombra](./media/bamboo-hr-tutorial/tutorial_general_400.png)

1. Alatt **BambooHR konfigurációs**válassza **konfigurálása BambooHR** megnyitásához a **bejelentkezés konfigurálása** ablak. Az a **rövid összefoglaló** területén másolja a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** későbbi használatra.

    ![BambooHR konfiguráció](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

1. Egy új ablakban jelentkezzen be a BambooHR vállalati hely rendszergazdaként.

1. A kezdőlapon tegye a következőket:
   
    ![A BambooHR egyszeri bejelentkezés lap](./media/bamboo-hr-tutorial/ic796691.png "egyszeri bejelentkezés")   

    a. Válassza ki **alkalmazások**.
   
    b. Az a **alkalmazások** ablaktáblán válassza előbb **egyszeri bejelentkezés**.
   
    c. Válassza ki **SAML egyszeri bejelentkezés**.

1. Az a **SAML egyszeri bejelentkezés** ablaktáblán tegye a következőket:
   
    ![A SAML egyszeri bejelentkezés panel](./media/bamboo-hr-tutorial/IC796692.png "SAML egyszeri bejelentkezés")
   
    a. Azokat a **Egyszeri bejelentkezési URL-cím** mezőbe illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** 6. lépésben az Azure Portalról másolt.
      
    b. A Jegyzettömbben, nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmat, és illessze be azt a **X.509-tanúsítvány** mezőbe.
   
    c. Kattintson a **Mentés** gombra.

> [!TIP]
> Amíg az alkalmazás beállítása, tudjon meg ezeket az utasításokat a tömör verzióját a [az Azure portal](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban egyszerűen válassza ki a **egyszeri bejelentkezés** lapra, és hozzáférhet a a beágyazott dokumentáció a **konfigurációs** alul található szakaszában. További információ: [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

   ![Az Azure AD tesztfelhasználó Britta Simon létrehozása][100]

Az Azure ad-ben hozzon létre egy tesztfelhasználót, tegye a következőket:

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/bamboo-hr-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bamboo-hr-tutorial/create_aaduser_02.png)

1. Felső részén a **minden felhasználó** ablaktáblán válassza előbb **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/bamboo-hr-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-bamboohr-test-user"></a>BambooHR tesztfelhasználó létrehozása

Az Azure AD-felhasználók BambooHR bejelentkezés engedélyezéséhez állítsa be őket manuálisan a BambooHR az alábbiak szerint:

1. Jelentkezzen be a **BambooHR** hely rendszergazdaként.

1. Válassza ki a felső eszköztáron **beállítások**.
   
    ![A beállítások gombra](./media/bamboo-hr-tutorial/IC796694.png "beállítás")

1. Válassza az **Áttekintés** lehetőséget.

1. A bal oldali panelen válassza ki a **biztonsági** > **felhasználók**.

1. Írja be a felhasználónevét, jelszavát és e-mail-címe érvényes Azure AD-fiók, hogy szeretné-e beállítva.

1. Kattintson a **Mentés** gombra.
        
>[!NOTE]
>Felhasználói fiókok Azure AD beállításához, BambooHR felhasználói fiók-létrehozási eszközök vagy API-k is használhatja.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Engedélyezze a felhasználó Britta Simon által biztosított hozzáférés BambooHR Azure SSO használatára.

![A felhasználói szerepkör hozzárendelése][200] 

Britta Simon felhasználó hozzárendelése BambooHR, tegye a következőket:

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, nyissa meg a könyvtár nézet, és válassza **vállalati alkalmazások** > **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az a **vállalati alkalmazások** listáról válassza ki **BambooHR**.

    ![A vállalati alkalmazások listájában a BambooHR hivatkozás](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

1. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki a **Hozzáadás** gombra, majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

1. Válassza ki a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** ablakban válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési panelen.

Amikor kiválasztja a **BambooHR** csempéje a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a BambooHR alkalmazásba.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-hr-tutorial/tutorial_general_203.png

