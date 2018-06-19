---
title: 'Oktatóanyag: Azure Active Directoryval integrált Veracode |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Veracode között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 3a6818c65b461dd989b1516191b845811642608d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926203"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Oktatóanyag: Azure Active Directoryval integrált Veracode

Ebben az oktatóanyagban elsajátíthatja Veracode integrálása az Azure Active Directory (Azure AD).

Veracode integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Veracode szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Veracode (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Veracode, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Veracode egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Adja hozzá a Veracode a gyűjteményből
2. Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-veracode-from-the-gallery"></a>Adja hozzá a Veracode a gyűjteményből
Az Azure AD integrálása a Veracode konfigurálásához kell hozzáadnia Veracode a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Veracode hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Veracode**, jelölje be **Veracode** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Veracode](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Veracode.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Veracode a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Veracode közötti kapcsolat kapcsolatot kell létrehozni.

Veracode, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Veracode tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Veracode tesztfelhasználó létrehozása](#create-a-veracode-test-user)**  - való Britta Simon valami Veracode, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Veracode alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Veracode, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Veracode** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

3. Az a **Veracode tartomány és az URL-címek** szakaszban, a felhasználó nem rendelkezik, az alkalmazás már előre integrálva van az Azure-ral bármely lépések végrehajtásához. 

    ![Egyszeri bejelentkezés konfigurálása](./media/veracode-tutorial/tutorial_veracode_url.png)

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

5. Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez Veracode fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.

    A Veracode alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban, amelyhez egyéni attribútum leképezései hozzáadása a **saml-jogkivonat attribútumok** konfigurációs. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Attribútumok](./media/veracode-tutorial/tutorial_veracode_attr.png "attribútumok")

6. A kötelező attribútum-leképezésekhez hozzáadásához hajtsa végre az alábbi lépéseket:

    | Attribútum neve | Attribútum értéke |
    |--- |--- |
    | Utónév |User.givenName |
    | Vezetéknév |User.surname |
    | e-mailben |User.mail |
    
    a. Kattintson a fenti adatokat minden egyes sorhoz kapcsolódóan **hozzáadása a felhasználói attribútum**.
    
    ![Attribútumok](./media/veracode-tutorial/tutorial_veracode_addattr.png "attribútumok")
    
    ![Attribútumok](./media/veracode-tutorial/tutorial_veracode_addattr1.png "attribútumok")
    
    b. Az a **attribútumnév** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **attribútumérték** szövegmező, válassza ki az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/veracode-tutorial/tutorial_general_400.png)

8. A a **Veracode konfigurációs** kattintson **konfigurálása Veracode** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** a a **rövid összefoglaló szakasz.**

    ![Veracode konfiguráció](./media/veracode-tutorial/tutorial_veracode_configure.png) 

9. Egy másik webes böngészőablakban jelentkezzen be a Veracode vállalati webhely rendszergazdaként.

10. Kattintson a felső menüben **beállítások**, és kattintson a **Admin**.
   
    ![Felügyeleti](./media/veracode-tutorial/ic802911.png "felügyeleti")

11. Kattintson a **SAML** fülre.

12. Az a **szervezeti SAML beállítások** területen tegye a következőket:
   
    ![Felügyeleti](./media/veracode-tutorial/ic802912.png "felügyeleti")
   
    a.  A **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.
    
    b. Kattintson az Azure-portálról letöltött tanúsítvány feltöltésének **Choose File**.
   
    c. Válassza ki **engedélyezze az önkiszolgáló regisztrációt**.

13. Az a **önkiszolgáló regisztrációs beállítások** szakaszt, hajtsa végre az alábbi lépéseket, és kattintson a **mentése**:
   
    ![Felügyeleti](./media/veracode-tutorial/ic802913.png "felügyeleti")
   
    a. Mint **új felhasználó aktiválása**, jelölje be **nem aktiválási szükséges**.
   
    b. Mint **felhasználói adatfrissítések**, jelölje be **preferencia Veracode felhasználói adatok**.
   
    c. A **SAML attribútum adatai**, jelölje be az alábbiakat:
      * **Felhasználói szerepkörök**
      * **Csoportházirendet felügyelő rendszergazda**
      * **felülvizsgáló**
      * **Biztonsági vezető**
      * **Vezetői**
      * **Küldő**
      * **Létrehozó**
      * **Az ellenőrzési típusok**
      * **A csoporttagságot**
      * **Alapértelmezett csoport**

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/veracode-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/veracode-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/veracode-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/veracode-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-veracode-test-user"></a>Veracode tesztfelhasználó létrehozása
Ahhoz, hogy az Azure AD-felhasználók Veracode bejelentkezni, akkor ki kell építenie Veracode be. Veracode, ha egy automatizált feladat. Nincs művelet elem meg. Felhasználók automatikusan létrejönnek szükség esetén az első egy bejelentkezési kísérlet során.

> [!NOTE]
> Bármely más Veracode felhasználói fiók létrehozása eszközök vagy Veracode kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.
> 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Veracode Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Veracode, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Veracode**.

    ![Az alkalmazások listáját a Veracode hivatkozás](./media/veracode-tutorial/tutorial_veracode_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Veracode csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Veracode alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

