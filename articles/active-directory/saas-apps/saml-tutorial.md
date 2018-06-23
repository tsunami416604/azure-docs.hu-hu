---
title: 'Oktatóanyag: Azure Active Directoryval integrált SAML 1.1 Token LOB-alkalmazás engedélyezése |} Microsoft Docs'
description: Egyszeri bejelentkezés Azure Active Directory közötti konfigurálásával és SAML 1.1 Token LOB-alkalmazás engedélyezése.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: fca447b24a299fed116356ca0f985020e079344b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317602"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Oktatóanyag: Azure Active Directoryval integrált SAML 1.1 Token LOB-alkalmazás engedélyezése

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás Azure Active Directory (Azure AD).

SAML 1.1 Token integrálása engedélyezett LOB-alkalmazás az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér SAML 1.1 Token LOB-alkalmazás engedélyezése.
- Engedélyezheti a felhasználók automatikusan el bejelentkezett a SAML 1.1 Token LOB-alkalmazás engedélyezése (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálhatja az Azure AD-integrációs SAML 1.1 jogkivonat a LOB-alkalmazás engedélyezve van, a következőkre van szüksége:

- Az Azure AD szolgáltatásra
- A SAML 1.1 Token LOB-alkalmazás egyszeri bejelentkezés engedélyezett előfizetés engedélyezve

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. SAML 1.1 Token hozzáadása engedélyezett LOB-alkalmazás a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>SAML 1.1 Token hozzáadása engedélyezett LOB-alkalmazás a gyűjteményből
SAML 1.1 Token integrációjának konfigurálása LOB-alkalmazás engedélyezése az Azure AD-be, hozzá kell adnia a SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**SAML 1.1 jogkivonat engedélyezve van a gyűjteményből LOB-alkalmazás hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás**, jelölje be **SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás az eredménylistában](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez a SAML 1.1 Token "Britta Simon" nevű tesztfelhasználó alapján engedélyezett LOB-alkalmazás.

Az egyszeri bejelentkezés működéséhez az Azure AD a SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás az Azure AD-hoz a felhasználó a párjukhoz felhasználó tudnia kell. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SAML 1.1 Token hivatkozás kapcsolatának engedélyezve LOB-alkalmazás kell létrehozni.

Konfigurálása és tesztelése az Azure AD egyszeri bejelentkezéshez a SAML 1.1 Token engedélyezett a LOB-alkalmazás, végre kell hajtania a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy SAML 1.1 jogkivonat engedélyezve LOB-alkalmazás tesztfelhasználó](#create-a-saml-11-token-enabled-lob-app-test-user)**  - kell rendelkeznie a SAML 1.1 Token Britta Simon valami engedélyezve van a LOB-alkalmazás, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez a SAML 1.1 jogkivonat a LOB-alkalmazás-alkalmazás engedélyezve.

**Konfigurálhatja az Azure AD egyszeri bejelentkezéshez a SAML 1.1 jogkivonat engedélyezve van a LOB-alkalmazás, hajtsa végre a következő lépéseket:**

1. Az Azure portálon a a **SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/saml-tutorial/tutorial_saml_samlbase.png)

3. Az a **SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás tartomány és az URL-címek** területen tegye a következőket:

    ![SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás tartomány és az URL-címek egyetlen bejelentkezés információk](./media/saml-tutorial/tutorial_saml_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://your-app-url`

    b. Az a **azonosítója (entitás azonosítója)** szövegmező, adja meg a következő minta használatával URL-címe: `https://your-app-url`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. A megadott URL-címeket, cserélje le ezeket az értékeket.  

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/saml-tutorial/tutorial_saml_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/saml-tutorial/tutorial_general_400.png)
    
7. A a **SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás-konfigurációs** kattintson **SAML 1.1 Token konfigurálása engedélyezett LOB-alkalmazás** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás konfigurációja](./media/saml-tutorial/tutorial_saml_configure.png) 

8. Egyszeri bejelentkezés konfigurálása **SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás** oldalon kell küldeniük a letöltött **tanúsítvány (Base64), a Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** számára alkalmazás támogatási csapatával. Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/saml-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/saml-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/saml-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/saml-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Hozzon létre egy SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás tesztfelhasználó számára

Ebben a szakaszban Britta Simon nevű felhasználó létrehozása SAML 1.1 Token LOB-alkalmazás engedélyezése. Az alkalmazás használata támogatási csoport alkalmazás oldalán a felhasználó létrehozásához. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Azure használandó Britta Simon engedélyezése egyszeri bejelentkezéshez által biztosított hozzáférés SAML 1.1 jogkivonat engedélyezve van a LOB-alkalmazás.

![A felhasználói szerepkör hozzárendelése][200] 

**LOB-alkalmazás engedélyezése Britta Simon hozzárendelése SAML 1.1 Token, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAML 1.1 jogkivonat engedélyezett LOB-alkalmazás**.

    ![A SAML 1.1 jogkivonat engedélyezett alkalmazáshivatkozása LOB-alkalmazások listájában](./media/saml-tutorial/tutorial_saml_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Kattintva a SAML 1.1 jogkivonat engedélyezve a hozzáférési panelen LOB-alkalmazás csempéjére, kapja meg automatikusan bejelentkezett a LOB-alkalmazás-alkalmazás a SAML 1.1 jogkivonat engedélyezve.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
