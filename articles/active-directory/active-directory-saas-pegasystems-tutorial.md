---
title: "Oktatóanyag: Azure Active Directory-integráció Pega rendszerekkel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Pega rendszerek között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: ffc3d94416378f6469eaa574dc10880ef840e91f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Oktatóanyag: Azure Active Directory-integráció Pega rendszerekkel

Ebben az oktatóanyagban elsajátíthatja Pega rendszerek integrálása az Azure Active Directory (Azure AD).

Pega rendszerek integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Pega rendszerek szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Pega rendszerekre (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Pega rendszerekkel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Pega rendszerek egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Pega rendszerek hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-pega-systems-from-the-gallery"></a>A gyűjteményből Pega rendszerek hozzáadása
Az Azure AD integrálása a Pega rendszerek konfigurálásához kell hozzáadnia Pega rendszerek a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Pega rendszerek hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Pega rendszerek**, jelölje be **Pega rendszerek** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Pega rendszerek](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó Pega rendszerekből.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Pega rendszerekben a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Pega rendszerek közötti kapcsolat kapcsolatot kell létrehozni.

Pega rendszerekben, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Pega rendszerekkel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Pega rendszerek tesztfelhasználó létrehozása](#create-a-pega-systems-test-user)**  - való egy megfelelője a Britta Simon Pega rendszerek, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Pega Systems alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Pega rendszerekkel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Pega rendszerek** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Az a **Pega rendszerek tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Pega rendszerek tartomány és az URL-címek](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Pega rendszerek tartomány és az URL-címek](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    Az a **továbbítási állapotot** szövegmező, adja meg a következő minta használatával URL-címe:`https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és továbbító állapot URL-CÍMÉT. Az oktatóanyag későbbi részében ismertetett Pega-alkalmazás azonosítója és a válasz URL-CÍMEN értékének megkeresése A továbbító állapothoz, lépjen kapcsolatba [Pega rendszerek ügyfél-támogatási csoport](https://www.pega.com/contact-us) az értéket be kell olvasni. 

5. A Pega rendszerek alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Ezeket a jogcímeket adott ügyfelünk, és a követelmény függ. A rendszer például következő választható jogcímeket csak amely konfigurálhatja az alkalmazás. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum-érték |
    | ------------------- | -------------------- |    
    | egyedi azonosítója | *********** |
    | CN  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | Szervezet | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Munkacsoport | *********** |
    | Telefonszám | *********** |

    > [!NOTE]
    > Ezek a felhasználói adott értékekre. Adja meg a megfelelő értékeket.

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

7. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. Egyszeri bejelentkezés konfigurálása **Pega rendszerek** oldalán, nyissa meg a **Pega Portal** egy másik böngészőablakban rendszergazdai fiókkal.

10. Válassza ki **létrehozása** -> **SysAdmin** -> **hitelesítési szolgáltatás**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. A következő műveletek végrehajtása **Aauthentication szolgáltatás létrehozása** képernyőjén:

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Válassza ki **SAML 2.0** típusból

    b. Az a **neve** szövegmezőhöz bármely neve például Azure AD SSO megadása

    c. Az a **rövid leírás** szövegmező, adja meg a leírás  

    d. Kattintson a **létrehozása és megnyitása** 
    
12. A **identitásszolgáltató (IdP) információt** területen kattintson a **importálási IdP metaadatok** , és keresse meg a metaadat-fájlnak, mely Azure-portálról letöltött. Kattintson a **Submit** a metaadatok betöltése.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Ez a kiállító terjesztési hely adatok feltölti, alább látható módon.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. A következő műveletek végrehajtása **Service Provider (SP) beállítások** szakasz:

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Másolás a **entitás azonosítója** értékét, és illessze be vissza az Azure portálon **azonosító** szövegmező.

    b.  Másolás a **helyességi feltétel fogyasztói Service (ACS) hely** értékét, és illessze be vissza az Azure portálon **válasz URL-CÍMEN** szövegmező.

    c. Válassza ki **tiltsa le a kérelem-aláírás**.

15. Kattintson a **mentése**
    
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-pega-systems-test-user"></a>Pega rendszerek tesztfelhasználó létrehozása

Ez a szakasz célja Pega rendszerek Britta Simon nevű felhasználót létrehozni. Adjon együttműködve [Pega rendszerek ügyfél-támogatási csoport](https://www.pega.com/contact-us) Pega Sysyems a felhasználók létrehozásához.


### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Pega rendszerek Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Pega rendszerek, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Pega rendszerek**.

    ![Az alkalmazások listáját a Pega rendszerek hivatkozás](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Pega rendszerek csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Pega rendszerek alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

