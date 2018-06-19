---
title: 'Oktatóanyag: Azure Active Directoryval integrált Elium |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Elium között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: 9cd607e3c35ae0769c3959e8cf30a4a466f7e3c5
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927717"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Oktatóanyag: Azure Active Directoryval integrált Elium

Ebben az oktatóanyagban elsajátíthatja Elium integrálása az Azure Active Directory (Azure AD).

Elium integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Elium szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Elium (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Elium, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Elium egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Elium hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-elium-from-the-gallery"></a>A gyűjteményből Elium hozzáadása
Az Azure AD integrálása a Elium konfigurálásához kell hozzáadnia Elium a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Elium hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Elium**, jelölje be **Elium** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Elium](./media/elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Elium.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Elium a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Elium közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Elium tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Elium tesztfelhasználó](#create-an-elium-test-user)**  - való Britta Simon valami Elium, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Elium alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Elium, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Elium** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/elium-tutorial/tutorial_elium_samlbase.png)

3. Az a **Elium tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Elium tartomány és az URL-címek](./media/elium-tutorial/tutorial_elium_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<platform-domain>.elium.com/login/saml2/acs`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Elium tartomány és az URL-címek](./media/elium-tutorial/tutorial_elium_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Ezeket az értékeket az elérhetővé válik a **SP metaadatfájl** letölthető a következő `https://<platform-domain>.elium.com/login/saml2/metadata`, amely esetén, tekintse meg az oktatóanyag későbbi részében.

5. A Elium alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/tutorial_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző ábrán látható módon, és hajtsa végre a következő lépéseket:
           
    | Attribútum neve | Attribútum értéke |   
    | ---------------| ----------------|
    | e-mailben   |user.mail |
    | first_name| User.givenName |
    | last_name| User.surname|
    | job_title| user.jobtitle|
    | Vállalati| User.CompanyName|
    
    > [!NOTE]
    > Ezek azok az alapértelmezett jogcímeket. **Csak e-mail jogcím szükség**. Jogcím kiépítése is csak az e-mailek JIT megadása kötelező. Egy másik felhasználói platformra más egyéni jogcím egy ügyfél platform változhat.

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/tutorial_attribute_04.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/tutorial_attribute_05.png)

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Névtér hagyja üresen.
    
    e. Kattintson az **OK** gombra. 

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/elium-tutorial/tutorial_elium_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/elium-tutorial/tutorial_general_400.png)
    
7. Egy másik webes böngészőablakban jelentkezzen be a Elium vállalati webhely rendszergazdaként.

8. Kattintson a **felhasználói profil** jobb felső sarokban, majd válassza ki a **felügyeleti**.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user1.png)

9. Válassza ki **biztonsági** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user2.png)

10. Görgessen le a **egyszeri bejelentkezés (SSO)** szakaszt, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user3.png)

    a. Másolja értékének **győződjön meg arról, hogy egy SAML2 authentication működik, a fiók** és illessze be a **bejelentkezési URL-cím** a szövegmező a **Elium tartomány és az URL-címek** szakaszban az Azure-ban portál.

    > [!NOTE]
    > Miután SSO, mindig érhető el az alapértelmezett távoli bejelentkezési oldalt a következő URL-címen: `https://<platform_domain>/login/regular/login` 

    b. Válassza ki **engedélyezése egy SAML2 összevonási** jelölőnégyzetet.

    c. Válassza ki **JIT-kiépítés** jelölőnégyzetet.

    d. Nyissa meg a **SP metaadatok** parancsával a **letöltése** gombra.

    e. Keresse meg a **entityid beállítást** a a **SP metaadatok** fájlt, másolja a **entityid beállítást** értékét, és illessze be a **azonosító** a aszövegmező **Elium tartomány és az URL-címek** szakaszban az Azure portálon. 

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user4.png)

    f. Keresse meg a **AssertionConsumerService** a a **SP metaadatok** fájlt, másolja a **hely** értékét, és illessze be a **válasz URL-CÍMEN** a szövegmező a **Elium tartomány és az URL-címek** szakaszban az Azure portálon.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user5.png)

    g. Nyissa meg a letöltött metaadatfájl Azure-portálon a Jegyzettömbbe, másolja a tartalmat, és illessze be azt a **IdP metaadatok** szövegmező.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/elium-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/elium-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/elium-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/elium-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-elium-test-user"></a>Hozzon létre egy Elium tesztfelhasználó számára

Ez a szakasz célja Elium Britta Simon nevű felhasználót létrehozni. Elium támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az Elium elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [Elium támogatási csoport](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Elium Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Elium, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Elium**.

    ![Az alkalmazások listáját a Elium hivatkozás](./media/elium-tutorial/tutorial_elium_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Elium csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Elium alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/elium-tutorial/tutorial_general_01.png
[2]: ./media/elium-tutorial/tutorial_general_02.png
[3]: ./media/elium-tutorial/tutorial_general_03.png
[4]: ./media/elium-tutorial/tutorial_general_04.png

[100]: ./media/elium-tutorial/tutorial_general_100.png

[200]: ./media/elium-tutorial/tutorial_general_200.png
[201]: ./media/elium-tutorial/tutorial_general_201.png
[202]: ./media/elium-tutorial/tutorial_general_202.png
[203]: ./media/elium-tutorial/tutorial_general_203.png

