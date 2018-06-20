---
title: 'Oktatóanyag: Azure Active Directoryval integrált Salesforce védőfal |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Salesforce védőfal között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 3d362228406b71e01bf74aa6082a268d4b603830
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219040"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Oktatóanyag: Azure Active Directoryval integrált Salesforce védőfal

Ebben az oktatóanyagban elsajátíthatja Salesforce védőfal integrálása az Azure Active Directory (Azure AD).

Salesforce védőfal integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Azt is szabályozhatja az Azure AD, aki hozzáfér a Salesforce védőfal felé.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Salesforce védőfal (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce védőfal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Salesforce védőfal egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Salesforce védőfal hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce védőfal hozzáadása a gyűjteményből
Az Azure AD integrálása a Salesforce védőfal konfigurálásához kell hozzáadnia Salesforce védőfal a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Vegye fel a Salesforce védőfal a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Salesforce védőfal**, jelölje be **Salesforce védőfal** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Salesforce védőfal](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása és tesztelése az Azure AD egyszeri bejelentkezést a Salesforce védőfal "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Salesforce védőfal tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Salesforce védőfal közötti kapcsolat kapcsolatot kell létrehozni.

A Salesforce védőfal, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Salesforce védőfal tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Salesforce védőfal tesztfelhasználó létrehozása](#create-a-salesforce-sandbox-test-user)**  - való Britta Simon egy megfelelője a Salesforce védőfal, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Salesforce védőfal alkalmazásban egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Salesforce védőfal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Salesforce védőfal** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Az a **Salesforce védőfal tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Salesforce védőfal tartomány és az URL-címek](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. Az a **azonosító** szövegmező, írja be az értéket a következő minta használatával: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Salesforce ügyfél-támogatási csoport](https://help.salesforce.com/support) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

6. A a **Salesforce védőfal konfigurációs** kattintson **konfigurálása Salesforce védőfal** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. Új lap megnyitása a böngészőben, és jelentkezzen be a Salesforce védőfal rendszergazdai fiókjával.

8. Kattintson a **telepítő** alatt **beállítások ikonra** az oldal jobb felső sarkában a.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

9. Görgessen le a **beállítások** navigációs ablaktábláján kattintson **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállítások**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

11. A SAML-alapú egyszeri bejelentkezés beállítások konfigurálásához kattintson **új**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. A SAML egyszeri bejelentkezés beállítások szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. Az a **neve** szövegmező, írja be a konfiguráció nevét (például: *SPSSOWAAD_Test*). 

    b. Az a **kibocsátó** mezőbe illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta

    c. Az a **entitásazonosító** szövegmezőhöz típus `https://<instancename>--Sandbox.<entityid>.my.salesforce.com` Salesforce védőfal elsősorban a könyvtárhoz hozzáadandó esetén. Ha már van egy példánya Salesforce védőfal, majd a a **Entitásazonosító** írja be a **URL-cím bejelentkezési**, amely a következő formátumban kell lennie: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    d. Töltse fel a **szolgáltató Identitástanúsítvány**, kattintson a **Choose File** keresse meg és jelölje ki a tanúsítványfájlt, amely az Azure-portálról letöltött.

    e. Mint **SAML identitástípus**, a következő lehetőségek közül választhat:

      * Válassza ki **helyességi feltételt tartalmaz, a felhasználónév Salesforce**, ha a SAML-előfeltétel szolgáltatóé, a Salesforce felhasználónév

      * Válassza ki **helyességi feltételt tartalmaz az összevonási azonosító felhasználó**, ha a felhasználó objektum összevonási azonosító van beadott SAML-előfeltétel

      * Válassza ki **helyességi feltételt tartalmaz a használja a User objektum azonosítója**, ha a SAML-előfeltétel szolgáltatóé, a felhasználó a felhasználói azonosító

    f. Mint **SAML-alapú identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentifier elemében van**.

    g. Mint **szolgáltató által kezdeményezett kérelem Szolgáltatáskötés**, jelölje be **HTTP POST**.

    h. A **Identity Provider bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    i. SFDC nem támogatja az SAML jelentkezzen ki.  A probléma megoldásához, illessze be `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` be azt a **Identity Provider kijelentkezési URL-cím** szövegmező.

    j. Kattintson a **Save** (Mentés) gombra.

### <a name="enable-your-domain"></a>A tartomány

Jelen szakaszban feltételezzük, hogy már létrehozta a tartományhoz.  További információkért lásd: [meghatározása saját tartomány neve](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Ahhoz, hogy a tartomány, hajtsa végre a következő lépéseket:**

1. A Salesforce bal oldali navigációs ablaktábláján kattintson **vállalati beállítások** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

   >[!NOTE]
   >Győződjön meg arról, hogy a tartomány megfelelően van konfigurálva.

2. Az a **hitelesítési konfigurációt** területen kattintson **szerkesztése**, majd, mint a **hitelesítési szolgáltatás**, válassza ki a nevét a SAML egyszeri bejelentkezés beállítása az előző szakaszt, és végül kattintson a **mentése**.

   ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

Amint egy tartományhoz, konfigurálva van, a felhasználók használjon bejelentkezni a Salesforce védőfal tartomány URL-CÍMÉT.

Ahhoz, hogy az URL-cím értékét, kattintson az előző szakaszban létrehozott SSO profilra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-salesforce-sandbox-test-user"></a>Salesforce védőfal tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználó Britta Simon nevű Salesforce védőfal jön létre. Salesforce védőfal támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van. Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Salesforce védőfal, egy új létrejön a Salesforce védőfal elérésére tett kísérlet során. Salesforce védőfal is támogatja az automatikus a felhasználók átadása, további részletei [Itt](salesforce-sandbox-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférést biztosít a Salesforce védőfal által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése Salesforce védőfal, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Salesforce védőfal**.

    ![Az alkalmazások listáját a Salesforce védőfal hivatkozás](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A hozzáférési panelen a Salesforce védőfal csempére kattintva, meg kell beolvasni automatikusan bejelentkezett a Salesforce védőfal alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](salesforce-sandbox-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
