---
title: 'Oktatóanyag: Azure Active Directoryval integrált ív közzétételi - egyszeri bejelentkezés |} Microsoft Docs'
description: Ismerje meg, az egyszeri bejelentkezés Azure Active Directory és az ív közzétételi - egyszeri bejelentkezés konfigurálása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: bf811d789c0c6effd6f8940ad433092ea9ba04cb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210071"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Oktatóanyag: Azure Active Directoryval integrált ív közzétételi - egyszeri bejelentkezés

Ebben az oktatóanyagban elsajátíthatja ív közzétételi - egyszeri bejelentkezés az Azure Active Directoryval (Azure AD) integrálása.

Ív közzétételi - egyszeri bejelentkezés az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér ív közzétételi - SSO szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett ív közzétételi - egyszeri bejelentkezés (egyszeri bejelentkezés) az Azure AD-fiókok számára.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs ív közzétételi - egyszeri bejelentkezés, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy ív közzétételi - egyszeri bejelentkezés az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Ív közzététel - hozzáadásának SSO a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Ív közzététel - hozzáadásának SSO a gyűjteményből
Ív közzétételi - egyszeri bejelentkezés az Azure AD integrálása konfigurálása kell hozzáadnia ív közzétételi - SSO a gyűjteményből, ami a kezelt SaaS-alkalmazások listáját.

**Adja hozzá a ív közzétételi - egyszeri bejelentkezés a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **ív közzétételi - SSO**, jelölje be **ív közzétételi - SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A közzététel - egyszeri bejelentkezés az eredménylistában ívvé](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az ív közzétételi tesztelése és konfigurálása,-SSO "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés használatához az Azure AD meg kell tudni, hogy milyen a párjukhoz felhasználó ív közzétételi - egyszeri bejelentkezés egy felhasználó számára az Azure ad-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a ív közzétételi - hivatkozás kapcsolatának SSO kell létrehozni.

Az Azure AD egyszeri bejelentkezést az ív közzétételi - tesztelése és konfigurálása egyszeri Bejelentkezést, kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy ív közzétételi - SSO tesztfelhasználó](#create-an-arc-publishing---sso-test-user)**  - való egy megfelelője a Britta Simon ív közzétételi - egyszeri bejelentkezés, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a ív közzétételi - SSO-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD konfigurálása egyszeri bejelentkezéshez az ív közzétételi - SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ív közzétételi - SSO** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/arc-tutorial/tutorial_arc_samlbase.png)

3. Az a **ív közzétételi.-SSO tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Ív közzétételi - SSO tartomány és az URL-címek egyetlen bejelentkezési adatokat](./media/arc-tutorial/tutorial_arc_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Ív közzétételi - SSO tartomány és az URL-címek egyetlen bejelentkezési adatokat](./media/arc-tutorial/tutorial_arc_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [ív közzétételi - SSO ügyfél-támogatási csoport](mailto:inf@washpost.com) beolvasni ezeket az értékeket. 

5. Ív közzétételi - SSO alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_arc_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | Utónév | User.givenName |
    | Vezetéknév | User.surname |
    | e-mailben | user.mail |
    | csoportok | User.assignedroles |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

     ![Egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Hagyja a **Namespace** üres.
    
    d. Kattintson a **Ok**

    > [!NOTE]
    > Itt a **csoportok** attribútum le van képezve **user.assignedroles**. Ezek a egyéni szerepkörök létrehozása az Azure AD-vissza az alkalmazás a csoport nevét. További útmutatás található [Itt](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) létrehozásával egyéni szerepkörök az Azure ad-ben. 

7. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/arc-tutorial/tutorial_arc_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/arc-tutorial/tutorial_general_400.png)
    
9. Az a **ív közzétételi - SSO konfigurációs** területen kattintson **konfigurálása ív közzétételi - SSO** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Ív közzétételi - egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_arc_configure.png) 

10. Egyszeri bejelentkezés konfigurálása **ív közzétételi - egyszeri bejelentkezés** oldalon kell küldeniük a letöltött **tanúsítvány (Base64), a Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** való [ívvé A közzététel - SSO támogatási csoport](mailto:inf@washpost.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/arc-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/arc-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/arc-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/arc-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Hozzon létre egy ív közzétételi - SSO tesztfelhasználó számára

Ez a szakasz célja meghívta Britta Simon ív közzétételi - egyszeri Bejelentkezéses felhasználó létrehozásához. Ív közzétételi - SSO támogatja a létesítést közvetlenül az időponthoz kötött, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az ív közzétételi - egyszeri bejelentkezés, ha még nem létezik elérésére tett kísérlet során.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [ív közzétételi - SSO támogatási csoport](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon használandó Azure egyszeri bejelentkezés által biztosított hozzáférés ív közzétételi - egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése ív közzétételi - SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ív közzétételi - SSO**.

    ![A ív közzétételi - SSO hivatkozásra az alkalmazások listáját](./media/arc-tutorial/tutorial_arc_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Kattintva a ív közzétételi - SSO csempe a hozzáférési panelen, meg kell beolvasása automatikusan bejelentkezett a ív közzétételi - SSO alkalmazás számára.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

