---
title: 'Oktatóanyag: Azure Active Directory-integráció az vxMaintain |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és vxMaintain között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 6362bcb701b444c8cd71b270222ce4f87b4cc2e3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055858"
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Oktatóanyag: Azure Active Directory-integráció az vxMaintain

Ebben az oktatóanyagban elsajátíthatja, hogyan vxMaintain integrálása az Azure Active Directory (Azure AD).

Ez az integráció kínál számos fontos előnnyel jár. A következőket teheti:

- Szabályozza, ki férhet hozzá vxMaintain Azure AD-ben.
- Engedélyezze a felhasználók automatikusan jelentkezhetnek be vxMaintain egyszeri bejelentkezéssel (SSO) az Azure AD-fiókjaik használatával.
- A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

VxMaintain az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy vxMaintain előfizetés SSO engedélyezése

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy nem használja éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. 

Ez az oktatóanyag ismerteti a forgatókönyv két fő építőelemeket áll:

* VxMaintain hozzáadása a katalógusból
* Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-vxmaintain-from-the-gallery"></a>VxMaintain hozzáadása a katalógusból
VxMaintain integrációjának konfigurálása az Azure ad-vel, hozzá kell vxMaintain a galériából a felügyelt SaaS-alkalmazások listájára.

A katalógusból vxMaintain hozzáadásához tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** gombra. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A "Nagyvállalati alkalmazások" panel][2]
    
3. Egy alkalmazás hozzáadása a a **minden alkalmazás** párbeszédpanelen jelölje ki **új alkalmazás**.

    ![Az "új alkalmazás" gombra][3]

4. A Keresés mezőbe írja be a **vxMaintain**.

    ![Az "Egyszeri bejelentkezési mód" legördülő lista](./media/vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Az eredmények listájában válassza ki a **vxMaintain**, majd válassza ki **Hozzáadás**.

    ![A vxMaintain hivatkozás](./media/vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD SSO vxMaintain, a teszt "Britta Simon." nevű felhasználó használatával

Az SSO működjön az Azure AD tudnia kell, a vxMaintain partner, az Azure AD-felhasználót. Azt jelenti az Azure AD-felhasználók és a megfelelő vxMaintain felhasználó közötti kapcsolat kapcsolatot kell létesítenie.

A hivatkozás kapcsolatot hozhat létre, rendelje hozzá a vxMaintain **felhasználónév** érték, mint az Azure AD **felhasználónév** értéket.

Konfigurálja és az Azure AD egyszeri bejelentkezés tesztelése vxMaintain használatával végezze el a következő építőelemeit.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Ebben a szakaszban is mind az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon, valamint egyszeri Bejelentkezést az vxMaintain alkalmazásban az alábbiak szerint:

1. Az Azure Portalon az a **vxMaintain** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Az "Egyszeri bejelentkezés" parancs][4]

2. Az SSO engedélyezéséhez a **egyszeri bejelentkezési mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Az "SAML-alapú bejelentkezés" parancs](./media/vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. A **vxMaintain tartomány és URL-címek**, tegye a következőket:

    ![A tartomány és URL-címek szakaszt vxMaintain](./media/vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. Az a **azonosító** mezőbe írja be egy URL-címet, amely rendelkezik a következő szintaxist: `https://<company name>.verisae.com`

    b. Az a **válasz URL-cím** mezőbe írja be egy URL-címet, amely rendelkezik a következő szintaxist: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Az előző értékek nem valódi. Frissítse azokat a tényleges azonosítóval, és a válasz URL-címe. Az értékek beszerzéséhez forduljon a [vxMaintain támogatási csapatának](https://www.hubspot.com/company/contact).
 
4. Alatt **SAML-aláíró tanúsítvány**válassza **metaadatainak XML**, majd mentse a metaadat-fájlt a számítógépre.

    ![Az "SAML aláíró tanúsítvány" szakasz](./media/vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![A Mentés gombra](./media/vxmaintain-tutorial/tutorial_general_400.png)

6. Konfigurálása **vxMaintain** SSO, küldjön a letöltött **metaadatainak XML** fájlt a [vxMaintain támogatási csapatának](https://www.hubspot.com/company/contact).

> [!TIP]
> Az alkalmazás beállítása során az előző utasítások a tömör verziója olvashat a [az Azure portal](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és hozzáférhet a beágyazott dokumentáció a **konfigurációs** szakaszban. 
>
>A beágyazott dokumentáció szolgáltatással kapcsolatos további tudnivalókért lásd: [egyszeri bejelentkezést a vállalati alkalmazások kezelése](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ebben a szakaszban tesztfelhasználó Britta Simon az Azure Portalon létrehozhat az alábbiak szerint:

![Az Azure ad-ben tesztfelhasználó számára][100]

1. Az a **az Azure portal**, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az "Azure Active Directory" gomb](./media/vxmaintain-tutorial/create_aaduser_01.png) 

2. Felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** > **minden felhasználó**.
    
    ![A "Minden felhasználó" hivatkozásra](./media/vxmaintain-tutorial/create_aaduser_02.png)  
    A **minden felhasználó** párbeszédpanel nyílik meg. 

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![A Hozzáadás gombra.](./media/vxmaintain-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:
 
    ![A felhasználó párbeszédpanel](./media/vxmaintain-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a tesztfelhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a létrehozott értéket a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-vxmaintain-test-user"></a>VxMaintain tesztfelhasználó létrehozása

Ebben a szakaszban vxMaintain Britta Simon tesztfelhasználót hoz létre. Felhasználók hozzáadása az vxMaintain platformon, dolgozni a [vxMaintain támogatási csapatának](https://www.hubspot.com/company/contact). Mielőtt használná az egyszeri bejelentkezés, hozzon létre, és aktiválja a felhasználókat.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze tesztfelhasználó Britta Simon Azure egyszeri bejelentkezés használatára vxMaintain való hozzáférést. Ehhez tegye a következőket:

![A megjelenítendő név listában a tesztfelhasználó számára][200] 

1. Az Azure Portalon **alkalmazások** nézet, lépjen a **Directory** Nézet > **vállalati alkalmazások** > **minden alkalmazás**.

    ![Az "Összes alkalmazás" hivatkozásra][201] 

2. Az a **alkalmazások** listáról válassza ki **vxMaintain**.

    ![A vxMaintain hivatkozás](./media/vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Válassza ki **Hozzáadás** , majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][203]

5. A a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**, majd válassza ki a **válassza** gombra.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSO konfigurálása a hozzáférési Panel használatával tesztelheti.

Válassza a **vxMaintain** csempe a hozzáférési panelen kell jelentkezhet be a vxMaintain alkalmazását automatikusan.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>További lépések

* [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/vxmaintain-tutorial/tutorial_general_203.png

