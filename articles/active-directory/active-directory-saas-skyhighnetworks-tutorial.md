---
title: 'Oktatóanyag: Azure Active Directoryval integrált Skyhigh hálózatok |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Skyhigh hálózatok között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: jeedes
ms.openlocfilehash: fc90d34853a584e9de65f923dfe267835e89d167
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34804832"
---
# <a name="tutorial-azure-active-directory-integration-with-skyhigh-networks"></a>Oktatóanyag: Azure Active Directory-integráció Skyhigh hálózatokkal

Ebben az oktatóanyagban elsajátíthatja Skyhigh hálózatok integrálása az Azure Active Directory (Azure AD).

Skyhigh hálózatok integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Skyhigh hálózatok szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Skyhigh hálózatokhoz (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Skyhigh hálózatok, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Skyhigh hálózatok egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Skyhigh hálózatokat a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-skyhigh-networks-from-the-gallery"></a>Skyhigh hálózatokat a gyűjteményből
Az Azure AD integrálása a Skyhigh hálózatok konfigurálásához szüksége Skyhigh hálózatokat adhat a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Skyhigh hálózatokat adhat a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Skyhigh hálózatok**, jelölje be **Skyhigh hálózatok** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Skyhigh hálózatok](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_skyhighnetworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó Skyhigh hálózatokat.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Skyhigh hálózatokban a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó Skyhigh hálózatok közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés Skyhigh hálózatokkal tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Skyhigh hálózatok tesztfelhasználó létrehozása](#create-a-skyhigh-networks-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon Skyhigh hálózatokban, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Skyhigh hálózatok alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Skyhigh hálózatokkal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Skyhigh hálózatok** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_skyhighnetworks_samlbase.png)

3. Az a **Skyhigh hálózatok tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Skyhigh hálózatok tartomány és az URL-címek](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_skyhighnetworks_url.png)

    a. Az a **azonosítója (entitás azonosítója)** szövegmező, adja meg a következő minta használatával URL-címe: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Skyhigh hálózatok tartomány és az URL-címek](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_skyhighnetworks_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [Skyhigh hálózatok ügyfél-támogatási csoport](mailto:support@skyhighnetworks.com) beolvasni ezeket az értékeket. 

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_skyhighnetworks_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_400.png)
    
7. Az a **Skyhigh hálózatok konfigurációs** kattintson **Skyhigh hálózatok konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Skyhigh hálózatok konfigurálása](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_skyhighnetworks_configure.png) 

8. Egyszeri bejelentkezés konfigurálása **Skyhigh hálózatok** oldalon kell küldeniük a letöltött **tanúsítvány (Base64), a Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** való [ Skyhigh hálózatok támogatási csoport](mailto:support@skyhighnetworks.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-skyhighnetworks-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-skyhighnetworks-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-skyhighnetworks-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-skyhighnetworks-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-skyhigh-networks-test-user"></a>Skyhigh hálózatok tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon meghívta Skyhigh hálózatok felhasználó létrehozása. Együttműködve [Skyhigh hálózatok támogatási csoport](mailto:support@skyhighnetworks.com) a felhasználók hozzáadása a Skyhigh hálózatok platform. Felhasználók kell létrehoznia és egyszeri bejelentkezés használata előtt aktiválva

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó Skyhigh hálózatokhoz való hozzáférés biztosítása.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Skyhigh hálózatok, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Skyhigh hálózatok**.

    ![Az alkalmazások listáját a Skyhigh hálózatok hivatkozás](./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_skyhighnetworks_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Skyhigh hálózatok csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Skyhigh hálózatok alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skyhighnetworks-tutorial/tutorial_general_203.png

