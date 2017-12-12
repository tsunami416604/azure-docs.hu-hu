---
title: "Oktatóanyag: Azure Active Directoryval integrált Teamphoria |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Teamphoria között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 260c85b14032e17def01ded4a461e6337d66239b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Oktatóanyag: Azure Active Directoryval integrált Teamphoria

Ebben az oktatóanyagban elsajátíthatja Teamphoria integrálása az Azure Active Directory (Azure AD).

Teamphoria integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Teamphoria hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Teamphoria (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Teamphoria, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Teamphoria egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Teamphoria hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-teamphoria-from-the-gallery"></a>A gyűjteményből Teamphoria hozzáadása
Az Azure AD integrálása a Teamphoria konfigurálásához kell hozzáadnia Teamphoria a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Teamphoria hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Teamphoria**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. Az eredmények panelen válassza ki a **Teamphoria**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Teamphoria.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Teamphoria a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Teamphoria közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Teamphoria a.

Az Azure AD egyszeri bejelentkezést a Teamphoria tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Teamphoria tesztfelhasználó létrehozása](#creating-a-teamphoria-test-user)**  - való Britta Simon valami Teamphoria, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és konfigurálása egyszeri bejelentkezéshez az Teamphoria alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Teamphoria, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **Teamphoria** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Az a **Teamphoria tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-CÍMÉT a következő mintát:`https://<sub-domain>.teamphoria.com/login`    

    > [!NOTE] 
    > Ne feledje, hogy ezek nincsenek a valódi értékek. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-címmel rendelkezik. Ügyfél [Teamphoria ügyfél-támogatási csoport](https://www.teamphoria.com/) lekérni a bejelentkezési URL-CÍMÉT. 

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. A a **Teamphoria konfigurációs** kattintson **konfigurálása Teamphoria** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **Teamphoria** oldalán, jelentkezzen be rendszergazdaként a Teamphoria alkalmazás.

8. Ugrás a **rendszergazdai beállítások** lehetőséget a bal oldali eszköztáron és a a a konfigurálása lapon kattintson a **egyetlen SIGN-ON** az SSO konfigurációs ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. Kattintson a **hozzáadása új IDENTITÁSSZOLGÁLTATÓ** az egyszeri bejelentkezés beállítások hozzáadásával a képernyő jobb felső sarokban beállítást.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. Adja meg a mezők a részleteket lásd az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **MEGJELENÍTENDŐ név** : a beépülő modul megjelenítendő nevét adja meg a felügyelet lapon.

    b. **GOMB neve** : a lap használatával történő Egyszeri bejelentkezéshez a bejelentkezési oldal megjelenítő nevét.

    c. **TANÚSÍTVÁNY** : Nyissa meg a tanúsítványt a Jegyzettömbben, Azure-portálról letöltött ugyanazt a tartalom másolása és illessze be ide a mezőbe.

    d. **A belépési pont** : illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** másolt korábbi űrlap az Azure-portálon.

    e. Váltás is **ON** , majd kattintson a **mentése**.   

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-teamphoria-test-user"></a>Teamphoria tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Teamphoria bejelentkezni, akkor ki kell építenie Teamphoria be. Teamphoria, ha egy kézi tevékenység.

**A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Teamphoria vállalati webhely.

2. Kattintson a **ADMIN** a bal oldali eszköztáron és a beállítások a **kezelése** lapon kattintson a **felhasználók** a felhasználók számára a rendszergazda lap megnyitásához.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. Kattintson a **manuális MEGHÍVÁSA** lehetőséget.

    ![Felkérése](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)  

4. Ezen a lapon hajtsa végre a következő művelet. 
    
    ![Felkérése](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)  

    a. Az a **E-mail cím** szövegmezőhöz a **e-mail cím** a BrittaSimon.

    b. Az a **UTÓNÉV** szövegmezőhöz típus **Britta**.

    c. Az a **Vezetéknév** szövegmezőhöz típus **Simon**.

    d. Kattintson a **MEGHÍVÁSA 1 felhasználó**. Fogadja el a rendszer létrehozása a meghívott felhasználó felhasználónak kell.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított a hozzáférés Teamphoria Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Teamphoria, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Teamphoria**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png

