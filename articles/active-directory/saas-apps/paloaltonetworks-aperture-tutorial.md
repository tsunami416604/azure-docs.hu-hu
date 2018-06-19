---
title: 'Oktatóanyag: Azure Active Directoryval integrált Palo Alto hálózatok - nyílás |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Palo Alto hálózatok - nyílás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 0b8e5dd58736c0ad27ea45bd09e21acbdb0a27ac
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923362"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Oktatóanyag: Azure Active Directoryval integrált Palo Alto hálózatok - nyílás

Ebben az oktatóanyagban elsajátíthatja Palo Alto hálózatok - nyílás, az Azure Active Directoryval (Azure AD) integrálása.

Palo Alto hálózatok - nyílás, az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Palo Alto hálózatok - nyílás szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Palo Alto hálózatokhoz - nyílás (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Palo Alto hálózatokkal - nyílás, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Palo Alto hálózatok - nyílás egyszeri bejelentkezés engedélyezve az előfizetéshez

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Palo Alto hálózatok - nyílás a gyűjteményből hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Palo Alto hálózatok - nyílás a gyűjteményből hozzáadása
Palo Alto hálózatok - nyílás, az Azure AD-integráció konfigurálása kell hozzáadnia a Palo Alto hálózatok - nyílás a gyűjteményből, a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Palo Alto hálózatok - nyílás a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Palo Alto hálózatok - nyílás**, jelölje be **Palo Alto hálózatok - nyílás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Palo Alto hálózatok - nyílás, az eredmények listájában](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD az egyszeri bejelentkezés Palo Alto hálózatokkal tesztelése és konfigurálása,-nyílás "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Palo Alto hálózatokban - nyílás a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Palo Alto hálózatokban - hivatkozás kapcsolatának nyílás kell létrehozni.

Konfigurálása és tesztelése az Azure AD az egyszeri bejelentkezés Palo Alto hálózatokkal - nyílás, kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Palo Alto hálózatok - nyílás tesztfelhasználó létrehozása](#create-a-palo-alto-networks---aperture-test-user)**  - rendelkezik egy megfelelője a Britta Simon Palo Alto hálózatokból - nyílás, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Palo Alto hálózatokon - nyílás alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD konfigurálása egyszeri bejelentkezéshez Palo Alto hálózatokkal - nyílás, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Palo Alto hálózatok - nyílás** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. Az a **Palo Alto hálózatok - nyílás tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Palo Alto hálózatok - nyílás tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Palo Alto hálózatok - nyílás tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [Palo Alto hálózatok - nyílás ügyfél-támogatási csoport](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) beolvasni ezeket az értékeket. 

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. Az a **Palo Alto hálózatok - nyílás konfigurációs** kattintson **konfigurálása Palo Alto hálózatok - nyílás** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![A konfigurálás hivatkozás](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. Egy másik webes böngészőablakban, Palo Alto hálózatokhoz - nyílás rendszergazdaként való bejelentkezés.

9. A felső menüsávban kattintson **beállítások**.

    ![A beállítások lap](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Navigáljon a **alkalmazás** szakaszban kattintson **hitelesítési** bal oldalán található menüben alkotnak.

    ![A hitelesítési lapon](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. Az a **hitelesítési** lapon hajtsa végre a következő lépéseket:
    
    ![A hitelesítési lapon](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Ellenőrizze a **engedélyezése egyszeri bejelentkezési-On(Supported SSP Providers are Okta, Onelogin)** a **egyszeri bejelentkezés** mező.

    b. Az a **identitás Szolgáltatóazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely Azure-portálon másolta.

    c. Kattintson a **Choose File** az Azure AD-t a letöltött tanúsítvány feltöltése a **szolgáltató Identitástanúsítvány** mező.

    d. A a **identitási szolgáltató egyszeri bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    e. Ellenőrizze a kiállító terjesztési hely **nyílás Info** szakaszt, és töltse le a tanúsítványt a **nyílás kulcs** mező.

    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Palo Alto hálózatok - nyílás tesztfelhasználó létrehozása

Ebben a szakaszban egy Palo Alto hálózatok - nyílás Britta Simon nevű felhasználót hoz létre. Együttműködve [Palo Alto hálózatok - nyílás ügyfél-támogatási csoport](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) a felhasználók hozzáadása a Palo Alto hálózatok - nyílás platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Palo Alto hálózatok - nyílás Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Palo Alto hálózatok - nyílás, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Palo Alto hálózatok - nyílás**.

    ![Palo Alto hálózatok - nyílás hivatkozásra az alkalmazások listáját](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Palo Alto hálózatok - nyílás csempe a hozzáférési panelen kattintva meg kell beolvasni automatikusan bejelentkezett a Palo Alto hálózatokhoz - nyílás alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

