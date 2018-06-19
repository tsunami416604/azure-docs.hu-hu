---
title: 'Oktatóanyag: Azure Active Directory-integrációval rendelkező Zscaler személyes hozzáférési (ZPA) |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Zscaler személyes hozzáférési (ZPA) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 7fca1c781ddc5ba62b094838eed83322e53ba3d9
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923736"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Oktatóanyag: Azure Active Directory-integrációval rendelkező Zscaler személyes hozzáférési (ZPA)

Ebben az oktatóanyagban elsajátíthatja Zscaler személyes hozzáférési (ZPA) integrálása az Azure Active Directory (Azure AD).

Zscaler személyes hozzáférési (ZPA) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér a Zscaler személyes hozzáférési (ZPA)
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a Zscaler személyes hozzáférési (ZPA) (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler személyes hozzáférési (ZPA), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Zscaler személyes hozzáférési (ZPA) egyszeri bejelentkezés engedélyezve van az előfizetésben


> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Zscaler személyes hozzáférési (ZPA) hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler személyes hozzáférési (ZPA) hozzáadása a gyűjteményből
Az Azure AD integrálása a Zscaler személyes hozzáférési (ZPA) konfigurálásához kell hozzáadnia Zscaler személyes hozzáférési (ZPA) a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Zscaler személyes hozzáférési (ZPA) hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Zscaler személyes hozzáférési (ZPA)**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. Az eredmények panelen válassza ki a **Zscaler személyes hozzáférési (ZPA)**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Zscaler személyes hozzáférési (ZPA) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó a Zscaler személyes hozzáférési (ZPA) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Zscaler személyes hozzáférési (ZPA) közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Zscaler személyes hozzáférési (ZPA).

Az Azure AD egyszeri bejelentkezést a Zscaler személyes hozzáférési (ZPA) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Zscaler személyes hozzáférési (ZPA) tesztfelhasználó létrehozása](#creating-a-zscaler-private-access-(zpa)-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon a Zscaler személyes hozzáférési (ZPA), amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és konfigurálása egyszeri bejelentkezéshez az Zscaler személyes hozzáférési (ZPA) alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés beállítása a Zscaler személyes hozzáférési (ZPA), hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **Zscaler személyes hozzáférési (ZPA)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. Az a **Zscaler személyes hozzáférési (ZPA) tartományhoz és URL-címek** területen tegye a következőket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. Az a **URL-cím bejelentkezési** szövegmező, adja meg a következő minta használatával URL-címe: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. Az a **azonosító** szövegmező, típus: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Ne feledje, hogy ezek nincsenek a valódi értékek. Akkor frissítheti ezeket az értékeket, és a tényleges URL-cím bejelentkezési azonosítója. Itt javasoljuk, hogy az azonosító URL-címe egyedi értékét használja. Ügyfél [Zscaler személyes hozzáférési (ZPA) támogatási csoport](https://help.zscaler.com/zpa-submit-ticket) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **hozzon létre új tanúsítvány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_400.png)     

5. A a **új tanúsítvány létrehozása** párbeszédpanel, kattintson a naptár ikonra, és válasszon egy **lejárati dátum**. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **új tanúsítvány aktiválásához** kattintson **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. Az előugró **helyettesítő tanúsítvány** ablak, kattintson a **OK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. Egy másik webes böngészőablakban jelentkezzen be a Zscaler személyes hozzáférési (ZPA) vállalati webhely rendszergazdaként.

10. Navigáljon a **rendszergazda** majd **Idp konfigurációs**.

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. Az a **Idp konfigurációs** kattintson **hozzáadása új IDP konfigurációs**.

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. Az a **új IDP konfigurációs** területen tegye a következőket:

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Kattintson a **fájl kiválasztása** , és töltse fel a letöltött metaadatait tartalmazó fájl.

    b. Kattintson a **mentése** gombra.
    


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Zscaler személyes hozzáférési (ZPA) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon a Zscaler személyes hozzáférési (ZPA) nevű felhasználót hoz létre. Adjon együttműködve [Zscaler személyes hozzáférési (ZPA) támogatási csoport](https://help.zscaler.com/zpa-submit-ticket) a felhasználók hozzáadása a Zscaler személyes hozzáférési (ZPA) platform.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon saját hozzáférés biztosítása a Zscaler személyes hozzáférési (ZPA) által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**A Zscaler személyes hozzáférési (ZPA) Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Zscaler személyes hozzáférési (ZPA)**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Zscaler személyes hozzáférési (ZPA) csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Zscaler személyes hozzáférési (ZPA) alkalmazáshoz.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccess-tutorial/tutorial_general_203.png