---
title: 'Oktatóanyag: Azure Active Directoryval integrált BambooHR |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és BambooHR között.
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
ms.openlocfilehash: d31d8d92590f665b847bb92370967e88ef4590ff
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210703"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Oktatóanyag: Azure Active Directoryval integrált BambooHR

Ebben az oktatóanyagban elsajátíthatja BambooHR integrálása az Azure Active Directory (Azure AD).

BambooHR integrálása az Azure AD a következő előnyökkel jár:

- Az Azure AD, aki hozzáfér BambooHR szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett BambooHR az Azure AD-fiókok egyszeri bejelentkezés (SSO) használatával.
- A fiók egyetlen központi helyen, az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs BambooHR, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Előfizetés BambooHR SSO engedélyezése

> [!NOTE]
> Ebben az oktatóanyagban tesztelésekor a lépéseket, ajánlott, hogy nem éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [ingyenes, egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. 

Ez az oktatóanyag ismerteti a forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből BambooHR hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-bamboohr-from-the-gallery"></a>Adja hozzá a BambooHR a gyűjteményből
Az Azure AD integrálása a BambooHR konfigurálásához hozzáadása BambooHR a gyűjteményből a kezelt SaaS-alkalmazások listáját a következő módon:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások ablaktábla][2]
    
3. Hozzáadhat egy alkalmazást, válassza ki a **új alkalmazás**.

    ![Az "új alkalmazás" gomb][3]

4. Írja be a keresőmezőbe, **BambooHR**. Az eredmények listájában válassza **BambooHR**, majd válassza ki **Hozzáadás**.

    ![Az eredménylistában BambooHR](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása és tesztelése Azure AD SSO BambooHR tesztfelhasználó "Britta Simon."

Az egyszeri bejelentkezés működjön az Azure AD tudnia kell, mi a párjukhoz felhasználó van BambooHR. Más szóval BambooHR az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létesítenie.

A hivatkozás kapcsolatot létesíteni a BambooHR, rendelje hozzá az Azure AD **felhasználónév** érték, mint a BambooHR **felhasználónév** érték.

Azure AD BambooHR egyszeri bejelentkezés tesztelése és konfigurálása, végezze el a következő öt szakaszokban építőelemeit.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri Bejelentkezést az Azure portálon, és egyszeri bejelentkezés konfigurálása az BambooHR alkalmazásban a következő módon:

1. Az Azure portálon a a **BambooHR** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** ablakban, a a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés ablak](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. A **BambooHR tartomány és az URL-címek**, tegye a következőket:

    ![A BambooHR tartomány és az URL-címek szakasz](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe, írja be egy URL-címet a következő formátumban: `https://<company>.bamboohr.com`.

    b. Az a **azonosító** mezőbe írjon be egy értéket: `BambooHR-SAML`.

    > [!NOTE] 
    > A **bejelentkezési URL-cím** érték nincs valós. Frissíti a tényleges bejelentkezési URL-CÍMÉT. Beszerezni az értéket, lépjen kapcsolatba a [BambooHR ügyfél-támogatási csoport](https://www.bamboohr.com/contact.php). 
 
4. A **SAML-aláíró tanúsítványa**, jelölje be **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![A Mentés gombra](./media/bamboo-hr-tutorial/tutorial_general_400.png)

6. A **BambooHR konfigurációs**, jelölje be **konfigurálása BambooHR** megnyitásához a **bejelentkezés konfigurálása** ablak. Az a **rövid összefoglaló** szakaszban, másolja a **SAML-alapú egyszeri bejelentkezési URL-címe** későbbi használatra.

    ![BambooHR konfiguráció](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. Egy új ablakban jelentkezzen be a BambooHR vállalati webhely rendszergazdaként.

8. A kezdőlapon tegye a következőket:
   
    ![A BambooHR egyszeri bejelentkezés lap](./media/bamboo-hr-tutorial/ic796691.png "egyszeri bejelentkezést.")   

    a. Válassza ki **alkalmazások**.
   
    b. Az a **alkalmazások** ablaktáblán válassza előbb **egyszeri bejelentkezés**.
   
    c. Válassza ki **SAML-alapú egyszeri bejelentkezést**.

9. Az a **SAML-alapú egyszeri bejelentkezést** ablaktáblán tegye a következőket:
   
    ![A SAML-alapú egyszeri bejelentkezést ablaktábla](./media/bamboo-hr-tutorial/IC796692.png "SAML-alapú egyszeri bejelentkezést.")
   
    a. Azokat a **Egyszeri bejelentkezési URL-cím** mezőbe illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** , az Azure-portálon a 6. lépésben másolt.
      
    b. A Jegyzettömbben, nyissa meg a base-64 kódolású tanúsítvány Azure-portálról letöltött, másolja a tartalmat, és illessze be azt a **X.509 tanúsítvány** mezőbe.
   
    c. Kattintson a **Mentés** gombra.

> [!TIP]
> Az alkalmazás beállítása, amíg el tudja olvasni ezeket az utasításokat a tömör verzióját a [Azure-portálon](https://portal.azure.com). Miután hozzáadta az alkalmazásból a **Active Directory** > **vállalati alkalmazások** szakaszban egyszerűen jelölje be a **egyszeri bejelentkezés** lapot, és hozzáférhet a beágyazott keresztül dokumentáció a **konfigurációs** szakasz alján. További információ: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja az Azure portálon Britta Simon nevű tesztfelhasználó létrehozása.

   ![Az Azure AD tesztfelhasználó Britta Simon létrehozása][100]

Tesztfelhasználó létrehozása az Azure ad-ben, tegye a következőket:

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/bamboo-hr-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bamboo-hr-tutorial/create_aaduser_02.png)

3. Felső részén a **minden felhasználó** ablaktáblán válassza előbb **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/bamboo-hr-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-bamboohr-test-user"></a>BambooHR tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók BambooHR bejelentkezni, állítsa be őket manuálisan a BambooHR a következő módon:

1. Jelentkezzen be a **BambooHR** hely rendszergazdaként.

2. Válassza ki a felső eszköztáron **beállítások**.
   
    ![A beállítások gombra](./media/bamboo-hr-tutorial/IC796694.png "beállítás")

3. Válassza az **Áttekintés** lehetőséget.

4. A bal oldali panelen válassza ki a **biztonsági** > **felhasználók**.

5. A felhasználónév, jelszó és e-mail cím a érvényes Azure ad fiók beállításához használni kívánt típusát.

6. Kattintson a **Mentés** gombra.
        
>[!NOTE]
>Azure AD-felhasználói fiókok beállításához BambooHR felhasználói fiók létrehozása eszközök vagy API-t is használhatja.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Engedélyezze a felhasználó által biztosított hozzáférés BambooHR Azure SSO használandó Britta Simon.

![A felhasználói szerepkör hozzárendelése][200] 

Felhasználó Britta Simon hozzárendelése BambooHR, tegye a következőket:

1. Az Azure portálon, az alkalmazások nézet megnyitásához, nyissa meg a könyvtár nézetet, és válassza **vállalati alkalmazások** > **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az a **vállalati alkalmazások** listáról válassza ki **BambooHR**.

    ![A vállalati alkalmazások listáját a BambooHR hivatkozás](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra, majd a a **hozzáadása hozzárendelés** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

6. Válassza ki a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** ablakban válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

A hozzáférési Panel az Azure AD egyszeri bejelentkezési beállítások tesztelése.

Ha bejelöli a **BambooHR** csempére a hozzáférési panelen, be kell beolvasni automatikusan jelentkezve az BambooHR alkalmazás.

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



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

