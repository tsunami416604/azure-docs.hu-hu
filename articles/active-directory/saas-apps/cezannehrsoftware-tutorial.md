---
title: 'Oktatóanyag: Azure Active Directory-integráció Cezanne HR szoftverrel |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Cezanne HR szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: a1e7d2ee41c9334c5dc5c64014d5bb2946bfe865
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35925393"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Oktatóanyag: Azure Active Directory-integráció Cezanne HR szoftverrel

Ebben az oktatóanyagban elsajátíthatja Cezanne HR szoftver integrálása az Azure Active Directory (Azure AD).

Cezanne HR szoftver integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Cezanne HR szoftver szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Cezanne HR szoftverre (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Cezanne HR szoftverrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Cezanne HR szoftver egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Cezanne HR szoftver hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>A gyűjteményből Cezanne HR szoftver hozzáadása
Az Azure AD integrálása a Cezanne HR szoftver konfigurálásához kell hozzáadnia Cezanne HR szoftver a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Cezanne HR szoftver hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Cezanne HR szoftver**, jelölje be **Cezanne HR szoftver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Cezanne HR szoftver](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Cezanne HR szoftverrel.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Cezanne HR szoftver a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Cezanne HR szoftver közötti kapcsolat kapcsolatot kell létrehozni.

Cezanne HR szoftverfrissítési, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Cezanne HR szoftverrel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Cezanne HR szoftver tesztfelhasználó létrehozása](#create-a-cezannehrsoftware-test-user)**  - való egy megfelelője a Britta Simon Cezanne HR szoftver, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Cezanne HR alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Cezanne HR szoftverrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Cezanne HR szoftver** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Az a **Cezanne HR szoftver tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Cezanne HR szoftver tartomány és az URL-címek](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Az a **azonosító** szövegmező, írja be az URL-cím: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Az a **válasz URL-CÍMEN** szövegmező, írja be az URL-cím: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és a válasz URL-CÍMEN. Ügyfél [Cezanne HR szoftver ügyfél-támogatási csoport](https://cezannehr.com/services/support/) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

6. A a **Cezanne HR szoftverkonfigurációt** kattintson **Cezanne HR szoftver konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak.

    ![Cezanne HR szoftverkonfigurációjának összeállítása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. Görgessen le a **rövid összefoglaló** szakasz. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe és SAML Entitásazonosító** a a **rövid összefoglaló szakasz.**

    ![Cezanne HR szoftverkonfigurációjának összeállítása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. Egy másik webes böngészőablakban bejelentkezés a Cezanne HR szoftver bérlő rendszergazdaként.

9. A bal oldali navigációs ablaktábláján kattintson **rendszerbeállítás**. Ugrás a **biztonsági beállítások**. Majd keresse meg a **egyszeri bejelentkezés konfigurációs**.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. Az a **engedélyezése a felhasználóknak, hogy jelentkezzen be a következő egyszeri bejelentkezés (SSO) szolgáltatás** panelen ellenőrizze a **SAML 2.0** mezőbe, majd válassza ki a **speciális konfiguráció** lehetőséget.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. Kattintson a **új hozzáadása** gombra.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. Hajtsa végre a következő lépéseket **SAML 2.0 IDENTITÁS-SZOLGÁLTATÓKTÓL** szakasz.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Adja meg az identitásszolgáltató, mint a **megjelenített név**.

    b. Az a **entitásazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** , amely az Azure portálról másolta. 

    c. Módosítsa a **SAML kötés** "Post".

    d. Az a **biztonsági jogkivonat szolgáltatásvégpont** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** , amely az Azure portálról másolta.

    e. Adja meg az ID attribútum felhasználónév szövegmezőjének `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Kattintson a **feltöltése** ikonra, töltse fel az Azure-portálról letöltött tanúsítványt.
    
    g. Kattintson az **OK** gombra. 

13. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Cezanne HR szoftver tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Cezanne HR szoftver bejelentkezni, akkor ki kell építenie Cezanne HR szoftver. Cezanne HR szoftvert, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1.  Jelentkezzen be a Cezanne HR szoftver vállalati webhely rendszergazdaként.

2.  A bal oldali navigációs ablaktábláján kattintson **rendszerbeállítás**. Ugrás a **felhasználók kezelése**. Majd keresse meg a **új felhasználó hozzáadása**.

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "új felhasználó")

3.  A **személy adatai** csoportjában hajtsa végre a következő lépések:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "új felhasználó")
    
    a. Állítsa be **belső felhasználói** , OFF.
    
    b. Az a **Utónév** szövegmező, a felhasználó utónevét típusát, például **Britta**.  
 
    c. Az a **Vezetéknév** szövegmező, a felhasználó vezetékneve típusát, például **Simon**.
    
    d. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

4.  A **fiókadatok** csoportjában hajtsa végre a következő lépések:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "új felhasználó")
    
    a. Az a **felhasználónév** szövegmezőben, az e-mailt a felhasználó típusát, például Brittasimon@contoso.com.
    
    b. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.
    
    c. Válassza ki **HR Professional** , **biztonsági szerepkör**.
    
    d. Kattintson az **OK** gombra.

5. Navigáljon a **egyszeri bejelentkezés** lapot, és válasszon **új hozzáadása** a a **SAML 2.0 azonosítók** területen.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "felhasználó")

6. Válassza ki az identitásszolgáltató az a **identitásszolgáltató** és a szövegmezőben a **felhasználói azonosító**, adja meg a Britta Simon fiókjának e-mail címe.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "felhasználó")
    
7. Kattintson a **mentése** gombra.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "felhasználó")

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés Cezanne HR szoftver használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Cezanne HR szoftver, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Cezanne HR szoftver**.

    ![Az alkalmazások listáját a Cezanne HR szoftver hivatkozás](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Cezanne HR szoftver csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Cezanne HR szoftverfrissítések alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

