---
title: 'Oktatóanyag: Azure Active Directoryval integrált Proxyclick |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Proxyclick között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: 3fb6c034df38486dfefd0ffccc6e05db32d9087d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923779"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Oktatóanyag: Azure Active Directoryval integrált Proxyclick

Ebben az oktatóanyagban elsajátíthatja Proxyclick integrálása az Azure Active Directory (Azure AD).

Proxyclick integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Proxyclick szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Proxyclick (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Proxyclick, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Proxyclick egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Proxyclick hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-proxyclick-from-the-gallery"></a>A gyűjteményből Proxyclick hozzáadása
Az Azure AD integrálása a Proxyclick konfigurálásához kell hozzáadnia Proxyclick a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Proxyclick hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Proxyclick**, jelölje be **Proxyclick** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Proxyclick.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Proxyclick a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Proxyclick közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Proxyclick tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Proxyclick tesztfelhasználó létrehozása](#create-a-proxyclick-test-user)**  - való Britta Simon valami Proxyclick, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Proxyclick alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Proxyclick, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Proxyclick** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

3. Az a **Proxyclick tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Proxyclick tartomány és az URL-címek](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://saml.proxyclick.com/init/<companyId>`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://saml.proxyclick.com/consume/<companyId>`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Proxyclick tartomány és az URL-címek](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. A tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím, az oktatóanyag későbbi részében ismertetett később frissíteni ezeket az értékeket.

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/proxyclick-tutorial/tutorial_general_400.png)

7. A a **Proxyclick konfigurációs** kattintson **konfigurálása Proxyclick** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Proxyclick konfiguráció](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

8. Egy másik webes böngészőablakban jelentkezzen be a Proxyclick vállalati webhely rendszergazdaként.

9. Válassza ki **fiók & beállítások**.

    ![Proxyclick konfiguráció](./media/proxyclick-tutorial/configure1.png)

10. Görgessen le a **integrációja** válassza **SAML**.

    ![Proxyclick konfiguráció](./media/proxyclick-tutorial/configure2.png)

11. Az a **SAML** területen tegye a következőket:

    ![Proxyclick konfiguráció](./media/proxyclick-tutorial/configure3.png)

    a. Másolás **SAML-alapú ügyfél URL-címe** értékét, és illessze be azt **válasz URL-CÍMEN** textbox **Proxyclick tartomány és az URL-címek** Azure-portál szakaszban.

    b. Másolás **SAML SSO átirányítási URL-cím** értékét, és illessze be azt **bejelentkezési URL-cím** és **azonosító** a szövegmezőből **Proxyclick tartomány és az URL-címek** szakasz Azure-portál.

    c. Válassza ki **SAML-kérelmet metódus** , **HTTP-átirányítás**.

    d. Az a **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** értéket, amely az Azure-portálon másolta.

    e. Az a **SAML 2.0 végponti URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** átmásolja az Azure-portálon.

    f. Nyissa meg a letöltött fájlt a Jegyzettömbben az Azure portálról, és illessze be azt a **tanúsítvány** szövegmező.

    g. Kattintson a **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/proxyclick-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/proxyclick-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/proxyclick-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-proxyclick-test-user"></a>Proxyclick tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Proxyclick bejelentkezni, akkor ki kell építenie a Proxyclick. Proxyclick, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Proxyclick vállalati webhely.

2. Kattintson a **munkatársak** a felső navigációs sáv a.

    ![Alkalmazott hozzáadása](./media/proxyclick-tutorial/user1.png)

3. Kattintson a **munkatársát hozzáadása**

    ![Alkalmazott hozzáadása](./media/proxyclick-tutorial/user2.png)

4. Az a **hozzáadása egy munkatársát** területen tegye a következőket:

    ![Alkalmazott hozzáadása](./media/proxyclick-tutorial/user3.png)

    a. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például **brittasimon@contoso.com**.

    b. Az a **Keresztnév** szövegmezőhöz felhasználói Britta például az első nevét.

    c. Az a **Vezetéknév** szövegmező, írja be például Simon felhasználó vezetékneve.

    d. Kattintson a **felhasználó hozzáadása**.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Proxyclick Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése Proxyclick, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Proxyclick**.

    ![Az alkalmazások listáját a Proxyclick hivatkozás](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Proxyclick csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Proxyclick alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

