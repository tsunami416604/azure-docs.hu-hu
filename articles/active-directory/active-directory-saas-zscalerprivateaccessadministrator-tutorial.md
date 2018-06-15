---
title: 'Oktatóanyag: Azure Active Directory-integráció Zscaler személyes hozzáférési rendszergazdai |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Zscaler privát hozzáférést rendszergazda között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: c3918e4b54d60d6f609e99e24ed2f4b2995bf80c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353721"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Oktatóanyag: Azure Active Directory-integráció Zscaler privát hozzáférést rendszergazda

Ebben az oktatóanyagban elsajátíthatja Zscaler személyes hozzáférési rendszergazdai integrálása az Azure Active Directory (Azure AD).

Zscaler személyes hozzáférési rendszergazdai integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér a Zscaler privát hozzáférést rendszergazda Azure AD-ben.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Zscaler személyes hozzáférési rendszergazdai (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Zscaler saját szolgáltatás rendszergazdája, a következő elemeket kell:

- Az Azure AD szolgáltatásra
- Egy Zscaler személyes hozzáférési rendszergazdai egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Zscaler privát hozzáférést rendszergazda hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>A gyűjteményből Zscaler privát hozzáférést rendszergazda hozzáadása
Az Azure AD integrálása Zscaler személyes hozzáférési rendszergazdai konfigurálásához kell hozzáadnia Zscaler saját szolgáltatás rendszergazdája a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Zscaler személyes hozzáférési rendszergazda hozzáadásához a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Zscaler személyes hozzáférési rendszergazdai**, jelölje be **Zscaler személyes hozzáférési rendszergazdai** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel a az alkalmazás.

    ![Az eredménylistában Zscaler saját szolgáltatás rendszergazdája](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Zscaler privát hozzáférést rendszergazda "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Zscaler privát hozzáférést rendszergazda egy felhasználó az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználói Zscaler privát hozzáférést rendszergazda közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés Zscaler személyes hozzáférési rendszergazdai tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Zscaler személyes hozzáférési rendszergazdai tesztfelhasználó létrehozása](#create-a-zscaler-private-access-administrator-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon a Zscaler saját szolgáltatás rendszergazdája, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez Zscaler személyes hozzáférési rendszergazdai alkalmazásában.

**Az Azure AD az egyszeri bejelentkezés konfigurálása Zscaler saját szolgáltatás rendszergazdája, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Zscaler személyes hozzáférési rendszergazdai** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Az a **Zscaler személyes hozzáférési rendszergazdai tartományi és URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információkat Zscaler személyes hozzáférési rendszergazdai tartományi és URL-címek](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Ellenőrizze **megjelenítése speciális URL-cím beállításai**

    d. Az a **RelayState** szövegmezőhöz be egy értéket: `idpadminsso`

4.  Ha szeretne beállítani az alkalmazás **SP** kezdeményezett módban hajtsa végre a következő lépéseket:

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-címet. Ügyfél [Zscaler személyes hozzáférési rendszergazdai támogatási csoport](https://help.zscaler.com/zpa-submit-ticket) beolvasni ezeket az értékeket.
 
5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. Egy másik webes böngészőablakban, bejelentkezési Zscaler személyes hozzáférési rendszergazdai rendszergazdaként.

8. Kattintson a felső **felügyeleti** , és keresse meg **hitelesítési** szakaszban kattintson **IdP konfigurációs**.

    ![Zscaler személyes hozzáférési rendszergazdai felügyelet](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. Kattintson a jobb felső sarokban, **hozzáadása IdP konfigurációs**. 

    ![Zscaler személyes hozzáférési rendszergazdai addidp](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. Az a **hozzáadása IdP konfigurációs** lapon hajtsa végre a következő lépéseket:
 
    ![Zscaler személyes hozzáférési rendszergazdai idpselect](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kattintson a **fájl kiválasztása** feltölteni a letöltött metaadat-fájlt az Azure AD-t a **IdP metaadatok fájlfeltöltés** mező.

    b. Emellett beolvassa a **IdP metaadatok** az Azure AD és a mezők adatai tölti fel az alább látható módon.

    ![Zscaler személyes hozzáférési rendszergazdai idpconfig](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Válassza ki **egyszeri bejelentkezés** , **rendszergazda**.

    d. Válassza ki a tartományt a **tartományok** mező.
    
    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Zscaler személyes hozzáférési rendszergazdai tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD felhasználók jelentkezzenek be az Zscaler titkos szolgáltatás rendszergazdája számára, azok ki kell építenie a Zscaler saját szolgáltatás rendszergazdája. Esetén Zscaler saját szolgáltatás rendszergazdája, egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Zscaler személyes hozzáférési rendszergazdai vállalati webhely.

2. Kattintson a felső **felügyeleti** , és keresse meg **hitelesítési** szakaszban kattintson **IdP konfigurációs**.

    ![Zscaler személyes hozzáférési rendszergazdai felügyelet](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Kattintson a **rendszergazdák** a bal oldalán található menüben.

    ![Zscaler személyes hozzáférési rendszergazda rendszergazda](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Kattintson a jobb felső sarokban, **rendszergazda felvétele**:

    ![Zscaler személyes hozzáférési rendszergazdai adja hozzá a rendszergazda](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Az a **rendszergazda felvétele** lapon, a következő lépésekkel:

    ![Zscaler személyes hozzáférési rendszergazda felhasználó felügyeleti](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Az a **felhasználónév** szövegmező, adja meg az e-mail címét, például a felhasználó **BrittaSimon@contoso.com**.

    b. Az a **jelszó** szövegmező, írja be a jelszót.

    c. Az a **jelszó megerősítése** szövegmező, írja be a jelszót.

    d. Válassza ki **szerepkör** , **Zscaler személyes hozzáférési rendszergazdai**.

    e. Az a **E-mail** szövegmező, adja meg az e-mail címét, például a felhasználó **BrittaSimon@contoso.com**.

    f. Az a **Phone** szövegmező, írja be a telefonszámot.

    g. Az a **időzóna** szövegmező, válassza ki az időzóna.

    h. Kattintson a **Save** (Mentés) gombra.  

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférés biztosítása a Zscaler saját szolgáltatás rendszergazdája által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Rendelje hozzá a Britta Simon Zscaler saját szolgáltatás rendszergazdája számára, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Zscaler személyes hozzáférési rendszergazdai**.

    ![Az alkalmazások listáját a Zscaler személyes hozzáférési rendszergazdai hivatkozás](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Zscaler személyes hozzáférési rendszergazdai csempére kattint, meg kell beolvasása automatikusan bejelentkezett az Zscaler személyes hozzáférési rendszergazdai alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

