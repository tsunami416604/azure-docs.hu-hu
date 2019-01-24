---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Sansan |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Sansan között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 6bd84e42bf46885a9dced363724538cbd4d2066a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815004"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Sansan

Ebben az oktatóanyagban elsajátíthatja, hogyan Sansan integrálása az Azure Active Directory (Azure AD).

Sansan integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Sansan Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Sansan (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Sansan az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Sansan egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Sansan hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sansan-from-the-gallery"></a>Sansan hozzáadása a katalógusból
Az Azure AD integrálása a Sansan konfigurálásához hozzá kell Sansan a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Sansan hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Sansan**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sansan-tutorial/tutorial_sansan_search.png)

5. Az eredmények panelen válassza ki a **Sansan**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Sansan a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Sansan mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Sansan hivatkozás kapcsolata kell létrehozni.

Sansan, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Sansan tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Sansan tesztfelhasználó létrehozása](#creating-a-sansan-test-user)**  – egy megfelelője a Britta Simon Sansan, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Sansan alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Sansan, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Sansan** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. Az a **Sansan tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sansan-tutorial/tutorial_sansan_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minták használatával URL-címe: 
    
    | Környezet | URL-cím |
    |:--- |:--- |
    | PC-s webhely |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Natív mobilalkalmazás |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Mobil böngésző beállításai |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges bejelentkezési URL-címet. Kapcsolattartó [Sansan ügyfél-támogatási csapatának](https://www.sansan.com/form/contact) beolvasni ezeket az értékeket. 
     
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sansan-tutorial/tutorial_general_400.png)

6. Sansan alkalmazás vár több **azonosítók** és **válasz URL-címek** támogatásához több környezethez (PC-s webes, natív mobilalkalmazás, mobil böngésző beállításait), amely konfigurálható a PowerShell használatával parancsfájl. A részletes lépéseket alá vannak írva.

7. Több konfigurálása **azonosítók** és **válasz URL-címek** Sansan alkalmazáshoz a PowerShell-parancsfájl használatával, hajtsa végre a következő lépéseket:

    ![Obj egyszeri bejelentkezés konfigurálása](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Nyissa meg a **tulajdonságok** lapján **Sansan** alkalmazást, és másolja a **Objektumazonosító** használatával **másolási** gombra, és illessze be a Jegyzettömbbe.

    b. A **Objektumazonosító**, az Azure Portalról másolt használt **ServicePrincipalObjectId** a PowerShell parancsfájlokban az oktatóanyag későbbi részében használni. 

    c. Most nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.
    
    >[!NOTE] 
    > Az Azure ad-modul telepítéséhez szüksége (a parancs használata `Install-Module -Name AzureAD`). Ha kéri, telepítse a NuGet-modult vagy az új Azure Active Directory V2 PowerShell modul, írja be az Y, és nyomja le az ENTER billentyűt.

    d. Futtatás `Connect-AzureAD` , és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.

    e. Egy alkalmazás több URL-cím frissítéséhez használja a következő szkriptet:

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. A PowerShell-parancsfájlt a sikeres telepítést követően a parancsfájl eredményét ehhez hasonló lesz, ahogy az alábbi és az URL-címértékekre frissítésére, de azok nem get megjelennek az Azure Portalon. 

    ![Egyszeri bejelentkezési parancsfájl konfigurálása](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. Az a **Sansan konfigurációs** területén kattintson **konfigurálása Sansan** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Az egyszeri bejelentkezés konfigurálása **Sansan** oldalon kell küldenie a letöltött **tanúsítvány**, **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [Sansan támogatási csapatának](https://www.sansan.com/form/contact). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

>[!NOTE]
>PC-böngészőbeállítások mobilalkalmazás és PC-s webes és mobilböngészők számára is működik. 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sansan-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sansan-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sansan-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sansan-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sansan-test-user"></a>Sansan tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Sansan hoz létre. Sansan alkalmazása szükséges, az alkalmazás egyszeri bejelentkezés végrehajtása előtt ki kell építeni a felhasználó. 

>[!NOTE]
>Ha szeretné manuálisan hozzon létre egy felhasználót vagy a batch-felhasználó, lépjen kapcsolatba kell a [Sansan támogatási csapatának](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Sansan Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Sansan, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Sansan**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Sansan csempére kattint, meg kell lekérése automatikusan bejelentkezett az Sansan alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

